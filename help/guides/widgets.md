---
title: Personalizzare un widget EDS generato
description: Comprendere e personalizzare il widget Edge Delivery Services creato automaticamente dalle app Adobe LLM.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '646'
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
>Questa guida presuppone una conoscenza di base di Adobe Edge Delivery Services (EDS). Se non hai ancora utilizzato EDS, prima di personalizzare un widget leggi l&#39;esercitazione per sviluppatori [EDS](https://www.aem.live/developer/tutorial) e [Esplorazione dei blocchi](https://www.aem.live/docs/exploring-blocks) per scoprire gli elementi di base, ovvero i blocchi, la funzione `decorate` e la struttura del progetto EDS.

La piattaforma crea un widget EDS per ogni azione generata. Il widget riceve già il risultato dell&#39;azione, esegue il rendering dei dati di esempio, applica lo stile host ed è collegato all&#39;azione in [!DNL LLM Apps].

Inizia testando il widget generato. Quindi personalizzane il contratto dati, l’interazione e la progettazione visiva.

**Percorso:** Trovare il blocco generato → allineare il relativo contratto dati → personalizzarlo in modo sicuro → l&#39;anteprima in locale → distribuire e testare.

## Trova il widget generato

Apri l&#39;archivio EDS selezionato al momento della creazione dell&#39;app. Ogni widget generato è un blocco EDS:

```text
blocks/
└── <action-name>/
    ├── <action-name>.js
    └── <action-name>.css
```

- Il file JavaScript legge il risultato dell’azione e crea l’interfaccia.
- Il file CSS controlla il layout, il comportamento reattivo e la progettazione visiva.
- La richiesta di pull generata mostra i file esatti creati per l’azione.

La piattaforma configura anche gli URL del widget e i file di supporto SDK. Non è necessario creare un secondo progetto EDS o immettere nuovamente tali valori per personalizzare un widget generato.

## Collegamento del widget da parte del SDK applicazioni LLM

Il pacchetto `@adobe/llmapps-sdk` connette il widget EDS all&#39;host LLM. L&#39;archivio EDS generato include:

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

Non si importa il SDK nel blocco. `bridge` connesso viene fornito automaticamente. Consente al widget di:

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
  content: [{ type: 'text', text: `Found ${products.length} products.` }],
  structuredContent: { products, total: products.length }
};
```

```javascript
// EDS block
export default async function decorate(block, bridge) {
  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];
  // Render products.
}
```

Quando modifichi `structuredContent`, aggiorna il gestore e il widget insieme. Vedi [Personalizzare un gestore generato](/help/guides/customize-handler.md) per il contratto di reso completo.

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
  button.addEventListener('click', () => {
    if (bridge && product.id) {
      bridge.sendMessage(`Show me details for product ${String(product.id)}`);
    }
  });

  card.append(title, button);
  return card;
}
```

Convalidare gli URL prima di assegnarli a `href` o `src` e consentire solo i protocolli richiesti dall&#39;esperienza.

## Utilizzare il bridge host

EDS passa un bridge connesso a `decorate(block, bridge)`. Controlla le chiamate del ponte in modo che il blocco venga riprodotto anche durante l’anteprima EDS diretta.

### Applicare gli stili host

```javascript
if (bridge) {
  bridge.applyHostStyles();
}
```

Questo applica la tipografia host e le variabili tema. Il CSS del widget deve supportare i temi host sia chiaro che scuro.

### Inviare un messaggio di follow-up

```javascript
await bridge.sendMessage('Show me similar products.');
```

Utilizza `sendMessage` quando un&#39;interazione deve continuare la conversazione.

### Richiama un&#39;altra azione

```javascript
const result = await bridge.callTool('get-product-details', {
  id: product.id
});
```

Utilizza `callTool` per un&#39;interazione esplicita che richiede un altro risultato dell&#39;azione. Passa solo i valori convalidati e gestisci gli errori senza esporre i dettagli interni.

### Mantieni sincronizzate le dimensioni del widget

```javascript
if (bridge) {
  bridge.autoResize(block);
}
```

Chiamare `autoResize` dopo il rendering iniziale in modo che l&#39;host possa rispondere alle modifiche al contenuto.

## Visualizzare l&#39;anteprima delle modifiche

I blocchi generati devono includere dati di esempio per l&#39;anteprima diretta quando `bridge` non è disponibile.

Per visualizzare localmente l&#39;anteprima del progetto EDS:

```bash
npm install -g @adobe/aem-cli
aem up
```

Apri la pagina del widget generato in `http://localhost:3000`. Verifica:

- Stati vuoti, di caricamento, di completamento e di errore.
- Testo lungo e campi facoltativi mancanti.
- Navigazione tramite tastiera e messa a fuoco visibile.
- Temi chiari e scuri.
- Layout stretti e larghi.

Quindi distribuisci l&#39;app per la gestione temporanea e il test con `structuredContent` live nella piattaforma LLM.

## Pubblicare la personalizzazione

1. Eseguire il commit e inviare le modifiche EDS.
2. Se hai modificato la forma dati, esegui il commit e invia le modifiche al gestore corrispondente.
3. Distribuisci l’app nell’ambiente di staging.
4. Verificare l&#39;azione e il widget in [!DNL ChatGPT].
5. Promuovi la versione verificata in produzione.

## Altre impostazioni EDS

Se non hai creato l&#39;app automaticamente o non desideri integrare un sito EDS esistente, consulta [Acquista un tuo progetto EDS](/help/guides/bring-your-own-eds.md).
