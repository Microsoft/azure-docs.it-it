---
title: Usare .NET per gestire proprietà e metadati per un contenitore BLOBUse .NET to manage properties and metadata for a blob container
titleSuffix: Azure Storage
description: Informazioni su come impostare e recuperare le proprietà di sistema e archiviare i metadati personalizzati nei contenitori BLOB nell'account di Archiviazione di Azure usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135922"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Gestire le proprietà e i metadati del contenitore con .NETManage container properties and metadata with .NET

I contenitori BLOB supportano le proprietà di sistema e i metadati definiti dall'utente, oltre ai dati che contengono. Questo articolo illustra come gestire le proprietà di sistema e i metadati definiti dall'utente con la [libreria client di Archiviazione di Azure per .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="about-properties-and-metadata"></a>Informazioni su proprietà e metadati

- **Proprietà di sistema:** proprietà di sistema in ogni risorsa di archiviazione BLOB. Alcune di esse possono essere lette o impostate, mentre altre sono di sola lettura. Anche se in modo non esplicito, alcune proprietà di sistema corrispondono a specifiche intestazioni HTTP standard. La libreria client di Archiviazione di Azure per .NET gestisce automaticamente queste proprietà.

- **Metadati definiti dall'utente:** i metadati definiti dall'utente sono costituiti da una o più coppie nome-valore specificate per una risorsa di archiviazione BLOB. È possibile usare i metadati per archiviare valori aggiuntivi con la risorsa. I valori di metadati sono solo per le proprie esigenze e non influiscono sul comportamento della risorsa.

Il recupero dei valori di proprietà e metadati per una risorsa di archiviazione BLOB è un processo in due passaggi. Prima di leggere questi valori, è necessario recuperarli in modo esplicito chiamando il metodo **FetchAttributes** o **FetchAttributesAsync**. L'eccezione a questa regola è che il **Exists** ed **ExistsAsync** metodi chiamano il metodo **FetchAttributes** appropriato sotto le coperte. Quando si chiama uno di questi metodi, non è necessario chiamare anche **FetchAttributes**.

> [!IMPORTANT]
> Se si ritiene che la proprietà o i valori dei metadati per una risorsa di archiviazione non siano stati popolati, verificare che il codice chiami il metodo **FetchAttributes** o **FetchAttributesAsync**.

Le coppie nome/valore dei metadati sono intestazioni HTTP valide e pertanto devono rispettare tutte le restrizioni che regolano le intestazioni HTTP. I nomi dei metadati devono essere nomi di intestazione HTTP validi e identificatori validi di C, possono contenere solo caratteri ASCII e devono essere considerati senza distinzione tra maiuscole e minuscole. I valori dei metadati contenenti caratteri non ASCII devono essere con codifica Base64 o con codifica URL.

## <a name="retrieve-container-properties"></a>Recuperare le proprietà del contenitoreRetrieve container properties

Per recuperare le proprietà del contenitore, chiamare uno dei seguenti metodi:

- [FetchAttributi](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

Nell'esempio di codice riportato di seguito vengono recuperate le proprietà di sistema di un contenitore e vengono scritti alcuni valori di proprietà in una finestra della console:The following code example fetches a container's system properties and writes some property values to a console window:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

## <a name="set-and-retrieve-metadata"></a>Impostare e recuperare i metadati

È possibile specificare i metadati come uno o più coppie nome-valore in una risorsa BLOB o contenitore. Per impostare i metadati, aggiungere coppie nome-valore alla raccolta **di metadati** nella risorsa, quindi chiamare uno dei metodi seguenti per scrivere i valori:

- [SetMetadata (Metadati)](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Il nome dei metadati deve essere conforme alle convenzioni di denominazione degli identificatori C#. I nomi dei metadati mantengono la distinzione tra maiuscole e minuscole con cui sono stati creati, ma non fanno distinzione tra maiuscole e minuscole quando vengono impostati o letti. Se due o più intestazioni di metadati con lo stesso nome vengono inviate per una risorsa, l'archiviazione BLOB restituisce il codice di errore HTTP 400 (richiesta non valida).

Il seguente codice di esempio imposta i metadati in un contenitore. Un valore è impostato mediante l'utilizzo del metodo di raccolta **Aggiungi** . L'altro valore è impostato utilizzando la sintassi implicita chiave/valore. Entrambi sono validi.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

Per recuperare i metadati, chiamare il metodo **FetchAttributes** o **FetchAttributesAsyn** nel BLOB o nel contenitore per popolare la raccolta **Metadati**, quindi leggere i valori, come nell'esempio mostrato di seguito.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Vedere anche

- [Operazione Get Container Properties](/rest/api/storageservices/get-container-properties)
- [Operazione Imposta metadati contenitore](/rest/api/storageservices/set-container-metadata)
- [Operazione Get Container Metadata](/rest/api/storageservices/set-container-metadata)
