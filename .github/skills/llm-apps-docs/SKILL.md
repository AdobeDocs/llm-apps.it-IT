---
name: llm-apps-docs
description: Crea, rivedi e convalida la documentazione delle app Adobe LLM, applica gli standard editoriali e acquisisci le schermate correnti dell’interfaccia utente tramite l’automazione del browser. Utilizza per articoli, navigazione, schermate e bundle di revisione offline di llm-apps.en.
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '1346'
ht-degree: 0%
---

# Documentazione delle app LLM

Crea una documentazione pubblica verificabile e orientata alle attività per le app Adobe LLM.

Questa abilità Copilot locale del repository è attiva in `.github/skills/llm-apps-docs/`.

## Ordine del Source della verità

Utilizza l’interfaccia utente in esecuzione e l’ambiente specificato dall’utente. In caso contrario, imposta come predefinito
Produzione in `https://experience.adobe.com/llm-apps`.
Verificare le attestazioni di prodotto in questo ordine per tale ambiente:

1. L’interfaccia utente corrente, osservata tramite l’automazione del browser.
2. L’implementazione corrispondente dell’interfaccia utente e dell’API nell’area di lavoro.
3. L&#39;attuale SDK pubblico, runtime e boilerplate.
4. Documentazione pubblica esistente

Registra la data di acquisizione e l’ambiente di origine nel manifesto della schermata. Do not
presentare le osservazioni locali o di stage come comportamento di produzione verificato. Se l’interfaccia utente
implementazione e le linee guida pubblicate non sono d’accordo, segnalano la mancata corrispondenza e descrivono
solo il comportamento supportato dalle prove disponibili.

## Avvia ogni attività

1. Lettura di `help/main-toc/TOC.md`.
2. Leggi l’articolo di destinazione e gli articoli direttamente correlati.
3. Classifica il contenuto utilizzando [content-model.md](content-model.md).
4. Identifica le etichette dell’interfaccia utente, gli URL, i comandi e i contratti che richiedono la verifica.
5. Mantenere l’esempio end-to-end e la terminologia coerenti tra le pagine.
6. Quando l’attività necessita della verifica dell’interfaccia utente o delle schermate, controlla l’interfaccia utente in esecuzione e acquisisci autonomamente le immagini. Chiedi all&#39;utente di autenticare o approvare le modifiche alle risorse, non di assemblare uno screenshot pack.

## Regole di authoring

- Guidare gli utenti alle prime **impostazioni infrastruttura**, quindi **azioni creazione**. La procedura guidata Crea-app imposta solo l&#39;infrastruttura; **[!UICONTROL Genera azioni automaticamente]** e **[!UICONTROL Genera le mie azioni]** avviano la generazione di azioni basate su sito Web successivamente, dalla pagina dell&#39;app. Utilizza **Adobe managed infrastructure** per l&#39;opzione con etichetta **[!UICONTROL Gestiamola]** nell&#39;interfaccia utente.
- Organizza la navigazione tra percorsi di utenti e risultati, non tra gli argomenti relativi all’implementazione.
- All&#39;inizio di ogni guida, utilizzare **In questa guida:** seguito da una o due frasi in linguaggio semplice che illustrano l&#39;attività e il passaggio successivo. Preferisci &quot;Scopri come...&quot; sulle promesse del futuro. Non utilizzare etichette **Percorso:** o elenchi di passaggi separati da freccia.
- Non utilizzare nomi in codice interni (ad esempio, &quot;Agente di onboarding&quot;) nei documenti rivolti al cliente; tale funzionalità non viene mai esposta nell’interfaccia utente del prodotto. Descrivilo genericamente (ad esempio, &quot;la piattaforma&quot;) e utilizza la copia corrente esatta dell&#39;interfaccia utente, ad esempio **[!UICONTROL Genera le mie azioni]** per i controlli.
- [!DNL Adobe LLM Apps] è indipendente dalla piattaforma: il server MCP funziona con qualsiasi piattaforma LLM supportata, non solo [!DNL ChatGPT]. Non formulare affermazioni generali o illustrative come se [!DNL ChatGPT] fosse l&#39;unica destinazione (ad esempio, preferire &quot;una piattaforma LLM supportata come [!DNL ChatGPT]&quot; rispetto a &quot;ChatGPT&quot; da sola). Assegna esplicitamente un nome a [!DNL ChatGPT] solo nel contenuto autentico e al momento specifico di [!DNL ChatGPT]: la [Guida di prova in ChatGPT](/help/guides/test-in-chatgpt.md) dedicata, i collegamenti incrociati diretti/i passaggi procedurali e il contenuto di riferimento o risoluzione dei problemi specifico di [!DNL ChatGPT].
- Spiega un concetto tecnico quando l’utente lo incontra per la prima volta; collega a un concetto più approfondito o materiale di riferimento.
- Mantieni i tutorial lineari, guide pratiche incentrate sulle attività e pagine di riferimento basate sui fatti.
- Includere solo le informazioni necessarie al lettore per l&#39;attività corrente; preferire frasi brevi e dirette.
- Utilizza un’app di rappresentanza in tutto il percorso.
- Distinguere lo scaffold generato dall’integrazione pronta per la produzione.
- Evita nomi di lavoratori interni, campi del database, ticket di implementazione e dettagli instabili della pipeline.
- Non duplicare le tabelle dei campi nelle guide; collega a riferimento.
- Mantenere il frontmatter e le direttive di Experience League: `[!DNL]`, ``, `[!IMPORTANT]`, `[!NOTE]` e `[!TIP]`.
- Utilizzare collegamenti interni relativi alla directory principale: `/help/...`.
- Utilizza la maiuscola/minuscola per titoli e intestazioni, a meno che l’etichetta del prodotto non richieda diversamente.
- Utilizza un testo alternativo immagine descrittivo che spiega lo schermo e lo stato.

## Standard editoriali

- Rivolgersi direttamente al lettore e utilizzare le istruzioni obbligatorie: &quot;Seleziona Salva&quot;, non &quot;Puoi selezionare Salva&quot;.
- Posiziona l’obiettivo prima dell’azione quando lo scopo richiede una spiegazione: &quot;Per aggiungere un parametro, seleziona Aggiungi&quot;. Evita clausole di scopo ridondanti.
- Preferisci la voce attiva e il presente teso. Denomina l’attore quando è importante: &quot;La piattaforma genera l’handler&quot;.
- Sostituisci i riferimenti ambigui come &quot;questo&quot;, &quot;questi&quot; o &quot;esso&quot; con un nome specifico quando il riferimento non è chiaro.
- Tieni un&#39;idea principale per frase. Abbreviare le frasi lunghe e dividere le clausole incorporate quando il dettaglio aggiuntivo oscura l&#39;attività. Mantenere le condizioni e gli avvisi necessari.
- Ridurre le catene preposizionali e le frasi dense dei sostantivi. Preferisci &quot;configurare le variabili app per Stage&quot; piuttosto che &quot;eseguire la configurazione delle variabili per l’app in Stage&quot;.
- Utilizza parole semplici invece di idiomi, linguaggio figurativo o frasi non necessarie. Spiegare termini tecnici sconosciuti quando il lettore li incontra per la prima volta.
- Controllare l&#39;ordine delle parole, la struttura della frase, le parole mancanti o duplicate, la punteggiatura e l&#39;iniziale maiuscola. Mantieni le voci elenco in parallelo dal punto di vista grammaticale.
- Corrispondenza con i nomi ufficiali dei prodotti e le maiuscole: Adobe LLM Apps, Adobe I/O Runtime, Edge Delivery Services, GitHub, ChatGPT, Claude e Model Context Protocol (MCP). Mantenere le etichette dell’interfaccia utente e l’ortografia del codice esatte anche quando differiscono dalle convenzioni di prosa.
- Utilizzare un linguaggio inclusivo e rispettoso. Evita stereotipi, supposizioni di genere, metafore stigmatizzanti e termini che presuppongono che ogni lettore abbia le stesse abilità o esperienze. Utilizza termini come &quot;inserisco nell&#39;elenco Consentiti di&quot; e &quot;inserisco nell&#39;elenco Bloccati di&quot; se accurati; non rinominare API letterali, rami, comandi o identificatori di codice per una preferenza editoriale.
- Mantieni i verbi modali quando esprimono opzioni, funzionalità, autorizzazioni, condizioni, incertezza o varianti di host reali. Non trasformare una stima in garanzia o raccomandazione in un requisito.
- Durante un passaggio editoriale, mantieni le etichette dell’interfaccia utente, i comandi, gli esempi di codice, gli identificatori, i contratti tecnici e la copia approvata da PM.
- Utilizza i risultati automatizzati come segnali di revisione, non sostituzioni obbligatorie. Individua la frase contrassegnata, correggi il problema effettivo o mantieni una formulazione significativa con un motivo. Una categoria di grammatica generica o un punteggio aggregato non sono sufficienti per identificare un errore specifico. Non richiedere un punteggio migliorato senza eseguire lo stesso valutatore.

## Documentazione approvata da PM

In `help/overview/overview.md`, queste sezioni sono approvate dal PM:

- **Operazioni possibili con le app LLM**
- **Perché le app LLM sono importanti**

Mantenere le intestazioni, i punti elenco, il testo, l&#39;ordine e le attestazioni per intero.
Non accorciarle, riscriverle, riorganizzarle o rimuoverle durante la documentazione generale
aggiornamenti. Modificare una sezione solo quando l’utente lo richiede esplicitamente e
conferma che la nuova copia è approvata tramite PM.

## Requisiti di sicurezza

- Non includere mai credenziali, token, URL privati, dati personali, nomi host interni o identificatori cliente.
- Mostra i segreti caricati dalla configurazione gestita, mai hardcoded.
- Richiedi HTTPS per i servizi esterni.
- Convalidare le risposte di input esterne e a monte.
- Eseguire il rendering dei valori esterni con API DOM sicure. Non è consigliabile interpolarli in `innerHTML`.
- Consiglia le autorizzazioni meno privilegiate per app GitHub, API, CSP, CORS e browser.
- Utilizza errori sicuri rivolti all’utente ed evita di registrare dati sensibili.

## Flusso di lavoro per schermate

Acquisisci le schermate con l’automazione del browser disponibile. Segui
[screenshot.md](screenshots.md) per la configurazione del browser, l&#39;autenticazione, la navigazione sicura,
controlli di stabilità, risanamento e revisione delle immagini. Utilizza
[screenshot-manifest.md](screenshot-manifest.md) per i checkpoint e i nomi di file correnti.

1. Apri l’interfaccia utente in esecuzione richiesta e sospendi l’autenticazione dell’utente.
2. Riutilizzare uno staffaggio della documentazione approvato ed esaminare gli stati dell’interfaccia utente richiesti.
3. Acquisire screenshot stabili nella directory `docs-captures/<run-id>/` ignorata. Non attendere un pacchetto di acquisizione creato dall&#39;utente.
4. Ritagliare o mascherare le copie sicure per `help/assets/`, quindi esaminare visivamente ogni immagine finale.
5. Fai corrispondere l’articolo, le etichette dell’interfaccia utente esatte e il testo alt allo stato osservato.
6. Convalida collegamenti e risorse e controlla il sito di revisione sottoposto a rendering.

Chiedi conferma prima di creare app o archivi, di iniziare la generazione, di convalidare una
archivio che può ricevere file standard, salvare le impostazioni, unire i file generati
richiedi, distribuisci, elimina o modifica l’accesso. Mantieni autenticazione e
consenso privilegiato con l’utente. Non ispezionare o esportare mai credenziali, token,
cookie o l’archiviazione del browser e non eseguire mai un flusso di acquisizione mutante senza approvazione.

Se l’automazione, l’accesso o uno stato richiesto non è disponibile, segnala gli
bloccante. Non creare uno screenshot, riutilizzare in silenzio un&#39;immagine obsoleta o presentare
verifica solo all’origine come nuova acquisizione dell’interfaccia utente. Mantieni le acquisizioni sorgente esistenti.

## Creare un archivio di revisione

Genera un sito HTML offline condivisibile e un archivio ZIP:

```bash
node .github/skills/llm-apps-docs/scripts/build_review_bundle.mjs
```

La build viene scritta accanto all’archivio, non al suo interno. Include solo
articoli pubblicati e risorse bonificate, converte le direttive Experience League
per la revisione offline e nota che il suo stile non è l’esperienza finale
Rendering in lega.

## Convalida

Esegui:

```bash
python3 .github/skills/llm-apps-docs/scripts/validate_docs.py
```

Correggi tutti gli articoli interni mancanti, le risorse mancanti, il percorso relativo alla radice non valido e il campo frontmatter mancante prima della consegna.

Controlla anche:

- Le etichette dell’interfaccia utente e le schermate corrispondono all’ambiente sorgente registrato.
- Gli esempi di script e URL di widget concordano tra guide e riferimenti.
- I comandi corrispondono alla boilerplate corrente.
- Le nuove pagine sono collegate dal sommario a meno che non siano contrassegnate intenzionalmente come `hidefromtoc: true`.
- Il flusso di lavoro di convalida degli articoli di Adobe, se disponibile, viene trasmesso.

## Riferimenti di supporto

- [Modello di contenuto e terminologia](content-model.md)
- [Procedura di screenshot guidata dall&#39;agente](screenshots.md)
- [Manifesto della schermata](screenshot-manifest.md)
