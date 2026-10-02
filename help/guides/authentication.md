---
title: Autenticazione degli utenti finali con il proprio provider di identità
description: Attiva l’autenticazione dell’utente finale per l’app Adobe LLM in modo che una piattaforma LLM supportata firmi l’utente con il provider di identità prima di richiamare azioni protette.
source-git-commit: fd41dbcabc4db0cae766de19cb042d7c85d8b7aa
workflow-type: tm+mt
source-wordcount: '2149'
ht-degree: 0%
---

# Autenticazione degli utenti finali con il proprio provider di identità {#authentication}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Per impostazione predefinita, ogni azione nell’app è pubblica: qualsiasi piattaforma LLM con l’URL del server MCP può chiamarla e il gestore non è in grado di identificare l’utente finale.

Attiva l’autenticazione quando un’azione deve sapere quale utente finale richiede, ad esempio per restituire ordini, adesioni o dettagli dell’account. La piattaforma LLM firma l&#39;utente con **il provider di identità** (IdP), invia il token di accesso risultante a ogni chiamata e il gestore riceve l&#39;identità verificata.

**Percorso:** Copia l&#39;identificatore della risorsa → configura il provider di identità → attivare l&#39;autenticazione → impostare una modalità di autenticazione per ogni azione → distribuire → leggere l&#39;identità nel gestore → testare l&#39;app protetta.

Si tratta di un ramo avanzato, non fa parte del percorso di prima esecuzione. Completa [Crea automaticamente la tua prima app](/help/guides/create-app.md) e [Distribuisci prima l&#39;app](/help/guides/deploy-your-app.md).

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

L&#39;autenticazione è configurata **per ambiente**. **[!UICONTROL Stage]** e **[!UICONTROL Produzione]** contengono impostazioni indipendenti, pertanto puoi verificare la configurazione in base a un tenant IdP di sviluppo prima di abilitarla in **[!UICONTROL Produzione]**.

## Prima di iniziare

- Provider di identità OAuth 2.1 o OpenID Connect che emette **JWT** token di accesso firmati con un algoritmo asimmetrico. I token opachi e i token con firma HMAC non sono supportati. Vedi [Requisiti del token](/help/reference/authentication-reference.md#token-requirements).
- Accesso amministratore a tale provider di identità, per consentire di registrare un’API e un client.
- L’app è stata distribuita almeno una volta nell’ambiente che stai configurando. L’URL del server MCP distribuito è il valore su cui i token devono avere l’ambito.

## Copia l’identificatore della risorsa

L&#39;**identificatore di risorsa** dell&#39;app è l&#39;URL del server MCP. Ogni token di accesso che il provider di identità rilascia per questa app deve denominare tale URL esatto come pubblico. Il binding è ciò che impedisce a un token coniato per un altro servizio di essere riprodotto contro la tua app.

1. Apri la pagina Dettagli app.
2. Scorri fino a **[!UICONTROL Verifica l&#39;app]**.
3. Nell&#39;ambiente che si sta configurando, selezionare **[!UICONTROL Copia URL]**.

![Dettagli app — copia l&#39;URL del server MCP dell&#39;area di gestione temporanea](/help/assets/guide-onboarding-agent/app-mcp-url.png)

Mantieni questo valore: ne hai bisogno nel provider di identità nel passaggio successivo. Incolla l’URL copiato anziché digitarlo nuovamente. Il controllo del pubblico è una corrispondenza esatta della stringa, che include qualsiasi componente del percorso; pertanto, una differenza di carattere singolo impedisce la convalida di ogni token.

>[!NOTE]
>
>Ogni ambiente ha il proprio URL di server MCP e quindi il proprio pubblico. Configura **[!UICONTROL Stage]** e **[!UICONTROL Produzione]** separatamente.

## Configurare il provider di identità

I passaggi esatti variano a seconda del provider, ma ogni provider ha bisogno degli stessi quattro elementi.

1. **Registra l&#39;app come API (risorsa).** Impostare il relativo identificatore, ovvero il valore immesso dal provider nell&#39;attestazione `aud` del token, sull&#39;URL del server MCP copiato. I provider etichettano questo campo in modo diverso, in genere *Identificatore* o *Pubblico*. Non utilizzare un valore generico come `api`. L&#39;identificatore deve essere univoco per questa app oppure un token emesso per un altro servizio può essere riprodotto su di essa.
2. **Definisci gli ambiti** con cui desideri eseguire il gate delle azioni, ad esempio `orders:read` o `profile:read`. Utilizza un ambito per ogni autorizzazione significativa, pertanto un’azione richiede solo ciò di cui ha bisogno.
3. **Supporto PKCE.** Le piattaforme LLM inviano un `code_challenge` con `code_challenge_method=S256` a ogni richiesta di autorizzazione, pertanto il server di autorizzazione deve supportare PKCE S256 e pubblicizzare `"code_challenge_methods_supported": ["S256"]` nei relativi metadati.
4. **Consenti alla piattaforma LLM di registrarsi come client.** Le piattaforme LLM supportate creano il proprio client OAuth rispetto al server di autorizzazione, quindi abilitate la registrazione client dinamica se offerta dal provider. In caso contrario, crea manualmente un client pubblico e fornisci il relativo ID client (e segreto), solo se il provider richiede l’autenticazione client riservata, durante la configurazione del connettore sulla piattaforma. Registra l&#39;URI di reindirizzamento nei documenti della piattaforma; per le superfici ospitate di [!DNL Claude] che è `https://claude.ai/api/mcp/auth_callback`. Alcune piattaforme generano un URI di reindirizzamento distinto per ogni connettore creato dall&#39;utente, [!DNL ChatGPT] tra di esse, pertanto leggi il valore dalla schermata di configurazione del connettore e registralo prima del primo accesso. Un URI di reindirizzamento non registrato fa in modo che il server di autorizzazione rifiuti completamente la richiesta di autorizzazione.

>[!IMPORTANT]
>
>Gli endpoint di tipo emittente, JWKS, autorizzazione e token del provider di identità devono essere tutti raggiungibili tramite HTTPS pubblico. Sia la piattaforma LLM che l’app implementata recuperano direttamente i metadati dal provider, pertanto un provider di identità dietro un elenco Consentiti VPN o IP non può completare il processo di accesso. Un firewall o un firewall dell’applicazione web di fronte al provider è una causa comune e può interrompere il flusso anche quando l’app stessa è raggiungibile.

## Attiva autenticazione

1. Nel menu di navigazione a sinistra, seleziona **[!UICONTROL Impostazioni]**, quindi apri la scheda **[!UICONTROL Autenticazione]**.
2. In **[!UICONTROL Workspace]**, scegli **[!UICONTROL Stage]** o **[!UICONTROL Produzione]**.
3. Attiva **[!UICONTROL Abilita autenticazione]**.
4. In **[!UICONTROL Impostazioni core]**, immetti:
   - **[!UICONTROL Emittente]**: l&#39;URL emittente del provider di identità, che è anche il valore inserito nell&#39;attestazione `iss` di ogni token. Questo è obbligatorio, deve essere HTTPS e viene pubblicato anche come server di autorizzazione dell’app in modo che le piattaforme LLM possano scoprire dove inviare gli utenti. È supportato un solo provider di identità per app.
   - **[!UICONTROL Ambiti supportati]**: ogni ambito richiesto dalle azioni dell&#39;app. Esegui il mirroring degli ambiti definiti nel provider di identità.
5. **[!UICONTROL Impostazioni avanzate]** è facoltativo. Imposta **[!UICONTROL URI JWKS]** solo quando le chiavi di firma non sono nel punto in cui i metadati del server autorizzazioni li pubblicizzano; in caso contrario l&#39;app le individua automaticamente.
6. Seleziona **[!UICONTROL Salva]**.

![Autenticazione: abilitare l&#39;autenticazione e completare le impostazioni di base](/help/assets/guide-authentication/auth-core-settings.png)

Per informazioni sull&#39;accettazione di ogni campo, vedere [Impostazioni di autenticazione](/help/reference/authentication-reference.md#authentication-settings).

## Scegli una modalità di autenticazione per ogni azione

Quando si attiva **[!UICONTROL Abilita autenticazione]**, ogni azione attualmente impostata su **[!UICONTROL Nessuna]** cambia in **[!UICONTROL Obbligatorio]**. In **[!UICONTROL Configurazione per azione]**, controlla l&#39;assegnazione e imposta la modalità necessaria per ogni azione:

| Modalità | Comportamento |
|------|----------|
| **[!UICONTROL Nessuno]** | Pubblico. L’azione può essere chiamata senza un token. |
| **[!UICONTROL Obbligatorio]** | Cancellato. L’azione può essere chiamata solo con un token valido che includa tutti gli ambiti elencati per essa. I chiamanti non autenticati sono costretti ad accedere. |
| **[!UICONTROL Facoltativo]** | Chiamabile in modo anonimo, ma l’azione annuncia anche il supporto dell’accesso. Il gestore decide per chiamata se distribuire un risultato generico o chiedere all&#39;utente di effettuare l&#39;accesso per uno personalizzato. |

![Autenticazione: impostare una modalità di autenticazione e gli ambiti per ogni azione](/help/assets/guide-authentication/auth-per-action.png)

Le azioni già impostate su **[!UICONTROL Obbligatorie]** o **[!UICONTROL Facoltative]** mantengono la modalità esistente.

Per un&#39;azione **[!UICONTROL Obbligatorio]** o **[!UICONTROL Facoltativo]**, aggiungi i **[!UICONTROL Ambiti]** necessari. Ogni ambito deve già essere visualizzato in **[!UICONTROL Ambiti supportati]**. In caso contrario, l&#39;app richiederà un&#39;autorizzazione che non viene annunciata alle piattaforme LLM. Il salvataggio è bloccato fino alla risoluzione della mancata corrispondenza.

**[!UICONTROL Ambiti supportati]** è l&#39;autorità per questo elenco. Se si rimuove un ambito da esso, tale ambito viene rimosso da ogni azione che lo richieda non appena si apporta la modifica, quindi aggiungere prima un ambito, quindi assegnarlo a un&#39;azione.

**[!UICONTROL Autenticazione obbligatoria per tutte le azioni]** imposta ogni azione su **[!UICONTROL Obbligatoria]**. Se si cancella, verrà restituita ogni azione a **[!UICONTROL None]**.

Al termine, seleziona **[!UICONTROL Salva]**. Le modifiche alla modalità di autenticazione e all’ambito vengono salvate insieme alle impostazioni a livello di app.

>[!IMPORTANT]
>
>Se si disattiva **[!UICONTROL Abilita autenticazione]**, questa configurazione per azione viene ignorata per l&#39;ambiente selezionato. La modalità e gli ambiti di ogni azione vengono cancellati, non memorizzati. La riattivazione riparte da tutti-**[!UICONTROL Obbligatorio]**.

>[!NOTE]
>
>L&#39;impostazione di ogni azione su **[!UICONTROL None]** non disattiva l&#39;autenticazione. Nessuna chiamata viene rifiutata in tale stato, ma l’app annuncia ancora il server di autorizzazione alle piattaforme LLM, in modo che un client possa offrire all’utente un accesso che non concede alcun accesso aggiuntivo. Per rendere l&#39;app completamente pubblica, disattivare **[!UICONTROL Abilita autenticazione]** e distribuire.

Sono supportate le modalità di combinazione in un&#39;app, alcune azioni pubbliche e altre gestite, e [!DNL ChatGPT] applica singolarmente la modalità di ogni azione: solo le azioni gestite richiedono l&#39;accesso dell&#39;utente.

>[!IMPORTANT]
>
>[!DNL Claude] è l&#39;eccezione. Applica l&#39;autenticazione per connettore anziché per azione, quindi se un&#39;azione nell&#39;app è impostata su **[!UICONTROL Obbligatorio]** o **[!UICONTROL Facoltativo]**, [!DNL Claude] chiede all&#39;utente di accedere prima di utilizzare il connettore, incluse le azioni impostate su **[!UICONTROL Nessuno]**. Per mantenere pubblica un&#39;azione per [!DNL Claude] utenti, inserirla in un&#39;app separata.

## Distribuire la modifica

Le modifiche di autenticazione hanno effetto sulla prossima distribuzione di questa app. **Distribuisci nuovamente l&#39;app** nell&#39;ambiente configurato. Consulta [Distribuire l&#39;app](/help/guides/deploy-your-app.md).

L’URL del server MCP non cambia, quindi tutti i plug-in o i connettori già creati continuano a funzionare. Ora è gestita, quindi ai suoi utenti viene richiesto di effettuare l’accesso la prossima volta che lo utilizzano.

## Leggi l’identità nel gestore

Un’identità verificata raggiunge il gestore come secondo argomento. È presente ogni volta che il chiamante invia un token valido, indipendentemente dalla modalità di autenticazione dell&#39;azione, quindi un&#39;azione **[!UICONTROL Facoltativa]** può personalizzare il risultato quando è presente un token e restituire comunque un risultato quando non lo è.

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

Non è necessario verificare il token personalmente. Per un&#39;azione **[!UICONTROL Obbligatoria]**, il runtime blocca ogni chiamata priva di un token valido che contiene gli ambiti elencati, pertanto il gestore viene eseguito solo per un chiamante autorizzato. Utilizzare `hasScope` quando si desidera eseguire un ramo su un&#39;autorizzazione anziché fare affidamento sul gate, ad esempio in un&#39;azione **[!UICONTROL Facoltativa]**.

Un&#39;azione **[!UICONTROL Facoltativa]** può richiedere all&#39;utente di accedere a metà conversazione restituendo `extra.challengeAuth()`. Questa opzione è disponibile solo per **[!UICONTROL Azioni facoltative]**:

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

Decidere se eseguire l&#39;escalation da un parametro di input esplicito, come `signIn` in questo caso, anziché esaminare la formulazione dell&#39;utente.

Imposta `error` in modo che corrisponda alla condizione segnalata. Utilizzare `invalid_token` quando il chiamante non dispone di una sessione valida e deve accedere, come nell&#39;esempio precedente, e `insufficient_scope` quando il chiamante è già connesso ma il token non dispone di un ambito necessario per l&#39;azione. La piattaforma LLM sceglie il testo della richiesta che l’utente vede, e quanto varia con questo valore in base alla piattaforma — quindi invia il codice che descrive la condizione con precisione.

Eseguire la verifica solo quando l&#39;identità necessaria è effettivamente mancante, come nel caso del controllo `!extra.authInfo`. L’accesso non può soddisfare un gestore che lancia una sfida incondizionata, pertanto all’utente viene richiesto di eseguire di nuovo l’autenticazione a ogni chiamata.

>[!NOTE]
>
>In [!DNL ChatGPT], un accesso generato in questo modo richiede all&#39;utente di riconnettersi al connettore anziché concedere un&#39;autorizzazione aggiuntiva. Il [!DNL Claude] l&#39;utente effettua l&#39;accesso prima dell&#39;esecuzione di qualsiasi azione, pertanto un&#39;azione non deve mai generarne una.

Mantieni il lato server dell’identità. Passa solo ciò di cui il widget ha bisogno in `structuredContent` e non inserire mai il token di accesso. Vedere [Personalizzare un gestore generato](/help/guides/customize-handler.md).

Per il contratto completo, vedi [API di autenticazione gestore](/help/reference/authentication-reference.md#handler-auth-api).

## Testare l’app protetta

Il plug-in o il connettore esistente rileva la modifica dopo la distribuzione. Per configurarne uno da zero:

### [!DNL ChatGPT]

Nella finestra di dialogo **[!UICONTROL Nuovo plug-in]**, imposta **[!UICONTROL Autenticazione]** in modo che corrisponda alla configurazione delle azioni dell&#39;app:

| Azioni dell&#39;app | Seleziona |
|--------------------|--------|
| Tutti impostati su **[!UICONTROL Nessuno]** | **[!UICONTROL Nessuna autenticazione]** |
| Tutto impostato su **[!UICONTROL Obbligatorio]** | **[!UICONTROL OAuth]** |
| Qualsiasi altra combinazione | **[!UICONTROL Misto]** |

![ChatGPT — seleziona la modalità di autenticazione per il plug-in](/help/assets/guide-authentication/chatgpt-authentication-mode.png)

Un&#39;azione **[!UICONTROL Facoltativa]** accetta sempre chiamate anonime, pertanto un&#39;app che ne contiene una non è mai completamente gestita. Scegliere **[!UICONTROL Mista]** anche se ogni azione è impostata su **[!UICONTROL Facoltativa]**. Solo **[!UICONTROL Richiesto]** rifiuta i chiamanti non autenticati.

Per il resto della finestra di dialogo, consulta [Test del plug-in ChatGPT](/help/guides/test-in-chatgpt.md).

### [!DNL Claude]

Aggiungi il connettore personalizzato, quindi seleziona **[!UICONTROL Connetti]** e completa l&#39;accesso presentato dal provider di identità. Nessuna scelta di autenticazione da effettuare: [!DNL Claude] cancella l&#39;intero connettore ogni volta che viene gestita un&#39;azione. Vedere [Verificare il connettore Claude](/help/guides/test-in-claude.md).

### Verificare

- La piattaforma ti reindirizzerà alla pagina di accesso del tuo provider di identità.
- Dopo l’accesso, un’azione protetta restituisce dati specifici dell’utente.
- Un&#39;azione protetta richiede l&#39;accesso quando si è disconnessi.
- Il [!DNL ChatGPT], un&#39;azione impostata su **[!UICONTROL None]** risponde ancora senza effettuare l&#39;accesso. Su [!DNL Claude], l&#39;intero connettore è gestito.

Se l&#39;accesso non viene avviato o se un token viene rifiutato, vedere [Risoluzione dei problemi](/help/reference/troubleshooting.md#authentication).

## Linee guida sulla sicurezza

- Concedi l’ambito più ristretto necessario per ogni azione. Non riutilizzare un ambito ampio in ogni azione.
- Mantieni il client segreto nel provider di identità e nella configurazione del connettore della piattaforma LLM. Non inserire mai metadati di azione, codice del gestore, widget JavaScript o controllo del codice sorgente.
- Tratta le attestazioni token come input da un sistema esterno. Convalidare qualsiasi elemento letto da `authInfo.extra` prima di utilizzarlo in una query.
- Autorizza e autentica. Un token valido dimostra chi è l’utente, non che può vedere un record particolare — controlla la proprietà nel gestore prima di restituire i dati.
- Non registrare token, set di attestazioni completi o identificatori utente.
- Restituisci errori sicuri. Non rendere visibili all’utente le risposte o le tracce dello stack del provider di identità a monte.
- Configura e verifica **[!UICONTROL Stage]** in un tenant del provider di identità non di produzione prima di abilitare l&#39;autenticazione in **[!UICONTROL Produzione]**.

## Passaggio successivo

- [Riferimento autenticazione](/help/reference/authentication-reference.md): campi, requisiti del token e comportamento della piattaforma.
- [Personalizzare un gestore generato](/help/guides/customize-handler.md). Chiamare un&#39;API upstream protetta da un gestore.
- [Distribuisci l&#39;app](/help/guides/deploy-your-app.md).
