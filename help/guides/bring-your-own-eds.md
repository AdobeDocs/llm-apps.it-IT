---
title: Portate con voi il vostro progetto Edge Delivery Services
description: Collega un progetto esistente di Adobe Edge Delivery Services a un’azione di Adobe LLM Apps.
hidefromtoc: true
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '866'
ht-degree: 1%
---

# Crea un tuo progetto EDS {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Utilizzare questa guida per collegare un progetto Edge Delivery Services (EDS) esistente compatibile o per aggiungere un widget a un&#39;azione creata personalmente. Un progetto esistente è facoltativo. Se si imposta la propria infrastruttura, il percorso predefinito inizia con due archivi vuoti che la piattaforma inizializza durante la [configurazione dell&#39;infrastruttura](/help/guides/create-app.md).

Se la piattaforma ha creato il widget automaticamente, segui [Personalizzare un widget generato](/help/guides/widgets.md). Il progetto generato include già la configurazione di file, blocchi, contenuti e azioni SDK descritta qui.

**In questa guida:** scopri come preparare il tuo progetto EDS, installare SDK, generare e pubblicare un widget. Quindi connetti il widget a un&#39;azione e testalo nell&#39;app implementata.

## Prima di iniziare

Hai bisogno di:

- Repository EDS compatibile con AEM boilerplate con [AEM Code Sync](https://github.com/apps/aem-code-sync) installato e un&#39;origine di contenuto configurata.
- Autorizzazione per aggiungere dipendenze e creare blocchi in tale archivio.
- Autorizzazione per configurare le intestazioni di risposta per il sito EDS.
- Azione in [!DNL LLM Apps] con un gestore che restituisce `structuredContent`.

Quando si collega un repository esistente, la piattaforma controlla `scripts/aem.js`, `blocks/`, `styles/styles.css` e `head.html`. L&#39;incorporamento importa anche `scripts/scripts.js` e chiama la funzione `decorateMain` esportata per preparare i blocchi. Mantenere intatto il contratto di blocco EDS. I soli file richiesti non confermano che il rendering di un progetto fortemente personalizzato sia corretto.

## Installare il SDK delle app LLM {#install-the-llm-apps-sdk}

Dalla directory principale del progetto EDS:

```bash
npm install @adobe/llmapps-sdk
```

Il gancio post-installazione del pacchetto copia il punto di ingresso del widget, l’implementazione del bridge e il supporto delle librerie Adobe nel progetto:

```text
scripts/
├── aem-embed.js
├── llmapps-sdk.js
├── alloy-base.min.js
└── alloy-standalone.min.js
```

L&#39;URL dello script dell&#39;azione punta a `scripts/aem-embed.js`, non a `scripts/llmapps-sdk.js`. Verificare che l&#39;installazione abbia copiato i file SDK, quindi eseguirne il commit e il push con il progetto. L&#39;hook sovrascrive i file copiati quando si installa o si aggiorna SDK; non personalizzare i file SDK copiati.

## Creare il blocco widget

Crea un blocco per l’azione:

```text
blocks/
└── search-products/
    ├── search-products.js
    └── search-products.css
```

Esporta la funzione EDS `decorate` standard con il bridge connesso come secondo argomento:

```javascript
const PREVIEW_RESULT = {
  records: [{ id: 'P-100', name: 'Frescopa House Blend' }]
};

export default async function decorate(block, bridge) {
  if (bridge?.isConnected) {
    bridge.applyHostStyles();
  }

  const live = bridge?.isConnected && bridge.hostContext?.preview !== true;
  const result = live ? await bridge.toolResult : null;
  const data = live ? result?.structuredContent : PREVIEW_RESULT;
  const records = Array.isArray(data?.records) ? data.records : [];

  if (result?.isError) {
    const message = document.createElement('p');
    message.textContent = 'Product search is unavailable. Try again later.';
    block.replaceChildren(message);
    bridge.autoResize(block);
    return;
  }

  const list = document.createElement('ul');
  records.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product?.name ?? 'Product');
    list.append(item);
  });

  if (records.length === 0) {
    const item = document.createElement('li');
    item.textContent = 'No matching products were found.';
    list.append(item);
  }
  block.replaceChildren(list);

  if (bridge?.isConnected) {
    bridge.autoResize(block);
  }
}
```

Utilizza le API DOM per la codifica di valori di testo. Non concatenare dati esterni in HTML.

L&#39;esempio prevede `structuredContent.records`. Corrispondenza con i nomi delle proprietà e i campi record restituiti dal gestore. Selezionare `bridge.isConnected`, non solo se esiste un bridge: `toolResult` non viene mai risolto in modalità autonoma. Nell&#39;anteprima delle app LLM, utilizza i dati di esempio del blocco quando `bridge.hostContext.preview` indica la modalità di anteprima.

## Creare e pubblicare la pagina widget

Creare una pagina EDS per il widget e aggiungere il blocco a tale pagina. Pubblica la pagina.

L&#39;URL della pagina diventa l&#39;URL del widget dell&#39;azione. Ad esempio, la configurazione generata di default utilizza l&#39;origine di anteprima EDS:

```text
https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
```

Un&#39;origine EDS live come `https://main--<repo>--<owner>.aem.live` funziona anche quando la pagina e le risorse vengono pubblicate lì. Utilizza l’origine effettiva del sito; i siti EDS configurati possono avere nomi di sito e organizzazione diversi da quelli dell’archivio e del proprietario GitHub.

Verificare che l&#39;aggiunta di `.plain.html` all&#39;URL del widget restituisca il frammento di pagina e che la pagina contenga il blocco `search-products`. Il percorso della pagina non deve necessariamente corrispondere all’identificatore dell’azione, ma una convenzione coerente facilita la gestione del progetto.

## Configurare CORS

Il widget carica la pagina EDS, gli script, gli stili, i blocchi e i file multimediali tra diverse origini. Configurare l&#39;intestazione per il sito EDS:

```json
{
  "/**": [
    {
      "key": "access-control-allow-origin",
      "value": "<allowed-host-origin>"
    }
  ]
}
```

Utilizza l’origine effettiva della sandbox del widget richiesta dalla piattaforma LLM supportata, non solo l’origine del sito web di chat. Verificare CORS nella risposta di `.plain.html` e in JavaScript, stili, font e file multimediali importati.

L&#39;installazione automatica di EDS della piattaforma configura `access-control-allow-origin: *` per la distribuzione di widget pubblici e può sostituire un valore esistente più ristretto. Utilizza tale criterio solo per risorse intenzionalmente pubbliche senza richieste di cross-origin certificate e quando i requisiti di sicurezza lo consentono. Se gestisci un sito esistente con restrizioni, controlla i criteri prima di connetterlo; non esporre contenuto del sito privato per far funzionare un widget.

Per informazioni dettagliate sulla configurazione EDS, vedere [Servizio di configurazione](https://aem.live/docs/config-service-setup).

## Configurare l’azione

In [!DNL LLM Apps], aprire l&#39;azione e selezionare **[!UICONTROL Dettagli widget LLM]**. In **[!UICONTROL Informazioni widget]**, impostare **[!UICONTROL Tipo widget]** su **[!UICONTROL EDS]**, quindi espandere **[!UICONTROL URL modello]**.

Inserisci:

- **[!UICONTROL URL script]**

  ```text
  https://main--<repo>--<owner>.aem.page/scripts/aem-embed.js
  ```

- **[!UICONTROL URL widget]**

  ```text
  https://main--<repo>--<owner>.aem.page/eds-widgets/search-products-demo
  ```

Sostituisci gli esempi con la stessa origine del sito di lavoro e la stessa pagina verificate in precedenza. Configura i domini CSP e le autorizzazioni del browser utilizzando i privilegi minimi. L’origine EDS deve essere consentita sia per le connessioni, come il recupero del frammento di pagina, sia per le risorse, come gli script di blocchi e gli stili. Aggiungi altre origini solo quando richiesto dal widget.

Seleziona **[!UICONTROL Salva modifiche]**, quindi ridistribuisci nell&#39;ambiente che stai testando. La configurazione del widget salvato non aggiorna il server MCP in esecuzione fino alla distribuzione.

Per le definizioni dei campi, vedere [Campi azione e widget](/help/reference/reference-docs.md).

## Testare l’integrazione

1. Visualizzare direttamente l&#39;anteprima della pagina EDS e verificarne il fallback dei dati di esempio.
2. Testa il gestore localmente e confronta il relativo `structuredContent` con la forma prevista dal blocco.
3. Distribuisci il gestore e i metadati dell’azione nello stage. La pubblicazione del codice EDS e del contenuto è un passaggio separato.
4. Richiama l’azione da una piattaforma LLM supportata. Vedi [Test in ChatGPT](/help/guides/test-in-chatgpt.md) o [Test in Claude](/help/guides/test-in-claude.md).
5. Verifica gli stati di caricamento, completamento, vuoto ed errore.

Se la pagina funziona direttamente ma non nella piattaforma LLM, selezionare CORS, CSP, URL HTTPS e la forma `structuredContent`. Consulta [Risoluzione dei problemi](/help/reference/troubleshooting.md).
