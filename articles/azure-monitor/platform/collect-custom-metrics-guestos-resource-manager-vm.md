---
title: Raccogliere le metriche della macchina virtuale Windows in Monitoraggio di Azure con il modello
description: Inviare le metriche del sistema operativo guest all'archivio di database delle metriche di Monitoraggio di Azure usando un modello di Resource Manager per una macchina virtuale Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: bwren
ms.subservice: metrics
ms.openlocfilehash: 14079f42fd857495396a0c44fd3bdeaf4371ea5f
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650553"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine"></a>Inviare le metriche del sistema operativo guest all'archivio delle metriche di Monitoraggio di Azure usando un modello di Azure Resource Manager per una macchina virtuale Windows
I dati sulle prestazioni del sistema operativo guest delle macchine virtuali di Azure non vengono raccolti automaticamente come altre [metriche della piattaforma](../insights/monitor-azure-resource.md#monitoring-data). Installare l'[estensione di diagnostica](diagnostics-extension-overview.md) di Monitoraggio di Azure per raccogliere le metriche del sistema operativo guest nel database di metriche, in modo che possa essere usato con tutte le funzionalità delle metriche di Monitoraggio di Azure, inclusi gli avvisi quasi in tempo reale, la creazione di grafici, il routing e l'accesso da un'API REST. Questo articolo illustra il processo da eseguire per inviare le metriche delle prestazioni del sistema operativo guest per una macchina virtuale Windows al database di metriche usando un modello di Resource Manager. 

> [!NOTE]
> Per informazioni dettagliate sulla configurazione dell'estensione di diagnostica per raccogliere le metriche del sistema operativo guest usando il portale di Azure, vedere [Installare e configurare l'estensione di Diagnostica di Microsoft Azure (WAD)](diagnostics-extension-windows-install.md).


Se non si ha familiarità con i modelli di Resource Manager, vedere le [distribuzioni dei modelli](../../azure-resource-manager/management/overview.md) e la struttura e la sintassi correlate.

## <a name="prerequisites"></a>Prerequisiti

- La sottoscrizione deve essere registrata con [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- È necessario avere installato [Azure PowerShell](/powershell/azure) o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

- La risorsa della macchina virtuale deve trovarsi in un'[area che supporta le metriche personalizzate](metrics-custom-overview.md#supported-regions). 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurare Monitoraggio di Azure come sink dei dati
L'estensione di Diagnostica di Azure usa una funzionalità denominata "sink di dati" per effettuare il routing delle metriche e dei log in percorsi diversi. Le procedure seguenti illustrano come usare un modello di Resource Manager e PowerShell per distribuire una VM usando il nuovo sink di dati "Monitoraggio di Azure".

## <a name="author-resource-manager-template"></a>Creare un modello di Resource Manager
Per questo esempio è possibile usare un modello di esempio disponibile pubblicamente. I modelli iniziali sono disponibili all'indirizzo https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy.json** è un modello di Resource Manager preconfigurato per la distribuzione di una macchina virtuale.

- **Azuredeploy.parameters.json** è un file di parametri in cui sono archiviate alcune informazioni, ad esempio il nome utente e la password che si vogliono impostare per la VM. Durante la distribuzione, il modello di Resource Manager usa i parametri impostati in questo file.

Scaricare e salvare entrambi i file in locale.

### <a name="modify-azuredeployparametersjson"></a>Modificare azuredeploy.parameters.json
Aprire il file *azuredeploy.parameters.json*

1. Immettere i valori relativi a **adminUsername** e **adminPassword** per la macchina virtuale. Questi parametri vengono usati per l'accesso remoto alla macchina virtuale. Per evitare che la VM sia sottoposta a hijack, NON usare i valori specificati in questo modello. I bot eseguono la ricerca in Internet dei nomi utente e delle password archiviati nei repository GitHub pubblici. È probabile che stiano testando le macchine virtuali con queste impostazioni predefinite.

1. Creare un nome DNS univoco per la VM.

### <a name="modify-azuredeployjson"></a>Modificare azuredeploy.json

Aprire il file *azuredeploy.json*

Aggiungere l'ID di un account di archiviazione alla sezione **variables** del modello dopo l'immissione di un valore per **storageAccountName**.

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Aggiungere questa estensione identità del servizio gestita al modello nella parte superiore della sezione **resources**. L'estensione assicura l'accettazione da parte di Monitoraggio di Azure delle metriche inviate.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Aggiungere la configurazione di **identity** alla risorsa VM per garantire che Azure assegni all'estensione identità del servizio gestita un'identità di sistema. Questo passaggio garantisce che la VM possa inviare metriche guest su se stessa a Monitoraggio di Azure.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Aggiungere la configurazione seguente per abilitare l'estensione Diagnostica in una macchina virtuale Windows. Per una macchina virtuale semplice basata su Resource Manager è possibile aggiungere la configurazione dell'estensione alla matrice di risorse per la macchina virtuale. La riga "sinks"&mdash; "AzMonSink" e il corrispondente "SinksConfig" più avanti nella sezione&mdash; consentono all'estensione di inviare le metriche direttamente a Monitoraggio di Azure. È possibile aggiungere o rimuovere i contatori delle prestazioni in base alle esigenze.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Salvare e chiudere entrambi i file.


## <a name="deploy-the-resource-manager-template"></a>Distribuire il modello di Resource Manager

> [!NOTE]
> È necessario eseguire la versione 1.5 o successiva dell'estensione Diagnostica di Azure E impostare la proprietà **autoUpgradeMinorVersion** su true nel modello di Resource Manager. Azure carica quindi l'estensione corretta all'avvio della macchina virtuale. Se nel modello sono specificate impostazioni diverse, modificarle e ridistribuire il modello.


Per la distribuzione del modello di Resource Manager verrà usato Azure PowerShell.

1. Avviare PowerShell.
1. Accedere ad Azure usando `Login-AzAccount`.
1. Ottenere l'elenco delle sottoscrizioni usando `Get-AzSubscription`.
1. Impostare la sottoscrizione che si usa per creare o aggiornare la macchina virtuale nella sezione seguente:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Per creare un nuovo gruppo di risorse per la VM che viene distribuita, eseguire il comando seguente:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Ricordarsi di [usare un'area di Azure abilitata per le metriche personalizzate](metrics-custom-overview.md).

1. Eseguire i comandi seguenti per distribuire la VM usando il modello di Resource Manager.
   > [!NOTE]
   > Se si desidera aggiornare una VM esistente, aggiungere semplicemente *-Mode Incremental* alla fine del comando seguente.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Al termine della distribuzione, la VM deve essere disponibile nel portale di Azure per inviare metriche a Monitoraggio di Azure.

   > [!NOTE]
   > È possibile che si verifichino errori in relazione al valore selezionato per vmSkuSize. In questo caso, tornare al file azuredeploy.json e aggiornare il valore predefinito del parametro vmSkuSize. In questo caso è consigliabile provare "Standard_DS1_v2".

## <a name="chart-your-metrics"></a>Tracciare il grafico delle metriche

1. Accedere al portale di Azure.

2. Nel menu a sinistra selezionare **Monitoraggio**.

3. Nella pagina Monitoraggio selezionare **Metrica**.

   ![Pagina delle metriche](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Modificare il periodo di aggregazione in **Ultimi 30 minuti**.

5. Nell'elenco a discesa delle risorse selezionare la VM creata. Se non si è modificato il nome nel modello, deve essere *SimpleWinVM2*.

6. Nell'elenco a discesa degli spazi dei nomi selezionare **azure.vm.windows.guest**.

7. Nell'elenco a discesa delle metriche selezionare **Memoria\%di byte vincolati in uso**.


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [metriche personalizzate](metrics-custom-overview.md).

