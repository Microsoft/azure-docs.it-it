---
title: Problemi comuni-servizio migrazione del database di Azure
description: Informazioni su come risolvere i problemi noti o gli errori comuni associati all'uso del servizio migrazione del database di Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: f4baca7f261aa7544b54992a5e1ddf620794774f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962281"
---
# <a name="troubleshoot-common-azure-database-migration-service-issues-and-errors"></a>Risolvere i problemi e gli errori comuni del servizio migrazione del database di Azure

Questo articolo descrive alcuni problemi comuni ed errori che gli utenti del servizio migrazione del database di Azure possono incontrare. L'articolo contiene anche informazioni su come risolvere questi problemi ed errori.

> [!NOTE]
> Comunicazione senza distorsione
>
> Microsoft supporta un ambiente diversificato ed inclusivo. Questo articolo contiene riferimenti alla parola _slave_. La [guida di stile Microsoft per la comunicazione senza pregiudizi](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) la riconosce come parola di esclusione. La parola viene usata in questo articolo per coerenza perché è attualmente la parola usata nel software. Quando il software verrà aggiornato per rimuovere la parola, questo articolo verrà aggiornato di conseguenza.
>

## <a name="migration-activity-in-queued-state"></a>Attività di migrazione nello stato in coda

Quando si creano nuove attività in un progetto di servizio migrazione del database di Azure, le attività rimangono in uno stato di Accodamento.

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo problema si verifica quando l'istanza del servizio migrazione del database di Azure ha raggiunto la capacità massima per le attività in corso eseguite simultaneamente. Ogni nuova attività viene accodata fino a quando non diventa disponibile la capacità. | Verificare che l'istanza del servizio di migrazione dei dati abbia attività in esecuzione nei progetti. È possibile continuare a creare nuove attività che vengono aggiunte automaticamente alla coda per l'esecuzione. Non appena una delle attività in esecuzione esistente viene completata, viene avviata l'esecuzione successiva dell'attività in coda e lo stato passa automaticamente allo stato di esecuzione. Non è necessario eseguire alcuna azione aggiuntiva per avviare la migrazione dell'attività in coda.<br><br> |

## <a name="max-number-of-databases-selected-for-migration"></a>Numero massimo di database selezionati per la migrazione

Si verifica l'errore seguente quando si crea un'attività per un progetto di migrazione del database per lo spostamento nel database SQL di Azure o in un Istanza gestita SQL di Azure:

* **Errore**: errore di convalida delle impostazioni di migrazione "," errorDetail ":" più del numero massimo di oggetti ' 4' di ' database ' è stato selezionato per la migrazione. "

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo errore viene visualizzato dopo aver selezionato più di quattro database per una singola attività di migrazione. Attualmente, ogni attività di migrazione è limitata a quattro database. | Selezionare quattro o meno database per attività di migrazione. Se è necessario eseguire la migrazione di più di quattro database in parallelo, effettuare il provisioning di un'altra istanza del servizio migrazione del database di Azure. Attualmente, ogni sottoscrizione supporta fino a due istanze del servizio migrazione del database di Azure.<br><br> |

## <a name="errors-for-mysql-migration-to-azure-mysql-with-recovery-failures"></a>Errori per la migrazione di MySQL ad Azure MySQL con errori di ripristino

Quando si esegue la migrazione da MySQL a database di Azure per MySQL usando il servizio migrazione del database di Azure, l'attività di migrazione ha esito negativo con l'errore seguente:

* **Errore**: errore di migrazione del database-l'attività' taskid ' è stata sospesa a causa di [n] errori di ripristino successivi.

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo errore può verificarsi quando l'utente che esegue la migrazione manca il ruolo ReplicationAdmin e/o i privilegi del CLIENT di replica, della REPLICA di replica e di SUPER (versioni precedenti a MySQL 5.6.6).<br><br><br><br><br><br><br><br><br><br><br><br><br> | Assicurarsi che i [privilegi prerequisiti](./tutorial-mysql-azure-mysql-online.md#prerequisites) per l'account utente siano configurati in modo accurato nell'istanza del database di Azure per MySQL. Ad esempio, è possibile seguire la procedura seguente per creare un utente denominato "migrateuser" con i privilegi necessari:<br>1. creare l'utente migrateuser@ '%' identificato da' Secret '; <br>2. concedere tutti i privilegi per db_name. * a' migrateuser ' @ '%' identificato da' Secret '; Ripetere questo passaggio per concedere l'accesso a più database <br>3. concedere a slave di replica il *.* in ' migrateuser ' @ '%' identificato da' Secret ';<br>4. concedere il client di replica in *.* in ' migrateuser ' @ '%' identificato da' Secret ';<br>5. privilegi di svuotamento; |

## <a name="error-when-attempting-to-stop-azure-database-migration-service"></a>Errore durante il tentativo di arrestare il servizio migrazione del database di Azure

Viene visualizzato l'errore seguente quando si arresta l'istanza del servizio migrazione del database di Azure:

* **Errore**: Impossibile arrestare il servizio. Errore: {'error':{'code':'InvalidRequest','message':'Una o più attività sono attualmente in esecuzione. Per arrestare il servizio, attendere che le attività siano state completate o arrestare manualmente tali attività e riprovare .'}}

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo errore viene visualizzato quando l'istanza del servizio che si sta tentando di arrestare include attività ancora in esecuzione o presenti nei progetti di migrazione. <br><br><br><br><br><br> | Assicurarsi che non siano presenti attività in esecuzione nell'istanza del servizio migrazione del database di Azure che si sta tentando di arrestare. È anche possibile eliminare le attività o i progetti prima di tentare di arrestare il servizio. Nei passaggi seguenti viene illustrato come rimuovere i progetti per la pulizia dell'istanza del servizio di migrazione eliminando tutte le attività in esecuzione:<br>1. nome Install-Module AzureRM. DataMigration <br>2. Login-AzureRmAccount <br>3. Select-AzureRmSubscription-Subscriptionname " \<subName> " <br> 4. Remove-AzureRmDataMigrationProject-Name \<projectName> -ResourceGroupName \<rgName> -ServiceName \<serviceName> -DeleteRunningTask |

## <a name="error-when-attempting-to-start-azure-database-migration-service"></a>Errore durante il tentativo di avviare il servizio migrazione del database di Azure

Viene visualizzato l'errore seguente all'avvio dell'istanza del servizio migrazione del database di Azure:

* **Errore**: Impossibile avviare il servizio. Errore: {"errorDetail": "Impossibile avviare il servizio. contattare il supporto tecnico Microsoft"}

| Causa         | Risoluzione |
| ------------- | ------------- |
| Questo errore viene visualizzato quando l'istanza precedente ha avuto esito negativo internamente. Questo errore si verifica raramente e il team di progettazione ne è a conoscenza. <br> | Eliminare l'istanza del servizio che non è possibile avviare, quindi eseguire il provisioning di un nuovo per sostituirlo. |

## <a name="error-restoring-database-while-migrating-sql-to-azure-sql-db-managed-instance"></a>Errore durante il ripristino del database durante la migrazione di SQL nell'istanza gestita di database SQL di Azure

Quando si esegue una migrazione in linea da SQL Server ad Azure SQL Istanza gestita, il cutover ha esito negativo con l'errore seguente:

* **Errore**: operazione di ripristino non riuscita per l'ID operazione ' OperationId '. Il codice ' AuthorizationFailed ', messaggio ' client ' ClientID ' con ID oggetto ' objectId ' non è autorizzato a eseguire l'azione ' Microsoft. SQL/locations/managedDatabaseRestoreAzureAsyncOperation/Read ' sull'ambito '/subscriptions/subscriptionId ' .'.

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Questo errore indica che l'entità applicazione utilizzata per la migrazione in linea da SQL Server a SQL Istanza gestita non dispone dell'autorizzazione di collaborazione per la sottoscrizione. Alcune chiamate API con Istanza gestita attualmente richiedono questa autorizzazione per la sottoscrizione per l'operazione di ripristino. <br><br><br><br><br><br><br><br><br><br><br><br><br><br> | Usare il `Get-AzureADServicePrincipal` cmdlet di PowerShell con `-ObjectId` disponibile nel messaggio di errore per elencare il nome visualizzato dell'ID applicazione usato.<br><br> Convalidare le autorizzazioni per questa applicazione e assicurarsi che disponga del [ruolo Collaboratore](../role-based-access-control/built-in-roles.md#contributor) a livello di sottoscrizione. <br><br> Il team di progettazione del servizio migrazione del database di Azure sta lavorando per limitare l'accesso richiesto dal ruolo di collaborazione corrente per la sottoscrizione. Se si dispone di un requisito aziendale che non consente l'uso del ruolo collaborazione, contattare il supporto tecnico di Azure per ulteriori informazioni. |

## <a name="error-when-deleting-nic-associated-with-azure-database-migration-service"></a>Errore durante l'eliminazione della scheda di rete associata al servizio migrazione del database di Azure

Quando si tenta di eliminare una scheda di interfaccia di rete associata al servizio migrazione del database di Azure, il tentativo di eliminazione ha esito negativo con questo errore:

* **Errore**: non è possibile eliminare la scheda di interfaccia di rete associata al servizio migrazione del database di Azure perché il servizio DMS usa la scheda di interfaccia di rete

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Questo problema si verifica quando l'istanza del servizio migrazione del database di Azure può essere ancora presente e utilizza la scheda di interfaccia di rete. <br><br><br><br><br><br><br><br> | Per eliminare questa scheda di interfaccia di rete, eliminare l'istanza del servizio DMS che elimina automaticamente la scheda di interfaccia di rete utilizzata dal servizio.<br><br> **Importante**: assicurarsi che l'istanza del servizio migrazione del database di Azure in fase di eliminazione non abbia attività in esecuzione.<br><br> Dopo l'eliminazione di tutti i progetti e le attività associati all'istanza del servizio migrazione del database di Azure, è possibile eliminare l'istanza del servizio. La scheda di interfaccia di rete usata dall'istanza del servizio viene pulita automaticamente come parte dell'eliminazione del servizio. |

## <a name="connection-error-when-using-expressroute"></a>Errore di connessione quando si usa ExpressRoute

Quando si cerca di connettersi all'origine nella procedura guidata del progetto del servizio Migrazione del database di Azure, la connessione ha esito negativo dopo un timeout prolungato se l'origine usa ExpressRoute per la connettività.

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Quando si usa [ExpressRoute](https://azure.microsoft.com/services/expressroute/), il servizio migrazione del database di Azure [richiede](./tutorial-sql-server-azure-sql-online.md) il provisioning di tre endpoint di servizio nella subnet della rete virtuale associata al servizio:<br> --Endpoint del bus di servizio<br> --Endpoint di archiviazione<br> --Endpoint database di destinazione (ad esempio, endpoint SQL, endpoint Cosmos DB)<br><br><br><br><br> | [Abilitare](./tutorial-sql-server-azure-sql-online.md) gli endpoint di servizio necessari per la connettività ExpressRoute tra l'origine e il servizio migrazione del database di Azure. <br><br><br><br><br><br><br><br> |

## <a name="lock-wait-timeout-error-when-migrating-a-mysql-database-to-azure-db-for-mysql"></a>Errore di timeout di attesa blocco durante la migrazione di un database MySQL nel database di Azure per MySQL

Quando si esegue la migrazione di un database MySQL a un'istanza di database di Azure per MySQL tramite il servizio migrazione del database di Azure, la migrazione ha esito negativo con un errore di timeout di attesa blocco

* **Errore**: errore di migrazione del database-non è stato possibile caricare il file. Impossibile avviare il processo di caricamento per il file ' n'RetCode: SQL_ERROR SQLSTATE: HY000 NativeError: 1205 messaggio: [MySQL] [driver ODBC] [mysqld] timeout di attesa di blocco superato; prova a riavviare la transazione

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Questo errore si verifica quando la migrazione non riesce a causa del timeout di attesa di blocco durante la migrazione. | Provare ad aumentare il valore del parametro Server **' innodb_lock_wait_timeout '**. Il valore massimo consentito è 1073741824. |

## <a name="error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance"></a>Errore durante la connessione all'origine SQL Server quando si utilizza una porta dinamica o un'istanza denominata

Quando si tenta di connettere il servizio migrazione del database di Azure a SQL Server origine eseguita in un'istanza denominata o in una porta dinamica, la connessione ha esito negativo con questo errore:

* **Errore**:-1-connessione SQL non riuscita. Si è verificato un errore di rete o specifico dell'istanza mentre veniva stabilita la connessione a SQL Server. Il server non è stato trovato o non è accessibile. Verificare che il nome dell'istanza sia corretto e che il server sia configurato in modo da consentire connessioni remote. (provider: interfacce di rete SQL, errore: 26 - Errore nell'individuazione del server/dell'istanza specificata)

| Causa         | Risoluzione    |
| ------------- | ------------- |
| Questo problema si verifica quando l'istanza di SQL Server di origine a cui il servizio migrazione del database di Azure tenta di connettersi ha una porta dinamica o usa un'istanza denominata. Il servizio SQL Server Browser è in ascolto sulla porta UDP 1434 per le connessioni in ingresso a un'istanza denominata o quando si utilizza una porta dinamica. La porta dinamica può variare ogni volta che SQL Server servizio viene riavviato. È possibile controllare la porta dinamica assegnata a un'istanza tramite la configurazione di rete in Gestione configurazione SQL Server.<br><br><br> |Verificare che il servizio migrazione del database di Azure sia in grado di connettersi al servizio di SQL Server Browser di origine sulla porta UDP 1434 e l'istanza di SQL Server tramite la porta TCP assegnata in modo dinamico, come applicabile. |

## <a name="additional-known-issues"></a>Problemi noti aggiuntivi

* [Problemi noti/limitazioni della migrazione con migrazioni online al database SQL di Azure](./known-issues-azure-sql-online.md)
* [Problemi noti/limitazioni della migrazione con migrazioni online al database di Azure per MySQL](./known-issues-azure-mysql-online.md)
* [Problemi noti/limitazioni della migrazione con migrazioni online al database di Azure per PostgreSQL](./known-issues-azure-postgresql-online.md)

## <a name="next-steps"></a>Passaggi successivi

* Vedere l'articolo [PowerShell del servizio migrazione del database di Azure](/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration).
* Vedere l'articolo [come configurare i parametri del server in database di Azure per MySQL usando il portale di Azure](../mysql/howto-server-parameters.md).
* Vedere l'articolo [Panoramica dei prerequisiti per l'uso del servizio migrazione del database di Azure](./pre-reqs.md).
* Vedere le [domande frequenti sull'uso del servizio migrazione del database di Azure](./faq.md).