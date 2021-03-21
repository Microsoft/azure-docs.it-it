---
title: Usare un volume basato su File di Azure in un'app Service Fabric mesh
description: Informazioni sull'archiviazione dello stato in un'applicazione di Azure Service Fabric Mesh attraverso il montaggio di un volume basato su File di Azure all'interno di un servizio che usa l'interfaccia della riga di comando di Azure.
author: georgewallace
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: gwallace
ms.custom: mvc, devcenter , devx-track-azurecli
ms.openlocfilehash: 40d10568e13ad455bc5178821da80e89f4132e93
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625838"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Montare un volume basato su File di Azure in un'applicazione di Service Fabric Mesh 

> [!IMPORTANT]
> L'anteprima di Azure Service Fabric mesh è stata ritirata. Le nuove distribuzioni non saranno più consentite tramite l'API Service Fabric mesh. Il supporto per le distribuzioni esistenti continuerà fino al 28 aprile 2021.
> 
> Per informazioni dettagliate, vedere il [ritiro anteprima di Azure Service Fabric mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Questo articolo spiega come montare un volume basato su File di Azure in un servizio di un'applicazione di Service Fabric Mesh.  Il driver di volume di File di Azure è un driver di volume Docker usato per montare una condivisione di File di Azure in un contenitore, che viene usato per rendere persistente lo stato del servizio. I volumi offrono una risorsa di archiviazione file per utilizzo generico e consentono di leggere/scrivere i file usando le normali API di file I/O del disco.  Per altre informazioni sui volumi e sulle opzioni per l'archiviazione dei dati dell'applicazione, vedere l'[archiviazione dello stato](service-fabric-mesh-storing-state.md).

Per montare un volume in un servizio, creare una risorsa di volume nell'applicazione di Service Fabric Mesh e quindi fare riferimento a quel volume nel servizio.  La dichiarazione della risorsa di volume e il riferimento alla stessa nella risorsa di servizio si possono effettuare nei [file di risorsa basati su YAML](#declare-a-volume-resource-and-update-the-service-resource-yaml) o nel [modello di distribuzione basato su JSON](#declare-a-volume-resource-and-update-the-service-resource-json). Prima di montare il volume, creare un account di archiviazione di Azure e una [condivisione file in File di Azure](../storage/files/storage-how-to-create-file-share.md).

## <a name="prerequisites"></a>Prerequisiti
> [!NOTE]
> **Problemi noti relativi alla distribuzione nel computer di sviluppo RS5 di Windows:** È presente un bug aperto con il cmdlet di PowerShell New-SmbGlobalMapping nei computer RS5 Windows che impedisce il montaggio di volumi Azurefile. Di seguito è riportato un errore di esempio che si verifica quando viene montato il volume basato su AzureFile nel computer di sviluppo locale.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
La soluzione alternativa per il problema è 1) eseguire il comando seguente come amministratore di PowerShell e 2) riavviare il computer.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Per completare questo articolo è possibile usare Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. 

Per usare l'interfaccia della riga di comando di Azure con questo articolo, verificare che `az --version` restituisca almeno `azure-cli (2.0.43)`.  Per installare (o aggiornare) il modulo dell'estensione dell'interfaccia della riga di comando di Azure Service Fabric Mesh, seguire queste [istruzioni](service-fabric-mesh-howto-setup-cli.md).

Per accedere ad Azure e impostare la sottoscrizione:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Creare un account di archiviazione e una condivisione file (facoltativo)
Il montaggio di un volume di File di Azure richiede un account di archiviazione e una condivisione file.  È possibile usare un account di archiviazione e una condivisione file di Azure esistenti o creare risorse:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Ottenere il nome e la chiave dell'account di archiviazione e il nome della condivisione file
Il nome dell'account di archiviazione, la chiave dell'account di archiviazione e il nome della condivisione file vengono indicati come `<storageAccountName>`, `<storageAccountKey>` e `<fileShareName>` nelle sezioni seguenti. 

Indicare gli account di archiviazione e ottenere il nome dell'account di archiviazione con la condivisione file da usare:
```azurecli-interactive
az storage account list
```

Ottenere il nome della condivisione file:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

Ottenere la chiave dell'account di archiviazione ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

È anche possibile trovare questi valori nel [portale di Azure](https://portal.azure.com):
* `<storageAccountName>` - In **Account di archiviazione** il nome dell'account di archiviazione usato per creare la condivisione file.
* `<storageAccountKey>` - Selezionare il proprio account di archiviazione in **Account di archiviazione**, quindi selezionare **Chiavi di accesso** e usare il valore in **key1**.
* `<fileShareName>` - Selezionare il proprio account di archiviazione in **Account di archiviazione** e quindi selezionare **File**. Il nome da usare è il nome della condivisione file creata.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Dichiarare una risorsa di volume e aggiornare la risorsa di servizio (JSON)

Aggiungere i parametri per i valori `<fileShareName>`, `<storageAccountName>` e `<storageAccountKey>` individuati in un passaggio precedente. 

Creare una risorsa Volume come peer della risorsa Applicazione. Specificare un nome e il provider ("SFAzureFile" per utilizzare il volume basato su File di Azure). In `azureFileParameters` specificare i parametri per i valori `<fileShareName>`, `<storageAccountName>` e `<storageAccountKey>` individuati in un passaggio precedente.

Per montare il volume nel servizio, aggiungere un `volumeRefs` all'elemento `codePackages` del servizio.  `name` è l'ID risorsa per il volume (o un parametro del modello di distribuzione per la risorsa di volume) e il nome del volume dichiarato nel file di risorse volume.yaml.  `destinationPath` è la directory locale in cui verrà montato il volume.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Dichiarare una risorsa di volume e aggiornare la risorsa di servizio (YAML)

Aggiungere un nuovo file *volume.yaml* nella directory delle *risorse dell'app* per l'applicazione.  Specificare un nome e il provider ("SFAzureFile" per utilizzare il volume basato su File di Azure). `<fileShareName>`, `<storageAccountName>` e `<storageAccountKey>` sono i valori individuati in un passaggio precedente.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Aggiornare il file *service.yaml* nella directory delle *risorse di servizio* per montare il volume nel servizio.  Aggiungere l'elemento `volumeRefs` all'elemento `codePackages`.  `name` è l'ID risorsa per il volume (o un parametro del modello di distribuzione per la risorsa di volume) e il nome del volume dichiarato nel file di risorse volume.yaml.  `destinationPath` è la directory locale in cui verrà montato il volume.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere l'applicazione di esempio sul volume di File di Azure in [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Per altre informazioni sul modello di risorse di Service Fabric, vedere [Service Fabric Mesh Resource Model](service-fabric-mesh-service-fabric-resources.md) (Modello di risorsa mesh Service Fabric).
- Per altre informazioni su mesh Service Fabric, leggere la [panoramica di mesh Service Fabric](service-fabric-mesh-overview.md).
