---
title: Creare un’app
description: Scopri come creare la prima app LLM e collegarla all’archivio GitHub.
source-git-commit: 914b8a659e690ff47257c2c112f76816f4b0232c
workflow-type: tm+mt
source-wordcount: '735'
ht-degree: 0%

---


# Creare un’app

>[!NOTE]
>
>Se sei un **partecipante al programma Beta**, utilizza invece la [guida all&#39;onboarding di Beta](/help/beta-onboarding/beta-onboarding.md), che copre l&#39;intera configurazione end-to-end dell&#39;app specifica.

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta. Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto.

>[!NOTE]
>
>Prima di iniziare, assicurati che siano soddisfatti tutti i [prerequisiti](/help/overview/overview.md#prerequisites).

Questa guida illustra come creare la prima app LLM, dallo stato vuoto a un progetto completamente configurato collegato all&#39;archivio [!DNL GitHub].

## Apri [!DNL LLM Apps]

Passa a [experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps). Se non è ancora stata creata alcuna app, viene visualizzata la pagina di primo caricamento con un messaggio che richiede di creare la prima app.

![Pagina app — non è ancora stata creata alcuna app](/help/assets/guide-create-app/first-load.png)

La barra laterale a sinistra consente di spostarsi tra **[!UICONTROL App]** e **[!UICONTROL Azioni]**. Fare clic su **[!UICONTROL Crea app]** per avviare.

## Inserisci i dettagli dell’app

Viene visualizzata la finestra di dialogo Crea app a schermo intero.

![Finestra di dialogo Crea app](/help/assets/guide-create-app/app-details-1.png)

Immetti le seguenti informazioni:

- **[!UICONTROL Nome app LLM]** (obbligatorio): il nome visualizzato dell&#39;app. Sono consentiti solo lettere, numeri e spazi.
- **[!UICONTROL Descrizione dell&#39;app LLM]**: breve descrizione delle funzioni dell&#39;app. *Consente ad esempio agli utenti di individuare prodotti e prenotare servizi tramite una piattaforma LLM*.
- **[!UICONTROL Sito Web]** (obbligatorio): l&#39;URL del sito Web del marchio. [!DNL LLM Apps] utilizza questa funzione per creare automaticamente azioni preconfigurate.

## Seleziona un’area dati di analisi

Scegli l&#39;area in cui verranno memorizzati i dati di analisi per questa app.

>[!IMPORTANT]
>
>Una volta creata l’app, non è possibile modificare l’area dati di analisi.

![Elenco a discesa area dati di Analytics](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

Il menu a discesa dell&#39;area **Analytics** è impostato su **Stati Uniti**. Le opzioni disponibili sono **Stati Uniti** e **Europa**. Prima di procedere, seleziona l’area che soddisfa al meglio i requisiti di residenza dei dati.

## Collega un archivio [!DNL GitHub]

Sotto i dettagli dell&#39;app, puoi collegare un archivio [!DNL GitHub]. In questo archivio è memorizzato il codice del gestore delle azioni: JavaScript funziona in una cartella `actions/` che viene eseguita su [!DNL Adobe I/O Runtime] quando la piattaforma LLM richiama l&#39;app.

Se si tratta della prima volta, nell’elenco non viene visualizzato alcun archivio. È necessario installare l&#39;app **[!DNL Adobe LLM Apps Link]** [!DNL GitHub] nell&#39;organizzazione:

1. Fai clic su **Gestisci archivio su Github** nella parte inferiore della finestra di dialogo.
2. Verrà aperta la pagina dell&#39;app [!DNL Adobe LLM Apps Link] [!DNL GitHub] in una nuova scheda.

   ![Collegamento app Adobe LLM - Pagina di installazione app GitHub](/help/assets/guide-create-app/github-app-install.png)

3. Fai clic su **[!UICONTROL Installa]** e seleziona la tua organizzazione [!DNL GitHub].
4. In **[!UICONTROL Accesso all&#39;archivio]**, scegli **Seleziona solo archivi** e seleziona l&#39;archivio che ospiterà il codice dell&#39;app.

   ![Collegamento alle app Adobe LLM — accesso all&#39;archivio](/help/assets/guide-create-app/github-repo-access.png)

5. Fai clic su **[!UICONTROL Salva]**. Torna alla finestra di dialogo Crea app: l&#39;archivio viene ora visualizzato nel menu a discesa **Seleziona archivio**.
6. Selezionare il repository che si desidera utilizzare.

![Finestra di dialogo Crea app - archivio collegato](/help/assets/guide-create-app/app-details-repo-linked.png)

>[!NOTE]
>
>Puoi saltare il collegamento di un archivio durante la creazione dell’app e farlo in un secondo momento dalle impostazioni dell’app. Tuttavia, non puoi distribuire finché non viene collegato un archivio.

## Creare l’app

Fare clic su **[!UICONTROL Crea app]**. Durante la creazione del progetto in Developer Console viene visualizzata una schermata di caricamento.

![Creazione dell&#39;app — caricamento della schermata](/help/assets/guide-create-app/app-loading.png)

Al termine, verrai reindirizzato alla pagina **Dettagli app**.

## Pagina Dettagli app

La pagina Dettagli app è l’hub centrale per la gestione dell’app.

![Pagina dettagli app - sezioni principali](/help/assets/guide-create-app/app-detail-top.png)

### Banner app

![Banner app](/help/assets/guide-create-app/app-banner.png)

Il banner colorato nella parte superiore mostra l’app attualmente selezionata, inclusi l’avatar, il nome, la descrizione e un menu a discesa dell’app per passare da un’app all’altra. Il banner rimane fisso nella parte superiore durante lo scorrimento.

### Titolo pagina e azioni

![Banner app](/help/assets/guide-create-app/page-title.png)

Sotto il banner viene visualizzato il nome dell’app come intestazione, con i seguenti pulsanti di azione:

- **...** (ulteriori azioni) — crea una nuova app o elimina quella corrente.
- **[!UICONTROL Impostazioni]** — configura l&#39;archivio collegato e altre opzioni.
- **[!UICONTROL Distribuisci]** — distribuisci l&#39;app in [!DNL Adobe I/O Runtime] (disattivato fino al collegamento di un repository).

### Scheda di informazioni app

![Scheda di informazioni app](/help/assets/guide-create-app/app-info-card.png)

Questa scheda riepiloga i metadati chiave dell&#39;app: nome, descrizione, badge di stato (**Non distribuito** o **Distribuito**), ID app e data di creazione. Mostra anche i due archivi collegati:

- **Archivio gestore**, dove risiede il codice del gestore azioni (JavaScript funziona su [!DNL Adobe I/O Runtime]).
- **Archivio EDS**, dove si trova l&#39;interfaccia utente del widget (blocchi e stili gestiti da [!DNL Edge Delivery Services]).

### Azioni, verifica dell’app e cronologia della distribuzione

![Pagina dettagli app - sezioni inferiori](/help/assets/guide-create-app/app-detail-bottom.png)

Sotto la scheda informazioni trovi tre sezioni:

- **[!UICONTROL Azioni]** — elenca i gestori azioni definiti per l&#39;app. Fare clic su **Vai a Azioni** per passare alla pagina Azioni.
- **[!UICONTROL Test dell&#39;app]**: dopo la distribuzione, visualizza gli URL del server MCP per gli ambienti di staging e produzione.
- **Cronologia distribuzione**: tiene traccia di ogni distribuzione tra ambienti con stato e data.

## Passaggi successivi

- [Guida: creare un&#39;azione](/help/guides/create-action.md) — definire un&#39;azione con le impostazioni dei metadati e dei widget.

