---
title: Sviluppo per le app Adobe LLM
description: Struttura del progetto, flusso di lavoro di sviluppo locale e configurazione di test per il codice del gestore delle app Adobe LLM.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '324'
ht-degree: 4%

---


# Sviluppo {#development}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Questa sezione descrive la struttura del progetto del gestore, il flusso di lavoro di sviluppo locale e la configurazione di test per [!DNL Adobe LLM Apps]. Per il contratto del gestore e il codice di esempio, vedere [Scrivere il gestore azioni](/help/guides/write-action-handler.md).

## Struttura del progetto

L’archivio collegato segue questo layout:

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   ├── search-products/
│   │   └── index.js           # Handler (async function)
│   ├── get-product-details/
│   │   └── index.js
│   └── echo/
│       └── index.js
├── test/
│   ├── actions/
│   │   └── search-products.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — local copy of UI metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

Punti chiave:

- **`entry.js`** è il punto di ingresso del webpack. Al momento della compilazione, rileva ogni file `actions/*/index.js` e lo raggruppa in un singolo `dist/index.js`. Non modificare.
- **`actions.json`** è ignorato. Scaricala dalla pagina Azioni nell’interfaccia utente per lo sviluppo locale. Per le distribuzioni, la pipeline lo scrive automaticamente dall’API.
- **I test** sono attivi in `test/actions/`, **non** in `actions/`. Webpack racchiude tutto ciò che si trova in `actions/` nell&#39;artefatto distribuito. I test di co-localizzazione verrebbero inviati a [!DNL Adobe I/O Runtime].

## Sviluppo locale

Puoi sviluppare e testare i gestori localmente senza credenziali Adobe:

```bash
npm install
npm run dev:local
```

In questo modo viene creato il progetto con Webpack e viene avviato un server HTTP Node.js semplice su `http://localhost:9080`. Il server rileva automaticamente i file del gestore in `actions/` e li registra come strumenti MCP.

### Scarica `actions.json`

Affinché il server locale sia a conoscenza dei metadati dell&#39;azione (nome, descrizione, schema di input), scaricare `actions.json` dalla pagina Azioni nell&#39;interfaccia utente di [!DNL LLM Apps] e inserirlo nella directory principale dell&#39;archivio. Senza di esso, il server individua i gestori ma li registra con metadati minimi.

È inoltre possibile copiare `actions.example.json` in `actions.json` come punto di partenza.

### Test con curl

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the search-products action
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"search-products","arguments":{"category":"bagged-coffee"}}}'
```

### Test con MCP Inspector

```bash
npx @modelcontextprotocol/inspector
```

Imposta **Transport Type** su `streamable-http` e **URL** su `http://localhost:9080`.

## Test

Gli unit test del gestore risiedono in `test/actions/` e rispecchiano il layout `actions/`:

```javascript
// test/actions/search-products.test.js
const handler = require('../../actions/search-products/index.js')

test('returns all products when no filter is given', async () => {
  const result = await handler({})
  expect(result.content[0].text).toContain('product')
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('filters by category', async () => {
  const result = await handler({ category: 'bagged-coffee' })
  expect(result.structuredContent.products.every(
    (p) => p.category === 'bagged-coffee'
  )).toBe(true)
})

test('filters by query', async () => {
  const result = await handler({ query: 'dark-roast' })
  expect(result.structuredContent.products.length).toBeGreaterThan(0)
})

test('returns empty result for unknown category', async () => {
  const result = await handler({ category: 'nonexistent' })
  expect(result.structuredContent.products).toHaveLength(0)
})
```

Esegui test con:

```bash
npm test                                      # all tests
npx jest test/actions/search-products        # one action only
```

## Distribuzione

Non generare o distribuire manualmente. Per informazioni dettagliate sulla pipeline di distribuzione, vedere [Distribuire l&#39;app](/help/guides/deploy-your-app.md).

Il flusso di lavoro quotidiano è:

| Passaggio | Azione |
|------|--------|
| &#x200B;1. Gestore scrittura o modifica | `actions/<name>/index.js` |
| &#x200B;2. Scarica metadati | Pagina Azioni → **Scarica actions.json** |
| &#x200B;3. Test locale | `npm run dev:local` |
| &#x200B;4. Codice push | `git push` |
| &#x200B;5. Distribuzione | Pagina Dettagli app → **[!UICONTROL Distribuisci]** |

