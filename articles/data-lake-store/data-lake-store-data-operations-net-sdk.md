---
title: '.NET SDK: operazioni del file System su Azure Data Lake Storage Gen1'
description: Usare il Azure Data Lake Storage Gen1 .NET SDK per le operazioni del file System in Data Lake Storage Gen1, ad esempio creare cartelle e così via.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 7e33ecbbb49fc2b0683d0757da36deec72796806
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75638902"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Operazioni del file System su Data Lake Storage Gen1 con .NET SDK

> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

Questo articolo illustra come eseguire operazioni del file System su Data Lake Storage Gen1 usando .NET SDK. Le operazioni del file system includono la creazione di cartelle in un account Data Lake Storage Gen1, il caricamento di file, il download di file e così via.

Per istruzioni su come eseguire operazioni di gestione di account in Data Lake Storage Gen1 usando .NET SDK, vedere [operazioni di gestione degli account su data Lake storage Gen1 con .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Prerequisiti

* **Visual Studio 2013 o versione successiva**. Le istruzioni riportate in questo articolo usano Visual Studio 2019.

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Account Azure Data Lake Storage Gen1**. Per istruzioni su come creare un account, vedere [Introduzione a Azure Data Lake storage Gen1](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Creare un'applicazione .NET

Il codice di esempio disponibile in [GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) offre una descrizione dei processi di creazione dei file nell'archivio, concatenazione dei file, download di un file ed eliminazione di alcuni file nell'archivio. Questa sezione dell'articolo descrive in modo dettagliato le parti principali del codice.

1. In Visual Studio selezionare il menu **file** , **nuovo**e quindi **progetto**.
1. Scegliere **app console (.NET Framework)** e quindi fare clic su **Avanti**.
1. In **Nome del progetto** immettere `CreateADLApplication` e quindi selezionare **Crea**.
1. Aggiungere i pacchetti NuGet al progetto.

   1. Fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**.
   1. Nella scheda **Gestione pacchetti NuGet** verificare che l'origine del **pacchetto** sia impostata su **NuGet.org**. Assicurarsi inoltre che sia selezionata la casella di controllo **Includi versione preliminare** .
   1. Cercare e installare i pacchetti NuGet seguenti:

      * `Microsoft.Azure.DataLake.Store`-Questo articolo usa la versione 1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`-Questo articolo USA v 2.3.1.

      Chiudere **Gestione pacchetti NuGet**.

1. Aprire **Program.cs**, eliminare il codice esistente e quindi includere le istruzioni seguenti per aggiungere riferimenti agli spazi dei nomi.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Dichiarare le variabili come illustrato di seguito e fornire i valori per i segnaposto. Assicurarsi anche che il percorso locale e il nome file forniti siano presenti nel computer.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

Nelle sezioni rimanenti dell'articolo è possibile vedere come usare i metodi .NET disponibili per eseguire operazioni quali l'autenticazione, il caricamento di file e così via.

## <a name="authentication"></a>Authentication

* Per l'autenticazione dell'utente finale per l'applicazione, vedere [Autenticazione dell'utente finale con Data Lake Storage Gen1 con .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Per l'autenticazione da servizio a servizio per l'applicazione, vedere [Autenticazione da servizio a servizio con Data Lake Storage Gen1 tramite .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Creare un oggetto client

Il frammento di codice seguente crea l'oggetto client del file System di Data Lake Storage Gen1, che viene usato per inviare le richieste al servizio.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Creare un file e una directory

Aggiungere il frammento di codice seguente all'applicazione. Questo frammento di codice aggiunge un file e tutte le directory padre che non esistono.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Aggiungere a un file

Il frammento di codice seguente accoda dati a un file esistente nell'account Data Lake Storage Gen1.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Leggere un file

Il frammento di codice seguente legge i contenuti di un file in Data Lake Storage Gen1.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Ottenere le proprietà dei file

Il frammento di codice seguente restituisce le proprietà associate a un file o una directory.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

La definizione del `PrintDirectoryEntry` metodo è disponibile come parte dell'esempio [su GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>Rinominare un file

Il frammento di codice seguente rinomina un file esistente in un account Data Lake Storage Gen1.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Enumerare una directory

Il frammento di codice seguente enumera le directory in un account Data Lake Storage Gen1.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

La definizione del `PrintDirectoryEntry` metodo è disponibile come parte dell'esempio [su GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Eliminare directory in modo ricorsivo

Il frammento di codice seguente elimina una directory e tutte le relative sottodirectory in modo ricorsivo.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Esempi

Ecco alcuni esempi che illustrano come usare Data Lake Storage Gen1 filesystem SDK.

* [Esempio di base su GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Esempio avanzato su GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Vedere anche

* [Operazioni di gestione di account in Data Lake Storage Gen1 con .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Riferimento a .NET SDK di Data Lake Storage Gen1](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
