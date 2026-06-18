---
title: Onboarding di Beta per le app Adobe LLM
description: Guida introduttiva alle app Adobe LLM come partecipante al programma Beta.
source-git-commit: 98d5590c927bf8ffad54061ee027664452c129c1
workflow-type: tm+mt
source-wordcount: '1551'
ht-degree: 0%

---


# Onboarding di Beta {#beta-onboarding}

>[!IMPORTANT]
>
>**Dichiarazione di non responsabilità:** Versione beta di [!DNL LLM Apps]. Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale dell’applicazione o del prodotto.

>[!NOTE]
>
>Prima di iniziare, assicurati che siano soddisfatti tutti i [prerequisiti](/help/beta-onboarding/prerequisites.md).

In qualità di partecipante al programma Beta, riceverai un’e-mail con due archivi zip e un riferimento per la configurazione dell’app. Per scaricare la tua app live, segui i passaggi seguenti.

## Prima di iniziare

Prima di immergerti nei passaggi, impara a conoscere i concetti chiave utilizzati in questa guida. Ti farà risparmiare tempo e aiuterà tutto a scattare in posizione.

**App LLM**: il tuo assistente di branding con cui gli utenti interagiscono all&#39;interno di [!DNL ChatGPT] o altre piattaforme LLM.

**Azione**: funzionalità offerta dall&#39;app. Ad esempio, &quot;Trova un distributore&quot; o &quot;Sfoglia i prodotti&quot;. Ogni azione viene richiamata da LLM quando l’utente fa una domanda rilevante.

**Gestore azioni**: il codice che viene eseguito quando viene richiamata un&#39;azione. Può richiamare le API, recuperare dati live o restituire dati statici. I gestori di esempio forniti da Adobe restituiscono dati hardcoded in modo da poter verificare la configurazione end-to-end prima di collegare il backend reale.

**Widget** — la risposta visiva mostrata all&#39;utente — una scheda, un carosello, una tabella o un&#39;interfaccia utente personalizzata sottoposta a rendering insieme alla risposta testuale del modulo di gestione dell&#39;accesso remoto.

**Riferimento alla configurazione dell&#39;app**: un file fornito da Adobe indica esattamente cosa immettere per ogni azione durante la configurazione dell&#39;app.


## Passaggio 1: invia gli archivi forniti a [!DNL GitHub]

Adobe fornisce due archivi zip tramite e-mail:

- **Codice applicazione** (`<project-name>.zip`): i gestori di azioni che vengono eseguiti su [!DNL Adobe I/O Runtime] e alimentano la logica dell&#39;app. Distribuirai questi elementi così come sono per far funzionare l’app in modo end-to-end, quindi aggiornali in un secondo momento per collegare il tuo back-end reale.
- **Progetto EDS** (`<project-name>-eds.zip`): codice front-end per i widget. Questi sono già stati creati da Adobe; si tratta della tua base di codice per possedere, personalizzare e usare lo stile che meglio si adatta al tuo marchio.

Crea **due nuovi archivi vuoti** in [!DNL GitHub] (uno per archivio), quindi decomprimi ogni archivio e invialo. È consigliabile denominare ogni repository dopo il file zip corrispondente: `<project-name>` per il codice dell&#39;applicazione e `<project-name>-eds` per il progetto EDS.

`<your-github-org>` fa riferimento al tuo nome utente [!DNL GitHub] personale o a un&#39;organizzazione [!DNL GitHub], a seconda dell&#39;account proprietario degli archivi.

**Archivio del codice dell&#39;applicazione**: decomprimi l&#39;archivio, inizializza un archivio Git locale e invialo a [!DNL GitHub]:

```bash
# Unzip and enter the folder
unzip <project-name>.zip
cd <project-name>

# Initialize and push
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-repo>.git
git push -u origin main
```

**Archivio EDS** — ripetere gli stessi passaggi per l&#39;archivio EDS, puntando al secondo archivio:

```bash
unzip <project-name>-eds.zip
cd <project-name>-eds

git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<your-github-org>/<your-eds-repo>.git
git push -u origin main
```

## Passaggio 2: creare un&#39;app LLM

Passa a [experience.adobe.com/llm-apps/](https://experience.adobe.com/llm-apps/) e fai clic su **[!UICONTROL Crea app LLM]**.

![Pagina app — non è ancora stata creata alcuna app](/help/assets/guide-create-app/first-load.png)

Compila i **[!UICONTROL dettagli app]** utilizzando i valori della sezione **[!UICONTROL Dettagli app]** nel riferimento alla configurazione dell&#39;app:

- **[!UICONTROL Nome app LLM]**
- **[!UICONTROL Descrizione app LLM]**
- **[!UICONTROL Sito Web]**

![Finestra di dialogo Crea app](/help/assets/guide-create-app/app-details-1.png)

In **[!UICONTROL Area dati di Analytics]**, selezionare l&#39;area in cui verranno archiviati i dati di Analytics. Impossibile modificare **&#x200B;**&#x200B;dopo la creazione dell&#39;app.

>[!IMPORTANT]
>
>Una volta creata l’app, non è possibile modificare l’area dati di analisi.

![Elenco a discesa area dati di Analytics](/help/assets/guide-create-app/app-details-analytics-dropdown.png)

In **Archivio**, selezionare l&#39;organizzazione [!DNL GitHub] e il **archivio del codice dell&#39;applicazione** precedentemente inviati.

>[!NOTE]
>
>Se questa è la prima volta che configuri un&#39;app, l&#39;organizzazione [!DNL GitHub] non verrà ancora visualizzata nell&#39;elenco. Fai clic su **[!UICONTROL Connetti un&#39;altra organizzazione GitHub]** per collegare la tua organizzazione e concedere l&#39;accesso all&#39;archivio.

![Finestra di dialogo Crea app - archivio collegato](/help/assets/guide-create-app/app-details-repo-linked.png)

Lascia **[!UICONTROL Deseleziona automaticamente le azioni basate sul tuo sito Web]**. Le azioni verranno configurate manualmente.

Accetta i **[!UICONTROL Termini di Adobe Developer]**, quindi fai clic su **[!UICONTROL Crea app]**.

![Creazione dell&#39;app — caricamento della schermata](/help/assets/guide-create-app/app-loading.png)

![Pagina dettagli app](/help/assets/guide-create-app/app-detail-top.png)


## Passaggio 3: attivazione dei widget

In questo passaggio verrà configurato il progetto EDS fornito da Adobe e verrà pubblicato tramite [!DNL DA.live], il livello di authoring e CDN di Adobe. Ogni documento pubblicato diventa il widget mostrato all&#39;utente quando viene richiamata un&#39;azione.

### Passaggio 3.1: connettere l&#39;archivio EDS a [!DNL DA.live]

1. Vai a [github.com/apps/aem-code-sync](https://github.com/apps/aem-code-sync). Se l&#39;app non è ancora stata installata, fare clic su **[!UICONTROL Installa]**. Se è già installato, fare clic su **[!UICONTROL Configura]** e aggiungere `<your-eds-repo>` all&#39;elenco degli archivi a cui può accedere.
2. Dopo l&#39;installazione, verrà visualizzata una pagina di conferma **[!DNL AEM Code Sync]registrata**. In **Operazioni successive → Creare il contenuto**, fare clic sul collegamento [!DNL DA.live].
3. Nella schermata **Contenuto demo**, seleziona **Nessuno** e fai clic su **Rendi qualcosa di meraviglioso**.
4. Sei portato alla visualizzazione Autore [!DNL DA.live] per il tuo sito.

### Passaggio 3.2: creare un documento [!DNL DA.live] per ogni azione

In [!DNL DA.live] è necessario creare **un documento per azione**. Dopo la pubblicazione, ogni documento diventa il widget mostrato all’utente quando tale azione viene richiamata.

Per ogni azione:

1. In [!DNL DA.live], creare un nuovo documento nella directory principale del sito e denominarlo come specificato nel riferimento alla configurazione dell&#39;app (vedere la sezione **[!DNL DA.live]documenti**).
2. Nel documento, utilizza la barra laterale a sinistra e fai clic su **[!UICONTROL Blocca]** per inserire un nuovo blocco.
3. Impostare l&#39;intestazione del blocco sul nome del blocco specificato nel riferimento alla configurazione dell&#39;app (vedere la sezione **[!DNL DA.live]documenti**).
4. Pubblica il documento utilizzando il pulsante **[!UICONTROL Pubblica]** (l&#39;icona del piano carta nella barra degli strumenti superiore).

Dopo la pubblicazione, ogni documento è accessibile da `https://main--<your-eds-repo>--<your-github-org>.aem.live/<document-name>`. Questo URL è quello che immetterai nel campo **[!UICONTROL URL widget]** durante la configurazione di ogni azione nel passaggio 4.


### Passaggio 3.3: configurare le intestazioni CORS per il sito EDS

Per consentire alle piattaforme LLM di caricare i widget tra origini diverse, è necessario aggiungere un&#39;intestazione `Access-Control-Allow-Origin` al sito EDS.

Vai a **Editor intestazioni HTTP** in [tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html).

1. Immetti la **organizzazione** (`<your-github-org>`) e il **sito** (nome dell&#39;archivio EDS) e fai clic su **[!UICONTROL Recupera]**. Ti verrà chiesto di autenticare e autorizzare l’accesso al sito.
2. Nel percorso `/**`, fare clic su **[!UICONTROL Aggiungi intestazione]**.
3. Impostare il nome dell&#39;intestazione su `Access-Control-Allow-Origin` e il valore su `*`.
4. Fai clic su **[!UICONTROL Salva]**.

Per la documentazione completa sulle intestazioni HTTP personalizzate in [!DNL AEM Edge Delivery Services], consulta [aem.live/docs/custom-headers](https://www.aem.live/docs/custom-headers).

Dopo aver salvato le intestazioni, attiva una sincronizzazione del codice per propagare le modifiche a tutti i file:

```bash
curl -X POST "https://admin.hlx.page/code/<your-github-org>/<your-eds-repo>/main/*"
```


## Passaggio 4: Aggiungere azioni

Nell&#39;interfaccia utente delle [app LLM](https://experience.adobe.com/llm-apps/), apri l&#39;app e passa a **[!UICONTROL Azioni]** nella barra laterale a sinistra. Fare clic su **+** per creare una nuova azione. Ripeti per ogni azione descritta nel riferimento alla configurazione dell&#39;app (vedi **Azione 1**, **Azione 2**, **Azione 3** sezioni).

![Pagina Azioni — ancora nessuna azione](/help/assets/guide-create-action/actions-empty.png)

### Scheda Azione

- **Nome azione** e **Descrizione**, utilizzati dalle piattaforme LLM per decidere quando richiamare l&#39;azione. Utilizza i valori esatti dalla sezione **scheda Azione** nel riferimento alla configurazione dell&#39;app.
- **Parametri di input**: nome, tipo e descrizione per ogni parametro. Usa i valori della sezione **Scheda Azione** nel riferimento alla configurazione dell&#39;app.

![Crea azione — informazioni di base](/help/assets/guide-create-action/action-basic-info.png)

### Scheda Metadati widget

- **Tipo** — selezionare **[!UICONTROL EDS]**.

Espandi **[!UICONTROL Configurazione CSP]** e compila:

- **[!UICONTROL CSP — Connect domains]** — utilizza i valori della **scheda Widget Metadata** sezione nel riferimento alla configurazione dell&#39;app.
- **[!UICONTROL CSP — Domini di risorse]** — utilizza i valori della **scheda Metadati widget** sezione nel riferimento alla configurazione dell&#39;app.

![Crea azione — metadati widget](/help/assets/guide-create-action/widget-metadata.png)

![Crea azione — autorizzazioni e CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

### Scheda Generatore di widget

In **[!UICONTROL Origine widget]**, seleziona **[!UICONTROL Usa un widget esistente]**, quindi compila:

- **[!UICONTROL URL script]**: utilizza il valore della sezione **Scheda metadati widget** nel riferimento alla configurazione dell&#39;app.
- **[!UICONTROL URL widget]**: utilizza il valore della **scheda Metadati widget** nel riferimento alla configurazione dell&#39;app.

Fai clic su **[!UICONTROL Crea azione]**. L&#39;azione viene visualizzata come una scheda nella pagina Azioni con un badge **[!UICONTROL EDS]** e un conteggio di parametri.

![Pagina Azioni — azione creata](/help/assets/guide-create-action/actions-with-action.png)


## Passaggio 5: distribuire

Una volta configurate tutte le azioni, vai alla pagina Dettagli app e fai clic su **[!UICONTROL Distribuisci]** nell&#39;angolo in alto a destra.

![Dettagli app - Pronto per la distribuzione](/help/assets/guide-deploy/app-detail-deploy-ready.png)

Selezionare l&#39;ambiente di destinazione e fare clic su **[!UICONTROL Distribuisci]**. La pipeline prevede quattro passaggi: preparazione delle credenziali, avvio della distribuzione, creazione dell&#39;app dall&#39;archivio e pubblicazione in [!DNL Adobe I/O Runtime].

![Distribuisci pipeline in esecuzione](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

Al termine, scorri fino alla sezione **[!UICONTROL Verifica l&#39;app]** nella pagina Dettagli app e copia il **[!UICONTROL URL del server MCP]**. Sarà necessario per registrare l&#39;app in [!DNL ChatGPT].

![Distribuzione completata](/help/assets/guide-deploy/app-detail-deploy-finish.png)

![Verifica dell&#39;app - URL distribuiti](/help/assets/guide-deploy/test-app-deployed.png)


## Passaggio 6: aggiungi l&#39;app a [!DNL ChatGPT]

L&#39;aggiunta di app personalizzate a [!DNL ChatGPT] richiede una sottoscrizione a **Pro**, **Business** o **Enterprise**. I piani Free e Plus non supportano le app MCP personalizzate.

1. In [!DNL ChatGPT], fai clic sull&#39;avatar del tuo profilo e passa a **[!UICONTROL Impostazioni]**.

   ![ChatGPT — Menu Impostazioni](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

2. Seleziona **[!UICONTROL App]** nella barra laterale, fai clic su **[!UICONTROL Impostazioni avanzate]** e abilita **[!UICONTROL Modalità sviluppatore]**.

   ![ChatGPT - Modalità sviluppatore abilitata](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

3. Vai a **[!UICONTROL Impostazioni] → [!UICONTROL App]** e fai clic su **[!UICONTROL Crea app]**.

   ![ChatGPT — Finestra di dialogo Crea app](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

4. Incolla l&#39;**[!UICONTROL URL server MCP]** copiato da [!DNL LLM Apps], imposta **[!UICONTROL Autenticazione]** su *Nessuna autenticazione*, seleziona la casella di controllo di conferma e fai clic su **Crea**.

L&#39;app viene visualizzata in **[!UICONTROL App abilitate]** con un distintivo **[!UICONTROL DEV]**.

![ChatGPT — app abilitata](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

Avvia una nuova conversazione, allega l&#39;app utilizzando il pulsante **+** o digitando **@** seguito dal nome dell&#39;app e poni una domanda che corrisponda a una delle azioni configurate.

![ChatGPT — seleziona l&#39;app dal menu](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

![ChatGPT — risultato azione](/help/assets/guide-test-chatgpt/chatgpt-response.png)

## Passaggio successivo

L’app di esempio distribuita utilizza dati hardcoded. Per trasformarlo in un’esperienza pronta per la produzione:

- **Connetti le API**: aggiorna i gestori delle azioni nell&#39;archivio del codice dell&#39;applicazione per chiamare le API, i database o i servizi reali. Ogni gestore risiede in `actions/<action-name>/index.js`.
- **Rivedi e perfeziona i widget**: apri il progetto EDS, regola gli stili e il layout dei blocchi in base al tuo marchio e verifica che il rendering del widget sia corretto con i dati live.
- **Ridistribuisci**: dopo aver aggiornato i gestori e i widget, invia le modifiche a [!DNL GitHub] e fai clic su **[!UICONTROL Distribuisci]** nell&#39;interfaccia utente di [!DNL LLM Apps] per pubblicare la nuova versione.
- **Invia per la pubblicazione** - Se l&#39;esperienza è soddisfacente, invia l&#39;app per la revisione tramite il plug-in [!DNL ChatGPT] o il processo di pubblicazione del connettore. Adobe non controlla questo processo. Per i requisiti e le tempistiche di invio, fare riferimento alla documentazione della piattaforma LLM.
