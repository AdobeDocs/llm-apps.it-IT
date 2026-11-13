---
title: Campi azione e widget
description: Definizioni dei campi per metadati di azioni, parametri, widget, CSP e autorizzazioni nelle app Adobe LLM.
source-git-commit: 9b2ec369baba5d533088d2dd4adefcb80f5c9727
workflow-type: tm+mt
source-wordcount: '1273'
ht-degree: 2%
---

# Campi azione e widget {#action-widget-configuration}

>[!IMPORTANT]
>
>Le app LLM sono disponibili per tutti i clienti di AEM Sites Cloud Service che desiderano sviluppare e gestire le proprie app. Una volta avviato il conteggio, ogni chiamata all’app verrà conteggiata nelle richieste di contenuto AEM Sites del cliente. Adobe annuncerà il tasso di misurazione e la data di inizio prima che il conteggio abbia effetto.

Utilizza questa pagina per cercare i campi nell’editor delle azioni. Per i passaggi di creazione, consulta [Creare un&#39;azione da zero](/help/guides/create-action.md).

## Trova le impostazioni

La finestra di dialogo **[!UICONTROL Crea nuova azione]** contiene **[!UICONTROL Action]**, **[!UICONTROL Widget Metadata]** e **[!UICONTROL ChatGPT Extensions]** schede.

Dopo la creazione, l’editor delle azioni salvate utilizza quattro schede:

| Scheda | Impostazioni |
|---|---|
| **[!UICONTROL Dettagli widget LLM]** | Tipo di widget, dominio sandbox, descrizione del widget, URL di modelli e CSP |
| **[!UICONTROL Dettagli azione]** | Identificatore di codice, descrizione, parametri di input e **[!UICONTROL impostazioni LLM]**, incluse annotazioni, testo dello stato di chiamata e visibilità |
| **[!UICONTROL Analytics]** | Raccolta e istruzioni per l’intento dell’utente |
| **[!UICONTROL Estensioni ChatGPT]** | Punti di ingresso con apertura diretta, icone di azione, preferenze di visualizzazione e menzioni del compositore |

Per salvare le modifiche, seleziona **[!UICONTROL Salva modifiche]**. Le azioni generate in attesa di revisione mostrano invece **[!UICONTROL Contrassegna come riviste]**, che unisce anche le richieste pull o i rami generati.

## Parametri azione

I parametri di input sono i valori che la piattaforma LLM invia al gestore delle azioni. Il modello estrae i valori dal messaggio dell&#39;utente e li mappa sui parametri configurati.

| Proprietà | Descrizione |
|----------|-------------|
| **Nome** | Identificatore del parametro (ad esempio, `category`, `query`) |
| **Tipo** | `String`, `Number`, `Integer`, `Boolean` o `Object`; l&#39;editor salvato offre anche `File` per i parametri di file caricati |
| **Descrizione** | Spiegazione leggibile: la piattaforma LLM utilizza la spiegazione per estrarre il valore corretto |
| **Obbligatorio** | Se questa opzione è selezionata, il modello deve fornire questo parametro prima di richiamare l’azione |

### Parametri del file

I parametri di file sono nomi di campi di input configurati nell’editor delle azioni. Quando un utente carica un file, l&#39;host fornisce un oggetto file per tali argomenti, in genere inclusi `download_url` e `file_id`.

I parametri del file caricato sono diversi da [Punti di ingresso del file](/help/guides/chatgpt-extensions.md#file-entrypoint). Un elemento Entrypoint File utilizza un parametro Object denominato `file`, contenente i `name` e i `resourceUri` del file aperto.

## Campi metadati

### Informazioni di base

| Campo | Obbligatorio | Descrizione |
|-------|----------|-------------|
| **Nome azione** | Sì | Nome visualizzato per l&#39;azione (ad esempio, *Cerca prodotti*) |
| **Descrizione** | Sì | Spiegazione delle operazioni dell&#39;azione: la piattaforma LLM utilizza la spiegazione per decidere quando invocare l&#39;azione |

La finestra di dialogo per la creazione visualizza in anteprima un **identificatore di codice** immutabile derivato dal nome dell&#39;azione. È univoco all’interno dell’app e contiene fino a 63 caratteri, a partire da una lettera minuscola e utilizzando lettere minuscole, cifre, trattini o trattini bassi. L&#39;azione viene mappata su `actions/<code-identifier>/index.js` nell&#39;archivio del gestore.

### Annotazioni

Hint facoltativi che descrivono il comportamento dell’azione:

| Annotazione | Descrizione |
|------------|-------------|
| **Suggerimento distruttivo** | L’azione può eliminare o sovrascrivere dati |
| **Idempotent (stessi argomenti = nessun effetto aggiuntivo)** | La ripetizione della stessa richiesta non ha alcun effetto aggiuntivo sull’ambiente; le richieste ripetute non restituiscono necessariamente dati identici |
| **Apri hint mondo** | L’azione interagisce con sistemi esterni |
| **Suggerimento di sola lettura** | L’azione legge solo i dati, non scrive mai |

### Metadati OpenAI

| Campo | Lunghezza massima | Descrizione |
|-------|------------|-------------|
| **Richiamo del testo di stato** | 64 caratteri | Messaggio visualizzato nella piattaforma LLM durante l&#39;esecuzione dell&#39;azione (ad esempio, *Caricamento prodotti ...*) |
| **Testo di stato richiamato** | 64 caratteri | Messaggio visualizzato al termine dell&#39;azione (ad esempio, *Prodotti caricati ...*) |
| **Descrizione widget** | 512 caratteri | Viene mappato su `_meta["openai/widgetDescription"]`; riepiloga il componente sottoposto a rendering per il modello e riduce i commenti ripetuti |

La descrizione dell&#39;azione controlla quando il modello seleziona l&#39;azione. La descrizione del widget spiega cosa viene visualizzato dal componente dopo il rendering.

### Visibilità

Per attivare o disattivare un&#39;azione, usa l&#39;opzione **[!UICONTROL Visibilità]** nella pagina dell&#39;app. Lo switch controlla se la distribuzione successiva include l’azione. L’editor azioni non dispone di un interruttore on/off separato.

Le seguenti impostazioni dell’editor di azioni controllano l’esposizione al modello e al widget, non lo stato di attivazione/disattivazione a livello di app:

| Attiva/disattiva | Descrizione |
|--------|-------------|
| **Esposizione a modello di IA** | L’azione può essere richiamata dal modello di intelligenza artificiale durante le conversazioni |
| **Mostra come widget nella superficie dell&#39;app** | L’azione esegue il rendering di un widget visivo nell’app |

Le impostazioni di esposizione del modello e del widget non sostituiscono la scelta di un tipo di widget.

### Analisi

| Campo | Descrizione |
|-------|-------------|
| **Raccogli intento utente** | Abilitato per impostazione predefinita. Richiede un riepilogo anonimo dell’obiettivo che ha portato all’azione per Analytics |
| **Descrizione intento utente** | Istruzioni utilizzate per richiedere il riepilogo, fino a 2.000 caratteri. L&#39;impostazione predefinita esclude le informazioni personali e riservate. Rivedi eventuali personalizzazioni in base all’informativa sulla privacy |

## Campi widget

### Informazioni widget

| Campo | Descrizione |
|-------|-------------|
| **Tipo / Tipo widget** | Nella finestra di dialogo di creazione, seleziona **[!UICONTROL Blocco Edge Delivery Services]** o **[!UICONTROL Nessun widget]**. L&#39;editor salvato contrassegna la scelta di solo testo **[!UICONTROL Nessuna interfaccia utente interattiva]** |
| **Dominio widget** | Origine sandbox utilizzata dall’host. L’interfaccia utente lo identifica come richiesto per l’invio dell’app e come univoco per app. Separato dall&#39;**URL widget** della pagina EDS |
| **Preferisce il bordo** | Richiede un contenitore delimitato; il rendering dipende dall’host. Disponibile nella finestra di dialogo di creazione |

### URL modello

| Campo | Descrizione |
|-------|-------------|
| **[!UICONTROL URL script]** | URL HTTPS per il punto di ingresso EDS `scripts/aem-embed.js`. Condiviso tra le azioni nello stesso progetto EDS |
| **URL widget** | URL HTTPS per la pagina EDS sottoposta a rendering dall’azione. Le azioni generate configurano automaticamente l’URL |

## Configurazione CSP

L’informativa sulla sicurezza dei contenuti controlla quali domini esterni l’iframe del widget può contattare. Ogni dominio esterno deve essere inserito nell&#39;elenco Consentiti esplicitamente.

| Direttiva | Descrizione |
|-----------|-------------|
| **Domini risorse** | Domini per risorse statiche: immagini, font, script, stili |
| **Connetti domini** | Domini che il widget può contattare tramite `fetch`, `XHR` o `WebSocket` |
| **Domini frame** | Le origini sono consentite per gli iframe nidificati; attiva una revisione app più rigorosa |
| **Domini di reindirizzamento** | Destinazioni attendibili per `openExternal` collegamenti di reindirizzamento ([!DNL ChatGPT] specifici) |
| **Domini URI di base** | Direttiva CSP `base-uri` (solo SDK delle app MCP, non [!DNL ChatGPT]) |

## Campi dell’estensione ChatGPT {#chatgpt-extension-fields}

Queste impostazioni facoltative sono solo ChatGPT e non configurano Claude o altre piattaforme LLM. La disponibilità varia in base al client ChatGPT. Per i requisiti e i passaggi, consulta [Configurare le estensioni ChatGPT](/help/guides/chatgpt-extensions.md).

### Punti di ingresso e icone delle azioni

I punti di ingresso richiedono un widget. Selezionate una combinazione di Global, Thread e File per lo stesso widget, oppure lasciate deselezionate tutte le opzioni. Global e Thread richiamano l&#39;azione senza input. File fornisce solo l&#39;oggetto `file`. Altri input richiesti impediscono il funzionamento del punto di ingresso corrispondente. Se File è combinato con Global o Thread, `file` deve essere facoltativo.

| Campo | Descrizione |
|---|---|
| **Dove gli utenti possono aprire questa app** | Caselle di controllo per Globale (schermo intero dalla navigazione), Thread (una scheda di contenuto lato conversazione) e File (un visualizzatore personalizzato per le estensioni di file corrispondenti). È possibile selezionare più tipi |
| **URL icona** | Icona azione opzionale condivisa dai relativi punti di ingresso. Accetta un URL HTTP(S) o un URI di dati; preferisci un URL immagine HTTPS pubblico. La modifica o la cancellazione dell’URL sostituisce tutte le varianti di icona salvate |
| **Estensioni file** | Almeno un&#39;estensione, ad esempio `.csv`, per un punto di ingresso file. Se omesso, l’editor aggiunge un punto iniziale |

Se si seleziona File, viene aggiunto un input Oggetto facoltativo denominato `file`, se mancante. Le definizioni esistenti vengono mantenute e devono accettare `name` e `resourceUri` del file aperto.

### Preferenze visualizzazione

Queste impostazioni del widget sono separate dai punti di ingresso. L’host sceglie la presentazione finale.

| Campo | Scelte e comportamento predefinito |
|---|---|
| **Visualizza quando avviato dal modello** | `inline`, `fullscreen` o **Nessuna preferenza** |
| **Modalità di visualizzazione risorse supportate** | **In linea**, **A schermo intero** o entrambi. Nessuna delle opzioni selezionate lascia attive le impostazioni predefinite host |
| **Visualizzazione risorsa iniziale** | **Impostazione predefinita host**, **In linea** o **A schermo intero**. Quando sono selezionate le modalità supportate, è necessario includere la modalità iniziale |

### Menzioni del compositore

**Abilita menzioni compositore** indica l&#39;azione di ricerca delle risorse dell&#39;app. Solo una azione per app può fornire la ricerca delle menzioni. L&#39;azione deve essere di sola lettura e visibile all&#39;app, con un parametro String denominato `query` che accetta testo vuoto e nessun altro input richiesto. Un provider può essere di solo testo; per abilitare la visibilità dell’app non è necessario configurare un widget per il provider di ricerca.

## Autorizzazioni

Le autorizzazioni dei widget regolano l’accesso all’hardware e alle API del browser. Le autorizzazioni vengono associate ai criteri di autorizzazione per iframe.

I controlli delle autorizzazioni vengono visualizzati in **[!UICONTROL Autorizzazioni (specifiche MCP)]** nella finestra di dialogo di creazione. Concedi solo le funzionalità richieste dal widget; vengono applicati anche il supporto host e il consenso degli utenti.

| Autorizzazione | Descrizione |
|------------|-------------|
| **Fotocamera** | Accedere alla fotocamera del dispositivo |
| **Microfono** | Accedere al microfono del dispositivo |
| **Geolocalizzazione** | Accedere al percorso dell&#39;utente |
| **Appunti** | Leggi o scrivi negli Appunti |
