---
title: Risolvere i problemi relativi alle eccezioni Azure Cosmos DB servizio non disponibile
description: Informazioni su come diagnosticare e correggere Azure Cosmos DB eccezioni non disponibili del servizio.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: d19d30c03412ba7212211b30646acb50c3f55ece
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340023"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Diagnosticare e risolvere i problemi relativi alle eccezioni Azure Cosmos DB servizio non disponibili
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

SDK non è riuscito a connettersi a Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
L'elenco seguente contiene le cause note e le soluzioni per le eccezioni del servizio non disponibili.

### <a name="the-required-ports-are-being-blocked"></a>Le porte necessarie sono bloccate
Verificare che tutte le [porte necessarie](sql-sdk-connection-modes.md#service-port-ranges) siano abilitate.

### <a name="client-side-transient-connectivity-issues"></a>Problemi di connettività temporanei lato client
Quando si verificano problemi di connettività temporanei che causano timeout, le eccezioni non disponibili del servizio possono essere rilevate. In genere, la traccia dello stack correlata a questo scenario conterrà un `TransportException` errore. Ad esempio:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Per risolverlo, seguire la procedura per la [risoluzione dei problemi di timeout della richiesta](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) .

### <a name="service-outage"></a>Interruzione del servizio
Controllare lo [stato di Azure](https://status.azure.com/status) per verificare se è in corso un problema.


## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi quando si usa Azure Cosmos DB .NET SDK.
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).