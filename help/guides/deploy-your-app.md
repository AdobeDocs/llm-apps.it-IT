---
title: Distribuire l’app
description: Scopri come distribuire l’app Adobe LLM nell’ambiente di staging e produzione utilizzando l’interfaccia utente delle app LLM.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '444'
ht-degree: 0%
---

# Distribuire l’app {#deploy-your-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Dopo aver esaminato le azioni generate o inviato il codice del gestore personalizzato all&#39;archivio collegato, distribuire l&#39;app da [!DNL LLM Apps]. Per le azioni generate, rivedi e unisci il lavoro generato prima della distribuzione.

**In questa guida:** scopri come distribuire l&#39;app nell&#39;ambiente di staging, controllare il risultato della distribuzione e copiare l&#39;URL del server MCP per i test. Distribuisci in produzione dopo il completamento del test.

## Avviare l’implementazione

Aprire la pagina Dettagli app e selezionare **[!UICONTROL Distribuisci]**.

Selezionare l&#39;ambiente di destinazione, quindi selezionare **[!UICONTROL Distribuisci]**.

Se i gestori utilizzano [variabili app](/help/guides/app-variables.md), configurale per l&#39;ambiente di destinazione prima della distribuzione. Le variabili aggiunte, aggiornate o eliminate hanno effetto su questa distribuzione; Stage e Production hanno valori indipendenti.

![Distribuisci — seleziona l&#39;ambiente di destinazione](/help/assets/guide-onboarding-agent/deploy-stage.png)

La distribuzione prevede quattro passaggi:

1. **Preparazione** - recupera la configurazione necessaria per distribuire l&#39;app.
2. **Avvia distribuzione** — avvia il processo di distribuzione in background.
3. **Genera app**: installa le dipendenze e crea il codice di archivio più recente.
4. **Pubblica** — pubblica l&#39;app in [!DNL Adobe I/O Runtime].

>[!NOTE]
>
>Se nell’interfaccia utente di un’azione sono presenti metadati ma nell’archivio non è presente alcun file di gestore corrispondente, l’azione viene comunque registrata nella distribuzione. Le chiamate utilizzano un gestore di stub predefinito fino a quando non si aggiunge il codice effettivo.

## Dopo una distribuzione corretta

Al termine di tutti i passaggi, nella finestra di dialogo viene visualizzato **Distribuzione completata**.

Per chiudere la finestra di dialogo, selezionare **Chiudi**. Nella pagina Dettagli app, scorri fino a **[!UICONTROL Verifica l&#39;app]**:

![Dettagli app — copia l&#39;URL del server MCP](/help/assets/guide-onboarding-agent/app-mcp-url.png)

Ogni ambiente distribuito mostra un URL del server MCP. Selezionare **[!UICONTROL Copia URL]** e utilizzarlo per creare un plug-in o un connettore nella piattaforma LLM di destinazione.

La sezione **Cronologia distribuzione** mostra le ultime 10 distribuzioni:

![Cronologia distribuzione](/help/assets/guide-deploy/deployment-history.png)

Ogni riga mostra la destinazione **Ambiente** (stage o produzione), **Stato** (riuscito o non riuscito) e **Distribuito in** data. Utilizzare la tabella per tenere traccia delle date di distribuzione e verificare che l&#39;ultima distribuzione sia stata completata correttamente.

## Distribuire le modifiche alla configurazione

Le modifiche ai metadati delle azioni, agli switch **[!UICONTROL Visibilità]** della pagina dell&#39;app, all&#39;autenticazione, alle variabili dell&#39;app e alle impostazioni di Analytics hanno effetto alla prossima distribuzione in tale ambiente. Il salvataggio di un&#39;impostazione non comporta l&#39;aggiornamento del server MCP distribuito.

Controlla l’ambiente di destinazione ogni volta. La distribuzione nell’ambiente di staging non distribuisce le stesse modifiche nell’ambiente di produzione.

Se l&#39;app non soddisfa i requisiti di runtime di Analytics o SDK, segui [Aggiorna un&#39;app esistente](/help/guides/upgrade-existing-app.md) prima di ridistribuirla.

## Passaggio successivo

- [Verifica l&#39;app distribuita come plug-in ChatGPT](/help/guides/test-in-chatgpt.md).
- [Verifica l&#39;app distribuita come connettore Claude](/help/guides/test-in-claude.md).
- [Invia gli eventi dell&#39;app ad Adobe Analytics o Customer Journey Analytics](/help/guides/analytics-setup.md).
