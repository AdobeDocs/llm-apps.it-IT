---
source-git-commit: 14d94a68dabddd2f2ef2cef65c51d31645d4a958
workflow-type: tm+mt
source-wordcount: '715'
ht-degree: 0%
---
# Modello di contenuto e terminologia

## Tipi di contenuto

### Esercitazione

Insegna a un nuovo utente attraverso un percorso completo e di successo.

- Indicare il risultato e i prerequisiti.
- Utilizza un’app di esempio e una sequenza.
- Spiega solo i concetti necessari a ogni passaggio.
- Termina con un risultato di lavoro e cancella i passaggi successivi.

Primo percorso eseguito: `help/guides/create-app.md` (configurazione infrastruttura), quindi `help/guides/creating-actions.md` (generazione azioni e revisione).

### Concetto

Spiega come le parti si relazionano senza diventare una routine o un catalogo di campi.

- Concentrati sui modelli mentali e sui limiti di proprietà.
- Spiegare il ruolo di ogni parte, il modo in cui l&#39;utente la percepisce e dove apportare modifiche.
- Usa le guide tecniche o i riferimenti collegati per creare strumenti, percorsi di file, chiamate al protocollo e contratti di codice, ma non la panoramica generale.
- Utilizza un piccolo diagramma per comprendere meglio.
- Mantenere utili i diagrammi esistenti durante la semplificazione di una pagina; semplificare le etichette anziché rimuovere la spiegazione visiva.
- Collegamento a tutorial, guide pratiche e riferimenti.

### Guida pratica

Aiuta un utente informato a completare un’attività.

- Inizia con il risultato desiderato.
- Includi solo i prerequisiti specifici per l’attività.
- Preferisci un percorso consigliato.
- Collegamento a un riferimento per campi esaustivi.

Esempi: crea un’azione da zero, personalizza un widget, inserisci un progetto EDS, distribuisci e verifica.

### Riferimento

Fornisce informazioni fattuali che gli utenti consultano mentre lavorano.

- Organizza in base al prodotto o alla superficie di codice.
- Definisci con precisione ogni campo, contratto, comando, limite e stato.
- Evita la narrazione dell’esercitazione e esempi ripetuti.

### Risoluzione di problemi

Inizia da un sintomo osservabile.

- Descrivere le cause probabili.
- Attribuisci passaggi diagnostici sicuri.
- Evita di chiedere agli utenti di visualizzare le credenziali o i registri sensibili.

## Terminologia canonica

- **App Adobe LLM** — nome completo del prodotto alla prima menzione.
- **App LLM**: un&#39;app gestita dal prodotto.
- **Infrastruttura gestita da Adobe**: infrastruttura creata e gestita da Adobe; selezionare **Gestiamola** nella procedura guidata di creazione-app. Non descrivere questa opzione come di sola prova o non presentare dichiarazioni non confermate sull’idoneità alla produzione.
- **Verrà configurato da solo**: opzione della procedura guidata per la connessione dei propri archivi, non è necessario scrivere azioni manualmente. Il percorso predefinito inizia con due archivi vuoti inizializzati dalla piattaforma; i progetti esistenti compatibili sono facoltativi.
- **Genera azioni automaticamente**: scelta consigliata per la creazione di azioni nella pagina dell&#39;app dopo la configurazione dell&#39;infrastruttura.
- **Genera le mie azioni** — pulsante che avvia la generazione di azioni basate sul sito Web.
- **Azione**: funzionalità esposta alla piattaforma LLM.
- **Metadati azione**: nome, descrizione, schema, annotazioni, visibilità e configurazione widget archiviati dalle app LLM.
- **Visibilità**: interruttore di attivazione/disattivazione dell&#39;azione della pagina dell&#39;app. Controlla se la distribuzione successiva include l’azione. L&#39;editor delle azioni non dispone di uno switch Enabled separato; il modello e le impostazioni di esposizione dei widget hanno uno scopo diverso.
- **Gestore azioni**: funzione lato server nell&#39;archivio del gestore.
- **Identificatore codice**: identificatore di azione immutabile utilizzato nelle chiamate MCP, distinto dal nome visualizzato. I nomi degli handler e delle cartelle di blocchi generati utilizzano un formato sillabato; il runtime risolve anche i caratteri di sottolineatura nell’identificatore in trattini nei percorsi degli handler.
- **Archivio gestore**: archivio contenente gestori e test, selezionato durante la **configurazione dell&#39;infrastruttura azioni** durante la connessione dei propri archivi.
- **Archivio EDS**: archivio contenente blocchi di widget e contenuto.
- **Widget** — risposta visiva di cui è stato eseguito il rendering nella piattaforma LLM.
- **`structuredContent`**: dati strutturati dei risultati delle azioni utilizzati dal widget. Non descriverlo come privato, invisibile al modello o privo di token; fa parte del risultato MCP distribuito all’host.
- **URL server MCP**: endpoint distribuito registrato con una piattaforma LLM.
- **Plug-in ChatGPT**: l&#39;integrazione ChatGPT creata dall&#39;URL di un server MCP.
- **Gestione temporanea** e **Produzione**: ambienti di distribuzione.

Evita il passaggio tra &quot;strumento&quot; e &quot;azione&quot; nella prosa rivolta all&#39;utente, a meno che non venga spiegato un dettaglio del protocollo MCP.

Il prodotto è indipendente dalla piattaforma: il suo server MCP funziona con qualsiasi piattaforma LLM supportata, non solo ChatGPT. Utilizza &quot;una piattaforma LLM supportata, ad esempio ChatGPT&quot; (o simile) per affermazioni generali o illustrative. Denomina ChatGPT da solo solo in contenuti che sono effettivamente specifici di ChatGPT oggi — la guida Test in ChatGPT, i suoi collegamenti incrociati diretti e il riferimento specifico di ChatGPT o il contenuto per la risoluzione dei problemi.

## Sequenza di lettura consigliata

1. Panoramica.
2. Crea un’app utilizzando l’infrastruttura gestita di Adobe o i tuoi archivi.
3. Avvia la generazione di azioni basate su siti web dalla pagina dell’app e controlla le azioni generate.
4. Distribuire in staging e test in una piattaforma LLM supportata.
5. Personalizza i gestori e i widget generati.
6. Distribuisci l’app personalizzata in Produzione.

La creazione di un&#39;azione da zero e la creazione di un progetto EDS sono rami avanzati, non il percorso predefinito di prima esecuzione.

La guida BYO EDS è attualmente nascosta nel sommario con `hidefromtoc: true`.
Mantenere tale stato a meno che l&#39;utente non richieda di ripristinare la voce di navigazione.
