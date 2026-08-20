---
title: Collegamento di un'app
description: Scopri più da vicino come i vari elementi che possiedi - metadati di azione, codice del gestore e widget - si combinano in un’unica app LLM in esecuzione, in fase di build e in fase di runtime.
source-git-commit: 2f3480b3667a6ab7c4ed65b999eed4638c383edb
workflow-type: tm+mt
source-wordcount: '335'
ht-degree: 0%

---


# Collegamento di un&#39;app {#app-architecture}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

## In una frase

Un&#39;app **LLM** è un set di **azioni** (ognuna delle quali è uno strumento esposto nel **Model Context Protocol** o **MCP**) che si pubblica in un singolo endpoint. Un host di chat come [!DNL ChatGPT] rileva tali strumenti, li chiama a metà conversazione ed esegue il rendering di un **widget interattivo** con il risultato, proprio all&#39;interno della chat.

## L&#39;intero cablaggio, la costruzione → l&#39;esecuzione

**Diagramma 1 — tempo di compilazione.** Possiedi tre superfici separate; la piattaforma le fonde in un’unica app distribuibile.

```
┌─────────────────────┐   ┌─────────────────────┐   ┌─────────────────────┐
│ 1  LLM Apps UI      │   │ 2  Action Handler   │   │ 3  Widget repo      │
│                     │   │    repo             │   │                     │
│ Create, edit, and   │   │                     │   │ Each widget is an   │
│ manage your action  │   │ Business logic —    │   │ EDS block,          │
│ definitions here    │   │ built from our      │   │ published to a      │
│ (metadata)          │   │ boilerplate         │   │ public URL on       │
│                     │   │                     │   │ *.aem.page          │
│                     │   │ Returns content     │   │                     │
│                     │   │ (for the LLM) +     │   │                     │
│                     │   │ structuredContent   │   │                     │
│                     │   │ (for the widget)    │   │                     │
└─────────────────────┘   └─────────────────────┘   └─────────────────────┘
           │                         │                         │
           └─────────────────────────┼─────────────────────────┘
                                     ▼
                       ┌─────────────────────────────┐
                       │ LLM Apps deploy pipeline    │
                       │ Combines the 3 surfaces     │
                       │ into one running app        │
                       └─────────────────────────────┘
                                     │
                                     ▼
                 ┌─────────────────────────────────────────┐
                 │ ONE MCP server on Adobe I/O Runtime     │
                 │ https://<ns>.adobeioruntime.net/.../mcp │
                 └─────────────────────────────────────────┘
```

- **Interfaccia utente delle app LLM** — in cui si crea, si modifica e si gestisce la definizione di ogni azione: il relativo **identificatore del codice** (un tag fisso impostato una volta qui, ad esempio `my_action`, che collega la stessa azione tra l&#39;interfaccia utente, il gestore e il widget), la descrizione, lo schema di input, la scelta del widget e i flag CSP/visibilità. Nessun codice.
- **Repo gestore azioni**: l&#39;archivio lato server (scaffolded dalla boilerplate) in cui si scrive la regola business. Ogni funzione del gestore restituisce due elementi: `content` (testo normale letto da *LLM*) e `structuredContent` (oggetto dati letto da *widget*).
- **Repo widget**: l&#39;archivio EDS in cui ogni widget risiede come un blocco e viene pubblicato in un URL `*.aem.page` pubblico. Ogni blocco utilizza [`@adobe/llmapps-sdk`](https://www.npmjs.com/package/@adobe/llmapps-sdk), il bridge tra il widget e l&#39;host/server. Implementa la specifica **MCP Apps**, il protocollo sottostante, dietro una semplice API e astrae l&#39;host LLM stesso, in modo che lo stesso widget funzioni senza modifiche in [!DNL ChatGPT], [!DNL Claude], Gemini o qualsiasi altro host MCP.

**Diagramma 2 - runtime.** Che cosa accade a ogni messaggio inviato dall’utente, una volta che quel server è attivo. Visualizzato con [!DNL ChatGPT] come host di esempio. La stessa sequenza viene eseguita per qualsiasi host MCP, ad esempio [!DNL Claude].

```
┌── ChatGPT  (the MCP host) ──────────────────────────────────────────────┐
│  1  tools/list  >  sees `my_action` + its description + input schema    │
│  2  user asks   >  "I need help with …"                                 │
│  3  model picks >  the description matches -> calls this tool           │
│  4  tools/call  >  { name: "my_action", arguments: {situation, ...} }   │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                 routes by CODE IDENTIFIER  ->  my_action
                                     ▼
┌── Adobe I/O Runtime ────────────────────────────────────────────────────┐
│  actions/my_action/index.js  --  your handler runs                      │
│  returns  { content -> text for the model , structuredContent -> data } │
└─────────────────────────────────────────────────────────────────────────┘
                                     │
                                     ▼
┌── Rendered inside the conversation ─────────────────────────────────────┐
│  5  render      >  ChatGPT renders the Widget repo's EDS block          │
│  The EDS block reads the structuredContent the Action Handler repo      │
│  returned, and draws the interactive card — live, inside the chat.      │
└─────────────────────────────────────────────────────────────────────────┘
```
