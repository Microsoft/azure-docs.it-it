---
title: Esplora le tue risorse di Azure
description: Informazioni su come usare il linguaggio di query di Resource Graph per esplorare le risorse e comprendere come sono connesse.
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: 33bf457a57f7e62b9c99471bcb7676f62046f61d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654496"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Esplorare le risorse di Azure con Resource Graph

Resource Graph di Azure offre la possibilità di esplorare e individuare rapidamente e su larga scala le risorse di Azure. Progettato per risposte rapide, è un ottimo modo per ottenere informazioni sull'ambiente e sulle proprietà che costituiscono le risorse di Azure.

## <a name="explore-virtual-machines"></a>Esplorazione delle macchine virtuali

Una risorsa comune in Azure è una macchina virtuale. Come tipo di risorsa, le macchine virtuali hanno molte proprietà su cui è possibile eseguire query. Ogni proprietà fornisce un'opzione per filtrare o individuare esattamente la risorsa che si sta cercando.

### <a name="virtual-machine-discovery"></a>Dettagli delle macchine virtuali

Iniziare con una semplice query per ottenere una singola macchina virtuale dall'ambiente ed esaminare le proprietà restituite.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Il cmdlet `Search-AzGraph` di Azure PowerShell restituisce un **PSCustomObject** per impostazione predefinita. Affinché l'output abbia lo stesso aspetto del valore restituito dall'interfaccia della riga di comando di Azure, viene usato il cmdlet `ConvertTo-Json`. Il valore predefinito per **Profondità** è _2_. Impostandolo su _100_ deve convertire tutti i livelli restituiti.

I risultati JSON sono strutturati in modo simile all'esempio seguente:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

Le proprietà forniscono informazioni aggiuntive relative alla risorsa di macchina virtuale stessa, tutte le informazioni relative allo SKU, al sistema operativo, ai dischi, ai tag, e al gruppo di risorse e alla sottoscrizione di cui è membro.

### <a name="virtual-machines-by-location"></a>Macchine virtuali per posizione

Con le informazioni acquisite sulle risorse delle macchine virtuali, è possibile usare la proprietà **posizione** per il conteggio di tutte le macchine virtuali per posizione. Per aggiornare la query, verrà rimosso il limite e verrà riepilogato il conteggio dei valori di posizione.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

I risultati JSON sono strutturati in modo simile all'esempio seguente:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

È possibile visualizzare il numero di macchine virtuali disponibili in ogni area di Azure.

### <a name="virtual-machines-by-sku"></a>Macchine virtuali per SKU

Tornando alle proprietà originali delle macchine virtuali, è possibile tentare di trovare tutte le macchine virtuali che hanno una dimensione SKU di **Standard_B2s**. Esaminando il codice JSON restituito, si noterà che è archiviato in **properties.hardwareprofile.vmsize**. Verrà aggiornata la query per trovare tutte le macchine virtuali che corrispondono a queste dimensioni e verrà restituito solo il nome della macchina virtuale e l'area.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup
```

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Macchine virtuali connesse a Dischi gestiti Premium

Se si desidera ottenere i dettagli dei Dischi gestiti Premium associati a queste macchine virtuali **Standard_B2s**, è possibile espandere la query per ottenere l'ID risorsa dei dischi gestiti.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Un altro modo per ottenere lo SKU può essere quello di usare la proprietà **alias** **Microsoft.Compute/virtualMachines/sku.name**. Vedere gli esempi [Mostra alias](../samples/starter.md#show-aliases) e [Mostra valori alias distinti](../samples/starter.md#distinct-alias-values).

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Il risultato è un elenco di ID di dischi.

### <a name="managed-disk-discovery"></a>Individuazione di un disco gestito

Con il primo record dalla query precedente, verranno esaminate le proprietà che esistono sul disco gestito che è stato collegato alla prima macchina virtuale. La query aggiornata usa l'ID del disco e cambia il tipo.

Output di esempio dalla query precedente, ad esempio:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
Resources
| where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Prima di eseguire la query, come facciamo a sapere di che **tipo** dovrebbe ora essere **Microsoft.Compute/disks**?
Se si esamina l'ID completo, si noterà **/providers/Microsoft.Compute/disks/** come parte della stringa. Questo frammento di stringa fornisce un hint per il tipo da cercare. Un metodo alternativo potrebbe essere quello di rimuovere il limite per tipo ed effettuare invece la ricerca solo per il campo ID. Poiché l'ID è univoco, verrebbe restituito un solo record e la proprietà **tipo** fornisce i dettagli.

> [!NOTE]
> Affinché questo esempio funzioni, è necessario sostituire il campo ID con un risultato dal proprio ambiente.

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

I risultati JSON sono strutturati in modo simile all'esempio seguente:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Esplorare le macchine virtuali per trovare gli indirizzi IP pubblici

Questo set di query prima di tutto trova e archivia tutte le risorse delle interfacce di rete (NIC) connesse alle macchine virtuali. Quindi le query usano l'elenco delle interfacce di rete per trovare ogni risorsa di indirizzo IP che è un indirizzo IP pubblico e archiviano tali valori. Infine, le query forniscono un elenco degli indirizzi IP pubblici.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nics.txt' file
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

```azurepowershell-interactive
# Use Resource Graph to get all NICs and store in the $nics variable
$nics = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20"

# Review the output of the query stored in the variable
$nics.nic
```

Usare il file (interfaccia della riga di comando di Azure) o la variabile (Microsoft Azure PowerShell) nella query successiva per ottenere i dettagli delle risorse di interfaccia di rete correlate in cui è presente un indirizzo IP pubblico collegato alla scheda di interfaccia di rete.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

```azurepowershell-interactive
# Use Resource Graph  with the $nics variable to get all related public IP addresses and store in $ips variable
$ips = Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$($nics.nic -join "','")') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp"

# Review the output of the query stored in the variable
$ips.publicIp
```

Infine usare l'elenco delle risorse di indirizzi IP pubblici archiviato nel file (interfaccia della riga di comando di Azure) o la variabile (Microsoft Azure PowerShell) per ottenere l'indirizzo IP pubblico effettivo e visualizzarlo.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

```azurepowershell-interactive
# Use Resource Graph with the $ips variable to get the IP address of the public IP address resources
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$($ips.publicIp -join "','")') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip"
```

Per informazioni su come eseguire questi passaggi in una singola query con l'operatore `join`, vedere l'esempio [elenco di macchine virtuali con l'interfaccia di rete e l'indirizzo IP pubblico](../samples/advanced.md#join-vmpip).

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [linguaggio di query](query-language.md).
- Vedere il linguaggio in uso in [Query di base](../samples/starter.md).
- Vedere gli usi avanzati in [Query avanzate](../samples/advanced.md).
