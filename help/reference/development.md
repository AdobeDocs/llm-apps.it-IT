---
title: Sviluppo e test del gestore locale
description: Struttura del progetto del gestore, comandi server locali, test MCP e unit test per le app Adobe LLM.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '664'
ht-degree: 0%
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

Per l’infrastruttura gestita di Adobe, richiedi l’accesso all’archivio prima di seguire questo riferimento. L’esecuzione del gestore locale non richiede le credenziali di Adobe, ma i servizi protetti a monte richiedono ancora il proprio accesso autorizzato.

## Struttura del progetto

La boilerplate del gestore corrente segue questo layout:

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

- **`entry.js`** è il punto di ingresso del webpack. Al momento della compilazione, rileva ogni file `actions/*/index.js` e lo raggruppa in un singolo `dist/index.js`. Non modificare il punto di ingresso.
- **`actions.json`** è ignorato. La pipeline di distribuzione lo scrive automaticamente dai metadati dell&#39;azione in [!DNL LLM Apps].
- **I test** sono attivi in `test/actions/`, **non** in `actions/`. La build individua i punti di ingresso del gestore e raggruppa le loro importazioni; mantieni i test al di fuori della struttura di individuazione.
- **I percorsi delle azioni** utilizzano l&#39;identificatore del codice o il relativo formato sillabato. Ad esempio, `search_products` viene risolto in `actions/search_products/index.js` o, per il codice generato, in `actions/search-products/index.js`. Il nome visualizzato non è la chiave di ricerca.
- **I dati di supporto**, ad esempio un `data.json` generato, vengono inclusi nel bundle quando il gestore li importa.

## Sviluppo locale

Per sviluppare e testare i gestori localmente senza credenziali Adobe, esegui:

```bash
npm install
npm run dev:local
```

Il comando di sviluppo crea il progetto con webpack e avvia un server HTTP Node.js semplice su `http://localhost:9080`. Arrestare ed eseguire di nuovo `npm run dev:local` dopo la modifica del gestore o dei metadati locali. Il comando non controlla i file.

Per utilizzare una porta diversa:

```bash
PORT=9081 npm run dev:local
```

### Comportamento metadati locale

L&#39;interfaccia utente corrente non fornisce un download `actions.json`, anche se il file README standard e il banner di avvio di runtime ne menzionano uno. Il server locale viene avviato anche senza un file popolato: la build crea `{"actions":[]}` quando il file risulta mancante e il runtime torna all&#39;individuazione del gestore.

L&#39;individuazione è utile per elencare i gestori, ma non è una modalità completa di test degli argomenti. Senza uno schema di input, il SDK MCP chiama il gestore con il contesto di runtime come primo argomento invece di utilizzare la firma `(args, extra)` normale. SDK non inoltra gli argomenti dell&#39;azione specificati.

Per le chiamate MCP con argomenti, crea un **local-only** `actions.json` con metadati rappresentativi corrispondenti all&#39;editor azioni. Per il gestore boilerplate `echo`:

```json
{
  "actions": [
    {
      "name": "echo",
      "description": "Echo the supplied message.",
      "inputSchema": {
        "type": "object",
        "properties": {
          "message": { "type": "string" }
        },
        "required": ["message"]
      }
    }
  ]
}
```

Quando la matrice `actions` non è vuota, il runtime registra solo le azioni denominate nella matrice. Utilizza l&#39;identificatore di codice di ogni azione come `name`. Anche un&#39;azione senza parametri richiede uno schema di oggetti con `"properties": {}` per mantenere la normale firma del gestore.

I metadati locali non aggiornano l’interfaccia utente o la configurazione di distribuzione. Il runtime convalida i tipi supportati, i campi obbligatori, le enumerazioni e gli elementi array, ma non tutti i vincoli dello schema JSON; convalida lunghezze, intervalli, oggetti nidificati e regole business nel gestore. I test di integrazione boilerplate utilizzano `test/fixtures/actions.json` come metadati rappresentativi.

### Variabili dell’app locale

Le variabili dell&#39;app raggiungono il gestore tramite `extra.variables`, non `process.env`. Leggili con `getVariable(extra, name)` da `@adobe/llm-apps-runtime`. Per simulare un&#39;impostazione non sensibile dopo la generazione:

```bash
npm run build
node server/local.js \
  --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' \
  --param GREETING_PREFIX=Howdy
```

Fornisci sia il manifesto JSON che il valore denominato. Includi il gestore in `actions.json` locale con uno schema di input durante il test del suo normale contratto `(args, extra)`. I flag della riga di comando non caricano le impostazioni dell’app dalla piattaforma. Consulta [Configurare le variabili e i segreti dell&#39;app](/help/guides/app-variables.md) per le impostazioni di distribuzione specifiche dell&#39;ambiente.

### Test con curl

Dopo aver aggiunto i metadati locali di `echo`, genera e avvia il server, quindi esegui:

```bash
# List all registered tools
curl -sX POST "http://localhost:9080" \
  -H 'content-type: application/json' \
  -H 'accept: application/json;q=1.0, text/event-stream;q=0.5' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list","params":{}}'

# Call the boilerplate echo action (returns "Echo: hello")
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

Gli unit test del gestore sono attivi in `test/actions/` e chiamano direttamente la funzione esportata, senza convalida dello schema MCP:

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
npm test                                 # configured Jest suite
npm test -- --runInBand test/actions/echo.test.js  # one boilerplate action
```

Alcuni test generati utilizzano l’esecuzione dei test integrata di Node invece di Jest. Controllare le importazioni del test e utilizzare l&#39;esecuzione corrispondente, ad esempio `node --test test/actions/search-products.test.js` per un file che importa `node:test`. Verificare le importazioni relative generate dopo aver inserito il file in `test/actions/`.

Gli unit test da soli non verificano i metadati, la convalida MCP, il caricamento dei widget o il comportamento degli host. Esegui il test dell’endpoint locale con metadati rappresentativi, quindi testa l’app implementata in una piattaforma LLM supportata.

Al termine dei test locali, invia le modifiche e segui [Distribuisci modifiche](/help/guides/deploy-your-app.md).
