---
title: Testare l’app LLM come plug-in ChatGPT
description: Crea un plug-in ChatGPT dall’URL del server MCP delle app Adobe LLM e testalo in una conversazione.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '546'
ht-degree: 0%
---

# Test dell&#39;app LLM come plug-in [!DNL ChatGPT] {#test-in-chatgpt}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Dopo la distribuzione, l’app LLM espone un URL del server MCP. Aggiungi questo URL a [!DNL ChatGPT] come plug-in, quindi verifica le azioni e i widget generati.

**In questa guida:** Scopri come connettere l&#39;app distribuita a [!DNL ChatGPT] e testarne azioni e widget in una conversazione. Aggiorna la connessione dopo aver modificato l’app.

## Requisiti del piano

Utilizza un account e un’area di lavoro che consentono l’aggiunta di server MCP personalizzati. I controlli relativi a disponibilità e amministratore variano in base all&#39;account e all&#39;area di lavoro. Consulta la documentazione [Connetti e verifica il plug-in](https://developers.openai.com/plugins/deploy/connect-chatgpt) corrente di OpenAI.

## Controllare l&#39;accesso ai server MCP personalizzati

Apri [Plug-in ChatGPT](https://chatgpt.com/plugins) e verifica che il tuo account consenta di aggiungere un server MCP personalizzato. Se l&#39;account richiede la modalità sviluppatore, abilitarla utilizzando i controlli dell&#39;account. Se l&#39;opzione del server MCP personalizzato risulta mancante o bloccata, rivolgersi all&#39;amministratore del workspace.

## Copia l&#39;URL del server MCP

In [!DNL LLM Apps]:

1. Apri la pagina Dettagli app.
2. Trova **[!UICONTROL Verifica l&#39;app]**.
3. In **[!UICONTROL Ambiente stage]**, selezionare **[!UICONTROL Copia URL]**.

## Creare il plug-in

1. Apri [chatgpt.com/plugins](https://chatgpt.com/plugins).
2. Selezionare **+**, quindi **[!UICONTROL Aggiungi server MCP personalizzato]**.
3. Configura la connessione:
   - **[!UICONTROL Nome]**: il nome del plug-in.
   - **[!UICONTROL Descrizione]** — facoltativo.
   - Scegli il metodo di connessione al server pubblico e immetti l’URL completo del server MCP HTTPS dell’app.
   - Scegli il metodo di autenticazione che corrisponde all&#39;app. Per un&#39;app pubblica con autenticazione disabilitata, utilizza **[!UICONTROL Nessuna autenticazione]**, dove disponibile.

   >[!NOTE]
   >
   >Se hai attivato l&#39;autenticazione dell&#39;utente finale, seleziona **[!UICONTROL OAuth]** quando ogni azione è impostata su **[!UICONTROL Obbligatorio]**, o **[!UICONTROL Misto]** per una combinazione di azioni pubbliche e protette, dove sono disponibili tali opzioni. Consulta [Autenticazione degli utenti finali con il tuo provider di identità](/help/guides/authentication.md).

4. Seleziona **[!UICONTROL Confermo e desidero continuare]**.
5. Seleziona **[!UICONTROL Crea come plug-in]**.
6. Controlla gli strumenti e i metadati individuati, quindi installa o collega il plug-in risultante. Completa l&#39;accesso se l&#39;app lo richiede.

OpenAI è proprietaria dell’interfaccia utente di configurazione e il suo layout può variare a seconda dell’account. Segui le [istruzioni di connessione](https://developers.openai.com/plugins/deploy/connect-chatgpt) correnti se le etichette visualizzate sono diverse.

## Test del plug-in

1. Avvia una nuova chat.
2. Digitare `@` nella casella di richiesta e selezionare il plug-in installato.
3. Fai una domanda che corrisponda a una delle azioni generate. Ad esempio: *Visualizza caffè.*

![Risposta ChatGPT illustrativa: un&#39;azione Frescopa restituisce schede prodotto nella conversazione](/help/assets/guide-onboarding-agent/chatgpt-generated-app.png)

Verifica che:

- [!DNL ChatGPT] richiama l&#39;azione prevista.
- Il widget visualizza i dati di esempio previsti.
- La risposta di testo corrisponde al widget.
- I controlli widget funzionano come previsto.

Inoltre, testa input non valido, risultati vuoti, prompt non correlati che non chiamano per l’app e azioni che modificano i dati. Esamina ogni richiesta di conferma e autenticazione anziché approvare tutte le richieste in anticipo.

## Aggiorna dopo le modifiche dell’app

Dopo aver modificato le descrizioni delle azioni, gli input, l’autenticazione o la configurazione dei widget:

1. Distribuisci l’app aggiornata nello stesso ambiente.
2. Apri la connessione su [Plug-in ChatGPT](https://chatgpt.com/plugins) e seleziona **[!UICONTROL Aggiorna]**.
3. Verifica che le azioni annunciate siano cambiate.
4. Avvia una nuova conversazione e ripeti i test interessati.

## Passaggio successivo

- [Personalizzare i widget generati](/help/guides/widgets.md).
- [Crea un&#39;azione da zero](/help/guides/create-action.md).
