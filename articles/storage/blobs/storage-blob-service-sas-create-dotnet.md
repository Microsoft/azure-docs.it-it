---
title: Creare una classe di accesso client per un contenitore o un BLOB con .NETCreate a service SAS for a container or blob with .NET
titleSuffix: Azure Storage
description: Informazioni su come creare una firma di accesso condiviso del servizio per un contenitore o un BLOB usando la libreria client .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/20/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 10045a760d7e0fcb02a754bc9bb52a5ebca970f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137212"
---
# <a name="create-a-service-sas-for-a-container-or-blob-with-net"></a>Creare una classe di accesso client per un contenitore o un BLOB con .NETCreate a service SAS for a container or blob with .NET

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Questo articolo illustra come usare la chiave dell'account di archiviazione per creare una sessione di accesso client del servizio per un contenitore o un BLOB con la [libreria client di Archiviazione di Azure per .NET.](/dotnet/api/overview/azure/storage?view=azure-dotnet)

## <a name="create-a-service-sas-for-a-blob-container"></a>Creare una classe di disponibilità del servizio per un contenitore BLOBCreate a service SAS for a blob container

Per creare una firma di accesso condiviso del servizio per un contenitore, chiamare il metodo [CloudBlobContainer.GetSharedAccessSignature.To](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) create a service SAS for a container, call the CloudBlobContainer.GetSharedAccessSignature method.

Esempio di codice seguente crea una firma di accesso base in un contenitore. Se viene specificato il nome di un criterio di accesso archiviato esistente, tale criterio è associato alla firma di accesso condiviso. Se non vengono forniti criteri di accesso archiviati, il codice crea una firma di accesso zo cand ad hoc nel contenitore.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

## <a name="create-a-service-sas-for-a-blob"></a>Creare una contratto di servizio per un BLOBCreate a service SAS for a blob

Per creare una firma di accesso condiviso del servizio per un BLOB, chiamare il metodo [CloudBlob.GetSharedAccessSignature.To](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) create a service SAS for a blob, call the CloudBlob.GetSharedAccessSignature method.

L'esempio di codice seguente crea una firma di accesso base in un BLOB. Se viene specificato il nome di un criterio di accesso archiviato esistente, tale criterio è associato alla firma di accesso condiviso. Se non vengono forniti criteri di accesso archiviati, il codice crea una firma di accesso zo cand inhoc nel BLOB.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Concedere l'accesso limitato alle risorse di Archiviazione di Azure usando le firme di accesso condivisoGrant limited access to Azure Storage resources using shared access signatures (SAS)](../common/storage-sas-overview.md)
- [Creare un SAS di servizio](/rest/api/storageservices/create-service-sas)
