---
title: Sviluppo e test del gestore locale
description: Struttura del progetto del gestore, comandi server locali, test MCP e unit test per le app Adobe LLM.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '280'
ht-degree: 1%

---


# Sviluppo e test del gestore locale {#development}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Utilizza questo riferimento durante lo sviluppo locale di gestori. Per il contratto dei risultati del gestore, vedere [Personalizzare un gestore generato](/help/guides/customize-handler.md).

## Requisiti

- Node.js 24 o versione successiva.
- npm.
- Clone locale dell&#39;archivio del gestore collegato.

## Struttura del progetto

L’archivio collegato segue questo layout:

```
your-llm-app/
├── entry.js                   # Webpack entry — do not modify
├── actions/                   # One folder per action
│   └── echo/
│       └── index.js           # Example handler
├── test/
│   ├── actions/
│   │   └── echo.test.js
│   ├── fixtures/
│   │   └── actions.json
│   ├── html-transform.js
│   ├── jest.setup.js
│   └── server.test.js
├── server/
│   └── local.js               # Local dev server (port 9080)
├── actions.json               # Gitignored — optional local metadata
├── app.config.yaml            # Adobe I/O Runtime config
├── webpack.config.js
└── package.json
```

Punti chiave:

- **`entry.js`** è il punto di ingresso del webpack. Al momento della compilazione, rileva ogni file `actions/*/index.js` e lo raggruppa in un singolo `dist/index.js`. Non modificare.
- **`actions.json`** è ignorato. La pipeline di distribuzione lo scrive automaticamente dai metadati dell&#39;azione in [!DNL LLM Apps].
- **I test** sono attivi in `test/actions/`, **non** in `actions/`. Webpack racchiude tutto ciò che si trova in `actions/` nell&#39;artefatto distribuito. I test di co-localizzazione verrebbero inviati a [!DNL Adobe I/O Runtime].

## Sviluppo locale

Puoi sviluppare e testare i gestori localmente senza credenziali Adobe:

```bash
npm install
npm run dev:local
```

In questo modo viene creato il progetto con Webpack e viene avviato un server HTTP Node.js semplice su `http://localhost:9080`. Il server rileva automaticamente i file del gestore in `actions/` e li registra come strumenti MCP.

### Comportamento metadati locale

L&#39;interfaccia utente corrente non fornisce un download `actions.json`. È possibile eseguire il server locale senza questo file; rileva i gestori in `actions/` e li registra con metadati minimi.

Senza `actions.json`, gli argomenti dell&#39;azione locale non vengono convalidati in base allo schema di input dell&#39;interfaccia utente. Gli unit test e gli integration test utilizzano `test/fixtures/actions.json` per i metadati rappresentativi.

### Test con curl

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"echo","arguments":{"message":"hello"}}}'
```

### Test con MCP Inspector

```bash
npx @modelcontextprotocol/inspector
```

Imposta **Transport Type** su `streamable-http` e **URL** su `http://localhost:9080`.

## Test

Gli unit test del gestore risiedono in `test/actions/` e rispecchiano il layout `actions/`:

```javascript
// test/actions/echo.test.js
const handler = require('../../actions/echo/index.js')

test('echoes the message', async () => {
  const result = await handler({ message: 'hello' })
  expect(result.content[0].text).toBe('Echo: hello')
})

test('always returns content parts', async () => {
  const result = await handler({})
  expect(Array.isArray(result.content)).toBe(true)
})
```

Esegui test con:

```bash
npm test                                      # all tests
npx jest test/actions/echo                   # one action only
```

Al termine dei test locali, invia le modifiche e segui [Distribuisci modifiche](/help/guides/deploy-your-app.md).

