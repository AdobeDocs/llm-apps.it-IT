---
title: Portate con voi il vostro progetto Edge Delivery Services
description: Collega un progetto esistente di Adobe Edge Delivery Services a un’azione di Adobe LLM Apps.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '472'
ht-degree: 2%

---


# Portate il vostro progetto EDS {#bring-your-own-eds}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Usa questa guida quando disponi già di un progetto Edge Delivery Services (EDS) o quando hai creato un’app senza crearla automaticamente.

Se la piattaforma ha creato il widget automaticamente, segui [Personalizzare un widget generato](/help/guides/widgets.md). Il progetto generato include già la configurazione di file, blocchi, contenuti e azioni SDK descritta qui.

**Percorso:** preparare il progetto EDS → installare la build di SDK → e pubblicare il blocco → configurare l&#39;azione → distribuire e testare.

## Prima di iniziare

Hai bisogno di:

- Repository EDS con [AEM Code Sync](https://github.com/apps/aem-code-sync) installato.
- Autorizzazione per aggiungere dipendenze e creare blocchi in tale archivio.
- Autorizzazione per configurare le intestazioni di risposta per il sito EDS.
- Azione in [!DNL LLM Apps] con un gestore che restituisce `structuredContent`.

## Installare il SDK delle app LLM

Dalla directory principale del progetto EDS:

```bash
npm install @adobe/llmapps-sdk
```

Il pacchetto copia il punto di ingresso del widget e l’implementazione del bridge nel progetto:

```text
scripts/
├── aem-embed.js
└── llmapps-sdk.js
```

L&#39;URL dello script utilizzato dall&#39;azione punta a `scripts/aem-embed.js`.

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
export default async function decorate(block, bridge) {
  if (bridge) {
    bridge.applyHostStyles();
  }

  const result = bridge ? await bridge.toolResult : null;
  const products = result?.structuredContent?.products ?? [];

  const list = document.createElement('ul');
  products.forEach((product) => {
    const item = document.createElement('li');
    item.textContent = String(product.name ?? 'Product');
    list.append(item);
  });

  block.replaceChildren(list);

  if (bridge) {
    bridge.autoResize(block);
  }
}
```

Utilizza le API DOM per la codifica di valori di testo. Non concatenare dati esterni in HTML.

## Creare e pubblicare la pagina widget

Creare una pagina EDS per il widget e aggiungere il blocco a tale pagina. Pubblica la pagina.

L&#39;URL della pagina live diventa l&#39;URL del widget dell&#39;azione:

```text
https://main--<repo>--<owner>.aem.live/<widget-page>
```

Il percorso della pagina non deve necessariamente corrispondere al nome dell’azione, ma una convenzione coerente semplifica la gestione del progetto.

## Configurare CORS

Il widget carica la pagina EDS, oltre a script, stili, blocchi e file multimediali di origini diverse. Configurare l&#39;intestazione per il sito EDS:

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

Utilizza l’origine host specifica richiesta dalla piattaforma LLM supportata. Utilizza `*` solo quando il widget è intenzionalmente pubblico, non utilizza richieste di origini diverse con credenziali e i requisiti di sicurezza lo consentono.

Per informazioni dettagliate sulla configurazione EDS, vedere [Servizio di configurazione](https://aem.live/docs/config-service-setup).

## Configurare l’azione

In [!DNL LLM Apps], aprire l&#39;azione e selezionare **[!UICONTROL Metadati widget]**.

Inserisci:

- **[!UICONTROL URL script]**

  ```text
  https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js
  ```

- **[!UICONTROL URL widget]**

  ```text
  https://main--<repo>--<owner>.aem.live/<widget-page>
  ```

Configura i domini CSP e le autorizzazioni del browser utilizzando i privilegi minimi. Aggiungi solo le origini e le funzionalità richieste dal widget.

Per le definizioni dei campi, vedere [Campi azione e widget](/help/reference/reference-docs.md).

## Testare l’integrazione

1. Visualizzare direttamente l&#39;anteprima della pagina EDS e verificarne il fallback dei dati di esempio.
2. Testa il gestore localmente e confronta il relativo `structuredContent` con la forma prevista dal blocco.
3. Distribuisci l’app nell’ambiente di staging.
4. Richiama l&#39;azione da [!DNL ChatGPT].
5. Verifica gli stati di caricamento, completamento, vuoto ed errore.

Se la pagina funziona direttamente ma non nella piattaforma LLM, selezionare CORS, CSP, URL HTTPS e la forma `structuredContent`. Consulta [Risoluzione dei problemi](/help/reference/troubleshooting.md).
