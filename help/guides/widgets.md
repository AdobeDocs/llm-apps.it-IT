---
title: Personalizzare un widget EDS generato
description: Comprendere e personalizzare il widget Edge Delivery Services creato automaticamente dalle app Adobe LLM.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1065'
ht-degree: 0%
---

# Personalizzare un widget generato {#customize-generated-widget}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

>[!NOTE]
>
>Questa guida presuppone una conoscenza di base di Adobe Edge Delivery Services (EDS). Se non hai ancora utilizzato EDS, leggi l&#39;[esercitazione per sviluppatori EDS](https://www.aem.live/developer/tutorial) e [Esplorazione dei blocchi](https://www.aem.live/docs/exploring-blocks) prima di personalizzare un widget. Queste risorse coprono i blocchi, la funzione `decorate` e la struttura del progetto EDS.

Dopo la generazione corretta, la piattaforma fornisce un widget EDS collegato all&#39;azione in [!DNL LLM Apps]. Il widget esegue il rendering dei dati rappresentativi per l’anteprima e legge i risultati del gestore quando si è connessi a una piattaforma LLM supportata. Rivedi la progettazione e il comportamento generati prima di personalizzare il widget.

Inizia testando il widget generato. Quindi personalizzane il contratto dati, l’interazione e la progettazione visiva.

**In questa guida:** scopri come trovare il widget generato e adattarlo ai dati dell&#39;azione e alla progettazione del tuo marchio. Quindi visualizza l’anteprima locale delle modifiche e testale nell’app implementata.

## Trova il widget generato

Aprire l&#39;archivio EDS connesso durante la [configurazione infrastruttura](/help/guides/create-app.md). Se utilizzi l’infrastruttura gestita di Adobe, richiedi l’accesso al codice prima di continuare. Ogni widget generato è un blocco EDS:

```text
blocks/
└── <action-slug>/
    ├── <action-slug>.js
    └── <action-slug>.css
```

- Il file JavaScript legge il risultato dell’azione e crea l’interfaccia.
- Il file CSS controlla il layout, il comportamento reattivo e la progettazione visiva.
- La richiesta di pull generata mostra i file esatti creati per l’azione.

Lo slug utilizza i trattini: un&#39;azione con identificatore di codice `search_products` utilizza un blocco come `blocks/search-products/`. La pagina widget generata è in genere `/eds-widgets/search-products-demo`. Utilizzare l&#39;**[!UICONTROL URL widget]** effettivo nei metadati azione.

La piattaforma configura anche gli URL del widget e i file di supporto SDK. Non è necessario creare un secondo progetto EDS o immettere nuovamente gli URL del widget per personalizzare un widget generato.

## Collegamento del widget da parte del SDK applicazioni LLM

Il pacchetto `@adobe/llmapps-sdk` connette il widget EDS all&#39;host LLM. I punti di ingresso nell&#39;archivio EDS generato sono:

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

`aem-embed.js` stabilisce la connessione host, carica la pagina EDS e chiama il blocco:

```javascript
export default async function decorate(block, bridge) {
  // Customize the widget here.
}
```

L&#39;incorporamento recupera il contenuto `.plain.html` della pagina widget e ne importa i blocchi. Non si importa il SDK nel blocco. L&#39;incorporamento fornisce automaticamente `bridge`; il normale caricamento della pagina EDS non lo fornisce. Il ponte consente al widget di:

- Leggi il risultato del gestore con `bridge.toolResult`.
- Applica lo stile host con `bridge.applyHostStyles()`.
- Continua la conversazione con `bridge.sendMessage()`.
- Richiama un&#39;altra azione con `bridge.callTool()`.
- Mantieni le dimensioni sincronizzate con `bridge.autoResize()`.

Questa guida descrive i metodi di bridge più comuni. Vedere il pacchetto [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk) per l&#39;API completa.

## Comprendere il contratto dati

Il gestore azioni restituisce `structuredContent` e il blocco lo legge da `bridge.toolResult`.

```javascript
// Handler result
return {
  content: [{ type: 'text', text: `Found ${records.length} products.` }],
  structuredContent: { arguments: { query: 'coffee' }, records }
};
```

```javascript
// EDS block
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];
  // Render records or an error state when result?.isError is true.
}
```

In questo esempio, `PREVIEW_RESULT` è l&#39;oggetto di dati di esempio locale del blocco. I widget attualmente generati utilizzano `structuredContent.arguments` e `structuredContent.records`; verificare la forma esatta nei file generati. Non sostituire mai un risultato live vuoto con record di anteprima.

`bridge.toolResult` viene risolto solo con il primo risultato. Utilizzare `bridge.onToolResult(callback)` se un widget rimane montato e deve gestire i risultati successivi.

Quando modifichi `structuredContent`, aggiorna il gestore e il widget insieme. Non presumere che i dati strutturati siano nascosti dal modello; fanno parte del risultato dell&#39;azione dell&#39;host. Vedi [Personalizzare un gestore generato](/help/guides/customize-handler.md) per il contratto di reso completo.

## Rendering sicuro dei dati esterni

Considera l’output del gestore come dati non attendibili. Preferisci API DOM come `textContent` invece di inserire valori di risposta in `innerHTML`.

```javascript
function createProductCard(product, bridge) {
  const card = document.createElement('article');
  card.className = 'product-card';

  const title = document.createElement('h3');
  title.textContent = String(product.name ?? 'Product');

  const button = document.createElement('button');
  button.type = 'button';
  button.textContent = 'Tell me more';
  button.disabled = !bridge?.isConnected;
  const status = document.createElement('p');
  status.setAttribute('role', 'status');
  button.addEventListener('click', async () => {
    if (bridge?.isConnected && typeof product.id === 'string') {
      try {
        await bridge.sendMessage(`Show me details for product ${product.id}`);
      } catch {
        status.textContent = 'Unable to send the request. Try again.';
      }
    }
  });

  card.append(title, button, status);
  return card;
}
```

Convalidare gli URL prima di assegnarli a `href` o `src` e consentire solo i protocolli richiesti dall&#39;esperienza.

## Utilizzare il bridge host

L&#39;incorporamento passa un ponte a `decorate(block, bridge)`. Proteggi le chiamate interattive con `bridge?.isConnected` in modo che il blocco venga riprodotto anche durante l&#39;anteprima EDS diretta. Un oggetto bridge può esistere in modalità standalone senza essere connesso; l&#39;attesa di `toolResult` in modalità standalone non viene mai risolta. L&#39;anteprima delle app LLM imposta anche `bridge.hostContext.preview` per consentire al blocco di scegliere dati di esempio.

### Applicare gli stili host

```javascript
if (bridge?.isConnected) {
  bridge.applyHostStyles();
}
```

Il metodo applica le variabili CSS host e gli stili di carattere disponibili. L&#39;applicazione degli stili host è facoltativa: i widget generati possono utilizzare i propri token di progettazione. L&#39;incorporamento mantiene `.light` e `.dark` classi nel relativo corpo shadow. Utilizza `bridge.hostContext.theme` e `bridge.onContextChange(callback)` quando il blocco deve anche reagire alle modifiche del tema.

### Inviare un messaggio di follow-up

```javascript
if (bridge?.isConnected) {
  await bridge.sendMessage('Show me similar products.');
}
```

Utilizza `sendMessage` per continuare la conversazione dopo un&#39;interazione.

### Richiama direttamente un’azione

```javascript
if (bridge?.isConnected) {
  const result = await bridge.callTool('search_products', { query: 'coffee' });
  // Re-render this widget from result.structuredContent, or show result.isError.
}
```

Utilizza `callTool` per un&#39;interazione esplicita che richiede un risultato dell&#39;azione. Passa l&#39;identificatore di codice dell&#39;azione di destinazione effettiva, non il nome della cartella di blocco o il nome visualizzato; `search_products` è un esempio. L’host deve supportare le chiamate di azione e l’azione di destinazione deve consentire la visibilità dell’app. La chiamata di un&#39;azione non esegue automaticamente il rendering del widget corrente. Passa solo i valori convalidati e gestisci le chiamate rifiutate senza esporre i dettagli interni.

Se è necessario modificare la visibilità dell&#39;azione di destinazione, modificare solo tale azione in **[!UICONTROL Dettagli azione]**, salvare la modifica e ridistribuire nell&#39;ambiente in fase di test.

### Mantieni sincronizzate le dimensioni del widget

```javascript
if (bridge?.isConnected) {
  bridge.autoResize(block);
}
```

Per consentire all&#39;host di rispondere alle modifiche al contenuto, chiamare `autoResize` dopo il rendering iniziale. Il metodo restituisce una funzione di pulizia che interrompe l&#39;osservazione quando il widget viene rimosso.

## Visualizzare l&#39;anteprima delle modifiche

I blocchi generati includono dati di esempio per l’anteprima diretta quando il bridge è mancante, disconnesso o contrassegnato come anteprima. Mantenere il fallback dei dati di esempio separato dagli stati live vuoti e di errore.

Per visualizzare localmente l&#39;anteprima del progetto EDS:

```bash
npm install -g @adobe/aem-cli
aem up
```

Aprire il percorso dall&#39;URL del widget dell&#39;azione sul server locale, ad esempio `http://localhost:3000/eds-widgets/search-products-demo`, anziché solo dalla directory principale del sito. Verifica:

- Stati vuoti, di caricamento, di completamento e di errore.
- Testo lungo e campi facoltativi mancanti.
- Navigazione tramite tastiera e messa a fuoco visibile.
- Temi chiari e scuri.
- Layout stretti e larghi.

L&#39;anteprima EDS diretta non verifica i dati dell&#39;handshake host o dell&#39;azione live. Pubblica le modifiche EDS e verifica con `structuredContent` live nella piattaforma LLM supportata.

## Pubblicare la personalizzazione

1. Eseguire il commit e inviare le modifiche EDS.
2. Verifica che la pagina del widget e gli script e gli stili modificati siano disponibili negli URL EDS configurati. Pubblicare le modifiche al contenuto tramite il flusso di lavoro di pubblicazione EDS.
3. Se hai modificato il gestore o i metadati dell’azione, invia tali modifiche e distribuisci l’app in Stage.
4. Testare l&#39;azione e il widget in una piattaforma LLM supportata. Vedi [Test in ChatGPT](/help/guides/test-in-chatgpt.md) o [Test in Claude](/help/guides/test-in-claude.md).
5. Dopo il completamento del test, distribuisci le modifiche del gestore e dei metadati in Produzione in base alle esigenze.

La pubblicazione EDS è distinta dalla distribuzione nell’app. Se Stage e Production utilizzano gli stessi URL EDS, le modifiche dei widget in corrispondenza di tali URL influiscono su entrambi gli ambienti; la distribuzione in Stage non crea una copia di widget isolata.

## Altre impostazioni EDS

Se le azioni sono state create personalmente o si desidera integrare un sito EDS esistente compatibile, vedere [Aggiungere un progetto EDS personalizzato](/help/guides/bring-your-own-eds.md).
