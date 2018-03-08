---
title: Progettare il primo database SQL di Azure | Microsoft Docs
description: Informazioni per la progettazione del primo database SQL di Azure nel portale di Azure e con SQL Server Management Studio.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop databases
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 01/29/2018
ms.author: carlrab
ms.openlocfilehash: 5c5402c89091b84cd3b49815d892c9fa0d59e22a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="design-your-first-azure-sql-database"></a>Progettare il primo database SQL di Azure

Il database SQL di Azure è un database relazionale distribuito come servizio, ovvero DBaaS, in Microsoft Cloud (Azure). Questa esercitazione illustra come usare il portale di Azure e [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) per le operazioni seguenti: 

> [!div class="checklist"]
> * Creare un database nel portale di Azure
> * Impostare una regola di firewall a livello di server nel portale di Azure
> * Connettersi al database con SSMS
> * Creare tabelle con SQL Server Management Studio
> * Eseguire il caricamento bulk dei dati con BCP
> * Eseguire una query su quei dati con SQL Server Management Studio
> * Ripristinare il database a un [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore) con il portale di Azure

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, accertarsi di avere installato:
- La versione più recente di [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS).
- La versione più recente di [BCP e SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433).

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Creare un database SQL vuoto

Un database SQL di Azure viene creato con un set definito di [risorse di calcolo e di archiviazione](sql-database-service-tiers.md). Il database viene creato in un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) e in un [server logico di database SQL di Azure](sql-database-features.md). 

Per creare un database SQL vuoto, attenersi alla procedura seguente. 

1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Database** dalla pagina **Nuovo**, quindi selezionare **Crea** in **Database SQL** nella pagina **Nuovo**.

   ![creare database vuoto](./media/sql-database-design-first-database/create-empty-database.png)

3. Compilare il modulo Database SQL con le informazioni seguenti, come illustrato nell'immagine precedente:   

   | Impostazione       | Valore consigliato | DESCRIZIONE | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Database name** (Nome database) | mySampleDatabase | Per i nomi di database validi, vedere [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificatori di database). | 
   | **Sottoscrizione** | Sottoscrizione in uso  | Per informazioni dettagliate sulle sottoscrizioni, vedere [Subscriptions](https://account.windowsazure.com/Subscriptions) (Sottoscrizioni). |
   | **Gruppo di risorse** | myResourceGroup | Per i nomi di gruppi di risorse validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). |
   | **Select source** (Seleziona origine) | Database vuoto | Indica che deve essere creato un database vuoto. |

4. Fare clic su **Server** per creare e configurare un nuovo server per il nuovo database. Compilare il **modulo del nuovo server** con le informazioni seguenti: 

   | Impostazione       | Valore consigliato | DESCRIZIONE | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Server name** (Nome server) | Qualsiasi nome globalmente univoco | Per i nomi di server validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). | 
   | **Accesso amministratore server** | Qualsiasi nome valido | Per i nomi di accesso validi, vedere [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificatori di database).|
   | **Password** | Qualsiasi password valida | La password deve contenere almeno otto caratteri delle tre categorie seguenti: maiuscole, minuscole, numeri e caratteri non alfanumerici. |
   | **Posizione** | Qualsiasi località valida | Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |

   ![Creare il server di database](./media/sql-database-design-first-database/create-database-server.png)

5. Fare clic su **Seleziona**.

6. Fare clic su **Piano tariffario** per specificare il livello di servizio, il numero di DTU e la quantità di risorse di archiviazione. Esplorare le opzioni relative al numero di DTU e di risorse di archiviazione disponibile per ogni livello di servizio. 

7. Per questa esercitazione selezionare il livello di servizio **Standard** e quindi usare il dispositivo di scorrimento per selezionare **100 DTU (S3)** e **400** GB di archiviazione.

   ![Creare il database s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Accettare le condizioni dell'anteprima per usare l'opzione **Spazio di archiviazione aggiuntivo**. 

   > [!IMPORTANT]
   > \* Le dimensioni di archiviazione superiori alla quantità di risorse di archiviazione incluse sono disponibili in anteprima e vengono applicati costi aggiuntivi. Per ulteriori informazioni, vedere [Database SQL Prezzi](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* Nel livello Premium sono attualmente disponibili più di 1 TB di risorse di archiviazione nelle aree seguenti: Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, Stati Uniti centrali, Francia centrale, Germania centrale, Giappone orientale, Giappone occidentale, Corea centrale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, Regno Unito meridionale, Regno Unito orientale, Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia ed Europa occidentale. Vedere [Limitazioni correnti per P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. Dopo la selezione del livello di servizio, del numero di DTU e della quantità di risorse di archiviazione, fare clic su **Applica**.  

10. Selezionare **regole di confronto** per il database vuoto. Per questa esercitazione usare il valore predefinito. Per altre informazioni sulle regole di confronto, vedere [Collations](https://docs.microsoft.com/sql/t-sql/statements/collations) (Regole di confronto)

11. Dopo aver completato il modulo del database SQL, fare clic su **Crea** per effettuare il provisioning del database. Il provisioning richiede alcuni minuti. 

12. Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.
    
     ![notifica](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Creare una regola del firewall a livello di server

Il servizio di database SQL crea un firewall a livello di server che impedisce alle applicazioni e agli strumenti esterni di connettersi al server o ai database sul server a meno che non venga creata una regola del firewall per aprire il firewall per indirizzi IP specifici. Seguire questi passaggi per creare una [regola del firewall a livello di server di database SQL](sql-database-firewall-configure.md) per l'indirizzo IP del client e abilitare la connettività esterna tramite il firewall del database SQL solo per il proprio indirizzo IP. 

> [!NOTE]
> Il database SQL comunica attraverso la porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non è possibile connettersi al server del database SQL di Azure, a meno che il reparto IT non apra la porta 1433.
>

1. Al termine della distribuzione, scegliere **Database SQL** dal menu a sinistra e fare clic su **mySampleDatabase** nella pagina **Database SQL**. Si apre la pagina di panoramica per il database che visualizza il nome completo del server, ad esempio **mynewserver-20170824.database.windows.net**, e offre altre opzioni di configurazione. 

2. Copiare il nome completo del server per connettersi al server e ai relativi database nelle guide introduttive successive. 

   ![Nome del server](./media/sql-database-get-started-portal/server-name.png) 

3. Fare clic su **Imposta firewall server** sulla barra degli strumenti. Si apre la pagina **Impostazioni del firewall** per il server del database SQL. 

   ![Regola del firewall del server](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP corrente a una nuova regola del firewall. Una regola del firewall può aprire la porta 1433 per un indirizzo IP singolo o un intervallo di indirizzi IP.

5. Fare clic su **Save**. Viene creata una regola del firewall a livello di server per l'indirizzo IP corrente, che apre la porta 1433 nel server logico.

6. Fare clic su **OK** e quindi chiudere la pagina **Impostazioni del firewall**.

È ora possibile connettersi al server del database SQL e ai relativi database usando SQL Server Management Studio o un altro strumento di propria scelta da questo indirizzo IP, con l'account amministratore del server creato in precedenza.

> [!IMPORTANT]
> Per impostazione predefinita, l'accesso attraverso il firewall del database SQL è abilitato per tutti i servizi di Azure. Selezionando **NO** in questa pagina permette di disabilitare tutti i servizi di Azure.

## <a name="sql-server-connection-information"></a>Informazioni di connessione SQL Server

Ottenere il nome completo del server per il server del database SQL di Azure nel portale di Azure. Usare il nome completo del server per connettersi al server tramite SQL Server Management Studio.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nel riquadro **Informazioni di base** della pagina del portale di Azure per il database individuare e quindi copiare il **Nome server**.

   ![informazioni di connessione](./media/sql-database-get-started-portal/server-name.png)

## <a name="connect-to-the-database-with-ssms"></a>Connettersi al database con SSMS

Usare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) per stabilire una connessione al server del database SQL di Azure.

1. Aprire SQL Server Management Studio.

2. Nella finestra di dialogo **Connetti al server** immettere le informazioni seguenti:

   | Impostazione       | Valore consigliato | DESCRIZIONE | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Tipo di server | Motore di database | Questo valore è obbligatorio |
   | Nome server | Nome completo del server | Il nome sarà simile a: **mynewserver20170824.database.windows.net**. |
   | Authentication | Autenticazione di SQL Server | L'autenticazione SQL è il solo tipo di autenticazione configurato in questa esercitazione. |
   | Login | Account amministratore del server | Si tratta dell'account specificato quando è stato creato il server. |
   | Password | Password per l'account amministratore del server | Si tratta della password specificata quando è stato creato il server. |

   ![connetti al server](./media/sql-database-connect-query-ssms/connect.png)

3. Fare clic su **Opzioni** nella finestra di dialogo **Connetti al server**. Nella sezione **Connetti al database** immettere **mySampleDatabase** per connettersi a tale database.

   ![connettersi al database nel server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Fare clic su **Connetti**. La finestra Esplora oggetti viene visualizzata in SSMS. 

5. In Esplora oggetti espandere **Database** e quindi espandere **mySampleDatabase** per visualizzare gli oggetti disponibili nel database di esempio.

   ![oggetti di database](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>Creare tabelle nel database 

Creare uno schema di database con quattro tabelle che modellano un sistema di gestione degli studenti per le università tramite [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference):

- Person
- Corso
- Studente
- Accreditare quel modello come un sistema di gestione degli studenti per le università

Nel diagramma seguente viene illustrato come queste tabelle sono correlate tra loro. Alcune di queste tabelle fanno riferimento a delle colonne in altre tabelle. Ad esempio, la tabella Student fa riferimento alla colonna **PersonId** della tabella **Person**. Studiare il diagramma per comprendere come sono correlate tra loro le tabelle in questa esercitazione. Per un esame approfondito su come creare tabelle di database efficaci, vedere [Create effective database tables](https://msdn.microsoft.com/library/cc505842.aspx) (Creare tabelle di database efficaci). Per informazioni sulla scelta dei tipi di dati, vedere [Tipi di dati](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> È anche possibile usare la [Progettazione delle tabelle in SQL Server Management Studio](https://msdn.microsoft.com/library/hh272695.aspx) per creare e progettare le tabelle. 

![Relazioni tra tabelle](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. In Esplora oggetti fare clic con il pulsante destro del mouse su **mySampleDatabase** e scegliere **Nuova query**. Viene visualizzata una finestra di query vuota, connessa al database.

2. Nella finestra di query, eseguire la query seguente per creare quattro tabelle nel database: 

   ```sql 
   -- Create Person table

   CREATE TABLE Person
   (
   PersonId   INT IDENTITY PRIMARY KEY,
   FirstName   NVARCHAR(128) NOT NULL,
   MiddelInitial NVARCHAR(10),
   LastName   NVARCHAR(128) NOT NULL,
   DateOfBirth   DATE NOT NULL
   )
   
   -- Create Student table
 
   CREATE TABLE Student
   (
   StudentId INT IDENTITY PRIMARY KEY,
   PersonId  INT REFERENCES Person (PersonId),
   Email   NVARCHAR(256)
   )
   
   -- Create Course table
 
   CREATE TABLE Course
   (
   CourseId  INT IDENTITY PRIMARY KEY,
   Name   NVARCHAR(50) NOT NULL,
   Teacher   NVARCHAR(256) NOT NULL
   ) 

   -- Create Credit table
 
   CREATE TABLE Credit
   (
   StudentId   INT REFERENCES Student (StudentId),
   CourseId   INT REFERENCES Course (CourseId),
   Grade   DECIMAL(5,2) CHECK (Grade <= 100.00),
   Attempt   TINYINT,
   CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
   (
   StudentId, CourseId, Grade, Attempt
   )
   )
   ```

   ![Creare tabelle](./media/sql-database-design-first-database/create-tables.png)

3. Espandere il nodo "tabelle" in SQL Server Management Studio Object Explorer per visualizzare le tabelle create.

   ![tabelle create con SQL Server Management Studio](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Caricare i dati nelle tabelle

1. Creare una cartella denominata **SampleTableData** nella cartella download per archiviare i dati di esempio per il database. 

2. Fare doppio clic sui seguenti collegamenti e salvarli nella cartella **SampleTableData**. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Aprire una finestra del prompt dei comandi e passare alla cartella SampleTableData.

4. Eseguire i comandi seguenti per inserire dei dati di esempio nelle tabelle sostituendo i valori per **ServerName**, **DatabaseName**, **UserName** e **Password** con i valori dell'ambiente.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

A questo punto, sono stati caricati i dati di esempio nelle tabelle create in precedenza.

## <a name="query-data"></a>Eseguire query sui dati

Eseguire le query seguenti per recuperare informazioni dalle tabelle del database. Vedere [Writing SQL Queries](https://technet.microsoft.com/library/bb264565.aspx) (Scrittura di query SQL) per altre informazioni sulla scrittura di query SQL. La prima query unisce tutte e quattro le tabelle per trovare tutti gli studenti a cui ha insegnato "Dominick Pope" che hanno un livello superiore al 75% nella sua classe. La seconda query unisce tutte e quattro le tabelle e trova tutti i corsi in cui si è registrato "Noe Coleman".

1. In una finestra di query di SQL Server Management Studio, eseguire la query seguente:

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

   SELECT  person.FirstName,
   person.LastName,
   course.Name,
   credit.Grade
   FROM  Person AS person
   INNER JOIN Student AS student ON person.PersonId = student.PersonId
   INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
   INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope' 
   AND Grade > 75
   ```

2. In una finestra di query di SQL Server Management Studio, eseguire la query seguente:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

   SELECT  course.Name,
   course.Teacher,
   credit.Grade
   FROM  Course AS course
   INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
   INNER JOIN Student AS student ON student.StudentId = credit.StudentId
   INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
   AND person.LastName = 'Coleman'
   ```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Ripristinare un database a un momento precedente

Si supponga di aver eliminato accidentalmente una tabella. Si tratta di un elemento che non è facile da ripristinare. Il Database SQL di Azure consente di tornare in qualsiasi punto degli ultimi 35 giorni e di ripristinare questo punto nel tempo in un nuovo database. È possibile usare questo database per ripristinare i dati eliminati. La procedura seguente consente di ripristinare il database di esempio in un punto precedente all'aggiunta delle tabelle.

1. Nella pagina Database SQL del database fare clic su **Ripristina** sulla barra degli strumenti. Si apre la pagina **Ripristina** .

   ![ripristinare](./media/sql-database-design-first-database/restore.png)

2. Compilare il modulo **Ripristina** con le informazioni obbligatorie:
    * Nome database: specificare un nome per il database 
    * Temporizzato: Selezionare la scheda **Temporizzato** del modulo Ripristino 
    * Punto di ripristino: selezionare un punto nel tempo precedente alla modifica del database
    * Server di destinazione: non è possibile modificare questo valore quando si ripristina un database 
    * Pool di database elastici: selezionare **Nessuno**  
    * Piano tariffario: selezionare **20 DTU** e **40 GB** di memoria.

   ![punto di ripristino](./media/sql-database-design-first-database/restore-point.png)

3. Fare clic su **OK** per ripristinare il database da ripristinare [ in un punto nel tempo ](sql-database-recovery-using-backups.md#point-in-time-restore) precedente all'aggiunta delle tabelle. Il ripristino di un database in un altro punto nel tempo crea un duplicato del database nello stesso server del database originale nel punto nel tempo specificato, a condizione che si trovi nell'ambito del periodo di conservazione per il [livello di servizio](sql-database-service-tiers.md) applicato.

## <a name="next-steps"></a>Passaggi successivi 
Questa esercitazione ha illustrato le attività di base che è possibile eseguire con i database, come creare database e tabelle, caricare dati, eseguire query sui dati e ripristinare un database a un momento precedente. Si è appreso come:
> [!div class="checklist"]
> * Creare un database
> * Configurare una regola del firewall
> * Connettersi al database con [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
> * Creare tabelle
> * Eseguire il caricamento bulk dei dati
> * Eseguire query sui dati
> * Ripristinare il database a un momento precedente usando le funzionalità di [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore) del database SQL

Per informazioni sulla progettazione di un database con Visual Studio e C#, passare all'esercitazione successiva.

> [!div class="nextstepaction"]
>[Progettare un database SQL di Azure e connettersi con C# e ADO.NET](sql-database-design-first-database-csharp.md)
