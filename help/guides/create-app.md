---
title: Configurazione dell’infrastruttura
description: Crea un’app Adobe LLM utilizzando l’infrastruttura gestita di Adobe oppure fornisci due archivi GitHub vuoti da inizializzare per la piattaforma.
source-git-commit: 079eb2403f15a97842c5c336c03ec5bfe10de622
workflow-type: tm+mt
source-wordcount: '1595'
ht-degree: 0%
---

# Configurazione dell’infrastruttura {#create-first-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

L’infrastruttura è la base tecnica dell’app: gli archivi che memorizzano il codice e i servizi che eseguono i gestori delle azioni e distribuiscono i widget agli utenti.

Come spiegato nella [panoramica](/help/overview/overview.md#key-concepts), un gestore **azioni** esegue un&#39;attività, ad esempio la ricerca di prodotti, mentre un **widget** presenta il risultato come un&#39;esperienza interattiva nella conversazione.

[!DNL LLM Apps] archivia il gestore e il codice sorgente del widget in due archivi [!DNL GitHub] separati:

- **Archivio azioni**: contiene i gestori lato server e i relativi test.
- **Archivio widget**: contiene il codice e gli stili dei widget interattivi.

Ad esempio, un gestore di ricerca di prodotti recupera i prodotti corrispondenti e il suo widget li visualizza come schede di prodotto. Gli archivi memorizzano tale codice; Adobe I/O Runtime esegue i gestori distribuiti e Edge Delivery Services fornisce i widget.

La procedura guidata di configurazione prepara questi archivi e collega i servizi necessari per l’app. È consigliabile iniziare con **l&#39;infrastruttura gestita di Adobe**, il modo più veloce per iniziare. Adobe crea e ospita gli archivi per i gestori delle azioni e i widget. Non è necessario configurare manualmente gli archivi.

In alternativa, fornire due archivi [!DNL GitHub] vuoti nel proprio account o organizzazione. La piattaforma li inizializza con i file di progetto richiesti; non è necessario preparare in anticipo il codice dell&#39;azione o del widget.

**In questa guida:** scopri come scegliere un&#39;opzione di infrastruttura e creare la tua app con la procedura guidata di configurazione. Dopo l&#39;installazione, continuare con [Crea azioni](/help/guides/creating-actions.md).

La creazione dell&#39;app **non** avvia la generazione delle azioni. Avvia la generazione dell&#39;azione separatamente dalla pagina dell&#39;app.

## Apri la procedura guidata di creazione app

Apri [App Adobe LLM](https://experience.adobe.com/llm-apps). Dalla pagina di benvenuto, seleziona **[!UICONTROL Inizia]**. Se hai già un&#39;app aperta, usa il selettore delle app e seleziona **[!UICONTROL Crea nuova app]**.

La procedura guidata **[!UICONTROL Crea app LLM]** inizia con **[!UICONTROL Inizia]**. Scegli come impostare l’ambiente tecnico.

![Crea la tua app LLM: scegli tra l&#39;infrastruttura gestita di Adobe e la connessione dei tuoi archivi](/help/assets/guide-infrastructure/setup-choice.png)

| Opzione infrastruttura | Seleziona nella procedura guidata | Che cosa significa |
|---|---|---|
| **Infrastruttura gestita Adobe** | **[!UICONTROL Gestiamolo]** | Adobe crea e gestisce gli archivi e i servizi connessi per la tua app. L’accesso al codice è disponibile su richiesta. |
| **Archivi personali** | **[!UICONTROL La configurerò io stesso]** | Fornisci due archivi GitHub vuoti e concedi l’accesso tramite la procedura guidata. La piattaforma inizializza uno per i gestori di azioni e l&#39;altro per i widget. |

Entrambe le opzioni supportano la generazione automatica delle azioni in un secondo momento. Nessuna delle due opzioni richiede la scrittura manuale delle azioni.

## Utilizzare l’infrastruttura gestita di Adobe {#adobe-managed-infrastructure}

Utilizza questa opzione per consentire ad Adobe di configurare e gestire gli archivi e i servizi connessi per la tua app. Non è necessario creare archivi o installare [!DNL GitHub] app.

1. Seleziona **[!UICONTROL Gestione]**.
2. Seleziona **[!UICONTROL Passaggio successivo]**.
3. Completare [Dettagli app](#app-details).
4. Selezionare **[!UICONTROL Crea app]**.

Nella pagina dell&#39;app è visualizzato **[!UICONTROL Configurazione dell&#39;app]** mentre Adobe prepara gli archivi e connette il sito widget. L’interfaccia utente indica che la configurazione in genere richiede 1-2 minuti; azioni, test e distribuzione rimangono non disponibili fino a quando la configurazione non è pronta.

![Configurazione dell&#39;app: Adobe sta preparando l&#39;infrastruttura dell&#39;app](/help/assets/guide-infrastructure/provisioning.png)

Se l&#39;installazione non riesce, leggere l&#39;errore e selezionare **[!UICONTROL Riprova]**. Se la pagina non è in grado di controllare l&#39;avanzamento, l&#39;installazione continua in background. Selezionare **[!UICONTROL Controlla di nuovo]** per riconnettersi.

Al termine dell&#39;installazione, continuare con [Crea azioni](/help/guides/creating-actions.md).

## Collegare i propri archivi {#your-own-repositories}

Seleziona **[!UICONTROL Imposta io stesso]**, quindi **[!UICONTROL Passaggio successivo]**. Questo percorso prevede quattro passaggi della procedura guidata:

**Introduzione → configurazione dell&#39;infrastruttura di azioni → Configurazione dell&#39;infrastruttura di widget → Dettagli dell&#39;app.**

È necessario accedere agli archivi selezionati e disporre delle autorizzazioni necessarie per installare o configurare le [!DNL GitHub] app richieste. Per gli archivi di proprietà dell’organizzazione, un amministratore potrebbe dover approvare l’accesso.

Crea due archivi vuoti: uno per i gestori di azioni e uno per i widget. Non inizializzare con un file README, una licenza o `.gitignore`. La piattaforma aggiunge la struttura di progetto richiesta quando si convalida ogni archivio nella procedura guidata.

Se si dispone già di azioni compatibili e di progetti EDS, è possibile collegarli, ma non è necessario il codice esistente.

>[!NOTE]
>
>Per un archivio vuoto, la convalida scrive i file di progetto iniziali. Scegliere gli archivi destinati all&#39;app prima di selezionare **[!UICONTROL Convalida archivio]**.

### Configurare l’infrastruttura delle azioni

Il repository delle azioni contiene i gestori e i test lato server eseguiti quando una piattaforma LLM richiama un&#39;azione.

1. In **[!UICONTROL Origine azioni]**, seleziona **[!UICONTROL GitHub]**. Le altre origini di codice non sono attualmente disponibili.
2. Risposta **[!UICONTROL Esiste già un archivio GitHub?]**:
   - Seleziona **[!UICONTROL Sì, utilizzerò un archivio già creato]** per connettere un progetto esistente compatibile o un archivio vuoto.
   - Seleziona **[!UICONTROL Non ho ancora un archivio]**, quindi **[!UICONTROL Crea archivi su GitHub]**, per creare un archivio azioni vuoto. In seguito, torna alla procedura guidata.
3. Connettere l&#39;archivio all&#39;app [!DNL GitHub] delle app Adobe LLM:
   - Se nessuna organizzazione è connessa, selezionare **[!UICONTROL Installa su GitHub]**.
   - Se un&#39;organizzazione è già connessa, selezionare **[!UICONTROL Connetti repository]** e aggiungere il repository a tale installazione.
4. In [!DNL GitHub], scegli **[!UICONTROL Seleziona solo archivi]** e concedi l&#39;accesso solo agli archivi utilizzati da questa app.
5. Torna alla procedura guidata. In **[!UICONTROL Seleziona archivio]**, scegli l&#39;**[!UICONTROL organizzazione]** e il **[!UICONTROL archivio]**.
6. Selezionare **[!UICONTROL Convalida repository]**. La convalida inizializza un archivio vuoto con il modulo di avvio delle app Adobe LLM. Un archivio esistente deve contenere un progetto di azioni compatibile.
7. Dopo la convalida, selezionare **[!UICONTROL Passaggio successivo]**.

![Configurazione dell&#39;infrastruttura delle azioni: scegliere GitHub e connettere un repository delle azioni](/help/assets/guide-infrastructure/action-repository.png)

Se manca un&#39;organizzazione o un repository, utilizzare **[!UICONTROL Connetti un&#39;altra organizzazione]** o **[!UICONTROL Connetti un altro repository]** nel relativo selettore. Dopo aver modificato l&#39;accesso in [!DNL GitHub], selezionare **[!UICONTROL Aggiorna organizzazioni e archivi]**.

### Configurare l’infrastruttura dei widget

I widget sono risposte visive facoltative collegate alle azioni, ad esempio schede di prodotto o moduli di prenotazione. La procedura guidata connette un archivio EDS ([!DNL Edge Delivery Services]) per ospitarli.

Utilizzare lo stesso account Adobe per [!DNL LLM Apps], l&#39;accesso al sito AEM e l&#39;accesso in scrittura DA.

#### Connettere l’archivio dei widget {#connect-widget-repository}

1. In **[!UICONTROL Origine widget]**, selezionare **[!UICONTROL Seleziona manualmente l&#39;archivio widget]**.
2. In **[!UICONTROL Origine codice]**, seleziona **[!UICONTROL GitHub]**.
3. Scegli se disponi già di un archivio. In caso contrario, selezionare **[!UICONTROL Crea archivi su GitHub]**, creare un archivio widget vuoto e tornare alla procedura guidata.
4. Installare o configurare l&#39;app Adobe LLM Apps [!DNL GitHub] per questo archivio, come è stato fatto per l&#39;archivio delle azioni.
5. Selezionare la relativa **[!UICONTROL organizzazione]** e **[!UICONTROL archivio]**. Non selezionare l’archivio delle azioni.
6. Selezionare **[!UICONTROL Convalida repository]**. La convalida inizializza un archivio vuoto con la boilerplate di AEM. Un repository esistente deve essere un progetto EDS compatibile.

#### &#x200B;7. Configurare l’accesso al sito AEM {#aem-site-access}

**Nuova installazione:** Seleziona **[!UICONTROL Installa AEM Code Sync]** e concedi l&#39;accesso all&#39;archivio widget. Completare la procedura guidata di configurazione nella pagina di conferma dell&#39;installazione per aggiungere se stessi come amministratore del sito.

**Sito EDS esistente:** Mantenere l&#39;installazione di AEM Code Sync e confermare che copre l&#39;archivio widget selezionato. Chiedere a un amministratore del sito esistente di aprire Amministratore utenti di AEM all&#39;indirizzo `https://tools.aem.live/tools/user-admin/index.html/?org=<org>&site=<site>` e aggiungere l&#39;account Adobe con il ruolo `admin`. Non è necessario reinstallare Sincronizzazione codice.

Sostituisci `<org>` e `<site>` con i tuoi nomi di organizzazione e sito AEM. L&#39;amministratore del sito può confermare questi valori.

#### &#x200B;8. Concedere l’accesso in scrittura a DA {#da-write-access}

Per i siti nuovi ed esistenti, chiedere a un amministratore dell&#39;organizzazione DA di:

1. Apri la configurazione DA in `https://da.live/config#/<org>/`, sostituendo `<org>` con l&#39;organizzazione DA che memorizza il contenuto del sito.
2. Apri il foglio **autorizzazioni**. Se non esiste, selezionare **[!UICONTROL Aggiungi foglio]**, denominarlo `permissions` e aggiungere le intestazioni di colonna **percorso**, **gruppi** e **azioni**. Puoi anche aggiungere una colonna **commenti** per descrivere ogni regola.
3. Aggiungi una riga per consentire al tuo account Adobe l&#39;accesso in scrittura a tutto il contenuto del sito: usa il percorso del contenuto del sito in **percorso**, l&#39;e-mail del tuo account Adobe in **gruppi** e `write` in **azioni**. Salva la configurazione.

   ![Foglio delle autorizzazioni DA che mostra gli account di esempio nella colonna dei gruppi e l&#39;accesso in scrittura nella colonna delle azioni](/help/assets/guide-infrastructure/da-write-permissions.png)

   La schermata utilizza account fittizi. Sostituisci `widget-author@example.com` con l&#39;e-mail del tuo account Adobe e utilizza il percorso del contenuto del tuo sito invece dell&#39;esempio `/ + **` a livello di organizzazione. La riga `CONFIG` è destinata agli amministratori di configurazione; non è necessario modificarla per concedere l&#39;accesso al contenuto.

I ruoli del sito AEM non concedono l’accesso in scrittura da. Non è necessario l’accesso in scrittura a livello di organizzazione.

Per informazioni dettagliate sulla configurazione delle autorizzazioni DA, vedere [Autorizzazioni e sicurezza](https://www.aem.live/docs/ew/administering/permissions).

#### &#x200B;9. Convalidare la configurazione di AEM {#validate-aem-setup}

1. Torna a [!DNL LLM Apps] e seleziona **[!UICONTROL Convalida installazione di AEM]**.
2. Quando viene visualizzata la **[!UICONTROL Configurazione di AEM verificata]**, selezionare **[!UICONTROL Passaggio successivo]**.

Se la convalida non riesce, segui il messaggio per controllare [l&#39;accesso al sito AEM](#aem-site-access) o [l&#39;accesso in scrittura DA](#da-write-access), quindi riconvalida.

>[!NOTE]
>
>La procedura guidata contrassegna **[!UICONTROL Utilizza Cloud Manager Edge Delivery Services]** come **[!UICONTROL Disponibile a breve]** e non consente la selezione. Utilizza l’opzione widget-repository manuale.

## Immetti i dettagli dell&#39;app {#app-details}

Entrambe le opzioni di infrastruttura terminano con **[!UICONTROL Dettagli app]**.

![Dettagli app — immetti un nome di app, seleziona un programma AEM e scegli l&#39;area dati di Analytics](/help/assets/guide-infrastructure/app-details.png)

1. Immettere il nome dell&#39;app **[!UICONTROL LLM]**. Utilizza un nome che identifichi il brand o l&#39;esperienza, ad esempio *Frescopa Coffee*.
2. Seleziona il **[!UICONTROL programma AEM]** a cui appartiene l&#39;app. Se si dispone dell&#39;accesso a un solo programma, la procedura guidata lo seleziona automaticamente.
3. In **[!UICONTROL Area dati di Analytics]**, scegliere **[!UICONTROL Stati Uniti]** o **[!UICONTROL Europa (UE)]**.
4. Selezionare **[!UICONTROL Crea app]**.

>[!IMPORTANT]
>
>Una volta creata l’app, non è possibile modificare l’area dati Programma AEM e Analisi. L’area di analisi determina dove vengono memorizzati i dati analitici dell’app.

Se non è disponibile alcun programma AEM, chiedi all’amministratore di accedervi. Se il caricamento dei programmi non riesce, selezionare **[!UICONTROL Riprova a caricare i programmi]**.

La procedura guidata apre la pagina dell’app dopo la creazione. Con l’infrastruttura gestita di Adobe, attendi il completamento della configurazione dell’infrastruttura prima di continuare.

## Successivo: creare le azioni

L’app ora dispone della relativa infrastruttura, ma non è ancora stata generata alcuna azione. Continua con [Crea azioni](/help/guides/creating-actions.md) per analizzare un sito Web o scegli la creazione manuale di azioni.

Per informazioni sulla relazione tra i metadati delle azioni, i gestori, i widget e il server MCP distribuito, vedere [Connessione tra app](/help/guides/app-architecture.md).
