---
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '695'
ht-degree: 0%

---
# Procedura per lo screenshot di produzione

Utilizza questa procedura per acquisire screenshot reali della documentazione pubblica da:

`https://experience.adobe.com/#/@llmapps/llm-apps/`

Il flusso di lavoro preferito è l’acquisizione umana seguita dall’assunzione assistita dall’agente. L’utente decide quali stati di produzione sono rilevanti; l’abilità li organizza, li bonifica e li integra nella documentazione.

## Casella in entrata di acquisizione

Inserire ogni acquisizione eseguita in:

```text
docs-captures/<YYYY-MM-DD>/
```

Git ignora la directory. Le schermate non elaborate devono rimanere locali e non devono mai essere eseguite.

L’utente può utilizzare qualsiasi nome di file, ma i nomi ordinati semplificano la revisione:

```text
01-create-app.png
02-connect-github.png
03-onboarding-enabled.png
04-generating-actions.png
05-review-actions.png
```

Un elemento `capture-notes.md` facoltativo può descrivere gli stati mancanti, il comportamento insolito o l&#39;ordine previsto.

## Limiti di sicurezza

- L’utente immette le credenziali di Adobe, GitHub e della piattaforma LLM direttamente nel browser.
- Interrompi per MFA, passkey, captcha, selezione organizzazione e consenso privilegiato.
- Non leggere, stampare, salvare o eseguire il commit di token, cookie, archiviazione del browser o credenziali.
- Utilizza un sito web pubblico non sensibile e nuovi archivi solo documentazione.
- Concedi alle app GitHub l’accesso solo ai due archivi utilizzati dallo staffaggio.
- Chiedi prima di creare, distribuire, eliminare, archiviare o modificare l’accesso all’archivio.
- Non acquisire informazioni personali, ID organizzazione, ID di installazione dell’archivio, token o URL completi di runtime.

## Denominazione staffaggio

Utilizzare nomi che identificano chiaramente le risorse di documentazione monouso:

```text
App: LLM Apps Docs <YYYY-MM-DD>
Handler repo: llm-apps-docs-<YYYYMMDD>
EDS repo: llm-apps-docs-<YYYYMMDD>-eds
```

Prima di creare qualsiasi cosa, conferma l’organizzazione Adobe di destinazione, il proprietario di GitHub, il sito web pubblico e i nomi degli staffaggi con l’utente.

Crea entrambi gli archivi vuoti e privati. Non inizializzarli con un file README, una licenza o `.gitignore`.

## Impostazioni di acquisizione

- Utilizza un riquadro di visualizzazione del desktop sufficientemente grande da mostrare finestre di dialogo complete senza il riquadro di selezione del browser.
- Mantieni lo zoom al 100%.
- Utilizza il tema del prodotto predefinito a meno che l’articolo non insegni specificamente i temi.
- Acquisire l&#39;area completa più piccola contenente l&#39;attività e il contesto necessario.
- Evita cursori, menu aperti non correlati al passaggio, toast da azioni precedenti e spiner transitori a meno che lo stato del girante non sia documentato.
- Usa PNG.
- Mantenere i nomi dei file stabili; sostituire il contenuto dell&#39;immagine anziché rinominare i file durante gli aggiornamenti.

## Sequenza di acquisizione consigliata

L’utente deve acquisire gli stati pertinenti dal manifesto, tra cui:

1. Crea un’app prima che GitHub sia connesso.
2. Selezione dell’accesso all’archivio dell’app GitHub.
3. **Crea automaticamente la mia app** abilitata con entrambi gli archivi selezionati.
4. Creazione dell’app o avvio dell’agente di onboarding.
5. Azioni in fase di generazione.
6. Azioni generate pronte per la revisione.
7. Metadati, gestore e widget di un’azione rappresentativa.
8. Revisione per azione e stato tutte le azioni riviste.
9. Distribuzione di staging completata.
10. La registrazione dell’app e un risultato rappresentativo si traducono nella piattaforma LLM.

Acquisisci schermate aggiuntive quando spiegano una decisione, un errore o un prerequisito reale. Non acquisire ogni clic.

## Flusso di lavoro di acquisizione abilità

Quando l’utente chiede di aggiornare la documentazione da una cartella di acquisizione:

1. Confermare la directory di acquisizione esatta.
2. Inventario di tutti i file PNG, JPEG e WebP ed esame visivo di ogni immagine.
3. Creare una mappatura dai file di origine alle voci in `screenshot-manifest.md`.
4. Confronta le etichette dell’interfaccia utente visibili e la sequenza con l’esercitazione esistente.
5. Rapporto:
   - stati richiesti mancanti;
   - immagini duplicate o ridondanti;
   - ordinamento ambiguo;
   - schermate non aggiornate;
   - informazioni sensibili;
   - Comportamento di produzione in conflitto con la documentazione.
6. Non modificare le acquisizioni di origine.
7. Per ogni immagine accettata, creare una copia bonificata con il nome file del manifesto stabile in `help/assets/guide-onboarding-agent/`.
8. Ritaglia solo quando l’interfaccia utente circostante non aggiunge alcun contesto utile.
9. Valori sensibili alle maschere. Se non è possibile applicare una maschera di sicurezza, chiedere di rieseguire la cattura.
10. Aggiorna l’articolo e il testo alt in modo che corrispondano al flusso di lavoro acquisito.
11. Eseguire la convalida del collegamento e della risorsa.
12. Lascia la cartella di acquisizione sul posto finché l’utente non chiede esplicitamente di rimuoverla.

## Acquisizione guidata da agente opzionale

Se l’utente chiede all’agente di guidare il browser, utilizza gli stessi limiti di manifesto e di sicurezza. Sospendi per autenticazione, consenso privilegiato, modifiche all’archivio, creazione di app, distribuzione e pulizia. Non eseguire mai questo flusso di mutazione di produzione in modalità automatica.

## Revisione immagine

Per ogni immagine:

- Abbina a una voce del manifesto.
- Verifica la copia dell’interfaccia utente in base all’articolo.
- Ritaglia la navigazione dell’account quando non è necessaria.
- Maschera nomi personali, avatar, identificatori organizzazione, identificatori di installazione dell’archivio, spazi dei nomi di runtime e app non correlate.
- Conferma che non siano visibili i dettagli di riempimento automatico, e-mail, token di accesso o archivio privato del browser.
- Scrivi testo alternativo che identifica sia lo schermo che lo stato.

## Quando interrompere

Interrompi e segnala un bloccante quando:

- La produzione non corrisponde al flusso di lavoro documentato.
- Il flusso di revisione è sostanzialmente diverso dalla documentazione pubblicata.
- La convalida dell’archivio rifiuta il flusso dell’archivio vuoto previsto.
- La pipeline di onboarding non riesce.
- Un&#39;azione con privilegi richiede un utente o un amministratore.
- Una schermata non può essere resa sicura senza nascondere le informazioni essenziali per il passaggio.
