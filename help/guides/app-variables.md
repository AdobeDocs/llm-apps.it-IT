---
title: Configurare le variabili e i segreti dell’app
description: Aggiungi variabili specifiche dell’ambiente all’app Adobe LLM Apps, leggetele in un gestore delle azioni, distribuitele e risolvi i problemi più comuni.
source-git-commit: 0f9dd0a5b1008e183eb8ab5a4c2eae34a78923bb
workflow-type: tm+mt
source-wordcount: '1170'
ht-degree: 1%
---

# Configurare le variabili e i segreti dell’app {#app-variables}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Utilizza variabili e segreti per configurare l’app senza valori di codifica fissa nei relativi gestori di azioni. Ad esempio, configura l’URL API del catalogo dei prodotti per utilizzare un servizio di test in Stage e il servizio live in Produzione senza modificare il codice del gestore.

Le variabili contengono impostazioni non sensibili. I segreti sono destinati a valori sensibili come le chiavi API e i token di accesso.

>[!IMPORTANT]
>
>L&#39;interfaccia utente supporta attualmente **solo variabili**. Il supporto segreto è pianificato per una versione futura. Fino ad allora, **NON archiviare** password, chiavi API, token di accesso o altre informazioni riservate **nelle variabili**: la tabella delle impostazioni visualizza i valori delle variabili e consente agli utenti di copiarli.

**In questa guida:** Scopri come aggiungere una variabile app e leggerla in un gestore azioni. Distribuisci l’app e verifica che il gestore utilizzi il valore previsto.

## Prima di iniziare {#before-you-begin}

Hai bisogno di:

- **Accedi all&#39;archivio del gestore dell&#39;app** per aggiornare i gestori che devono leggere le variabili.
- **`@adobe/llm-apps-runtime`1.1.0 o versione successiva** in tale archivio. Controlla la versione prima di utilizzare l’helper seguente.

Per verificare la versione, esegui il seguente comando nell’archivio del gestore:

```bash
npm ls @adobe/llm-apps-runtime
```

Se la versione è precedente alla 1.1.0, aggiornarla, quindi eseguire il commit e il push di `package.json` e `package-lock.json`:

```bash
npm install @adobe/llm-apps-runtime@latest
```

## Gestisci variabili {#manage-variables}

Ogni ambiente, **[!UICONTROL Stage]** o **[!UICONTROL Produzione]**, ha le proprie variabili. Controllare **[!UICONTROL Workspace]** prima di aggiungere, aggiornare o eliminare una variabile. Le modifiche diventano effettive la prossima volta che distribuisci l’app in tale ambiente.

### Aggiungi una variabile {#add-variable-to-app}

In questo esempio viene utilizzata la variabile non sensibile `GREETING_PREFIX` con il valore `Good day` per ignorare il messaggio di saluto predefinito del gestore `Hello`. L&#39;aggiunta di una variabile **non** modifica automaticamente un&#39;azione: il gestore **deve** leggere la variabile.

#### Passaggio 1: aggiungere una variabile nell’interfaccia utente {#add-a-variable}

1. Apri l&#39;app e seleziona **[!UICONTROL Impostazioni]** nel menu di navigazione a sinistra.
2. Apri la scheda **[!UICONTROL Variabili e segreti]**.
3. In **[!UICONTROL Workspace]**, selezionare **[!UICONTROL Stage]** o **[!UICONTROL Produzione]**.
4. Seleziona **[!UICONTROL Aggiungi]**.

   ![Variabili e segreti: area di lavoro area di lavoro area di lavoro vuota con il pulsante Aggiungi](/help/assets/guide-app-variables/variables-empty.png)

5. Nella finestra di dialogo, immetti:
   - **[!UICONTROL Nome]**: `GREETING_PREFIX`.
   - **[!UICONTROL Tipo]**: lascia selezionata **[!UICONTROL Variabile]**.
   - **[!UICONTROL Valore]**: `Good day`.

   ![Aggiungi variabile o segreto — GREETING_PREFIX impostato su Good Day](/help/assets/guide-app-variables/add-variable-dialog.png)

6. Per salvare la variabile, selezionare **[!UICONTROL Aggiungi]**.

La variabile viene visualizzata nella tabella con la relativa data **[!UICONTROL Name]**, **[!UICONTROL Type]**, **[!UICONTROL Value]** e **[!UICONTROL Last updated]**. Per copiare il valore, utilizzate il controllo Copia adiacente.

![Variabili e segreti - GREETING_PREFIX salvato nell&#39;area di lavoro dello stage](/help/assets/guide-app-variables/variable-added.png)

>[!IMPORTANT]
>
>Il salvataggio aggiunge la variabile alla configurazione dell’ambiente selezionato. **not** aggiorna l&#39;app distribuita fino a quando non la ridistribuisci.

#### Passaggio 2: leggere la variabile nel gestore {#use-a-variable}

Nel repository del gestore, aprire `actions/<action-name>/index.js`. Assicurarsi che il gestore accetti un secondo argomento, `extra`, e legga la variabile con `getVariable`:

```javascript
const { getVariable } = require('@adobe/llm-apps-runtime');

module.exports = async ({ name = 'there' } = {}, extra) => {
  const prefix = getVariable(extra, 'GREETING_PREFIX') || 'Hello';

  return {
    content: [{ type: 'text', text: `${prefix}, ${name}!` }]
  };
};
```

Con il valore `Good day`, una chiamata con `name` impostato su `Ada` restituisce `Good day, Ada!` invece del valore predefinito `Hello, Ada!`. Se in seguito si modifica il valore in `Howdy`, il messaggio di saluto cambia dopo la ridistribuzione, senza un&#39;altra modifica del gestore.

Il nome nel codice **deve** corrispondere esattamente al nome nell&#39;interfaccia utente. Se la variabile non è impostata, `getVariable` restituisce `undefined`. Scegli un valore predefinito appropriato, come nell’esempio, oppure restituisce un errore chiaro se l’azione richiede l’impostazione.

>[!NOTE]
>
>Le variabili sono disponibili solo nei gestori di azioni dell&#39;app; sono **non** automaticamente disponibili per i widget.

Quando le modifiche del gestore sono pronte, esegui il commit e inviale all&#39;archivio del gestore dell&#39;app. La distribuzione successiva utilizza il codice push più recente. Per ulteriori informazioni sulla modifica dei gestori, vedere [Personalizzare un gestore generato](/help/guides/customize-handler.md).

#### Passaggio 3: distribuire e testare {#deploy-and-verify}

1. [Distribuisci l&#39;app](/help/guides/deploy-your-app.md) nello stesso ambiente selezionato in **[!UICONTROL Workspace]**.
2. Chiama l&#39;azione da una piattaforma LLM supportata con `name` impostato su `Ada`. Consulta [Test del plug-in ChatGPT](/help/guides/test-in-chatgpt.md) o [Test del connettore Claude](/help/guides/test-in-claude.md).
3. Verificare che la risposta sia `Good day, Ada!`. La risposta verifica che il gestore legga la variabile configurata e sostituisca il messaggio di saluto predefinito.

Per configurare l&#39;altro ambiente, selezionare l&#39;ambiente in **[!UICONTROL Workspace]**, ripetere l&#39;installazione con il valore appropriato, quindi distribuire e testare l&#39;app.

>[!NOTE]
>
>Stage e Produzione hanno **configurazioni indipendenti**. Le modifiche in un ambiente **non** influiscono sull&#39;altro. Se necessario, utilizza lo stesso nome in entrambi gli ambienti e scegli il valore appropriato per ciascuno di essi.

>[!TIP]
>
>Per eseguire il test in locale prima della distribuzione, vedere [Sviluppo e test del gestore locale](/help/reference/development.md) e passare le variabili al server locale:
>
>`node server/local.js --param 'LLMA_VARIABLE_NAMES=["GREETING_PREFIX"]' --param GREETING_PREFIX=Howdy`

### Aggiornare una variabile {#update-or-delete}

1. Selezionare il controllo di modifica nella riga della variabile.
2. Rivedi il **[!UICONTROL valore corrente]** della variabile e immetti un **[!UICONTROL nuovo valore]**.

   ![Aggiorna GREETING_PREFIX — cambia il valore da Good Day a Howdy](/help/assets/guide-app-variables/update-variable-dialog.png)

3. Seleziona **[!UICONTROL Aggiorna]**.
4. Distribuisci nuovamente l’app nello stesso ambiente e verifica il comportamento modificato.

L’aggiornamento di una variabile ne modifica solo il valore. **impossibile** rinominare le variabili. Per utilizzare un nome diverso, elimina la variabile esistente, aggiungi una nuova variabile e aggiorna il gestore per leggere il nuovo nome.

### Eliminare una variabile {#delete-a-variable}

1. Controlla se per qualsiasi azione è ancora necessaria la variabile. Se necessario, aggiorna e invia il gestore **first**.
2. Selezionare il controllo Elimina nella riga della variabile. Per eliminare più voci, selezionare le relative caselle di controllo e scegliere **[!UICONTROL Elimina]**.
3. Rivedi i nomi nella finestra di conferma, quindi seleziona **[!UICONTROL Elimina]**.

   ![Elimina GREETING_PREFIX — conferma l&#39;eliminazione definitiva](/help/assets/guide-app-variables/delete-variable-dialog.png)

4. Distribuisci nuovamente l’app nello stesso ambiente.

>[!IMPORTANT]
>
>**IMPOSSIBILE** annullare l&#39;eliminazione. Verifica di aver selezionato la variabile corretta. L’app implementata mantiene la configurazione esistente fino alla distribuzione successiva. Dopo tale distribuzione, i gestori **non ricevono più** la variabile eliminata, pertanto un&#39;azione che richiede la variabile potrebbe non riuscire.

## Regole e limiti {#good-to-know}

| Elemento | Regola |
|------|------|
| Nome | Fino a 64 caratteri: lettere maiuscole, cifre e caratteri di sottolineatura, senza iniziare con una cifra. **Deve** essere univoco per app e ambiente e deve corrispondere al gestore. |
| Nomi riservati | Nomi che iniziano con `LLMA_` e `MCP_SERVER_URL`. |
| Valore | **Richiesto**, fino a 500 caratteri. La piattaforma rimuove gli spazi iniziali e finali. |
| Limite | 50 variabili per app e ambiente. |
| Visibilità | I valori delle variabili sono visibili e copiabili. Il supporto segreto pianificato mantiene i valori salvati nascosti. |
| Modifiche | Ha effetto sulla prossima distribuzione nell’ambiente selezionato. |

## Risoluzione di problemi {#verify-configuration}

| Cosa vedi | Cosa fare |
|--------------|------------|
| **[!UICONTROL Aggiungi]** è disabilitato e nella pagina è visualizzato il limite di **[!UICONTROL Workspace raggiunto]** | Elimina le variabili non più necessarie. |
| *Utilizzare solo lettere maiuscole, cifre e caratteri di sottolineatura* | Rinomina, ad esempio `API_BASE_URL`. |
| *Questo nome è riservato dalla piattaforma* | Scegliere un nome che non inizi con `LLMA_` e non sia `MCP_SERVER_URL`. |
| *Esiste già una variabile con questo nome* | Aggiorna invece la variabile esistente. |
| *La variabile è stata appena modificata altrove* | Aggiorna la pagina e riprova. |
| *Impossibile caricare le variabili* | Ricarica la pagina. Se l’errore persiste, controlla di avere accesso all’app. |
| L’azione non utilizza il nuovo valore | Verifica di aver distribuito **dopo** il salvataggio della variabile e di aver testato lo stesso ambiente. Conferma che la modifica del gestore è stata inviata e che il nome della variabile corrisponde esattamente a **1&rbrace;.**&#x200B;**[!UICONTROL Ultimo aggiornamento]** mostra quando hai salvato il valore, non quando hai distribuito l&#39;app. |
| `getVariable is not a function` | L’app utilizza un runtime precedente alla versione 1.1.0. Aggiornalo come descritto in [Prima di iniziare](#before-you-begin), quindi distribuiscilo. |
| L’azione non riesce dopo aver eliminato una variabile | Aggiungi nuovamente la variabile o aggiorna il gestore in modo che non sia più necessario, quindi distribuiscilo. |

## Passaggio successivo {#whats-next}

- [Personalizzare un gestore generato](/help/guides/customize-handler.md)
- [Distribuire l’app](/help/guides/deploy-your-app.md)
