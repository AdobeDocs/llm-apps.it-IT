---
source-git-commit: 03c918b1643d9c4e8ebee40fd67694acb6751a14
workflow-type: tm+mt
source-wordcount: '1080'
ht-degree: 0%
---
# Manifesto della schermata

Casella in entrata di acquisizione: `docs-captures/<YYYY-MM-DD>/`

Acquisisci solo punti di controllo che aiutano materialmente l’utente a prendere una decisione o a verificare lo stato.

Non è necessario che i nomi dei file Source corrispondano ai nomi dei file finali. L’abilità mappa le schermate in base allo stato visibile dell’interfaccia utente, conserva i file non elaborati e crea copie bonificate utilizzando i nomi seguenti.

Ogni guida di seguito dichiara la propria directory di output. Utilizza quella per la sezione a cui appartiene l&#39;acquisizione.

&#x200B;# Guida all’onboarding

Directory di output: `help/assets/guide-onboarding-agent/`

## Acquisizioni richieste

### `app-details-onboarding.png`

- Stato: nome app, area di analisi e **Crea automaticamente l&#39;app** selezionata.
- Includi: dettagli dell’app, area di analisi e l’inizio della Generazione dell’app.
- Testo alternativo: `Create LLM App — app details and Build My App enabled`

### `install-aem-code-sync.png`

- Stato: archivio EDS vuoto inizializzato con boilerplate di AEM; sincronizzazione codice di AEM richiesta.
- Include: messaggio di convalida del repository EDS e collegamento di installazione.
- Testo alternativo: `Create LLM App — empty EDS repository initialized and AEM Code Sync required`

### `eds-admin-required.png`

- Stato: AEM Code Sync installato, ma l&#39;utente corrente non è un amministratore del sito EDS.
- Includi: il messaggio di convalida completo e **Apri AEM Live Admin**.
- Testo alternativo: `Create LLM App — EDS administrator access required`

### `actions-generating.png`

- Stato: pagina Azioni quando l’onboarding è attivo.
- Includi: messaggi di avanzamento e passaggi di generazione.
- Testo alternativo: `Actions — generating recommendations`

### `actions-ready-for-review.png`

- Stato: elenco di azioni generato al termine dell’onboarding e prima dell’approvazione.
- Include: nomi delle azioni, stato generato/revisione e controllo revisione.
- Utilizzare solo il contenuto degli staffaggi.
- Testo alternativo: `Actions — generated actions ready for review`

### `generated-action-review.png`

- Stato: un rappresentante ha generato un’azione.
- Includi: navigazione metadati azione e widget, risultato generazione gestore e **Contrassegna come revisionato**.
- Maschera: se necessario, proprietario dell’archivio.
- Testo alternativo: `Generated action — ready to mark as reviewed`

### `actions-reviewed.png`

- Stato: ogni azione generata è stata rivista.
- Includi: **Tutte le azioni sono riviste**, i distintivi delle azioni e **Vai alla pagina dell&#39;app**.
- Testo alternativo: `Actions — all generated actions reviewed`

### `deploy-stage.png`

- Stato: finestra di dialogo Distribuzione prima dell’avvio.
- Include: ambiente di destinazione della fase e **Distribuisci**.
- Testo alternativo: `Deploy — select the Stage environment`

### `deploy-running.png`

- Stato: pipeline di distribuzione in esecuzione.
- Includi: passaggi di preparazione, avvio, generazione e pubblicazione.
- Testo alternativo: `Deploy — deployment pipeline running`

### `deploy-successful.png`

- Stato: distribuzione di staging riuscita.
- Include: ambiente e stato di completamento.
- Maschera: spazio dei nomi runtime, URL MCP completo, ID, timestamp se identificabili.
- Testo alternativo: `Deploy — successful staging deployment`

### `app-mcp-url.png`

- Stato: verifica la sezione dell’app dopo la distribuzione.
- Include: ambiente di gestione temporanea, **Copia URL** e cronologia di distribuzione completata.
- Mask: URL del server MCP.
- Testo alternativo: `App Detail — copy the staging MCP server URL`

### `chatgpt-plugins-page.png`

- Stato: pagina Plug-in di ChatGPT.
- Includi: scheda Plug-in, pulsante Cerca e crea.
- Testo alternativo: `ChatGPT — Plugins page`

### `chatgpt-new-plugin.png`

- Stato: finestra di dialogo Nuovo plug-in.
- Include: nome, descrizione, URL server, autenticazione, conferma e creazione.
- Mask: URL del server MCP.
- Testo alternativo: `ChatGPT — create a plugin with the MCP server URL`

### `chatgpt-plugin-connect.png`

- Stato: conferma dopo la creazione del plug-in.
- Includi: **Aggiungi <plugin> a ChatGPT &#x200B;** e**&#x200B; Connetti &#x200B;**.
- Maschera: URL del browser e identificatori del connettore.
- Testo alternativo: `ChatGPT — connect the new plugin`

### `chatgpt-generated-app.png`

- Stato: il plug-in di correzione viene richiamato in ChatGPT.
- Include: app allegata, widget generato e risposta testuale.
- Escludi: cronologia conversazioni, nome account e app non correlate.
- Testo alternativo: `ChatGPT — generated LLM App plugin response`

## Acquisizioni facoltative

Aggiungi un&#39;acquisizione solo se la prosa non è in grado di spiegare chiaramente la decisione:

- Selezione dell’accesso all’archivio dell’app GitHub.
- Stato di onboarding non riuscito per la risoluzione dei problemi.
- Caricamento icona plug-in.

Non aggiungere schermate per elenchi di campi statici già chiari in prosa.

&#x200B;# Guida all’autenticazione

Directory di output: `help/assets/guide-authentication/`

Riferimento da [authentication.md](../../../help/guides/authentication.md).

Il passaggio **[!UICONTROL Copia l&#39;identificatore di risorsa]** riutilizza la guida all&#39;onboarding di
`app-mcp-url.png`. Non catturarlo di nuovo.

Ogni acquisizione in questa sezione mostra la configurazione della sicurezza. Maschera prima del salvataggio:

- L&#39;URL **[!UICONTROL Issuer]** e qualsiasi nome host che identifica il provider di identità o il relativo fornitore.
- L’URL completo del server MCP, ovunque venga visualizzato.
- Identificatori tenant, client e organizzazione.
- Nome account, avatar ed e-mail.

Utilizza valori segnaposto neutri in cui un campo deve rimanere leggibile, ad esempio un emittente di
`https://auth.example.com`. I nomi di ambito devono essere letti come esempi generici, ad esempio `orders:read`.

## Acquisizioni richieste

### `auth-core-settings.png`

- Stato: **[!UICONTROL Impostazioni]** > **[!UICONTROL Autenticazione]** con **[!UICONTROL Abilita autenticazione]** e **[!UICONTROL Impostazioni di base]** compilate.
- Include: il selettore **[!UICONTROL Workspace]** che mostra **[!UICONTROL Stage]**, **[!UICONTROL Abilita autenticazione]** nello stato On, **[!UICONTROL Issuer]** e **[!UICONTROL Ambiti supportati]** con almeno due ambiti.
- Includere il controllo compresso **[!UICONTROL Impostazioni avanzate]**, in modo che il lettore possa vedere che **[!UICONTROL JWKS URI]** è facoltativo e dove si trova.
- Maschera: il nome host dell’emittente.
- Testo alternativo: `Authentication — enable authentication and complete the core settings`

Acquisito il 25 agosto 2026. Ritagliato per rilasciare l’area di lavoro vuota; non è necessaria alcuna maschera, perché
**[!UICONTROL Emittente]** è stato impostato su `https://auth.example.com` nel prodotto prima del
acquisizione. Preferisci modificare l’immagine in un secondo momento. **[!UICONTROL Ambiti supportati]** blocchi
un ambito (`read:all`); due illustrerebbero meglio il campo, ma questo non vale la pena
ri-acquisire da solo.

### `auth-per-action.png`

- Stato: **[!UICONTROL Configurazione per azione]** dopo l&#39;abilitazione dell&#39;autenticazione, con le modalità deliberatamente miste.
- Includi: almeno tre azioni, una per modalità - **[!UICONTROL Nessuna]**, **[!UICONTROL Obbligatoria]** e **[!UICONTROL Facoltativa]** - e la colonna **[!UICONTROL Ambiti]** popolata su quelle gestite.
- Includi: **[!UICONTROL Richiedi autenticazione per tutte le azioni]**, idealmente nello stato indeterminato, che è ciò che produce una configurazione mista.
- Utilizzate solo i nomi delle azioni di staffaggio.
- Testo alternativo: `Authentication — set an auth mode and scopes for each action`

Acquisito il 25 agosto 2026. Solo ritagliato, niente da mascherare. Mostra tutte e tre le modalità, un popolato
**[!UICONTROL Ambito]** cella e **[!UICONTROL Richiedi autenticazione per tutte le azioni]** nel relativo
stato indeterminato, con `Test Action 1/2/3` come nomi di staffaggio.

Ritaglia **all&#39;interno** del bordo del contenitore del pannello delle impostazioni. Una regola di altezza intera di 1 pixel è posizionata su ogni
dell&#39;acquisizione, lasciando uno dei due nel frame si legge come una linea
immagine.

L&#39;avviso del prodotto relativo all&#39;applicazione dell&#39;autenticazione a [!DNL Claude] per connettore è stato
**non osservato in questa scheda in due turni di acquisizione**, pertanto non è necessario in questo punto. Il
guida afferma invece quel comportamento in prosa. Se l’avviso è presente in una build successiva,
acquisirlo come `auth-claude-warning.png` e aggiungere una voce.

### `chatgpt-authentication-mode.png`

- Stato: viene aperta la finestra di dialogo **[!UICONTROL Nuovo plug-in]** con il menu a discesa **[!UICONTROL Autenticazione]**.
- Includi: tutti e tre i valori - **[!UICONTROL Nessuna autenticazione]**, **[!UICONTROL Misti]** e **[!UICONTROL OAuth]** - in modo che la tabella di mappatura nella guida possa essere confrontata con il controllo reale.
- Maschera: l’URL del server MCP e qualsiasi identificatore di connettore nell’URL del browser.
- Testo alternativo: `ChatGPT — select the authentication mode for the plugin`

Inquadrala nello stesso modo in cui viene inquadrata nella `chatgpt-new-plugin.png` della guida all’onboarding: la scheda con
un margine della pagina ancora visibile, circa 40 px a sinistra e in alto. Non ritagliare lo scaricamento in
il Card.

Acquisito il 25 agosto 2026 in modalità chiara, per sincronizzare tutte le altre acquisizioni nella documentazione. Il
nel menu a discesa viene omesso il campo **[!UICONTROL URL server]**, pertanto l&#39;URL MCP non è leggibile, ma
il materiale traslucido consente di sfocare un&#39;immagine sfocata del contenuto del campo accanto al
opzioni. Le tre righe non evidenziate sono state ridipinte con il riempimento del pannello e le relative etichette
, che lo rimuove. Verificare mediante campionamento, non per occhio: il sanguinamento è sufficientemente debole da
manca ed è l’URL del server MCP.

Nota che il controllo attivo offre **quattro** valori: **[!UICONTROL OAuth]**, **Accesso
token/chiave API&rbrack;**, &#x200B;** [!UICONTROL Nessuna autenticazione] **&#x200B; e &#x200B;** [!UICONTROL Misto]**. Mappatura della guida
la tabella descrive solo i tre a cui le modalità di autenticazione di un’app possono mappare, il che è corretto, ma non
descrivi il menu a discesa come contenente tre opzioni.

## Acquisizioni facoltative

Aggiungi solo se la prosa si dimostra insufficiente:

- `auth-scope-blocked.png` — **[!UICONTROL Salva]** bloccato perché un&#39;azione richiede un ambito mancante da **[!UICONTROL Ambiti supportati]**. Utile per la voce relativa alla risoluzione dei problemi.
- La richiesta di accesso alla conversazione intermedia genera un&#39;azione **[!UICONTROL Facoltativa]**. L’interfaccia utente di proprietà della piattaforma cambia spesso ed è già descritta in prosa.

Non acquisire la pagina di accesso del provider di identità. Identifica il fornitore, nome non presente in questa documentazione.
