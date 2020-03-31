---
title: Creazione di avvisi delle metriche per i log in Monitoraggio di Azure
description: Esercitazione sulla creazione di avvisi delle metriche near real time sui dati di Log Analytics più diffusi.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/17/2018
ms.subservice: alerts
ms.openlocfilehash: 6b21f228858954292e7a3bc5561d5e86fcfaaf41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055170"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Creare avvisi delle metriche per i log in Monitoraggio di Azure

## <a name="overview"></a>Panoramica

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Monitoraggio di Azure un [tipo di avviso delle metriche](../../azure-monitor/platform/alerts-metric-near-real-time.md) che presenta vantaggi rispetto agli [avvisi classici](../../azure-monitor/platform/alerts-classic-portal.md). Sono disponibili metriche per un [lungo elenco di servizi di Azure](../../azure-monitor/platform/metrics-supported.md). Questo articolo illustra l'uso di un subset per la risorsa - `Microsoft.OperationalInsights/workspaces`.

È possibile usare gli avvisi di metrica nei log di Log Analytics estratti come metriche come parte delle metriche dai log, incluse le risorse in Azure o in locale. Le soluzioni di Log Analytics supportate sono elencate di seguito:

- [Contatori delle prestazioni](../../azure-monitor/platform/data-sources-performance-counters.md) per i computer Windows e Linux
- [Record di heartbeat per l'Integrità agente](../../azure-monitor/insights/solution-agenthealth.md)
- Record di [Gestione aggiornamenti](../../automation/automation-update-management.md)
- I log dei [dati dell'evento](../../azure-monitor/platform/data-sources-windows-events.md)

Esistono diversi vantaggi per l'uso di **avvisi delle metriche per i log** rispetto ad [avvisi dei log](../../azure-monitor/platform/alerts-log.md) basati su query in Azure. Alcuni vantaggi sono elencati di seguito:

- Gli avvisi delle metriche offrono funzionalità di monitoraggio quasi in tempo reale e gli avvisi delle metriche per i log creano una copia tramite fork dei dati dall'origine dei log per garantire lo stesso funzionamento.
- Gli avvisi delle metriche sono con stato e vengono notificati due volte, quando l'avviso viene generato e quando viene risolto, a differenza degli avvisi dei log che sono senza stato e vengono generati costantemente a ogni intervallo se la condizione di avviso viene soddisfatta.
- Gli avvisi delle metriche per i log offrono più dimensioni, consentendo di filtrare in base a valori specifici, ad esempio computer, tipo di sistema operativo e così via, senza la necessità di creare query nell'ambiente di analisi.

> [!NOTE]
> Metriche e/o dimensioni specifiche verranno visualizzate solo se i relativi dati esistono nel periodo scelto. Tali metriche sono disponibili per i clienti con le aree di lavoro di Azure Log Analytics.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Metriche e dimensioni supportate per i log

 Gli avvisi delle metriche supportano le metriche con dimensioni. Le dimensioni possono essere usate per filtrare le metriche al livello corretto. Viene indicato l'elenco completo delle metriche supportate per i log in [aree di lavoro di Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) tra le soluzioni supportate.

> [!NOTE]
> Per visualizzare una metrica supportata estratta da un'area di lavoro di Log Analytics tramite [Monitoraggio di Azure - Metriche](../../azure-monitor/platform/metrics-charts.md), è necessario creare un avviso di metrica per il log in tale metrica specifica. Le dimensioni scelte nell'avviso di metrica per i log, verranno visualizzate solo per l'esplorazione tramite Monitoraggio di Azure - Metriche.

## <a name="creating-metric-alert-for-log-analytics"></a>Creazione di avvisi delle metriche per Log Analytics

I dati delle metriche derivanti dai log più comuni vengono inviati prima di essere elaborati in Log Analytics in Monitoraggio di Azure - Metriche. Ciò consente agli utenti di sfruttare le funzionalità della piattaforma di metriche e degli avvisi delle metriche, tra cui gli avvisi con frequenza minima di 1 minuto.
Di seguito sono elencati i modi in cui creare un avviso delle metriche per i log.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Prerequisiti per creare un avviso delle metriche per i log

Prima che sia possibile usare la metrica per i log raccolta sui dati di Log Analytics, è necessario che siano configurati e disponibili gli elementi seguenti:

1. **area di lavoro Log Analytics attiva**: è necessario che sia presente un'area di lavoro Log Analytics valida e attiva. Per altre informazioni, vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](../../azure-monitor/learn/quick-create-workspace.md).
2. **L'agente è configurato per Log Analytics Workspace:** l'agente deve essere configurato per le macchine virtuali di Azure (e/o) locali per inviare dati nell'area di lavoro di Log Analytics usata nel passaggio precedente. Per altre informazioni, vedere [Panoramica degli agenti di Azure per monitorare macchine virtuali di Azure](../../azure-monitor/platform/agents-overview.md).
3. **È installata la**soluzione Log Analytics supportata : la soluzione Log Analytics deve essere configurata e l'invio di dati nell'area di lavoro di Log Analytics: le soluzioni supportate sono [contatori delle prestazioni per Windows & Linux](../../azure-monitor/platform/data-sources-performance-counters.md), i record Heartbeat per [l'integrità dell'agente,](../../azure-monitor/insights/solution-agenthealth.md)la [gestione](../../automation/automation-update-management.md)degli aggiornamenti e i [dati degli](../../azure-monitor/platform/data-sources-windows-events.md)eventi .
4. **Soluzioni di Log Analytics configurate per inviare log**: per la soluzione Log Analytics è necessario che siano abilitati i log e i dati necessari corrispondenti alle [metriche supportate per le aree di lavoro di Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces). Ad esempio, per il valore relativo alla *percentuale di memoria disponibile* il contatore relativo deve essere prima configurato nella soluzione [contatori delle prestazioni](../../azure-monitor/platform/data-sources-performance-counters.md).

## <a name="configuring-metric-alert-for-logs"></a>Configurazione degli avvisi delle metriche per i log

 Gli avvisi delle metriche possono essere creati e gestiti tramite il portale di Azure, i modelli di Resource Manager, l'API REST, PowerShell e l'interfaccia della riga di comando di Azure. Poiché gli avvisi delle metriche per i log sono una variante degli avvisi delle metriche, una volta soddisfatti i prerequisiti, possono essere creati per l'area di lavoro Log Analytics specificata. Tutte le caratteristiche e le funzionalità degli [ avvisi delle metriche](../../azure-monitor/platform/alerts-metric-near-real-time.md) sono applicabili agli avvisi delle metriche per i log, ad esempio lo schema di payload, i limiti di quota applicabile e il prezzo fatturato.

Per informazioni dettagliate ed esempi, vedere [Creazione e gestione degli avvisi delle metriche](https://aka.ms/createmetricalert). In particolare, per gli avvisi delle metriche per i log, seguire le istruzioni per la gestione e verificare quanto segue:

- La destinazione per un avviso delle metriche è un'*area di lavoro Log Analytics*
- Il segnale scelto per l'avviso delle metriche relativo all'*area di lavoro Log Analytics* selezionata deve essere di tipo **Metrica**
- Il filtro per condizioni o risorse specifiche deve essere applicato tramite filtri di dimensione perché le metriche per i log sono multidimensionali
- Quando si configura un *segnale logico*, un singolo avviso può essere creato per essere distribuito su più valori di dimensione (ad esempio computer)
- Se **non** si usa il portale di Azure per creare un avviso delle metriche per l'*area di lavoro Log Analytics* selezionata, è necessario prima creare manualmente una regola esplicita per convertire i dati di log in una metrica usando [Monitoraggio di Azure - Regole di query pianificate](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> Quando si crea un avviso di metrica per l'area di lavoro di Log Analytics tramite il portale di Azure, regola corrispondente per la conversione dei dati di log in metrica tramite [Monitoraggio di Azure:](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) le regole di query pianificate vengono create automaticamente in background, senza la necessità di alcun *intervento o azione dell'utente.* Per la creazione di avvisi delle metriche per i log tramite il portale di Azure, vedere la sezione [Modello di risorse per gli avvisi delle metriche per i log](#resource-template-for-metric-alerts-for-logs) sugli strumenti di esempio per creare una regola di conversione da log a metrica basata su una regola di query pianificata prima della creazione degli avvisi delle metriche. In caso contrario, non sarà presente alcun dato per l'avviso delle metriche sui log creati.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Modello di risorse per gli avvisi delle metriche per i log

Come indicato in precedenza, il processo per la creazione di avvisi delle metriche a partire dai log è costituito da due passaggi:

1. Creare una regola per estrarre le metriche dai log supportati tramite l'API scheduledQueryRule
2. Creare un avviso per la metrica estratta dal log (nel passaggio 1) e un'area di lavoro Log Analytics come risorsa di destinazione

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Avvisi delle metriche per i log con soglie statiche

Per ottenere lo stesso risultato, è possibile usare il modello di esempio di Azure Resource Manager seguente, in cui la creazione di avvisi delle metriche con soglie statiche dipende dalla creazione corretta della regola per estrarre le metriche dai log tramite scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Se il codice JSON precedente viene salvato come metricfromLogsAlertStatic.json, può essere abbinato a un file JSON dei parametri per la creazione basata su un modello di risorse. Un file JSON dei parametri di esempio è indicato di seguito:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Se il file dei parametri precedente viene salvato come metricfromLogsAlertStatic.parameters.json, è possibile creare l'avviso delle metriche per i log usando il [modello di risorse per la creazione nel portale di Azure](../../azure-resource-manager/templates/deploy-portal.md).

In alternativa, è possibile usare anche il comando di Azure Powershell seguente:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

In alternativa, distribuire il modello di risorse tramite l'interfaccia della riga di comando di Azure:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Avvisi delle metriche con soglie dinamiche

Per ottenere lo stesso risultato, è possibile usare il modello di esempio di Azure Resource Manager seguente, in cui la creazione di avvisi delle metriche con soglie dinamiche dipende dalla creazione corretta della regola per estrarre le metriche dai log tramite scheduledQueryRule.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Se il codice JSON precedente viene salvato come metricfromLogsAlertDynamic.json, può essere abbinato a un file JSON dei parametri per la creazione basata su un modello di risorse. Un file JSON dei parametri di esempio è indicato di seguito:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
          },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Se il file dei parametri precedente viene salvato come metricfromLogsAlertDynamic.parameters.json, è possibile creare l'avviso delle metriche per i log usando il [modello di risorse per la creazione nel portale di Azure](../../azure-resource-manager/templates/deploy-portal.md).

In alternativa, è possibile usare anche il comando di Azure Powershell seguente:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

In alternativa, distribuire il modello di risorse tramite l'interfaccia della riga di comando di Azure:

```azurecli
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sugli [avvisi delle metriche](alerts-metric.md).
- Per altre informazioni, fare riferimento agli [avvisi di log in Azure](../../azure-monitor/platform/alerts-unified-log.md).
- Informazioni sugli [avvisi in Azure](alerts-overview.md).
