---
title: Riferimento autenticazione
description: Definizioni dei campi, requisiti dei token, endpoint di individuazione, API del gestore e comportamento della piattaforma LLM per l’autenticazione degli utenti finali nelle app Adobe LLM.
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '1260'
ht-degree: 2%
---

# Riferimento autenticazione {#authentication-reference}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Utilizzare questa pagina per cercare campi e contratti di autenticazione. Per il percorso di installazione, vedere [Autenticare gli utenti finali con il proprio provider di identità](/help/guides/authentication.md).

## Impostazioni di autenticazione {#authentication-settings}

Trovato in **[!UICONTROL Impostazioni]** > **[!UICONTROL Autenticazione]**. Ogni campo viene archiviato per ogni ambiente: il selettore **[!UICONTROL Workspace]** seleziona quello che si sta modificando e il salvataggio non influisce mai sull&#39;altro.

| Campo | Obbligatorio | Descrizione |
|-------|----------|-------------|
| **[!UICONTROL Workspace]** | — | Ambiente a cui si applicano queste impostazioni: **[!UICONTROL Fase]** o **[!UICONTROL Produzione]** |
| **[!UICONTROL Abilita autenticazione]** | — | Interruttore principale Quando è disattivata, ogni azione è pubblica indipendentemente dalla modalità di autenticazione |
| **[!UICONTROL Emittente]** | Sì | L&#39;URL dell&#39;autorità di certificazione del provider di identità e l&#39;attestazione `iss` prevista. Deve essere HTTPS. Pubblicato anche come server di autorizzazione dell&#39;app. Un provider di identità per app |
| **[!UICONTROL Ambiti supportati]** | No | Il set completo di ambiti che le azioni di questa app potrebbero richiedere. Pubblicato su piattaforme LLM come ambiti supportati dell’app |
| **[!UICONTROL URI JWKS]** | No | Avanzato. L’URL HTTPS del set di chiavi di firma. Necessario solo se diverso da quello pubblicizzato dai metadati del server di autorizzazione |

### Regole di convalida

| Regola | Effetto |
|------|--------|
| **[!UICONTROL Emittente]** è vuoto mentre **[!UICONTROL Abilita autenticazione]** è attivo | Il salvataggio è bloccato |
| **[!UICONTROL Emittente]** o **[!UICONTROL JWKS URI]** non è un URL HTTPS | Il salvataggio è bloccato |
| Un&#39;azione richiede un ambito mancante da **[!UICONTROL Ambiti supportati]** | Il salvataggio è bloccato finché non aggiungi l’ambito o lo rimuovi dall’azione |
| Ambito rimosso da **[!UICONTROL Ambiti supportati]** | Viene rimosso da ogni azione che lo richiedeva, immediatamente, senza attendere un salvataggio |
| **[!UICONTROL Gli ambiti supportati]** sono vuoti | Non è possibile concedere alcun ambito, pertanto qualsiasi ambito già presente in un&#39;azione viene rimosso. In questo caso non viene visualizzata alcuna avvertenza |
| `offline_access` è elencato in **[!UICONTROL Ambiti supportati]** o in un&#39;azione | Rimosso quando l’app viene distribuita, a prescindere dalla combinazione di maiuscole e minuscole o dallo spazio vuoto circostante, in modo che la pagina delle impostazioni possa mostrare un ambito non disponibile per l’app distribuita. `offline_access` richiede un token di aggiornamento dal server di autorizzazione anziché concedere l&#39;accesso all&#39;app, pertanto non è un ambito pubblicizzato dall&#39;app. Non è necessario elencarla: la piattaforma LLM lo richiede direttamente al server di autorizzazione |

Le barre finali su **[!UICONTROL Emittente]** sono normalizzate e il confronto `iss` tollera la differenza: un provider che emette sempre una barra finale viene comunque convalidato.

## Modalità di autenticazione {#auth-modes}

Impostato per azione in **[!UICONTROL Configurazione per azione]**.

| Modalità | Token richiesto | Il gestore riceve l’identità | Pubblicizzato sulla piattaforma come |
|------|----------------|---------------------------|-------------------------------|
| **[!UICONTROL Nessuno]** | No | Solo quando il chiamante fornisce un token valido | `noauth` |
| **[!UICONTROL Obbligatorio]** | Sì, con ogni ambito elencato | Sempre | `oauth2` |
| **[!UICONTROL Facoltativo]** | No | Quando è presente un token valido | `noauth` e `oauth2` |

Il gestore di un&#39;azione **[!UICONTROL Required]** non viene mai eseguito senza un token con ambito corretto valido. Il gestore di un&#39;azione **[!UICONTROL Optional]** è sempre in esecuzione e può richiedere l&#39;accesso con `extra.challengeAuth()`.

**[!UICONTROL Obbligatorio]** è pertanto l&#39;unica modalità che rifiuta i chiamanti non autenticati. Un&#39;app è completamente gestita solo quando ciascuna delle sue azioni è **[!UICONTROL Obbligatoria]**; una singola azione **[!UICONTROL Nessuna]** o **[!UICONTROL Facoltativa]** rende l&#39;app mista, perché le chiamate anonime vengono comunque eseguite correttamente per almeno un&#39;azione.

Le modalità di autenticazione sono attive solo quando è attiva l&#39;opzione **[!UICONTROL Abilita autenticazione]**. Le modifiche diventano effettive alla prossima distribuzione dell’app.

L’attivazione di tale opzione riscrive le modalità per azione:

| Modifica switch | Effetto sulle modalità per azione |
|---------------|----------------------------|
| Da spento a attivato | Ogni azione **[!UICONTROL None]** diventa **[!UICONTROL Required]**. Le azioni già **[!UICONTROL Richieste]** o **[!UICONTROL Facoltative]** mantengono la loro modalità |
| Attivato/disattivato | La modalità e gli ambiti di ogni azione vengono cancellati per tale ambiente. La configurazione non viene ripristinata se si riattiva l&#39;interruttore |

Il passaggio a **[!UICONTROL Workspace]** non riscrive mai le modalità e carica la configurazione salvata dell&#39;altro ambiente così com&#39;è.

**[!UICONTROL Abilita autenticazione]** su con ogni azione impostata su **[!UICONTROL Nessuno]** è una combinazione valida ma inerte: nessuna chiamata viene mai rifiutata, tuttavia l&#39;app pubblica ancora il server di autorizzazione per l&#39;individuazione. Disattiva l&#39;interruttore per rendere l&#39;app completamente pubblica.

Le modalità possono essere combinate liberamente in una sola app. Per informazioni sulle modalità di applicazione di ciascuna piattaforma, vedere [comportamento della piattaforma LLM](/help/reference/authentication-reference.md#platform-behavior).

## Requisiti del token {#token-requirements}

Il provider di identità deve rilasciare token di accesso che soddisfino tutte le condizioni seguenti. Un token che non supera un controllo viene considerato assente: il chiamante non è autenticato e un&#39;azione **[!UICONTROL Obbligatorio]** impedisce l&#39;accesso.

| Requisito | Dettagli |
|-------------|--------|
| Formato | JWT firmato. I token opachi non sono supportati |
| Algoritmo di firma | `RS256`, `RS384`, `RS512`, `ES256`, `ES384`, `ES512`, `PS256`, `PS384` o `PS512`. Gli algoritmi HMAC come `HS256` vengono rifiutati |
| `iss` | Deve corrispondere a **[!UICONTROL Emittente]** |
| `aud` | Deve contenere l&#39;identificatore di risorsa dell&#39;app, l&#39;URL del server MCP per tale ambiente |
| `exp` | Deve essere nel futuro |
| `scope` oppure `scp` | Stringa delimitata da spazi o matrice di stringhe. Fornisce gli ambiti verificati in base ai requisiti di ogni azione |
| `sub` | Identificatore utente letto dal gestore tramite `getAuthenticatedUser` |
| Trasporto | `Authorization: Bearer <token>` intestazione di richiesta |

Eventuali attestazioni flat aggiuntive incluse dal provider, ad esempio `tenant` o `email`, vengono passate al gestore. Gli oggetti nidificati vengono eliminati e i valori stringa lunghi vengono troncati.

## Individuazione provider di identità {#discovery}

L&#39;app pubblica i metadati delle risorse protette [RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728) in modo che le piattaforme LLM possano individuare il server di autorizzazione. Non puoi creare, ospitare o configurare nulla per esso.

È necessario fornire l&#39;individuazione da parte dell&#39;utente:

| Requisito | Dettagli |
|-------------|--------|
| Metadati del server di autorizzazione | L&#39;emittente deve fornire i propri metadati [RFC 8414](https://datatracker.ietf.org/doc/html/rfc8414) o l&#39;individuazione [!DNL OpenID Connect] nel percorso `/.well-known/`. L&#39;app la legge per individuare le chiavi di firma |
| Un emittente con un percorso | Il segmento noto precede il percorso, non lo segue. Un emittente in `https://auth.example.com/oauth2/default` fornisce i propri metadati in `https://auth.example.com/.well-known/oauth-authorization-server/oauth2/default` |
| Chiavi ospitate altrove | Imposta **[!UICONTROL URI JWKS]** quando le chiavi di firma non sono nel punto in cui i metadati li pubblicizzano |

## API di autenticazione gestore {#handler-auth-api}

Esportato da `@adobe/llm-apps-runtime`. Ogni helper accetta `extra`, il secondo argomento ricevuto dal gestore.

| Helper | Restituisce |
|--------|---------|
| `getAuthenticatedUser(extra)` | Attestazione `sub` dell&#39;utente connesso o `undefined` quando la chiamata non è autenticata |
| `hasScope(extra, scope)` | `true` quando il token del chiamante contiene `scope` |

Le informazioni sul token verificate non elaborate si trovano in `extra.authInfo`, che è `undefined` per una chiamata non autenticata.

| Proprietà | Descrizione |
|----------|-------------|
| `authInfo.token` | Il token Bearer non elaborato. Non registrarlo o restituirlo al client |
| `authInfo.clientId` | Attestazione `client_id` o `azp` oppure `unknown` |
| `authInfo.scopes` | Array di ambiti concessi |
| `authInfo.expiresAt` | Scadenza token, come attestazione `exp` |
| `authInfo.resource` | Identificatore di risorsa dell’app su cui è stato convalidato il token |
| `authInfo.extra` | `sub` più altre richieste di rimborso forfettarie incluse nel provider di identità |

`extra.challengeAuth(options)` è disponibile solo per **[!UICONTROL Azioni facoltative]**. Restituisce il risultato dal gestore per chiedere all&#39;utente di accedere invece di restituire il contenuto.

| Opzione | Descrizione |
|--------|-------------|
| `error` | Codice di errore [RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750) Bearer: `invalid_token`, `insufficient_scope` o `invalid_request`. Impostazione predefinita: `insufficient_scope` |
| `errorDescription` | Il messaggio mostrato all’utente. Impostazione predefinita: un prompt di accesso generico |
| `scope` | Ambiti delimitati da spazi da richiedere. Ometti per consentire alla piattaforma di tornare agli ambiti supportati dall’app |

>[!IMPORTANT]
>
>Imposta sempre `error` esplicitamente. Utilizzare `invalid_token` per un chiamante senza una sessione valida e `insufficient_scope` solo per un chiamante il cui token è valido ma non dispone di un ambito richiesto. Il valore viene trasmesso alla piattaforma LLM, che decide autonomamente come formulare il prompt visualizzato dall&#39;utente. Invia il codice che descrive accuratamente la condizione anziché quella di cui preferisci il prompt.

## Comportamento della piattaforma LLM {#platform-behavior}

Il supporto per l’autenticazione di singole azioni varia in base alla piattaforma. Configurare nello stesso modo per entrambi; la differenza è ciò che l’utente sperimenta.

| Comportamento | [!DNL ChatGPT] | [!DNL Claude] |
|----------|----------------|---------------|
| Granularità | Per azione | Per connettore |
| Autenticazione mista, in cui l’app non è completamente gestita | Supportato. Solo le azioni **[!UICONTROL Richieste]** richiedono l&#39;accesso | Non supportato. L’intero connettore richiede l’accesso, incluse le azioni non collegate |
| Configurazione del connettore | Imposta **[!UICONTROL Autenticazione]** su **[!UICONTROL Nessuna autenticazione]** quando ogni azione è **[!UICONTROL Nessuna]**, **[!UICONTROL OAuth]** quando ogni azione è **[!UICONTROL Obbligatoria]**, **[!UICONTROL Mista]** in caso contrario | Nessuna scelta di autenticazione da effettuare; l&#39;accesso inizia il **[!UICONTROL Connect]** |
| Nuova autenticazione | Richiesto nella conversazione quando viene chiamata un’azione gestita | Richiesto per il connettore |


## Correlato

- [Autenticazione degli utenti finali con il proprio provider di identità](/help/guides/authentication.md)
- [Campi azione e widget](/help/reference/reference-docs.md)
- [Risoluzione di problemi](/help/reference/troubleshooting.md#authentication)
