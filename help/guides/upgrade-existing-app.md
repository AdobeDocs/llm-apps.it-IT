---
title: Aggiornare un’app esistente
description: Aggiorna il runtime di un’app Adobe LLM precedente e il SDK EDS prima di abilitare Adobe Analytics o Customer Journey Analytics.
source-git-commit: 557173e8d95325b980efac4d72c06bb3d90dcd72
workflow-type: tm+mt
source-wordcount: '793'
ht-degree: 0%
---

# Aggiornare un’app esistente {#upgrade-existing-app}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Utilizzare questa guida solo se l&#39;app utilizza `@adobe/llm-apps-runtime` prima della versione 1.2.0 o i relativi widget EDS utilizzano il SDK delle app LLM prima del 0.1.13. Le app che soddisfano già entrambi i requisiti possono passare direttamente a [Configura Adobe Analytics e Customer Journey Analytics](/help/guides/analytics-setup.md).

**In questa guida:** Scopri come aggiornare e verificare il runtime di un&#39;app precedente e il SDK EDS. Quindi torna alla guida all’impostazione di Analytics per abilitare Analytics e verificare la raccolta.

## Prima di iniziare {#before-you-begin}

È necessario accedere all&#39;archivio del gestore e, per le app con widget EDS, all&#39;archivio EDS collegato all&#39;app. Se la tua app utilizza l’infrastruttura gestita di Adobe, richiedi l’accesso al codice prima di apportare aggiornamenti. Consulta [Utilizzare l&#39;infrastruttura gestita di Adobe](/help/guides/create-app.md#adobe-managed-infrastructure).

Non è necessario creare un&#39;altra app. Aggiorna solo i componenti che si trovano sotto le versioni supportate. Puoi riutilizzare la configurazione Adobe esistente che soddisfa i [requisiti di installazione di Analytics](/help/guides/analytics-setup.md#setup-checklist).

Per le app con widget EDS, pubblica e verifica prima l’aggiornamento di SDK, quindi unisci l’aggiornamento di runtime e ridistribuisci l’app. I due aggiornamenti dell’archivio possono essere preparati indipendentemente, ma entrambi devono essere disponibili prima di testare la distribuzione abilitata per Analytics.

## Controllare e aggiornare l’archivio azioni delle app LLM {#upgrade-runtime}

1. Apri l’archivio del gestore collegato all’app e installane le dipendenze:

   ```bash
   npm install
   ```

1. Controllare il runtime installato:

   ```bash
   npm ls @adobe/llm-apps-runtime
   ```

1. Se il runtime è precedente alla versione 1.2.0, installa la versione corrente:

   ```bash
   npm install @adobe/llm-apps-runtime@latest
   npm ls @adobe/llm-apps-runtime
   ```

   Verifica che la versione installata sia 1.2.0 o successiva. Mantieni una versione più recente supportata, se l’app ne utilizza già una.

1. Verificare che la dipendenza runtime e la relativa versione risolta soddisfino il requisito minimo. La versione dell’applicazione è separata dalla dipendenza runtime.
1. Eseguire i controlli del repository. Per il piatto bollente delle applicazioni LLM:

   ```bash
   npm test -- --runInBand
   npm run build
   npm run lint
   ```

   Analizza gli errori prima di distribuirli. Se un controllo ha già esito negativo nella versione originale, registra tale baseline separatamente da eventuali errori introdotti dall&#39;aggiornamento. Una build locale corretta non verifica la raccolta di analisi.

1. Eseguire il commit dell&#39;aggiornamento delle dipendenze e del relativo file di blocco, quindi inviare e unire l&#39;aggiornamento nel ramo predefinito dell&#39;archivio del gestore, in genere `main`. Cloni di distribuzione di quel ramo. Un aggiornamento presente solo localmente o su un ramo di funzionalità non unito non è incluso nella distribuzione.

   Quando esiste un file di blocco, la distribuzione installa le dipendenze con `npm ci`, che utilizza le versioni registrate. La ridistribuzione non aggiorna automaticamente il runtime. Prima di eseguire la ridistribuzione, verifica la dipendenza aggiornata e la versione risolta nel ramo predefinito in GitHub.

## Verifica e aggiorna l’archivio dei widget delle app LLM {#upgrade-sdk}

Per le app con widget EDS, controlla il SDK nell’archivio EDS e nel runtime del server. L’aggiornamento di un archivio non aggiorna l’altro.

Alcuni archivi EDS includono script SDK copiati senza una dipendenza npm. Il browser carica direttamente gli script forniti, pertanto l’assenza di una voce di pacchetto non significa che SDK sia mancante. Aggiorna la distribuzione SDK copiata utilizzando il processo di aggiornamento SDK del progetto. L’aggiunta di una dipendenza npm permanente non è necessaria per un progetto che mantiene SDK come script copiati.

Per i progetti che gestiscono SDK tramite npm:

1. Dalla directory principale dell&#39;archivio EDS, installa il SDK corrente e ne conferma la versione:

   ```bash
   npm install --save-dev @adobe/llmapps-sdk@latest
   npm ls @adobe/llmapps-sdk
   ```

   Utilizzare 0.1.13 o versione successiva. Mantieni una versione supportata successiva se il progetto ne utilizza già una.

1. Verificare che l&#39;installazione abbia aggiornato la distribuzione SDK distribuita. Il hook di installazione sovrascrive gli script SDK copiati, quindi controlla eventuali personalizzazioni locali prima di confermare. L&#39;aggiornamento solo della dichiarazione di dipendenza non aggiorna gli script utilizzati da EDS. Consulta [Installare le app LLM SDK](/help/guides/bring-your-own-eds.md#install-the-llm-apps-sdk).
1. Eseguire i controlli dell&#39;archivio EDS, ad esempio `npm run lint`, e verificare che un widget esistente continui a eseguire il rendering e a rispondere alle interazioni.
1. Esegui il commit della distribuzione di SDK aggiornata insieme a eventuali aggiornamenti di dipendenza e file di blocco. EDS fornisce direttamente gli script copiati, non il pacchetto npm installato.

Dopo entrambi i metodi di aggiornamento, invia e unisci l&#39;aggiornamento nel ramo EDS utilizzato dall&#39;**[!UICONTROL URL script]** dell&#39;azione. Verifica che l’URL serva il SDK aggiornato e che le relative librerie di supporto siano state caricate correttamente. Se l&#39;URL utilizza il sito EDS live, completa i passaggi di pubblicazione per tale sito.

## Passaggio successivo {#next-step}

Torna a [Configura Adobe Analytics e Customer Journey Analytics](/help/guides/analytics-setup.md). Tutte le app devono abilitare Analytics e implementarle per raccogliere gli eventi; l&#39;aggiornamento del runtime o di SDK da solo non abilita la raccolta.

Dopo aver salvato le impostazioni di Analytics, distribuisci in Stage e [verifica nuovi eventi di interazione azione-chiamata e widget](/help/guides/analytics-setup.md#verify) prima di distribuirli in Produzione. Verifica che l’azione e il widget continuino a funzionare e che una singola selezione di collegamento generi un evento di interazione widget.

Analytics inizia a raccogliere nuove interazioni dopo la distribuzione abilitata per Analytics. L’aggiornamento non ricostruisce le interazioni che si sono verificate prima dell’abilitazione della raccolta.
