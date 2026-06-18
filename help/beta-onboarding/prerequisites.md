---
title: Prerequisiti per le app Adobe LLM
description: Cosa è necessario impostare prima della sessione di onboarding di Adobe LLM Apps Beta.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '571'
ht-degree: 1%

---


# Prerequisiti per le app Adobe LLM {#prerequisites-for-adobe-llm-apps}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Prima della sessione di onboarding di [!DNL Adobe LLM Apps] con Adobe, verifica di disporre dei seguenti elementi. Se possibile, eseguire i passaggi di verifica riportati di seguito. I risultati indicano chi deve trovarsi nella room e non se è possibile procedere.

## Console per sviluppatori di Adobe

Devi accedere a [Adobe Developer Console](https://developer.adobe.com/console) con il ruolo **Sviluppatore** (o **Amministratore di sistema**) nell&#39;organizzazione Adobe IMS. Assicurati che la tua organizzazione abbia accesso a [[!DNL App Builder]](https://developer.adobe.com/app-builder/docs/intro_and_overview/).

Per verificare, vai a [developer.adobe.com/console](https://developer.adobe.com/console). Se viene visualizzata la schermata di avvio rapido, le autorizzazioni sono impostate correttamente.

![Adobe Developer Console: schermata di avvio rapido che conferma l&#39;accesso per gli sviluppatori](/help/assets/overview/dev-console-access-granted.png)

Se invece viene visualizzato il messaggio **Accesso limitato**, non si dispone del ruolo Sviluppatore. Invita l’amministratore dell’organizzazione IMS alla sessione di onboarding.

![Adobe Developer Console - Messaggio ad accesso limitato](/help/assets/overview/dev-console-access-denied.png)

## [!DNL GitHub]

È necessario un account [!DNL GitHub] con le seguenti autorizzazioni nell&#39;organizzazione:

- **Crea archivi**: è necessario creare due archivi nell&#39;organizzazione: uno per il codice dell&#39;applicazione e uno per il progetto EDS. Per verificare, vai a [github.com/new](https://github.com/new). Se puoi selezionare la tua organizzazione dal menu a discesa **Proprietario**, disponi dell&#39;autorizzazione.

  ![Menu a discesa del proprietario del nuovo archivio GitHub con la selezione dell&#39;organizzazione](/help/assets/overview/github-repo-owner-dropdown.png)

- **Installa [!DNL GitHub] app**. Sono necessarie le autorizzazioni appropriate per installare un&#39;app [!DNL GitHub] nell&#39;organizzazione. Vedi [Requisiti per installare un&#39;app GitHub](https://docs.github.com/en/apps/using-github-apps/installing-a-github-app-from-a-third-party#requirements-to-install-a-github-app).

**Verifica le autorizzazioni prima della sessione di onboarding**

Esegui questa verifica rapida prima di incontrare Adobe. Il risultato è chi deve stare nella stanza, non se si può procedere.

1. Vai a [github.com/new](https://github.com/new), seleziona la tua organizzazione come proprietario e crea un archivio denominato `llm-apps-test`.
2. Passare alla pagina di installazione di [Controllo autorizzazioni app Adobe LLM](https://github.com/apps/adobe-llm-apps-permission-checker/installations/new) e installare l&#39;app solo per l&#39;archivio `llm-apps-test`.

| Risultato | Che cosa significa | Azione |
|---|---|---|
| Entrambi i passaggi hanno esito positivo | Disponi delle autorizzazioni necessarie | Sei pronto per la sessione di onboarding |
| Il passaggio 2 mostra **Richiesta** invece di **Installa** | Non si dispone dell&#39;autorizzazione per installare [!DNL GitHub] app | Invita l&#39;amministratore organizzazione [!DNL GitHub] alla riunione di onboarding |

Al termine, eliminare l&#39;archivio `llm-apps-test` e disinstallare l&#39;app di controllo delle autorizzazioni dalle impostazioni dell&#39;organizzazione.

## AEM Sites con [!DNL Edge Delivery Services]

I widget di azione sono ospitati in **Adobe Experience Manager [!DNL Edge Delivery Services] (EDS)**. La tua organizzazione necessita di una licenza AEM Sites che includa [!DNL Edge Delivery Services]. Devi avere il ruolo **Amministratore** nell&#39;organizzazione EDS.

Per verificare il funzionamento, vai allo strumento di amministrazione utenti di [EDS](https://tools.aem.live/tools/user-admin/index.html), immetti il nome della tua organizzazione, lascia vuoto **Sito** e fai clic su **Recupera utenti**. Trova il tuo account nell&#39;elenco e conferma che mostri il badge **admin**.

![Strumento di amministrazione utenti EDS che mostra un utente con il ruolo di amministratore](/help/assets/overview/eds-user-admin.png)

Se non disponi ancora di un’organizzazione EDS, non è necessaria alcuna azione; ne verrà creata una durante il processo di onboarding.

## Piattaforma LLM (per test)

Per testare l&#39;app implementata, è necessario un livello di abbonamento supportato che consenta l&#39;attivazione di app MCP personalizzate e della **modalità sviluppatore**. Ad esempio, [!DNL ChatGPT] richiede un abbonamento **Pro**, **Business** o **Enterprise / Edu**.
