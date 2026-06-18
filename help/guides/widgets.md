---
title: Configurazione del widget (EDS)
description: Scopri come impostare un progetto widget Edge Delivery Services e implementare il contratto di blocco per il rendering delle risposte visive nelle piattaforme LLM.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '1226'
ht-degree: 1%

---


# Configurazione del widget (EDS)

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Questa guida spiega come creare un widget EDS end-to-end: dalla configurazione dell&#39;azione nell&#39;interfaccia utente [!DNL LLM Apps] alla configurazione del progetto EDS, alla scrittura del codice di blocco che esegue il rendering dei dati all&#39;interno della piattaforma LLM. Per una panoramica di alto livello, vedi [Concetti di base](/help/overview/overview.md#widgets-eds).

## Il SDK [!DNL LLM Apps]

Tutto inizia con il pacchetto [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk) npm. SDK è la libreria JavaScript che alimenta il canale di comunicazione bidirezionale tra il widget e l’host LLM.

Il SDK invia anche `aem-embed.js`, il punto di ingresso specifico per EDS che collega SDK alla pipeline di blocchi EDS standard. Quando `npm install @adobe/llmapps-sdk`, uno script di post-installazione copia automaticamente due file nel progetto:

```
scripts/
└── llm-apps/
    ├── aem-embed.js     ← EDS widget entry point, ships with the SDK
    └── llmapps-sdk.js   ← core SDK, loaded internally by aem-embed.js
```

Nei progetti EDS, **non utilizzi mai SDK direttamente nel codice di blocco.** `aem-embed.js` crea e gestisce la connessione SDK e passa un&#39;istanza `LLMApp` completamente connessa al blocco come argomento `bridge` in `decorate(block, bridge)`. L&#39;API SDK completa è disponibile su `bridge`. Non è necessaria alcuna importazione.

Se si sta creando un widget **senza EDS** (un bundler standard o un progetto TypeScript), è possibile utilizzare direttamente SDK:

```javascript
import { LLMApp } from '@adobe/llmapps-sdk';

const app = new LLMApp({ appInfo: { name: 'MyWidget', version: '1.0.0' } });
await app.connect();

const { structuredContent } = await app.toolResult;
```

## Come si combina tutto

Quando l&#39;IA chiama l&#39;azione e il gestore restituisce `structuredContent`, la piattaforma LLM esegue il rendering di un widget interattivo nella conversazione. Tre fattori rendono possibile il funzionamento congiunto:

**Interfaccia utente [!DNL LLM Apps]**: quando crei un&#39;azione, immetti un **[!UICONTROL URL script]** e un **[!UICONTROL URL widget]** nella scheda Metadati widget. L&#39;URL dello script punta a `aem-embed.js`, il file che viene fornito con SDK e risiede nell&#39;archivio EDS in `scripts/llm-apps/aem-embed.js`. Indica alla piattaforma LLM quale script caricare quando viene richiamata l’azione.

**`aem-embed.js`** — la piattaforma LLM carica questo script in una superficie di widget in modalità sandbox. `aem-embed.js` è un elemento HTML personalizzato (`<aem-embed>`) che funge da punto di ingresso compatibile con EDS per il widget. Esegue l&#39;handshake con l&#39;host LLM utilizzando SDK, sopprime la normale pipeline della pagina EDS (senza intestazione/piè di pagina), recupera il contenuto della pagina EDS dall&#39;URL del widget, esegue la pipeline del blocco EDS e distribuisce un oggetto `bridge` attivo alla funzione `decorate()` di ciascun blocco.

**Codice blocco**: si scrive un blocco EDS standard che esporta una funzione `decorate(block, bridge)`. `bridge` è l&#39;istanza di SDK connessa. Fornisce il risultato strutturato dell&#39;azione e consente di inviare nuovamente messaggi alla conversazione.

## Aggiungi a un progetto EDS esistente

Se disponi già di un progetto EDS, sono disponibili solo due passaggi prima di poter iniziare a scrivere blocchi.

1. Installa `@adobe/llmapps-sdk`. Lo script di post-installazione copia `aem-embed.js` e `llmapps-sdk.js` in `scripts/llm-apps/`:

   ```bash
   npm install @adobe/llmapps-sdk
   ```

2. Configura le intestazioni CORS in modo che la piattaforma LLM possa caricare le pagine widget e gli script tra origini diverse. Vedi [Configurare le intestazioni CORS](#configure-cors-headers) di seguito.

Quindi crea il blocco seguendo il contratto [`decorate(block, bridge)`](#the-decorateblock-bridge-contract), crea la pagina del widget e immetti gli URL nella finestra di dialogo Crea azione.

## Imposta un nuovo progetto EDS

### Creare l’archivio

1. Crea un nuovo archivio [!DNL GitHub] basato sul modello [AEM boilerplate](https://github.com/adobe/aem-boilerplate).
2. Aggiungi l&#39;[app GitHub di sincronizzazione codice AEM](https://github.com/apps/aem-code-sync) all&#39;archivio.
3. Installare AEM CLI per lo sviluppo locale: `npm install -g @adobe/aem-cli`.
4. Installa `@adobe/llmapps-sdk`. Lo script di post-installazione copia `aem-embed.js` e `llmapps-sdk.js` in `scripts/llm-apps/`:

   ```bash
   npm install @adobe/llmapps-sdk
   ```

Per una guida completa sui progetti EDS, consulta l&#39;[esercitazione per sviluppatori di AEM](https://www.aem.live/developer/tutorial) e l&#39;[anatomia del progetto](https://www.aem.live/developer/anatomy-of-a-project).

Una volta configurato, il sito EDS sarà disponibile all&#39;indirizzo:

- **Anteprima:** `https://main--<repo>--<owner>.aem.page/`
- **Live:** `https://main--<repo>--<owner>.aem.live/`

### Struttura dell’archivio

```
my-brand-eds/
├── scripts/
│   ├── llm-apps/
│   │   ├── aem-embed.js           # Widget entry point — copied by post-install
│   │   └── llmapps-sdk.js         # Core SDK — copied by post-install
│   ├── aem.js                     # AEM core library
│   └── scripts.js                 # Site-level decoration and loading
├── blocks/
│   └── search-products/           # One folder per widget block
│       ├── search-products.js
│       └── search-products.css
├── styles/
│   └── styles.css
├── head.html
└── package.json
```

### Configurare le intestazioni CORS

Le pagine dei widget EDS vengono caricate all’interno di una superficie di widget in modalità sandbox dalla piattaforma LLM. Il sito EDS deve restituire `access-control-allow-origin` intestazioni corrette in modo che l&#39;host possa recuperare il contenuto del widget da un&#39;origine all&#39;altra.

Le intestazioni vengono configurate tramite il pannello di amministrazione di AEM in `admin.hlx.page` utilizzando il [servizio di configurazione](https://aem.live/docs/config-service-setup). Aggiungi intestazioni di risposta personalizzate per i percorsi in cui risiedono le pagine dei widget e gli script di SDK:

```json
{
  "/<your-widget-pages-path>/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ],
  "/scripts/**": [
    { "key": "access-control-allow-origin", "value": "*" }
  ]
}
```

>[!NOTE]
>
>L&#39;utilizzo di `*` come valore di origine è accettabile per il contenuto del widget pubblico nel dominio `.aem.live`. Se il sito contiene contenuto protetto, limita l’origine a domini specifici.

### Creare la pagina del widget

Creare una pagina nello strumento di creazione EDS e aggiungervi il blocco. L&#39;URL della pagina diventa l&#39;**[!UICONTROL URL widget]** configurato nell&#39;azione, ovvero l&#39;unica connessione tra l&#39;azione e il blocco. Non esiste alcun requisito di denominazione tra il blocco e il nome dell’azione.

![Authoring EDS - blocco aggiunto alla pagina widget](/help/assets/guide-widget/aem-author.png)

### Immetti gli URL nella finestra di dialogo Crea azione

Dopo aver configurato l&#39;archivio EDS, vai a **URL modello → metadati widget** durante la creazione dell&#39;azione:

**[!UICONTROL URL script]** — punta a `aem-embed.js` nell&#39;archivio EDS. Questo è lo stesso valore per ogni azione nello stesso progetto EDS:

```
https://main--<repo>--<owner>.aem.live/scripts/llm-apps/aem-embed.js
```

**[!UICONTROL URL widget]**: URL della pagina EDS creata per questo widget. Univoco per azione:

```
https://main--<repo>--<owner>.aem.live/<path-to-your-widget-page>
```

La piattaforma LLM carica `aem-embed.js` dall&#39;URL dello script. `aem-embed.js` recupera quindi `.plain.html` dall&#39;URL del widget per ottenere il contenuto del blocco.

## Flusso di dati

Percorso completo dal gestore a un widget di cui è stato eseguito il rendering:

1. **Il gestore azioni** restituisce `structuredContent`:

```javascript
// actions/search-products/index.js
return {
  structuredContent: {
    products: [
      { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
      { id: 'COF-002', name: 'Colombia Huila Natural', price: '$22', rating: 4.5 },
    ],
    total: 2,
    category: 'coffee'
  }
};
```

1. **La piattaforma LLM** apre una superficie di widget e carica `aem-embed.js` dall&#39;URL dello script.

1. **`aem-embed.js`** si connette all&#39;host tramite SDK, recupera `.plain.html` dall&#39;URL del widget, esegue la pipeline del blocco EDS e chiama `decorate(block, bridge)` sul blocco.

1. **Il blocco** legge i dati da `bridge.toolResult` ed esegue il rendering dell&#39;interfaccia utente.

1. **L&#39;interazione utente** attiva `bridge.sendMessage(...)` o `bridge.callTool(...)`, inviando un follow-up alla conversazione.

## Il contratto `decorate(block, bridge)`

Ogni blocco di widget EDS deve esportare una funzione `decorate` predefinita. Questa è la firma di blocco EDS standard, estesa con un secondo argomento, `bridge` connesso, che è un&#39;istanza SDK [`LLMApp`](https://www.npmjs.com/package/@adobe/llmapps-sdk) con l&#39;API completa disponibile:

```javascript
export default async function decorate(block, bridge) {
  // ...
}
```

`bridge` è presente solo quando viene eseguito all&#39;interno della superficie del widget della piattaforma LLM. Proteggi sempre le chiamate bridge in modo che il blocco venga riprodotto anche quando viene visualizzata in anteprima direttamente in un browser o nel server di sviluppo locale.

### Rendering dei dati dal risultato dell’azione

`bridge.toolResult` è una promessa che risolve con il risultato completo restituito dal gestore, incluso `structuredContent`.

```javascript
const SAMPLE_PRODUCTS = [
  { id: 'COF-001', name: 'Single Origin Ethiopian Coffee', price: '$18', rating: 4.7 },
];

export default async function decorate(block, bridge) {
  let products = SAMPLE_PRODUCTS;

  if (bridge) {
    const result = await bridge.toolResult;
    products = result?.structuredContent?.products ?? [];
  }

  block.innerHTML = products.map(p => `
    <div class="product-card">
      <h3>${p.name}</h3>
      <p class="price">${p.price}</p>
      <button data-id="${p.id}">Tell me more</button>
    </div>
  `).join('');
}
```

### Applicazione del tema host

Chiamare `bridge.applyHostStyles()` all&#39;inizio di `decorate` per inserire nel widget le variabili e i font CSS dell&#39;host (tema chiaro/scuro, tipografia). In questo modo il widget rimane visivamente coerente con l’interfaccia utente della piattaforma LLM circostante.

```javascript
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }
  // ...
}
```

Per reagire alle modifiche del tema in fase di runtime (ad esempio, quando l’utente passa dalla modalità chiara alla modalità scura):

```javascript
if (bridge) {
  bridge.onContextChange(ctx => {
    block.dataset.theme = ctx.theme; // 'light' | 'dark'
  });
}
```

### Invio di un messaggio di completamento

`bridge.sendMessage(text)` inserisce un messaggio utente nella conversazione. Questo è il modo principale in cui un widget attiva un’ulteriore interazione di intelligenza artificiale, ad esempio quando un utente fa clic su una scheda del prodotto per richiedere i dettagli.

```javascript
block.querySelectorAll('button[data-id]').forEach(btn => {
  btn.addEventListener('click', () => {
    bridge.sendMessage(`Show me details for product ${btn.dataset.id}`);
  });
});
```

### Chiamata diretta di un&#39;altra azione

`bridge.callTool(name, args)` richiama un&#39;altra azione dall&#39;interno del widget senza passare attraverso un messaggio utente. Utile per caricare dati correlati su richiesta.

```javascript
btn.addEventListener('click', async () => {
  const result = await bridge.callTool('get-product-details', { id: product.id });
  renderDetails(result.structuredContent);
});
```

### Ridimensionamento automatico del widget

La piattaforma LLM ridimensiona il widget in base a ciò che viene riportato. Utilizza `bridge.autoResize(element)` per mantenere sincronizzata l&#39;altezza del widget man mano che il contenuto cambia, usa un `ResizeObserver` internamente. Chiamalo dopo il rendering iniziale:

```javascript
export default async function decorate(block, bridge) {
  // ... render content ...

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

In alternativa, puoi segnalare manualmente una dimensione fissa:

```javascript
bridge.reportSize(block.offsetWidth, block.offsetHeight);
```

### Modalità Anteprima e sviluppo locale

Quando si visualizza in anteprima una pagina EDS direttamente in un browser o sul server di sviluppo locale, `bridge` è `undefined`. Utilizza il pattern di fallback dei dati di esempio mostrato qui sopra in modo che il blocco venga eseguito immediatamente senza un gestore live.

Per avviare un server di sviluppo locale:

```bash
npm install -g @adobe/aem-cli
aem up
```

Verrà aperto `http://localhost:3000`, in cui è possibile passare alle pagine del widget e visualizzare i blocchi di rendering con dati di esempio. Le modifiche apportate al blocco JS e CSS vengono applicate immediatamente.

## Passaggi successivi

- [Guida: scrivere il gestore azioni](/help/guides/write-action-handler.md)

