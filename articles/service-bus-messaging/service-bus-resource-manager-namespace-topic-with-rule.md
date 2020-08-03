---
title: Creare una sottoscrizione e una regola di argomento del bus di servizio usando il modello di Azure
description: Creare uno spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola usando un modello di Azure Resource Manager
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli
ms.openlocfilehash: 01024ff7d6f152049acf7351313f11d1806d1b3b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496152"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Creare uno spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola usando un modello di Azure Resource Manager

Questo articolo illustra come usare un modello di Azure Resource Manager per creare uno spazio dei nomi del bus di servizio con un argomento, una sottoscrizione e una regola (filtro). L'articolo spiega come specificare le risorse da distribuire e come definire i parametri che devono essere specificati quando viene eseguita la distribuzione. È possibile usare questo modello per la distribuzione o personalizzarlo in base alle esigenze.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Azure Resource Manager][Authoring Azure Resource Manager templates].

Per altre informazioni su procedure e modelli relativi alle convenzioni di denominazione delle risorse di Azure, vedere [Recommended naming conventions for Azure resources][Recommended naming conventions for Azure resources] (Convenzioni di denominazione consigliate per le risorse di Azure).

Per il modello completo, vedere il [modello dello spazio dei nomi del bus di servizio con argomento, sottoscrizione e regola][Service Bus namespace with topic, subscription, and rule] su GitHub.

> [!NOTE]
> Questi modelli di Azure Resource Manager sono disponibili per il download e la distribuzione.
> 
> * [Creare uno spazio dei nomi del bus di servizio con coda e regola di autorizzazione](service-bus-resource-manager-namespace-auth-rule.md)
> * [Creare uno spazio dei nomi del bus di servizio con coda](service-bus-resource-manager-namespace-queue.md)
> * [Creare uno spazio dei nomi del bus di servizio](service-bus-resource-manager-namespace.md)
> * [Creare uno spazio dei nomi del bus di servizio con argomento e sottoscrizione](service-bus-resource-manager-namespace-topic.md)
> 
> Per verificare la presenza dei modelli più recenti, visitare la raccolta [modelli di avvio rapido di Azure][Azure Quickstart Templates] e cercare il bus di servizio.

## <a name="what-do-you-deploy"></a>Cosa distribuire?

Questo modello consente di distribuire uno spazio dei nomi del bus di servizio con un argomento, una sottoscrizione e una regola (filtro).

Gli [argomenti e le sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) offrono una forma di comunicazione uno-a-molti, in un schema di *pubblicazione/sottoscrizione*. Quando si usano gli argomenti e le sottoscrizioni, i componenti di un'applicazione distribuita non comunicano direttamente tra loro, ma scambiano messaggi tramite un argomento, che agisce da intermediario. La sottoscrizione a un argomento è simile a una coda virtuale che riceve copie dei messaggi che sono stati inviati all'argomento. L'applicazione di un filtro a una sottoscrizione consente di specificare quali messaggi inviati a un argomento devono essere presenti in una specifica sottoscrizione dell'argomento.

## <a name="what-are-rules-filters"></a>Che cosa sono le regole (filtri)?

In molti scenari, i messaggi con caratteristiche specifiche devono essere elaborati in modi specifici. Per abilitare questa elaborazione personalizzata, è possibile configurare le sottoscrizioni in modo che trovino i messaggi che presentano le proprietà specifiche e apportare quindi modifiche a tali proprietà. Anche se nelle sottoscrizioni del bus di servizio tutti i messaggi vengono inviati all'argomento, l'utente può copiare solo un sottoinsieme di tali messaggi nella coda virtuale delle sottoscrizioni. Questa operazione viene eseguita usando i filtri della sottoscrizione. Per altre informazioni sulle regole (filtri), vedere [Regole e azioni](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri

Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata `Parameters` che contiene tutti i valori dei parametri. Definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite.

Il modello definisce i parametri seguenti:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Nome dello spazio dei nomi del bus di servizio da creare.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Nome dell'argomento creato nello spazio dei nomi del bus di servizio.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Nome della sottoscrizione creata nello spazio dei nomi del bus di servizio.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusrulename"></a>serviceBusRuleName

Nome della regola (filtro) creata nello spazio dei nomi del bus di servizio.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

Versione API del bus di servizio del modello.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Risorse da distribuire

Crea uno spazio dei nomi del bus di servizio standard di tipo **Messaggistica** con argomento, sottoscrizione e regole.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

Per la sintassi e le proprietà JSON, vedere [Spazi dei nomi](/azure/templates/microsoft.servicebus/namespaces), [Argomenti](/azure/templates/microsoft.servicebus/namespaces/topics), [Sottoscrizioni](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions) e [Regole](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Comandi per eseguire la distribuzione

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire queste risorse, vedere gli articoli seguenti:

* [Gestire i bus di servizio di Azure](service-bus-management-libraries.md)
* [Gestire Bus di servizio con PowerShell](service-bus-manage-with-ps.md)
* [Gestire le risorse del bus di servizio con Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
