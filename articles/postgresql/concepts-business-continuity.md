---
title: Continuità aziendale-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive la continuità aziendale (ripristino temporizzato, interruzione data center, ripristino geografico) quando si usa database di Azure per PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 35b2236ae6ffd3df3e458cdbd4bc01e89a1da2b2
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245307"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Panoramica della continuità aziendale con database di Azure per PostgreSQL-server singolo

Questa panoramica descrive le funzionalità offerte da Database di Azure per PostgreSQL per la continuità aziendale e il ripristino di emergenza. Informazioni sulle opzioni per il ripristino da eventi di arresto improvviso che potrebbero provocare la perdita di dati o la disattivazione del database e dell'applicazione. Informazioni sulle operazioni da eseguire quando si verifica un errore generato da un utente o da un'applicazione che influisce sull'integrità dei dati, se in un'area di Azure si verifica un'interruzione o quando l'applicazione richiede manutenzione.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funzionalità che è possibile usare per assicurare la continuità aziendale

Database di Azure per PostgreSQL offre funzionalità di continuità aziendale che includono i backup automatizzati e la possibilità per gli utenti di avviare il ripristino geografico. Ogni funzionalità presenta caratteristiche diverse in termini di tempo di recupero stimato (ERT) e di potenziale perdita di dati. Dopo aver compreso le opzioni disponibili, è possibile scegliere una di esse e usarle in modo combinato per i diversi scenari. Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Si tratta dell'obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti (intervallo di tempo) che l'applicazione riesce a tollerare durante il ripristino dopo l'evento di arresto improvviso, ovvero l'obiettivo del punto di recupero (RPO).

La tabella seguente mette a confronto i valori ERT e RPO per le funzionalità disponibili:

| **Funzionalità** | **Basic** | **Utilizzo generico** | **Ottimizzato per la memoria** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Ripristino temporizzato dal backup | Qualsiasi punto di ripristino compreso nel periodo di conservazione | Qualsiasi punto di ripristino compreso nel periodo di conservazione | Qualsiasi punto di ripristino compreso nel periodo di conservazione |
| Ripristino geografico dai backup con replica geografica | Non supportato | ERT < 12 ore<br/>RPO < 1 ora | ERT < 12 ore<br/>RPO < 1 ora |

> [!IMPORTANT]
> **Non è possibile** ripristinare i server eliminati. Se si elimina il server, vengono eliminati anche tutti i database appartenenti al server e non sarà possibile recuperarli. Usare [blocco risorse di Azure](../azure-resource-manager/management/lock-resources.md) per evitare l'eliminazione accidentale del server.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Ripristinare un server in seguito a errore di un'applicazione o di un utente

È possibile usare i backup del servizio per ripristinare un server da svariati eventi che possono causare interruzioni del funzionamento. Un utente può accidentalmente eliminare alcuni dati, una tabella importante o addirittura un intero database. Un'applicazione potrebbe sovrascrivere accidentalmente dei dati con dati errati a causa di un difetto dell'applicazione e così via.

È possibile eseguire un **ripristino temporizzato** per creare una copia del server a un momento valido noto. Questo punto nel tempo deve essere compreso nel periodo di conservazione di backup configurato per il server. Dopo il ripristino dei dati nel nuovo server, è possibile sostituire il server originale con il server appena ripristinato o copiare i dati necessari dal server ripristinato nel server originale.

## <a name="recover-from-an-azure-data-center-outage"></a>Ripristino da un'interruzione del data center di Azure

Anche se raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che potrebbe durare solo pochi minuti, ma anche ore.

Una delle opzioni è attendere che il server torni online al termine dell'interruzione del data center. Questa opzione funziona per le applicazioni che possono permettersi di avere il server offline per un certo periodo, ad esempio gli ambienti di sviluppo. Quando si verifica un'interruzione di un data center, non si sa quanto tempo può durare l'interruzione, quindi questa opzione funziona solo se non è necessario il server per un periodo di tempo.

## <a name="geo-restore"></a>Ripristino geografico

La funzionalità di ripristino geografico ripristina il server usando i backup con ridondanza geografica. I backup sono ospitati nell' [area abbinata](../best-practices-availability-paired-regions.md)del server. È possibile eseguire il ripristino da questi backup in qualsiasi altra area. Il ripristino geografico crea un nuovo server con i dati dei backup. Per altre informazioni sul ripristino geografico, vedere l' [articolo Concetti relativi a backup e ripristino](concepts-backup.md).

> [!IMPORTANT]
> Il ripristino geografico è possibile solo se è stato effettuato il provisioning del server con l'archivio di backup con ridondanza geografica. Per passare da backup con ridondanza locale a backup con ridondanza geografica per un server esistente, è necessario eseguire un dump del server esistente usando pg_dump e quindi ripristinarlo in un nuovo server configurato per i backup con ridondanza geografica.

## <a name="cross-region-read-replicas"></a>Repliche Read tra aree
È possibile usare le repliche di lettura tra aree per migliorare la continuità aziendale e la pianificazione del ripristino di emergenza. Le repliche di lettura vengono aggiornate in modo asincrono usando la tecnologia di replica fisica di PostgreSQL. Altre informazioni sulle repliche di lettura, le aree disponibili e su come eseguire il failover dall' [articolo leggere i concetti relativi alle repliche](concepts-read-replicas.md). 

## <a name="faq"></a>Domande frequenti
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Dove database di Azure per PostgreSQL archivia i dati dei clienti?
Per impostazione predefinita, database di Azure per PostgreSQL non sposta o archivia i dati dei clienti dall'area in cui è distribuito. Tuttavia, i clienti possono scegliere di abilitare i [backup con ridondanza geografica](concepts-backup.md#backup-redundancy-options) o creare una [replica di lettura tra aree](concepts-read-replicas.md#cross-region-replication) per archiviare i dati in un'altra area.


## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sui [backup automatizzati in Database di Azure per PostgreSQL](concepts-backup.md). 
- Informazioni su come effettuare il ripristino usando il [portale di Azure](howto-restore-server-portal.md) o l'[interfaccia della riga di comando di Azure](howto-restore-server-cli.md).
- Altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).