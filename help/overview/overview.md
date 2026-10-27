---
title: Panoramica delle app Adobe LLM
description: Scopri cos’è un’app Adobe LLM, come funziona e come iniziare a creare la tua app.
source-git-commit: d37cb1f4e199d77c94def7a034a55598bd3d8d83
workflow-type: tm+mt
source-wordcount: '624'
ht-degree: 0%
---

# App Adobe LLM: panoramica {#adobe-llm-apps-an-overview}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

## Cos&#39;è [!DNL Adobe LLM Apps]?

[!DNL Adobe LLM Apps] consente al tuo marchio di offrire azioni utili, come l&#39;individuazione dei prodotti, i controlli di disponibilità o le prenotazioni dei servizi, all&#39;interno degli assistenti AI supportati come [!DNL ChatGPT] e [!DNL Claude].

>[!VIDEO](https://video.tv.adobe.com/v/3504881/?captions=ita&learn=on&enablevpops)

[!DNL LLM Apps] è disponibile in [experience.adobe.com/llm-apps](https://experience.adobe.com/llm-apps).


## Operazioni possibili con [!DNL LLM Apps]

- **Creare azioni LLM di proprietà del brand** — Definire i flussi di business specifici che si desidera attivare negli assistenti AI (ad esempio, *Pianificare un&#39;unità di test*, *Confrontare i prodotti*, *Prenotare un servizio*).
- **Creare widget LLM interattivi**: creare componenti dell&#39;interfaccia utente visiva (schede prodotto, moduli di prenotazione, localizzatori di store) gestiti come componenti AEM nell&#39;archivio [!DNL GitHub].
- **Gestione centralizzata della governance del brand**: autori e sviluppatori mantengono il controllo completo su tutti i contenuti, le copie e gli elementi visivi esposti all&#39;interno della piattaforma LLM, con approvazioni gestite tramite AEM.
- **Distribuzione a staging e produzione**: una pipeline di distribuzione controllata consente di testare l&#39;esperienza in un ambiente di staging prima di passare alla produzione.
- **Controlla la visibilità a livello di azione** — abilita o disabilita singole azioni, quindi distribuisci l&#39;app per applicare la modifica.
- **Misura le decisioni che determinano**: conteggi dei trigger delle azioni di superficie incorporati in Analytics (basati su Adobe Customer Journey Analytics), tassi di successo, tassi di abbandono, prompt degli utenti principali e punteggi di visibilità.

## Perché [!DNL LLM Apps] conta

Le interazioni LLM sono fondamentalmente diverse dalla ricerca tradizionale. La durata media della sessione LLM è quattro volte superiore a quella di una sessione di ricerca tradizionale. Oltre il 40% dei consumatori si affida agli strumenti di intelligenza artificiale per decisioni di acquisto complesse. Senza [!DNL LLM Apps], potresti vincere la menzione ma perdere il cliente. [!DNL LLM Apps] assicura che il tuo marchio non sia solo visibile ma utilizzabile nel momento esatto in cui un utente è pronto a decidere.

## Concetti fondamentali {#key-concepts}

### App LLM

Il tuo assistente personalizzato con cui gli utenti interagiscono all&#39;interno di [!DNL ChatGPT] o altre piattaforme LLM. Raggruppa tutte le azioni e le distribuisce come una singola unità.

### Azione {#actions}

Una funzionalità offerta dalla tua app, ad esempio *Trova un distributore* o *Sfoglia prodotti*. La piattaforma LLM richiama un’azione quando una richiesta corrisponde alla relativa descrizione. [!DNL LLM Apps] gestisce i metadati delle azioni e l&#39;archivio [!DNL GitHub] memorizza il codice del gestore.

### Gestore azioni

La funzione lato server che viene eseguita quando la piattaforma LLM richiama un&#39;azione. Utilizza il gestore per convalidare l’input, chiamare le API e restituire testo e dati strutturati.

### Widget {#widgets-eds}

La risposta visiva mostrata con la risposta di LLM, ad esempio una scheda, un carosello o una tabella. I widget generati sono blocchi in un archivio [!DNL Edge Delivery Services] (EDS). Con l’infrastruttura gestita di Adobe, Adobe prepara e ospita l’archivio per te. In alternativa, puoi collegare il tuo archivio.

### Server MCP

Endpoint esposto dopo la distribuzione. Una piattaforma LLM supportata si connette a questo endpoint per individuare e richiamare le azioni.

## Come funziona

Puoi definire le funzionalità del tuo marchio in [!DNL LLM Apps]. La piattaforma rende tali funzionalità disponibili come azioni e un assistente AI utilizza le azioni per rispondere alle richieste dei clienti nella conversazione.

```
┌────────────────────┐          ┌────────────────────┐          ┌────────────────────┐
│     Your brand     │          │      LLM Apps      │          │    AI assistant    │
│                    │          │                    │          │                    │
│   What you offer   │   ───▶   │  Turns that into   │   ───▶   │ Answers with your  │
│  and how you help  │          │    something AI    │          │    brand, live     │
│  customers today   │          │     can act on     │          │  inside the chat   │
└────────────────────┘          └────────────────────┘          └────────────────────┘
```

Per informazioni tecniche su cosa generare e come funzionano i componenti insieme, vedi [Collegamento tra app](/help/guides/app-architecture.md).

## Introduzione {#choose-your-journey}

La creazione di un’app e delle relative azioni è un passaggio separato:

1. [Configura l&#39;infrastruttura](/help/guides/create-app.md). Utilizza l&#39;infrastruttura gestita di Adobe per una configurazione rapida oppure scegli di utilizzare i tuoi archivi [!DNL GitHub].
2. [Crea le tue azioni](/help/guides/creating-actions.md). Chiedi alla piattaforma di analizzare il tuo sito web e generare azioni, o creale tu stesso.
3. [Distribuisci e verifica l&#39;app](/help/guides/deploy-your-app.md). Rivedi il lavoro generato, distribuiscilo nell’ambiente di staging e testa in una piattaforma LLM supportata.

La generazione automatica offre un punto di partenza con un comportamento di esempio. Prima di rendere l&#39;app disponibile ai clienti, [connetti i gestori ai sistemi](/help/guides/customize-handler.md) e [personalizza i widget](/help/guides/widgets.md).

Per creare rapporti sull&#39;app nel tuo [!DNL Adobe Analytics] o [!DNL Customer Journey Analytics], vedi [Configurare Adobe Analytics e Customer Journey Analytics](/help/guides/analytics-setup.md).
