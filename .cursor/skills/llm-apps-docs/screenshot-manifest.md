---
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '398'
ht-degree: 0%

---
# Manifesto della schermata di onboarding

Casella in entrata di acquisizione: `docs-captures/<YYYY-MM-DD>/`

Directory di output: `help/assets/guide-onboarding-agent/`

Acquisisci solo punti di controllo che aiutano materialmente l’utente a prendere una decisione o a verificare lo stato.

Non è necessario che i nomi dei file Source corrispondano ai nomi dei file finali. L’abilità mappa le schermate in base allo stato visibile dell’interfaccia utente, conserva i file non elaborati e crea copie bonificate utilizzando i nomi seguenti.

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
- Testo alternativo: `Actions — Onboarding Agent generating recommendations`

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
