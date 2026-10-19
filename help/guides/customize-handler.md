---
title: Personalizzare un gestore di azioni generato
description: Comprendi il contratto del gestore Adobe LLM Apps, sostituisci i dati di esempio generati e mantieni l’output del gestore allineato al relativo widget.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '880'
ht-degree: 0%
---

# Personalizzare un gestore generato {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

La piattaforma crea un gestore di lavoro per ogni azione generata. Il gestore restituisce inizialmente dati di esempio per testare l’esperienza completa.

**In questa guida:** Scopri come trovare il gestore generato, sostituirne i dati di esempio con chiamate ai sistemi e mantenere la risposta compatibile con il widget. Quindi verifica e distribuisci le modifiche.

## Trova il gestore generato

Aprire l&#39;archivio del gestore connesso durante la [configurazione dell&#39;infrastruttura](/help/guides/create-app.md). Se utilizzi l’infrastruttura gestita di Adobe, richiedi l’accesso al codice prima di continuare.

```text
actions/
└── <action-slug>/
    ├── index.js
    └── data.json
```

I test corrispondenti si trovano in una directory separata:

```text
test/
└── actions/
    └── <action-slug>.test.js
```

Utilizza la richiesta di pull generata per individuare i file esatti. Il margine della cartella utilizza i trattini: la piattaforma genera un&#39;azione con l&#39;identificatore di codice `search_products` in `actions/search-products/`. Il runtime riconosce il modulo sillabato e una cartella che corrisponde esattamente all’identificatore.

Modifica `index.js` generato ed eventuali dati di esempio associati. Non modificare i file di runtime come `entry.js`.

## Contratto gestore

Ogni gestore esporta una funzione asincrona:

```javascript
module.exports = async (args, extra) => {
  return {
    content: [
      { type: 'text', text: 'Response for the LLM platform.' }
    ],
    structuredContent: {
      // Data for the widget.
    }
  };
};
```

Per un&#39;azione con uno schema di input, la funzione riceve un oggetto `args` e un contesto di runtime facoltativo, `extra`, e restituisce un oggetto risultato. Utilizzare `extra` per gli helper di runtime come `getVariable(extra, 'PRODUCT_API_ORIGIN')`; le variabili dell&#39;app non vengono esposte automaticamente tramite `process.env`. Consulta [Configurare le variabili e i segreti dell&#39;app](/help/guides/app-variables.md).

### Input: `args`

`args` contiene i parametri definiti per l&#39;azione in [!DNL LLM Apps].

Per un&#39;azione con `category` e `query` parametri:

```javascript
module.exports = async ({ category = '', query = '' } = {}, extra) => {
  // Validate business rules before using the arguments.
};
```

Con i metadati dell’azione, il runtime converte lo schema di input dichiarato in convalida per le regole supportate, inclusi campi obbligatori, tipi primitivi, enum ed elementi array. Il runtime non esegue la convalida completa dello schema JSON. Controllare i vincoli quali la lunghezza delle stringhe, gli intervalli numerici e le proprietà degli oggetti nidificati nel gestore.

L&#39;individuazione locale senza metadati dello schema non equivale alla distribuzione: il runtime non passa argomenti di azione nel normale modulo `(args, extra)`. Utilizzare test del gestore diretto o metadati locali `actions.json` rappresentativi per testare gli argomenti. Consulta [Sviluppo e test del gestore locale](/help/reference/development.md).

### Output: `content`

Restituisce sempre `content`. Si tratta di un array di parti di contenuto lette dalla piattaforma LLM e dagli host che non visualizzano widget.

```javascript
content: [
  {
    type: 'text',
    text: 'Found 3 products matching your search.'
  }
]
```

Mantieni concisa la risposta testuale. Non includere credenziali, errori interni o dati che l’utente non è autorizzato a visualizzare.

### Output: `structuredContent`

Restituisce `structuredContent` quando l&#39;azione ha un widget. Deve essere un oggetto semplice, non un array semplice.

```javascript
structuredContent: {
  arguments: { query: 'coffee' },
  records: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ]
}
```

`structuredContent` fa parte del risultato dell&#39;azione MCP consegnato all&#39;host. Non presumere che sia nascosto dal modello o dall&#39;utente. Restituisci solo i dati autorizzati richiesti dall’esperienza, mai le credenziali o la configurazione di un server privato.

Per un&#39;azione solo testo, la restituzione di `structuredContent` è facoltativa.

## Il contratto handler-widget

Il gestore e il widget condividono un contratto: la forma di `structuredContent`.

| Output gestore | Scopo |
|---|---|
| `content` | Risposta leggibile del modello e fallback di solo testo |
| `structuredContent` | Dati consegnati all&#39;host e letti dal widget tramite `bridge.toolResult` |

Il widget legge il risultato del gestore dal bridge SDK delle applicazioni LLM:

```javascript
export default async function decorate(block, bridge) {
  if (!bridge?.isConnected || bridge.hostContext?.preview === true) {
    // Render the block's local preview data instead.
    return;
  }
  const result = await bridge.toolResult;
  const records = result?.structuredContent?.records ?? [];

  // Render records.
}
```

I gestori attualmente generati utilizzano un oggetto contenente `arguments` e `records`. Le app esistenti possono utilizzare una forma diversa, quindi controlla i file generati prima di modificarli. La modifica del nome o del tipo di un campo può interrompere il widget. Aggiorna insieme il gestore, il widget e i test.

## Sostituisci dati di esempio

I gestori generati caricano i record rappresentativi da `data.json`. Sostituisci la ricerca di dati di esempio con una chiamata lato server al sistema. L’esempio che segue utilizza un’API di catalogo HTTPS pubblica e legge la sua origine non sensibile da una variabile di app. Adatta la convalida della risposta all’API.

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ query = '' } = {}, extra) => {
  const normalizedQuery = typeof query === 'string' ? query.trim() : '';
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { arguments: {}, records: [] },
      isError: true
    };
  }

  const knownArguments = { query };
  try {
    const apiOrigin = getVariable(extra, 'PRODUCT_API_ORIGIN');
    if (!apiOrigin) {
      throw new Error('Missing configuration');
    }
    const origin = new URL(apiOrigin);
    if (origin.protocol !== 'https:' || origin.username || origin.password) {
      throw new Error('Invalid API origin');
    }
    const url = new URL('/v1/products', origin);
    url.searchParams.set('query', normalizedQuery);

    const response = await fetch(url, {
      signal: AbortSignal.timeout(8000)
    });
    if (!response.ok) {
      throw new Error('Request failed');
    }
    const payload = await response.json();
    if (!payload || !Array.isArray(payload.products)
        || !payload.products.every((product) =>
          product
          && typeof product.id === 'string'
          && typeof product.name === 'string'
          && typeof product.price === 'string')) {
      throw new Error('Unexpected response');
    }

    const records = payload.products.map(({ id, name, price }) => ({
      id,
      name,
      price
    }));

    return {
      content: [
        { type: 'text', text: `Found ${records.length} matching products.` }
      ],
      structuredContent: { arguments: knownArguments, records }
    };
  } catch {
    return {
      content: [{ type: 'text', text: 'Product search is unavailable. Try again later.' }],
      structuredContent: { arguments: knownArguments, records: [] },
      isError: true
    };
  }
};
```

Mantieni l&#39;accesso di rete protetto nel gestore. Non inserire mai le credenziali API in widget JavaScript o nel controllo del codice sorgente. Per le richieste effettuate per conto di un utente connesso, vedere [Autenticare gli utenti finali](/help/guides/authentication.md).

Per le impostazioni non riservate necessarie al gestore, ad esempio l&#39;URL di un servizio, vedere [Configurare le variabili e i segreti dell&#39;app](/help/guides/app-variables.md). Configura le variabili separatamente per ogni ambiente. Le modifiche apportate alle variabili hanno effetto sulla distribuzione successiva. L’interfaccia utente corrente supporta solo l’aggiunta di variabili; non utilizzare le variabili per memorizzare le credenziali API.

## Gestire gli stati previsti

Mantenere una forma di output prevedibile per ogni risultato.

### Risultati trovati

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [...] }
}
```

### Nessun risultato

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { arguments: { query: 'coffee' }, records: [] }
}
```

Una forma risultato prevedibile consente al widget di eseguire il rendering di uno stato vuoto senza indovinare se `records` esiste.

Per errori di servizio, restituire un risultato di errore sicuro con `isError: true`, come nell&#39;esempio. Controllare `result.isError` nel widget in modo che una richiesta non riuscita non venga presentata come una ricerca vuota riuscita. Non esporre tracce dello stack, credenziali, host interni o corpi di risposta a monte.

## Verifica il contratto

Aggiorna i test generati ogni volta che il gestore cambia. Copertina:

- Argomenti validi e non validi
- Stati dei risultati e dei non risultati.
- Errori e timeout API.
- Risposte API in formato non valido.
- Presenza di `content` in ogni risultato.
- Oggetto normale per `structuredContent`.
- Forma prevista dal widget.

Esegui la suite di test configurata dell’archivio:

```bash
npm test
```

Se un test generato importa `node:test` anziché Jest, eseguirlo con `node --test test/actions/<action-slug>.test.js` e verificare che il relativo percorso di importazione venga risolto da `test/actions/`. Utilizzare l&#39;esecuzione dei test appropriata per il file. Il comando `npm test` della boilerplate esegue Jest.

Per il test MCP locale, vedere [Sviluppo e test del gestore locale](/help/reference/development.md).

## Distribuire la modifica

1. Esegui il commit e invia le modifiche al gestore.
2. Se si modifica la forma dati, aggiornare e inviare il widget.
3. [Distribuisci l&#39;app](/help/guides/deploy-your-app.md) nell&#39;area di visualizzazione.
4. Eseguire il test in una piattaforma LLM supportata. Vedi [Test in ChatGPT](/help/guides/test-in-chatgpt.md) o [Test in Claude](/help/guides/test-in-claude.md).
5. Dopo il superamento dei test in Stage, distribuisci in Produzione.

Quindi, vedere [Personalizzare un widget generato](/help/guides/widgets.md).
