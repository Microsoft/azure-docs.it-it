---
title: Creare o eliminare un contenitore BLOB con .NET-archiviazione di Azure
description: Informazioni su come creare o eliminare un contenitore BLOB nell'account di archiviazione di Azure usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/04/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: dac50d8e35080a083e42891732512e012fae8fbd
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576448"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Creare o eliminare un contenitore in archiviazione di Azure con .NET

I BLOB in Archiviazione di Azure sono organizzati in contenitori. Prima di caricare un BLOB, è necessario innanzitutto creare un contenitore. Questo articolo illustra come creare ed eliminare contenitori con la [libreria client di archiviazione di Azure per .NET](/dotnet/api/overview/azure/storage).

## <a name="name-a-container"></a>Assegnare un nome a un contenitore

Un nome di contenitore deve essere un nome DNS valido perché fa parte dell'URI univoco usato per indirizzare il contenitore o i relativi BLOB. Quando si denomina un contenitore, seguire queste regole:

- I nomi dei contenitori possono avere una lunghezza compresa tra 3 e 63 caratteri.
- I nomi dei contenitori devono iniziare con una lettera o un numero e possono contenere solo lettere minuscole, numeri e il carattere trattino (-).
- Nei nomi dei contenitori non sono consentiti due o più caratteri Dash consecutivi.

Il formato dell'URI per un contenitore è il seguente:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Creare un contenitore

Per creare un contenitore, chiamare uno dei metodi seguenti:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [CreateBlobContainer](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainer)
- [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync)

Questi metodi generano un'eccezione se esiste già un contenitore con lo stesso nome.

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Creare](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

I metodi **create** e **CreateAsync** generano un'eccezione se esiste già un contenitore con lo stesso nome.

I metodi **CreateIfNotExists** e **CreateIfNotExistsAsync** restituiscono un valore booleano che indica se il contenitore è stato creato. Se esiste già un contenitore con lo stesso nome, questi metodi restituiscono **false** per indicare che non è stato creato un nuovo contenitore.

---

I contenitori vengono creati immediatamente sotto l'account di archiviazione. Non è possibile annidare un contenitore in un altro.

Nell'esempio seguente viene creato un contenitore in modo asincrono:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```
---

## <a name="create-the-root-container"></a>Creare il contenitore radice

Un contenitore radice funge da contenitore predefinito per l'account di archiviazione. Ogni account di archiviazione può avere un contenitore radice, che deve essere denominato *$root*. Il contenitore radice deve essere creato o eliminato in modo esplicito.

È possibile fare riferimento a un BLOB archiviato nel contenitore radice senza includere il nome del contenitore radice. Il contenitore radice consente di fare riferimento a un BLOB al livello superiore della gerarchia dell'account di archiviazione. Ad esempio, è possibile fare riferimento a un BLOB nel contenitore radice nel modo seguente:

`https://myaccount.blob.core.windows.net/default.html`

Nell'esempio seguente viene creato in modo sincrono il contenitore radice:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateRootContainer":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```
---

## <a name="delete-a-container"></a>Eliminare un contenitore

Per eliminare un contenitore in .NET, usare uno dei metodi seguenti:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [Elimina](/dotnet/api/azure.storage.blobs.blobcontainerclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexistsasync)

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Elimina](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)
---

I metodi **Delete** e **DeleteAsync** generano un'eccezione se il contenitore non esiste.

I metodi **DeleteIfExists** e **DeleteIfExistsAsync** restituiscono un valore booleano che indica se il contenitore è stato eliminato. Se il contenitore specificato non esiste, questi metodi restituiscono **false** per indicare che il contenitore non è stato eliminato.

Dopo aver eliminato un contenitore, non è possibile creare un contenitore con lo stesso nome per *almeno* 30 secondi. Il tentativo di creare un contenitore con lo stesso nome avrà esito negativo con codice di errore HTTP 409 (conflitto). Qualsiasi altra operazione nel contenitore o nei BLOB in esso contenuti avrà esito negativo con codice di errore HTTP 404 (non trovato).

Nell'esempio seguente viene eliminato il contenitore specificato e viene gestita l'eccezione se il contenitore non esiste:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

Nell'esempio seguente viene illustrato come eliminare tutti i contenitori che iniziano con un prefisso specificato.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteContainersWithPrefixAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Vedi anche

- [Operazione di creazione del contenitore](/rest/api/storageservices/create-container)
- [Delete Container operation](/rest/api/storageservices/delete-container) (Operazione di eliminazione contenitore)
