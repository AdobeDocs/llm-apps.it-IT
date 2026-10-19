---
title: Testare l’app LLM come connettore Claude
description: Crea un connettore Claude dall’URL del server MCP delle app Adobe LLM e testalo in una conversazione.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '676'
ht-degree: 0%
---

# Verifica l&#39;app LLM come connettore [!DNL Claude] {#test-in-claude}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Dopo la distribuzione, l’app LLM espone un URL del server MCP. Aggiungi questo URL a [!DNL Claude] come connettore personalizzato, quindi verifica le azioni e i widget generati.

**In questa guida:** scopri come aggiungere l&#39;app distribuita come connettore [!DNL Claude], sceglierne il comportamento di accesso e testarne azioni e widget in una conversazione.

Per un&#39;app con azioni pubbliche e protette, [!DNL Claude] supporta l&#39;accesso differito fino a quando non chiama un&#39;azione protetta. Un’app le cui azioni richiedono tutte l’autenticazione deve effettuare l’accesso prima dell’utilizzo. Consulta [Autenticazione degli utenti finali con il tuo provider di identità](/help/guides/authentication.md).

## Requisiti del piano

I connettori personalizzati che utilizzano MCP remoto sono disponibili per piani Gratuiti, Pro, Max, Team ed Enterprise. Gli account gratuiti supportano un connettore personalizzato. In Team ed Enterprise, un proprietario autorizzato aggiunge il connettore per l&#39;organizzazione prima che i membri si connettano a essa. Anche i ruoli personalizzati dell’organizzazione possono concedere tale autorizzazione.

Consulta le [istruzioni correnti del connettore personalizzato di Claude](https://claude.com/docs/connectors/custom/add-unlisted) per i controlli del piano e dell&#39;organizzazione. L’endpoint MCP remoto deve essere raggiungibile dall’infrastruttura di Claude, non solo dal browser locale.

## Copia l&#39;URL del server MCP

In [!DNL LLM Apps]:

1. Apri la pagina Dettagli app.
2. Trova **[!UICONTROL Verifica l&#39;app]**.
3. In **[!UICONTROL Ambiente stage]**, selezionare **[!UICONTROL Copia URL]**.

## Aggiungere il connettore personalizzato

1. Apri [Personalizza > Connettori](https://claude.ai/customize/connectors). Per un connettore a livello di organizzazione, un proprietario autorizzato apre invece [Impostazioni organizzazione > Connettori](https://claude.ai/admin-settings/connectors).
2. Seleziona **[!UICONTROL Aggiungi connettore personalizzato]** o **[!UICONTROL Aggiungi]** > **[!UICONTROL Personalizzato]** > **[!UICONTROL Web]** per il flusso dell&#39;organizzazione.
3. Se richiesto, immetti il nome del connettore e incolla l’URL completo del server MCP HTTPS.
4. Se la finestra di dialogo prevede più passaggi, selezionare **[!UICONTROL Continua]** e rivedere le impostazioni di autenticazione rilevate:
   - **[!UICONTROL Nessun accesso]** per un&#39;app pubblica con autenticazione disabilitata.
   - **[!UICONTROL Accedi quando necessario]** per un&#39;app con azioni pubbliche e protette.
   - **[!UICONTROL Accedi ora]** per richiedere l&#39;accesso prima dell&#39;utilizzo del connettore.
5. Per OAuth, scegli un metodo di identità client supportato dal provider di identità. Se utilizzi un client preregistrato, inserisci i relativi dettagli solo nelle impostazioni OAuth del connettore, non nell’URL o nei metadati dell’azione.
6. Seleziona **[!UICONTROL Aggiungi]**. Per un connettore organizzazione, ogni membro seleziona quindi **[!UICONTROL Connetti]** dal proprio elenco di connettori.

Alcuni account visualizzano una finestra di dialogo a schermata singola con **[!UICONTROL Impostazioni avanzate]**, mentre altri visualizzano un flusso con più passaggi. Utilizza le [istruzioni per la piattaforma corrente](https://claude.com/docs/connectors/custom/add-unlisted#choose-authentication-settings) se il layout è diverso.

>[!NOTE]
>
>Utilizza solo i connettori di sviluppatori considerati attendibili. Anthropic non controlla quali strumenti gli sviluppatori mettono a disposizione o garantiscono che tali strumenti funzionino come previsto o rimangano invariati.

## Consenti strumenti generati

[!DNL Claude] elenca ogni azione generata in **[!UICONTROL Autorizzazioni dello strumento]** nella pagina del connettore. I nuovi strumenti sono impostati per impostazione predefinita su **[!UICONTROL Richiede l&#39;approvazione]**, richiedendo l&#39;approvazione di ogni chiamata durante il test.

Mantieni abilitati i prompt di approvazione per le azioni che modificano i dati o eseguono operazioni sensibili. Utilizza **[!UICONTROL Consenti sempre]** solo per azioni attendibili e a basso rischio specifiche quando appropriato per il test.

## Verificare il connettore

1. Avvia una nuova chat.
2. Selezionare **+** nella finestra di messaggio (o digitare `/`), passare il puntatore del mouse su **[!UICONTROL Connettori]** e attivare il connettore aggiunto per la conversazione.
3. Fai una domanda che corrisponda a una delle azioni generate. Ad esempio: *Visualizza caffè.*

Verifica che:

- [!DNL Claude] richiama l&#39;azione prevista.
- Il widget visualizza i dati di esempio previsti.
- La risposta di testo corrisponde al widget.
- I controlli widget funzionano come previsto.

Per l’autenticazione mista, chiama prima un’azione pubblica senza accedere. Quindi invoca un&#39;azione protetta, completa la richiesta di accesso e verifica che la stessa richiesta abbia esito positivo in seguito. Verifica anche le autorizzazioni mancanti e l’input non valido.

Dopo aver modificato l’app, ridistribuisci e ripeti i test interessati. Se devi modificare le impostazioni di autenticazione salvate di un connettore, [!DNL Claude] potrebbe richiedere di rimuovere e aggiungere nuovamente il connettore.

## Passaggio successivo

- [Personalizzare i widget generati](/help/guides/widgets.md).
- [Crea un&#39;azione da zero](/help/guides/create-action.md).
