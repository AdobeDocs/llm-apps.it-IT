---
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '1418'
ht-degree: 1%
---
# Manifesto della schermata

Acquisire questi stati con l&#39;automazione del browser utilizzando
[screenshot.md](screenshots.md). Seleziona i punti di controllo pertinenti per l’articolo;
questo manifesto non autorizza la creazione, la generazione, l&#39;approvazione o
distribuzione.

Le clip non elaborate rimangono in `docs-captures/<run-id>/`. Pubblica solo visivamente recensito,
PNG bonificati in `help/assets/`. Mantenere stabili i nomi dei file sottostanti quando
aggiornamento delle immagini.

## Configurazione dell’infrastruttura

Articolo: `/help/guides/create-app.md`

Directory di output: `help/assets/guide-infrastructure/`

| Nome file | Stato e contesto utile | Testo alternativo consigliato |
|---|---|---|
| `setup-choice.png` | Introduzione a entrambe le opzioni di infrastruttura visibili | Creare l’app LLM: scegli l’infrastruttura gestita di Adobe o i tuoi archivi |
| `app-details.png` | Nome app, programma AEM, area di analisi e avviso di impostazione immutabile | Dettagli app: inserisci un nome, seleziona un programma AEM e scegli un’area di analisi |
| `action-repository.png` | Percorso proprio dell’archivio con GitHub selezionato e istruzioni di connessione all’archivio | Configurazione dell’infrastruttura delle azioni: connettere un archivio delle azioni vuoto o compatibile |
| `provisioning.png` | Configurazione gestita di Adobe approvata in corso | Configurazione dell’app: Adobe sta preparando l’infrastruttura |

Escludi dettagli account, ID organizzazione, nomi di programmi reali, archivio
identificatori e ID delle app. Utilizzare campi vuoti o un nome di staffaggio neutro dove
possibile. La convalida dell’archivio può inizializzare il codice, quindi ottieni l’approvazione prima
acquisizione di un risultato di convalida.

## Creare azioni e rivedere il lavoro generato

Articolo: `/help/guides/creating-actions.md`

Directory di output: `help/assets/guide-creating-actions/`

| Nome file | Stato e contesto utile | Testo alternativo consigliato |
|---|---|---|
| `build-choice.png` | Azioni di compilazione selezionate automaticamente, è stato inserito un sito Web pubblico e un controllo di avvio stabile, se incluso | Azioni: scegli la generazione automatica e immetti un sito web pubblico |
| `generating.png` | Esecuzione della generazione, stima del tempo e azioni Vai a | Azioni: la generazione è in esecuzione |
| `actions-ready-for-review.png` | Fissare i nomi delle azioni, i badge di revisione e i controlli di revisione | Azioni: le azioni generate sono pronte per la revisione e non distribuite |
| `generated-action-review.png` | Dettagli del widget LLM, dettagli dell’azione, Analytics, un avviso di richiesta di pull generato e Contrassegna come rivisto | Azione generata: esamina il widget e il gestore prima dell’approvazione |

Mascherare l’archivio privato e richiamare i collegamenti delle richieste. Ritaglia i dati personali generati o
i dettagli di contatto quando l’articolo necessita solo dello stato di revisione. Non selezionare
**Contrassegna come revisionato** senza approvazione.

Acquisisci lo stato revisionato di tutte le azioni solo quando l’approvazione è nell’ambito e il
lo stato esiste veramente. Non approvare il lavoro generato per soddisfare uno screenshot
elenco di controllo.

## Creare un’azione da zero

Articolo: `/help/guides/create-action.md`

Directory di output: `help/assets/guide-create-action/`

| Nome file | Stato e contesto utile | Testo alternativo consigliato |
|---|---|---|
| `action-basic-info.png` | Cerca prodotti, il relativo identificatore derivato, una descrizione utile e annotazioni di sola lettura | Crea nuova azione: informazioni di base e annotazioni |
| `action-analytics-user-intent.png` | Raccogli le finalità dell’utente e le istruzioni di riepilogo anonime predefinite | Crea nuova azione - Rivedi raccolta intento utente |
| `widget-metadata.png` | Tipo di blocco Edge Delivery Services e origine sicura della sandbox di esempio | Crea nuova azione: tipo di widget e dominio sandbox |
| `widget-template-urls.png` | Esempio di URL di script e widget | Crea nuova azione: script e URL widget |
| `widget-permissions.png` | Autorizzazioni browser facoltative, con autorizzazioni non utilizzate disattivate | Crea nuova azione - Autorizzazioni browser |
| `widget-csp.png` | Controlli del dominio URI di base, di risorsa, connessione, frame e reindirizzamento | Crea nuova azione: domini dei criteri di sicurezza dei contenuti |

La finestra di dialogo per la creazione contiene **schede Azione** e **Metadati widget**. L’editor salvato
ha **Dettagli widget LLM**, **Dettagli azione** e **Schede Analytics**. Acquisire
layout utilizzato dalla routine. Compila i campi di esempio senza salvare, quindi
elimina le modifiche.

## Autenticazione

Articolo: `/help/guides/authentication.md`

Directory di output: `help/assets/guide-authentication/`

| Nome file | Stato e contesto utile | Testo alternativo consigliato |
|---|---|---|
| `auth-core-settings.png` | Fase, autenticazione abilitata, `https://auth.example.com`, `catalog:read`, `profile:read` e impostazioni avanzate | Autenticazione: abilita l’autenticazione e completa le impostazioni principali |
| `auth-per-action.png` | Azioni di staffaggio che mostrano Nessuno, Obbligatorio, Facoltativo e un ambito assegnato | Autenticazione: scegli una modalità di autenticazione e gli ambiti per ogni azione |

Usa le impostazioni non salvate e poi eliminale. Verificare che l&#39;autenticazione salvata
rimane invariato. Il passaggio dell’identificatore di risorsa riutilizza
`help/assets/guide-onboarding-agent/app-mcp-url.png`.

Se l’interfaccia utente mostra un avviso in conflitto con il runtime o il
comportamento della piattaforma, documenta la discrepanza. È appropriato un ritaglio di sola tabella
quando si insegnano le modalità, ma non nascondere un avviso rilevante senza spiegarlo.

## Variabili app

Articolo: `/help/guides/app-variables.md`

Directory di output: `help/assets/guide-app-variables/`

Utilizza `GREETING_PREFIX` con `Good day` come valore di esempio e `Howdy` come
valore di sostituzione. Non acquisire mai un valore di configurazione reale.

| Nome file | Stato e contesto utile | Testo alternativo consigliato |
|---|---|---|
| `variables-empty.png` | Area di lavoro di staging vuota, selettore Workspace e Aggiungi | Variabili e segreti - Area di lavoro di staging vuota |
| `add-variable-dialog.png` | Variabile GREETING_PREFIX non salvata e avviso di supporto segreto corrente | Aggiungi variabile o segreto - immetti GREETING_PREFIX e Good day |
| `variable-added.png` | Variabile di staffaggio approvata con controlli di nome, tipo, valore, marca temporale e riga | Variabili e segreti - GREETING_PREFIX nell’area di lavoro dello stage |
| `update-variable-dialog.png` | Valore corrente Giorno buono e nuovo valore non salvato | Aggiornamento di GREETING_PREFIX - cambia il giorno lavorativo in Howdy |
| `delete-variable-dialog.png` | Finestra di dialogo di conferma senza conferma dell’eliminazione | Elimina GREETING_PREFIX: controlla la conferma dell’eliminazione |

L’apertura di una finestra di dialogo non autorizza il salvataggio o l’eliminazione. Riutilizzare un elemento esistente sicuro
variabile di staffaggio o richiesta prima di crearne una. Mantieni un&#39;immagine precedente solo dopo
verifica che i campi e i controlli corrispondano ancora all’interfaccia utente corrente.

## Distribuzione e test

Articolo: `/help/guides/deploy-your-app.md`

| Directory di output | Nome file | Stato e contesto utile |
|---|---|---|
| `help/assets/guide-onboarding-agent/` | `deploy-stage.png` | Fase dell’ambiente di destinazione e controllo Distribuzione, prima dell’avvio |
| `help/assets/guide-onboarding-agent/` | `app-mcp-url.png` | Test corrente: verifica della scheda dell’app, delle etichette dell’ambiente e copia URL; maschera ogni URL di runtime |
| `help/assets/guide-deploy/` | `deployment-history.png` | Colonne di ambiente, stato e data di distribuzione senza identificatori di app |

Se consentito, utilizza una distribuzione esistente di sola lettura per la scheda di test e la cronologia.
Non avviare una distribuzione per ottenere tali stati, a meno che l&#39;utente non lo approvi.
Acquisire le finestre di dialogo di distribuzione in esecuzione o riuscite solo quando lo stato è entrambi
pertinente e autorizzato.

Per `/help/guides/test-in-chatgpt.md`, il conservato
`help/assets/guide-onboarding-agent/chatgpt-generated-app.png` illustra un prodotto
risposta. Non verifica l’interfaccia utente di configurazione corrente. Nuova configurazione di ChatGPT o Claude
le schermate richiedono l&#39;accesso autenticato corrente; in caso contrario, utilizza il fornitore ufficiale
e omettere le immagini non verificate.

## Configurazione di Analytics

Articolo: `/help/guides/analytics-setup.md`

Directory di output: `help/assets/guide-analytics/`

| Nome file | Stato e contesto utile | Testo alternativo consigliato |
|---|---|---|
| `analytics-settings.png` | Impostazioni app, scheda Analytics, Abilita Analytics su, ID Datastream vuoto, Requisiti espansi, Salvataggio delle modifiche disabilitato | Impostazioni app, scheda Analytics: abilitazione di Analytics attivata, campo ID stream di dati vuoto ed elenco esteso dei requisiti |
| `datastream-analytics-service.png` | Raccolta dati, Aggiungi servizio, Adobe Analytics abilitato con un ID suite di rapporti fittizio | Aggiungi servizio: Adobe Analytics abilitato con un ID suite di rapporti di esempio |
| `datastream-platform-service.png` | Raccolta dati, servizio Adobe Experience Platform con solo il set di dati evento selezionato | Servizio Adobe Experience Platform: il set di dati dell’evento selezionato e le altre opzioni cancellate |
| `datastream-services.png` | Dettagli dello stream di dati con entrambi i servizi abilitati, un ID dello stream di dati fittizio e lo schema di mappatura | Dettagli dello stream di dati: entrambi i servizi sono abilitati, con l’ID dello stream di dati di esempio e lo schema di mappatura |

Non acquisire mai un ID dello stream di dati reale. Non selezionare **Salva modifiche**: dopo un salvataggio,
solo un amministratore può modificare lo stream di dati. Mostra Experience Platform, Adobe Analytics,
e Customer Journey Analytics solo dove viene visualizzata un&#39;impostazione specifica per
App LLM; collegamento ad Experience League per tutto il resto. Sostituisci organizzazione
nomi, ID suite di rapporti, ID stream di dati, ID set di dati, e-mail e nomi personali
con i seguenti valori fittizi: `example-org`, `demo.<suffix>` suite di rapporti,
`00000000-0000-0000-0000-000000000000`, `0000…0001`, `user@example.com` e
`Example User`.

## Provenienza della cattura esistente

L’esecuzione dell’acquisizione 2026-10-19 ha utilizzato l’interfaccia utente locale richiesta dall’utente, con interfaccia/API corrispondente
controlli di origine. Non stabilisce la parità di produzione.

- Acquisizioni non elaborate: `docs-captures/2026-10-19/` e la relativa sottodirectory `full-review/`.
- Le immagini pubbliche precedenti o sostituite rimangono nelle sottodirectory `retired/` locali.
- Lo staffaggio della documentazione ha raggiunto la fase di revisione delle azioni generate. Nessuna azione
approvazioni, unioni di richieste pull, distribuzioni o autenticazione/variabile salvata
le modifiche facevano parte dell’esecuzione dell’acquisizione.
- La riga delle variabili, l’aggiornamento e l’eliminazione delle immagini da un’esecuzione precedente sono stati mantenuti dopo
il controllo dei controlli rispetto all’implementazione corrente.
- Le schermate di configurazione di terze parti non sono state acquisite nuovamente. Le schermate della configurazione obsolete sono state
è stato rimosso invece di essere presentato come corrente.

Per ogni nuovo aggiornamento, aggiorna la data, l’ambiente di origine e gli stati verificati,
bonifica ed eventuali limiti di verifica rimanenti. Non copiare la vecchia provenienza
come prova di una nuova cattura.

### Rimozione dello switch dell’editor delle azioni, 2026-10-19

Ha aggiornato `guide-creating-actions/generated-action-review.png` dall&#39;esecuzione
nell’interfaccia utente locale dopo aver rimosso l’opzione Abilitato dell’editor. Visibilità della pagina dell’app
l&#39;interruttore rimane il comando di attivazione/disattivazione dell&#39;azione; le impostazioni di esposizione del modello e del widget
rimani nell’editor. Il collegamento dell’archivio è mascherato e la navigazione dell’account è
esclusi. Nessuna azione è stata salvata, approvata, attivata o distribuita durante l&#39;aggiornamento.
Acquisizione raw: `docs-captures/2026-10-19/remove-enabled/`.

### Impostazioni di Analytics, 2026-10-21

Aggiornato `guide-analytics/analytics-settings.png` dal file fornito dall&#39;utente
`Screenshot 2026-10-21 at 11.48.36.png`, non è un&#39;esecuzione basata su agenti. Origine
ambiente: non registrato e non visibile sull&#39;immagine; la parità di produzione non è
verificato. Revisione visiva confermata Abilitazione di Analytics attivata, campo ID dello stream di dati
è vuoto, l&#39;opzione Requisiti è espansa e l&#39;opzione Salva modifiche è disabilitata. L’immagine era
copiato senza modifiche; non sono visibili identificatori o dettagli dell’account.

### Schermate dello stream di dati, 2026-10-19

Aggiunto `guide-analytics/datastream-analytics-service.png`,
`datastream-platform-service.png` e `datastream-services.png` da esistenti
acquisizioni interne di Data Collection (29/07/2026 e 10/19/2026). ID suite di rapporti
e l&#39;ID dello stream di dati sono stati sostituiti con sovrapposizioni di testo fittizie (`demo.aem.llm` e
`00000000-0000-0000-0000-000000000000`); il font di sovrapposizione è San Francisco, quindi
differisce leggermente dal font del prodotto. L’immagine del servizio piattaforma non era necessaria
modifiche. L’immagine del servizio Analytics mostra ancora il puntatore del mouse dalla
acquisizione originale. Un passaggio OCR sulle immagini finali non ha trovato identificatori originali.
Sostituiscili con nuove acquisizioni che utilizzano valori fittizi digitati nell’interfaccia utente quando
l’articolo viene quindi aggiornato.
