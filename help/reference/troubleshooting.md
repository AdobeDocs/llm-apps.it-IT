---
title: Risoluzione dei problemi relativi alle app Adobe LLM
description: Risolvere i problemi comuni relativi a infrastruttura, generazione di azioni, gestore, widget, distribuzione, autenticazione e connessione alla piattaforma LLM.
source-git-commit: a48b4022979d9a13b4fc3aa870c4ee6375e8bc13
workflow-type: tm+mt
source-wordcount: '1350'
ht-degree: 0%
---

# Risoluzione di problemi {#troubleshooting}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Inizia con il sintomo osservabile. Durante la risoluzione dei problemi, non condividere credenziali, token, URL MCP privati o risultati sensibili del gestore.

## Creazione di app e generazione di azioni

| Sintomo | Cosa provare |
|---------|-------------|
| L&#39;installazione dell&#39;infrastruttura gestita Adobe non riesce | Lettura dell&#39;errore in **Installazione non riuscita**, quindi selezionare **Riprova**. Se la pagina non riesce a controllare l&#39;avanzamento, selezionare **Controlla di nuovo**; l&#39;installazione continua in background |
| Nessun programma AEM disponibile | Chiedi all’amministratore l’accesso al programma. Se il caricamento non è riuscito, selezionare **Riprova a caricare i programmi** |
| I nuovi archivi non vengono visualizzati | Utilizza **Connetti un altro archivio** per concedere l&#39;accesso all&#39;app GitHub per le app Adobe LLM, torna alla procedura guidata e seleziona **Aggiorna organizzazioni e archivi** |
| Convalida dell&#39;archivio azioni o widget non riuscita | Utilizza un progetto esistente compatibile o un archivio vuoto. Usa archivi separati per azioni e widget, quindi seleziona di nuovo **Convalida archivio** |
| Sincronizzazione codice AEM non rilevata | Installa AEM Code Sync per l&#39;archivio widget, aggiungi te stesso o te stessa come amministratore del sito nella pagina di conferma, torna alla procedura guidata e seleziona **Convalida installazione AEM Code Sync** |
| La creazione dell’app non ha generato azioni | La creazione di app imposta solo l’infrastruttura. Nella pagina dell&#39;app, seleziona **Genera automaticamente azioni**, immetti **Il tuo sito Web** e seleziona **Genera le mie azioni**. Vedi [Crea azioni](/help/guides/creating-actions.md) |
| Le azioni sono ancora in fase di generazione | L’interfaccia utente stima fino a 30 minuti per la generazione. Seguire l&#39;avanzamento nella pagina Azioni oppure uscire dalla pagina e tornare in un secondo momento |
| Generazione delle azioni segnala un errore | Verifica che il sito web sia pubblico tramite HTTPS e che l’accesso all’archivio sia ancora valido per i tuoi archivi. Se il problema persiste, contatta il team di Beta con il messaggio di errore visibile |

## Azioni e gestori

| Sintomo | Cosa provare |
|---------|-------------|
| Azione non richiamata | Abilita il plug-in o il connettore dell’app nella conversazione. Conferma **Esposizione al modello di intelligenza artificiale** nelle impostazioni delle azioni e l&#39;opzione **Visibilità** dell&#39;azione nella pagina dell&#39;app è attivata. Migliorare la descrizione e ridistribuire le modifiche ai metadati |
| Risposta vuota o di errore | Esegui `npm test`, quindi chiama il gestore con MCP Inspector o `curl`. Vedi [Sviluppo e test del gestore locale](/help/reference/development.md) |
| Il gestore funziona localmente, ma non dopo la distribuzione | Controllare l&#39;ultimo commit inviato, confermare la configurazione di runtime e verificare che l&#39;identificatore del codice azione corrisponda a `actions/<code-identifier>/index.js` |
| L&#39;azione generata non può essere contrassegnata come rivista | Generazione del gestore e del widget di conferma completata. Controlla le richieste pull generate per i conflitti di unione, ricarica l&#39;azione e seleziona **Contrassegna come revisionato** |

## Widget

| Sintomo | Cosa provare |
|---------|-------------|
| Widget non eseguito rendering | Verifica l’URL dello script, l’URL del widget, HTTPS, la pubblicazione EDS, i domini CSP e le intestazioni CORS |
| Il widget esegue il rendering ma non mostra dati | Chiamare il gestore con MCP Inspector e confrontare la relativa forma `structuredContent` con i campi letti da `bridge.toolResult` |
| Il widget funziona in anteprima diretta ma non in ChatGPT | L’anteprima diretta potrebbe utilizzare dati di esempio. Verifica il risultato del gestore distribuito e verifica che l’origine EDS sia consentita da CORS e CSP |
| La richiesta del browser è bloccata | Aggiungi solo l’origine richiesta al campo CSP corretto e ridistribuisci |
| L’editor intestazioni HTTP non può salvare la configurazione | Utilizza il [Servizio di configurazione AEM](https://aem.live/docs/config-service-setup) o chiedi all&#39;amministratore EDS di inizializzare la configurazione delle intestazioni del sito |

Non registrare valori `bridge.toolResult` completi quando possono contenere dati personali o sensibili.

## Distribuzione

| Sintomo | Cosa provare |
|---------|-------------|
| Distribuzione non riuscita durante **la preparazione** | Verifica che l’archivio del gestore sia collegato e che l’accesso a Adobe Developer Console sia ancora valido |
| Distribuzione non riuscita durante **Build app** | Eseguire `npm install`, `npm test` e `npm run build` localmente. Correggi gli errori di dipendenza, sintassi o test e invia le modifiche |
| La distribuzione riesce ma mancano le modifiche | Conferma che il commit previsto è stato inviato e ridistribuito nello stesso ambiente |
| L&#39;azione rimane **Non distribuita** | Ripeti la distribuzione dopo aver esaminato l’azione o averne modificato i metadati |

## Autenticazione {#authentication}

Applicabile quando **[!UICONTROL Abilita autenticazione]** è attivo. Consulta [Autenticazione degli utenti finali con il tuo provider di identità](/help/guides/authentication.md).

| Sintomo | Cosa provare |
|---------|-------------|
| Le azioni rimangono pubbliche dopo il salvataggio delle impostazioni | Distribuisci nuovamente l’app in tale ambiente. Le modifiche di autenticazione hanno effetto alla prossima distribuzione |
| Le impostazioni non vengono visualizzate correttamente dopo il passaggio a un altro ambiente | Conferma che il selettore **[!UICONTROL Workspace]** mostri l&#39;ambiente desiderato. **[!UICONTROL Stage]** e **[!UICONTROL Produzione]** sono configurati in modo indipendente |
| L’accesso non si avvia | Conferma di aver distribuito l&#39;app dopo l&#39;abilitazione dell&#39;autenticazione e che l&#39;azione utilizzi **[!UICONTROL Obbligatorio]**. Un&#39;azione **[!UICONTROL Facoltativa]** richiede l&#39;accesso solo quando il relativo gestore richiede l&#39;accesso |
| La piattaforma invia l’utente alla pagina di accesso errata | Verifica che **[!UICONTROL Emittente]** corrisponda esattamente all&#39;URL dell&#39;emittente del provider di identità e che sia raggiungibile tramite HTTPS pubblico |
| L’accesso ha esito positivo ma ogni chiamata viene ancora rifiutata | Conferma che il provider di identità emetta token il cui pubblico è l’URL del server MCP dell’app per tale ambiente e che il token è un JWT firmato con un algoritmo asimmetrico. Vedi [Requisiti del token](/help/reference/authentication-reference.md#token-requirements) |
| Il provider di identità non riceve mai traffico | Gli endpoint di individuazione, autorizzazione e token del provider devono essere raggiungibili tramite HTTPS pubblico. Verifica la presenza di un firewall, un WAF o un elenco Consentiti IP di fronte a esso: l’app può essere raggiungibile mentre il provider non lo è |
| Il provider di identità rifiuta di emettere un token per il pubblico richiesto | Alcuni provider rilasciano solo token per una risorsa registrata con loro. Conferma che l’URL del server MCP dell’app sia registrato come identificatore di risorsa nel provider di identità. |
| All&#39;accesso non è richiesto un ambito appena aggiunto | Le piattaforme LLM memorizzano nella cache i metadati pubblicati dell’app per alcuni minuti. Attendi, quindi riprova l’accesso |
| All&#39;utente viene richiesto di accedere nuovamente per un ambito | L’azione richiede un ambito che il token non contiene. Aggiungi l’ambito alla sovvenzione del client nel provider di identità o rimuovilo dall’azione |
| L&#39;utente riceve ripetute richieste di accesso | Un gestore che restituisce `extra.challengeAuth()` per ogni chiamata senza controllare nuovamente le richieste di identità del chiamante dopo l&#39;autenticazione. Richiedi autenticazione solo quando manca l’identità necessaria per l’azione |
| L&#39;accesso viene rifiutato prima che l&#39;utente raggiunga il provider di identità | L’URI di reindirizzamento inviato dalla piattaforma LLM non è registrato nel server di autorizzazione. Alcune piattaforme ne rilasciano una diversa per ciascun connettore, pertanto registra il valore mostrato nella schermata di configurazione del connettore |
| Il salvataggio è bloccato con un messaggio di ambito non supportato | Aggiungi l&#39;ambito a **[!UICONTROL Ambiti supportati]** o rimuovilo dall&#39;azione che lo richiede |
| Un&#39;azione pubblica richiede ancora l&#39;accesso in [!DNL Claude] | Verifica che il connettore utilizzi **[!UICONTROL Accedi quando necessario]**, non **[!UICONTROL Accedi ora]**. Per un’app implementata meno recente, aggiorna il runtime e ridistribuiscila. Un’app mista nel runtime corrente lascia le azioni pubbliche richiamabili in modo anonimo |
| Un&#39;azione **[!UICONTROL Facoltativa]** restituisce il testo di accesso ma non la richiesta di accesso Claude | `extra.challengeAuth()` è una richiesta di verifica del risultato dello strumento ChatGPT. Per un gate di accesso portatile, utilizza un&#39;azione **[!UICONTROL Obbligatorio]** in modo che il runtime restituisca la sfida HTTP prevista da Claude |
| L&#39;app richiede l&#39;accesso anche se ogni azione è **[!UICONTROL Nessuna]** | Disattivare **[!UICONTROL Abilita autenticazione]** e distribuire. Quando è attiva, l’app annuncia un server di autorizzazione anche quando non viene inviata alcuna azione |

Non incollare token di accesso, set di attestazioni o segreti client del provider di identità in una richiesta di supporto.

## Plug-in ChatGPT

| Sintomo | Cosa provare |
|---------|-------------|
| Il plug-in non viene visualizzato | Apri [chatgpt.com/plugins](https://chatgpt.com/plugins), verifica che l&#39;account o l&#39;area di lavoro consenta i server MCP personalizzati e installa o connetti il plug-in risultante |
| Creazione del plug-in non riuscita | Copia nuovamente l&#39;URL completo del server MCP HTTPS da **Verifica l&#39;app** e scegli il metodo di autenticazione corrispondente all&#39;app. Usa **Nessuna autenticazione** solo per un&#39;app pubblica, non come soluzione alternativa per OAuth non riuscito |
| Il plug-in si connette ma non può richiamare azioni | Conferma che il plug-in sia allegato alla chat, che le azioni siano esposte al modello e che sia distribuita la versione più recente |
| Il plug-in utilizza l’ambiente errato | Modifica o ricrea il plug-in con l’URL del server MCP di stage o produzione previsto |
| Il plug-in mostra ancora i metadati dell’azione obsoleti | Distribuisci le modifiche, seleziona **Aggiorna** nella connessione ChatGPT, quindi avvia una nuova conversazione |

Se il problema persiste, registra il nome dell’app, l’ambiente, il passaggio non riuscito, l’ora e il messaggio di errore visibile prima di contattare il team di Beta. Non includere segreti o dati sensibili dei clienti.
