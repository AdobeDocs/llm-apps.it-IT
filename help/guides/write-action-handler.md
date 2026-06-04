---
title: Scrivi il gestore azioni
description: Scopri come scrivere un gestore di azioni per l’app Adobe LLM, incluso il contratto del gestore, structuredContent e un esempio funzionante.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '714'
ht-degree: 0%

---


# Scrivi il gestore azioni

>[!IMPORTANT]
>
>**Dichiarazione di non responsabilità:** Versione beta di [!DNL LLM Apps]. Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale dell’applicazione o del prodotto.

Dopo aver creato un&#39;azione nell&#39;interfaccia utente, i metadati vengono memorizzati nell&#39;API [!DNL LLM Apps], ma non è ancora presente codice. Questa guida illustra come scrivere la funzione di gestione che viene eseguita quando una piattaforma LLM (ad esempio [!DNL ChatGPT] o Claude) richiama l&#39;azione.

Per i dettagli relativi al layout del progetto, allo sviluppo locale e ai test, vedere [Sviluppo](/help/reference/development.md).

## Contratto sviluppatore

Scrivi solo i gestori. Tutto il resto (nome azione, descrizione, schema di input, annotazioni, visibilità widget, autorizzazioni, CSP) risiede nell&#39;interfaccia utente [!DNL LLM Apps] e viene consegnato automaticamente al runtime al momento della distribuzione. Non è mai possibile modificare i metadati nel repository e non registrare mai uno strumento nel codice.

| Preoccupazione | Dove vive |
|---------|----------------|
| Metadati (nome, descrizione, schema, impostazioni widget) | Interfaccia utente [!DNL LLM Apps] — salvata nell&#39;API |
| Codice del gestore (la funzione che viene eseguita) | Archivio [!DNL GitHub] - `actions/<name>/index.js` |
| `actions.json` (snapshot metadati) | Scritto dalla pipeline di distribuzione; scaricato dall’interfaccia utente per lo sviluppo locale |

## Guida introduttiva

Prima di poter scrivere i gestori, l’archivio collegato ha bisogno della struttura del progetto. Clona la **[piattaforma di avvio delle applicazioni Adobe LLM](https://github.com/Adobe-AIFoundations/llm-apps-boilerplate)** per iniziare con un punto di partenza vuoto.

Invia il contenuto all&#39;archivio collegato durante la creazione dell&#39;app (ad esempio, `your-org/your-repo`).

Una volta inserito il codice, esegui:

```bash
npm install
```

In questo modo vengono installate tutte le dipendenze, incluso [`@adobe/llm-apps-runtime`](https://www.npmjs.com/package/@adobe/llm-apps-runtime), il runtime che gestisce la comunicazione del protocollo MCP, l&#39;individuazione delle azioni e il routing delle richieste. Non si interagisce direttamente con il runtime, che viene utilizzato da `entry.js` al momento della compilazione.

>[!TIP]
>
>Se si utilizza [Claude Code](https://claude.ai/code) o [Cursor](https://cursor.com), la boilerplate include un&#39;abilità Claude pronta all&#39;uso in `.claude/skills/llm-apps-action-author/`. È in grado di eseguire lo scaffolding di nuove azioni, generare file di test, convalidare forme gestore e guidare l&#39;utente attraverso il contratto del gestore, il tutto dall&#39;editor. Per utilizzarla, chiedere a Claude di *&quot;aggiungere un&#39;azione denominata search-products&quot;* che seguirà automaticamente le convenzioni di progetto corrette.

## Contratto gestore

Un gestore è un singolo file in `actions/<name>/index.js` che esporta una funzione asincrona:

```javascript
module.exports = async (args) => {
  return {
    content: [{ type: 'text', text: 'response for the LLM' }],
    structuredContent: { /* data for the widget */ }
  }
}
```

La funzione riceve gli argomenti di input dell&#39;azione come oggetto semplice, ovvero i parametri definiti nella finestra di dialogo Crea azione. Il server li convalida in base allo schema di input prima che il gestore venga chiamato.

### `content` (obbligatorio)

Array di parti di contenuto inviate agli host LLM e agli host di solo testo. Questo è ciò che la piattaforma LLM legge per formulare la sua risposta.

```javascript
content: [
  { type: 'text', text: 'Found 5 products matching category "bagged-coffee".' }
]
```

Restituisce sempre `content`: è il fallback universale per qualsiasi host.

### `structuredContent`

Oggetto JavaScript normale inviato al widget. Questi dati hanno **costo zero token**, vengono utilizzati dal blocco del widget EDS per eseguire il rendering di un&#39;interfaccia utente avanzata come un carosello di prodotto o una mappa.

```javascript
structuredContent: {
  products: [
    { name: 'Product A', category: 'bagged-coffee', imageUrl: '...' },
    { name: 'Product B', category: 'bagged-coffee', imageUrl: '...' }
  ],
  total: 2,
  category: 'bagged-coffee'
}
```

La struttura dipende da te. Deve corrispondere a quanto previsto dal blocco del widget EDS tramite `bridge.toolResult`.

>[!IMPORTANT]
>
>`structuredContent` deve essere un oggetto semplice, non un array semplice.

### `_meta` (facoltativo)

Metadati aggiuntivi inviati insieme al risultato. La chiave `openai/widgetDescription` indica alla piattaforma LLM come presentare il widget:

```javascript
_meta: {
  'openai/widgetDescription': 'The widget displays a scrollable product carousel. '
    + 'Do NOT repeat the product list. Instead, highlight one or two recommendations.'
}
```

## Esempio: Gestore di ricerca prodotti

Esempio di gestore `search-products`. Accetta un filtro `category` facoltativo e un testo libero `query`, cerca un catalogo di prodotti e restituisce sia un riepilogo testuale per il modulo LLM che dati strutturati per il carosello del widget.

>[!NOTE]
>
>Questo esempio utilizza un array di prodotti hardcoded per semplicità. In un’applicazione reale, in genere si richiama l’API del prodotto o il database personalizzato per recuperare i risultati in modo dinamico.

```javascript
// actions/search-products/index.js

const PRODUCTS = [
  {
    name: 'Product A',
    description: 'A short description of Product A.',
    category: 'bagged-coffee',
    sub_category: 'dark-roast',
    image_url: 'https://www.example.com/products/product-a/hero.jpg',
    url: 'https://www.example.com/products/product-a',
    productId: 'PROD-001',
    rating: 4.7,
    reviewCount: 58
  },
  // ... more products
];

const WIDGET_DESCRIPTION = 'The widget displays a scrollable product carousel '
  + 'with images, star ratings, and review counts. Do NOT repeat the product list.';

module.exports = async ({ category = '', query = '' } = {}) => {
  let results = PRODUCTS;

  if (category) {
    const categoryLower = category.toLowerCase();
    results = results.filter((p) =>
      p.category.toLowerCase().includes(categoryLower)
      || p.sub_category.toLowerCase().includes(categoryLower)
    );
  }

  if (query) {
    const queryLower = query.toLowerCase();
    results = results.filter((p) =>
      p.name.toLowerCase().includes(queryLower)
      || p.description.toLowerCase().includes(queryLower)
    );
  }

  const products = results.map((p) => ({
    productId: p.productId,
    name: p.name,
    shortDescription: p.description,
    category: p.category,
    rating: p.rating,
    reviewCount: p.reviewCount,
    imageUrl: p.image_url,
    productUrl: p.url,
  }));

  if (products.length === 0) {
    return {
      content: [{ type: 'text', text: `No products found for "${category}".` }],
      structuredContent: { products: [], total: 0, category: null },
      _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
    };
  }

  return {
    content: [
      { type: 'text', text: `Found ${products.length} product(s) in "${category}".` }
    ],
    structuredContent: { products, total: products.length, category },
    _meta: { 'openai/widgetDescription': WIDGET_DESCRIPTION }
  };
};
```

**Cosa succede in fase di esecuzione:**

1. Un utente chiede alla piattaforma LLM *&quot;Mostra i tuoi prodotti per il caffè&quot;*
2. La piattaforma LLM corrisponde all&#39;intento di *Ricerca prodotti* ed estrae `category`.
3. Il server MCP chiama il gestore con `{ category: 'bagged-coffee' }`.
4. Il gestore filtra il catalogo e restituisce `content` (riepilogo testo per il modulo LLM) + `structuredContent` (array prodotto per il widget).
5. La piattaforma LLM mostra la risposta testuale e trasmette i dati strutturati al widget EDS, che esegue il rendering di un carosello di prodotto.

## Cosa succede se manca il gestore?

Se hai definito un’azione nell’interfaccia utente ma non hai ancora creato il file del gestore, l’azione viene comunque registrata al momento della distribuzione. Le chiamate utilizzano un gestore di stub predefinito che restituisce contenuto vuoto fino all&#39;aggiunta del codice effettivo. Ciò significa che puoi definire prima tutte le azioni nell’interfaccia utente e implementarle in modo incrementale.

