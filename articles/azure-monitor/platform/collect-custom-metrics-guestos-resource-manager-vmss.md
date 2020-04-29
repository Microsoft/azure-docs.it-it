---
title: Raccogliere le metriche del set di scalabilità Windows in monitoraggio di Azure con il modello
description: Inviare le metriche del sistema operativo guest all'archivio delle metriche di Monitoraggio di Azure usando un modello di Resource Manager per un set di scalabilità di macchine virtuali Windows
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 24f83e4f6285d045e67bdaef431ebcff2345ef84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77663897"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Inviare le metriche del sistema operativo guest all'archivio delle metriche di Monitoraggio di Azure usando un modello di Azure Resource Manager per un set di scalabilità di macchine virtuali Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

L'[estensione Diagnostica di Microsoft Azure (WAD)](diagnostics-extension-overview.md) di Monitoraggio di Azure consente di raccogliere le metriche e i log dal sistema operativo guest eseguito come parte di una macchina virtuale, di un servizio cloud o di un cluster di Azure Service Fabric. L'estensione può inviare i dati di telemetria a molti percorsi diversi elencati nell'articolo indicato in precedenza.  

In questo articolo viene descritto il processo per inviare le metriche delle prestazioni del sistema operativo guest per un set di scalabilità di macchine virtuali Windows all'archivio dati di Monitoraggio di Azure. A partire dalla versione 1.11 di Diagnostica di Microsoft Azure è possibile scrivere le metriche direttamente nell'archivio delle metriche di Monitoraggio di Azure in cui sono già state raccolte le metriche standard della piattaforma. L'archiviazione in questa posizione consente di accedere alle stesse azioni disponibili per le metriche della piattaforma. Le azioni includono la creazione di avvisi in tempo quasi reale, la creazione di grafici, il routing, l'accesso dall'API REST e altro ancora. Le versioni precedenti dell'estensione Diagnostica di Microsoft Azure eseguono operazioni di scrittura in Archiviazione di Azure, ma non nell'archivio dati di Monitoraggio di Azure.  

Se non si ha familiarità con Gestione risorse modelli, vedere le informazioni sulle [distribuzioni](../../azure-resource-manager/management/overview.md) dei modelli e la relativa struttura e sintassi.  

## <a name="prerequisites"></a>Prerequisiti

- La sottoscrizione deve essere registrata con [Microsoft. Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- È necessario avere installato [Azure PowerShell](/powershell/azure) oppure è possibile usare [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). 

- La risorsa VM deve trovarsi in un' [area che supporta le metriche personalizzate](metrics-custom-overview.md#supported-regions).

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurare Monitoraggio di Azure come sink dei dati 
L'estensione Diagnostica di Azure usa una funzionalità denominata **sink di dati** per indirizzare le metriche e i log in posizioni diverse. Le procedure seguenti illustrano come usare un modello di Resource Manager e PowerShell per distribuire una VM usando il nuovo sink di dati Monitoraggio di Azure. 

## <a name="author-a-resource-manager-template"></a>Creare un modello di Resource Manager 
Per questo esempio, è possibile usare un modello di [esempio](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale)disponibile pubblicamente:  

- **File azuredeploy. JSON** è un modello di gestione risorse preconfigurato per la distribuzione di un set di scalabilità di macchine virtuali.

- **Azuredeploy.parameters.json** è un file di parametri in cui sono archiviate alcune informazioni, ad esempio il nome utente e la password che si vogliono impostare per la macchina virtuale. Durante la distribuzione, il modello di Resource Manager usa i parametri impostati in questo file. 

Scaricare e salvare entrambi i file in locale. 

###  <a name="modify-azuredeployparametersjson"></a>Modificare azuredeploy.parameters.json
Aprire il file **file azuredeploy. Parameters. JSON** :  
 
- Specificare un valore per **vmSKU** da distribuire. È consigliabile usare Standard_D2_v3. 
- Specificare un valore desiderato per **windowsOSVersion** per il set di scalabilità di macchine virtuali. È consigliabile usare 2016-Datacenter. 
- Assegnare un nome alla risorsa del set di scalabilità di macchine virtuali da distribuire usando una proprietà **vmssName**. Un esempio può essere **VMSS-WAD-TEST**.    
- Specificare il numero di VM da eseguire nel set di scalabilità di macchine virtuali usando la proprietà **instanceCount**.
- Immettere i valori relativi a **adminUsername** e **adminPassword** per il set di scalabilità di macchine virtuali. Questi parametri vengono usati per l'accesso remoto alle macchine virtuali nel set di scalabilità. Per evitare che la VM sia sottoposta a hijack, **non** usare i valori specificati in questo modello. I bot eseguono la ricerca in Internet dei nomi utente e delle password archiviati nei repository GitHub pubblici. È probabile che le VM vengano testate con queste impostazioni predefinite. 


###  <a name="modify-azuredeployjson"></a>Modificare azuredeploy.json
Aprire il file **file azuredeploy. JSON** . 

Aggiungere una variabile per conservare le informazioni sull'account di archiviazione nel modello di Resource Manager. Tutti i log o i contatori delle prestazioni specificati nel file di configurazione di diagnostica verranno scritti sia nell'archivio delle metriche di Monitoraggio di Azure sia nell'account di archiviazione specificato di seguito: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Individuare la definizione del set di scalabilità di macchine virtuali nella sezione risorse e aggiungere la sezione **Identity** alla configurazione. In questo modo, Azure assegna alla configurazione un'identità di sistema. Questo passaggio assicura anche che le VM nel set di scalabilità possano inviare metriche guest su se stesse a Monitoraggio di Azure:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

Nella risorsa del set di scalabilità di macchine virtuali trovare la sezione **virtualMachineProfile**. Aggiungere un nuovo profilo chiamato **extensionsProfile** per gestire le estensioni.  


In **extensionProfile** aggiungere una nuova estensione al modello come illustrato nella sezione **VMSS-WAD-extension**.  Questa sezione è l'estensione relativa alle identità gestite per le risorse di Azure che garantisce che le metriche generate vengano accettate da Monitoraggio di Azure. Il campo **nome** può contenere un nome qualsiasi. 

Il codice seguente dell'estensione MSI aggiunge anche l'estensione Diagnostica e la relativa configurazione come risorsa di estensione alla risorsa del set di scalabilità di macchine virtuali. È possibile aggiungere o rimuovere i contatori delle prestazioni in base alle esigenze: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Aggiungere un **dependsOn** per l'account di archiviazione per assicurarsi che venga creato nell'ordine corretto: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Se non è già stato creato nel modello, creare un account di archiviazione: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Salvare e chiudere entrambi i file. 

## <a name="deploy-the-resource-manager-template"></a>Distribuire il modello di Resource Manager 

> [!NOTE]  
> È necessario eseguire l'estensione Diagnostica di Azure 1,5 o versione successiva **e** impostare la proprietà **autoUpgradeMinorVersion:** su **true** nel modello di gestione risorse. Azure carica quindi l'estensione corretta all'avvio della macchina virtuale. Se nel modello sono specificate impostazioni diverse, modificarle e ridistribuire il modello. 


Per distribuire il modello di Resource Manager, usare Azure PowerShell:  

1. Avviare PowerShell. 
1. Accedere al portale di Azure con `Login-AzAccount`.
1. Ottenere l'elenco delle sottoscrizioni usando `Get-AzSubscription`.
1. Impostare la sottoscrizione che verrà creata o aggiornare la macchina virtuale: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Creare un nuovo gruppo di risorse per la VM da distribuire. Eseguire il comando seguente: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Ricordarsi di usare un'area di Azure abilitata per le metriche personalizzate. Ricordarsi di usare un'[area di Azure abilitata per le metriche personalizzate](https://github.com/MicrosoftDocs/azure-docs-pr/pull/metrics-custom-overview.md#supported-regions).
 
1. Eseguire i comandi seguenti per distribuire la VM:  

   > [!NOTE]  
   > Se si vuole aggiornare un set di scalabilità esistente, aggiungere **-Mode Incremental** alla fine del comando. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Al termine della distribuzione, il set di scalabilità di macchine virtuali dovrebbe essere disponibile nel portale di Azure e inviare metriche a Monitoraggio di Azure. 

   > [!NOTE]  
   > È possibile che si verifichino errori in relazione al valore selezionato per **vmSkuSize**. In questo caso, tornare al file **azuredeploy.json** e aggiornare il valore predefinito del parametro **vmSkuSize**. È consigliabile provare **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Tracciare il grafico delle metriche 

1. Accedere al portale di Azure. 

1. Nel menu a sinistra selezionare **Monitoraggio**. 

1. Nella pagina **Monitoraggio** selezionare **Metriche**. 

   ![Monitoraggio - pagina Metriche](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Modificare il periodo di aggregazione in **Ultimi 30 minuti**.  

1. Nell'elenco a discesa delle risorse selezionare il set di scalabilità di macchine virtuali creato.  

1. Nel menu a discesa spazi dei nomi selezionare **Azure. VM. Windows. Guest**. 

1. Nell'elenco a discesa delle metriche selezionare **Memoria\%di byte vincolati in uso**.  

È anche possibile scegliere di usare le dimensioni di questa metrica per disegnare tale metrica per una specifica VM o per tracciare ogni VM nel set di scalabilità. 



## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [metriche personalizzate](metrics-custom-overview.md).


