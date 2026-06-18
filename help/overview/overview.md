---
title: Panoramica delle app Adobe LLM
description: Scopri cos’è un’app Adobe LLM, come funziona e cosa serve per iniziare.
source-git-commit: 98d5590c927bf8ffad54061ee027664452c129c1
workflow-type: tm+mt
source-wordcount: '873'
ht-degree: 1%

---


# App Adobe LLM: panoramica {#adobe-llm-apps-an-overview}

>[!NOTE]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta. Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

## Cos&#39;è [!DNL Adobe LLM Apps]?

[!DNL Adobe LLM Apps] consente al tuo marchio di esporre azioni chiave, come l&#39;individuazione dei prodotti, i controlli di disponibilità o le prenotazioni di servizi, direttamente all&#39;interno di assistenti AI come [!DNL ChatGPT] o Claude. Invece di essere menzionati passivamente nelle risposte generate dall’intelligenza artificiale, il tuo marchio può guidare i clienti attraverso flussi di business reali senza che questi abbandonino mai la conversazione.

[!DNL LLM Apps] è disponibile in [experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps).

## Operazioni possibili con [!DNL LLM Apps]

- **Creare azioni LLM di proprietà del brand** — Definire i flussi di business specifici che si desidera attivare negli assistenti AI (ad esempio, *Pianificare un&#39;unità di test*, *Confrontare i prodotti*, *Prenotare un servizio*).
- **Creare widget LLM interattivi**: creare componenti dell&#39;interfaccia utente visiva (schede prodotto, moduli di prenotazione, localizzatori di store) gestiti come componenti AEM nell&#39;archivio [!DNL GitHub].
- **Gestione centralizzata della governance del brand**: autori e sviluppatori mantengono il controllo completo su tutti i contenuti, le copie e gli elementi visivi esposti all&#39;interno della piattaforma LLM, con approvazioni gestite tramite AEM.
- **Distribuzione a staging e produzione**: una pipeline di distribuzione controllata consente di testare l&#39;esperienza in un ambiente di staging prima di passare alla produzione.
- **Controlla la visibilità a livello di azione**. Dopo la distribuzione, è possibile attivare o disattivare singole azioni senza ridistribuire l&#39;intera app.
- **Misura le decisioni che determinano**: conteggi dei trigger delle azioni di superficie incorporati in Analytics (basati su Adobe Customer Journey Analytics), tassi di successo, tassi di abbandono, prompt degli utenti principali e punteggi di visibilità.

## Perché [!DNL LLM Apps] conta

Le interazioni LLM sono fondamentalmente diverse dalla ricerca tradizionale. La durata media della sessione di [!DNL ChatGPT] è quattro volte superiore a quella di una sessione di ricerca tradizionale. Oltre il 40% dei consumatori si affida agli strumenti di intelligenza artificiale per decisioni di acquisto complesse. Senza [!DNL LLM Apps], potresti vincere la menzione ma perdere il cliente. [!DNL LLM Apps] assicura che il tuo marchio non sia solo visibile ma utilizzabile nel momento esatto in cui un utente è pronto a decidere.

## Concetti fondamentali

**App LLM**: il tuo assistente di branding con cui gli utenti interagiscono all&#39;interno di [!DNL ChatGPT] o altre piattaforme LLM. Raggruppa tutte le azioni e le distribuisce come una singola unità.

**Azione**: funzionalità offerta dall&#39;app. Ad esempio, &quot;Trova un distributore&quot; o &quot;Sfoglia i prodotti&quot;. Ogni azione viene richiamata da LLM quando l’utente fa una domanda rilevante. Ogni azione è composta da due parti: i metadati (nome, descrizione, parametri) gestiti nell&#39;interfaccia utente [!DNL LLM Apps] e un gestore (il codice) in [!DNL GitHub].

**Gestore azioni**: il codice che viene eseguito quando viene richiamata un&#39;azione. Può richiamare le API, recuperare dati live o restituire dati statici. I gestori risiedono nell&#39;archivio [!DNL GitHub] in `actions/<name>/index.js`.

**Widget** — la risposta visiva mostrata all&#39;utente — una scheda, un carosello, una tabella o un&#39;interfaccia utente personalizzata sottoposta a rendering insieme alla risposta testuale del modulo di gestione dell&#39;accesso remoto. I widget sono pagine HTML ospitate su un sito [!DNL Edge Delivery Services] (EDS).

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

## Prerequisiti

### Console per sviluppatori di Adobe

Devi accedere a [Adobe Developer Console](https://developer.adobe.com/console) con il ruolo **Sviluppatore** (o **Amministratore di sistema**) nell&#39;organizzazione Adobe IMS. Assicurati che la tua organizzazione abbia accesso a [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/).

Per verificare, vai a [developer.adobe.com/console](https://developer.adobe.com/console). Se viene visualizzata la schermata di avvio rapido, le autorizzazioni sono impostate correttamente.

![Adobe Developer Console: schermata di avvio rapido che conferma l&#39;accesso per gli sviluppatori](/help/assets/overview/dev-console-access-granted.png)

Se invece viene visualizzato il messaggio **Accesso limitato**, non si dispone del ruolo Sviluppatore. Contatta l’amministratore dell’organizzazione IMS per richiedere l’accesso.

![Adobe Developer Console - Messaggio ad accesso limitato](/help/assets/overview/dev-console-access-denied.png)

### [!DNL GitHub]

È necessario un account [!DNL GitHub] con le seguenti autorizzazioni nell&#39;organizzazione:

- **Crea archivi**: è necessario creare due archivi nell&#39;organizzazione: uno per il codice dell&#39;applicazione e uno per il progetto EDS. Per verificare, vai a [github.com/new](https://github.com/new). Se puoi selezionare la tua organizzazione dal menu a discesa **Proprietario**, disponi dell&#39;autorizzazione.

  ![Menu a discesa del proprietario del nuovo archivio GitHub con la selezione dell&#39;organizzazione](/help/assets/overview/github-repo-owner-dropdown.png)

- **Installa [!DNL GitHub] app**. Sono necessarie le autorizzazioni appropriate per installare un&#39;app [!DNL GitHub] nell&#39;organizzazione. Vedi [Requisiti per installare un&#39;app GitHub](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app).

### AEM Sites con [!DNL Edge Delivery Services]

I widget di azione sono ospitati in **Adobe Experience Manager [!DNL Edge Delivery Services] (EDS)**. La tua organizzazione necessita di una licenza AEM Sites che includa [!DNL Edge Delivery Services]. Devi avere il ruolo **Amministratore** nell&#39;organizzazione EDS.

Per verificare il funzionamento, vai allo strumento di amministrazione utenti di [EDS](https://tools.aem.live/tools/user-admin/index.html), immetti il nome della tua organizzazione, lascia vuoto **Sito** e fai clic su **Recupera utenti**. Trova il tuo account nell&#39;elenco e conferma che mostri il badge **admin**.

![Strumento di amministrazione utenti EDS che mostra un utente con il ruolo di amministratore](/help/assets/overview/eds-user-admin.png)

### Piattaforma LLM (per test)

Per testare l&#39;app implementata, è necessario un livello di abbonamento supportato che consenta l&#39;attivazione di app MCP personalizzate e della **modalità sviluppatore**. Ad esempio, [!DNL ChatGPT] richiede un abbonamento **Pro**, **Business** o **Enterprise / Edu**.

## Introduzione

Scegli il percorso che corrisponde alla tua situazione:

| | **Partecipante Beta** | **Disponibilità generale** |
|---|---|---|
| **Hai** | Hai partecipato al programma Beta e hai ricevuto un archivio di codice dell’applicazione, un archivio di progetto EDS e un riferimento alla configurazione di app da Adobe | Un caso d’uso in mente: Adobe ti guida attraverso la creazione e la distribuzione dell’app |
| **Inizia qui** | [Onboarding di Beta](/help/beta-onboarding/beta-onboarding.md) | [Crea un&#39;app](/help/guides/create-app.md) |

