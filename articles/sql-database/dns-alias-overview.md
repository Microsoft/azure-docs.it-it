---
title: Alias DNS
description: Le applicazioni possono connettersi a un alias per il nome del server di database SQL di Azure. Nel frattempo, è possibile modificare il database SQL a cui l'alias fa riferimento in qualsiasi momento, per semplificare i test e così via.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 05fa542a0ad1c72f73148eefd304a9771798598d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820620"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias DNS per il database SQL di Azure

Il database SQL di Azure ha un server DNS (Domain Name System). PowerShell e le API REST accettano [chiamate per creare e gestire alias DNS](#anchor-powershell-code-62x) per il nome del server di database SQL.

È possibile usare un *alias DNS* al posto del nome del server di database SQL di Azure. I programmi client possono usare l'alias nelle stringhe di connessione. L'alias DNS fornisce un livello di conversione che consente di reindirizzare i programmi client a server diversi. Questo livello consente di evitare le difficoltà legate alla ricerca e alla modifica di tutti i client e delle relative stringhe di connessione.

Gli usi comuni per un alias DNS includono i casi seguenti:

- Creare un nome facile da ricordare per un server SQL di Azure.
- Durante lo sviluppo iniziale, l'alias può fare riferimento a un server di database SQL di test. Quando l'applicazione viene resa disponibile, è possibile modificare l'alias in modo che faccia riferimento al server di produzione. Il passaggio dall'ambiente di test a quello di produzione non richiede alcuna modifica alle configurazioni dei diversi client che si connettono al server di database.
- Si supponga che solo il database nell'applicazione venga spostato in un altro server di database SQL. In questo caso, è possibile modificare l'alias senza dover modificare le configurazioni di diversi client.
- Durante un'interruzione a livello di area si usa il ripristino geografico per ripristinare il database in un server e in un'area diversi. È possibile modificare l'alias esistente in modo che punti al nuovo server in modo che l'applicazione client esistente possa riconnettersi a esso. 

## <a name="domain-name-system-dns-of-the-internet"></a>DNS (Domain Name System) in Internet

Internet si basa su DNS. Il sistema DNS converte i nomi descrittivi nel nome del server di database SQL di Azure.

## <a name="scenarios-with-one-dns-alias"></a>Scenari con un alias DNS

Si supponga di dover spostare il sistema in un nuovo server di database SQL di Azure. In passato era necessario trovare e aggiornare ogni stringa di connessione in ogni programma client. Ora invece se le stringhe di connessione usano un alias DNS, è necessario aggiornare solo una proprietà dell'alias.

La funzionalità di alias DNS del database SQL di Azure può essere utile negli scenari seguenti:

### <a name="test-to-production"></a>Da ambiente di test ad ambiente di produzione

Quando si inizia a sviluppare programmi client, fare in modo che usino un alias DNS nelle stringhe di connessione. Impostare le proprietà dell'alias in modo da puntare a una versione di test del server di database SQL di Azure.

Successivamente, quando il nuovo sistema passa in produzione, è possibile aggiornare le proprietà dell'alias in modo da puntare al server di database SQL di produzione. Non è necessario apportare modifiche ai programmi client.

### <a name="cross-region-support"></a>Supporto per più aree

In seguito a un ripristino di emergenza, il server di database SQL potrebbe venire spostato in un'area geografica diversa. Per un sistema che utilizzava un alias DNS, è possibile evitare la necessità di trovare e aggiornare tutte le stringhe di connessione per tutti i client. È invece possibile aggiornare un alias per fare riferimento al nuovo server di database SQL che ora ospita il database.

## <a name="properties-of-a-dns-alias"></a>Proprietà di un alias DNS

Le proprietà seguenti si applicano a ogni alias DNS per il server di database SQL:

- *Nome univoco:* ogni nome di alias creato è univoco tra tutti i server di database SQL di Azure, proprio come avviene per i nomi dei server.
- *Server necessario:* è possibile creare un alias DNS solo se fa riferimento a un solo server, che deve esistere già. Un alias aggiornato deve sempre fare riferimento a un solo server esistente.
  - Quando si elimina un server di database SQL, Azure System elimina anche tutti gli alias DNS che fanno riferimento al server.
- *Nessuna associazione alle aree:* gli alias DNS non sono associati a un'area. Qualsiasi alias DNS può essere aggiornato per fare riferimento a un server di database SQL di Azure che si trova in qualsiasi area geografica.
  - Quando si aggiorna un alias per fare riferimento a un altro server, tuttavia, entrambi i server devono trovarsi nella stessa *sottoscrizione* di Azure.
- *Autorizzazioni:* per gestire un alias DNS, l'utente deve avere autorizzazioni di *collaboratore server* o superiori. Per altre informazioni, vedere [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../role-based-access-control/overview.md).

## <a name="manage-your-dns-aliases"></a>Gestire gli alias DNS

Per gestire a livello di codice gli alias DNS, sono disponibili le API REST e i cmdlet di PowerShell.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>API REST per la gestione degli alias DNS

La documentazione per le API REST è disponibile nel sito Web seguente:

- [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) (API REST del database SQL di Azure)

Le API REST sono inoltre disponibili in GitHub nella pagina:

- [Azure SQL Database server, DNS alias REST APIs](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json) (API REST per l'alias DNS per il server di database SQL di Azure)

<a name="anchor-powershell-code-62x"/>

#### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell per la gestione degli alias DNS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo di PowerShell Azure Resource Manager è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Per questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono sostanzialmente identici.

Sono disponibili cmdlet di PowerShell per chiamare le API REST.

Un esempio di codice relativo ai cmdlet di PowerShell usati per gestire gli alias DNS è disponibile in:

- [PowerShell per l'alias DNS per il database SQL di Azure](dns-alias-powershell.md)

I cmdlet usati nell'esempio di codice sono i seguenti:

- [New-AzSqlServerDNSAlias:](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias)crea un nuovo alias DNS nel sistema del servizio database SQL di Azure.New-AzSqlServerDNSAlias : Creates a new DNS alias in the Azure SQL Database service system. L'alias fa riferimento al server di database SQL di Azure 1.
- [Get-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Ottiene ed elenca tutti gli alias DNS assegnati al server di database SQL 1.
- [Set-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): Modifica il nome del server a cui l'alias è configurato per fare riferimento, dal server 1 al server di database SQL 2.
- [Remove-AzSqlServerDNSAlias](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): Rimuove l'alias DNS dal server di database SQL 2, utilizzando il nome dell'alias.

## <a name="limitations-during-preview"></a>Limitazioni durante la fase di anteprima

Attualmente l'alias DNS ha le limitazioni seguenti:

- *Ritardo fino a 2 minuti:* per l'aggiornamento o la rimozione di un alias DNS sono necessari fino a 2 minuti.
  - Indipendentemente da qualsiasi breve ritardo, l'alias smette immediatamente di reindirizzare le connessioni client al server legacy.
- *Ricerca DNS:* per il momento, l'unico modo autorevole per controllare a quale server fa riferimento un alias DNS specifico consiste nell'eseguire una [ricerca DNS](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup).
- _Il controllo delle tabelle non è supportato:Table auditing is not supported:_ Non è possibile usare un alias DNS in un server di database SQL di Azure con *il controllo* delle tabelle abilitato in un database.
  - Il controllo tabelle è deprecato.
  - È consigliabile passare al [controllo BLOB](sql-database-auditing.md).

## <a name="related-resources"></a>Risorse correlate

- [Panoramica della continuità aziendale del database SQL di Azure](sql-database-business-continuity.md), incluso il ripristino di emergenza.

## <a name="next-steps"></a>Passaggi successivi

- [PowerShell per l'alias DNS per il database SQL di Azure](dns-alias-powershell.md)
