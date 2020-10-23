---
title: Esaminare l'utilizzo delle risorse di Azure con l'API REST
description: Informazioni su come usare le API REST di Azure per esaminare l'uso delle risorse di servizio di Azure. Creare un report di gestione dei costi e filtrare per tipi di risorsa specifici.
author: lleonard-msft
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: reference
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 018e61fbc74091fa25211b8a25ce067862a0cc7d
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132398"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Esaminare l'utilizzo di risorse di Azure usando l'API REST

Le API di Gestione costi di Azure aiutano a esaminare e gestire il consumo delle risorse di Azure.

Questo articolo descrive come creare un report giornaliero in grado di generare un documento di valori delimitati da virgole, con le informazioni relative all'utilizzo orario e come usare successivamente i filtri per personalizzare il report, in modo che sia possibile eseguire una query sull'utilizzo di macchine virtuali, database e risorse contrassegnate in un gruppo di risorse di Azure.

>[!NOTE]
> L'API di Gestione costi è attualmente in modalità Private Preview.

## <a name="create-a-basic-cost-management-report"></a>Creare un report di gestione dei costi di base

Usare l'operazione `reports` nell'API Gestione costi per definire la modalità di generazione dei report di costi e il percorso in cui verrà pubblicato il report.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

Il parametro `{subscriptionGuid}` è obbligatorio e deve contenere un ID di sottoscrizione che può essere letto usando le credenziali fornite nel token API. 

Il parametro `{reportName}` specifica il nome del report. Per ottenere un elenco dei nomi dei report, è possibile usare l'operazione Reports_List: `/subscriptions/{subscriptionId}/providers/Microsoft.CostManagement/reports`. Visualizzare l'output di esempio in [GitHub](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/cost-management/resource-manager/Microsoft.CostManagement/preview/2018-08-01-preview/examples/ReportList.json).

Gli argomenti seguenti sono obbligatori:

|Intestazione della richiesta|Descrizione|  
|--------------------|-----------------|  
|*Content-Type:*| Obbligatorio. Impostare su `application/json`. |  
|*Authorization:*| Obbligatorio. Impostare un valore valido per il token API `Bearer`. |

Configurare i parametri del report nel corpo della richiesta HTTP. Nell'esempio seguente, il report è impostato per generarsi ogni giorno quando attivo; si tratta di un file CSV scritto in un contenitore blob di Archiviazione di Microsoft Azure e contiene informazioni sui costi su base oraria per tutte le risorse nel gruppo di risorse `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

Il valore di

## <a name="filtering-reports"></a>Filtraggio di report

Le sezioni `filter` e `dimensions` del corpo della richiesta durante la creazione di un report consentono di concentrarsi sui costi per tipo di risorsa specifico. Il corpo della richiesta precedente mostra come filtrare le risorse in base all’area.

### <a name="get-all-compute-usage"></a>Ottenere tutti gli utilizzi di calcolo

Usare la dimensione `ResourceType` per creare report dei costi delle macchine virtuali di Azure nella sottoscrizione di tutte le aree.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines",
                "Microsoft.Compute/virtualMachines"
        ]
    }
}
```

### <a name="get-all-database-usage"></a>Ottenere l'uso di tutti i database

Usare la dimensione `ResourceType` per creare report dei costi del database SQL di Azure nella sottoscrizione di tutte le aree.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ]
    }
}
```

### <a name="report-on-specific-instances"></a>Creare report su istanze specifiche

La dimensione `Resource` consente di creare report sui costi per risorse specifiche.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Modifica intervalli di tempo

Impostare la definizione `timeframe` su `Custom` per impostare un intervallo di tempo al di fuori delle opzioni predefinite da inizio mese e inizio settimana.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Passaggi successivi
- [Introduzione all'API REST di Azure](/rest/api/azure/)