---
title: Creare un’azione
description: Scopri come definire un’azione nell’interfaccia utente delle app LLM, inclusi metadati, parametri di input e configurazione dei widget.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '868'
ht-degree: 1%

---


# Creare un’azione

Questa guida illustra come definire un&#39;azione nell&#39;interfaccia utente di [!DNL LLM Apps]. Per informazioni generali sulle azioni e sul loro funzionamento, consulta [Concetti di base](/help/overview/overview.md#actions).

## Aprire la pagina Azioni

Passa a **[!UICONTROL Azioni]** nella barra laterale a sinistra oppure fai clic su **Vai a Azioni** nella pagina Dettagli app. Se non esiste ancora alcuna azione, la pagina mostra uno stato vuoto.

![Pagina Azioni — ancora nessuna azione](/help/assets/guide-create-action/actions-empty.png)

Fare clic su **+ Crea azione** per aprire la finestra di dialogo a schermo intero.

## Schede delle azioni

Ogni azione viene visualizzata come una scheda che mostra:

- Azione **name** e **description**
- Un&#39;immagine di anteprima **widget**, generata automaticamente dal widget, che mostra l&#39;aspetto dell&#39;output dell&#39;azione nella piattaforma LLM
- **Badge**: tipo di widget (**[!UICONTROL EDS]**), stato distribuzione (**Non distribuito**, **Distribuito nell&#39;area di gestione temporanea**, **Distribuito nell&#39;area di produzione**), **Modifiche non distribuite** quando l&#39;azione è stata modificata dall&#39;ultima distribuzione e numero di parametri
- Un interruttore **Visibilità**: abilita o disabilita l&#39;azione sull&#39;endpoint live senza ridistribuirla
- Un collegamento **Rivedi** nell&#39;angolo in alto a destra per aprire l&#39;editor azioni

![Pagina Azioni: schede delle azioni](/help/assets/guide-create-action/action-card.png)

Quando una o più azioni sono state modificate dopo l&#39;ultima distribuzione, nella parte superiore della pagina Azioni viene visualizzato un banner **Distribuzione richiesta**. Ridistribuisci l’app per applicare le modifiche.

## Scheda Azione

La finestra di dialogo contiene due schede: **Azione** e **[!UICONTROL Metadati widget]**.

### Informazioni di base

![Crea azione — informazioni di base](/help/assets/guide-create-action/action-basic-info.png)

- **Nome azione** (obbligatorio): l&#39;identificatore dell&#39;azione, ad esempio *Cerca prodotti*.
- **Descrizione** (obbligatorio): spiegazione chiara delle operazioni dell&#39;azione. La piattaforma LLM utilizza questa funzione per decidere quando richiamare l’azione. Ad esempio: *Cerca nel catalogo prodotti per parola chiave. Restituisce prodotti corrispondenti con nome, categoria, immagine e prezzo.*
- **Annotazioni** — suggerimenti facoltativi che descrivono il comportamento dell&#39;azione:

  | Annotazione | Descrizione |
  |-----------|-------------|
  | **Suggerimento distruttivo** | L’azione modifica o elimina dati |
  | **Idempotent** | Se si richiama l&#39;azione più volte con gli stessi argomenti, si ottiene lo stesso risultato |
  | **Apri hint mondo** | L’azione interagisce con sistemi esterni |
  | **Suggerimento di sola lettura** | L’azione legge solo i dati, non scrive mai |

  Per ulteriori dettagli, vedere [Riferimento: campi metadati](/help/reference/reference-docs.md).

### Metadati OpenAI

- **Richiamo del testo di stato**: il messaggio visualizzato nella piattaforma LLM durante l&#39;esecuzione dell&#39;azione (massimo 64 caratteri). Esempio: *Caricamento prodotti in corso...*
- **Testo di stato richiamato**: il messaggio visualizzato al termine dell&#39;azione (massimo 64 caratteri). Esempio: *Prodotti caricati.*

### Parametri di visibilità e input

**Visibilità** controlla dove è disponibile l&#39;azione:

- **Esposizione a modello di IA**: l&#39;azione può essere richiamata dal modello di IA.
- **Mostra come widget nella superficie dell&#39;app**. L&#39;azione esegue il rendering di un widget visivo.

I **parametri di input** sono i valori che la piattaforma LLM invia al gestore. Il modello le estrae automaticamente dal messaggio dell&#39;utente. Per *Search Products* si definiscono:

- **categoria** (stringa, facoltativo): filtro categoria per limitare i risultati (ad esempio, un tipo di prodotto o un reparto).
- **query** (stringa, facoltativo): termine di ricerca a testo libero.

Ogni parametro ha un **Nome**, **Tipo** (Stringa, Numero, Numero intero, Booleano), **Descrizione** e una casella di controllo **Obbligatorio**. Fare clic su **+ Aggiungi** per aggiungere altri parametri.

Per ulteriori dettagli, vedere [Riferimento: Parametri azione](/help/reference/reference-docs.md).

### Analisi

![Crea azione - Intento utente di Analytics](/help/assets/guide-create-action/action-analytics-user-intent.png)

- **Intento utente** — quando abilitato, a [!DNL ChatGPT] viene richiesto di riepilogare la conversazione che ha portato alla chiamata di questa azione. Tale riepilogo viene raccolto e visualizzato in Analytics, fornendo insight in cosa gli utenti cercavano di eseguire quando l’azione veniva attivata.

## Scheda Metadati widget

Questa scheda configura il rendering della risposta visiva dell’azione nella piattaforma LLM. Per una spiegazione completa del funzionamento dei widget, vedere [Guida: configurazione del widget (EDS)](/help/guides/widgets.md).

![Crea azione — metadati widget](/help/assets/guide-create-action/widget-metadata.png)

### Informazioni widget

- **Tipo** — tecnologia widget (attualmente **[!UICONTROL EDS]**).
- **Dominio widget (origine sandbox)**: origine in cui è ospitato il widget. Obbligatorio per l’invio dell’app a OpenAI; deve essere univoco per app.
- **Preferisce il bordo** — esegue il rendering del widget all&#39;interno di una scheda con bordi.

### URL modello

- **[!UICONTROL URL script]**: il punto di ingresso che avvia il widget, condiviso tra tutte le azioni:
  `https://main--<repo>--<owner>.aem.live/scripts/aem-embed.js`
- **URL di incorporamento widget** — la pagina EDS per questa azione specifica:
  `https://main--<repo>--<owner>.aem.live/eds-widgets/<action-name>`

### Autorizzazioni

API hardware e browser a cui il widget può accedere:

| Autorizzazione | Descrizione |
|-----------|-------------|
| **Fotocamera** | Accedere alla fotocamera del dispositivo |
| **Microfono** | Accedere al microfono del dispositivo |
| **Geolocalizzazione** | Accedere al percorso dell&#39;utente |
| **Appunti** | Leggi o scrivi negli Appunti |

### Configurazione CSP

![Crea azione — autorizzazioni e CSP](/help/assets/guide-create-action/widget-permissions-csp.png)

Controlla quali domini esterni l&#39;iframe del widget può contattare. Ogni dominio esterno deve essere inserito nell&#39;elenco Consentiti esplicitamente.

| Direttiva | Descrizione |
|-----------|-------------|
| **Domini risorse** | Domini per risorse statiche: immagini, font, script, stili |
| **Connetti domini** | Domini che il widget può contattare tramite `fetch`, `XHR` o `WebSocket` |
| **Domini frame** | Le origini consentite per gli iframe nidificati; l’aggiunta di voci attiva una revisione app più rigorosa da OpenAI |
| **Domini di reindirizzamento** | Destinazioni attendibili per `openExternal` collegamenti di reindirizzamento ([!DNL ChatGPT] specifici) |
| **Domini URI di base** | Direttiva CSP `base-uri` (solo applicazioni MCP SDK, non supportata da [!DNL ChatGPT]) |

Fai clic su **Crea nuova azione** per salvare.

## Dopo aver creato un’azione

L&#39;azione viene visualizzata come una scheda nella pagina Azioni:

![Pagina Azioni — azione creata](/help/assets/guide-create-action/actions-with-action.png)

Ogni scheda mostra il nome dell&#39;azione, la descrizione, il badge del tipo (**[!UICONTROL EDS]**), lo stato della distribuzione (**Non distribuito**) e il conteggio dei parametri. È possibile fare clic su **...** per modificare o eliminare oppure fare clic su **Rivedi** per verificare la configurazione.

![Dettagli app — non distribuiti](/help/assets/guide-create-action/app-detail-not-deployed.png)

I metadati dell&#39;azione vengono salvati, ma non è ancora stato distribuito alcun codice. Per rendere l’azione funzionale, è necessario:

1. **Configurare il widget EDS**. Vedere [Guida: configurare il widget (EDS)](/help/guides/widgets.md).
2. **Scrivere il gestore**. Vedere [Guida: scrivere il gestore azioni](/help/guides/write-action-handler.md).
3. **[!UICONTROL Distribuisci]** — vedere [Guida: distribuire l&#39;app](/help/guides/deploy-your-app.md).

## Passaggi successivi

- [Guida: configurazione del widget (EDS)](/help/guides/widgets.md)

