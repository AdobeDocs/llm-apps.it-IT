---
title: Testare l’app LLM come connettore Claude
description: Crea un connettore Claude dall’URL del server MCP delle app Adobe LLM e testalo in una conversazione.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '399'
ht-degree: 1%

---


# Test dell&#39;app LLM come connettore [!DNL Claude] {#test-in-claude}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Dopo la distribuzione, l’app LLM espone un URL del server MCP. Aggiungi questo URL a [!DNL Claude] come connettore personalizzato, quindi verifica le azioni e i widget generati.

Questo è il passaggio di verifica finale dopo la creazione, la personalizzazione o l’estensione di un’app.

## Requisiti del piano

I connettori personalizzati che utilizzano MCP remoto sono disponibili su [!DNL Claude], [!DNL Claude] Desktop e Cowork per piani Gratuiti, Pro, Max, Team e Enterprise. Gli account del piano gratuiti sono limitati a un connettore personalizzato. Per le organizzazioni del team e dell&#39;organizzazione, un proprietario o un proprietario principale deve abilitare i connettori prima che altri membri possano utilizzarli.

## Copia l&#39;URL del server MCP

In [!DNL LLM Apps]:

1. Apri la pagina Dettagli app.
2. Trova **[!UICONTROL Verifica l&#39;app]**.
3. In **[!UICONTROL Ambiente di gestione temporanea]**, selezionare **[!UICONTROL Copia URL]**.

## Aggiungere il connettore personalizzato

1. Apri [claude.ai/new?modal=add-custom-connector](https://claude.ai/new?modal=add-custom-connector#settings/customize-connectors). Verrà aperta direttamente la finestra di dialogo **[!UICONTROL Aggiungi connettore personalizzato]**.
2. Inserisci:
   - **[!UICONTROL Nome]**: il nome del connettore.
   - **[!UICONTROL URL server MCP remoto]**: l&#39;URL del server MCP copiato.
3. Seleziona **[!UICONTROL Aggiungi]**.

   ![Claude - Finestra di dialogo Aggiungi connettore personalizzato](/help/assets/guide-test-claude/claude-add-custom-connector.png)

>[!NOTE]
>
>Utilizza i connettori solo da sviluppatori considerati attendibili. Antropico non controlla quali strumenti gli sviluppatori mettono a disposizione e non può verificare se funzioneranno come previsto o se non cambieranno.

## Consenti strumenti generati

Ogni azione generata è elencata in **[!UICONTROL Autorizzazioni dello strumento]** nella pagina del connettore. Per impostazione predefinita, i nuovi strumenti sono impostati su **[!UICONTROL È necessaria l&#39;approvazione]**, che richiede di approvare ogni chiamata durante il test.

Imposta ogni strumento o l&#39;intero gruppo di **[!UICONTROL strumenti interattivi]** su **[!UICONTROL Consenti sempre]** in modo che il test non venga interrotto dai prompt di approvazione.

![Claude — imposta le autorizzazioni dello strumento su Consenti sempre](/help/assets/guide-test-claude/claude-tool-permissions.png)

## Verificare il connettore

1. Avvia una nuova chat.
2. Selezionare **+** nella finestra di messaggio (o digitare `/`), passare il puntatore del mouse su **[!UICONTROL Connettori]** e attivare il connettore aggiunto per la conversazione.

   ![Claude — abilita il connettore per la conversazione](/help/assets/guide-test-claude/claude-enable-connector-chat.png)

3. Fai una domanda che corrisponda a una delle azioni generate. Ad esempio: *Visualizza caffè.*

Verifica che:

- [!DNL Claude] richiama l&#39;azione prevista.
- Il widget visualizza i dati di esempio previsti.
- La risposta di testo corrisponde al widget.
- I controlli widget funzionano come previsto.

## Passaggio successivo

- [Personalizzare i widget generati](/help/guides/widgets.md).
- [Crea un&#39;azione da zero](/help/guides/create-action.md).
