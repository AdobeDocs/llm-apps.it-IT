---
title: Test in ChatGPT
description: Scopri come aggiungere l’app Adobe LLM implementata a ChatGPT e testarla in una conversazione reale.
source-git-commit: 483a71f5f1de5caf1bd89b26f4d67d2d5a0aa15a
workflow-type: tm+mt
source-wordcount: '798'
ht-degree: 2%

---


# Prova in [!DNL ChatGPT]

>[!IMPORTANT]
>
>**Dichiarazione di non responsabilità:** Versione beta di [!DNL LLM Apps]. Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale dell’applicazione o del prodotto.

>[!NOTE]
>
>Questa guida utilizza [!DNL ChatGPT] come esempio. I passaggi generali — registrazione di un URL del server MCP e test in una conversazione — si applicano anche ad altre piattaforme LLM, anche se il flusso di configurazione e l&#39;interfaccia utente variano.

Dopo una distribuzione corretta, l&#39;app è in esecuzione su [!DNL Adobe I/O Runtime] ed espone un URL del server MCP. Questa guida illustra come aggiungerlo a [!DNL ChatGPT] e testarlo in una conversazione reale.

## Requisiti del piano

L&#39;aggiunta di app per sviluppatori personalizzate a [!DNL ChatGPT] è regolata dai livelli di abbonamento di OpenAI. Non si tratta di una limitazione di [!DNL LLM Apps], ma del modo in cui OpenAI gestisce attualmente l&#39;accesso alle app MCP personalizzate.

| [!DNL ChatGPT] piano | App MCP personalizzate |
|--------------|-----------------|
| Gratuito | Non disponibile |
| Vai | Non disponibile |
| Più | Non disponibile |
| Pro | Disponibile |
| Economia | Disponibile |
| Enterprise/Edu | Disponibile |

>[!NOTE]
>
>Se sei su un piano Gratis, Go o Plus, **non potrai aggiungere l&#39;app implementata** a [!DNL ChatGPT]. Esegui l&#39;aggiornamento a **Pro** o chiedi all&#39;amministratore della tua organizzazione di abilitarlo in un&#39;area di lavoro **Business** o **Enterprise**.

## Abilita modalità sviluppatore

Per aggiungere un&#39;app MCP personalizzata, devi avere **modalità sviluppatore** abilitata nel tuo account [!DNL ChatGPT]. Segui
i passaggi seguenti per verificarlo e abilitarlo.

### Apri impostazioni

Fai clic sull&#39;avatar del tuo profilo in basso a sinistra, quindi fai clic su **[!UICONTROL Impostazioni]**.

![ChatGPT — Menu Impostazioni](/help/assets/guide-test-chatgpt/chatgpt-settings-menu.png)

### Passa ad App

Nella finestra di dialogo Impostazioni, seleziona **[!UICONTROL App]** nella barra laterale a sinistra. Fai clic su **[!UICONTROL Impostazioni avanzate]** in basso.

![ChatGPT — Impostazioni app](/help/assets/guide-test-chatgpt/chatgpt-apps-settings.png)

### Attiva modalità sviluppatore

Assicurarsi che l&#39;interruttore **[!UICONTROL Modalità sviluppatore]** sia attivato (blu). Questo consente di registrare URL di server MCP personalizzati e non verificati.

>[!NOTE]
>
>La modalità sviluppatore è etichettata *Rischio elevato* perché consente app che non sono state esaminate da OpenAI. [!DNL ChatGPT] disabilita automaticamente la memoria per le conversazioni che utilizzano app in modalità sviluppatore.

![ChatGPT - Modalità sviluppatore abilitata](/help/assets/guide-test-chatgpt/chatgpt-developer-mode.png)

## Aggiungi app a [!DNL ChatGPT]

### Copia l&#39;URL del server MCP

Vai alla pagina **Dettagli app** in [!DNL LLM Apps] e individua la sezione **[!UICONTROL Verifica l&#39;app]**. Copiare l&#39;URL **Staging** o **Produzione**, che avrà l&#39;aspetto seguente:

```
https://<namespace>.adobeioruntime.net/api/v1/web/llm-apps/mcp
```

### Apri la pagina App.

In [!DNL ChatGPT], vai a **[!UICONTROL Impostazioni] → [!UICONTROL App]**.

![ChatGPT - Pagina app](/help/assets/guide-test-chatgpt/chatgpt-apps-page.png)

### Creare una nuova app

Fare clic su **[!UICONTROL Crea app]** nella riga Impostazioni avanzate.

![ChatGPT — Finestra di dialogo Crea app](/help/assets/guide-test-chatgpt/chatgpt-create-app.png)

Compila quanto segue:

| Campo | Valore |
|-------|-------|
| **Icona** | Opzionale: caricamento di un file PNG 128x128 (max 10 KB) |
| **Nome** | Un nome visualizzato per l&#39;app (ad esempio, *My Brand App*) |
| **Descrizione** | Breve descrizione delle funzioni dell’app |
| **URL server MCP** | Incolla l&#39;URL da [!DNL LLM Apps] |
| **[!UICONTROL Autenticazione]** | Seleziona *Nessuna autenticazione* |

Selezionare **La casella di controllo** riconosce che il server MCP
non è stato esaminato da OpenAI e fare clic su **Crea**.

### Verifica che l’app sia abilitata

Dopo la creazione, l&#39;app viene visualizzata in **[!UICONTROL App abilitate]** con un badge **[!UICONTROL DEV]**, che ne conferma l&#39;attivazione.

>[!NOTE]
>
>L&#39;app viene visualizzata anche in **Bozze**. Si tratta di app private create in modalità sviluppatore e visibili solo al tuo account.

L&#39;app è ora pronta per essere utilizzata nelle [!DNL ChatGPT] conversazioni.

![ChatGPT — app abilitata](/help/assets/guide-test-chatgpt/chatgpt-app-enabled.png)

## Eseguire il test in una conversazione

Una volta abilitata l&#39;app, avviare una nuova conversazione in [!DNL ChatGPT]. Prima di porre una domanda, allega l’app utilizzando uno dei due metodi.

### Opzione 1: selezionare dal menu

Fai clic sul pulsante **+** nell&#39;input della chat, quindi su **Altro** per espandere l&#39;elenco completo degli strumenti disponibili. Seleziona l’app dall’elenco per allegarla alla conversazione corrente.

![ChatGPT — seleziona l&#39;app dal menu](/help/assets/guide-test-chatgpt/chatgpt-select-app.png)

### Opzione 2 — @mention d’uso

Digita **@** nell&#39;input della chat e seleziona l&#39;app dal menu a discesa. In questo modo l’app viene collegata in linea e puoi continuare a digitare la domanda nello stesso messaggio.

>[!NOTE]
>
>Se si utilizza **@mention** una seconda volta nella stessa app, verrà deselezionato e rimosso dalla conversazione.

![ChatGPT — @mention l&#39;app](/help/assets/guide-test-chatgpt/chatgpt-mention-app.png)

Una volta selezionata, l’app viene collegata in linea e puoi digitare la domanda nello stesso messaggio:

![ChatGPT — app collegata tramite @mention](/help/assets/guide-test-chatgpt/chatgpt-mention.png)

### Visualizza il risultato

Dopo aver collegato l&#39;app, digitare una domanda allineata a una delle azioni configurate, ad esempio *&quot;Mostra prodotti&quot;*. [!DNL ChatGPT] corrisponde all&#39;azione rilevante, estrae i parametri di input, chiama il gestore su [!DNL Adobe I/O Runtime] ed esegue il rendering del risultato:

![ChatGPT — risultato azione](/help/assets/guide-test-chatgpt/chatgpt-response.png)

La risposta include:

- **Il widget EDS** è un componente dell&#39;interfaccia utente avanzato con immagini, valutazioni e pulsanti di azione.
- **La risposta testuale**: sotto il widget, [!DNL ChatGPT] utilizza `content` restituito dal gestore
formulare un riassunto in linguaggio naturale dei risultati.
- **Indicatore di stato**: il *testo di stato richiamato* configurato nella finestra di dialogo Crea azione.

## Passaggio successivo

- **Aggiungi altre azioni** — definisci ulteriori azioni nell&#39;interfaccia utente, scrivi i relativi gestori e ridistribuisci.
- **Distribuisci in produzione** — se hai eseguito il test in Stage, distribuisci in produzione per l&#39;esperienza live.
- **Condividi con il tuo team**. Utilizza **Copia URL** nella pagina Dettagli app per condividere l&#39;URL del server MCP con i colleghi.

