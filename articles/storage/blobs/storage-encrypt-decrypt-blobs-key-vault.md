---
title: 'Esercitazione: Crittografare e decrittografare BLOB con Azure Key Vault'
titleSuffix: Azure Storage
description: Informazioni su come crittografare e decrittografare un BLOB usando la crittografia lato client con Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c1d26fda57d665cc8d83f594f4efeebebc7bf139
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81456890"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Esercitazione: Crittografare e decrittografare BLOB con Azure Key Vault

Questa esercitazione illustra come usare la crittografia di archiviazione lato client con l'insieme di credenziali delle chiavi di Azure. Illustra come crittografare e decrittografare un BLOB in un'applicazione console mediante queste tecnologie.

**Tempo previsto per il completamento**: 20 minuti

Per informazioni generali sull'Insieme di credenziali delle chiavi di Azure, vedere [Cos'è l'Insieme di credenziali delle chiavi di Azure?](../../key-vault/general/overview.md)

Per informazioni generali sulla crittografia lato client per Archiviazione di Azure, vedere l'articolo relativo alla [crittografia lato client e all'Insieme di credenziali delle chiavi di Azure per Archiviazione di Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue:

* Un account di archiviazione Azure
* Visual Studio 2013 o versioni successive
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Panoramica della crittografia lato client

Per una panoramica sulla crittografia lato client per Archiviazione di Azure, vedere l'articolo relativo alla [crittografia lato client e all'Insieme di credenziali delle chiavi di Azure per Archiviazione di Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Ecco una breve descrizione del funzionamento della crittografia lato client:

1. Azure Storage Client SDK genera una chiave di crittografia del contenuto (CEK) che funziona come chiave simmetrica monouso.
2. I dati del cliente vengono crittografati con questa chiave CEK.
3. Viene quindi eseguito il wrapping della chiave CEK mediante la chiave di crittografia della chiave (KEK). La chiave KEK è identificata con un identificatore di chiave e può essere costituita da una coppia di chiavi asimmetriche o da una chiave simmetrica. Può essere gestita localmente o archiviata nell'insieme di credenziali delle chiavi di Azure. Il client di archiviazione non ha mai accesso alla chiave KEK. Richiama solo l'algoritmo di wrapping della chiave fornito dall'insieme di credenziali chiave. I clienti possono scegliere di usare provider personalizzati per il wrapping o la rimozione del wrapping delle chiavi, se lo desiderano.
4. I dati crittografati vengono quindi caricati nel servizio Archiviazione di Azure.

## <a name="set-up-your-azure-key-vault"></a>Impostare l'insieme di credenziali delle chiavi di Azure

Per procedere con questa esercitazione, è necessario effettuare le operazioni seguenti, descritte nell'esercitazione [Avvio rapido: Impostare e recuperare un segreto da Azure Key Vault tramite un'app Web .NET](../../key-vault/secrets/quick-create-net.md):

* Creare un insieme di credenziali delle chiavi.
* Aggiungere una chiave o un segreto all'insieme di credenziali delle chiavi.
* Registrare un'applicazione con Azure Active Directory.
* Autorizzare l'applicazione a usare la chiave o il segreto.

Prendere nota dei valori di ClientID e ClientSecret generati durante la registrazione di un'applicazione con Azure Active Directory.

Creare entrambe le chiavi nell'insieme di credenziali delle chiavi. Per il resto dell'esercitazione si presuppone che siano stati usati i nomi seguenti: ContosoKeyVault e TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Creare un'applicazione console con pacchetti e AppSettings

In Visual Studio creare una nuova applicazione console.

Aggiungere i pacchetti NuGet necessari nella console Gestione pacchetti.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Aggiungere AppSettings al file App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Aggiungere le direttive `using` seguenti e assicurarsi di aggiungere al progetto un riferimento a System.Configuration.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Aggiungere un metodo per ottenere un token per l'applicazione console

Il metodo seguente viene usato dalle classi dell'insieme di credenziali delle chiavi che devono essere autenticate per l'accesso all'insieme di credenziali delle chiavi dell'utente.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Accedere all'archiviazione di Azure e a Key Vault nel programma

Aggiungere il codice seguente al metodo Main().

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Modelli a oggetti dell'insieme di credenziali chiave
> 
> È importante capire che esistono in realtà due modelli a oggetti dell'insieme di credenziali chiave da tenere presenti: uno è basato sull'API REST (spazio dei nomi KeyVault) e l'altro è un'estensione per la crittografia lato client.
> 
> Il client dell'insieme di credenziali delle chiavi interagisce con l'API REST e interpreta correttamente i segreti e le chiavi Web JSON per i due tipi di oggetti contenuti nell'insieme di credenziali delle chiavi.
> 
> Le estensioni dell'insieme di credenziali chiave sono classi che sembrano create appositamente per la crittografia lato client in Archiviazione di Azure. Contengono un'interfaccia per le chiavi, ovvero IKey, e classi basate sul concetto di resolver di chiavi. Esistono due implementazioni di IKey che è necessario conoscere: RSAKey e SymmetricKey. Coincidono con gli oggetti contenuti in un insieme di credenziali chiave, ma a questo punto sono classi indipendenti (pertanto la chiave e il segreto recuperati dal client insieme di credenziali chiave non implementano IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Crittografare un BLOB ed eseguire il caricamento

Aggiungere il codice seguente per crittografare un BLOB e caricarlo nell'account di Archiviazione di Azure. Il metodo **ResolveKeyAsync** usato restituisce un'interfaccia IKey.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Se si osserva il costruttore BlobEncryptionPolicy, è possibile notare che è in grado di accettare una chiave e/o un resolver. Tenere presente che al momento non è possibile usare un resolver per la crittografia perché non supporta attualmente una chiave predefinita.

## <a name="decrypt-blob-and-download"></a>Decrittografare un BLOB ed eseguire il download

La decrittografia è l'operazione per cui si rivelano utili le classi Resolver. L'ID della chiave usata per la crittografia è associato al BLOB nei relativi metadati, pertanto non è necessario recuperare la chiave e ricordare l'associazione tra la chiave e il BLOB. È sufficiente verificare che la chiave rimanga nell'insieme di credenziali delle chiavi.   

La chiave privata di una chiave RSA resta nell'insieme di credenziali delle chiavi. Perché venga eseguita la decrittografia, la chiave crittografata nei metadati del BLOB che contengono la chiave CEK viene pertanto inviata all'insieme di credenziali delle chiavi per la decrittografia.

Aggiungere quanto segue per decrittografare il BLOB appena caricato.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Esistono un paio di altri tipi di resolver per semplificare la gestione delle chiavi, ovvero: AggregateKeyResolver e CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Uso dei segreti dell'insieme di credenziali delle chiavi

Per usare un segreto con la crittografia lato client, usare la classe SymmetricKey, dal momento che un segreto è in pratica una chiave simmetrica. Come indicato in precedenza però, un segreto nell'insieme di credenziali delle chiavi non corrisponde esattamente a una classe SymmetricKey. Tenere presenti le informazioni seguenti:

* La chiave in una classe SymmetricKey deve avere una lunghezza fissa: 128, 192, 256, 384 o 512 bit.
* La chiave in una classe SymmetricKey deve essere con codifica Base64.
* Un segreto dell'insieme di credenziali delle chiavi da usare come SymmetricKey deve avere come tipo di contenuto "application/octet-stream" nell'insieme di credenziali delle chiavi.

Di seguito è riportato un esempio di PowerShell per la creazione di un segreto nell'insieme di credenziali delle chiavi che può essere usato come SymmetricKey.
Si noti che il valore hardcoded $key è solo a scopo dimostrativo. È possibile generare questa chiave nel codice personale.

```csharp
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

Nell'applicazione console è possibile usare la stessa chiamata descritta precedentemente per recuperare questo segreto come SymmetricKey.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

È tutto. Buon lavoro.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Archiviazione di Microsoft Azure con C#, vedere [Libreria client di archiviazione di Microsoft Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Per altre informazioni sull'API REST per i BLOB, vedere [API REST del servizio BLOB](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Per le informazioni più recenti su Archiviazione di Microsoft Azure, visitare il [blog del relativo team](https://blogs.msdn.com/b/windowsazurestorage/).
