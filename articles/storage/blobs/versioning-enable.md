---
title: Abilitare e gestire il controllo delle versioni dei BLOB
titleSuffix: Azure Storage
description: Informazioni su come abilitare il controllo delle versioni dei BLOB nel portale di Azure o usando un modello di Azure Resource Manager.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5b6bd16eacf4b1bbb7b93f5500813e7fa9dc7eef
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095843"
---
# <a name="enable-and-manage-blob-versioning"></a>Abilitare e gestire il controllo delle versioni dei BLOB

È possibile abilitare il controllo delle versioni dell'archiviazione BLOB per mantenere automaticamente le versioni precedenti di un BLOB quando viene modificato o eliminato. Quando è abilitata la funzionalità di controllo delle versioni dei BLOB, è possibile ripristinare una versione precedente di un BLOB per ripristinare i dati se vengono erroneamente modificati o eliminati.

Questo articolo illustra come abilitare o disabilitare il controllo delle versioni dei BLOB per l'account di archiviazione usando il portale di Azure o un modello di Azure Resource Manager. Per altre informazioni sul controllo delle versioni dei BLOB, vedere [controllo delle versioni dei BLOB](versioning-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Abilitare il controllo delle versioni dei BLOB

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Per abilitare il controllo delle versioni dei BLOB per un account di archiviazione nel portale di Azure:

1. Passare all'account di archiviazione nel portale.
1. In **servizio BLOB** scegliere **protezione dati**.
1. Nella sezione **controllo delle versioni** selezionare **abilitato**.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Screenshot che illustra come abilitare il controllo delle versioni dei BLOB in portale di Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per abilitare il controllo delle versioni dei BLOB per un account di archiviazione con PowerShell, installare prima il modulo [AZ. storage](https://www.powershellgallery.com/packages/Az.Storage) versione 2.3.0 o successiva. Chiamare quindi il comando [Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) per abilitare il controllo delle versioni, come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori tra parentesi angolari con valori personalizzati:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per abilitare il controllo delle versioni BLOB per un account di archiviazione con l'interfaccia della riga di comando di Azure, installare prima l'interfaccia della riga di comando di Azure 2.2.0 Chiamare quindi il comando [AZ storage account Blob-Service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) per abilitare il controllo delle versioni, come illustrato nell'esempio seguente. Ricordarsi di sostituire i valori tra parentesi angolari con valori personalizzati:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Modello](#tab/template)

Per abilitare il controllo delle versioni dei BLOB con un modello, creare un modello con la proprietà **IsVersioningEnabled** su **true**. Nei passaggi seguenti viene descritto come creare un modello nel portale di Azure.

1. Nella portale di Azure scegliere **Crea una risorsa**.
1. In **Cerca nel Marketplace** Digitare **distribuzione modello**, quindi premere **invio**.
1. Scegliere **distribuzione modelli**, fare clic su **Crea** e quindi scegliere **Compila modello personalizzato nell'editor**.
1. Nell'editor dei modelli incollare il codice JSON seguente. Sostituire il segnaposto `<accountName>` con il nome del proprio account di archiviazione.
1. Salvare il modello.
1. Specificare il gruppo di risorse dell'account, quindi scegliere il pulsante **Acquista** per distribuire il modello e abilitare il controllo delle versioni dei BLOB.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Per altre informazioni sulla distribuzione di risorse con i modelli nel portale di Azure, vedere [distribuire le risorse con portale di Azure](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Modificare un BLOB per attivare una nuova versione

L'esempio di codice seguente illustra come attivare la creazione di una nuova versione con la libreria client di archiviazione di Azure per .NET, versione [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) o successiva. Prima di eseguire questo esempio, verificare di aver abilitato il controllo delle versioni per l'account di archiviazione.

Nell'esempio viene creato un BLOB in blocchi, quindi vengono aggiornati i metadati del BLOB. Se si aggiornano i metadati del BLOB, viene attivata la creazione di una nuova versione. Nell'esempio vengono recuperate la versione iniziale e la versione corrente e viene indicato che solo la versione corrente include i metadati.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Elencare le versioni BLOB

Per elencare le versioni o gli snapshot dei BLOB con la libreria client di .NET V12, specificare il parametro [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) con il campo **Version** .

L'esempio di codice seguente mostra come elencare la versione dei BLOB con la libreria client di archiviazione di Azure per .NET, versione [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) o successiva. Prima di eseguire questo esempio, verificare di aver abilitato il controllo delle versioni per l'account di archiviazione.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Passaggi successivi

- [Controllo delle versioni dei BLOB](versioning-overview.md)
- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](./soft-delete-blob-overview.md)