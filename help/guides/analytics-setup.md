---
title: Configurare Adobe Analytics e Customer Journey Analytics
description: Inoltra gli eventi delle app Adobe LLM alla tua suite di rapporti Adobe Analytics e alla visualizzazione dati di Customer Journey Analytics attraverso un flusso di dati Adobe Experience Platform.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '2962'
ht-degree: 2%
---

# Configurare Adobe Analytics e Customer Journey Analytics {#analytics-setup}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Invia gli eventi dell&#39;app a [!DNL Adobe Analytics], [!DNL Customer Journey Analytics] o a entrambi. [!DNL LLM Apps] inoltra gli eventi all&#39;Edge Network [!DNL Adobe Experience Platform] tramite uno stream di dati di tua proprietà. Lo stream di dati indirizza gli eventi a ogni servizio aggiunto.

- **[!DNL Adobe Analytics]** segnala le azioni chiamate dalla piattaforma LLM, i collegamenti widget selezionati dagli utenti e l&#39;intento dietro ogni chiamata.
- **[!DNL Customer Journey Analytics]** analizza gli stessi eventi per persona, nelle visualizzazioni dati create con dati [!DNL Experience Platform].

Questa configurazione è separata dall&#39;analisi integrata in [!DNL LLM Apps].

**In questa guida:** Scopri quali impostazioni [!DNL LLM Apps] richiedono in [!DNL Experience Platform], [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics], con collegamenti alla procedura Experience League per ogni schermata di prodotto. Quindi abilita Analytics nell’app, implementa nell’ambiente di staging e verifica i dati prima di implementarli nell’ambiente di produzione.

## Elenco di controllo per l’installazione {#setup-checklist}

Completa ogni passaggio contrassegnato **Obbligatorio** per i servizi che utilizzi. Un passaggio ignorato raramente mostra un errore. Al contrario, i dati non arrivano.

| Passaggio | [!DNL Adobe Analytics] | [!DNL Customer Journey Analytics] |
|---|---|---|
| [1. Crea lo spazio dei nomi dell&#39;identità](#identity-namespace) | Obbligatorio | Obbligatorio |
| [2. Crea schema](#schema) | Obbligatorio | Obbligatorio |
| [3. Crea il set di dati](#dataset) | Non necessario | Obbligatorio |
| [4. Prepara la suite di rapporti](#report-suite) | Obbligatorio | Non necessario |
| [5. Crea lo stream di dati](#datastream) | Obbligatorio | Obbligatorio |
| [6. Abilita Analytics e distribuisci in Stage](#enable-analytics) | Obbligatorio | Obbligatorio |
| [7. Crea connessione e visualizzazione dati](#cja) | Non necessario | Obbligatorio |
| [8. Verifica i dati e distribuiscili alla produzione](#verify) | Obbligatorio | Obbligatorio |

Crea lo spazio dei nomi, lo schema, il set di dati e lo stream di dati nella stessa sandbox [!DNL Experience Platform]. Utilizza la sandbox e lo stream di dati da cui intendi creare rapporti in Produzione. Dopo aver salvato lo stream di dati nell’app, contatta il team delle app LLM per modificarlo.

## Prima di iniziare {#before-you-begin}

Hai bisogno di:

- **Runtime e SDK supportati** L&#39;archivio del gestore deve utilizzare `@adobe/llm-apps-runtime` 1.2.0 o versione successiva. Per i widget EDS, utilizza le app LLM SDK 0.1.13 o versioni successive. Per aggiornare un&#39;app meno recente all&#39;ultimo runtime e a SDK prima di abilitare Analytics, segui [Aggiornare un&#39;app esistente](/help/guides/upgrade-existing-app.md). Le app che soddisfano questi requisiti non necessitano di un aggiornamento.
- **I gruppi di campi agenti.** L&#39;organizzazione deve essere abilitata per i gruppi di campi Informazioni agente ed Evento di conversazione. Se questi gruppi di campi non vengono visualizzati quando aggiungi gruppi di campi a uno schema, contatta il team del tuo account di Adobe. Consulta [Implementare Informazioni Sulla Conversazione](https://experienceleague.adobe.com/it/docs/analytics-platform/using/conversation-insights/implement#schema-and-datasets). Le chiamate di azione sono registrate nel campo `agenticExperience.tools` di Informazioni sull&#39;agente, che si trova in Beta in [!DNL Experience Platform].
- **[!DNL Experience Platform]autorizzazioni** per creare spazi dei nomi delle identità, schemi, set di dati e flussi di dati nella sandbox.
- **Per [!DNL Adobe Analytics]:** autorizzazione a gestire suite di rapporti e regole di elaborazione. Per impostazione predefinita, gli amministratori di prodotto hanno entrambi.
- **Per [!DNL Customer Journey Analytics]:** accesso amministratore prodotto per creare connessioni e visualizzazioni dati.

## Cosa invia l’app {#what-your-app-sends}

Dopo aver abilitato Analytics e distribuito, l’app invia due tipi di eventi:

| Evento | `eventType` | Inviato quando | Inclusioni |
|---|---|---|---|
| Chiamata azione | `mcp.tool_call` | La piattaforma LLM chiama una delle azioni dell’app, incluse le chiamate che hanno esito negativo o che restituiscono una richiesta di accesso. | L’identificatore del codice dell’azione e se la chiamata non è riuscita. L’intento dell’utente, quando l’azione lo raccoglie e la piattaforma ne fornisce uno. Quando la piattaforma li fornisce: il nome della piattaforma, l’ID della conversazione, l’ID utente, la lingua e l’organizzazione della piattaforma e la posizione approssimativa dell’utente. |
| Interazione widget | `web.webinteraction.linkClicks` | Qualcuno seleziona un collegamento o un pulsante in un widget Edge Delivery Services. | Etichetta del collegamento o del pulsante, fino a 100 caratteri. Se fornito dalla chiamata dell’azione, il relativo ID di conversazione e l’ID di turno. Adobe Web SDK aggiunge inoltre l&#39;URL del frame del widget, le dimensioni dello schermo e della finestra del browser, l&#39;ora locale e il fuso orario e la relativa versione. |

In [!DNL Adobe Analytics], ogni chiamata di azione conta come una visualizzazione di pagina di una pagina denominata `mcp:<code identifier>` e ogni interazione del widget conta come un clic di collegamento. Vedi [Tipi di evento Edge Network in Adobe Analytics](https://experienceleague.adobe.com/it/docs/analytics/implementation/aep-edge/hit-types).

Ogni chiamata di azione e ogni interazione del widget è un hit [!DNL Adobe Analytics] e un evento nel set di dati [!DNL Experience Platform]. In un’app a traffico elevato, controlla che il contratto copra questo volume prima di abilitare Analytics.

Tieni presente questi dettagli quando leggi i rapporti:

- **Le chiamate non riuscite includono le richieste di accesso.** Se alcune azioni richiedono l’accesso, prevedi una percentuale di errori più elevata.
- **Alcune chiamate rifiutate non sono registrate.** Una chiamata viene rifiutata prima dell&#39;esecuzione dell&#39;azione e non invia alcun evento quando una piattaforma diversa da [!DNL ChatGPT] chiama un&#39;azione che richiede l&#39;accesso senza un accesso valido o l&#39;ambito richiesto. Nelle app in cui ogni azione richiede l’accesso, tutte le chiamate senza accesso vengono rifiutate allo stesso modo.
- **Intento utente spesso mancante.** La piattaforma decide se fornirla, quindi molte chiamate non ne hanno e vengono visualizzate come **Non specificato**.
- **I dettagli della piattaforma variano.** Attualmente, [!DNL ChatGPT] fornisce tutti i componenti. Per [!DNL Claude], viene registrato solo il nome della piattaforma.
- **Le etichette dei widget provengono dalla pagina.** Per impostare un&#39;etichetta stabile, aggiungere un attributo `data-analytics` al collegamento o al pulsante. In caso contrario, viene utilizzato il relativo testo visibile.

Per scegliere quali azioni raccogliere le finalità dell&#39;utente, vedere [Configurare Analytics](/help/guides/create-action.md#configure-analytics).

>[!NOTE]
>
>Gli eventi possono includere un ID utente della piattaforma e una posizione approssimativa. Prima di abilitare Analytics, controlla le impostazioni di privacy policy e governance dei dati.

### Come gli eventi identificano le persone {#identities}

| Evento | Identità primaria | Namespace |
|---|---|---|
| Chiamata azione | L’ID utente dalla piattaforma LLM, quando la piattaforma ne fornisce uno | `MCPHOSTUSER` |
| Interazione widget | Un Experience Cloud ID (ECID) che Edge Network genera durante la chiamata dell&#39;azione. L&#39;ID `MCPHOSTUSER` viene incluso come identità secondaria quando disponibile. | `ECID` |

Alcune piattaforme, ad esempio [!DNL Claude], non forniscono un ID utente. Le chiamate di azione da queste piattaforme non hanno identità, quindi [!DNL Customer Journey Analytics] le ignora.

>[!NOTE]
>
>Ogni chiamata di azione ottiene un nuovo ECID e un’interazione widget utilizza l’ECID della chiamata di azione che ha mostrato il widget. Di conseguenza:
>
>- [!DNL Customer Journey Analytics] conta le chiamate di azione di un utente come una sola persona, ma conta le interazioni dei widget come una persona diversa per ogni chiamata di azione.
>- [!DNL Adobe Analytics] può contare ogni chiamata di azione come nuovo visitatore.

## Passaggio 1: creare lo spazio dei nomi delle identità {#identity-namespace}

**Richiesto per:** [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics].

Le chiamate di azione identificano le persone nello spazio dei nomi `MCPHOSTUSER`, pertanto lo spazio dei nomi deve esistere nella sandbox. Crea uno spazio dei nomi personalizzato con le seguenti impostazioni:

| Impostazione | Valore |
|---|---|
| Nome visualizzato | `MCP host user` o un altro nome scelto |
| Simbolo di identità | `MCPHOSTUSER`, esattamente come mostrato |
| Tipo di identità | **[!UICONTROL ID multi-dispositivo individuale]** |

Per la procedura, vedere [Creare spazi dei nomi personalizzati](https://experienceleague.adobe.com/it/docs/experience-platform/identity/features/namespaces#create-namespaces).

## Passaggio 2: creare lo schema {#schema}

**Richiesto per:** [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics].

Lo stream di dati e il set di dati utilizzano entrambi questo schema. Crea uno schema con queste impostazioni:

| Impostazione | Valore |
|---|---|
| Tipo di schema | **[!UICONTROL Standard]**, creato con l&#39;opzione **[!UICONTROL Manuale]** |
| Classe base | **[!UICONTROL Evento esperienza]** |
| Gruppi di campi | Informazioni sull’agente, evento di conversazione, dettagli web, dettagli sull’ambiente e dettagli sull’implementazione |
| Nome visualizzato | Qualsiasi nome, ad esempio `AEM LLM Apps` |

>[!IMPORTANT]
>
>Aggiungi tutti e cinque i gruppi di campi. [!DNL LLM Apps] richiede informazioni sull&#39;agente e evento di conversazione per le chiamate di azione. Gli altri tre contengono i campi pagina, collegamento, dispositivo, ambiente e implementazione inclusi negli eventi. Se manca un gruppo di campi, [!DNL Experience Platform] può eliminare tali campi o rifiutare l&#39;evento.

Non è necessario il gruppo di campi Adobe Analytics ExperienceEvent. L&#39;intento dell&#39;utente raggiunge [!DNL Adobe Analytics] tramite una regola di elaborazione. Vedere [Passaggio 4](#report-suite).

Per la procedura, vedere [Creare un nuovo schema](https://experienceleague.adobe.com/it/docs/experience-platform/xdm/ui/resources/schemas#create) e [Aggiungere gruppi di campi](https://experienceleague.adobe.com/it/docs/experience-platform/xdm/ui/resources/schemas#add-field-groups).

## Passaggio 3: creare il set di dati {#dataset}

**Richiesto per:** [!DNL Customer Journey Analytics].

Il set di dati memorizza gli eventi dell&#39;app in [!DNL Experience Platform]. Crea un set di dati con queste impostazioni:

| Impostazione | Valore |
|---|---|
| Metodo di creazione | **[!UICONTROL Crea set di dati dallo schema]** |
| Schema | Lo schema dal [passaggio 2](#schema) |
| Nome | Qualsiasi nome, ad esempio `AEM LLM Apps` |

Per la procedura, vedere [Creare un set di dati](https://experienceleague.adobe.com/it/docs/experience-platform/catalog/datasets/user-guide#create).

## Passaggio 4: preparare la suite di rapporti {#report-suite}

**Richiesto per:** [!DNL Adobe Analytics].

[!DNL Adobe Analytics] mappa automaticamente i campi del nome della pagina e del collegamento, pertanto le pagine `mcp:<code identifier>` e i clic sul collegamento del widget non richiedono alcuna configurazione aggiuntiva. L’intento dell’utente richiede un eVar e una regola di elaborazione.

### Creare o scegliere una suite di rapporti {#create-report-suite}

Crea una suite di rapporti o scegliine una esistente. Prendere nota dell&#39;ID suite di rapporti completo, incluso il prefisso aggiunto da [!DNL Adobe Analytics]. È necessario l&#39;ID in [Passaggio 5](#datastream).

Per informazioni sulla procedura, vedere [Creare una suite di rapporti](https://experienceleague.adobe.com/it/docs/analytics/admin/admin-tools/manage-report-suites/c-new-report-suite/t-create-a-report-suite).

### Abilita eVar intento utente {#user-intent-evar}

Nelle variabili di conversione della suite di rapporti, abilita un’eVar con le seguenti impostazioni:

| Impostazione | Valore |
|---|---|
| eVar | EVAR3 |
| Nome | `User Intent` |
| Tipo | **[!UICONTROL Stringa di testo]** |
| Allocazione | **[!UICONTROL Più recente (ultimo)]** |
| Scade dopo | **[!UICONTROL Visita]** |
| Stato | **[!UICONTROL Abilitato]** |

Se la suite di rapporti utilizza già eVar3, scegli un altro eVar non utilizzato e utilizza tale eVar nella regola di elaborazione.

Per la procedura, vedere [Aggiungere o modificare le variabili di conversione](https://experienceleague.adobe.com/it/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/conversion-variables/conversion-var-admin#add-or-edit-conversion-variables).

### Creare la regola di elaborazione {#processing-rule}

[!DNL Adobe Analytics] riceve l&#39;intento dell&#39;utente come variabile di dati di contesto, non come eVar. Per informazioni sul motivo, vedere [Mappatura implicita](https://experienceleague.adobe.com/it/docs/analytics/implementation/aep-edge/xdm-var-mapping#implicit-mapping). Crea una regola di elaborazione che copia l’intento dell’utente in eVar:

- **Condizione:** la variabile di dati di contesto `a.x.conversation.prompt.raw.0.purpose` è uguale a `intents`.
- **Azione:** sovrascrive il valore di **Intento utente (eVar3)** con la variabile di dati di contesto `a.x.conversation.prompt.raw.0.text`.

Senza questa regola, la dimensione **Intento utente** rimane vuota. La regola si applica solo ai dati raccolti dopo il salvataggio. Le interazioni dei widget non hanno alcun intento da parte dell’utente, pertanto la regola non le modifica.

Anche altri campi, ad esempio se la chiamata non è riuscita o il nome della piattaforma, arrivano come variabili di dati di contesto. Per creare rapporti su di esse in [!DNL Adobe Analytics], creare altre regole di elaborazione nello stesso modo.

Per la procedura, vedere [Panoramica delle regole di elaborazione](https://experienceleague.adobe.com/it/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-overview) e [Casi di utilizzo delle regole di elaborazione](https://experienceleague.adobe.com/it/docs/analytics/admin/admin-tools/manage-report-suites/edit-report-suite/report-suite-general/processing-rules/pr-use-cases).

## Passaggio 5: creare lo stream di dati {#datastream}

**Richiesto per:** [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics].

Lo stream di dati riceve gli eventi dell’app e li inoltra a ogni servizio aggiunto.

### Creare lo stream di dati {#create-datastream}

Crea un flusso di dati con le seguenti impostazioni:

| Impostazione | Valore |
|---|---|
| Nome | Qualsiasi nome, ad esempio `AEM LLM Apps` |
| Schema di mappatura | Lo schema dal [passaggio 2](#schema) |

Crea lo stream di dati nella stessa sandbox dello schema. Non è possibile modificare la sandbox di un flusso di dati dopo averlo creato.

Per la procedura, vedere [Creare un flusso di dati](https://experienceleague.adobe.com/it/docs/experience-platform/datastreams/configure#create).

### Aggiungere i servizi {#add-services}

Aggiungi un servizio per ogni destinazione:

| Servizio | Impostazioni | Obbligatorio per |
|---|---|---|
| **[!UICONTROL Adobe Analytics]** | **[!UICONTROL Abilitato]**. In **[!UICONTROL ID suite di rapporti]**, immetti l&#39;ID da [Passaggio 4](#report-suite). | [!DNL Adobe Analytics] |
| **[!UICONTROL Adobe Experience Platform]** | **[!UICONTROL Abilitato]**. In **[!UICONTROL Set di dati evento]**, selezionare il set di dati da [Passaggio 3](#dataset). Lascia deselezionate le altre opzioni. | [!DNL Customer Journey Analytics] |

![Aggiungi servizio - Adobe Analytics abilitato con un ID suite di rapporti di esempio](/help/assets/guide-analytics/datastream-analytics-service.png)

![Servizio Adobe Experience Platform - il set di dati evento selezionato e le altre opzioni cancellate](/help/assets/guide-analytics/datastream-platform-service.png)

Per la procedura, vedere [Aggiungere servizi a un flusso di dati](https://experienceleague.adobe.com/it/docs/experience-platform/datastreams/configure#add-services), [Impostazioni Adobe Analytics](https://experienceleague.adobe.com/it/docs/experience-platform/datastreams/configure#analytics) e [Impostazioni Adobe Experience Platform](https://experienceleague.adobe.com/it/docs/experience-platform/datastreams/configure#aep).

Quindi copia il **[!UICONTROL ID Datastream]** dai dettagli dello stream di dati. Ne hai bisogno in [Passaggio 6](#enable-analytics).

![Dettagli dello stream di dati - Servizi Adobe Analytics e Adobe Experience Platform abilitati, con l&#39;ID dello stream di dati di esempio e lo schema di mappatura nel pannello laterale](/help/assets/guide-analytics/datastream-services.png)

## Passaggio 6: abilitare Analytics e distribuirla nello stage {#enable-analytics}

**Richiesto per:** [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics].

>[!IMPORTANT]
>
>Dopo il salvataggio, lo stream di dati è di sola lettura in Impostazioni app. Contatta il team delle app LLM per modificarlo o per disattivare Analytics. Controlla l’ID dello stream di dati prima di salvare. Puoi comunque modificare la suite di rapporti e il set di dati nel flusso di dati stesso.

1. Apri l&#39;app e seleziona **[!UICONTROL Impostazioni]** nel menu di navigazione a sinistra.
2. Selezionare la scheda **[!UICONTROL Analytics]**.
3. Attiva **[!UICONTROL Abilita Analytics]**.
4. In **[!UICONTROL ID Datastream]**, incolla l&#39;ID da [Passaggio 5](#datastream).
5. In **[!UICONTROL Requisiti]**, seleziona **[!UICONTROL Visualizza dettagli]** e verifica che l&#39;app soddisfi tutti i requisiti.

   ![Impostazioni app, scheda Analytics - Abilita Analytics attivato, un campo ID Datastream vuoto e l&#39;elenco dei requisiti espanso](/help/assets/guide-analytics/analytics-settings.png)

6. Seleziona **[!UICONTROL Salva modifiche]**.
7. [Distribuisci l&#39;app](/help/guides/deploy-your-app.md) in Stage. L’impostazione ha effetto sulla prossima distribuzione di ogni ambiente.

>[!NOTE]
>
>Lo stream di dati si applica all’intera app. Stage e Production inviano eventi allo stesso flusso di dati e gli eventi non registrano quali ambienti li hanno inviati. Le chiamate di test sullo stage vengono visualizzate negli stessi rapporti del traffico di produzione.

## Passaggio 7: creare la connessione e la visualizzazione dati {#cja}

**Richiesto per:** [!DNL Customer Journey Analytics].

### Creare la connessione {#connection}

Crea una connessione e aggiungi il set di dati da [Passaggio 3](#dataset) con le seguenti impostazioni:

| Impostazione | Valore |
|---|---|
| Sandbox | Sandbox che contiene il set di dati |
| Numero medio di eventi giornalieri | Intervallo corrispondente al volume previsto |
| Set di dati | Il set di dati da [Passaggio 3](#dataset) |
| ID persona | **[!UICONTROL Mappa identità]** |
| Utilizzare lo spazio dei nomi identità primario | Selezionato |
| Marca temporale | **[!UICONTROL Timestamp]** |
| Tipo di origine dati | **[!UICONTROL Altro]** |
| Descrizione origine dati | Qualsiasi descrizione, ad esempio `LLM Apps events` |
| Importa nuovi dati | **[!UICONTROL Importazione di tutti i nuovi dati]** attivata |
| Retrocompilazione dei set di dati | **[!UICONTROL Recupera tutti i dati esistenti]** attivato, se il set di dati contiene già eventi da analizzare |

>[!IMPORTANT]
>
>Attiva **[!UICONTROL Importa tutti i nuovi dati]**. Questa impostazione crea una connessione continua e i nuovi eventi diventano automaticamente disponibili in Workspace.

Con questo ID persona, [!DNL Customer Journey Analytics] utilizza l&#39;identità primaria di ogni evento come persona e ignora gli eventi che non hanno un&#39;identità primaria. Vedi [Come gli eventi identificano le persone](#identities).

Per la procedura, vedere [Creare una connessione](https://experienceleague.adobe.com/it/docs/analytics-platform/using/cja-connections/create-connection#create-connection), [Impostazioni set di dati evento](https://experienceleague.adobe.com/it/docs/analytics-platform/using/cja-connections/create-connection#event-dataset-settings) e [Utilizzare Identity Map come ID persona](https://experienceleague.adobe.com/it/docs/analytics-platform/using/cja-connections/create-connection#id-map).

### Creare la visualizzazione dati {#data-view}

Crea una visualizzazione dati sulla connessione con queste impostazioni:

| Impostazione | Valore |
|---|---|
| Connessione | La connessione appena creata |
| Nome | Qualsiasi nome, ad esempio `LLM Apps` |
| Componenti | Le dimensioni e le metriche su cui desideri creare rapporti. Vedi la tabella seguente. |

Aggiungi questi campi schema come componenti:

| Report su | Campo schema | Aggiungi come |
|---|---|---|
| Azione | `agenticExperience.tools.name` | Dimensione |
| Chiamate di azione non riuscite | `agenticExperience.tools.failed` | Dimensione |
| Motivo dell’errore | `agenticExperience.tools.errorReason` | Dimensione |
| Platform | `agenticExperience.agents.name` | Dimensione |
| Intento utente | `conversation.prompt.raw.text` | Dimensione |
| Conversazione | `conversation.conversationID` | Dimensione |
| Etichetta widget | `web.webInteraction.name` | Dimensione |

Aggiungi campi di testo, ad esempio intento dell’utente, come dimensioni. Un campo di testo aggiunto come metrica può essere conteggiato solo e la suddivisione per i relativi valori mostra **Non valido**.

Per la procedura, vedere [Configurare una visualizzazione dati](https://experienceleague.adobe.com/it/docs/analytics-platform/using/cja-dataviews/create-dataview#configure) e [Componenti](https://experienceleague.adobe.com/it/docs/analytics-platform/using/cja-dataviews/create-dataview#components).

## Passaggio 8: verificare i dati e distribuirli in produzione {#verify}

**Richiesto per:** [!DNL Adobe Analytics] e [!DNL Customer Journey Analytics].

Chiama un&#39;azione nell&#39;app Stage da una piattaforma LLM supportata e seleziona un collegamento nel relativo widget. Vedi [Test in ChatGPT](/help/guides/test-in-chatgpt.md) o [Test in Claude](/help/guides/test-in-claude.md). Controlla quindi ogni servizio configurato:

| Servizio | Dove controllare | Cosa aspettarsi |
|---|---|---|
| [!DNL Experience Platform] | **[!UICONTROL Attività set di dati]** per il set di dati da [Passaggio 3](#dataset) | Nuovi batch acquisiti e nessun batch non riuscito |
| [!DNL Adobe Analytics] | Un progetto Workspace che utilizza la suite di rapporti di [Passaggio 4](#report-suite), con una tabella a forma libera | **[!UICONTROL Righe pagina]** denominate `mcp:<code identifier>`, **[!UICONTROL Righe collegamento personalizzato]** per le interazioni widget e testo intento nella dimensione **Intento utente** |
| [!DNL Customer Journey Analytics] | Un progetto Workspace che utilizza la visualizzazione dati di [Passaggio 7](#cja) | **[!UICONTROL Eventi]** per le chiamate azione e le interazioni widget |

Ogni servizio elabora i dati con un certo ritardo, pertanto i nuovi eventi non vengono visualizzati immediatamente.

Per generare i report, vedere [Creare progetti in Adobe Analytics](https://experienceleague.adobe.com/it/docs/analytics/analyze/analysis-workspace/build-workspace-project/create-projects), [Panoramica tabelle a forma libera](https://experienceleague.adobe.com/it/docs/analytics/analyze/analysis-workspace/visualizations/freeform-table/freeform-table) e [Creare progetti in Customer Journey Analytics](https://experienceleague.adobe.com/it/docs/analytics-platform/using/cja-workspace/build-workspace-project/create-projects).

Quando i dati sono visualizzati correttamente, [distribuisci l&#39;app](/help/guides/deploy-your-app.md) in produzione.

## Limitazioni {#limitations}

- **Interazioni widget divise in più persone.** Ogni chiamata di azione ottiene un nuovo ECID, quindi [!DNL Customer Journey Analytics] conta le interazioni dei widget da diverse chiamate di azione come persone diverse. Vedi [Come gli eventi identificano le persone](#identities).
- **Nessun collegamento al sito Web.** Quando qualcuno segue un collegamento widget al tuo sito web, la visita del sito web non è collegata alla conversazione.
- **Report condivisioni di produzione e staging.** Entrambi gli ambienti inviano allo stesso flusso di dati e gli eventi non registrano quale ambiente li ha inviati.
- **Alcune piattaforme non inviano identità.** Le chiamate di azione provenienti dalle piattaforme che non forniscono un ID utente, ad esempio [!DNL Claude], vengono ignorate in [!DNL Customer Journey Analytics].
- **Un turno per chiamata azione.** Ogni chiamata di azione viene registrata come proprio turno di conversazione, quindi un prompt che attiva diverse azioni conta come diversi turni.

## Risoluzione di problemi {#troubleshooting}

| Cosa vedi | Cosa controllare |
|---|---|
| Il set di dati non riceve batch | Hai implementato l’app nell’ambiente che stai testando dopo aver salvato le impostazioni di Analytics. L’ID dello stream di dati è corretto. Lo stream di dati include il servizio **[!UICONTROL Adobe Experience Platform]** con il set di dati. |
| Batch non riusciti | Lo schema include tutti e cinque i gruppi di campi del [passaggio 2](#schema). |
| [!DNL Adobe Analytics] non ha dati | Il servizio **[!UICONTROL Adobe Analytics]** dello stream di dati è abilitato con l&#39;ID suite di rapporti corretto. |
| **La pagina** mostra `mcp:<code identifier>`, ma **l&#39;intento dell&#39;utente** e i campi dell&#39;azione sono vuoti | L&#39;archivio del gestore utilizza `@adobe/llm-apps-runtime` 1.2.0 o versione successiva e l&#39;utente ha distribuito dopo l&#39;aggiornamento. Vedi [Prima di iniziare](#before-you-begin). |
| **Intento utente** è vuoto per alcune azioni | La regola di elaborazione del [passaggio 4](#processing-rule) utilizza le variabili di dati di contesto esattamente come mostrato, in minuscolo, e l&#39;eVar corretto. L&#39;azione ha **[!UICONTROL Raccogli intento utente]** attivato. La regola si applica solo ai dati raccolti dopo averli salvati. |
| [!DNL Customer Journey Analytics] mostra un numero inferiore di eventi rispetto al set di dati | **[!UICONTROL Importazione di tutti i nuovi dati]** in corso. Gli eventi senza un’identità primaria vengono ignorati, ad esempio le chiamate di azione provenienti da piattaforme che non forniscono un ID utente. |
| Non puoi modificare l’ID dello stream di dati o disattivare Analytics | Queste impostazioni sono di sola lettura in Impostazioni app dopo l’attivazione. Contatta il team delle app LLM per assistenza. Per instradare i dati altrove, modifica la suite di rapporti o il set di dati nel flusso di dati stesso. |

## Passaggio successivo {#whats-next}

- [Configurare Analytics per un’azione](/help/guides/create-action.md#configure-analytics)
- [Distribuire l’app](/help/guides/deploy-your-app.md)
