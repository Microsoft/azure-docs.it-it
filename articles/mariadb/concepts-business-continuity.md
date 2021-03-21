---
title: Continuità aziendale-database di Azure per MariaDB
description: Informazioni sulla continuità aziendale (ripristino temporizzato, interruzione del data center, ripristino geografico) quando si usa il servizio database di Azure per MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: e164930736f7b93e56d9bd4187e72a9c8eb76202
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98659853"
---
# <a name="understand-business-continuity-in-azure-database-for-mariadb"></a>Comprendere la continuità aziendale nel database di Azure per MariaDB

Questo articolo descrive le funzionalità offerte da database di Azure per MariaDB per la continuità aziendale e il ripristino di emergenza. Informazioni sulle opzioni per il ripristino da eventi di arresto improvviso che potrebbero provocare la perdita di dati o la disattivazione del database e dell'applicazione. Informazioni sulle operazioni da eseguire quando si verifica un errore generato da un utente o da un'applicazione che influisce sull'integrità dei dati, se in un'area di Azure si verifica un'interruzione o quando l'applicazione richiede manutenzione.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funzionalità che è possibile usare per assicurare la continuità aziendale

Database di Azure per MariaDB offre funzionalità di continuità aziendale che includono i backup automatizzati e la possibilità per gli utenti di avviare il ripristino geografico. Ogni funzionalità presenta caratteristiche diverse in termini di tempo di recupero stimato (ERT) e di potenziale perdita di dati. Dopo aver compreso le opzioni disponibili, è possibile scegliere una di esse e usarle in modo combinato per i diversi scenari. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Si tratta dell'obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti (intervallo di tempo) che l'applicazione riesce a tollerare durante il ripristino dopo l'evento di arresto improvviso, ovvero l'obiettivo del punto di recupero (RPO).

La tabella seguente mette a confronto i valori ERT e RPO per le funzionalità disponibili:

| **Capacità** | **Base** | **Utilizzo generico** | **Ottimizzato per la memoria** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Ripristino temporizzato dal backup | Qualsiasi punto di ripristino compreso nel periodo di conservazione | Qualsiasi punto di ripristino compreso nel periodo di conservazione | Qualsiasi punto di ripristino compreso nel periodo di conservazione |
| Ripristino geografico dai backup con replica geografica | Non supportato | ERT < 12 ore<br/>RPO < 1 ora | ERT < 12 ore<br/>RPO < 1 ora |

> [!IMPORTANT]
> Se si elimina il server, vengono eliminati anche tutti i database contenuto nel server e non sarà possibile recuperarli. Non è possibile ripristinare un server eliminato.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Ripristinare un server in seguito a errore di un'applicazione o di un utente

È possibile utilizzare i backup del servizio per ripristinare un server da vari eventi di disturbo. Un utente può accidentalmente eliminare alcuni dati, una tabella importante o addirittura un intero database. Un'applicazione potrebbe sovrascrivere accidentalmente dei dati con dati errati a causa di un difetto dell'applicazione e così via.

È possibile eseguire un ripristino temporizzato per creare una copia del server a un punto valido nel tempo. Questo punto nel tempo deve essere compreso nel periodo di conservazione di backup configurato per il server. Dopo il ripristino dei dati nel nuovo server, è possibile sostituire il server originale con il server appena ripristinato o copiare i dati necessari dal server ripristinato nel server originale.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Eseguire il ripristino in seguito un'interruzione del data center a livello di area di Azure

Anche se raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che potrebbe durare solo pochi minuti, ma anche ore.

Una delle opzioni è attendere che il server torni online al termine dell'interruzione del data center. Questa opzione funziona per le applicazioni che possono permettersi di avere il server offline per un certo periodo, ad esempio gli ambienti di sviluppo. Quando il data center registra un'interruzione del servizio, non si sa quanto tempo essa durerà, quindi questa opzione funziona solo se è possibile rinunciare al server per un periodo di tempo.

La seconda opzione consiste nell'usare la funzionalità di ripristino geografico di Database di Azure per MariaDB che ripristina il server usando i backup con ridondanza geografica. Questi backup sono accessibili anche quando l'area in cui il server è ospitato è offline. È possibile eseguire il ripristino da questi backup a qualsiasi altra area e riportare il server online.

> [!IMPORTANT]
> Il ripristino geografico è possibile solo se è stato effettuato il provisioning del server con l'archivio di backup con ridondanza geografica.

## <a name="cross-region-read-replicas"></a>Repliche Read tra aree

È possibile usare le repliche di lettura tra aree per migliorare la continuità aziendale e la pianificazione del ripristino di emergenza. Le repliche di lettura vengono aggiornate in modo asincrono utilizzando la tecnologia di replica dei log binari di MariaDB. Altre informazioni sulle repliche di lettura, le aree disponibili e su come eseguire il failover dall' [articolo leggere i concetti relativi alle repliche](concepts-read-replicas.md). 

## <a name="faq"></a>Domande frequenti
### <a name="where-does-azure-database-for-mariadb-store-customer-data"></a>Dove il database di Azure per MariaDB archivia i dati dei clienti?
Per impostazione predefinita, il database di Azure per MariaDB non sposta o archivia i dati del cliente all'esterno dell'area in cui viene distribuito. Tuttavia, i clienti possono scegliere di abilitare i [backup con ridondanza geografica](concepts-backup.md#backup-redundancy-options) o creare una [replica di lettura tra aree](concepts-read-replicas.md#cross-region-replication) per archiviare i dati in un'altra area.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [backup automatici nel database di Azure per MariaDB](concepts-backup.md).
- Informazioni su come effettuare il ripristino usando il [portale di Azure](howto-restore-server-portal.md) o l'[interfaccia della riga di comando di Azure](howto-restore-server-cli.md).
- Informazioni sulle [repliche di lettura nel database di Azure per MariaDB](concepts-read-replicas.md).