---
title: Creare un’azione da zero
description: Definisci i metadati delle azioni, implementane il gestore, connetti un widget EDS, verificalo e implementalo con le app Adobe LLM.
source-git-commit: 0276ae4e4627e49157911ad579a42c0e77045eee
workflow-type: tm+mt
source-wordcount: '1582'
ht-degree: 0%
---

# Creare un’azione da zero {#create-action-from-scratch}

>[!IMPORTANT]
>
>Le app LLM sono disponibili per tutti i clienti di AEM Sites Cloud Service che desiderano sviluppare e gestire le proprie app. Una volta avviato il conteggio, ogni chiamata all’app verrà conteggiata nelle richieste di contenuto AEM Sites del cliente. Adobe annuncerà il tasso di misurazione e la data di inizio prima che il conteggio abbia effetto.

>[!NOTE]
>
>Questa guida presuppone una conoscenza di base di Adobe Edge Delivery Services (EDS). Se non si ha familiarità con EDS, prima di connettere un widget leggere l&#39;esercitazione per sviluppatori [EDS](https://www.aem.live/developer/tutorial) e [Esplorare i blocchi](https://www.aem.live/docs/exploring-blocks) per apprendere gli elementi essenziali, ovvero i blocchi, la funzione `decorate` e la struttura del progetto EDS.

Aggiungi una funzionalità non creata dalla piattaforma. Definisci l&#39;azione in [!DNL LLM Apps], scrivi il relativo gestore nell&#39;archivio collegato e aggiungi un widget, se necessario.

**In questa guida:** Scopri come definire un&#39;azione, implementarne il gestore e connettere un widget facoltativo. Verifica l’azione a livello locale, distribuisci l’app e prova l’azione in una piattaforma LLM supportata.

Per la prima app, inizia con [Configurazione infrastruttura](/help/guides/create-app.md), quindi [Crea azioni](/help/guides/creating-actions.md). La guida alle azioni Crea spiega la generazione automatica da un sito web e la creazione manuale.

## Prima di iniziare

Hai bisogno di:

- Un&#39;app LLM esistente.
- Un archivio di gestori collegato.
- Clone locale del repository con le relative dipendenze installate.
- Un progetto EDS se l’azione visualizza un widget.
- Un’API o un’origine dati chiara per i risultati di produzione.

## Pianificare l’azione

Progetta ogni azione intorno a un’attività utente chiara. Prima di aprire l’interfaccia utente, definisci:

- **Intenzione**: ciò che l&#39;utente sta tentando di realizzare.
- **Descrizione** — quando selezionare l&#39;azione.
- **Input**: informazioni minime richieste all&#39;utente.
- **Risultato**: il testo e i dati strutturati restituiti dal gestore.
- **Comportamento**: se l&#39;azione legge dati, modifica dati o chiama sistemi esterni.
- **Widget** — indica se il risultato richiede un&#39;interfaccia visiva.

Ad esempio, utilizza questo piano per un&#39;azione **Cerca prodotti**:

```text
Intent: Find products matching a category or search phrase
Inputs:
  category: optional string
  query: optional string
Result:
  content: text summary
  structuredContent: products and total count
Behavior: read-only, idempotent, open-world
Widget: product cards
```

Mantieni separate le attività correlate ma diverse. Utilizza azioni separate per la ricerca dei prodotti e l’acquisto dei prodotti, in quanto le attività hanno requisiti diversi in termini di input, rischi e conferma.

## Creare i metadati dell’azione

Apri l&#39;app e seleziona **[!UICONTROL Azioni]**, quindi seleziona **[!UICONTROL Crea azione]**.

La finestra di dialogo **[!UICONTROL Crea nuova azione]** contiene quattro schede: **[!UICONTROL Dettagli azione]**, **[!UICONTROL Dettagli widget LLM]**, **[!UICONTROL Approfondimenti applicazione]** ed **[!UICONTROL Estensioni ChatGPT]**. La finestra di dialogo si apre su **[!UICONTROL Dettagli azione]**. L’editor delle azioni salvate utilizza le stesse schede.

Le impostazioni facoltative delle estensioni **[!UICONTROL ChatGPT]** consentono alle persone di aprire direttamente l&#39;app, utilizzare un visualizzatore di file personalizzato o trovare risorse nel compositore sui client supportati. Configura questi quando l&#39;azione corrispondente e il comportamento del widget sono pronti. Consulta [Configurare le estensioni ChatGPT](/help/guides/chatgpt-extensions.md).

### Immettere le informazioni sull&#39;azione e i parametri di input

![Crea nuova azione - Dettagli azione con il nome dell&#39;azione, l&#39;identificatore del codice, la descrizione e due parametri di input](/help/assets/guide-create-action/action-basic-info.png)

In **[!UICONTROL Informazioni azione]**, immettere:

- **[!UICONTROL Nome azione]**: nome breve dell&#39;attività, ad esempio *Prodotti di ricerca*.
- **[!UICONTROL Descrizione]**: spiega quando utilizzare l&#39;azione e cosa restituisce.

La finestra di dialogo deriva un **[!UICONTROL identificatore di codice]** dal nome, ad esempio `search-products`. L’identificatore viene corretto dopo la creazione e deve essere univoco all’interno dell’app. Utilizza l’identificatore esatto della cartella del gestore.

Una descrizione utile è specifica:

```text
Search the product catalog by category or keyword. Returns matching
products with their names, prices, categories, and image URLs.
```

Evita descrizioni vaghe come *Ottiene informazioni sul prodotto*. La piattaforma LLM utilizza la descrizione per scegliere tra le azioni.

In **[!UICONTROL Parametri di input]**, selezionare **[!UICONTROL Aggiungi parametro]** una volta per ogni valore accettato dal gestore. Per ogni parametro, immetti:

- **[!UICONTROL Nome parametro]**: la chiave ricevuta dal gestore.
- **[!UICONTROL Tipo]**: stringa, numero, numero intero, booleano, oggetto o file.
- **[!UICONTROL Obbligatorio]** — indica se l&#39;azione richiede il valore.
- **[!UICONTROL Descrizione]** — come estrarre il valore.

Per **Prodotti Di Ricerca**:

```text
category
  Type: String
  Required: No
  Description: Product category used to narrow the catalog.

query
  Type: String
  Required: No
  Description: Product name or search phrase.
```

Utilizza nomi di parametri stabili. La modifica di un nome richiede anche la modifica del gestore e dei relativi test.

### Configurare le impostazioni LLM

Espandere **[!UICONTROL Impostazioni LLM]**. La sezione viene compressa per impostazione predefinita.

![Crea nuova azione - Impostazioni LLM con annotazioni, testi di stato metadati OpenAI e visibilità](/help/assets/guide-create-action/action-llm-settings.png)

In **[!UICONTROL Annotazioni]**, seleziona solo gli hint che sono true per l&#39;azione:

- **[!UICONTROL Suggerimento distruttivo]**: l&#39;azione può eliminare o modificare definitivamente i dati.
- **[!UICONTROL Idempotent (stessi argomenti = nessun effetto aggiuntivo)]** — la ripetizione della stessa richiesta ha lo stesso effetto.
- **[!UICONTROL Open world hint]**: l&#39;azione comunica con sistemi esterni.
- **[!UICONTROL Suggerimento di sola lettura]**: l&#39;azione non modifica i dati.

Ad esempio, la ricerca di prodotti è normalmente di sola lettura, idempotente e open-world.

In **[!UICONTROL Metadati OpenAI]**, immettere brevi messaggi visualizzati durante l&#39;esecuzione dell&#39;azione e al suo completamento, ognuno contenente un massimo di 64 caratteri:

```text
Invoking status text: Searching products...
Invoked status text: Products found
```

In **[!UICONTROL Visibilità]**:

- **[!UICONTROL Esposizione al modello di IA]** consente al modello di selezionare l&#39;azione.
- **[!UICONTROL Mostra come widget nella superficie dell&#39;app]** mostra il widget configurato.

Visibilità non sceglie il tipo di widget. Per un&#39;azione solo testo, selezionare **[!UICONTROL Nessuna interfaccia utente interattiva]** in **[!UICONTROL Dettagli widget LLM]** > **[!UICONTROL Tipo widget]**.

### Configurare Application Insights {#configure-analytics}

Apri **[!UICONTROL Application Insights]**. **[!UICONTROL L&#39;opzione Raccogli intento utente]** è selezionata per impostazione predefinita: la piattaforma LLM crea un riepilogo breve e anonimo di ciò che l&#39;utente stava tentando di eseguire durante l&#39;esecuzione dell&#39;azione.

Se configuri [!DNL Adobe Analytics] o [!DNL Customer Journey Analytics], viene inviato anche l&#39;intento utente. Vedere [Configurazione di Adobe Analytics e Customer Journey Analytics](/help/guides/analytics-setup.md).

Rivedi le istruzioni sotto la casella di controllo, che guida il riepilogo. Limitali a finalità non sensibili e anonime e descrivi la raccolta di dati nell’informativa sulla privacy. Le istruzioni accettano fino a 2.000 caratteri. Cancella **[!UICONTROL Raccogli l&#39;intento dell&#39;utente]** di non raccogliere alcun riepilogo.

![Crea nuova azione - Approfondimenti applicazione con raccolta intento utente e istruzioni predefinite](/help/assets/guide-create-action/action-analytics-user-intent.png)

Per le definizioni complete dei campi, vedere [Campi azione e widget](/help/reference/reference-docs.md).

## Configurare il widget

Apri **[!UICONTROL Dettagli widget LLM]**. Per eseguire un&#39;azione solo testo, selezionare **[!UICONTROL Nessuna interfaccia utente interattiva]** in **[!UICONTROL Tipo widget]** e ignorare le altre impostazioni del widget.

![Crea nuova azione - Dettagli widget LLM con blocco Edge Delivery Services selezionato e URL modello di esempio](/help/assets/guide-create-action/widget-metadata.png)

In **[!UICONTROL Informazioni widget]**, configura:

- **[!UICONTROL Tipo widget]** — selezionare **[!UICONTROL Blocco Edge Delivery Services]** per un widget interattivo.
- **[!UICONTROL Dominio widget]**: origine della sandbox del widget. L’interfaccia utente lo identifica come richiesto per l’invio dell’app e come univoco per app; non è lo stesso dell’URL della pagina EDS.
- **[!UICONTROL Descrizione del widget]** - il contenuto del modello dopo il rendering del widget, fino a 512 caratteri.

La descrizione del widget è diversa dalla descrizione dell&#39;azione:

- **Descrizione azione** consente al modello di decidere quando richiamare l&#39;azione.
- **La descrizione del widget** è mappata a `_meta["openai/widgetDescription"]` e riepiloga ciò che viene visualizzato dal componente sottoposto a rendering, riducendo la narrazione ripetuta.

[!DNL LLM Apps] applica la descrizione del widget come metadati del componente. Non restituire la descrizione dal gestore.

Espandi **[!UICONTROL URL modello]** e immetti:

- **[!UICONTROL URL script]**: il punto di ingresso del widget EDS.
- **[!UICONTROL URL widget]**: la pagina EDS pubblicata per questa azione.

Gli URL tipici sono:

```text
Script URL:
https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js

Widget URL:
https://main--<repo>--<owner>.aem.live/<widget-page>
```

In **[!UICONTROL Configurazione CSP]**, aggiungi solo le origini necessarie al widget per le risorse e le richieste di rete. Un widget che carica risorse esterne potrebbe non eseguire il rendering se la relativa CSP è incompleta.

![Crea nuova azione - Configurazione CSP con elenchi di domini di URI di base, di risorse, di connessione, di frame e di reindirizzamento](/help/assets/guide-create-action/widget-csp.png)

Se il progetto EDS o la pagina del widget non esiste ancora, completa [Porta il tuo progetto EDS](/help/guides/bring-your-own-eds.md), quindi torna all&#39;azione.

## Salva l’azione

Seleziona **[!UICONTROL Crea nuova azione]**. L&#39;azione viene visualizzata nella pagina Azioni con un contrassegno **Non distribuito**.

Il salvataggio crea i metadati, ma l’azione richiede ancora un gestore.

Quando riapri l’azione salvata, l’editor mostra le stesse schede. Per salvare le modifiche successive, selezionare **[!UICONTROL Salva modifiche]**.

## Implementare il gestore

Clona l’archivio del gestore collegato e installane le dipendenze:

```bash
npm install
```

Crea:

```text
actions/
└── search-products/
    └── index.js
```

Il nome della cartella deve corrispondere all&#39;identificatore di codice dell&#39;azione visualizzato nell&#39;editor delle azioni.

Per il contratto del risultato completo e la relazione handler-widget, vedere [Personalizzare un handler generato](/help/guides/customize-handler.md).

### Contratto gestore

Esporta una funzione asincrona:

```javascript
module.exports = async (args) => {
  return {
    content: [
      { type: 'text', text: 'Response for the LLM platform.' }
    ],
    structuredContent: {
      // Data for the widget.
    }
  };
};
```

Il gestore riceve i parametri definiti nell’interfaccia utente.

### Restituisce `content`

La piattaforma LLM legge `content` come fallback del testo:

```javascript
content: [
  { type: 'text', text: 'Found 3 matching products.' }
]
```

Restituisce sempre `content` utile, anche quando l&#39;azione ha un widget.

### Restituisce `structuredContent`

Il widget utilizza `structuredContent` come oggetto normale:

```javascript
structuredContent: {
  products: [
    { id: 'P-100', name: 'Product A', price: '$20' }
  ],
  total: 1
}
```

La forma deve corrispondere a quanto letto dal blocco EDS da `bridge.toolResult`.

### Connettere un’API

Mantieni le chiamate API nel gestore lato server. Questo esempio chiama un’API di prodotto pubblica. Configura l&#39;origine HTTPS dell&#39;API in una variabile [app](/help/guides/app-variables.md) denominata `PRODUCT_API_ORIGIN`. L&#39;esempio accetta i parametri `query` e `category` definiti in precedenza, richiede almeno un parametro e non richiede credenziali.

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ query = '', category = '' } = {}, extra) => {
  const normalizedQuery = typeof query === 'string' ? query.trim() : '';
  const normalizedCategory = typeof category === 'string' ? category.trim() : '';
  if (typeof query !== 'string' || typeof category !== 'string'
      || (!normalizedQuery && !normalizedCategory)
      || normalizedQuery.length > 200 || normalizedCategory.length > 100) {
    return {
      isError: true,
      content: [{ type: 'text', text: 'Enter a valid product search.' }],
      structuredContent: { products: [], total: 0 }
    };
  }

  const apiOrigin = getVariable(extra, 'PRODUCT_API_ORIGIN');
  if (!apiOrigin) {
    throw new Error('Product API configuration is unavailable.');
  }

  const origin = new URL(apiOrigin);
  if (origin.protocol !== 'https:') {
    throw new Error('Product API configuration must use HTTPS.');
  }

  const url = new URL('/v1/products', origin);
  if (normalizedQuery) {
    url.searchParams.set('query', normalizedQuery);
  }
  if (normalizedCategory) {
    url.searchParams.set('category', normalizedCategory);
  }

  const response = await fetch(url, {
    signal: AbortSignal.timeout(8000)
  });

  if (!response.ok) {
    throw new Error('Product service request failed.');
  }

  const payload = await response.json();
  if (!payload || !Array.isArray(payload.products)
      || !payload.products.every((product) =>
        product
        && typeof product.id === 'string'
        && typeof product.name === 'string'
        && typeof product.price === 'string')) {
    throw new Error('Product service returned an unexpected response.');
  }

  const products = payload.products.map((product) => ({
    id: product.id,
    name: product.name,
    price: product.price
  }));

  return {
    content: [
      { type: 'text', text: `Found ${products.length} matching products.` }
    ],
    structuredContent: {
      products,
      total: products.length
    }
  };
};
```

Non inserire credenziali API nel codice sorgente, nei metadati di azioni, nel widget JavaScript, nei registri o in errori rivolti all’utente. L&#39;interfaccia utente di **[!UICONTROL Variabili e segreti]** supporta attualmente solo variabili non sensibili, non segreti. Non memorizzare un token API in una variabile. Se l’API a monte richiede delle credenziali, disponi un meccanismo di credenziali lato server approvato prima di connetterla.

Per il codice di produzione, convalidare la risposta upstream completa prima di mappare i campi approvati in `structuredContent`.

## Aggiungi test del gestore

Crea il test corrispondente:

```text
test/
└── actions/
    └── search-products.test.js
```

Prova almeno:

- Input valido.
- Input mancante o non valido.
- Risultati vuoti.
- Timeout o errore API.
- Dati API in formato non valido.
- Forma `structuredContent` prevista dal widget.

Esegui:

```bash
npm test
```

Per il layout del progetto e il test MCP locale, vedere [Sviluppo e test del gestore locale](/help/reference/development.md).

## Verifica l’azione a livello locale

Esegui:

```bash
npm run dev:local
```

Senza un `actions.json` locale, il server rileva il gestore con metadati minimi e nessuna convalida dello schema di input.

Utilizzare MCP Inspector o `curl` per:

1. Elencare le azioni registrate.
2. Chiama la nuova azione con argomenti rappresentativi.
3. Verificare `content` e `structuredContent`.
4. Verifica richieste non valide e vuote.

## Connetti e verifica il widget

Se l’azione ha un widget:

1. Fare in modo che il widget legga `structuredContent` del gestore.
2. Eseguire il rendering di valori esterni con API DOM sicure come `textContent`.
3. Aggiungi stati di caricamento, vuoto ed errore.
4. Visualizzare localmente l&#39;anteprima della pagina EDS.
5. Verifica gli URL CSP, CORS e widget.

Vedi [Porta il tuo progetto EDS](/help/guides/bring-your-own-eds.md).

## Distribuzione e test

1. Esegui il commit e invia le modifiche al gestore e al widget.
2. [Distribuisci l&#39;app](/help/guides/deploy-your-app.md) nell&#39;area di visualizzazione.
3. Verifica l&#39;app in [ChatGPT](/help/guides/test-in-chatgpt.md) o [Claude](/help/guides/test-in-claude.md).
4. Verifica la selezione dell’azione con prompt corrispondenti all’azione e prompt non correlati che non la richiedono.
5. Dopo che lo staging ha esito positivo, distribuisci in produzione.

Se esistono metadati senza un gestore corrispondente, la distribuzione registra l’azione con uno stub predefinito. Aggiungi il gestore prima di rendere l’azione disponibile agli utenti.
