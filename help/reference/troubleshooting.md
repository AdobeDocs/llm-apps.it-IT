---
title: Risoluzione dei problemi per le app Adobe LLM
description: Soluzioni per problemi comuni durante la creazione, la distribuzione e il test delle app Adobe LLM.
source-git-commit: 1a99e2e80e50a3bcf9ce6fb910365202bf06e113
workflow-type: tm+mt
source-wordcount: '451'
ht-degree: 0%

---


# Risoluzione di problemi {#troubleshooting}

>[!IMPORTANT]
>
>[!DNL Adobe LLM Apps] è attualmente in Beta.
>
>Le funzioni, i flussi di lavoro e l’interfaccia utente mostrati qui non rappresentano necessariamente lo stato finale del prodotto. Per partecipare al Beta, invia un’e-mail a llm-apps-beta@adobe.com.

Fornisce informazioni sulla risoluzione dei problemi durante l&#39;utilizzo di [!DNL Adobe LLM Apps].

## Problemi comuni

| Sintomo | Possibile causa | Cosa provare |
|---------|----------------|-------------|
| L’app non viene visualizzata nella piattaforma LLM | La sottoscrizione alla piattaforma LLM non supporta le app MCP personalizzate o la modalità sviluppatore non è abilitata | Verifica che il piano supporti le app MCP personalizzate. Attiva modalità sviluppatore in **Impostazioni → app → Impostazioni avanzate** |
| Errore &quot;Impossibile connettersi&quot; nella piattaforma LLM | L&#39;URL del server MCP non è corretto o la distribuzione non è riuscita | Controlla nuovamente l’URL dalla pagina Dettagli app. Controllare la cronologia della distribuzione per individuare eventuali errori |
| Azione non richiamata | La piattaforma LLM non è in grado di associare la domanda dell&#39;utente all&#39;azione | Utilizza `@YourApp` per richiamarlo esplicitamente. Migliora la descrizione dell’azione per facilitare la corrispondenza dell’intento del modello |
| Widget non eseguito rendering | Gli URL dei widget EDS o i domini CSP non sono configurati correttamente | Verifica l’URL dello script e l’URL di incorporamento del widget nella finestra di dialogo Crea azione. Verifica che la risorsa CSP e i domini di connessione includano l’origine EDS |
| Risposta vuota o di errore | Il gestore presenta un bug o è mancante | Eseguire prima il test localmente con `npm start`. Vedi [Sviluppo locale](/help/reference/development.md#local-development) |
| Il widget viene caricato ma non mostra dati | La forma `structuredContent` non corrisponde a quanto previsto dal blocco | Registra `bridge.toolResult` nella funzione `decorate` del blocco e confronta con l&#39;output del gestore |
| L’implementazione non riesce in &quot;Clone and build&quot; (Clona e genera) | Errore di `npm install` o di compilazione del webpack nel tuo archivio | Esegui `npm install && npm run build` localmente per riprodurre l&#39;errore |
| La distribuzione non riesce in corrispondenza di &quot;Raccogli credenziali&quot; | Archivio non collegato o progetto Developer Console non configurato correttamente | Verifica che l’archivio sia collegato alla pagina Impostazioni dettagli app |
| Errore CORS durante il caricamento del widget | Nel sito EDS mancano `access-control-allow-origin` intestazioni | Configurare le intestazioni CORS tramite `admin.hlx.page` |
| L&#39;editor intestazioni HTTP restituisce `404 Error updating config: config not found` durante il salvataggio delle intestazioni CORS | Nella configurazione del sito manca una sezione `headers` | Consulta [Inizializzare la sezione delle intestazioni di configurazione del sito EDS](#initialize-the-eds-site-config-headers-section) di seguito |
| Il widget esegue il rendering in anteprima ma non nella piattaforma LLM | Il blocco torna ai dati di esempio in modalità di anteprima ma non riesce con i dati live | Verifica con `structuredContent` reale utilizzando l&#39;ispettore MCP o il curl |

## Inizializzare la sezione delle intestazioni di configurazione del sito EDS

Se l&#39;editor delle intestazioni HTTP restituisce `404 Error updating config: config not found`, nella configurazione del sito manca una sezione `headers`. Correggi manualmente:

1. Vai a [tools.aem.live/tools/headers-edit/index.html](https://tools.aem.live/tools/headers-edit/index.html), immetti l&#39;organizzazione e il sito e fai clic su **[!UICONTROL Recupera]**.
2. Aprire il browser DevTools (scheda Rete) e copiare il valore dell&#39;intestazione `x-auth-token` dalla richiesta Fetch.
3. Recupera la configurazione del sito corrente:

   ```bash
   curl -H "x-auth-token: $TOKEN" \
     https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json > config.json
   ```

4. Apri `config.json` e aggiungi `"headers": {}` all&#39;oggetto JSON.
5. PUBBLICA di nuovo la configurazione aggiornata:

   ```bash
   curl -X POST \
     -H "x-auth-token: $TOKEN" \
     -H "Content-Type: application/json" \
     -d @config.json \
     "https://admin.hlx.page/config/<your-github-org>/sites/<your-eds-repo>.json"
   ```

6. Ricarica l&#39;Editor intestazioni e salva l&#39;intestazione `Access-Control-Allow-Origin` normalmente.

