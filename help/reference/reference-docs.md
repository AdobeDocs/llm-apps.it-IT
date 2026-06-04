---
title: Riferimento
description: Riferimento a livello di campo per la configurazione dell’azione nell’interfaccia utente delle app Adobe LLM.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '489'
ht-degree: 6%

---


# Riferimento

>[!IMPORTANT]
>
>**Dichiarazione di non responsabilità:** Versione beta di [!DNL LLM Apps]. Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale dell’applicazione o del prodotto.

Questa sezione fornisce un riferimento a livello di campo per la configurazione dell&#39;azione nell&#39;interfaccia utente [!DNL LLM Apps].

## Parametri azione

I parametri di input sono i valori che la piattaforma LLM ([!DNL ChatGPT], Claude) invia al gestore azioni. Il modello le estrae dal messaggio dell’utente e le mappa automaticamente su questi campi.

| Proprietà | Descrizione |
|----------|-------------|
| **Nome** | Identificatore del parametro (ad esempio, `category`, `query`) |
| **Tipo** | `String`, `Number`, `Integer` o `Boolean` |
| **Descrizione** | Spiegazione leggibile: la piattaforma LLM utilizza questa funzione per estrarre il valore corretto |
| **Obbligatorio** | Se questa opzione è selezionata, il modello deve fornire questo parametro prima di richiamare l’azione |

### Parametri del file

I parametri dei file contengono oggetti file con proprietà `download_url` e `file_id`. Definisci i nomi dei campi di input che devono ricevere i dati del file quando un utente carica un file nella conversazione.

## Campi metadati

### Informazioni di base

| Campo | Obbligatorio | Descrizione |
|-------|----------|-------------|
| **Nome azione** | Sì | Identificatore dell&#39;azione (ad esempio, *Cerca prodotti*) |
| **Descrizione** | Sì | Spiegazione delle operazioni eseguite: la piattaforma LLM utilizza questa proprietà per decidere quando richiamarla |

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

### Visibilità

| Attiva/disattiva | Descrizione |
|--------|-------------|
| **Esposizione a modello di IA** | L’azione può essere richiamata dal modello di intelligenza artificiale durante le conversazioni |
| **Mostra come widget nella superficie dell&#39;app** | L’azione esegue il rendering di un widget visivo nell’app |

### Informazioni widget

| Campo | Descrizione |
|-------|-------------|
| **Tipo** | Tecnologia widget — attualmente **[!UICONTROL EDS]** |
| **Dominio widget (origine sandbox)** | Origine in cui è ospitato il widget; deve essere univoca per app |
| **Preferisce il bordo** | Se questa opzione è selezionata, il widget esegue il rendering all’interno di una scheda con bordi nella piattaforma LLM |

### URL modello

| Campo | Descrizione |
|-------|-------------|
| **[!UICONTROL URL script]** | Script del punto di ingresso - `https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js`. Condiviso tra tutte le azioni |
| **URL di incorporamento widget** | Pagina EDS per questa azione - `https://main--<repo>--<owner>.aem.live/eds-widgets/<action-name>`. Univoco per azione |

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

