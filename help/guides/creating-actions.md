---
title: Creare azioni
description: Genera azioni di Adobe LLM App da un sito Web pubblico, segui l’avanzamento della generazione, rivedi il lavoro generato o scegli la creazione manuale delle azioni.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '938'
ht-degree: 0%
---

# Creare azioni {#creating-actions}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Le azioni sono le funzionalità offerte dall’app all’interno di una conversazione LLM, ad esempio la ricerca di prodotti, il controllo della disponibilità o la prenotazione di un servizio. L&#39;handler di ogni azione esegue l&#39;operazione e un widget facoltativo presenta il risultato.

Dopo la [configurazione dell&#39;infrastruttura](/help/guides/create-app.md), scegli come creare azioni dalla pagina dell&#39;app. La generazione automatica è disponibile sia con l&#39;infrastruttura gestita di Adobe che con i propri archivi.

**In questa guida:** scopri come generare azioni dal tuo sito Web o crearle autonomamente. Per la generazione automatica, avvia il processo e controlla i risultati prima di distribuire e testare l’app.

## Scegli come creare le azioni

Apri l’app. Se Adobe sta ancora preparando la propria infrastruttura, attendi il completamento della configurazione.

Nella sezione **[!UICONTROL Azioni]**, **[!UICONTROL Come desideri che vengano create le azioni?]** offre due opzioni:

| Scelta | Utilizzala quando |
|---|---|
| **[!UICONTROL Genera azioni automaticamente]** | Desideri che la piattaforma analizzi il tuo sito web e generi azioni suggerite, codice del gestore, test e widget. Questo è il punto di partenza consigliato. |
| **[!UICONTROL Io stesso creerò le azioni]** | Desideri definire ogni azione e fornirne l’implementazione autonomamente. |

La scelta dell’infrastruttura nella procedura guidata di creazione dell’app non comporta la selezione automatica di nessuna di queste opzioni.

## Generare azioni da un sito Web {#generate-from-website}

1. Seleziona **[!UICONTROL Genera azioni automaticamente]**.
2. In **[!UICONTROL Il tuo sito Web]**, immetti un sito Web HTTPS pubblico che rappresenti i prodotti, i servizi o le attività dei clienti del tuo marchio. Questo esempio utilizza `https://frescopa.coffee/`.
3. Seleziona **[!UICONTROL Genera le mie azioni]**.

![Azioni - generazione automatica selezionata con il sito Web pubblico immesso prima dell&#39;avvio](/help/assets/guide-creating-actions/build-choice.png)

Utilizza un sito web che sei autorizzato ad analizzare. Non fornire un sito che esponga informazioni confidenziali o soggette al controllo degli accessi.

Selezionando **[!UICONTROL Genera le mie azioni]** inizia la generazione; l&#39;immissione del solo URL non ne fa parte. Non è necessario ricreare l’app o riaprire la procedura guidata dell’infrastruttura.

### Progresso della generazione

Nella pagina dell&#39;app sono visualizzate **[!UICONTROL azioni generate]**. Seleziona **[!UICONTROL Vai alle azioni]** oppure apri **[!UICONTROL Azioni]** dalla navigazione per seguire l&#39;avanzamento.

L’interfaccia utente stima fino a 30 minuti per la generazione. Lascia la pagina e torna in un secondo momento, se necessario.

![Azioni: la generazione è in esecuzione, con un collegamento per seguire l&#39;avanzamento nella pagina Azioni](/help/assets/guide-creating-actions/generating.png)

Durante la generazione, la piattaforma:

1. Analizza il sito web e identifica le attività dei clienti per l’app.
2. Crea i metadati delle azioni consigliati, incluse le descrizioni e i parametri di input.
3. Genera handler e test lato server nell&#39;archivio azioni.
4. Genera widget nell&#39;archivio EDS.
5. Prepara le azioni generate per la revisione.

Mentre la piattaforma analizza il sito Web, nella pagina Azioni vengono visualizzate **[!UICONTROL Informazioni sulle azioni per la conversazione]**. Le azioni suggerite vengono visualizzate quando la piattaforma le rileva; è possibile che il codice e le anteprime dei widget vengano ancora generati. Attendi il termine della generazione prima di rivedere ogni azione.

>[!IMPORTANT]
>
>I gestori generati utilizzano inizialmente dati di esempio rappresentativi. L’analisi del sito web non collega automaticamente l’app alle API di produzione, all’inventario live, ai servizi di pagamento o ai sistemi di prenotazione.

Se la generazione segnala un errore, leggere l&#39;errore visibile e consultare le [istruzioni per la risoluzione dei problemi](/help/reference/troubleshooting.md). Non approvare un&#39;azione semplicemente per ignorare un errore di generazione.

## Esamina le azioni generate {#review-generated-actions}

Esamina ogni azione suggerita prima della distribuzione. Conferma che rappresenta un’attività utile e che il comportamento generato corrisponde a quello che intendi fare con l’app.

![Azioni: le azioni Frescopa generate sono pronte per la revisione e non sono state distribuite](/help/assets/guide-creating-actions/actions-ready-for-review.png)

1. Nella pagina **[!UICONTROL Azioni]**, seleziona **[!UICONTROL Rivedi]** per un&#39;azione pronta.
2. Rivedi la descrizione dell’azione, i parametri di input, le annotazioni, la visibilità e il gestore generato.
3. Rivedi la configurazione del widget e l&#39;anteprima, se l&#39;azione ha un widget.
4. Esamina il codice e i test generati, comprese eventuali richieste pull collegate. Scopri cosa è il comportamento di esempio e cosa deve essere sostituito con integrazioni reali.
5. Quando l&#39;azione è pronta, selezionare **[!UICONTROL Contrassegna come revisionato]**.
6. Torna all’elenco delle azioni e ripeti per le azioni rimanenti.

L&#39;editor delle azioni salvate separa **[!UICONTROL Dettagli widget LLM]**, **[!UICONTROL Dettagli azione]** e **[!UICONTROL Analytics]**. Rivedi le richieste pull di widget e gestore nelle rispettive schede.

![Azione generata: schede dell&#39;editor corrente e Contrassegna come revisionato con il collegamento dell&#39;archivio nascosto](/help/assets/guide-creating-actions/generated-action-review.png)

Se si seleziona **[!UICONTROL Contrassegna come revisionato]**, le richieste pull generate dall&#39;azione verranno unite se non rimangono bloccanti di unione. Se una richiesta di pull aperta blocca la revisione, risolvi il blocco e riprova. Non approvare un’implementazione con errori di generazione del codice o di unione non risolti.

Dopo aver esaminato tutte le azioni generate, nella pagina Azioni viene visualizzato **[!UICONTROL Tutte le azioni sono state riviste!]**. Per tornare all&#39;app, selezionare **[!UICONTROL Vai alla pagina dell&#39;app]**.

La revisione non è implementazione. Le azioni non sono disponibili tramite il server MCP dell’app finché non distribuisci l’app.

## Creare azioni autonomamente {#manual-actions}

Se preferisci non generare azioni da un sito web:

1. Nella scelta per la creazione di azioni della pagina dell&#39;app, seleziona **[!UICONTROL Le azioni verranno create io stesso]**.
2. Seleziona **[!UICONTROL Creerò le mie azioni]**.
3. Nella pagina Azioni selezionare **[!UICONTROL Crea azione]**.
4. Segui [Crea un&#39;azione da zero](/help/guides/create-action.md) per definire i metadati, implementare un gestore e connettere un widget, se necessario.

L&#39;infrastruttura rimane la stessa. La scelta della creazione manuale non crea codice del gestore o non collega automaticamente i sistemi aziendali.

Aggiungi azioni manuali dopo aver esaminato le azioni generate automaticamente in base alle esigenze.

## Distribuzione e test

Dopo aver esaminato il lavoro generato:

1. [Distribuisci l&#39;app](/help/guides/deploy-your-app.md) in **[!UICONTROL Stage]**.
2. Testarlo in [ChatGPT](/help/guides/test-in-chatgpt.md) o [Claude](/help/guides/test-in-claude.md), utilizzando l&#39;URL del server MCP di staging.
3. I prompt di test che corrispondono a ogni azione e i prompt non correlati che non richiedono tale azione. Controlla sia la risposta testuale che le interazioni widget.

Prima di rendere l&#39;app disponibile ai clienti, [personalizza i gestori](/help/guides/customize-handler.md) per connettere le API e [personalizzare i widget](/help/guides/widgets.md). Esegui il test dell’app in Stage per verificare che funzioni come previsto. Distribuisci in produzione solo dopo il completamento del test.
