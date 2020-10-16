---
title: Visualizzazione dei log di diagnostica per Azure Data Lake Storage Gen1 | Microsoft Docs
description: 'Informazioni su come configurare e accedere ai log di diagnostica per Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 4476e20772c0736f35c074b200ea9fd47a0ae81c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109171"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Accesso ai log di diagnostica per Azure Data Lake Storage Gen1
Informazioni su come abilitare la registrazione diagnostica per l'account Azure Data Lake Storage Gen1 e visualizzare i log raccolti per l'account.

Le organizzazioni possono abilitare la registrazione diagnostica per l'account Azure Data Lake Storage Gen1 per raccogliere gli itinerari di controllo dell'accesso ai dati che forniscono informazioni come l'elenco di utenti che accedono ai dati, la frequenza con cui si accede ai dati, la quantità di dati archiviati nell'account e così via. Se abilitata, la diagnostica e/o le richieste vengono registrate in base al massimo sforzo. Vengono create voci nei log sia delle richieste che della diagnostica solo se esistono richieste effettuate verso l'endpoint di servizio.

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Account Azure Data Lake Storage Gen1**. Seguire le istruzioni fornite in [Introduzione ad Azure Data Lake Storage Gen1 con il portale di Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Abilitare la registrazione diagnostica per l'account di Data Lake Storage Gen1
1. Accedere al nuovo [portale di Azure](https://portal.azure.com).
2. Aprire l'account di Data Lake Storage Gen1 e nel pannello corrispondente fare clic su **Impostazioni di diagnostica**.
3. Nel pannello **Impostazioni di diagnostica**, fare clic su **Attivare la diagnostica**.

    ![Screenshot dell'account Data Lake Storage generazione 1 con l'opzione di impostazione di diagnostica e l'opzione attiva diagnostica denominata.](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Abilitare i log di diagnostica")

3. Nel pannello **Impostazioni di diagnostica**, apportare le modifiche seguenti per configurare la registrazione diagnostica.
   
    ![Screenshot della sezione dell'impostazione di diagnostica con la casella di testo nome e l'opzione Salva denominata.](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Abilitare i log di diagnostica")
   
   * Per **Nome** immettere un valore per la configurazione del log di diagnostica.
   * È possibile scegliere di archiviare/elaborare i dati in modi diversi.
     
        * Selezionare l'opzione per **archiviare in un account di archiviazione** per archiviare i log in un account di archiviazione di Azure. Utilizzare questa opzione se si desidera archiviare i dati che saranno elaborati in batch in un secondo momento. Se si seleziona questa opzione è necessario fornire un account di archiviazione di Azure in cui salvare i log.
        
        * Selezionare l'opzione per eseguire lo **streaming in Hub eventi** per trasmettere i dati di log a un Hub eventi di Azure. Molto probabilmente questa opzione viene utilizzata se si dispone di una pipeline di elaborazione a valle per analizzare in tempo reale i log in ingresso. Se si seleziona questa opzione, è necessario fornire i dettagli dell'Hub eventi di Azure che si desidera utilizzare.

        * Consente di selezionare l'opzione da **inviare a log Analytics** per usare il servizio monitoraggio di Azure per analizzare i dati di log generati. Se si seleziona questa opzione, è necessario fornire i dettagli per l'area di lavoro Log Analytics che si vuole usare per eseguire l'analisi dei log. Per informazioni dettagliate sull'uso dei log di monitoraggio di Azure, vedere [visualizzare o analizzare i dati raccolti con i log di monitoraggio di Azure](../azure-monitor/log-query/get-started-portal.md) .
     
   * Specificare se si desidera ottenere i log di controllo, i log delle richieste o entrambi.
   * Specificare il numero di giorni per cui devono essere conservati i dati. La conservazione dei dati è disponibile solo se si usano account di archiviazione di Azure per archiviare i dati del log.
   * Fare clic su **Salva**.

Dopo aver attivato le impostazioni di diagnostica, è possibile controllare i log nella scheda **Log di diagnostica** .

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Visualizzare i log di diagnostica dell'account di Data Lake Storage Gen1
Esistono due modi per visualizzare i dati di log dell'account Data Lake Storage Gen1.

* Dalla visualizzazione delle impostazioni dell'account Data Lake Storage Gen1
* Dall'account di Archiviazione di Azure dove sono archiviati i dati

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Usando la visualizzazione delle impostazioni dell'account Data Lake Storage Gen1
1. Nel pannello **Impostazioni** dell'account Data Lake Storage Gen1 fare clic su **Log di diagnostica**.
   
    ![Visualizzare i log di diagnostica](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Visualizzare i log di diagnostica") 
2. Nel pannello **Log di diagnostica** vengono visualizzati i log classificati come **log di controllo** e **log delle richieste**.
   
   * I log delle richieste acquisiscono tutte le richieste API fatte nell'account di Data Lake Storage Gen1.
   * I log di controllo sono simili a quelli delle richieste ma forniscono una scomposizione più dettagliata delle operazioni eseguite nell'account di Data Lake Storage Gen1. Ad esempio, una singola chiamata API di caricamento nei log delle richieste potrebbe risultare in molteplici operazioni di "Aggiungi" nei log di controllo.
3. Per scaricare i log, fare clic sul collegamento **Scarica** per ogni voce di log.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Dall'account di Archiviazione di Azure che contiene i dati di log
1. Aprire il pannello Account di Archiviazione di Azure associato a Data Lake Storage Gen1 per la registrazione e quindi fare clic su BLOB. Il pannello **Servizio BLOB** elenca due contenitori.
   
    ![Screenshot del pannello Data Lake Storage generazione 1 l'opzione BLOB selezionati e il pannello servizio Blog con i nomi dei due servizi BLOB richiamati.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Visualizzare i log di diagnostica")
   
   * Il contenitore **insights-logs-audit** contiene i log di controllo.
   * Il contenitore **insights-logs-requests** contiene i log delle richieste.
2. All'interno di questi contenitori i log vengono archiviati con la struttura seguente.
   
    ![Screenshot della struttura di log così come viene archiviato nel contenitore.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Visualizzare i log di diagnostica")
   
    Ad esempio, il percorso completo a un log di controllo potrebbe essere `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Analogamente, il percorso completo di un log richieste potrebbe essere `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Informazioni sulla struttura dei dati del log
I log di controllo e delle richieste sono in formato JSON. In questa sezione, viene esaminata la struttura di JSON per i log delle richieste e di controllo.

### <a name="request-logs"></a>Request Logs
Di seguito viene riportata una voce di esempio nel log delle richieste in formato JSON. Ogni BLOB ha un oggetto radice denominato **record** che contiene una matrice di oggetti di log.

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-07T21:02:53.456Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Requests",
        "operationName": "GETCustomerIngressEgress",
        "resultType": "200",
        "callerIpAddress": "::ffff:1.1.1.1",
        "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
        "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
        "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Schema del log delle richieste
| Nome | Tipo | Description |
| --- | --- | --- |
| time |string |Il timestamp del log (fusorario UTC) |
| resourceId |string |L’ID della risorsa interessata dall’operazione |
| category |string |La categoria di log. Ad esempio, **Richieste**. |
| operationName |string |Il nome dell'operazione registrata. Ad esempio, getfilestatus. |
| resultType |string |Lo stato dell'operazione, ad esempio 200. |
| callerIpAddress |string |L’indirizzo IP del client che esegue la richiesta |
| correlationId |string |L'ID del log che può essere usato per raggruppare un set di voci di log correlate |
| identity |Oggetto |L'identità che ha generato il log |
| properties |JSON |Vedere di seguito per ulteriori dettagli |

#### <a name="request-log-properties-schema"></a>Schema delle proprietà del log di richiesta
| Nome | Tipo | Description |
| --- | --- | --- |
| HttpMethod |string |Il metodo HTTP utilizzato per l'operazione. Esempio: GET. |
| Percorso |string |Il percorso coinvolto nell'operazione |
| RequestContentLength |INT |La lunghezza del contenuto della richiesta HTTP |
| ClientRequestId |string |L'ID che identifica la richiesta in modo univoco |
| StartTime |string |L'ora in cui il server ha ricevuto la richiesta |
| EndTime |string |L'ora in cui il server ha inviato una risposta |

### <a name="audit-logs"></a>Log di controllo
Di seguito viene riportata una voce di esempio nel log di controllo in formato JSON. Ogni BLOB ha un oggetto radice denominato **record** che contiene una matrice di oggetti log

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-08T19:08:59.359Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Audit",
        "operationName": "SeOpenStream",
        "resultType": "0",
        "resultSignature": "0",
        "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
        "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
        "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="audit-log-schema"></a>Schema del log di controllo
| Nome | Tipo | Description |
| --- | --- | --- |
| time |string |Il timestamp del log (fusorario UTC) |
| resourceId |string |L’ID della risorsa interessata dall’operazione |
| category |string |La categoria di log. Ad esempio, **Audit**. |
| operationName |string |Il nome dell'operazione registrata. Ad esempio, getfilestatus. |
| resultType |string |Lo stato dell'operazione, ad esempio 200. |
| resultSignature |string |Dettagli aggiuntivi sull'operazione. |
| correlationId |string |L'ID del log che può essere usato per raggruppare un set di voci di log correlate |
| identity |Oggetto |L'identità che ha generato il log |
| properties |JSON |Vedere di seguito per ulteriori dettagli |

#### <a name="audit-log-properties-schema"></a>Schema delle proprietà del log di controllo
| Nome | Tipo | Description |
| --- | --- | --- |
| StreamName |string |Il percorso coinvolto nell'operazione |

## <a name="samples-to-process-the-log-data"></a>Esempi per elaborare i dati di log
Quando si inviano log da Azure Data Lake Storage Gen1 ai log di monitoraggio di Azure (vedere [visualizzare o analizzare i dati raccolti con](../azure-monitor/log-query/get-started-portal.md) i log di monitoraggio di Azure ricerca per informazioni dettagliate sull'uso dei log di monitoraggio di Azure), la query seguente restituisce una tabella contenente un elenco di nomi visualizzati dall'utente, l'ora degli eventi e il numero di eventi per l'ora dell'evento insieme a un grafico visivo. È possibile modificarlo facilmente per mostrare i GUID utente o altri attributi:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 fornisce un esempio su come elaborare e analizzare i dati di log. È possibile trovare l'esempio in [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) . 

## <a name="see-also"></a>Vedi anche
* [Panoramica di Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)