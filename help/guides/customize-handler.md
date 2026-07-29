---
title: Personalizzare un gestore di azioni generato
description: Comprendi il contratto del gestore Adobe LLM Apps, sostituisci i dati di esempio generati e mantieni l’output del gestore allineato al relativo widget.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 0%

---


# Personalizzare un gestore generato {#customize-generated-handler}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

L’agente di onboarding crea un gestore di lavoro per ogni azione generata. Inizialmente il gestore restituisce dati di esempio in modo da poter testare l’esperienza completa.

Usa questa guida per comprendere il contratto del gestore e sostituire i dati di esempio con le API o le origini dati.

**Percorso:** Trova il gestore generato → comprenderne gli input e il risultato → connettere il sistema → mantenere il contratto del widget allineato → test e distribuzione.

## Trova il gestore generato

Apri l’archivio del gestore selezionato durante l’onboarding:

```text
actions/
└── <action-name>/
    └── index.js
```

I test corrispondenti vengono memorizzati separatamente:

```text
test/
└── actions/
    └── <action-name>.test.js
```

Modifica `index.js` generato. Non modificare i file di runtime come `entry.js`.

## Contratto gestore

Ogni gestore esporta una funzione asincrona:

```javascript
module.exports = async (args) => {
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

La funzione riceve un oggetto `args` e restituisce un oggetto risultato.

### Input: `args`

`args` contiene i parametri definiti per l&#39;azione in [!DNL LLM Apps].

Per un&#39;azione con `category` e `query` parametri:

```javascript
module.exports = async ({ category = '', query = '' } = {}) => {
  // Use the validated action arguments.
};
```

Il runtime convalida lo schema di input quando i metadati dell&#39;azione includono `inputSchema`, come avviene dopo la distribuzione. L&#39;individuazione del gestore locale senza `actions.json` non applica la convalida dello schema. Il gestore deve sempre applicare le regole aziendali, ad esempio valori supportati, lunghezze massime e combinazioni consentite.

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

Mantieni concisa questa risposta. Non includere credenziali, errori interni o dati che l’utente non è autorizzato a visualizzare.

### Output: `structuredContent`

Restituisce `structuredContent` quando l&#39;azione ha un widget. Deve essere un oggetto semplice, non un array semplice.

```javascript
structuredContent: {
  products: [
    {
      id: 'P-100',
      name: 'Frescopa House Blend',
      price: '$14.99'
    }
  ],
  total: 1
}
```

`structuredContent` viene inviato al widget, non al LLM. Restituisce solo i campi richiesti dall’interfaccia.

Per un&#39;azione solo testo, è possibile omettere `structuredContent`.

## Il contratto handler-widget

Il gestore e il widget condividono un contratto: la forma di `structuredContent`.

```text
Action arguments
      ↓
Handler
      ├── content → LLM text response
      └── structuredContent → Widget
                                  ↓
                           bridge.toolResult
```

Il widget legge il risultato del gestore dal bridge SDK delle applicazioni LLM:

```javascript
export default async function decorate(block, bridge) {
  const result = await bridge.toolResult;
  const products = result?.structuredContent?.products ?? [];

  // Render products.
}
```

Se il gestore restituisce:

```javascript
structuredContent: {
  products: [...],
  total: 3
}
```

il widget deve leggere `structuredContent.products` e `structuredContent.total`.

La modifica del nome o del tipo di un campo può interrompere il widget. Aggiorna insieme il gestore, il widget e i test.

## Sostituisci dati di esempio

I gestori generati in genere contengono una matrice di esempio in memoria. Sostituisci la ricerca dei dati con una chiamata lato server al sistema.

```javascript
const API_ORIGIN = process.env.PRODUCT_API_ORIGIN;
const API_TOKEN = process.env.PRODUCT_API_TOKEN;

module.exports = async ({ query = '' } = {}) => {
  const normalizedQuery = String(query).trim();
  if (!normalizedQuery || normalizedQuery.length > 200) {
    return {
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { products: [], total: 0 }
    };
  }

  if (!API_ORIGIN || !API_TOKEN) {
    throw new Error('Product API configuration is unavailable.');
  }

  const origin = new URL(API_ORIGIN);
  if (origin.protocol !== 'https:') {
    throw new Error('Product API configuration must use HTTPS.');
  }

  const url = new URL('/v1/products', origin);
  url.searchParams.set('query', normalizedQuery);

  const response = await fetch(url, {
    headers: { Authorization: `Bearer ${API_TOKEN}` },
    signal: AbortSignal.timeout(8000)
  });

  if (!response.ok) {
    throw new Error('Product service request failed.');
  }

  const payload = await response.json();
  if (!payload || !Array.isArray(payload.products)
      || !payload.products.every((product) =>
        product
        && typeof product.id === 'string'
        && typeof product.name === 'string'
        && typeof product.price === 'string')) {
    throw new Error('Product service returned an unexpected response.');
  }

  const products = payload.products.map(({ id, name, price }) => ({
    id,
    name,
    price
  }));

  return {
    content: [
      { type: 'text', text: `Found ${products.length} matching products.` }
    ],
    structuredContent: {
      products,
      total: products.length
    }
  };
};
```

Mantieni l&#39;accesso di rete protetto nel gestore. Non inserire mai le credenziali API in widget JavaScript o nel controllo del codice sorgente.

## Gestire gli stati previsti

Mantenere una forma di output prevedibile per ogni risultato.

### Risultati trovati

```javascript
{
  content: [{ type: 'text', text: 'Found 3 products.' }],
  structuredContent: { products: [...], total: 3 }
}
```

### Nessun risultato

```javascript
{
  content: [{ type: 'text', text: 'No matching products were found.' }],
  structuredContent: { products: [], total: 0 }
}
```

Il widget può ora eseguire il rendering di uno stato vuoto senza indovinare se `products` esiste.

Per gli errori del servizio, restituisce o genera un errore sicuro senza esporre le tracce dello stack, i token, gli host interni o i corpi di risposta upstream.

## Verifica il contratto

Aggiorna i test generati ogni volta che il gestore cambia. Copertina:

- Argomenti validi e non validi
- Stati dei risultati e dei non risultati.
- Errori e timeout API.
- Risposte API in formato non valido.
- `content` è sempre presente.
- `structuredContent` è un oggetto semplice.
- Forma prevista dal widget.

Esegui:

```bash
npm test
```

Per il test MCP locale, vedere [Sviluppo e test del gestore locale](/help/reference/development.md).

## Distribuire la modifica

1. Esegui il commit e invia le modifiche al gestore.
2. Se la forma dati è stata modificata, aggiornare e inviare il widget.
3. [Distribuisci l&#39;app](/help/guides/deploy-your-app.md) nell&#39;area di visualizzazione.
4. [Verifica il plug-in ChatGPT](/help/guides/test-in-chatgpt.md).
5. Dopo che lo staging ha esito positivo, distribuisci in produzione.

Quindi, vedere [Personalizzare un widget generato](/help/guides/widgets.md).
