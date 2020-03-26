---
title: Configurare il servizio Device Provisioning in hub IoT di Azure con un modello di Azure Resource Manager
description: 'Avvio rapido di Azure: configurare il servizio Device Provisioning in hub IoT di Azure con un modello'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 482401b75cadf44e2cef03cced8dd216d0980524
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "74969582"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Guida introduttiva: Configurare il servizio Device Provisioning in hub IoT con un modello di Azure Resource Manager

È possibile usare [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) per configurare a livello di codice le risorse cloud di Azure necessarie per il provisioning dei dispositivi. Questa procedura illustra come creare un hub IoT e un nuovo servizio Device Provisioning in hub IoT e come collegare i due servizi con un modello di Azure Resource Manager. Questo argomento di avvio rapido usa l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) per eseguire i passaggi a livello di codice necessari per creare un gruppo di risorse e distribuire il modello, ma è possibile usare in modo semplice il [portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby o altri linguaggi di programmazione per eseguire questa procedura e distribuire il modello. 


## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- Questa guida di avvio rapido richiede l'esecuzione dell'interfaccia della riga di comando di Azure nell'ambiente locale. È necessario che sia installata l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario installare o aggiornare l'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Accedere ad Azure e creare un gruppo di risorse

Accedere al proprio account Azure e selezionare la sottoscrizione.

1. Al prompt dei comandi eseguire il [comando per l'accesso][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Seguire le istruzioni per l'autenticazione tramite il codice e accedere all'account Azure con un Web browser.

2. Se si usano più sottoscrizioni di Azure, effettuando l'accesso ad Azure è possibile accedere a tutti gli account Azure associati alle credenziali. Usare il seguente [comando per elencare gli account Azure][lnk-az-account-command] che è possibile usare:
    
    ```azurecli
    az account list 
    ```

    Usare il comando seguente per selezionare la sottoscrizione che si vuole usare per eseguire i comandi per creare l'hub IoT. È possibile usare il nome o l'ID della sottoscrizione dall'output del comando precedente:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Le risorse del cloud di Azure come hub IoT e servizi di provisioning vengono create in un gruppo di risorse. Usare un gruppo di risorse esistente o eseguire questo [comando per creare un gruppo di risorse][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > L'esempio precedente crea il gruppo di risorse nella località Stati Uniti occidentali. È possibile visualizzare un elenco di località disponibili eseguendo il comando `az account list-locations -o table`.
    >
    >

## <a name="create-a-resource-manager-template"></a>Creare un modello di Resource Manager

Usare un modello JSON per creare un servizio di provisioning e un hub IoT collegato nel gruppo di risorse. È anche possibile usare un modello di Azure Resource Manager per apportare modifiche a un servizio di provisioning o hub IoT esistente.

1. Usare un editor di testo per creare un modello di Azure Resource Manager denominato **template.json** con la struttura seguente. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Sostituire la sezione **parameters** con il contenuto seguente. La sezione parameters definisce i parametri i cui valori possono essere passati da un altro file. Questa sezione definisce il nome dell'hub IoT e del servizio di provisioning da creare. Definisce anche la posizione dell'hub IoT e del servizio di provisioning. I valori saranno limitati alle aree di Azure che supportano hub IoT e servizi di provisioning. Per un elenco delle posizioni supportate per il servizio Device Provisioning, è possibile eseguire il comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` oppure passare alla pagina [Stato di Azure](https://azure.microsoft.com/status/) e cercare "Servizio Device Provisioning".

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Sostituire la sezione **variables** con il contenuto seguente. Questa sezione definisce i valori usati in un secondo momento per costruire la stringa di connessione dell'hub IoT, necessaria per collegare il servizio di provisioning e l'hub IoT. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. Per creare un hub IoT, aggiungere le righe seguenti alla raccolta **resources**. Il codice JSON specifica le proprietà minime necessarie per creare un hub IoT. I valori **name** e **location** vengono passati come parametri da un altro file. Per altre informazioni sulle proprietà che è possibile specificare per un hub IoT in un modello, vedere le [informazioni di riferimento sul modello Microsoft.Devices/IotHubs](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. Per creare un servizio di provisioning, aggiungere le righe seguenti dopo la specifica dell'hub IoT nella raccolta **resources**. I valori **name** e **location** del servizio di provisioning vengono passati come parametri. La raccolta **iotHubs** specifica gli hub IoT da collegare al servizio di provisioning. È necessario specificare almeno le proprietà **connectionString** e **location** per ogni hub IoT collegato. È anche possibile impostare proprietà quali **allocationWeight** e **applyAllocationPolicy** in ogni hub IoT, nonché proprietà quali **allocationPolicy** e  **authorizationPolicies** nel servizio di provisioning stesso. Vedere anche le [informazioni di riferimento sul modello Microsoft.Devices/provisioningServices](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices).

   La proprietà **dependsOn** viene usata per assicurare che Resource Manager crei l'hub IoT prima di creare il servizio di provisioning. Il modello richiede la stringa di connessione dell'hub IoT per specificare il collegamento al servizio di provisioning, quindi l'hub e le relative chiavi devono essere creati prima. Il modello usa funzioni quali **concat** e **listKeys** per creare la stringa di connessione da variabili con parametri. Per altre informazioni, vedere [Funzioni del modello di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions).

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]",
                        "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Salvare il file di modello. Il modello completato avrà un aspetto simile al seguente:

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]",
                           "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Creare un file di parametri di Resource Manager

Il modello definito nel passaggio precedente usa i parametri per specificare il nome dell'hub IoT, il nome del servizio di provisioning e la posizione (area di Azure) in cui crearli. Questi parametri vengono passati nel modello da un file separato. È così possibile riutilizzare lo stesso modello per più distribuzioni. Per creare il file di parametri, seguire questi passaggi:

1. Usare un editor di testo per creare un file di parametri di Azure Resource Manager denominato **parameters.json** con la struttura seguente: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Aggiungere il valore **iotHubName** alla sezione parameters.  Un nome dell'hub IoT deve essere univoco a livello globale in Azure, quindi potrebbe essere necessario aggiungere un prefisso o un suffisso univoco al nome di esempio oppure scegliere un nuovo nome. Assicurarsi che il nome segua le convenzioni di denominazione appropriate per un hub ioT: deve avere una lunghezza compresa tra 3 e 50 caratteri e può contenere solo caratteri alfanumerici maiuscoli o minuscoli oppure trattini ('-'). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Aggiungere il valore **provisioningServiceName** alla sezione parameters. Sarà anche necessario scegliere un nome univoco a livello globale per il servizio di provisioning. Assicurarsi che il nome segua le convenzioni di denominazione appropriate per un servizio Device Provisioning in hub IoT: deve avere una lunghezza compresa tra 3 e 64 caratteri e può contenere solo caratteri alfanumerici maiuscoli o minuscoli oppure trattini ('-').

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Aggiungere il valore **hubLocation** alla sezione parameters. Questo valore specifica la posizione dell'hub IoT e del servizio di provisioning. Il valore deve corrispondere a una delle posizioni specificate nella raccolta **allowedValues** della definizione dei parametri del file modello. Questa raccolta limita i valori alle aree di Azure che supportano hub IoT e servizi di provisioning. Per un elenco delle posizioni supportate per il servizio Device Provisioning, è possibile eseguire il comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` oppure passare alla pagina [Stato di Azure](https://azure.microsoft.com/status/) e cercare "Servizio Device Provisioning".

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Salvare il file. 


> [!IMPORTANT]
> L'hub IoT e il servizio di provisioning saranno individuabili pubblicamente come endpoint DNS, quindi evitare di indicare informazioni riservate nell'assegnazione del nome.
>

## <a name="deploy-the-template"></a>Distribuire il modello

Usare questi comandi dell'interfaccia della riga di comando di Azure per distribuire i modelli e verificare la distribuzione.

1. Per distribuire un modello, passare alla cartella contenente i file dei modelli e dei parametri ed eseguire il [comando seguente per avviare una distribuzione](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Il completamento di questa operazione potrebbe richiedere alcuni minuti. Al termine, cercare la proprietà **provisioningState** che mostra "Succeeded" nell'output. 

   ![Output del provisioning](./media/quick-setup-auto-provision-rm/output.png) 


2. Per verificare la distribuzione, eseguire questo [comando per elencare le risorse](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) e cercare il servizio di provisioning e l'hub IoT creati nell'output:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide di avvio rapido di questa raccolta si basano sulla presente guida di avvio rapido. Se si prevede di continuare a usare le guide di avvio rapido successive o le esercitazioni, non pulire le risorse create in questa guida di avvio rapido. Se non si prevede di continuare, è possibile usare l'interfaccia della riga di comando di Azure per [eliminare una singola risorsa][lnk-az-resource-command], ad esempio un hub IoT o un servizio di provisioning, oppure eliminare un gruppo di risorse e tutte le relative risorse.

Per eliminare un servizio di provisioning, eseguire questo comando:

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
Per eliminare un hub IoT, eseguire questo comando:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Per eliminare un gruppo di risorse e tutte le risorse, eseguire questo comando:

```azurecli
az group delete --name {your resource group name}
```

È anche possibile eliminare gruppi di risorse e singole risorse usando il portale di Azure, PowerShell o le API REST nonché gli SDK di piattaforma supportati pubblicati per Azure Resource Manager o il servizio Device Provisioning in hub IoT.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati distribuiti un hub IoT e un'istanza del servizio Device Provisioning e le due risorse sono state collegate. Per informazioni su come usare questa configurazione per eseguire il provisioning di un dispositivo simulato, proseguire con l'argomento di avvio rapido per la creazione di un dispositivo simulato.

> [!div class="nextstepaction"]
> [Avvio rapido per la creazione di un dispositivo simulato](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
