---
title: Distribuire l’app
description: Scopri come distribuire l’app Adobe LLM nell’ambiente di staging e produzione utilizzando l’interfaccia utente delle app LLM.
source-git-commit: bb3d8a02f22a91ceeeba5999453aeb4221060f80
workflow-type: tm+mt
source-wordcount: '322'
ht-degree: 0%

---


# Distribuire l’app {#deploy-your-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Dopo aver scritto il codice del gestore e averlo inviato all&#39;archivio collegato, puoi distribuire l&#39;app dall&#39;interfaccia utente [!DNL LLM Apps].

Questo è un passaggio condiviso per ogni percorso. Dopo la distribuzione, continuare a [testare il plug-in ChatGPT](/help/guides/test-in-chatgpt.md) o [testare il connettore Claude](/help/guides/test-in-claude.md).

## Avviare l’implementazione

Aprire la pagina Dettagli app e selezionare **[!UICONTROL Distribuisci]**.

Selezionare l&#39;ambiente di destinazione, quindi selezionare **[!UICONTROL Distribuisci]**.

![Distribuisci — seleziona l&#39;ambiente di destinazione](/help/assets/guide-onboarding-agent/deploy-stage.png)

La distribuzione prevede quattro passaggi:

1. **Preparazione** - recupera la configurazione necessaria per distribuire l&#39;app.
2. **Avvia distribuzione** — avvia il processo di distribuzione in background.
3. **Genera app**: installa le dipendenze e crea il codice di archivio più recente.
4. **Pubblica** — pubblica l&#39;app in [!DNL Adobe I/O Runtime].

![Distribuzione: pipeline di distribuzione in esecuzione](/help/assets/guide-onboarding-agent/deploy-running.png)

>[!NOTE]
>
>Se nell’interfaccia utente di un’azione sono presenti metadati ma nell’archivio non è presente alcun file di gestore corrispondente, l’azione viene comunque registrata. Le chiamate utilizzano un gestore di stub predefinito fino a quando non si aggiunge il codice effettivo.

## Dopo una distribuzione corretta

Al termine di tutti i passaggi, nella finestra di dialogo viene visualizzato **Distribuzione completata**.

![Distribuzione - distribuzione completata](/help/assets/guide-onboarding-agent/deploy-successful.png)

Fai clic su **Chiudi** per chiudere la finestra di dialogo. Scorri verso il basso fino alla sezione **[!UICONTROL Verifica l&#39;app]** nella pagina Dettagli app:

![Dettagli app — copia l&#39;URL del server MCP](/help/assets/guide-onboarding-agent/app-mcp-url.png)

Ogni ambiente distribuito mostra un URL del server MCP. Selezionare **[!UICONTROL Copia URL]** e utilizzarlo per creare un plug-in nella piattaforma LLM di destinazione.

La sezione **Cronologia distribuzione** mostra le ultime 10 distribuzioni:

![Cronologia distribuzione](/help/assets/guide-deploy/deployment-history.png)

Ogni riga mostra la destinazione **Ambiente** (stage o produzione), **Stato** (riuscito o non riuscito) e la data **Distribuito alle**. È possibile utilizzare questa tabella per tenere traccia di quando si sono verificate le distribuzioni e verificare che
distribuzione più recente completata.

## Passaggio successivo

- [Verifica l&#39;app distribuita come plug-in ChatGPT](/help/guides/test-in-chatgpt.md).
- [Verifica l&#39;app distribuita come connettore Claude](/help/guides/test-in-claude.md).

