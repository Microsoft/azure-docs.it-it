---
title: Monitorare e diagnosticare i contenitori Windows
description: In questa esercitazione si apprenderà come configurare i log di Monitoraggio di Azure per il monitoraggio e la diagnostica dei contenitori di Windows in Azure Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: eeb279892f987ed1f26ced97ab267e8140ccb20e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614061"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-azure-monitor-logs"></a>Esercitazione: Monitorare i contenitori di Windows in Service Fabric usando i log di Monitoraggio di Azure

Questa è la terza parte di un'esercitazione in cui si spiega come configurare i log di Monitoraggio di Azure per monitorare i contenitori di Windows orchestrati in Service Fabric.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare i log di Monitoraggio di Azure per il cluster di Service Fabric
> * Usare un'area di lavoro Log Analytics per visualizzare i log ed eseguire query su di essi dai contenitori e dai nodi
> * Configurare l'agente di Log Analytics per raccogliere le metriche relative ai contenitori e ai nodi

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario:

* Disporre di un cluster in Azure o [crearne uno seguendo le istruzioni in questa esercitazione](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Distribuire nel cluster un'applicazione nei contenitori](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-azure-monitor-logs-with-your-cluster-in-the-resource-manager-template"></a>Configurazione dei log di Monitoraggio di Azure con il cluster nel modello di Resource Manager

Nel caso in cui sia stato usato il [modello fornito](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) nella prima parte di questa esercitazione, è necessario aggiungere le modifiche seguenti a un modello generico di Azure Resource Manager di Service Fabric. Se si dispone di un cluster e si vuole configurarlo per monitorare i contenitori con i log di Monitoraggio di Azure:

* Apportare le modifiche seguenti al modello di Resource Manager.
* Distribuire il modello usando PowerShell per aggiornare il cluster tramite la [distribuzione del modello](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm). Azure Resource Manager viene informato dell'esistenza della risorsa e la distribuisce come aggiornamento.

### <a name="adding-azure-monitor-logs-to-your-cluster-template"></a>Aggiunta dei log di Monitoraggio di Azure al modello di cluster

Apportare le modifiche seguenti nel file *template.json*:

1. Aggiungere il nome e il percorso dell'area di lavoro Log Analytics nella sezione *parameters*:

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    Per modificare il valore usato per entrambi, aggiungere gli stessi parametri nel file *template.parameters.json* e modificare i valori usati in quel file.

2. Aggiungere il nome della soluzione e la soluzione nella sezione *variables*:

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Aggiungere Microsoft Monitoring Agent come estensione macchina virtuale. Trovare la risorsa set di scalabilità di macchine virtuali: *resources* >  *"apiVersion": "[variables('vmssApiVersion')]"* . In *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions* aggiungere la descrizione di estensione seguente nell'estensione *ServiceFabricNode*: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Aggiungere l'area di lavoro Log Analytics come singola risorsa. In *resources*, dopo la risorsa set di scalabilità di macchine virtuali, aggiungere quanto segue:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

[Qui](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/d2ffa318581fc23ac7f1b0ab2b52db1a0d7b4ba7/5-VM-Windows-OMS-UnSecure/sfclusteroms.json) è disponibile un modello di esempio (usato nella prima parte dell'esercitazione) che include tutte queste modifiche che può essere usato se necessario come riferimento. Queste modifiche determinano l'aggiunta di un'area di lavoro Log Analytics al gruppo di risorse. L'area di lavoro viene quindi configurata per raccogliere gli eventi della piattaforma Service Fabric dalle tabelle di archiviazione configurate con l'agente di [Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md). È stato aggiunto anche l'agente Log Analytics (Microsoft Monitoring Agent) a ogni nodo nel cluster come estensione macchina virtuale. Questo significa che ridimensionando il cluster, l'agente viene automaticamente configurato in ogni computer e collegato alla stessa area di lavoro.

Distribuire il modello con le nuove modifiche per aggiornare il cluster attuale. Al termine, le risorse di Log Analytics saranno visibili nel gruppo di risorse. Quando il cluster è pronto, distribuirvi l'applicazione nei contenitori. Nel passaggio successivo si imposterà il monitoraggio dei contenitori.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>Aggiungere la soluzione Monitoraggio contenitori all'area di lavoro Log Analytics

Per configurare la soluzione Contenitori nell'area di lavoro, cercare *Soluzione Monitoraggio contenitori* e creare una risorsa Contenitori (nella categoria Monitoraggio e gestione).

![Aggiunta della soluzione Contenitori](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Quando viene chiesto di immettere l'*area di lavoro di Log Analytics* selezionare l'area di lavoro creata nel gruppo di risorse e fare clic su **Crea**. Questa operazione aggiunge una *Soluzione Monitoraggio contenitori* all'area di lavoro e fa sì che l'agente Log Analytics distribuito dal modello inizi a raccogliere statistiche e log di Docker. 

Tornare al *gruppo di risorse*, in cui è ora visibile la soluzione di monitoraggio appena aggiunta. Se si fa clic sulla soluzione, viene visualizzata la pagina di destinazione in cui viene mostrata la serie di immagini di contenitori in esecuzione.

*Si noti che sono state eseguite 5 istanze del contenitore fabrikam derivanti dalla [seconda parte](service-fabric-host-app-in-a-container.md) dell'esercitazione*.

![Pagina di destinazione della soluzione Contenitori](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Se si fa clic nella **Soluzione Monitoraggio contenitori**, viene visualizzato un dashboard più dettagliato che consente di esaminare più pannelli, nonché di eseguire query sui log di Monitoraggio di Azure.

*Si noti che da settembre 2017 la soluzione è oggetto di alcuni aggiornamenti. Eventuali errori che si possono ottenere da eventi Kubernetes devono essere ignorati, in quanto si sta lavorando all'integrazione di più agenti di orchestrazione nella stessa soluzione.*

Poiché raccoglie i log Docker, per impostazione predefinita l'agente mostra *stdout* e *stderr*. Se si scorre la schermata verso destra, si possono vedere l'inventario delle immagini dei contenitori, lo stato, le metriche e alcune query di esempio che è possibile eseguire per ottenere dati più utili.

![Dashboard della soluzione Contenitori](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Se si fa clic in uno qualsiasi di questi pannelli, viene visualizzata la query di Kusto che genera il valore visualizzato. Modificare la query in *\** per vedere tutti i diversi generi di log che è possibile usare. Da qui è possibile eseguire una query o applicare un filtro sulle prestazioni di contenitore, i log, oppure esaminare gli eventi della piattaforma Service Fabric. Gli agenti installati emettono inoltre costantemente un heartbeat da ogni nodo, che è possibile esaminare per verificare che i dati vengano ancora raccolti da tutti i computer interessati nel caso in cui venga modificata la configurazione del cluster.

![Query di contenitore](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>Configurare l'agente Log Analytics per acquisire contatori delle prestazioni

Un altro vantaggio dell'uso dell'agente Log Analytics è la possibilità di modificare i contatori delle prestazioni che si vogliono acquisire attraverso l'interfaccia utente di Log Analytics, anziché dover configurare l'agente di diagnostica di Azure ed eseguire un aggiornamento basato su modelli di Resource Manager ogni volta. A tale scopo, fare clic sull'**area di lavoro di OMS** nella pagina di destinazione della Soluzione Monitoraggio contenitori (o Service Fabric).

Verrà visualizzata l'area di lavoro Log Analytics, in cui è possibile visualizzare le proprie soluzioni, creare dashboard personalizzati e configurare l'agente Log Analytics. 
* Per aprire il menu Impostazioni avanzate, fare clic su **Impostazioni avanzate**.
* Fare clic su **Origini connesse** > **Server Windows** e verificare che siano presenti *5 computer Windows connessi*.
* Fare clic su **Dati** > **Contatori delle prestazioni di Windows** per cercare e aggiungere nuovi contatori delle prestazioni. Qui viene visualizzato un elenco di contatori delle prestazioni consigliati dai log di Monitoraggio di Azure da cui è possibile raccogliere dati. È anche possibile cercare altri contatori. Verificare che i contatori **Processor(_Total)\% Processor Time** e **Memory(*)\Available MBytes** vengano raccolti.

**Aggiornare** la soluzione Monitoraggio contenitori e in pochi minuti si dovrebbero iniziare a vedere i dati relativi alle *prestazioni del computer*. Questi dati consentono di capire come vengono usate le risorse. È possibile usare queste metriche anche per prendere decisioni appropriate sul ridimensionamento del cluster o per verificare se il bilanciamento del carico in un cluster avviene come previsto.

*Nota: assicurarsi di impostare i filtri temporali nel modo appropriato per usare queste metriche.*

![Contatori delle prestazioni 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Configurare i log di Monitoraggio di Azure per il cluster di Service Fabric
> * Usare un'area di lavoro Log Analytics per visualizzare i log ed eseguire query su di essi dai contenitori e dai nodi
> * Configurare l'agente di Log Analytics per raccogliere le metriche relative ai contenitori e ai nodi

Il monitoraggio dell'applicazione in contenitori è ora impostato ed è quindi possibile eseguire le operazioni seguenti:

* Configurare i log di Monitoraggio di Azure per un cluster Linux seguendo una procedura simile a quella appena illustrata. Fare riferimento a [questo modello](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS) per apportare le modifiche al modello di Resource Manager.
* Configurare i log di Monitoraggio di Azure per impostare gli [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) per semplificare il rilevamento e la diagnostica.
* Esplorare l'elenco di [contatori delle prestazioni consigliati](service-fabric-diagnostics-event-generation-perf.md) in Service Fabric per configurare i cluster.
* Acquisire familiarità con le funzionalità di [ricerca log ed esecuzione di query](../log-analytics/log-analytics-log-searches.md) incluse nei log di Monitoraggio di Azure.
