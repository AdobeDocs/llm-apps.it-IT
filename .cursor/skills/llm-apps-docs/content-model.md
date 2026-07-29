---
source-git-commit: eec74b87457bc852d7a8dd0e46c2a4385a93ae0a
workflow-type: tm+mt
source-wordcount: '387'
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

Esercitazione principale: `help/guides/create-app.md`.

### Concetto

Spiega come le parti si relazionano senza diventare una routine o un catalogo di campi.

- Concentrati sui modelli mentali e sui limiti di proprietà.
- Utilizza un piccolo diagramma per comprendere meglio.
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
- **Agente di onboarding**: funzionalità che crea lo scaffold iniziale.
- **Genera la mia app** — Sezione interfaccia utente nella finestra di dialogo di creazione dell&#39;app.
- **Crea automaticamente l&#39;app**. Etichetta di casella di controllo esatta.
- **Azione**: funzionalità esposta alla piattaforma LLM.
- **Metadati azione**: nome, descrizione, schema, annotazioni, visibilità e configurazione widget archiviati dalle app LLM.
- **Gestore azioni**: funzione lato server nell&#39;archivio del gestore.
- **Archivio gestore**: archivio contenente gestori e test. Utilizzare l&#39;etichetta dell&#39;interfaccia utente **Boilerplate Repository** solo per descrivere il controllo.
- **Archivio EDS**: archivio contenente blocchi di widget e contenuto.
- **Widget** — risposta visiva di cui è stato eseguito il rendering nella piattaforma LLM.
- **URL server MCP**: endpoint distribuito registrato con una piattaforma LLM.
- **Plug-in ChatGPT**: l&#39;integrazione ChatGPT creata dall&#39;URL di un server MCP.
- **Gestione temporanea** e **Produzione**: ambienti di distribuzione.

Evita il passaggio tra &quot;strumento&quot; e &quot;azione&quot; nella prosa rivolta all&#39;utente, a meno che non venga spiegato un dettaglio del protocollo MCP.

## Percorso di lettura consigliato

1. Panoramica e prerequisiti.
2. Crea un’app con l’agente di onboarding.
3. Esamina le azioni generate.
4. Distribuisci nell’ambiente di staging e verifica il plug-in ChatGPT.
5. Personalizza i gestori e i widget generati.
6. Distribuisci l’app personalizzata in Produzione.

La creazione di un&#39;azione da zero e la creazione di un progetto EDS sono rami avanzati, non il percorso predefinito di prima esecuzione.
