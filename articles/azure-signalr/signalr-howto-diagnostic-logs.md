---
title: Log delle risorse per il servizio Azure SignalR
description: Informazioni su come configurare i log delle risorse per il servizio Azure SignalR e su come usarlo per risolvere i problemi di autonomia.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 5650ff0e039d1e9211b8d0013726e101efdfab78
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572252"
---
# <a name="resource-logs-for-azure-signalr-service"></a>Log delle risorse per il servizio Azure SignalR

Questa esercitazione illustra i log delle risorse per il servizio Azure SignalR, come configurarli e come risolverli. 

## <a name="prerequisites"></a>Prerequisiti
Per abilitare i log delle risorse, è necessario un punto in cui archiviare i dati di log. Questa esercitazione Usa archiviazione di Azure e Log Analytics.

* [Archiviazione di Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) : mantiene i log delle risorse per il controllo dei criteri, l'analisi statica o il backup.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) : strumento di ricerca e analisi dei log flessibile che consente di analizzare i log non elaborati generati da una risorsa di Azure.

## <a name="set-up-resource-logs-for-an-azure-signalr-service"></a>Configurare i log delle risorse per un servizio Azure SignalR

È possibile visualizzare i log delle risorse per il servizio Azure SignalR. Questi log forniscono una visualizzazione più dettagliata della connettività all'istanza del servizio Azure SignalR. I log delle risorse forniscono informazioni dettagliate su ogni connessione. Ad esempio, le informazioni di base (ID utente, ID connessione e tipo di trasporto e così via) e le informazioni sull'evento (connessione, disconnessione e evento di interruzione e così via) della connessione. i log delle risorse possono essere usati per l'identificazione dei problemi, il monitoraggio e l'analisi della connessione.

### <a name="enable-resource-logs"></a>Abilitare i log risorse

I log delle risorse sono disabilitati per impostazione predefinita. Per abilitare i log delle risorse, attenersi alla procedura seguente:

1. Nel [portale di Azure](https://portal.azure.com), in **monitoraggio**, fare clic su **impostazioni di diagnostica**.

    ![Spostamento riquadro in impostazioni di diagnostica](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Quindi fare clic su **Aggiungi impostazione di diagnostica**.

    ![Aggiungere i log delle risorse](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Impostare la destinazione dell'archivio desiderata. Attualmente è supportata **l'archiviazione in un account di archiviazione e l'** **invio a log Analytics**.

1. Selezionare i log che si desidera archiviare.

    ![Riquadro Impostazioni di diagnostica](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Salvare le nuove impostazioni di diagnostica.

Le nuove impostazioni diventano effettive in circa 10 minuti. Trascorso questo tempo, i log vengono visualizzati nella destinazione di archiviazione configurata, nel riquadro **Log di diagnostica**.

Per altre informazioni sulla configurazione della diagnostica, vedere [Panoramica dei log delle risorse di Azure](../azure-monitor/essentials/platform-logs-overview.md).

### <a name="resource-logs-categories"></a>Categorie di log delle risorse

Il servizio Azure SignalR acquisisce i log delle risorse in una categoria:

* **Tutti i log**: tenere traccia delle connessioni che si connettono al servizio Azure SignalR. I log forniscono informazioni sulla connessione, la disconnessione, l'autenticazione e la limitazione. Per ulteriori informazioni, vedere la sezione successiva.

### <a name="archive-to-a-storage-account"></a>Archivia in un account di archiviazione

I log vengono archiviati nell'account di archiviazione configurato nel riquadro **log di diagnostica** . Un contenitore denominato `insights-logs-alllogs` viene creato automaticamente per archiviare i log delle risorse. All'interno del contenitore i log vengono archiviati nel file `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` . Fondamentalmente, il percorso è combinato da `resource ID` e `Date Time` . I file di log sono suddivisi in base a `hour` . Pertanto, i minuti sono sempre `m=00` .

Tutti i log vengono archiviati in formato JavaScript Object Notation (JSON). Ogni voce presenta campi stringa che usano il formato descritto nelle sezioni seguenti.

Le stringhe JSON di log di archiviazione includono elementi elencati nelle tabelle seguenti:

**Formato**

Nome | Descrizione
------- | -------
time | Ora evento log
livello | Livello evento log
resourceId | ID risorsa del servizio Azure SignalR
posizione | Località del servizio Azure SignalR
category | Categoria dell'evento log
operationName | Nome dell'operazione dell'evento
callerIpAddress | Indirizzo IP del server/client
properties | Proprietà dettagliate correlate a questo evento del log. Per ulteriori dettagli, vedere la tabella delle proprietà seguente.

**Tabella delle proprietà**

Nome | Descrizione
------- | -------
type | Tipo di evento del log. Attualmente, vengono fornite informazioni sulla connettività al servizio Azure SignalR. `ConnectivityLogs`È disponibile solo il tipo
collection | Raccolta dell'evento log. I valori consentiti sono: `Connection` `Authorization` e `Throttling`
connectionId | Identità della connessione
transportType | Tipo di trasporto della connessione. I valori consentiti sono: `Websockets` \| `ServerSentEvents` \|`LongPolling`
connectionType | Tipo di connessione. I valori consentiti sono: `Server` \| `Client`. `Server`: connessione dal lato server; `Client`: connessione dal lato client
userId | Identità dell'utente
message | Messaggio dettagliato dell'evento del log

Il codice seguente è un esempio di stringa JSON di log di archiviazione:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Schema dei log di archiviazione per Log Analytics

Per visualizzare i log delle risorse, attenersi alla procedura seguente:

1. Fare clic `Logs` nel log Analytics di destinazione.

    ![Voce di menu Log Analytics](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Immettere `SignalRServiceDiagnosticLogs` e selezionare intervallo di tempo per eseguire query sui log delle risorse. Per le query avanzate, vedere [Introduzione a log Analytics in monitoraggio di Azure](../azure-monitor/logs/log-analytics-tutorial.md)

    ![Log di query in Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Le colonne del log di archiviazione includono gli elementi elencati nella tabella seguente:

Nome | Descrizione
------- | ------- 
TimeGenerated | Ora evento log
Raccolta | Raccolta dell'evento log. I valori consentiti sono: `Connection` `Authorization` e `Throttling`
OperationName | Nome dell'operazione dell'evento
Location | Località del servizio Azure SignalR
Level | Livello evento log
CallerIpAddress | Indirizzo IP del server/client
Message | Messaggio dettagliato dell'evento del log
UserId | Identità dell'utente
ConnectionId | Identità della connessione
ConnectionType | Tipo di connessione. I valori consentiti sono: `Server` \| `Client`. `Server`: connessione dal lato server; `Client`: connessione dal lato client
TransportType | Tipo di trasporto della connessione. I valori consentiti sono: `Websockets` \| `ServerSentEvents` \|`LongPolling`

### <a name="troubleshooting-with-resource-logs"></a>Risoluzione dei problemi con i log delle risorse

Per risolvere i problemi relativi al servizio Azure SignalR, è possibile abilitare i log lato server/client per l'acquisizione degli errori. Attualmente, il servizio Azure SignalR espone i log delle risorse, è anche possibile abilitare i log per il lato servizio.

Quando si verifica una situazione di crescita o eliminazione imprevista della connessione, è possibile sfruttare i log delle risorse per risolvere i problemi.

I problemi tipici sono spesso relativi a modifiche impreviste per le connessioni, le connessioni raggiungono i limiti di connessione e l'errore di autorizzazione. Vedere le sezioni successive sulla risoluzione dei problemi.

#### <a name="unexpected-connection-number-changes"></a>Modifiche impreviste al numero di connessione

##### <a name="unexpected-connection-dropping"></a>Eliminazione di connessioni imprevista

Se si verificano perdite di connessioni impreviste, abilitare prima di tutto i log nei lati servizio, server e client.

Se una connessione si disconnette, i log delle risorse registreranno questo evento di disconnessione. verrà visualizzato `ConnectionAborted` o `ConnectionEnded` in `operationName` .

La differenza tra `ConnectionAborted` e `ConnectionEnded` è che `ConnectionEnded` si tratta di una disconnessione prevista attivata dal lato client o server. Mentre `ConnectionAborted` è in genere un evento imprevisto di eliminazione della connessione e il motivo dell'interruzione verrà fornito in `message` .

I motivi di interruzione sono elencati nella tabella seguente:

Motivo | Descrizione
------- | ------- 
Il numero di connessioni raggiunge il limite | Il numero di connessioni raggiunge il limite del livello di prezzo corrente. Prendere in considerazione l'unità di servizio di scalabilità verticale
Il server applicazioni ha chiuso la connessione | Il server applicazioni attiva l'aborto. Può essere considerato come un aborto previsto
Timeout ping connessione | In genere è causata da un problema di rete. Provare a controllare la disponibilità del server app da Internet
Ricaricamento del servizio, riconnessione | Il servizio Azure SignalR viene ricaricato. Azure SignalR supporta la riconnessione automatica. è possibile attendere il completamento della riconnessione o riconnessione manuale al servizio Azure SignalR
Errore temporaneo del server interno | Si verifica un errore temporaneo nel servizio Azure SignalR, che deve essere recuperato automaticamente
Connessione al server eliminata | La connessione al server si interrompe con un errore sconosciuto. per prima cosa, provare a eseguire la risoluzione dei problemi con il log del lato servizio/server/client Provare a escludere problemi di base, ad esempio problema di rete, problema sul lato server app e così via. Se il problema non viene risolto, contattaci per ulteriori informazioni. Per ulteriori informazioni, vedere la sezione [ottenere la guida](#get-help) . 

##### <a name="unexpected-connection-growing"></a>Aumento imprevisto della connessione

Per risolvere i problemi relativi a un aumento imprevisto della connessione, è prima di tutto necessario filtrare le connessioni aggiuntive. È possibile aggiungere un ID utente di test univoco alla connessione client di test. Quindi, verificarlo con i log delle risorse. Se si visualizzano più connessioni client con lo stesso ID utente di test o IP, è probabile che il lato client crei e stabilisca più connessioni rispetto a quanto previsto. Verificare il lato client.

#### <a name="authorization-failure"></a>Errore di autorizzazione

Se viene restituito 401 non autorizzato per le richieste client, controllare i log delle risorse. Se si verifica `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` , significa che tutti i gruppi di destinatari nel token di accesso non sono validi. Provare a usare i destinatari validi suggeriti nel log.


#### <a name="throttling"></a>Limitazione

Se non è possibile stabilire connessioni client SignalR al servizio Azure SignalR, controllare i log delle risorse. Se si verifica `Connection count reaches limit` un log delle risorse, si stabiliscono troppe connessioni al servizio SignalR, che raggiungono il limite del numero di connessioni. Prendere in considerazione la scalabilità verticale del servizio SignalR. Se si verifica `Message count reaches limit` nel log delle risorse, significa che si usa il livello gratuito e si usa la quota dei messaggi. Se si desidera inviare più messaggi, provare a modificare il servizio SignalR nel livello standard per inviare messaggi aggiuntivi. Per altre informazioni, vedere [prezzi del servizio Azure SignalR](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Ottieni supporto

Si consiglia di eseguire prima la risoluzione dei problemi. La maggior parte dei problemi è causata da problemi di rete o del server dell'app. Seguire la [Guida alla risoluzione dei problemi con il log delle risorse](#troubleshooting-with-resource-logs) e la guida alla risoluzione dei [problemi di base](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md) per individuare la causa principale.
Se il problema persiste, è consigliabile aprire un problema in GitHub o creare un ticket nel portale di Azure.
Specificare:
1. Intervallo di tempo di circa 30 minuti quando si verifica il problema
2. ID risorsa del servizio Azure SignalR
3. Dettagli del problema, il più possibile specifico: ad esempio, AppServer non invia messaggi, le perdite di connessione client e così via
4. Log raccolti dal lato server/client e altro materiale che può essere utile
5. Opzionale Codice di ripetizione

> [!NOTE]
> Se si apre un problema in GitHub, lasciare private le informazioni riservate (ad esempio, ID risorsa, log server/client), inviare solo ai membri dell'organizzazione Microsoft privata.