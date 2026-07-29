---
name: llm-apps-docs
description: Crea, aggiorna, rivedi e convalida la documentazione pubblica e le schermate delle app Adobe LLM. Utilizza ogni volta che modifichi gli articoli llm-apps.en, il relativo sommario di Experience League, la guida all’agente di onboarding, i documenti dei widget EDS, la guida di preparazione alla produzione o le schermate della documentazione.
source-git-commit: ca0d8f49a295e6465f2e9b20809e69436bfa93d5
workflow-type: tm+mt
source-wordcount: '716'
ht-degree: 0%

---


# Documentazione delle app LLM

Crea una documentazione pubblica verificabile e orientata alle attività per le app Adobe LLM.

## Ordine del Source della verità

Verificare le attestazioni di prodotto nell&#39;ordine seguente:

1. Interfaccia utente di produzione corrente in `https://experience.adobe.com/#/@llmapps/llm-apps/`
2. Implementazione API e interfaccia utente corrente disponibile nell’area di lavoro
3. Attuale SDK pubblico e comportamento boilerplate
4. Documentazione pubblica esistente

Se Produzione è in conflitto con l&#39;origine o i piani, documentare Produzione e segnalare la mancata corrispondenza. Non pubblicare un flusso di lavoro imminente come attualmente disponibile.

## Avvia ogni attività

1. Lettura di `help/main-toc/TOC.md`.
2. Leggi l’articolo di destinazione e gli articoli direttamente correlati.
3. Classifica il contenuto utilizzando [content-model.md](content-model.md).
4. Identifica le etichette dell’interfaccia utente, gli URL, i comandi e i contratti che richiedono la verifica.
5. Mantenere l’esempio end-to-end e la terminologia coerenti tra le pagine.

## Regole di authoring

- Guida gli utenti alle prime esperienze con Onboarding Agent.
- Organizza la navigazione tra percorsi di utenti e risultati, non tra gli argomenti relativi all’implementazione.
- Inserisci la sequenza di percorso vicino all’inizio di ogni guida e specifica il passaggio successivo condiviso.
- Utilizza **Agente di onboarding** per le funzionalità del prodotto e copia esatta dell&#39;interfaccia utente, ad esempio **[!UICONTROL Crea automaticamente la mia app]** per i controlli.
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

Per le immagini nuove o aggiornate, segui [screenshot.md](screenshots.md) e [screenshot-manifest.md](screenshot-manifest.md).

Il flusso di lavoro predefinito utilizza un pacchetto di acquisizione di produzione creato dall’utente:

1. Cercare le schermate in `docs-captures/<run-id>/` o utilizzare la cartella fornita dall&#39;utente.
2. Inventario e controllo visivo di ogni file PNG, JPEG e WebP; non fare affidamento solo sul nome del file.
3. Abbina le schermate agli stati del manifesto utilizzando il contenuto visibile dell’interfaccia utente.
4. Segnala acquisizioni mancanti, duplicate, ambigue, non aggiornate o non sicure prima di modificare la documentazione.
5. Mantieni le acquisizioni di origine invariate.
6. Creare copie finali bonificate utilizzando i nomi file del manifesto stabile in `help/assets/`.
7. Aggiorna il tutorial e le guide correlate in modo che corrispondano al flusso di lavoro di produzione effettivamente acquisito.
8. Aggiungi testo alternativo accurato ed esegui la convalida della documentazione.

L’acquisizione del browser guidata dall’agente rimane un fallback facoltativo. Non memorizzare lo stato o le credenziali del browser e non eseguire l’acquisizione di screenshot con modifica della produzione in CI.

Quando ti viene chiesto di &quot;aggiornare i documenti dalle schermate&quot;:

- Considera come origine la cartella di acquisizione selezionata in modo esplicito più recente.
- Chiedi solo quando il flusso dell’app o la mappatura della schermata è genuinamente ambiguo.
- Non eseguire mai il commit delle cartelle di acquisizione non elaborate.
- Non eliminare o modificare mai le acquisizioni di origine senza un&#39;approvazione esplicita.
- Se non è possibile rimuovere le informazioni riservate senza oscurare l&#39;attività, richiedere un recupero sicuro.

## Creare un archivio di revisione

Genera un sito HTML offline condivisibile e un archivio ZIP:

```bash
node .cursor/skills/llm-apps-docs/scripts/build_review_bundle.mjs
```

La build viene scritta accanto all’archivio, non al suo interno. Include solo
articoli pubblicati e risorse bonificate, converte le direttive Experience League
per la revisione offline e nota che il suo stile non è l’esperienza finale
Rendering in lega.

## Convalida

Esegui:

```bash
python3 .cursor/skills/llm-apps-docs/scripts/validate_docs.py
```

Correggi tutti gli articoli interni mancanti, le risorse mancanti, il percorso relativo alla radice non valido e il campo frontmatter mancante prima della consegna.

Controlla anche:

- Le etichette dell’interfaccia utente e le schermate corrispondono a Produzione.
- Gli esempi di script e URL di widget concordano tra guide e riferimenti.
- I comandi corrispondono alla boilerplate corrente.
- Le nuove pagine sono collegate dal sommario.
- Il flusso di lavoro di convalida degli articoli di Adobe, se disponibile, viene trasmesso.

## Riferimenti di supporto

- [Modello di contenuto e terminologia](content-model.md)
- [Procedura per lo screenshot di produzione](screenshots.md)
- [Manifesto della schermata](screenshot-manifest.md)
