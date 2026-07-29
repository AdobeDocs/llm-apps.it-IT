---
title: Campi azione e widget
description: Definizioni dei campi per metadati di azioni, parametri, widget, CSP e autorizzazioni nelle app Adobe LLM.
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '606'
ht-degree: 5%

---


# Campi azione e widget {#action-widget-configuration}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Utilizza questa pagina per cercare i campi nell’editor delle azioni. Per il percorso di creazione completo, vedere [Creazione di un&#39;azione da zero](/help/guides/create-action.md).

## Parametri azione

I parametri di input sono i valori che la piattaforma LLM invia al gestore delle azioni. Il modello le estrae dal messaggio dell’utente e le mappa su questi campi.

| Proprietà | Descrizione |
|----------|-------------|
| **Nome** | Identificatore del parametro (ad esempio, `category`, `query`) |
| **Tipo** | `String`, `Number`, `Integer` o `Boolean` |
| **Descrizione** | Spiegazione leggibile: la piattaforma LLM utilizza questa funzione per estrarre il valore corretto |
| **Obbligatorio** | Se questa opzione è selezionata, il modello deve fornire questo parametro prima di richiamare l’azione |

### Parametri del file

I parametri di file sono nomi di campi di input configurati nell’editor delle azioni. Quando un utente carica un file, l&#39;host fornisce un oggetto file per tali argomenti, in genere inclusi `download_url` e `file_id`.

## Campi metadati

### Informazioni di base

| Campo | Obbligatorio | Descrizione |
|-------|----------|-------------|
| **Nome azione** | Sì | Nome visualizzato per l&#39;azione (ad esempio, *Cerca prodotti*) |
| **Descrizione** | Sì | Spiegazione delle operazioni eseguite: la piattaforma LLM utilizza questa proprietà per decidere quando richiamarla |

Dopo la creazione, l&#39;editor mostra anche un **identificatore di codice** immutabile. L&#39;azione viene mappata su `actions/<code-identifier>/index.js` nell&#39;archivio del gestore.

### Annotazioni

Hint facoltativi che descrivono il comportamento dell’azione:

| Annotazione | Descrizione |
|------------|-------------|
| **Suggerimento distruttivo** | L’azione modifica o elimina dati |
| **Idempotent** | La chiamata dell&#39;azione più volte con gli stessi argomenti produce lo stesso risultato |
| **Apri hint mondo** | L’azione interagisce con sistemi esterni |
| **Suggerimento di sola lettura** | L’azione legge solo i dati, non scrive mai |

### Metadati OpenAI

| Campo | Lunghezza massima | Descrizione |
|-------|------------|-------------|
| **Richiamo del testo di stato** | 64 caratteri | Messaggio visualizzato nella piattaforma LLM durante l&#39;esecuzione dell&#39;azione (ad esempio, *Caricamento prodotti ...* ) |
| **Testo di stato richiamato** | 64 caratteri | Messaggio visualizzato al termine dell&#39;azione (ad esempio, *Prodotti caricati ...* ) |
| **Descrizione widget** | 512 caratteri | Viene mappato su `_meta["openai/widgetDescription"]`; riepiloga il componente sottoposto a rendering per il modello e riduce i commenti ripetuti |

La descrizione dell&#39;azione controlla quando il modello seleziona l&#39;azione. La descrizione del widget spiega cosa viene visualizzato dal componente dopo il rendering.

### Visibilità

| Attiva/disattiva | Descrizione |
|--------|-------------|
| **Esposizione a modello di IA** | L’azione può essere richiamata dal modello di intelligenza artificiale durante le conversazioni |
| **Mostra come widget nella superficie dell&#39;app** | L’azione esegue il rendering di un widget visivo nell’app |

### Analisi

| Campo | Descrizione |
|-------|-------------|
| **Raccogli intento utente** | Raccoglie un riepilogo della conversazione che ha portato all’azione per analytics |

## Campi widget

### Informazioni widget

| Campo | Descrizione |
|-------|-------------|
| **Tipo** | Tecnologia widget — attualmente **[!UICONTROL EDS]** |
| **Dominio widget (origine sandbox)** | Origine in cui è ospitato il widget; deve essere univoca per app |
| **Preferisce il bordo** | Se questa opzione è selezionata, il widget esegue il rendering all’interno di una scheda con bordi nella piattaforma LLM |

### URL modello

| Campo | Descrizione |
|-------|-------------|
| **[!UICONTROL URL script]** | URL HTTPS per il punto di ingresso EDS `scripts/aem-embed.js`. Condiviso tra le azioni nello stesso progetto EDS |
| **URL widget** | URL HTTPS per la pagina EDS su cui è stato eseguito il rendering da questa azione. Le azioni generate configurano questo valore automaticamente |

## Configurazione CSP

L’informativa sulla sicurezza dei contenuti controlla quali domini esterni l’iframe del widget può contattare. Ogni dominio esterno deve essere inserito nell&#39;elenco Consentiti esplicitamente.

| Direttiva | Descrizione |
|-----------|-------------|
| **Domini risorse** | Domini per risorse statiche: immagini, font, script, stili |
| **Connetti domini** | Domini che il widget può contattare tramite `fetch`, `XHR` o `WebSocket` |
| **Domini frame** | Le origini sono consentite per gli iframe nidificati; attiva una revisione app più rigorosa |
| **Domini di reindirizzamento** | Destinazioni attendibili per `openExternal` collegamenti di reindirizzamento ([!DNL ChatGPT] specifici) |
| **Domini URI di base** | Direttiva CSP `base-uri` (solo SDK delle app MCP, non [!DNL ChatGPT]) |

## Autorizzazioni

API hardware e browser a cui il widget può accedere. Questi corrispondono ai criteri di autorizzazione per iframe.

| Autorizzazione | Descrizione |
|------------|-------------|
| **Fotocamera** | Accedere alla fotocamera del dispositivo |
| **Microfono** | Accedere al microfono del dispositivo |
| **Geolocalizzazione** | Accedere al percorso dell&#39;utente |
| **Appunti** | Leggi o scrivi negli Appunti |

