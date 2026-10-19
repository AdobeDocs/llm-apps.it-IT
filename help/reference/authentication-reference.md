---
title: Riferimento autenticazione
description: Definizioni dei campi, requisiti dei token, endpoint di individuazione, API del gestore e comportamento della piattaforma LLM per l’autenticazione degli utenti finali nelle app Adobe LLM.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1428'
ht-degree: 2%
---

# Riferimento autenticazione {#authentication-reference}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Utilizzare questa pagina per cercare campi e contratti di autenticazione. Per le istruzioni di installazione, vedere [Autenticare gli utenti finali con il proprio provider di identità](/help/guides/authentication.md).

## Impostazioni di autenticazione {#authentication-settings}

Apri **[!UICONTROL Impostazioni]** > **[!UICONTROL Autenticazione]**. Ogni ambiente memorizza le proprie impostazioni. Utilizza il selettore **[!UICONTROL Workspace]** per selezionare l&#39;ambiente da modificare; il salvataggio delle impostazioni non influisce mai sull&#39;altro ambiente.

| Campo | Obbligatorio | Descrizione |
|-------|----------|-------------|
| **[!UICONTROL Workspace]** | — | Ambiente per le impostazioni di autenticazione: **[!UICONTROL Fase]** o **[!UICONTROL Produzione]** |
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

Le barre finali dell&#39;**[!UICONTROL emittente]** sono normalizzate. Il confronto di `iss` tollera la differenza, pertanto i token di un provider che include sempre una barra finale vengono comunque convalidati.

## Modalità di autenticazione {#auth-modes}

Impostato per azione in **[!UICONTROL Configurazione per azione]**.

| Modalità | Token richiesto | Il gestore riceve l’identità | Pubblicizzato sulla piattaforma come |
|------|----------------|---------------------------|-------------------------------|
| **[!UICONTROL Nessuno]** | No | Solo quando il chiamante fornisce un token valido | `noauth` |
| **[!UICONTROL Obbligatorio]** | Sì, con ogni ambito elencato | Sempre | `oauth2` |
| **[!UICONTROL Facoltativo]** | No | Quando è presente un token valido | `noauth` e `oauth2` |

Il gestore di un&#39;azione **[!UICONTROL Required]** non viene mai eseguito senza un token con ambito corretto valido. Il gestore di un&#39;azione **[!UICONTROL Optional]** è sempre in esecuzione e può richiedere l&#39;accesso con `extra.challengeAuth()`.

**[!UICONTROL Obbligatorio]** è pertanto l&#39;unica modalità che rifiuta i chiamanti non autenticati. Un&#39;app rifiuta tutte le chiamate anonime solo quando ogni azione è **[!UICONTROL obbligatoria]**. Se un&#39;azione è **[!UICONTROL None]** o **[!UICONTROL Optional]**, l&#39;app utilizza l&#39;autenticazione mista perché almeno un&#39;azione accetta chiamate anonime.

Le modalità di autenticazione hanno effetto solo quando **[!UICONTROL Abilita autenticazione]** è attivo. Le modifiche hanno effetto sulla prossima distribuzione dell’app.

La modifica dello switch di autenticazione riscrive anche le modalità per azione:

| Modifica switch | Effetto sulle modalità per azione |
|---------------|----------------------------|
| Da spento a attivato | Ogni azione **[!UICONTROL None]** diventa **[!UICONTROL Required]**. Le azioni già **[!UICONTROL Richieste]** o **[!UICONTROL Facoltative]** mantengono la loro modalità |
| Attivato/disattivato | La modalità e gli ambiti di ogni azione vengono cancellati per tale ambiente. La configurazione non viene ripristinata se si riattiva l&#39;interruttore |

Il passaggio a **[!UICONTROL Workspace]** non riscrive mai le modalità. Il selettore carica la configurazione salvata dell’altro ambiente senza modificarla.

Con **[!UICONTROL Abilita autenticazione]** in e ogni azione impostata su **[!UICONTROL Nessuna]**, l&#39;app non richiede l&#39;autenticazione ma pubblica comunque il server di autorizzazione per l&#39;individuazione. Per rendere l’app completamente pubblica, disattiva lo switch.

Un’app può utilizzare diverse modalità di autenticazione per le singole azioni. Per informazioni sulle modalità di applicazione di ciascuna piattaforma, vedere [comportamento della piattaforma LLM](/help/reference/authentication-reference.md#platform-behavior).

## Requisiti del token {#token-requirements}

Il provider di identità deve rilasciare token di accesso che soddisfino tutti i seguenti requisiti. Il runtime considera assente un token che non supera un controllo: il chiamante non è autenticato e un&#39;azione **[!UICONTROL Obbligatorio]** impedisce al chiamante di accedere.

| Requisito | Dettagli |
|-------------|--------|
| Formato | JWT firmato. I token opachi non sono supportati |
| Algoritmo di firma | `RS256`, `RS384`, `RS512`, `ES256`, `ES384`, `ES512`, `PS256`, `PS384` o `PS512`. Gli algoritmi HMAC come `HS256` vengono rifiutati |
| `iss` | Deve corrispondere a **[!UICONTROL Emittente]** |
| `aud` | Deve contenere l&#39;identificatore di risorsa dell&#39;app, l&#39;URL del server MCP per tale ambiente |
| `exp` | Deve essere nel futuro |
| `scope` oppure `scp` | Il runtime legge una stringa `scope` delimitata da spazi o utilizza come fallback una matrice di stringhe in `scp`. Utilizzare una di queste forme per controllare gli ambiti in base ai requisiti di ogni azione |
| `sub` | Identificatore utente letto dal gestore tramite `getAuthenticatedUser` |
| Trasporto | `Authorization: Bearer <token>` intestazione di richiesta |

Eventuali attestazioni flat aggiuntive incluse dal provider, ad esempio `tenant` o `email`, vengono passate al gestore. Gli oggetti nidificati vengono eliminati e i valori stringa lunghi vengono troncati.

## Individuazione provider di identità {#discovery}

L&#39;app pubblica i metadati delle risorse protette [RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728) in modo che le piattaforme LLM possano individuare il server di autorizzazione. Non è necessario creare, ospitare o configurare i metadati delle risorse protette.

Il provider di identità deve fornire le seguenti informazioni di individuazione:

| Requisito | Dettagli |
|-------------|--------|
| Metadati del server di autorizzazione | Il server di autorizzazione deve pubblicare i metadati di individuazione per i client LLM. A meno che non sia impostato l&#39;URI **[!UICONTROL JWKS]**, il runtime dell&#39;app cerca in modo specifico i metadati del server di autorizzazione OAuth [RFC 8414](https://datatracker.ietf.org/doc/html/rfc8414) per individuare `jwks_uri`, ma non torna al percorso di individuazione OIDC |
| Un emittente con un percorso | Il segmento noto precede il percorso, non lo segue. Un emittente in `https://auth.example.com/oauth2/default` fornisce i propri metadati in `https://auth.example.com/.well-known/oauth-authorization-server/oauth2/default` |
| URL chiave di firma diretta | Impostare **[!UICONTROL URI JWKS]** quando le chiavi sono ospitate altrove o il provider espone solo l&#39;individuazione OIDC. In questo modo viene ignorato il rilevamento runtime delle chiavi, non il rilevamento richiesto dai client LLM |

## API di autenticazione gestore {#handler-auth-api}

Il pacchetto `@adobe/llm-apps-runtime` esporta i seguenti helper. Ogni helper accetta `extra`, il secondo argomento ricevuto dal gestore.

| Helper | Restituisce |
|--------|---------|
| `getAuthenticatedUser(extra)` | Attestazione `sub` dell&#39;utente connesso o `undefined` quando la chiamata non è autenticata |
| `hasScope(extra, scope)` | `true` quando il token del chiamante contiene `scope` |

Le informazioni sul token verificato sono disponibili in `extra.authInfo`, che è `undefined` per una chiamata non autenticata.

| Proprietà | Descrizione |
|----------|-------------|
| `authInfo.token` | Il token Bearer non elaborato. Non registrarlo o restituirlo al client |
| `authInfo.clientId` | Attestazione `client_id` o `azp` oppure `unknown` |
| `authInfo.scopes` | Array di ambiti concessi |
| `authInfo.expiresAt` | Scadenza token, come attestazione `exp` |
| `authInfo.resource` | Identificatore di risorsa dell’app su cui è stato convalidato il token |
| `authInfo.extra` | `sub` più altre richieste di rimborso forfettarie incluse nel provider di identità |

`extra.challengeAuth(options)` è disponibile solo per **[!UICONTROL Azioni facoltative]**. Restituisce il risultato dal gestore per chiedere all&#39;utente di accedere invece di restituire il contenuto.

L&#39;helper restituisce un risultato di strumento per [!DNL ChatGPT]. [!DNL Claude] richiede una richiesta HTTP a livello di trasporto. Il runtime fornisce tale verifica per le azioni **[!UICONTROL Obbligatorie]**, ma non tramite l&#39;helper del gestore facoltativo.

| Opzione | Descrizione |
|--------|-------------|
| `error` | Codice di errore [RFC 6750](https://datatracker.ietf.org/doc/html/rfc6750) Bearer: `invalid_token`, `insufficient_scope` o `invalid_request`. Impostazione predefinita: `insufficient_scope` |
| `errorDescription` | Il messaggio mostrato all’utente. Impostazione predefinita: un prompt di accesso generico |
| `scope` | Ambiti delimitati da spazi da richiedere. Ometti per consentire alla piattaforma di tornare agli ambiti supportati dall’app |

>[!IMPORTANT]
>
>Imposta sempre `error` esplicitamente. Utilizzare `invalid_token` per un chiamante senza una sessione valida e `insufficient_scope` solo per un chiamante il cui token è valido ma non dispone di un ambito richiesto. Il runtime passa il valore alla piattaforma LLM, che sceglie la formulazione del prompt di accesso. Invia il codice che descrive accuratamente la condizione anziché il codice con la richiesta preferita.

## Comportamento della piattaforma LLM {#platform-behavior}

Entrambe le piattaforme supportano azioni pubbliche e protette nella stessa app, ma i trigger di accesso sono diversi.

| Comportamento | [!DNL ChatGPT] | [!DNL Claude] |
|----------|----------------|---------------|
| Autenticazione combinata | Le azioni pubbliche funzionano in modo anonimo; **[!UICONTROL Richieste]** azioni richiedono l&#39;accesso | Con **[!UICONTROL Accedi quando necessario]**, le azioni pubbliche funzionano in modo anonimo e una chiamata protetta avvia l&#39;accesso |
| App con ogni azione **[!UICONTROL Obbligatoria]** | Configurare l’autenticazione OAuth | Richiede l’autenticazione prima dell’uso |
| Configurazione del connettore | Utilizza **[!UICONTROL Nessuna autenticazione]** per un&#39;app pubblica, **[!UICONTROL OAuth]** quando ogni azione è **[!UICONTROL Obbligatoria]**, e **[!UICONTROL Mista]** in caso contrario, dove vengono offerte queste scelte | Rivedi le impostazioni rilevate. Scegli **[!UICONTROL Accedi quando necessario]** per le app miste o **[!UICONTROL Accedi ora]** per l&#39;accesso iniziale |
| Identità mancante o ambito richiesto | Runtime restituisce la sfida di autenticazione della piattaforma | Il runtime restituisce HTTP 401 per identità mancante o non valida oppure HTTP 403 con una richiesta di verifica di ambito insufficiente per un token valido privo di un ambito richiesto |
| `extra.challengeAuth()` del gestore facoltativo | Può avviare la riautenticazione | In caso di esito positivo, la risposta del risultato dello strumento HTTP non avvia l’accesso. Utilizza un&#39;azione **[!UICONTROL Obbligatoria]** per un gate di accesso portatile |

Per informazioni sul comportamento corrente del client, vedere [Claude lazy authentication](https://claude.com/docs/connectors/building/lazy-authentication) e [ChatGPT authentication](https://developers.openai.com/plugins/build/auth). L’interfaccia utente e i criteri del connettore possono variare a seconda dell’account e dell’organizzazione.


## Correlato

- [Autenticazione degli utenti finali con il proprio provider di identità](/help/guides/authentication.md)
- [Campi azione e widget](/help/reference/reference-docs.md)
- [Risoluzione di problemi](/help/reference/troubleshooting.md#authentication)
