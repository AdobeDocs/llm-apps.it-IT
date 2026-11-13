---
title: Configurazione dell’infrastruttura
description: Crea un’app Adobe LLM utilizzando l’infrastruttura gestita delle app LLM o i tuoi archivi aggiunti a Cloud Manager.
source-git-commit: 9b2ec369baba5d533088d2dd4adefcb80f5c9727
workflow-type: tm+mt
source-wordcount: '1947'
ht-degree: 1%
---

# Configurazione dell’infrastruttura {#create-first-app}

>[!IMPORTANT]
>
>Le app LLM sono disponibili per tutti i clienti di AEM Sites Cloud Service che desiderano sviluppare e gestire le proprie app. Una volta avviato il conteggio, ogni chiamata all’app verrà conteggiata nelle richieste di contenuto AEM Sites del cliente. Adobe annuncerà il tasso di misurazione e la data di inizio prima che il conteggio abbia effetto.

L’infrastruttura è la base tecnica dell’app: gli archivi che memorizzano il codice e i servizi che eseguono i gestori delle azioni e distribuiscono i widget agli utenti.

Come spiegato nella [panoramica](/help/overview/overview.md#key-concepts), un gestore **azioni** esegue un&#39;attività, ad esempio la ricerca di prodotti, mentre un **widget** presenta il risultato come un&#39;esperienza interattiva nella conversazione.

[!DNL LLM Apps] archivia il gestore e il codice sorgente del widget in due archivi separati:

- **Archivio azioni**: contiene i gestori lato server e i relativi test.
- **Archivio widget**: contiene il codice e gli stili dei widget interattivi.

Ad esempio, un gestore di ricerca di prodotti recupera i prodotti corrispondenti e il suo widget li visualizza come schede di prodotto. Gli archivi memorizzano tale codice; Adobe I/O Runtime esegue i gestori distribuiti e Edge Delivery Services fornisce i widget.

La procedura guidata di configurazione prepara questi archivi e collega i servizi necessari per l’app. Scegli una delle due opzioni seguenti:

- L&#39;infrastruttura gestita **[!DNL LLM Apps]** è il modo più veloce per iniziare. Adobe crea e ospita gli archivi per i gestori delle azioni e i widget, pertanto non devi impostare gli archivi personalmente.
- **I tuoi archivi** mantengono il codice dell&#39;app negli archivi che possiedi, aggiunti a [!DNL Cloud Manager]. Possono essere ospitati su [!DNL GitHub], [!DNL GitHub Enterprise], [!DNL GitLab], [!DNL Bitbucket] o [!DNL Azure DevOps]. La piattaforma inizializza archivi vuoti con i file di progetto richiesti, quindi non è necessario preparare in anticipo il codice dell’azione o del widget.

**In questa guida:** scopri come scegliere un&#39;opzione di infrastruttura e creare la tua app con la procedura guidata di configurazione. Dopo l&#39;installazione, continuare con [Crea azioni](/help/guides/creating-actions.md).

La creazione dell&#39;app **non** avvia la generazione delle azioni. Avvia la generazione dell&#39;azione separatamente dalla pagina dell&#39;app.

## Apri la procedura guidata di creazione app

Apri [App Adobe LLM](https://experience.adobe.com/llm-apps). Dalla pagina di benvenuto, seleziona **[!UICONTROL Inizia]**. Se hai già un&#39;app aperta, usa il selettore delle app e seleziona **[!UICONTROL Crea nuova app]**.

La procedura guidata **[!UICONTROL Crea app LLM]** inizia con **[!UICONTROL Inizia]**. Scegli come impostare l’ambiente tecnico.

![Crea la tua app LLM — Inizia a utilizzarla e usa la configurazione di Cloud Manager; il contorno rosso indica l&#39;opzione del repository](/help/assets/guide-infrastructure/setup-choice.png)

| Opzione infrastruttura | Seleziona nella procedura guidata | Che cosa significa |
|---|---|---|
| **[!DNL LLM Apps]infrastruttura gestita** | **[!UICONTROL Gestiamolo]** | Adobe crea e gestisce gli archivi e i servizi connessi per la tua app. L’accesso al codice è disponibile su richiesta. |
| **Archivi personali** | **[!UICONTROL Usa la mia installazione di Cloud Manager]** | Sono stati selezionati due archivi aggiunti a [!DNL Cloud Manager]: uno per i gestori di azioni e uno per i widget. La piattaforma li inizializza se sono vuoti. |

Entrambe le opzioni supportano la generazione automatica delle azioni in un secondo momento. Nessuna delle due opzioni richiede la scrittura manuale delle azioni.

## Utilizza infrastruttura gestita [!DNL LLM Apps] {#adobe-managed-infrastructure}

Utilizza questa opzione per consentire ad Adobe di configurare e gestire gli archivi e i servizi connessi per la tua app. Non è necessario creare archivi o installare [!DNL GitHub] app.

1. Seleziona **[!UICONTROL Gestione]**.
2. Seleziona **[!UICONTROL Passaggio successivo]**.
3. Completare [Dettagli app](#app-details).
4. Selezionare **[!UICONTROL Crea app]**.

Nella pagina dell&#39;app è visualizzato **[!UICONTROL Configurazione dell&#39;app]** mentre Adobe prepara gli archivi e connette il sito widget. L’interfaccia utente indica che la configurazione in genere richiede 1-2 minuti; azioni, test e distribuzione rimangono non disponibili fino a quando la configurazione non è pronta.

![Configurazione dell&#39;app: Adobe sta preparando l&#39;infrastruttura dell&#39;app](/help/assets/guide-infrastructure/provisioning.png)

Se l&#39;installazione non riesce, leggere l&#39;errore e selezionare **[!UICONTROL Riprova]**. Se la pagina non è in grado di controllare l&#39;avanzamento, l&#39;installazione continua in background. Selezionare **[!UICONTROL Controlla di nuovo]** per riconnettersi.

Al termine dell&#39;installazione, continuare con [Crea azioni](/help/guides/creating-actions.md).

## Utilizzare i propri archivi {#your-own-repositories}

Seleziona **[!UICONTROL Usa la configurazione di Cloud Manager]**, quindi **[!UICONTROL Passaggio successivo]**. Questo percorso prevede quattro passaggi della procedura guidata:

**Introduzione → configurazione dell&#39;infrastruttura di azioni → Configurazione dell&#39;infrastruttura di widget → Dettagli dell&#39;app.**

[!DNL LLM Apps] raggiunge gli archivi tramite [!DNL Cloud Manager], utilizzando il tuo account Adobe. Non installare un&#39;app [!DNL GitHub].

### Prima di iniziare {#own-repositories-prerequisites}

- Accedi al programma [!DNL Cloud Manager] a cui apparterrà l&#39;app.
- Due archivi, uno per i gestori di azioni e uno per i widget. Gli archivi vuoti vanno bene, incluso un archivio che contiene solo il README il suo host Git creato.
- Entrambi gli archivi [aggiunti a Cloud Manager](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-manager/content/managing-code/external-repositories) come archivi privati, con proprietà convalidata e stato **Pronto**. [!DNL Cloud Manager] deve essere in grado di scrivervi; un token di accesso di sola lettura non è sufficiente.

Vengono elencati solo gli archivi che ospiti personalmente. Gli archivi ospitati da Adobe in [!DNL Cloud Manager] non sono supportati.

#### L&#39;archivio dei widget è un sito Edge Delivery Services {#widget-repository-is-an-eds-site}

I widget vengono gestiti da un sito [!DNL Edge Delivery Services] (EDS) e l&#39;archivio dei widget contiene il codice di tale sito. Assicurati che l&#39;archivio dei widget appartenga a un sito EDS o farà. Esistono due modi per impostarne uno:

- **Qualsiasi provider Git, con Porta il tuo Git.** Crea il sito EDS in [!DNL Cloud Manager] e collegalo al tuo archivio con **Porta il tuo Git**. [!DNL Cloud Manager] sincronizza quindi il codice del repository con il sito. Eseguire questa operazione prima di avviare la procedura guidata. Vedi [Porta il tuo Git](https://www.aem.live/developer/byo-git).
- **Un archivio [!DNL GitHub], con AEM Code Sync.** Configurare autonomamente il sito EDS, come nell&#39;[esercitazione di Edge Delivery Services](https://www.aem.live/developer/tutorial). La procedura guidata ti guida attraverso l’installazione di AEM Code Sync e il controllo dell’accesso dopo la convalida dell’archivio.

Il passaggio del widget rileva quale caso si applica. Consulta [Configurare l&#39;infrastruttura del widget](#set-up-the-widget-infrastructure).

>[!TIP]
>
>Anche se la società dispone già di un sito EDS, creare un sito EDS separato per i widget [!DNL LLM Apps].

>[!NOTE]
>
>Per un archivio vuoto, la convalida scrive i file di progetto iniziali. Scegliere gli archivi destinati all&#39;app prima di selezionare **[!UICONTROL Convalida archivio]**.

### Configurare l’infrastruttura delle azioni

Il repository delle azioni contiene i gestori e i test lato server eseguiti quando una piattaforma LLM richiama un&#39;azione.

1. In **[!UICONTROL Programma Cloud Manager]**, selezionare il programma che contiene l&#39;archivio azioni.
2. In **[!UICONTROL repository Cloud Manager]**, selezionare il repository delle azioni. Se manca, aggiungerlo in [!DNL Cloud Manager] con **[!UICONTROL Vai a Cloud Manager]**, quindi aggiorna l&#39;elenco.
3. Selezionare **[!UICONTROL Convalida repository]**. La convalida inizializza un archivio vuoto con il modulo di avvio delle app Adobe LLM. Un archivio esistente deve contenere un progetto di azioni compatibile.
4. Quando viene visualizzato **[!UICONTROL Archivio convalidato]**, selezionare **[!UICONTROL Passaggio successivo]**.

![Configurazione dell&#39;infrastruttura di azioni: programma Cloud Manager e repository selezionati, con repository convalidato](/help/assets/guide-infrastructure/action-repository.png)

La piattaforma invia il codice dell’azione generata a un ramo di questo archivio. Selezionando **[!UICONTROL Contrassegna come revisionato]** il ramo viene unito. Consulta [Creare azioni](/help/guides/creating-actions.md).

### Configurare l’infrastruttura dei widget {#set-up-the-widget-infrastructure}

I widget sono risposte visive facoltative collegate alle azioni, ad esempio schede di prodotto o moduli di prenotazione. Edge Delivery Services (EDS) li fornisce da un sito che utilizza l’archivio dei widget.

1. In **[!UICONTROL Origine widget]**, selezionare **[!UICONTROL Seleziona manualmente l&#39;archivio widget]**.

   ![Configurazione infrastruttura widget - Origine widget con selezione manuale archivio widget](/help/assets/guide-infrastructure/widget-source.png)

2. In **[!UICONTROL Programma Cloud Manager]**, selezionare il programma che contiene l&#39;archivio widget. Può essere diverso dal programma dell’archivio delle azioni.
3. In **[!UICONTROL Archivio Edge Delivery Services]**, selezionare l&#39;archivio widget. Non selezionare l’archivio delle azioni.
4. Selezionare **[!UICONTROL Convalida repository]**. La convalida inizializza un archivio vuoto con la boilerplate di AEM. Un repository esistente deve essere un progetto EDS compatibile.

Ciò che accade dopo dipende da dove è ospitato l’archivio dei widget.

#### Archivio widget su un altro provider (porta il tuo Git) {#widget-repository-byog}

Per un repository in [!DNL GitLab], [!DNL Bitbucket], [!DNL Azure DevOps] o [!DNL GitHub Enterprise], la convalida trova il sito EDS che [!DNL Cloud Manager] collega al repository. La procedura guidata mostra il sito in **[!UICONTROL sito Edge Delivery Services]**.

![Configurazione infrastruttura widget - Archivio widget convalidato e sito Edge Delivery Services in cui Cloud Manager lo sincronizza](/help/assets/guide-infrastructure/widget-repository.png)

[!DNL Cloud Manager] sincronizza il codice dell&#39;archivio in tale sito, quindi non è necessario AEM Code Sync. Seleziona **[!UICONTROL Passaggio successivo]**.

Se viene segnalato che nessun sito è collegato, collegare l&#39;archivio a un sito EDS in [!DNL Cloud Manager] con **Porta il tuo Git**, quindi eseguire di nuovo la convalida. Vedi [Porta il tuo Git](https://www.aem.live/developer/byo-git).

#### Archivio widget su GitHub {#widget-repository-github}

Un archivio [!DNL GitHub] utilizza AEM Code Sync per pubblicare il proprio codice. Dopo la convalida, la procedura guidata mostra **[!UICONTROL Installa AEM Code Sync]**. Utilizzare lo stesso account Adobe per [!DNL LLM Apps], l&#39;accesso al sito AEM e l&#39;accesso in scrittura DA.

##### Configurare l’accesso al sito AEM {#aem-site-access}

**Nuova installazione:** Seleziona **[!UICONTROL Installa AEM Code Sync]** e concedi l&#39;accesso all&#39;archivio widget. Completare la procedura guidata di configurazione nella pagina di conferma dell&#39;installazione per aggiungere se stessi come amministratore del sito.

**Sito EDS esistente:** Mantenere l&#39;installazione di AEM Code Sync e confermare che copre l&#39;archivio widget selezionato. Chiedere a un amministratore del sito esistente di aprire Amministratore utenti di AEM all&#39;indirizzo `https://tools.aem.live/tools/user-admin/index.html/?org=<org>&site=<site>` e aggiungere l&#39;account Adobe con il ruolo `admin`. Non è necessario reinstallare Sincronizzazione codice.

Sostituisci `<org>` e `<site>` con i tuoi nomi di organizzazione e sito AEM. L&#39;amministratore del sito può confermare questi valori.

##### Concedere l’accesso in scrittura a DA {#da-write-access}

Per i siti nuovi ed esistenti, chiedere a un amministratore dell&#39;organizzazione DA di:

1. Apri la configurazione DA in `https://da.live/config#/<org>/`, sostituendo `<org>` con l&#39;organizzazione DA che memorizza il contenuto del sito.
2. Apri il foglio **autorizzazioni**. Se non esiste, selezionare **[!UICONTROL Aggiungi foglio]**, denominarlo `permissions` e aggiungere le intestazioni di colonna **percorso**, **gruppi** e **azioni**. Puoi anche aggiungere una colonna **commenti** per descrivere ogni regola.
3. Aggiungi una riga per consentire al tuo account Adobe l&#39;accesso in scrittura a tutto il contenuto del sito: usa il percorso del contenuto del sito in **percorso**, l&#39;e-mail del tuo account Adobe in **gruppi** e `write` in **azioni**. Salva la configurazione.

   ![Foglio delle autorizzazioni DA che mostra gli account di esempio nella colonna dei gruppi e l&#39;accesso in scrittura nella colonna delle azioni](/help/assets/guide-infrastructure/da-write-permissions.png)

   La schermata utilizza account fittizi. Sostituisci `widget-author@example.com` con l&#39;e-mail del tuo account Adobe e utilizza il percorso del contenuto del tuo sito invece dell&#39;esempio `/ + **` a livello di organizzazione. La riga `CONFIG` è destinata agli amministratori di configurazione; non è necessario modificarla per concedere l&#39;accesso al contenuto.

I ruoli del sito AEM non concedono l’accesso in scrittura da. Non è necessario l’accesso in scrittura a livello di organizzazione.

Per informazioni dettagliate sulla configurazione delle autorizzazioni DA, vedere [Autorizzazioni e sicurezza](https://www.aem.live/docs/ew/administering/permissions).

##### Convalidare la configurazione di AEM {#validate-aem-setup}

1. Torna a [!DNL LLM Apps] e seleziona **[!UICONTROL Convalida installazione di AEM]**.
2. Quando viene visualizzata la **[!UICONTROL Configurazione di AEM verificata]**, selezionare **[!UICONTROL Passaggio successivo]**.

Se la convalida non riesce, segui il messaggio per controllare [l&#39;accesso al sito AEM](#aem-site-access) o [l&#39;accesso in scrittura DA](#da-write-access), quindi riconvalida.

>[!NOTE]
>
>La procedura guidata contrassegna **[!UICONTROL Utilizza Cloud Manager Edge Delivery Services]** come **[!UICONTROL Disponibile a breve]** e non consente la selezione. Utilizza l’opzione widget-repository manuale.

## Immetti i dettagli dell&#39;app {#app-details}

Entrambe le opzioni di infrastruttura terminano con **[!UICONTROL Dettagli app]**.

![Dettagli app: il nome dell&#39;app LLM immesso, il programma AEM impostato dall&#39;archivio Cloud Manager e l&#39;area dati Approfondimenti applicazione](/help/assets/guide-infrastructure/app-details.png)

1. Immettere il nome dell&#39;app **[!UICONTROL LLM]**. Utilizza un nome che identifichi il brand o l&#39;esperienza, ad esempio *Frescopa Coffee*.
2. Seleziona il **[!UICONTROL programma AEM]** a cui appartiene l&#39;app. Se si dispone dell&#39;accesso a un solo programma, la procedura guidata lo seleziona automaticamente. Con i tuoi archivi, il programma è il programma dell’archivio azioni e non può essere modificato qui.
3. In **[!UICONTROL Area dati Approfondimenti applicazione]**, scegliere **[!UICONTROL Stati Uniti]** o **[!UICONTROL Europa (UE)]**.
4. Selezionare **[!UICONTROL Crea app]**.

>[!IMPORTANT]
>
>Una volta creata l’app, non è possibile modificare l’area dati Programma AEM e Approfondimenti applicazione. L&#39;area determina dove vengono memorizzati i dati di Application Insights dell&#39;app.

Se non è disponibile alcun programma AEM, chiedi all’amministratore di accedervi. Se il caricamento dei programmi non riesce, selezionare **[!UICONTROL Riprova a caricare i programmi]**.

La procedura guidata apre la pagina dell’app dopo la creazione. Con l&#39;infrastruttura gestita [!DNL LLM Apps], attendere il completamento della configurazione dell&#39;infrastruttura prima di continuare.

Per controllare gli archivi in un secondo momento, apri le **[!UICONTROL impostazioni]** dell&#39;app. La sezione **[!UICONTROL Archivio]** mostra il programma e il nome di ogni repository e, per un archivio widget che utilizza Bring Your Own Git, il relativo sito EDS e l&#39;origine. Gli archivi non possono essere modificati dopo la creazione dell’app.

![Impostazioni app - Sezione archivio con repository boilerplate e Edge Delivery Services, inclusi il sito e l&#39;origine collegati](/help/assets/guide-infrastructure/settings-repositories.png)

Le app create in precedenza con l&#39;app [!DNL GitHub] di Adobe LLM continuano a funzionare con gli archivi esistenti.

## Successivo: creare le azioni

L’app ora dispone della relativa infrastruttura, ma non è ancora stata generata alcuna azione. Continua con [Crea azioni](/help/guides/creating-actions.md) per analizzare un sito Web o scegli la creazione manuale di azioni.

Per informazioni sulla relazione tra i metadati delle azioni, i gestori, i widget e il server MCP distribuito, vedere [Connessione tra app](/help/guides/app-architecture.md).
