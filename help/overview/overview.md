---
title: Panoramica delle app Adobe LLM
description: Scopri cos’è un’app Adobe LLM, come funziona e cosa serve per iniziare.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '970'
ht-degree: 1%

---


# App Adobe LLM: panoramica {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

## Cos&#39;è [!DNL Adobe LLM Apps]?

[!DNL Adobe LLM Apps] consente al tuo marchio di offrire azioni utili, come l&#39;individuazione dei prodotti, i controlli di disponibilità o le prenotazioni dei servizi, all&#39;interno di assistenti AI come [!DNL ChatGPT].

[!DNL LLM Apps] è disponibile in [experience.adobe.com](https://experience.adobe.com/#/@llmapps/llm-apps/).

## Operazioni possibili con [!DNL LLM Apps]

- **Creare azioni LLM di proprietà del brand** — Definire i flussi di business specifici che si desidera attivare negli assistenti AI (ad esempio, *Pianificare un&#39;unità di test*, *Confrontare i prodotti*, *Prenotare un servizio*).
- **Creare widget LLM interattivi**: creare componenti dell&#39;interfaccia utente visiva (schede prodotto, moduli di prenotazione, localizzatori di store) gestiti come componenti AEM nell&#39;archivio [!DNL GitHub].
- **Gestione centralizzata della governance del brand**: autori e sviluppatori mantengono il controllo completo su tutti i contenuti, le copie e gli elementi visivi esposti all&#39;interno della piattaforma LLM, con approvazioni gestite tramite AEM.
- **Distribuzione a staging e produzione**: una pipeline di distribuzione controllata consente di testare l&#39;esperienza in un ambiente di staging prima di passare alla produzione.
- **Controlla la visibilità a livello di azione**. Dopo la distribuzione, è possibile attivare o disattivare singole azioni senza ridistribuire l&#39;intera app.
- **Misura le decisioni che determinano**: conteggi dei trigger delle azioni di superficie incorporati in Analytics (basati su Adobe Customer Journey Analytics), tassi di successo, tassi di abbandono, prompt degli utenti principali e punteggi di visibilità.

## Perché [!DNL LLM Apps] conta

Le interazioni LLM sono fondamentalmente diverse dalla ricerca tradizionale. La durata media della sessione LLM è quattro volte superiore a quella di una sessione di ricerca tradizionale. Oltre il 40% dei consumatori si affida agli strumenti di intelligenza artificiale per decisioni di acquisto complesse. Senza [!DNL LLM Apps], potresti vincere la menzione ma perdere il cliente. [!DNL LLM Apps] assicura che il tuo marchio non sia solo visibile ma utilizzabile nel momento esatto in cui un utente è pronto a decidere.

## Concetti fondamentali {#key-concepts}

### App LLM

Il tuo assistente personalizzato con cui gli utenti interagiscono all&#39;interno di [!DNL ChatGPT] o altre piattaforme LLM. Raggruppa tutte le azioni e le distribuisce come una singola unità.

### Azione {#actions}

Una funzionalità offerta dalla tua app, ad esempio *Trova un distributore* o *Sfoglia prodotti*. La piattaforma LLM richiama un’azione quando una richiesta corrisponde alla relativa descrizione. I metadati dell&#39;azione sono gestiti in [!DNL LLM Apps], mentre il relativo gestore è il codice nel repository [!DNL GitHub].

### Gestore azioni

La funzione lato server che viene eseguita quando viene richiamata un’azione. Può convalidare l’input, chiamare le API e restituire testo e dati strutturati.

### Widget {#widgets-eds}

La risposta visiva mostrata con la risposta di LLM, ad esempio una scheda, un carosello o una tabella. I widget generati sono blocchi in un archivio [!DNL Edge Delivery Services] (EDS) di tua proprietà.

### Server MCP

Endpoint esposto dopo la distribuzione. Una piattaforma LLM supportata si connette a questo endpoint per individuare e richiamare le azioni.

## Come funziona

Il diagramma seguente mostra come si combinano i diversi elementi: dalla definizione di un’app nell’interfaccia utente alla visualizzazione dei risultati in tempo reale sulla piattaforma LLM.

```
┌─────────────────────────────────────────────────────────────┐
│                      LLM Apps UI                            │
│  ┌──────────┐   ┌──────────┐   ┌───────────────────────┐    │
│  │   App    │──▶│ Actions  │──▶│ Metadata + Widget cfg │    │
│  └──────────┘   └──────────┘   └───────────┬───────────┘    │
└─────────────────────────────────────────── │ ────────────-──┘
                                             │ deploy
                                             ▼
┌─────────────────────────────────────────────────────────────┐
│                  Adobe I/O Runtime                          │
│               MCP Server (auto-generated)                   │
│  ┌───────────────┐ ┌──────────────────┐ ┌───────────────┐   │
│  │ search-       │ │ get-product-     │ │ find-where-   │   │
│  │ products      │ │ details          │ │ to-buy        │   │
│  └───────────────┘ └──────────────────┘ └───────────────┘   │
└──────────────────────────────┬──────────────────────────────┘
                               │ MCP protocol
                               ▼
┌─────────────────────────────────────────────────────────────┐
│                        ChatGPT                              │
│  Conversation                                               │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  EDS Widget                                           │  │
│  │  Product carousel, store locator, detail card ...     │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

## Requisiti {#requirements}

Completa tutti i seguenti requisiti prima di creare un’app.

### Console per sviluppatori di Adobe

L&#39;organizzazione Adobe IMS deve avere accesso a [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/). È necessario il ruolo **Sviluppatore** o **Amministratore di sistema**.

Per verificare il tuo accesso, apri [Adobe Developer Console](https://developer.adobe.com/console). La schermata di avvio rapido conferma che si dispone dell&#39;accesso richiesto.

![Adobe Developer Console: schermata di avvio rapido che conferma l&#39;accesso per gli sviluppatori](/help/assets/overview/dev-console-access-granted.png)

Se trovi **Accesso limitato**, contatta l&#39;amministratore dell&#39;organizzazione IMS e richiedi il ruolo Sviluppatore.

![Adobe Developer Console - Messaggio ad accesso limitato](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

È necessario un account [!DNL GitHub] in grado di:

- Crea due archivi nell’account o nell’organizzazione di cui sarà proprietaria l’app.
- Installare o richiedere l&#39;installazione dell&#39;app [!DNL GitHub] delle app Adobe LLM.
- Installare o richiedere l&#39;installazione di AEM Code Sync per l&#39;archivio EDS.

Per verificare l&#39;accesso alla creazione dell&#39;archivio, aprire [github.com/new](https://github.com/new) e verificare che l&#39;account o l&#39;organizzazione previsti siano visualizzati in **Proprietario**.

![GitHub — seleziona un proprietario del repository](/help/assets/overview/github-repo-owner-dropdown.png)

Per gli archivi di proprietà dell&#39;organizzazione, l&#39;amministratore potrebbe dover approvare le app [!DNL GitHub]. Concedi a ogni app l’accesso solo agli archivi utilizzati dall’app LLM.

### AEM Sites con Edge Delivery Services

La tua organizzazione ha bisogno di una licenza Adobe Experience Manager Sites che includa Edge Delivery Services (EDS). È inoltre necessario accedere come amministratore al sito EDS creato dall&#39;archivio widget.

Per verificare l&#39;accesso, aprire lo strumento di amministrazione utenti di [EDS](https://tools.aem.live/tools/user-admin/index.html), immettere il nome dell&#39;organizzazione e recuperare gli utenti. Verifica che il tuo account disponga del badge **admin**.

### Sito Web

È necessario un sito web HTTPS pubblico che rappresenti i prodotti, i servizi o le attività supportati dall’app. La piattaforma analizza questo sito web per proporre azioni e creare dati di esempio rappresentativi.

Non utilizzare siti web che espongono informazioni confidenziali o soggette al controllo degli accessi.

### [!DNL ChatGPT] o [!DNL Claude] per il test

Per completare l&#39;esercitazione introduttiva, utilizzare un piano [!DNL ChatGPT] supportato con la modalità sviluppatore abilitata oppure un piano [!DNL Claude] supportato con i connettori personalizzati abilitati. Gli amministratori di Workspace o dell’organizzazione possono limitare l’accesso. Vedi [Test in ChatGPT](/help/guides/test-in-chatgpt.md#plan-requirements) o [Test in Claude](/help/guides/test-in-claude.md#plan-requirements).

## Scegli il tuo percorso {#choose-your-journey}

### &#x200B;1. Creare e avviare la prima app

Inizia con [Crea e avvia la tua prima app](/help/guides/create-app.md). Questo percorso inizia con due archivi vuoti e termina con un&#39;app pronta per la produzione testata come plug-in in una piattaforma LLM supportata, ad esempio [!DNL ChatGPT].

### &#x200B;2. Personalizzare l’app generata

Scegli questo percorso quando la piattaforma ha creato l’app automaticamente e vuoi sostituire il comportamento di esempio:

1. [Personalizza i gestori generati](/help/guides/customize-handler.md) per connettere le API e definire i dati restituiti da ogni azione.
2. [Personalizza i widget generati](/help/guides/widgets.md) per utilizzare tali dati e applicare le interazioni e la progettazione.

### &#x200B;3. Aggiungi una nuova azione da zero

Scegli [Aggiungi una nuova azione da zero](/help/guides/create-action.md) per definire nuovi metadati, scrivere il gestore, connettere un widget, testare e distribuire l&#39;azione.

### &#x200B;4. Connettere un progetto EDS esistente

Scegliere [Connetti un progetto EDS esistente](/help/guides/bring-your-own-eds.md) se si dispone già di un sito EDS o se l&#39;app non è stata generata automaticamente.

Ogni percorso utilizza il passaggio [distribuzione](/help/guides/deploy-your-app.md) condivisa, quindi [test del plug-in ChatGPT](/help/guides/test-in-chatgpt.md) o [test del connettore Claude](/help/guides/test-in-claude.md).

