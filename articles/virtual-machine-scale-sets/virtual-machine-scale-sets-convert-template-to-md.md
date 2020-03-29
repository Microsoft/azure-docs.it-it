---
title: Convertire un modello di set di scalabilità per l'uso di dischi gestiti
description: Convertire un modello di set di scalabilità della macchina virtuale di Azure Resource Manager in un modello di set di scalabilità del disco gestito.
keywords: set di scalabilità di macchine virtuali
author: mayanknayar
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: 4ab5c48c6673a2353c70fe808d09aa15675e0424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278122"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Convertire un modello di set di scalabilità in un modello di set di scalabilità per i dischi gestiti

I clienti con un modello di Resource Manager per la creazione di un set di scalabilità che non usa i dischi gestiti potrebbero volerlo modificare per usare i dischi gestiti. Questo articolo illustra come usare i dischi gestiti, usando come esempio una richiesta pull dai [modelli di avvio rapido di Azure](https://github.com/Azure/azure-quickstart-templates), un repository gestito dalla community di modelli di Resource Manager di esempio. La richiesta pull completa può [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998)essere visto qui: , e le parti rilevanti del diff sono qui sotto, insieme a spiegazioni:

## <a name="making-the-os-disks-managed"></a>Impostazione dei dischi del sistema operativo come gestiti

Nel Diff seguente sono state rimosse diverse variabili correlate all'account di archiviazione e alle proprietà del disco. Il tipo di account di archiviazione non è più necessario, il tipo predefinito è Standard_LRS, ma è ugualmente possibile specificarlo, se lo si desidera. Con il disco gestito sono supportati solo Standard_LRS e Premium_LRS. Nel modello precedente, per generare i nomi degli account di archiviazione, vengono usati un nuovo suffisso dell'account di archiviazione, una matrice di stringhe univoca e il conteggio degli account di archiviazione. Queste variabili non sono più necessarie nel nuovo modello perché il disco gestito crea automaticamente gli account di archiviazione per conto del cliente. Allo stesso modo, non sono più necessari il nome del contenitore del disco rigido virtuale e il nome del disco del sistema operativo perché il disco gestito denomina automaticamente i dischi e i contenitori BLOB di archiviazione sottostanti.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


Nel Diff seguente la versione dell'API di calcolo è stata aggiornata alla versione 2016-04-30-preview, ovvero la versione necessaria meno recente per il supporto del disco gestito con i set di scalabilità. Se lo si desidera, è possibile usare dischi non gestiti nella nuova versione dell'API con la sintassi precedente. Se si aggiorna solo la versione dell'API di calcolo e non si modifica nient'altro, il modello dovrebbe continuare a funzionare come prima.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

Nel Diff seguente la risorsa dell'account di archiviazione viene rimossa completamente dalla matrice delle risorse. Le risorse non sono più necessarie poiché il disco gestito le crea automaticamente.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

Nel Diff seguente è possibile osservare che viene rimossa la clausola depends on che crea un riferimento dal set di scalabilità al ciclo che crea gli account di archiviazione. Nel modello precedente ciò assicura che gli account di archiviazione vengano creati prima che il set di scalabilità inizi la creazione, ma questa clausola non è più necessaria con i dischi gestiti. Vengono rimosse anche la proprietà dei contenitori dei dischi rigidi virtuali e la proprietà del nome del disco del sistema operativo perché queste proprietà vengono gestite automaticamente in background dal disco gestito. È possibile aggiungere `"managedDisk": { "storageAccountType": "Premium_LRS" }` nella configurazione "osDisk" se si preferiscono dischi di sistema operativo Premium. Solo le VM con una "s" maiuscola o minuscola nell'unità SKU possono usare dischi Premium.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Non esistono proprietà esplicite nella configurazione del set di scalabilità che indichino se usare dischi gestiti o non gestiti. Il set di scalabilità determina quale usare in base alle proprietà presenti nel profilo di archiviazione. È quindi importante, quando si modifica il modello, assicurarsi che nel profilo di archiviazione del set di scalabilità siano presenti le proprietà corrette.


## <a name="data-disks"></a>Dischi dati

Con le modifiche precedenti, il set di scalabilità usa dischi gestiti per il disco del sistema operativo. Non si è però parlato dei dischi dati. Per aggiungere dischi dati, aggiungere la proprietà "dataDisks" in "storageProfile" allo stesso livello di "osDisk". Il valore della proprietà è un elenco JSON di oggetti, ognuno dei quali ha proprietà "lun" (che devono essere univoche per ogni disco dati in una VM), "createOption" ("empty" è attualmente la sola opzione supportata) e "diskSizeGB" (le dimensioni del disco in gigabyte, che devono essere maggiori di 0 e minori di 1024), come nell'esempio seguente:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Se si specificano `n` dischi in questa matrice, ogni VM nel set di scalabilità ottiene `n` dischi dati. Si noti tuttavia che questi dischi dati sono dispositivi RAW. Non sono formattati. È responsabilità del cliente collegare, creare le partizioni e formattare i dischi prima di usarli. Facoltativamente è possibile anche specificare `"managedDisk": { "storageAccountType": "Premium_LRS" }` in ogni oggetto del disco dati per precisare che deve essere un disco dati Premium. Solo le VM con una "s" maiuscola o minuscola nell'unità SKU possono usare dischi Premium.

Per altre informazioni sull'uso dei dischi dati con i set di scalabilità, vedere [questo articolo](./virtual-machine-scale-sets-attached-disks.md).


## <a name="next-steps"></a>Passaggi successivi
Ad esempio, i modelli di Resource Manager che usano i set di scalabilità, cercare "vmss" nel [repository GitHub](https://github.com/Azure/azure-quickstart-templates)dei modelli di guida rapida di Azure.

Per informazioni generali, vedere la [pagina di destinazione principale per i set di scalabilità](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

