---
title: Risoluzione dei problemi relativi alle app Adobe LLM
description: Risolvi i problemi comuni relativi a archivio, onboarding, gestore, widget, distribuzione e plug-in ChatGPT.
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '1144'
ht-degree: 0%
---

# Risoluzione di problemi {#troubleshooting}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Inizia con il sintomo che puoi vedere. Durante la risoluzione dei problemi, non condividere credenziali, token, URL MCP privati o risultati sensibili del gestore.

## Creazione e onboarding di app

| Sintomo | Cosa provare |
|---------|-------------|
| I nuovi archivi non vengono visualizzati | Seleziona **Gestisci repository su GitHub**, concedi all&#39;app GitHub delle app Adobe LLM l&#39;accesso a entrambi gli archivi, torna alla finestra di dialogo e aggiorna gli elenchi |
| L&#39;archivio EDS richiede AEM Code Sync | Installa AEM Code Sync per l’archivio EDS, quindi torna alla finestra di dialogo Crea app LLM |
| La convalida EDS indica che non si è amministratori | Seleziona **Apri AEM Live Admin**, aggiungi te stesso/a come amministratore per il sito EDS, quindi aggiorna l&#39;archivio |
| L’onboarding è ancora in fase di generazione | Attendere circa 15 minuti. Puoi uscire dalla pagina e tornare in un secondo momento |
| Report di onboarding non riusciti | Verifica che entrambi gli archivi siano accessibili e che il sito web sia pubblico tramite HTTPS, quindi contatta il team di Beta con il messaggio di errore visibile |

## Azioni e gestori

| Sintomo | Cosa provare |
|---------|-------------|
| Azione non richiamata | Allega il plug-in ChatGPT, conferma che **Esposizione al modello di IA** sia abilitato, migliora la descrizione dell&#39;azione e ridistribuisci le modifiche ai metadati |
| Risposta vuota o di errore | Esegui `npm test`, quindi chiama il gestore con MCP Inspector o `curl`. Vedi [Sviluppo e test del gestore locale](/help/reference/development.md) |
| Il gestore funziona localmente, ma non dopo la distribuzione | Conferma il push dell&#39;ultimo commit, la configurazione runtime è presente e l&#39;identificatore del codice azione corrisponde a `actions/<code-identifier>/index.js` |
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
| L’accesso non si avvia | Verificare che l&#39;app sia stata distribuita dopo l&#39;abilitazione dell&#39;autenticazione e che l&#39;azione che si sta chiamando sia impostata su **[!UICONTROL Obbligatorio]**. Un&#39;azione **[!UICONTROL Facoltativa]** richiede l&#39;accesso solo quando il relativo gestore richiede l&#39;accesso |
| La piattaforma invia l’utente alla pagina di accesso errata | Verifica che **[!UICONTROL Emittente]** corrisponda esattamente all&#39;URL dell&#39;emittente del provider di identità e che sia raggiungibile tramite HTTPS pubblico |
| L’accesso ha esito positivo ma ogni chiamata viene ancora rifiutata | Conferma che il provider di identità emetta token il cui pubblico è l’URL del server MCP dell’app per tale ambiente e che il token è un JWT firmato con un algoritmo asimmetrico. Vedi [Requisiti del token](/help/reference/authentication-reference.md#token-requirements) |
| Il provider di identità non riceve mai traffico | Gli endpoint di individuazione, autorizzazione e token del provider devono essere raggiungibili tramite HTTPS pubblico. Verifica la presenza di un firewall, un WAF o un elenco Consentiti IP di fronte a esso: l’app può essere raggiungibile mentre il provider non lo è |
| Il provider di identità rifiuta di emettere un token per il pubblico richiesto | Alcuni provider rilasciano solo token per una risorsa registrata con loro. Conferma che l’URL del server MCP dell’app sia registrato come identificatore di risorsa nel provider di identità. |
| All&#39;accesso non è richiesto un ambito appena aggiunto | Le piattaforme LLM memorizzano nella cache i metadati pubblicati dell’app per alcuni minuti. Attendi, quindi riprova l’accesso |
| All&#39;utente viene richiesto di accedere nuovamente per un ambito | L’azione richiede un ambito che il token non contiene. Aggiungi l’ambito alla sovvenzione del client nel provider di identità o rimuovilo dall’azione |
| All’utente viene richiesto di eseguire l’accesso ripetutamente, in modo | Un&#39;azione è impegnativa su ogni chiamata. Un gestore che restituisce `extra.challengeAuth()` senza prima verificare se il chiamante è già autenticato non può mai essere soddisfatto, perché l&#39;accesso produce nuovamente la stessa sfida. Sfida solo quando manca l’identità necessaria per l’azione |
| L&#39;accesso viene rifiutato prima che l&#39;utente raggiunga il provider di identità | L’URI di reindirizzamento inviato dalla piattaforma LLM non è registrato nel server di autorizzazione. Alcune piattaforme ne rilasciano una diversa per ciascun connettore, pertanto registra il valore mostrato nella schermata di configurazione del connettore |
| Il salvataggio è bloccato con un messaggio di ambito non supportato | Aggiungi l&#39;ambito a **[!UICONTROL Ambiti supportati]** o rimuovilo dall&#39;azione che lo richiede |
| Un&#39;azione impostata su **[!UICONTROL Nessuno]** richiede ancora l&#39;accesso | Previsto su [!DNL Claude], che esegue l&#39;autenticazione per connettore anziché per azione. |
| L&#39;app richiede l&#39;accesso anche se ogni azione è **[!UICONTROL Nessuna]** | Disattivare **[!UICONTROL Abilita autenticazione]** e distribuire. Quando è attiva, l’app annuncia un server di autorizzazione anche quando non viene inviata alcuna azione |

Non incollare token di accesso, set di attestazioni o segreti client del provider di identità in una richiesta di supporto.

## Plug-in ChatGPT

| Sintomo | Cosa provare |
|---------|-------------|
| Il plug-in non viene visualizzato | Attiva la modalità sviluppatore, apri [chatgpt.com/plugins](https://chatgpt.com/plugins), verifica che il plug-in esista e seleziona **Connetti** |
| Creazione del plug-in non riuscita | Conferma che la modalità sviluppatore è abilitata, copia di nuovo l&#39;URL del server MCP da **Verifica l&#39;app** e utilizza **URL del server** con **Nessuna autenticazione** |
| Il plug-in si connette ma non può richiamare azioni | Conferma che il plug-in sia allegato alla chat, che le azioni siano esposte al modello e che sia distribuita la versione più recente |
| Il plug-in utilizza l’ambiente errato | Modifica o ricrea il plug-in con l’URL del server MCP di stage o produzione previsto |

Se il problema persiste, registra il nome dell’app, l’ambiente, il passaggio non riuscito, l’ora e il messaggio di errore visibile prima di contattare il team di Beta. Non includere segreti o dati sensibili dei clienti.
