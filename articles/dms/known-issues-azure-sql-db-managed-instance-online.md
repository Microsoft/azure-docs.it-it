---
title: Problemi noti e limitazioni con le migrazioni online in Azure SQL Istanza gestita
description: Informazioni sui problemi noti e sulle limitazioni della migrazione associate alle migrazioni online in Azure SQL Istanza gestita.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98695535"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Problemi noti/limitazioni della migrazione con migrazioni online in Azure SQL Istanza gestita

Di seguito sono descritti i problemi noti e le limitazioni associate alle migrazioni online da SQL Server al Istanza gestita SQL di Azure.

> [!IMPORTANT]
> Con le migrazioni online di SQL Server al database SQL di Azure, la migrazione dei tipi di dati SQL_variant non è supportata.

## <a name="backup-requirements"></a>Requisiti di backup

- **Backup con checksum**

    Il servizio migrazione del database di Azure usa il metodo di backup e ripristino per eseguire la migrazione dei database locali a SQL Istanza gestita. Il servizio migrazione del database di Azure supporta solo i backup creati con checksum.

    [Abilitare o disabilitare i checksum di backup durante il backup o il ripristino (SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).

    > [!NOTE]
    > Se si esegue il backup del database con la compressione, il checksum è un comportamento predefinito a meno che non sia esplicitamente disabilitato.

    Con le migrazioni offline, se si sceglie di eseguire il **servizio migrazione del database di Azure...**, il servizio migrazione del database di Azure eseguirà il backup del database con l'opzione checksum abilitata.

- **Supporti di backup**

    Assicurarsi di eseguire ogni backup su un supporto di backup separato (file di backup). Il servizio migrazione del database di Azure non supporta i backup aggiunti a un singolo file di backup. Eseguire backup completi e backup del log per separare i file di backup.

## <a name="data-and-log-file-layout"></a>Layout del file di dati e di log

- **Numero di file di log**

    Il servizio migrazione del database di Azure non supporta i database con più file di log. Se si dispone di più file di log, compattarli e riorganizzarli in un unico file di log delle transazioni. Poiché non è possibile eseguire la modalità remota per i file di log non vuoti, è necessario innanzitutto eseguire il backup del file di log.

## <a name="sql-server-features"></a>caratteristiche di SQL Server

- **FileStream/FileTable**

    SQL Istanza gestita attualmente non supporta FileStream e tabelle FileTable. Per i carichi di lavoro che dipendono da queste funzionalità, è consigliabile optare per i server SQL in esecuzione nelle macchine virtuali di Azure come destinazione di Azure.

- **Tabelle in memoria**

    OLTP in memoria è disponibile nei livelli Premium e business critical per SQL Istanza gestita; il livello per utilizzo generico non supporta OLTP in memoria.

## <a name="migration-resets"></a>Reimpostazioni della migrazione

- **Deployments**

    SQL Istanza gestita è un servizio PaaS con aggiornamenti automatici delle patch e della versione. Durante la migrazione del Istanza gestita SQL, gli aggiornamenti non critici vengono mantenuti per un massimo di 36 ore. In seguito (e per gli aggiornamenti critici), se la migrazione viene interrotta, il processo Reimposta lo stato di ripristino completo.

    La migrazione di cutover può essere chiamata solo dopo il ripristino del backup completo e l'aggiornamento di tutti i backup del log. Se le complete della migrazione di produzione sono interessate, contattare l' [alias di feedback DMS di Azure](mailto:dmsfeedback@microsoft.com).

## <a name="smb-file-share-connectivity"></a>Connettività condivisione file SMB

I problemi di connessione alla condivisione file SMB sono probabilmente causati da un problema di autorizzazioni. 

Per testare la connettività della condivisione file SMB, attenersi alla seguente procedura: 

1. Salvare un backup nella condivisione file SMB. 
1. Verificare la connettività di rete tra la subnet del servizio migrazione del database di Azure e la SQL Server di origine. Il modo più semplice per eseguire questa operazione consiste nel distribuire una macchina virtuale SQL Server alla subnet DMS e connettersi alla SQL Server di origine usando SQL Server Management Studio. 
1. Ripristinare l'intestazione nel SQL Server di origine dal backup nella condivisione file: 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

Se non è possibile connettersi alla condivisione file, configurare le autorizzazioni con i passaggi seguenti: 

1. Passare alla condivisione file usando Esplora file. 
1. Fare clic con il pulsante destro del mouse sulla condivisione file e scegliere Proprietà. 
1. Scegliere la scheda **condivisione** e scegliere **condivisione avanzata**. 
1. Aggiungere l'account di Windows utilizzato per la migrazione e assegnargli l'accesso con controllo completo. 
1. Aggiungere l'account del servizio SQL Server e assegnargli l'accesso con controllo completo. Controllare la **Gestione configurazione SQL Server** per l'account del servizio SQL Server se non si è certi dell'account usato. 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="Fornire l'accesso con controllo completo agli account di Windows utilizzati per la migrazione e per l'account del servizio SQL Server. ":::

