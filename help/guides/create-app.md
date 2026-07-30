---
title: Crea automaticamente la prima app LLM
description: Crea un’app Adobe LLM dal sito web, controlla le azioni generate, distribuiscila e testala in una piattaforma LLM supportata, ad esempio ChatGPT.
source-git-commit: f91bb73a39cc5aacf44979ee55dd0ab5f69d4c81
workflow-type: tm+mt
source-wordcount: '1272'
ht-degree: 0%

---


# Creare Automaticamente La Prima App {#create-first-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

La piattaforma trasforma il tuo sito web in un’app completamente funzionale. Propone azioni, scrive il codice e i test dell&#39;handler, crea widget EDS e invia i file generati a due archivi [!DNL GitHub] di tua proprietà.

Attendere circa 15 minuti per la generazione. Al termine di questa esercitazione, verrà distribuita un&#39;app che sarà possibile testare in una piattaforma LLM supportata, ad esempio [!DNL ChatGPT].

**Percorso:** Conferma i requisiti → creare due archivi → creare l&#39;app → esaminare le azioni generate → distribuire in Stage → testare il plug-in → connettere i sistemi di produzione.

## Prima di iniziare

Completa tutti i [requisiti delle app LLM](/help/overview/overview.md#requirements) prima di iniziare questa esercitazione.

Questa esercitazione crea un&#39;app LLM per [Frescopa Coffee](https://frescopa.coffee/).

## Creare due archivi vuoti

La piattaforma ha bisogno di due archivi vuoti. Creare entrambi con lo stesso account o organizzazione [!DNL GitHub]:

- **Archivio gestori**: archivia i gestori azioni e i test. Ad esempio, `my-brand-llm-app`.
- **Archivio EDS**: memorizza gli stili e i blocchi di widget generati. Ad esempio, `my-brand-llm-app-eds`.

Vai a [github.com/new](https://github.com/new) per ogni archivio.

Non inizializzare l&#39;archivio con una licenza README, `.gitignore` o. La piattaforma prepara la struttura di progetto richiesta.

>[!TIP]
>
>Utilizza i nomi dei repository che identificano l’app e lo scopo di ciascun repository. Questo ne semplifica il riconoscimento nella finestra di dialogo per la creazione dell’app.

## Avviare l’app

1. Apri [App Adobe LLM](https://experience.adobe.com/#/@llmapps/llm-apps/) e seleziona **[!UICONTROL Crea app]**.
2. Immettere il nome dell&#39;app **[!UICONTROL LLM]** e una descrizione facoltativa.
3. Seleziona l&#39;**[!UICONTROL area geografica di Analytics]**.

   >[!IMPORTANT]
   >
   >Una volta creata l’app, non è possibile modificare l’area di analisi.

4. In **[!UICONTROL Genera la mia app]**, seleziona **[!UICONTROL Genera automaticamente la mia app]**.
5. In **[!UICONTROL sito Web]**, immettere l&#39;URL del sito Web, incluso il protocollo `https://`. La piattaforma analizza questo sito per determinare azioni utili e risultati di esempio rappresentativi.

![Crea app LLM: dettagli app e la compilazione dell&#39;app sono abilitati](/help/assets/guide-onboarding-agent/app-details-onboarding.png)

## Concedi a [!DNL LLM Apps] l&#39;accesso agli archivi

L&#39;app Adobe LLM Apps [!DNL GitHub] consente a [!DNL LLM Apps] di accedere agli archivi selezionati.

>[!NOTE]
>
>La connessione di un&#39;organizzazione [!DNL GitHub] è una configurazione una tantum. Se l&#39;organizzazione è già visualizzata nella finestra di dialogo, utilizza **[!UICONTROL Gestisci repository su GitHub]** invece di riconnetterti.

### Organizzazione connessa

Se l&#39;app [!DNL GitHub] delle app Adobe LLM è già stata installata prima della creazione degli archivi:

1. Selezionare l&#39;organizzazione connessa.
2. Seleziona **[!UICONTROL Gestisci repository su GitHub]**.
3. Aggiungere i due archivi all&#39;installazione dell&#39;app [!DNL GitHub] esistente.
4. Tornare a [!DNL LLM Apps] e aggiornare gli elenchi del repository.

### Solo prima connessione

Se l’organizzazione non viene visualizzata nella finestra di dialogo:

1. Seleziona **[!UICONTROL Connetti un&#39;organizzazione GitHub]**.
2. Installare l&#39;app [!DNL GitHub] delle app Adobe LLM.
3. Scegliere **[!UICONTROL Seleziona solo archivi]** e selezionare i due archivi.
4. Torna alla finestra di dialogo Crea app LLM.

Se non riesci a installare o aggiornare l&#39;app [!DNL GitHub], rivolgiti a un amministratore dell&#39;organizzazione.

## Selezionare gli archivi

1. In **[!UICONTROL Repository boilerplate]**, selezionare l&#39;organizzazione e l&#39;archivio del gestore vuoto.
2. In **[!UICONTROL Archivio EDS]**, selezionare l&#39;organizzazione e il repository EDS vuoto.

   ![Genera la mia app — seleziona l&#39;organizzazione GitHub, l&#39;archivio standard e l&#39;archivio EDS](/help/assets/guide-onboarding-agent/repos-selected.png)

3. In **[!UICONTROL Termini e condizioni]**, seleziona **[!UICONTROL Accetto i termini di Adobe Developer]**.
4. Selezionare **[!UICONTROL Crea app]**.

## Completare la configurazione EDS

Quando l&#39;archivio EDS selezionato è vuoto, [!DNL LLM Apps] lo inizializza con la boilerplate di AEM. La finestra di dialogo chiede quindi di installare AEM Code Sync prima di riprovare a creare l’app.

1. Nel messaggio seguente all&#39;archivio EDS, seleziona **[!UICONTROL Installa AEM Code Sync]**.
2. In [!DNL GitHub], installare AEM Code Sync e concedergli l&#39;accesso all&#39;archivio EDS.

   Nella pagina di conferma di **Sincronizzazione codice AEM registrato**, in **[!UICONTROL Utenti del sito]**, selezionare **[!UICONTROL + Aggiungi utente]** e aggiungere l&#39;indirizzo di posta elettronica utilizzato per accedere a [!DNL LLM Apps] con il ruolo **[!UICONTROL amministratore]**. Quindi selezionare **[!UICONTROL Termina installazione]** nella parte inferiore della pagina.

   ![Sincronizzazione codice AEM registrata. Aggiungere se stessi come utente del sito con ruolo di amministratore](/help/assets/guide-onboarding-agent/aem-code-sync-site-users-admin.png)

3. Torna alla finestra di dialogo Crea app LLM.

![Crea app LLM: archivio EDS vuoto inizializzato e sincronizzazione codice AEM richiesta](/help/assets/guide-onboarding-agent/install-aem-code-sync.png)

È necessario essere un amministratore per il sito EDS. Se la finestra di dialogo indica che non sei un amministratore:

![Crea app LLM: è richiesto l&#39;accesso dell&#39;amministratore EDS](/help/assets/guide-onboarding-agent/eds-admin-required.png)

1. Seleziona **[!UICONTROL Apri AEM Live Admin]**.
2. Per aggiungerti come amministratore per il sito EDS, fai clic sul pulsante **[!UICONTROL + Aggiungi utente/i]**.

   ![Crea app LLM — aggiungi come amministratore EDS](/help/assets/guide-onboarding-agent/add-eds-admin.png)

3. Torna a [!DNL LLM Apps], aggiorna l&#39;archivio EDS e seleziona di nuovo **[!UICONTROL Crea app]**.

Dopo il superamento dei controlli dell&#39;archivio e dell&#39;amministratore, [!DNL LLM Apps] crea l&#39;app e avvia la generazione di azioni.

## Attendi generazione azioni

Vai alla pagina **[!UICONTROL Azioni]**, da sinistra. Nella pagina Azioni è visualizzato **Individuazione delle azioni per l&#39;esperienza di conversazione** durante l&#39;analisi del sito Web e la generazione dell&#39;app da parte dell&#39;agente. La generazione richiede in genere circa 15 minuti. Puoi uscire da questa pagina e tornare in un secondo momento.

![Azioni — generazione di consigli](/help/assets/guide-onboarding-agent/actions-generating.png)

Durante la generazione, [!DNL LLM Apps]:

1. Analizza il sito web e identifica gli intenti utili dei clienti.
2. Crea metadati di azione, incluse descrizioni e parametri di input.
3. Genera un gestore e verifica ogni azione nell&#39;archivio del gestore.
4. Genera un widget EDS per ogni azione nell&#39;archivio EDS.
5. Prepara le azioni per la revisione.

I gestori generati inizialmente utilizzano dati di esempio derivati dal sito web. Dimostrano l’esperienza completa ma non si connettono ai sistemi di produzione.

## Esamina le azioni generate

Al termine della generazione, nella pagina Azioni vengono visualizzate le azioni generate e le anteprime dei widget. Ogni azione ha un **[!UICONTROL azione generata dall&#39;intelligenza artificiale, che richiede un distintivo]**.

![Azioni — azioni generate pronte per la revisione](/help/assets/guide-onboarding-agent/actions-ready-for-review.png)

Per ogni azione:

1. Seleziona **[!UICONTROL Rivedi]**.
2. Rivedi il nome, la descrizione, i parametri, le annotazioni, il gestore generato e il widget.
3. Seleziona **[!UICONTROL Contrassegna come revisionato]**. In questo modo vengono unite le richieste pull generate.
4. Torna alla pagina Azioni e ripeti per le azioni rimanenti.

![Azione generata - pronta per contrassegnare come rivista](/help/assets/guide-onboarding-agent/generated-action-review.png)

Dopo aver esaminato tutte le azioni, selezionare **[!UICONTROL Vai alla pagina app]**.

![Azioni — tutte le azioni generate sono state esaminate](/help/assets/guide-onboarding-agent/actions-reviewed.png)

>[!NOTE]
>
>Il codice generato è un punto di partenza di tua proprietà. Puoi modificare i metadati delle azioni, i gestori, i test, il widget JavaScript e gli stili di widget dopo la revisione.

## Distribuire l’app

1. Torna alla pagina Dettagli app.
2. Selezionare **[!UICONTROL Distribuisci]**.
3. Seleziona **[!UICONTROL Stage]** come ambiente di destinazione.
4. Selezionare **[!UICONTROL Distribuisci]**.

![Distribuisci — seleziona l&#39;ambiente di staging](/help/assets/guide-onboarding-agent/deploy-stage.png)

[!DNL LLM Apps] sta preparando, generando e pubblicando l&#39;app. Attendi.

![Distribuzione: pipeline di distribuzione in esecuzione](/help/assets/guide-onboarding-agent/deploy-running.png)

![Distribuzione: distribuzione di staging completata](/help/assets/guide-onboarding-agent/deploy-successful.png)

Dopo la distribuzione, nella sezione **[!UICONTROL Test dell&#39;app]** viene visualizzato l&#39;URL del server MCP dell&#39;area di gestione temporanea. Seleziona **[!UICONTROL Copia URL]**.

![Dettagli app — copia l&#39;URL del server MCP dell&#39;area di gestione temporanea](/help/assets/guide-onboarding-agent/app-mcp-url.png)

## Prova in [!DNL ChatGPT]

Segui [Test in ChatGPT](/help/guides/test-in-chatgpt.md) per creare un plug-in utilizzando l&#39;URL del server MCP di staging.

Fai una domanda che corrisponda a una delle azioni generate. Verifica che:

- [!DNL ChatGPT] seleziona l&#39;azione prevista.
- Il widget esegue il rendering e contiene i dati di esempio previsti.
- I controlli Widget producono il comportamento di follow-up previsto.
- La risposta testuale riepiloga accuratamente il risultato.

![ChatGPT — risposta del plug-in dell&#39;app LLM generata](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

Ora disponi di un’app end-to-end completamente funzionante.

## Prepara l’app per la produzione

L’app generata utilizza dati di esempio. Prima di utilizzarlo con i clienti:

1. **Connetti i sistemi** — [personalizza ogni gestore generato](/help/guides/customize-handler.md) per sostituire i dati di esempio con chiamate alle API o alle origini dati.
2. **Proteggi credenziali** — archivia URL API e credenziali in una configurazione runtime gestita, mai in codice sorgente o widget JavaScript.
3. **Convalida dati** — convalida argomenti azione e risposte API, aggiungi timeout richiesta e restituisce messaggi di errore sicuri.
4. **Aggiorna i widget**. Mantenere ogni widget allineato al relativo `structuredContent` del gestore, quindi applicare i requisiti di branding e accessibilità. Vedi [Personalizzare un widget generato](/help/guides/widgets.md).
5. **Verificare i gestori**. Verificare l&#39;input valido, l&#39;input non valido, i risultati vuoti, gli errori API e la forma dati prevista dal widget.
6. **Verifica in Stage** — ridistribuisci e verifica ogni azione tramite il plug-in [!DNL ChatGPT].
7. **Distribuisci in produzione** — dopo il completamento del test dello staging, distribuisci in produzione e crea o aggiorna il plug-in con l&#39;URL del server MCP di produzione.

Per aggiungere una funzionalità non creata dalla piattaforma, vedere [Creare un&#39;azione da zero](/help/guides/create-action.md).

