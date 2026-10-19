---
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '1109'
ht-degree: 0%
---
# Procedura di screenshot guidata dall&#39;agente

Acquisisci tu stesso l’interfaccia utente in esecuzione con l’automazione del browser. L’utente fornisce
e approva le modifiche alle risorse; l&#39;agente naviga, acquisisce,
bonifica e integra le immagini. Non richiedere un assemblaggio manuale
screenshot pack prima di iniziare.

## Scegli l’ambiente

Utilizza l’URL fornito dall’utente. In caso contrario, utilizza Produzione:

`https://experience.adobe.com/llm-apps`

Registra la data di acquisizione, l’ambiente e lo stato osservato in
[manifesto-schermata.md](screenshot-manifest.md). Un&#39;acquisizione locale o Stage non
verifica il comportamento di produzione. Confrontare l’interfaccia utente con l’implementazione corrispondente e
segnalare i conflitti anziché presentare un flusso non verificato come rilasciato.

## Apri e autentica il browser

1. Verifica che l’interfaccia utente richiesta sia in esecuzione. Non avviare un server duplicato.
2. Utilizza l’automazione del browser disponibile. Riutilizzare una sessione del browser solo quando
la proprietà e l&#39;ambito di accesso sono chiari.
3. Se l’automazione non può essere collegata, apri un browser separato sotto il controllo dell’agente.
Per Chrome, utilizza un profilo di sessione dedicato all’esterno dell’archivio e associa
debug remoto su `127.0.0.1` su una porta locale non utilizzata. Non copiare mai il
profilo personale del browser o esporre la porta di debug alla rete.
4. Verifica che il browser sia in esecuzione e che il relativo endpoint di automazione risponda.
5. Apri l’app e sospendi l’autenticazione dell’utente. L’utente gestisce Adobe,
Credenziali GitHub e piattaforma LLM, MFA, passkey, captchas, organizzazione
selezione e consenso privilegiato.
6. Riprendi dopo che l’utente ha confermato l’autenticazione. Con Playwright, collegare
   `chromium.connect_over_cdp()` quando si utilizza una sessione di debug controllata di Chrome.
7. Individua il frame dell’app in base all’origine o al contenuto visibile. Unified Shell può posizionare
l’app all’interno di un iframe; non presumere un indice di frame fisso.

Non leggere, stampare, esportare o confermare credenziali, token, cookie o browser
archiviazione. Non chiamare `storage_state()` o controllare le intestazioni di autenticazione. Lascia che
il browser gestisce l’accesso senza estrarre il relativo stato.

## Ispezionare e navigare in modo sicuro

- Preferisci localizzatori semantici come `get_by_role()` e `get_by_label()`. Ispeziona
la struttura di accessibilità e il testo visibile prima di scegliere un controllo.
- Verifica le etichette dell’interfaccia utente esatte sullo schermo renderizzato. La finestra di dialogo per la creazione e
gli editor delle azioni salvate hanno schede diverse; non inferire una dall&#39;altra.
- Se possibile, riutilizza un’app approvata solo per la documentazione. Utilizza un file pubblico,
sito Web non sensibile e contenuto di staffaggi riconoscibili.
- Compila i moduli non salvati con valori di esempio solo dopo aver confermato che il modulo
non si salva automaticamente. Annulla o elimina le modifiche dopo l&#39;acquisizione, quindi verifica
le impostazioni salvate rimangono invariate.
- Chiedi conferma prima di creare app o archivi, avviare la generazione di azioni,
convalida di un archivio che può ricevere file standard, salvataggio delle impostazioni,
aggiunta o eliminazione di variabili, unione di richieste pull, distribuzione, eliminazione o
archiviazione delle risorse o modifica dell&#39;accesso all&#39;archivio.
- Considera **Contrassegna come revisionato** come una mutazione dell&#39;archivio perché può unire il pull
richieste. L’apertura della schermata di revisione non autorizza l’approvazione.
- Mantieni l’accesso all’app GitHub limitato agli archivi degli staffaggi. Richiesta
approvazione dell’amministratore quando richiesta.
- Non approvare azioni o distribuire un&#39;app solo per ottenere uno screenshot opzionale.
Acquisisci gli stati accessibili e segnala lo stato mancante.

Quando sono necessarie nuove risorse, conferma l’organizzazione di destinazione, il sito web,
opzione infrastruttura, programma AEM e nome dello staffaggio prima della creazione. Per
Infrastruttura gestita da Adobe, consente alla piattaforma di creare gli archivi. Per
percorso proprio dell’archivio, utilizza due archivi vuoti e ottieni l’approvazione prima di
la convalida li inizializza.

## Acquisire stati stabili dell’interfaccia utente

1. Scegli i punti di controllo pertinenti dal manifesto. Acquisire decisioni e
risultati verificabili, non tutti i clic.
2. Utilizza un riquadro di visualizzazione del desktop adatto al pannello pertinente, uno zoom al 100% e
tema prodotto predefinito.
3. Attendi che siano impostati il contenuto previsto, i font, i menu e il layout della finestra di dialogo.
Unified Shell ridimensiona l’iframe attorno alle finestre di dialogo, in modo che sia visibile solo l’intestazione
non dimostra che la regione di cattura è stabile.
4. Verificare che l&#39;area di destinazione rimanga nella stessa posizione e nelle stesse dimensioni. Attendi
caricamento di sovrapposizioni, animazioni di uscita, toast e transizioni disattivate su attivate
a meno che uno di questi stati non sia l&#39;oggetto dell&#39;immagine.
5. Allontana lo stato attivo dagli input e chiudi menu non correlati quando ciò non avviene
modificare lo stato documentato.
6. Acquisire la regione completa più piccola che include l&#39;attività e che è utile
contesto. Preferisci un pannello o una schermata di dialogo rispetto a un’immagine desktop intera.
7. Con Playwright, usa le schermate del localizzatore e `animations='disabled'`, più
maschere di tempo di acquisizione per i valori sensibili. Ispezionare il PNG risultante;
Il successo dell’API della schermata non dimostra che la regione è corretta.

Non modificare mai il DOM, le etichette di aggiornamento o l&#39;aspetto di un controllo per creare un
la schermata sembra pronta o corretta.

## Archiviare e ripulire le immagini

Salva ogni esecuzione nella directory Git ignorata:

```text
docs-captures/<run-id>/
```

Utilizza nomi di file descrittivi come `setup-choice.png`, `action-basic-info.png`,
e `generated-action-review.png`. Mantieni le acquisizioni di origine invariate. Mantieni raw
acquisisce e visualizza i profili di browser da commit e archivi di revisione.

Crea copie finali nelle directory di output nel manifesto:

- Ritaglia il riquadro del browser, la navigazione dell’account, le app non correlate e l’area di lavoro vuota.
- Escludi completamente i segreti dall’acquisizione, comprese le acquisizioni non elaborate.
- Mascherare nomi personali, avatar, identificatori organizzazione e installazione, privati
dettagli dell’archivio, spazi dei nomi di runtime e URL completi del server MCP. Preferisci solido
maschere neutre da sfocare, che possono lasciare valori recuperabili.
- Usa valori sicuri come `https://auth.example.com`, `catalog:read`,
  `GREETING_PREFIX` e `Good day` in moduli di esempio non salvati.
- Escludi i dettagli di contatto generati e altri dati di esempio dall’aspetto personale quando
tale contenuto non è necessario per il passaggio.
- Mantenere lo stato effettivo dell’interfaccia utente. Ritagliare o redigere i contenuti sensibili, non gli errori,
avvertenze o funzionalità mancanti che l’articolo deve spiegare.
- Riutilizzare i nomi di file pubblici stabili durante l&#39;aggiornamento di un punto di controllo esistente.

Non eliminare le acquisizioni di origine, i profili del browser o le risorse di staffaggio senza
approvazione. Non rimuovere un profilo browser attivo mentre il browser è in esecuzione.

## Revisione e integrazione

Per ogni immagine finale:

1. Apri l’immagine e ispezionala visivamente.
2. Verifica che il pannello, le etichette, lo stato e i relativi controlli siano visibili.
3. Verifica la presenza di testo ritagliato, aree vuote o errate, sovrapposizioni di caricamento, aree transitorie
menu e dettagli dell&#39;account.
4. Conferma che l’articolo descriva il flusso di lavoro osservato e che il testo alternativo
identifica lo schermo e lo stato.
5. Aggiorna il manifesto con l’ambiente di acquisizione, la data ed eventuali limiti per
verifica.
6. Esegui la convalida della documentazione e crea il sito di revisione utilizzando i comandi
tra `SKILL.md`.
7. Ispeziona l’articolo renderizzato e verifica che ogni immagine venga caricata in modo leggibile
dimensioni.

Per le piattaforme di terze parti, utilizza un browser autenticato solo all’interno del
ambito approvato. Se l’accesso non è disponibile, verifica le istruzioni rispetto a corrente
documentazione ufficiale del fornitore, ometti le schermate di configurazione non verificate e lo stato
che tali schermi non sono stati catturati. Non presentare una configurazione di terze parti precedente
immagine come corrente.

Se l’utente fornisce immagini, ispezionarle e verificarle prima di riutilizzarle. Essi
sono materiale di origine aggiuntivo, non un prerequisito per il flusso di lavoro di acquisizione.

## Blocchi dei rapporti

Sospendi per l’autenticazione, il consenso o una modifica della risorsa non approvata. Rapporto
errori di automazione, stati non disponibili, acquisizioni non sicure, convalida dell’archivio
errori, errori di generazione e mancate corrispondenze esplicite tra interfaccia utente e origine. Continua con
lavoro verificato, non mutante ove possibile; non inventare mai uno stato di successo o
sostituisci silenziosamente le prove mancanti con un’immagine obsoleta.
