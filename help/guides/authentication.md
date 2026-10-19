---
title: Autenticazione degli utenti finali con il proprio provider di identità
description: Attiva l’autenticazione dell’utente finale per l’app Adobe LLM in modo che una piattaforma LLM supportata firmi l’utente con il provider di identità prima di richiamare azioni protette.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '2136'
ht-degree: 0%
---

# Autenticazione degli utenti finali con il proprio provider di identità {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Per impostazione predefinita, ogni azione nell&#39;app è pubblica. Qualsiasi piattaforma LLM con l’URL del server MCP può richiamare tali azioni e il gestore non è in grado di identificare l’utente finale.

Attiva l’autenticazione quando un’azione deve sapere quale utente finale richiede, ad esempio per restituire ordini, adesioni o dettagli dell’account. La piattaforma LLM firma l&#39;utente con **il provider di identità** (IdP), invia il token di accesso risultante a ogni chiamata e il gestore riceve l&#39;identità verificata.

**In questa guida:** Scopri come connettere il provider di identità e scegliere quali azioni richiedono l&#39;accesso. Utilizza l’identità verificata nei gestori, distribuisci l’app e verifica l’accesso e le azioni protette.

La configurazione dell’autenticazione è un’attività avanzata. Completa [Configurazione infrastruttura](/help/guides/create-app.md), [Crea azioni](/help/guides/creating-actions.md) e [Distribuisci prima l&#39;app](/help/guides/deploy-your-app.md).

## Come funziona

Hai il tuo provider di identità. L&#39;app distribuita è solo un server di risorse **OAuth 2.1**. Verifica i token emessi dal server di autorizzazione. Non emette mai token e [!DNL Adobe] non memorizza mai l&#39;ID client o il segreto client.

```
┌── Your identity provider ───────────────────────────────────────────────┐
│  Authorization server — you own it                                      │
│  Issues access tokens, holds the user directory, defines the scopes     │
└─────────────────────────────────────────────────────────────────────────┘
        ▲  2  user signs in, platform gets an access token
        │                                    │
        │  1  platform discovers your        │  3  every tools/call carries
        │     authorization server from      │     Authorization: Bearer <token>
        │     your app's metadata            ▼
┌── LLM platform (ChatGPT, Claude, …) ────────────────────────────────────┐
└─────────────────────────────────────────────────────────────────────────┘
                                             │
                                             ▼
┌── Your LLM App on Adobe I/O Runtime ────────────────────────────────────┐
│  Resource server — verifies the token's signature, issuer, audience,    │
│  and expiry, then enforces the auth mode you set for each action        │
│                                                                         │
│  Your handler reads the verified identity from its second argument      │
└─────────────────────────────────────────────────────────────────────────┘
```

Configura l&#39;autenticazione **per ambiente**. **[!UICONTROL Stage]** e **[!UICONTROL Production]** contengono impostazioni indipendenti. Verificare la configurazione rispetto a un tenant IdP di sviluppo prima di abilitare l&#39;autenticazione in **[!UICONTROL Produzione]**.

## Prima di iniziare

- Provider di identità OAuth 2.1 o OpenID Connect che emette **JWT** token di accesso firmati con un algoritmo asimmetrico. I token opachi e i token con firma HMAC non sono supportati. Vedi [Requisiti del token](/help/reference/authentication-reference.md#token-requirements).
- L’accesso come amministratore a quel provider di identità per registrare un’API e un client.
- L’app è stata distribuita almeno una volta nell’ambiente che stai configurando. L’URL del server MCP distribuito è il valore su cui i token devono avere l’ambito.

## Copia l’identificatore della risorsa

L&#39;**identificatore di risorsa** dell&#39;app è l&#39;URL del server MCP. Ogni token di accesso che il provider di identità rilascia per l’app deve denominare tale URL esatto come pubblico. Questo binding di pubblico impedisce l’utilizzo di un token rilasciato per un altro servizio.

1. Apri la pagina Dettagli app.
2. Scorri fino a **[!UICONTROL Verifica l&#39;app]**.
3. Nell&#39;ambiente che si sta configurando, selezionare **[!UICONTROL Copia URL]**.

![Dettagli app — copia l&#39;URL del server MCP dell&#39;area di gestione temporanea](/help/assets/guide-onboarding-agent/app-mcp-url.png)

Nel passaggio successivo, conserva l’URL del server MCP copiato per la configurazione del provider di identità. Incolla l’URL anziché digitarlo nuovamente. Il controllo del pubblico richiede una corrispondenza esatta della stringa, incluso il percorso. Una differenza di carattere singolo fa sì che ogni token non superi la convalida.

>[!NOTE]
>
>Ogni ambiente ha il proprio URL di server MCP e quindi il proprio pubblico. Configura **[!UICONTROL Stage]** e **[!UICONTROL Produzione]** separatamente.

## Configurare il provider di identità

I passaggi variano a seconda del provider. Configura i quattro elementi seguenti.

1. **Registra l&#39;app come API (risorsa).** Impostare il relativo identificatore, ovvero il valore immesso dal provider nell&#39;attestazione `aud` del token, sull&#39;URL del server MCP copiato. I provider di solito etichettano questo campo *Identificatore* o *Pubblico*. Utilizzare un identificatore univoco per l&#39;app, non un valore generico come `api`, per impedire l&#39;utilizzo dei token rilasciati per un altro servizio.
2. **Definisci gli ambiti** che proteggono le azioni, ad esempio `orders:read` o `profile:read`. Utilizza un ambito per ogni autorizzazione significativa, in modo che ogni azione richieda solo l’accesso necessario.
3. **Supporto PKCE.** Le piattaforme LLM inviano un `code_challenge` con `code_challenge_method=S256` a ogni richiesta di autorizzazione, pertanto il server di autorizzazione deve supportare PKCE S256 e pubblicizzare `"code_challenge_methods_supported": ["S256"]` nei relativi metadati.
4. **Scegliere in che modo la piattaforma LLM identifica il client OAuth.** Utilizza i documenti di metadati ID client (CIMD) se il provider in uso supporta i requisiti della piattaforma, Dynamic Client Registration (DCR) o un client OAuth preregistrato. Specifica un ID cliente e un segreto solo quando il metodo scelto li richiede. Registra l&#39;URI di reindirizzamento esatto documentato o visualizzato dalla piattaforma per le superfici ospitate di [!DNL Claude] che è `https://claude.ai/api/mcp/auth_callback`. [!DNL ChatGPT] può utilizzare un callback stabile o specifico del connettore a seconda delle funzionalità annunciate del provider, pertanto copia l&#39;URI visualizzato anziché indovinare. Consulta la [documentazione di autenticazione ChatGPT](https://developers.openai.com/plugins/build/auth) e [documentazione di autenticazione Claude](https://claude.com/docs/connectors/building/authentication).

>[!IMPORTANT]
>
>Gli endpoint di tipo emittente, JWKS, autorizzazione e token del provider di identità devono essere tutti raggiungibili tramite HTTPS pubblico. Sia la piattaforma LLM che l’app implementata recuperano i metadati direttamente dal provider. Un firewall per applicazioni web, firewall o inserisce nell&#39;elenco Consentiti di accesso VPN, IP che blocca tali richieste impedisce l’accesso, anche se l’app stessa rimane raggiungibile.

## Attiva autenticazione

1. Nel menu di navigazione a sinistra, seleziona **[!UICONTROL Impostazioni]**, quindi apri la scheda **[!UICONTROL Autenticazione]**.
2. In **[!UICONTROL Workspace]**, scegli **[!UICONTROL Stage]** o **[!UICONTROL Produzione]**.
3. Attiva **[!UICONTROL Abilita autenticazione]**.
4. In **[!UICONTROL Impostazioni core]**, immetti:
   - **[!UICONTROL Emittente]**: l&#39;URL dell&#39;emittente HTTPS del provider di identità, che è anche il valore inserito nell&#39;attestazione `iss` di ogni token. La piattaforma richiede questo URL e pubblica l’emittente come server di autorizzazione dell’app per l’individuazione del client LLM. Ogni app supporta un provider di identità.
   - **[!UICONTROL Ambiti supportati]**: ogni ambito richiesto dalle azioni dell&#39;app. Esegui il mirroring degli ambiti definiti nel provider di identità.
5. **[!UICONTROL Impostazioni avanzate]** è facoltativo. Imposta **[!UICONTROL URI JWKS]** solo quando le chiavi di firma non sono nel punto in cui i metadati del server autorizzazioni li pubblicizzano; in caso contrario l&#39;app le individua automaticamente.
6. Seleziona **[!UICONTROL Salva]**.

![Autenticazione: abilitare l&#39;autenticazione e completare le impostazioni di base](/help/assets/guide-authentication/auth-core-settings.png)

Per i requisiti dei campi, vedere [Impostazioni di autenticazione](/help/reference/authentication-reference.md#authentication-settings).

## Scegli una modalità di autenticazione per ogni azione

Quando si attiva **[!UICONTROL Abilita autenticazione]**, ogni azione attualmente impostata su **[!UICONTROL Nessuna]** cambia in **[!UICONTROL Obbligatorio]**. In **[!UICONTROL Configurazione per azione]**, controlla l&#39;assegnazione e imposta la modalità necessaria per ogni azione:

| Modalità | Comportamento |
|------|----------|
| **[!UICONTROL Nessuno]** | Pubblico. Ai chiamanti non serve un token. |
| **[!UICONTROL Obbligatorio]** | Protetto. I chiamanti devono disporre di un token valido che contenga tutti gli ambiti elencati. Il runtime impedisce ai chiamanti non autenticati di accedere. |
| **[!UICONTROL Facoltativo]** | Chiamabile in modo anonimo, ma l’azione annuncia anche il supporto dell’accesso. Il gestore decide per chiamata se distribuire un risultato generico o chiedere all&#39;utente di effettuare l&#39;accesso per uno personalizzato. |

![Autenticazione: impostare una modalità di autenticazione e gli ambiti per ogni azione](/help/assets/guide-authentication/auth-per-action.png)

Le azioni già impostate su **[!UICONTROL Obbligatorie]** o **[!UICONTROL Facoltative]** mantengono la modalità esistente.

Per un&#39;azione **[!UICONTROL Obbligatorio]** o **[!UICONTROL Facoltativo]**, aggiungi i **[!UICONTROL Ambiti]** necessari. Ogni ambito deve già essere visualizzato in **[!UICONTROL Ambiti supportati]**, pertanto l&#39;app annuncia ogni autorizzazione richiesta alle piattaforme LLM. La piattaforma blocca il salvataggio fino a quando non risolvi eventuali discrepanze nell’ambito.

**[!UICONTROL Gli ambiti supportati]** controllano gli ambiti disponibili. La rimozione di un ambito dall&#39;elenco comporta la rimozione immediata dell&#39;ambito da ogni azione necessaria. Aggiungere ogni ambito all&#39;elenco prima di assegnarlo a un&#39;azione.

Se si seleziona **[!UICONTROL Richiedi autenticazione per tutte le azioni]**, **[!UICONTROL Nessuna]** azioni diventeranno **[!UICONTROL Richieste]**, ma verranno mantenute le azioni già impostate su **[!UICONTROL Facoltative]**. Per richiedere l&#39;accesso per ogni azione, modifica le righe rimanenti **[!UICONTROL Facoltativo]** in **[!UICONTROL Obbligatorio]** singolarmente. Deselezionando la casella di controllo, ogni azione viene restituita a **[!UICONTROL None]**.

Al termine, seleziona **[!UICONTROL Salva]**. Le modifiche alla modalità di autenticazione e all’ambito vengono salvate insieme alle impostazioni a livello di app.

>[!IMPORTANT]
>
>Se si disattiva **[!UICONTROL Abilita autenticazione]**, la modalità di autenticazione e gli ambiti di ogni azione vengono cancellati per l&#39;ambiente selezionato. La piattaforma non mantiene tali impostazioni. L&#39;attivazione dell&#39;autenticazione comporta l&#39;impostazione di tutte le azioni su **[!UICONTROL Obbligatorio]**.

>[!NOTE]
>
>L&#39;impostazione di ogni azione su **[!UICONTROL None]** non disattiva l&#39;autenticazione. Nessuna azione richiede l’accesso in tale stato, ma l’app continua a pubblicizzare il server di autorizzazione sulle piattaforme LLM. Un client può offrire un accesso che non concede alcun accesso aggiuntivo. Per rendere l&#39;app completamente pubblica, disattivare **[!UICONTROL Abilita autenticazione]** e distribuire.

[!DNL ChatGPT] supporta modalità di autenticazione miste all&#39;interno di un&#39;app e applica singolarmente la modalità di ogni azione. Le azioni pubbliche non richiedono l’accesso; le azioni protette richiedono invece l’accesso in base alle esigenze.

[!DNL Claude] supporta anche le app miste tramite **autenticazione lazy**. Con **[!UICONTROL Accesso quando necessario]** selezionato durante la configurazione del connettore, le azioni pubbliche rimangono utilizzabili in modo anonimo e la piattaforma richiede l&#39;accesso quando viene chiamata un&#39;azione **[!UICONTROL Obbligatorio]**. Un&#39;app le cui azioni sono tutte **[!UICONTROL Obbligatorie]** richiede l&#39;accesso prima dell&#39;uso. Per la distinzione tra un&#39;azione richiesta e un gestore facoltativo che richiede l&#39;accesso, vedere [comportamento piattaforma LLM](/help/reference/authentication-reference.md#platform-behavior).

>[!NOTE]
>
>Il runtime corrente supporta questo comportamento di autenticazione mista, verificato in base alla versione 1.1.5. Le app distribuite meno recenti potrebbero richiedere un aggiornamento e una ridistribuzione runtime. Alcune build dell’interfaccia utente mostrano ancora un avviso legacy secondo cui Claude richiede l’autenticazione per l’intero connettore. Verifica il runtime distribuito e utilizza l&#39;opzione **[!UICONTROL Accedi quando necessario]** di Claude, invece di fare affidamento sull&#39;avviso.

## Distribuire la modifica

Le modifiche di autenticazione hanno effetto sulla prossima distribuzione di questa app. **Distribuisci nuovamente l&#39;app** nell&#39;ambiente configurato. Consulta [Distribuire l&#39;app](/help/guides/deploy-your-app.md).

L&#39;URL del server MCP non viene modificato. Aggiorna i metadati della connessione e verifica che le impostazioni di autenticazione corrispondano alla nuova configurazione dell&#39;app. Una piattaforma potrebbe richiedere di riconnettersi o di ricreare la connessione quando cambia il relativo metodo di autenticazione.

## Leggi l’identità nel gestore

Un’identità verificata raggiunge il gestore come secondo argomento ogni volta che il chiamante invia un token valido, indipendentemente dalla modalità di autenticazione dell’azione. Un&#39;azione **[!UICONTROL Facoltativa]** può utilizzare tale identità per personalizzare i risultati quando disponibili e restituire un risultato anonimo in caso contrario.

Utilizza `getAuthenticatedUser` per leggere l&#39;utente connesso:

```javascript
const { getAuthenticatedUser } = require('@adobe/llm-apps-runtime');

module.exports = async ({ orderId }, extra) => {
  const userId = getAuthenticatedUser(extra);

  if (!userId) {
    return { content: [{ type: 'text', text: 'Sign in to see your orders.' }] };
  }

  const order = await fetchOrderForUser(userId, orderId);

  return {
    content: [{ type: 'text', text: `Order ${order.id} is ${order.status}.` }],
    structuredContent: order
  };
};
```

Il runtime verifica il token automaticamente. Per un&#39;azione **[!UICONTROL Obbligatorio]**, vengono bloccate tutte le chiamate prive di token valido con gli ambiti elencati. Il gestore viene eseguito solo per un chiamante autorizzato. Utilizzare `hasScope` per scegliere il comportamento del gestore in base a un&#39;autorizzazione, ad esempio in un&#39;azione **[!UICONTROL Facoltativa]**.

Per richiedere l&#39;accesso durante una conversazione, restituire `extra.challengeAuth()` dal gestore. L&#39;helper è disponibile solo per **[!UICONTROL azioni facoltative]**:

```javascript
module.exports = async ({ signIn }, extra) => {
  if (signIn && !extra.authInfo) {
    return extra.challengeAuth({
      error: 'invalid_token',
      errorDescription: 'Sign in to see member pricing.'
    });
  }

  return {
    content: [{
      type: 'text',
      text: extra.authInfo ? await memberDeals() : await publicDeals()
    }]
  };
};
```

Utilizzare un parametro di input esplicito, ad esempio `signIn`, per decidere quando richiedere l&#39;autenticazione. Non dedurre tale decisione dalla formulazione dell’utente.

Imposta `error` per corrispondere alla condizione riportata. Utilizzare `invalid_token` quando il chiamante non dispone di una sessione valida e deve effettuare l&#39;accesso, come nell&#39;esempio precedente. Usa `insufficient_scope` quando il chiamante ha effettuato l&#39;accesso ma il token non ha l&#39;ambito richiesto. Ogni piattaforma LLM sceglie il testo del prompt e la modalità di risposta al codice di errore. Invia il codice che descrive accuratamente la condizione.

Richiedi l&#39;autenticazione solo quando manca l&#39;identità richiesta, come fa il controllo `!extra.authInfo` in questo esempio. L&#39;accesso non risolve una sfida incondizionata: il gestore richiede nuovamente l&#39;autenticazione a ogni chiamata.

>[!NOTE]
>
>`extra.challengeAuth()` restituisce una richiesta di verifica del risultato dello strumento per [!DNL ChatGPT]. [!DNL Claude] richiede una richiesta di autenticazione HTTP prima che il gestore venga eseguito. Per un&#39;operazione che richiede l&#39;accesso tra piattaforme supportate, utilizzare un&#39;azione **[!UICONTROL Obbligatorio]**. Se necessario, mantieni un&#39;azione **[!UICONTROL None]** separata per l&#39;esperienza pubblica.

Mantieni il lato server dell’identità. Passa solo i dati necessari al widget in `structuredContent`. Non includere mai un token di accesso in `structuredContent`. Vedi [Personalizzare un gestore generato](/help/guides/customize-handler.md).

Per il contratto completo, vedi [API di autenticazione gestore](/help/reference/authentication-reference.md#handler-auth-api).

## Testare l’app protetta

Il plug-in o il connettore esistente utilizza la configurazione aggiornata dopo la distribuzione. Per creare una nuova connessione, segui le istruzioni per la tua piattaforma:

### [!DNL ChatGPT]

Nella finestra di dialogo **[!UICONTROL Nuovo plug-in]**, imposta **[!UICONTROL Autenticazione]** in modo che corrisponda alla configurazione delle azioni dell&#39;app:

| Azioni dell&#39;app | Seleziona |
|--------------------|--------|
| Tutti impostati su **[!UICONTROL Nessuno]** | **[!UICONTROL Nessuna autenticazione]** |
| Tutto impostato su **[!UICONTROL Obbligatorio]** | **[!UICONTROL OAuth]** |
| Qualsiasi altra combinazione | **[!UICONTROL Misto]** |

Un&#39;azione **[!UICONTROL Facoltativa]** accetta sempre chiamate anonime, pertanto un&#39;app che ne contiene una non è mai completamente gestita. Scegliere **[!UICONTROL Mista]** anche se ogni azione è impostata su **[!UICONTROL Facoltativa]**. Solo **[!UICONTROL Richiesto]** rifiuta i chiamanti non autenticati.

Per il resto della finestra di dialogo, consulta [Test del plug-in ChatGPT](/help/guides/test-in-chatgpt.md).

### [!DNL Claude]

Aggiungi il connettore personalizzato e rivedi le impostazioni di autenticazione. Per un&#39;app mista pubblica/protetta, scegli **[!UICONTROL Accedi quando necessario]** quando disponibile. Per richiedere l&#39;accesso prima dell&#39;utilizzo del connettore, selezionare **[!UICONTROL Accedi ora]**. La finestra di dialogo varia in base all&#39;account o all&#39;organizzazione. Vedere [Verificare il connettore Claude](/help/guides/test-in-claude.md).

### Verificare

- La piattaforma ti reindirizzerà alla pagina di accesso del tuo provider di identità.
- Dopo l’accesso, un’azione protetta restituisce dati specifici dell’utente.
- Un&#39;azione protetta richiede l&#39;accesso quando si è disconnessi.
- In un&#39;app mista, un&#39;azione impostata su **[!UICONTROL Nessuno]** risponde comunque senza effettuare l&#39;accesso. In [!DNL Claude], verifica l&#39;azione pubblica con **[!UICONTROL Accedi quando necessario]**.
- Dopo l’accesso, riprova l’azione protetta e verifica che il runtime applichi le autorizzazioni previste.

Se l&#39;accesso non viene avviato o se un token viene rifiutato, vedere [Risoluzione dei problemi](/help/reference/troubleshooting.md#authentication).

## Linee guida sulla sicurezza

- Concedi l’ambito più ristretto necessario per ogni azione. Non riutilizzare un ambito ampio in ogni azione.
- Mantieni il client segreto nel provider di identità e nella configurazione del connettore della piattaforma LLM. Non inserire mai metadati di azione, codice del gestore, widget JavaScript o controllo del codice sorgente.
- Tratta le attestazioni token come input da un sistema esterno. Convalidare i valori da `authInfo.extra` prima di utilizzarli in una query.
- Autorizza e autentica. Un token valido dimostra l’identità dell’utente, non l’autorizzazione a leggere un record specifico. Controlla la proprietà nel gestore prima di restituire i dati.
- Non registrare token, set di attestazioni completi o identificatori utente.
- Restituisci errori sicuri. Non rendere visibili all’utente le risposte o le tracce dello stack del provider di identità a monte.
- Configura e verifica **[!UICONTROL Stage]** in un tenant del provider di identità non di produzione prima di abilitare l&#39;autenticazione in **[!UICONTROL Produzione]**.

## Passaggio successivo

- [Riferimento autenticazione](/help/reference/authentication-reference.md): campi, requisiti del token e comportamento della piattaforma.
- [Personalizzare un gestore generato](/help/guides/customize-handler.md). Chiamare un&#39;API upstream protetta da un gestore.
- [Distribuisci l&#39;app](/help/guides/deploy-your-app.md).
