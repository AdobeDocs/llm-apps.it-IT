---
title: Creare un’azione da zero
description: Definisci i metadati delle azioni, implementane il gestore, connetti un widget EDS, verificalo e implementalo con le app Adobe LLM.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '1137'
ht-degree: 0%

---


# Creare un’azione da zero {#create-action-from-scratch}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

>[!NOTE]
>
>Questa guida presuppone una conoscenza di base di Adobe Edge Delivery Services (EDS). Se non si ha familiarità con EDS, prima di connettere un widget leggere l&#39;esercitazione per sviluppatori [EDS](https://www.aem.live/developer/tutorial) e [Esplorare i blocchi](https://www.aem.live/docs/exploring-blocks) per apprendere gli elementi essenziali, ovvero i blocchi, la funzione `decorate` e la struttura del progetto EDS.

Usa questa guida per aggiungere una funzionalità che la piattaforma non ha creato. Definirai l&#39;azione in [!DNL LLM Apps], scriverai il relativo gestore nell&#39;archivio collegato, aggiungerai un widget se necessario, verificherai e implementerai.

**Percorso:** Pianifica l&#39;azione → crearne i metadati → scrivere il gestore → connettere il widget → il test in locale → distribuire e testare il plug-in.

Per la prima app, inizia con [Crea automaticamente la prima app](/help/guides/create-app.md).

## Prima di iniziare

Hai bisogno di:

- Un&#39;app LLM esistente.
- Un archivio di gestori collegato.
- Archivio clonato localmente con le relative dipendenze installate.
- Un progetto EDS se l’azione visualizza un widget.
- Un’API o un’origine dati chiara per i risultati di produzione.

## Pianificare l’azione

Un’azione deve eseguire un’attività utente chiara. Prima di aprire l’interfaccia utente, definisci:

- **Intenzione**: ciò che l&#39;utente sta tentando di realizzare.
- **Descrizione** — quando la piattaforma LLM deve selezionare questa azione.
- **Input**: informazioni minime richieste all&#39;utente.
- **Risultato**: il testo e i dati strutturati restituiti dal gestore.
- **Comportamento**: se l&#39;azione legge dati, modifica dati o chiama sistemi esterni.
- **Widget** — indica se il risultato richiede un&#39;interfaccia visiva.

Ad esempio, per un&#39;azione di **Ricerca prodotti** è possibile utilizzare:

```text
Intent: Find products matching a category or search phrase
Inputs:
  category: optional string
  query: optional string
Result:
  content: text summary
  structuredContent: products and total count
Behavior: read-only, idempotent, open-world
Widget: product cards
```

Mantieni separate le attività correlate ma diverse. La ricerca dei prodotti e l’acquisto dei prodotti non devono essere considerati come un’unica azione, in quanto presentano input, rischi e requisiti di conferma diversi.

## Creare i metadati dell’azione

Apri l&#39;app e seleziona **[!UICONTROL Azioni]**, quindi seleziona **[!UICONTROL Crea azione]**.

L&#39;editor contiene **[!UICONTROL schede Azione]** e **[!UICONTROL Metadati widget]**.

### Immetti le informazioni di base

![Crea azione — informazioni di base](/help/assets/guide-create-action/action-basic-info.png)

Inserisci:

- **[!UICONTROL Nome azione]**: nome breve dell&#39;attività, ad esempio *Prodotti di ricerca*.
- **[!UICONTROL Descrizione]**: spiega quando utilizzare l&#39;azione e cosa restituisce.

Una descrizione utile è specifica:

```text
Search the product catalog by category or keyword. Returns matching
products with their names, prices, categories, and image URLs.
```

Evita descrizioni vaghe come *Ottiene informazioni sul prodotto*. La piattaforma LLM utilizza la descrizione per scegliere tra le azioni.

### Seleziona annotazioni

Le annotazioni descrivono il comportamento dell’azione:

- **Suggerimento distruttivo**: l&#39;azione può eliminare o modificare definitivamente i dati.
- **Idempotent (stessi argomenti = nessun effetto aggiuntivo)** — la ripetizione della stessa richiesta ha lo stesso effetto.
- **Open world hint**: l&#39;azione comunica con sistemi esterni.
- **Suggerimento di sola lettura**: l&#39;azione non modifica i dati.

Seleziona solo le annotazioni vere. Ad esempio, la ricerca di prodotti è normalmente di sola lettura, idempotente e open-world.

### Aggiungi metadati OpenAI

Inserisci brevi messaggi visualizzati durante l’esecuzione dell’azione e al suo completamento:

```text
Invoking: Searching products...
Invoked: Products found
```

Per le azioni con widget, aggiungi **[!UICONTROL Descrizione widget]**. È diverso dalla descrizione dell’azione:

- **Descrizione azione** consente al modello di decidere quando richiamare l&#39;azione.
- **La descrizione del widget** è mappata a `_meta["openai/widgetDescription"]` e riepiloga ciò che viene visualizzato dal componente sottoposto a rendering, riducendo la narrazione ripetuta.

[!DNL LLM Apps] applica questo elemento come metadati del componente. Non restituirlo dal gestore.

### Configurare la visibilità

- **[!UICONTROL Esposizione al modello di IA]** consente al modello di selezionare l&#39;azione.
- **[!UICONTROL Mostra come widget nella superficie dell&#39;app]** mostra il widget configurato.

Disattiva la visibilità dei widget quando l&#39;azione restituisce solo testo.

### Aggiungi parametri di input

Aggiungi un parametro per ogni valore accettato dal gestore. Ogni parametro richiede:

- **Nome**: la chiave ricevuta dal gestore.
- **Tipo** — Stringa, Numero, Numero intero o Booleano.
- **Descrizione** — modalità di estrazione del valore da parte del modello.
- **Obbligatorio** - Indica se l&#39;azione può essere eseguita senza di essa.

Per **Prodotti Di Ricerca**:

```text
category
  Type: String
  Required: No
  Description: Product category used to narrow the catalog.

query
  Type: String
  Required: No
  Description: Product name or search phrase.
```

Utilizza nomi di parametri stabili. La modifica di un nome richiede anche la modifica del gestore e dei relativi test.

### Configurare Analytics

Abilitare **[!UICONTROL Raccogli intento utente]** quando si desidera che Analytics includa un riepilogo della conversazione che ha portato all&#39;azione.

![Crea azione - analisi intento utente](/help/assets/guide-create-action/action-analytics-user-intent.png)

Per le definizioni complete dei campi, vedere [Campi azione e widget](/help/reference/reference-docs.md).

## Configurare il widget

Ignora questa sezione per un&#39;azione di solo testo.

Apri **[!UICONTROL Metadati widget]**.

![Crea azione — metadati widget](/help/assets/guide-create-action/widget-metadata.png)

Configurare:

- **Tipo** — selezionare EDS.
- **Dominio widget**: origine EDS che ospita il widget.
- **Preferisce il bordo** — richiede un contenitore con bordi nell&#39;host.
- **URL script**: il punto di ingresso del widget EDS.
- **URL widget**: la pagina EDS pubblicata per questa azione.

Gli URL tipici sono:

```text
Script URL:
https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js

Widget URL:
https://main--<repo>--<owner>.aem.live/<widget-page>
```

Concedi solo le autorizzazioni browser e i domini CSP richiesti.

![Crea azione — autorizzazioni e CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

Se il progetto EDS o la pagina del widget non esiste ancora, completa [Porta il tuo progetto EDS](/help/guides/bring-your-own-eds.md), quindi torna all&#39;azione.

## Salva l’azione

Seleziona **[!UICONTROL Crea nuova azione]**. L&#39;azione viene visualizzata nella pagina Azioni con un contrassegno **Non distribuito**.

A questo punto, i metadati esistono, ma l’azione richiede ancora un gestore.

## Implementare il gestore

Clona l’archivio del gestore collegato e installane le dipendenze:

```bash
npm install
```

Crea:

```text
actions/
└── search-products/
    └── index.js
```

Il nome della cartella deve corrispondere all&#39;identificatore di codice dell&#39;azione visualizzato nell&#39;editor delle azioni.

Per il contratto del risultato completo e la relazione handler-widget, vedere [Personalizzare un handler generato](/help/guides/customize-handler.md).

### Contratto gestore

Esporta una funzione asincrona:

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

Il gestore riceve i parametri definiti nell’interfaccia utente.

### Restituisce `content`

`content` è il testo di riserva letto dalla piattaforma LLM:

```javascript
content: [
  { type: 'text', text: 'Found 3 matching products.' }
]
```

Restituisce sempre `content` utile, anche quando l&#39;azione ha un widget.

### Restituisce `structuredContent`

`structuredContent` è un oggetto normale utilizzato dal widget:

```javascript
structuredContent: {
  products: [
    { id: 'P-100', name: 'Product A', price: '$20' }
  ],
  total: 1
}
```

La forma deve corrispondere a quanto letto dal blocco EDS da `bridge.toolResult`.

### Connettere un’API

Mantieni l’accesso API protetto nel gestore lato server. Carica la configurazione dall’ambiente di runtime e utilizza un’origine HTTPS fissa.

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

  const products = payload.products.map((product) => ({
    id: product.id,
    name: product.name,
    price: product.price
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

Non inserire credenziali API nel codice sorgente, nei metadati di azioni, nel widget JavaScript, nei registri o in errori rivolti all’utente.

Per il codice di produzione, convalidare la risposta upstream completa prima di mappare i campi approvati in `structuredContent`.

## Aggiungi test del gestore

Crea il test corrispondente:

```text
test/
└── actions/
    └── search-products.test.js
```

Prova almeno:

- Input valido.
- Input mancante o non valido.
- Risultati vuoti.
- Timeout o errore API.
- Dati API in formato non valido.
- Forma `structuredContent` prevista dal widget.

Esegui:

```bash
npm test
```

Per il layout del progetto e il test MCP locale, vedere [Sviluppo e test del gestore locale](/help/reference/development.md).

## Verifica l’azione a livello locale

Esegui:

```bash
npm run dev:local
```

Senza un `actions.json` locale, il server rileva il gestore con metadati minimi e nessuna convalida dello schema di input.

Utilizzare MCP Inspector o `curl` per:

1. Elencare le azioni registrate.
2. Chiama la nuova azione con argomenti rappresentativi.
3. Verificare `content` e `structuredContent`.
4. Verifica richieste non valide e vuote.

## Connetti e verifica il widget

Se l’azione ha un widget:

1. Fare in modo che il widget legga `structuredContent` del gestore.
2. Eseguire il rendering di valori esterni con API DOM sicure come `textContent`.
3. Aggiungi stati di caricamento, vuoto ed errore.
4. Visualizzare localmente l&#39;anteprima della pagina EDS.
5. Verifica gli URL CSP, CORS e widget.

Vedi [Porta il tuo progetto EDS](/help/guides/bring-your-own-eds.md).

## Distribuzione e test

1. Esegui il commit e invia le modifiche al gestore e al widget.
2. [Distribuisci l&#39;app](/help/guides/deploy-your-app.md) nell&#39;area di visualizzazione.
3. [Verifica il plug-in ChatGPT](/help/guides/test-in-chatgpt.md).
4. Verifica i prompt che devono e non devono richiamare l’azione.
5. Dopo che lo staging ha esito positivo, distribuisci in produzione.

Se esistono metadati senza un gestore corrispondente, la distribuzione registra l’azione con uno stub predefinito. Aggiungi il gestore prima di rendere l’azione disponibile agli utenti.
- [Guida: configurazione del widget (EDS)](/help/guides/widgets.md)
