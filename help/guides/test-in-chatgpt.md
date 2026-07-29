---
title: Testare l’app LLM come plug-in ChatGPT
description: Crea un plug-in ChatGPT dall’URL del server MCP delle app Adobe LLM e testalo in una conversazione.
source-git-commit: b7199fbb387d91a5c77deac47a2bc883381931c1
workflow-type: tm+mt
source-wordcount: '335'
ht-degree: 1%

---


# Test dell&#39;app LLM come plug-in [!DNL ChatGPT] {#test-in-chatgpt}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Dopo la distribuzione, l’app LLM espone un URL del server MCP. Aggiungi questo URL a [!DNL ChatGPT] come plug-in, quindi verifica le azioni e i widget generati.

Questo è il passaggio di verifica finale dopo la creazione, la personalizzazione o l’estensione di un’app.

## Requisiti del piano

La modalità Sviluppatore è disponibile sul web per gli account Pro, Plus, Business, Enterprise e Education. Gli amministratori di Workspace possono limitare l’accesso.

## Abilita modalità sviluppatore

In [!DNL ChatGPT]:

1. Apri **[!UICONTROL Impostazioni] → [!UICONTROL Protezione e accesso]**.
2. Attiva **[!UICONTROL Modalità sviluppatore]**.

Il pulsante più nella pagina Plug-in crea i plug-in basati su MCP solo dopo l’abilitazione della modalità sviluppatore. Vedi [Modalità sviluppatore ChatGPT](https://developers.openai.com/api/docs/guides/developer-mode).

## Copia l&#39;URL del server MCP

In [!DNL LLM Apps]:

1. Apri la pagina Dettagli app.
2. Trova **[!UICONTROL Verifica l&#39;app]**.
3. In **[!UICONTROL Ambiente di gestione temporanea]**, selezionare **[!UICONTROL Copia URL]**.

## Creare il plug-in

1. Apri [chatgpt.com/plugins](https://chatgpt.com/plugins).
2. Nella scheda **[!UICONTROL Plug-in]**, seleziona **+** accanto al campo di ricerca.

   ![ChatGPT - Pagina plug-in](/help/assets/guide-onboarding-agent/chatgpt-plugins-page.png)

3. In **[!UICONTROL Nuovo plug-in]**, immettere:
   - **[!UICONTROL Nome]**: il nome del plug-in.
   - **[!UICONTROL Descrizione]** — facoltativo.
   - **[!UICONTROL Connessione]** — selezionare **[!UICONTROL URL server]** e incollare l&#39;URL del server MCP.
   - **[!UICONTROL Autenticazione]** — selezionare **[!UICONTROL Nessuna autenticazione]**.
4. Seleziona **[!UICONTROL Confermo e desidero continuare]**.
5. Seleziona **[!UICONTROL Crea]**.

   ![ChatGPT — crea un plug-in con l&#39;URL del server MCP](/help/assets/guide-onboarding-agent/chatgpt-new-plugin.png)

6. Nella finestra di dialogo di conferma, seleziona **[!UICONTROL Connetti]**.

   ![ChatGPT — connetti il nuovo plug-in](/help/assets/guide-onboarding-agent/chatgpt-plugin-connect.png)

## Test del plug-in

1. Avvia una nuova chat.
2. Scegliere **[!UICONTROL Modalità sviluppatore]** dal menu Plus e selezionare il plug-in.
3. Fai una domanda che corrisponda a una delle azioni generate. Ad esempio: *Visualizza caffè.*

![ChatGPT — risposta del plug-in dell&#39;app LLM generata](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

Verifica che:

- [!DNL ChatGPT] richiama l&#39;azione prevista.
- Il widget visualizza i dati di esempio previsti.
- La risposta di testo corrisponde al widget.
- I controlli widget funzionano come previsto.

## Passaggio successivo

- [Personalizzare i widget generati](/help/guides/widgets.md).
- [Crea un&#39;azione da zero](/help/guides/create-action.md).
