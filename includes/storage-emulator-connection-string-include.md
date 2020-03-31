---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67180351"
---
L'emulatore di archiviazione supporta un singolo account fisso e una chiave di autenticazione nota per l’autenticazione con chiave condivisa. Questo account e questa chiave sono le uniche credenziali con chiave condivisa consentite per l'utilizzo con l'emulatore di archiviazione. ovvero:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> La chiave di autenticazione supportata dall'emulatore di archiviazione è destinata solo al test della funzionalità del codice di autenticazione client. Non viene utilizzata per eventuali scopi di sicurezza. Non è possibile usare l'account di archiviazione di produzione e la chiave con l'emulatore di archiviazione. Non usare l'account di sviluppo con dati di produzione.
> 
> L'emulatore di archiviazione supporta solo la connessione tramite HTTP. HTTPS è tuttavia il protocollo consigliato per accedere alle risorse in un account di archiviazione di Azure di produzione.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Connettersi all'account dell'emulatore utilizzando un collegamento
Il modo più semplice per eseguire la connessione all'emulatore di archiviazione dall'applicazione consiste nel configurare una stringa di connessione nel file di configurazione dell'applicazione che fa riferimento al collegamento `UseDevelopmentStorage=true`. Ecco un esempio di stringa di connessione all'emulatore di archiviazione in un file app.config:Here's an example of a connection string to the storage emulator in an *app.config* file: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Connettersi all'account dell'emulatore utilizzando il nome account ben noto e la chiave
Per creare una stringa di connessione che fa riferimento al nome e alla chiave dell'account emulatore, è necessario specificare gli endpoint per ciascuno dei servizi che si vogliono usare dall'emulatore nella stringa di connessione. Ciò è necessario per fare in modo che la stringa di connessione faccia riferimento agli endpoint dell’emulatore, che variano rispetto a quelli per un account di archiviazione di produzione. Ad esempio, il valore della stringa di connessione risulterà simile al seguente:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Questo valore è identico al collegamento mostrato in precedenza, `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Specificare un proxy HTTP
È inoltre possibile specificare un proxy HTTP da usare quando si esegue il test del servizio nell'emulatore di archiviazione. Ciò può essere utile per osservare le richieste HTTP e le risposte in fase di debug delle operazioni nei servizi di archiviazione. Per specificare un proxy, aggiungere `DevelopmentStorageProxyUri` l'opzione alla stringa di connessione, e impostarne il valore sull'URI del proxy. Ad esempio, di seguito viene presentata una stringa di connessione che punta all'emulatore di archiviazione e configura un proxy HTTP:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

