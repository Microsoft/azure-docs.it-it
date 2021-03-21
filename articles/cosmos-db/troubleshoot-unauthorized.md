---
title: Risoluzione dei problemi Azure Cosmos DB eccezioni non autorizzate
description: Informazioni su come diagnosticare e correggere le eccezioni non autorizzate.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: aa9bf1fd706ccf6064893f1141be5e5b2f185ff3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94411168"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Diagnosticare e risolvere i problemi Azure Cosmos DB le eccezioni non autorizzate
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 401: la firma MAC trovata nella richiesta HTTP non corrisponde alla firma calcolata.
Se è stato ricevuto il messaggio di errore 401 "la firma MAC trovata nella richiesta HTTP non corrisponde alla firma calcolata", può essere causata dagli scenari seguenti.

Per gli SDK precedenti, l'eccezione può apparire come un'eccezione JSON non valida anziché l'eccezione 401 non autorizzata corretta. Gli SDK più recenti gestiscono correttamente questo scenario e forniscono un messaggio di errore valido.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi
Nell'elenco seguente sono contenute le cause e le soluzioni note per le eccezioni non autorizzate.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>La chiave non è stata ruotata correttamente è lo scenario più comune
La firma 401 MAC viene visualizzata poco dopo una rotazione della chiave e alla fine si interrompe senza alcuna modifica. 

#### <a name="solution"></a>Soluzione:
La chiave è stata ruotata e non ha seguito le [procedure consigliate](secure-access-to-data.md#key-rotation). La rotazione della chiave dell'account Azure Cosmos DB può richiedere da alcuni secondi a un certo numero di giorni, a seconda delle dimensioni dell'account di Azure Cosmos DB.

### <a name="the-key-is-misconfigured"></a>La chiave non è configurata correttamente 
Il problema della firma di 401 MAC sarà coerente e si verifica per tutte le chiamate che usano tale chiave.

#### <a name="solution"></a>Soluzione:
La chiave non è configurata correttamente nell'applicazione e usa la chiave errata per l'account oppure l'intera chiave non è stata copiata.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>L'applicazione usa le chiavi di sola lettura per le operazioni di scrittura
Il problema della firma 401 MAC si verifica solo per le operazioni di scrittura come create o Replace, ma le richieste di lettura hanno esito positivo.

#### <a name="solution"></a>Soluzione:
Impostare l'applicazione in modo che usi una chiave di lettura/scrittura per consentire il corretto completamento delle operazioni.

### <a name="race-condition-with-create-container"></a>Race condition con create container
Il problema di firma MAC 401 si verifica poco dopo la creazione di un contenitore. Questo problema si verifica solo fino al completamento della creazione del contenitore.

#### <a name="solution"></a>Soluzione:
Esiste una race condition con la creazione del contenitore. Un'istanza dell'applicazione sta provando ad accedere al contenitore prima del completamento della creazione del contenitore. Lo scenario più comune per questa race condition è se l'applicazione è in esecuzione e il contenitore viene eliminato e ricreato con lo stesso nome. L'SDK tenta di usare il nuovo contenitore, ma la creazione del contenitore è ancora in corso, quindi non contiene le chiavi.

## <a name="next-steps"></a>Passaggi successivi
* [Diagnosticare e risolvere](troubleshoot-dot-net-sdk.md) i problemi quando si usa Azure Cosmos DB .NET SDK.
* Informazioni sulle linee guida sulle prestazioni per [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) e [.NET v2](performance-tips.md).
* [Diagnosticare e risolvere](troubleshoot-java-sdk-v4-sql.md) i problemi quando si usa il Azure Cosmos DB Java v4 SDK.
* Informazioni sulle linee guida sulle prestazioni per [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).