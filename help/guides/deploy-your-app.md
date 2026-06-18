---
title: Distribuire l’app
description: Scopri come distribuire l’app Adobe LLM nell’ambiente di staging e produzione utilizzando l’interfaccia utente delle app LLM.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '359'
ht-degree: 0%

---


# Distribuire l’app

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Dopo aver scritto il codice del gestore e averlo inviato all&#39;archivio collegato, puoi distribuire l&#39;app dall&#39;interfaccia utente [!DNL LLM Apps].

## Avviare l’implementazione

Passa alla pagina Dettagli app. Fai clic sul pulsante **[!UICONTROL Distribuisci]** nell&#39;angolo in alto a destra:

![Dettagli app - Pronto per la distribuzione](/help/assets/guide-deploy/app-detail-deploy-ready.png)

Verrà aperta la finestra di dialogo di distribuzione. Seleziona l’ambiente di destinazione dal menu a discesa:

![Finestra di dialogo Distribuisci - Seleziona ambiente di destinazione](/help/assets/guide-deploy/deploy-pipeline-dropdown.png)

Fare clic su **[!UICONTROL Distribuisci]** per avviare la pipeline. Le quattro fasi sono:

1. **Raccogli le credenziali**: legge i metadati dell&#39;app, genera un token [!DNL GitHub] e recupera le credenziali di runtime dall&#39;API della console.
2. **Attiva pipeline di compilazione** - invia tutti i parametri alla pipeline di compilazione.
3. **Clona e genera**: la pipeline clona l&#39;archivio, genera `actions.json` dai metadati dell&#39;interfaccia utente, esegue `npm install` e webpack per produrre `dist/index.js`.
4. **Distribuisci in fase di esecuzione**: distribuisce il bundle nello spazio dei nomi [!DNL Adobe I/O Runtime] dell&#39;app.

Una volta avviata, la pipeline viene eseguita automaticamente e mostra l’avanzamento in tempo reale:

![Distribuisci pipeline in esecuzione](/help/assets/guide-deploy/deploy-pipeline-deploying.png)

>[!NOTE]
>
>Se nell’interfaccia utente di un’azione sono presenti metadati ma nell’archivio non è presente alcun file di gestore corrispondente, l’azione viene comunque registrata. Le chiamate utilizzano un gestore di stub predefinito fino a quando non si aggiunge il codice effettivo.

## Dopo una distribuzione corretta

Al termine di tutti i passaggi, la finestra di dialogo mostra una conferma di **Distribuzione riuscita** con l&#39;URL distribuito e i dettagli dell&#39;artefatto:

![Distribuzione completata](/help/assets/guide-deploy/app-detail-deploy-finish.png)

Fai clic su **Chiudi** per chiudere la finestra di dialogo. Scorri verso il basso fino alla sezione **[!UICONTROL Verifica l&#39;app]** nella pagina Dettagli app:

![Verifica dell&#39;app - URL distribuiti](/help/assets/guide-deploy/test-app-deployed.png)

Ogni ambiente (**Gestione temporanea** e **Produzione**) mostra l&#39;URL del server MCP in [!DNL Adobe I/O Runtime]. Questo è l’URL fornito alla piattaforma LLM al momento della registrazione dell’app. Fai clic su **Copia URL** per copiarlo negli Appunti.

La sezione **Cronologia distribuzione** di seguito contiene un registro completo di ogni distribuzione tra gli ambienti:

![Cronologia distribuzione](/help/assets/guide-deploy/deployment-history.png)

Ogni riga mostra la destinazione **Ambiente** (stage o produzione), **Stato** (riuscito o non riuscito) e la data **Distribuito alle**. È possibile utilizzare questa tabella per tenere traccia di quando si sono verificate le distribuzioni e verificare che
distribuzione più recente completata.

