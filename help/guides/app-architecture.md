---
title: Collegamento di un'app
description: Comprendi le tre parti di un’app LLM, come funzionano in una conversazione e dove apportare modifiche.
source-git-commit: af83670aebde1e6f73585c89a891bc7f5e92fb3a
workflow-type: tm+mt
source-wordcount: '573'
ht-degree: 0%
---

# Collegamento di un&#39;app {#app-architecture}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Un&#39;app **LLM** porta i servizi del tuo marchio in una conversazione AI. Ad esempio, un cliente richiede consigli su caffè, l’app trova prodotti corrispondenti e l’assistente mostra i risultati.

**In questa guida:** scopri le funzioni di ogni parte dell&#39;app, dove apportare modifiche e come tali parti funzionano insieme quando qualcuno utilizza l&#39;app.

## Le tre parti di un’app

Un&#39;app può raggruppare diverse azioni correlate, ad esempio *Sfoglia prodotti*, *Confronta prodotti* e *Trova uno store*. Ogni azione ha le proprie impostazioni, il proprio gestore e un widget facoltativo. L’aggiunta di un’azione non richiede un’altra coppia di archivi.

Un&#39;app con widget interattivi si compone di tre parti: impostazioni di azione, codice del gestore e codice del widget. Le impostazioni sono gestite in [!DNL LLM Apps]. Il codice del gestore e del widget si trovano in due archivi [!DNL GitHub] separati.

```text
┌───────────────────────┐   ┌───────────────────────┐   ┌───────────────────────┐
│ 1. LLM Apps UI        │   │ 2. Handler repository │   │ 3. Widget repository  │
│                       │   │                       │   │                       │
│ Action descriptions   │   │ Connects to your APIs │   │ Widget layout         │
│ Required information  │   │ Runs the task         │   │ Branding and styles   │
│ When to use an action │   │ Returns results       │   │ Interactive controls  │
└───────────────────────┘   └───────────────────────┘   └───────────────────────┘
            │                           │                           │
            └───────────────────────────┼───────────────────────────┘
                                        ▼
                    ┌───────────────────────────────────────┐
                    │ Your app in an AI conversation        │
                    │ The action runs and the widget        │
                    │ shows the result.                     │
                    └───────────────────────────────────────┘
```

- **Le impostazioni delle azioni descrivono l&#39;attività.** Comunicano all’assistente quando utilizzare un’azione e quali informazioni sono necessarie. Per la ricerca di prodotti, tali informazioni potrebbero essere una categoria o un termine di ricerca.
- **L&#39;handler esegue il lavoro.** Ottiene i dati dai sistemi o esegue un’attività. Un gestore di ricerca dei prodotti, ad esempio, recupera i prodotti corrispondenti dal catalogo.
- **Il widget presenta il risultato.** Controlla ciò che il cliente vede e con cui interagisce, ad esempio schede prodotto con nomi, prezzi e pulsanti.

È consigliabile aggiungere un widget a ogni azione. Un widget consente di controllare l’aspetto dei risultati e il modo in cui i clienti interagiscono con essi, anziché lasciare la presentazione all’assistente di intelligenza artificiale. Sono supportate anche le risposte di solo testo.

## Dove apportare modifiche

| Cosa si desidera modificare | Dove modificare |
|---|---|
| Quando l’assistente utilizza un’azione o quali informazioni richiede l’azione | [Impostazioni azione nelle app LLM](/help/reference/reference-docs.md) |
| Da dove provengono i risultati o cosa accade quando viene eseguita un’azione | [Codice del gestore nell&#39;archivio del gestore](/help/guides/customize-handler.md) |
| Aspetto dei risultati, inclusi layout, branding e interazioni | [Codice widget nell&#39;archivio widget](/help/guides/widgets.md) |

Mantenere coerenti le tre parti. Ad esempio, se modificate le informazioni restituite da un gestore, aggiornate il widget per visualizzare il nuovo risultato.

## Cosa succede in una conversazione

Dopo la distribuzione, l&#39;app dispone di un URL di connessione denominato **URL del server MCP**. Aggiungi questo URL a una piattaforma di intelligenza artificiale supportata in modo che l’assistente possa individuare e utilizzare le azioni dell’app.

Per un’app con un’azione di ricerca prodotto e un widget:

1. Un cliente chiede all&#39;assistente, *Mostra baccelli da caffè.*
2. L’assistente utilizza la descrizione dell’azione per scegliere l’azione di ricerca del prodotto.
3. Il gestore ottiene i prodotti corrispondenti dal catalogo.
4. Il widget visualizza i prodotti come schede nella conversazione.
5. Il cliente esamina i risultati o pone una domanda complementare.

Il cliente resta nella conversazione mentre l&#39;app fornisce le informazioni o il servizio. Il supporto dei widget dipende dalla piattaforma LLM.

## Applicare e verificare le modifiche

Pubblica le modifiche al widget e implementa le modifiche alle azioni prima di testare l’esperienza aggiornata. Segui [Distribuisci l&#39;app](/help/guides/deploy-your-app.md) per eseguire la distribuzione nell&#39;ambiente di staging e testare l&#39;app in una piattaforma LLM supportata.

Le azioni generate automaticamente iniziano con dati di esempio. Connetti i gestori ai sistemi reali e verifica l’app prima di renderla disponibile ai clienti.
