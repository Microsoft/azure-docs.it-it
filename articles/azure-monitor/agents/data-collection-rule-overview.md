---
title: Regole di raccolta dati in monitoraggio di Azure (anteprima)
description: Panoramica delle regole di raccolta dati (DCR) in monitoraggio di Azure, inclusi il contenuto e la struttura e il modo in cui è possibile crearli e utilizzarli.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2021
ms.openlocfilehash: a0c5e9f89b983871224e79c2fc4f518a15d42a6f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039615"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Regole di raccolta dati in monitoraggio di Azure (anteprima)
Le regole di raccolta dati (DCR) definiscono i dati in arrivo in monitoraggio di Azure e specificano la posizione in cui devono essere inviati o archiviati i dati. Questo articolo fornisce una panoramica delle regole di raccolta dei dati, inclusi il contenuto e la struttura e il modo in cui è possibile crearli e utilizzarli.

## <a name="input-sources"></a>Origini di input
Le regole di raccolta dati supportano attualmente le origini di input seguenti:

- Macchina virtuale di Azure con l'agente di monitoraggio di Azure. Vedere [configurare la raccolta dati per l'agente di monitoraggio di Azure (anteprima)](../agents/data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Componenti di una regola di raccolta dati
Una regola di raccolta dati include i componenti seguenti.

| Componente | Descrizione |
|:---|:---|
| Origini dati | Origine univoca dei dati di monitoraggio con il proprio formato e metodo per esporre i dati. Esempi di un'origine dati includono registro eventi di Windows, contatori delle prestazioni e syslog. Ogni origine dati corrisponde a un determinato tipo di origine dati, come descritto di seguito. |
| Flussi | Handle univoco che descrive un set di origini dati che verranno trasformate e schematizzato come un unico tipo. Ogni origine dati richiede uno o più flussi e un flusso può essere usato da più origini dati. Tutte le origini dati in un flusso condividono uno schema comune. Usare più flussi, ad esempio, quando si vuole inviare una determinata origine dati a più tabelle nella stessa area di lavoro Log Analytics. |
| Destinazioni | Set di destinazioni in cui devono essere inviati i dati. Gli esempi includono Log Analytics area di lavoro, le metriche di monitoraggio di Azure e hub eventi di Azure. | 
| Flussi di dati | Definizione dei flussi da inviare a quali destinazioni. | 

Il diagramma seguente illustra i componenti di una regola di raccolta dati e la relativa relazione

[![Diagramma di DCR](media/data-collection-rule-overview/data-collection-rule-components.png)](media/data-collection-rule-overview/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Tipi di origini dati
Ogni origine dati ha un tipo di origine dati. Ogni tipo definisce un set univoco di proprietà che devono essere specificate per ogni origine dati. I tipi di origine dati attualmente disponibili sono illustrati nella tabella seguente.

| Tipo di origine dati | Descrizione | 
|:---|:---|
| estensione | Origine dati basata sull'estensione della macchina virtuale |
| performanceCounters | Contatori delle prestazioni per Windows e Linux |
| syslog | Eventi Syslog in Linux |
| windowsEventLogs | Registro eventi di Windows |


## <a name="limits"></a>Limiti
Per i limiti che si applicano a ogni regola di raccolta dati, vedere [limiti del servizio di monitoraggio di Azure](../service-limits.md#data-collection-rules).


## <a name="create-a-dcr"></a>Creare un DCR
È attualmente possibile usare uno dei metodi seguenti per creare un DCR:

- [Usare il portale di Azure](../agents/data-collection-rule-azure-monitor-agent.md) per creare una regola di raccolta dati e associarla a una o più macchine virtuali.
- Modificare direttamente la regola di raccolta dati in JSON e [inviarla usando l'API REST](/rest/api/monitor/datacollectionrules).
- Creare DCR e associazioni con l'interfaccia della riga di comando di [Azure](https://github.com/Azure/azure-cli-extensions/blob/master/src/monitor-control-service/README.md).
- Creare DCR e associazioni con Azure PowerShell.
  - [Get-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRule.md)
  - [New-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRule.md)
  - [Set-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Set-AzDataCollectionRule.md)
  - [Update-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Update-AzDataCollectionRule.md)
  - [Remove-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRule.md)
  - [Get-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRuleAssociation.md)
  - [New-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRuleAssociation.md)
  - [Remove-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRuleAssociation.md)

## <a name="sample-data-collection-rule"></a>Regola di raccolta dati di esempio
La regola di raccolta dati di esempio riportata di seguito è per le macchine virtuali con l'agente di gestione di Azure e include i dettagli seguenti:

- Dati sulle prestazioni
  - Raccoglie contatori specifici del processore, della memoria, del disco logico e del disco fisico ogni 15 secondi e carica ogni minuto.
  - Raccoglie contatori di processo specifici ogni 30 secondi e viene caricato ogni 5 minuti.
- Eventi Windows
  - Raccoglie gli eventi di sicurezza di Windows e i caricamenti ogni minuto.
  - Raccoglie gli eventi di sistema e le applicazioni Windows e i caricamenti ogni 5 minuti.
- syslog
  - Raccoglie gli eventi di debug, critici e di emergenza dalla funzionalità cron.
  - Raccoglie gli eventi di avviso, critici e di emergenza dalla funzionalità syslog.
- Destinazioni
  - Invia tutti i dati a un'area di lavoro Log Analytics denominata centralWorkspace.

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-Event"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Passaggi successivi

- [Creare una regola di raccolta dati](data-collection-rule-azure-monitor-agent.md) e un'associazione a essa da una macchina virtuale usando l'agente di monitoraggio di Azure.
