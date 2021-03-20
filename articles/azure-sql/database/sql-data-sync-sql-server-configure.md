---
title: Configurare la sincronizzazione dati SQL
description: Questa esercitazione illustra come configurare la sincronizzazione dati SQL per Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: d6b5bab1c1b6c8db4821fdf84728eb66eb55b899
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98882230"
---
# <a name="tutorial-set-up-sql-data-sync-between-databases-in-azure-sql-database-and-sql-server"></a>Esercitazione: Configurare la sincronizzazione dati SQL tra i database nel database SQL di Azure e in SQL Server
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In questa esercitazione si imparerà a configurare la sincronizzazione dati SQL creando un gruppo di sincronizzazione che contiene sia istanze del database SQL di Azure che istanze di SQL Server. Il gruppo di sincronizzazione ha una configurazione personalizzata ed esegue la sincronizzazione in base alla pianificazione impostata dall'utente.

Questa esercitazione presuppone un certo livello di esperienza con il database SQL di Azure e SQL Server.

Per una panoramica della sincronizzazione dati SQL, vedere [Sincronizzare i dati tra più database cloud e locali con la sincronizzazione dati SQL](sql-data-sync-data-sql-server-sql-database.md).

Per esempi di PowerShell su come configurare la sincronizzazione dati SQL, vedere [Come eseguire la sincronizzazione tra database nel database SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md) o [tra database nel database SQL di Azure e in SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)

> [!IMPORTANT]
> Al momento, la sincronizzazione dati SQL **non** supporta Istanza gestita di SQL di Azure.

## <a name="create-sync-group"></a>Creare un gruppo di sincronizzazione

1. Passare al [portale di Azure](https://portal.azure.com) per trovare il database nel database SQL. Cercare e selezionare **Database SQL**.

    ![Cercare i database, portale di Microsoft Azure](./media/sql-data-sync-sql-server-configure/search-for-sql-databases.png)

1. Selezionare il database che si vuole usare come database hub per la sincronizzazione dati.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/select-sql-database.png" alt-text = "Select from the database list, Microsoft Azure portal":::

    > [!NOTE]
    > Il database hub è un endpoint centrale della topologia di sincronizzazione, in cui per un gruppo di sincronizzazione sono presenti più endpoint del database. Tutti gli altri database membri con endpoint nel gruppo di sincronizzazione eseguono la sincronizzazione con il database hub.

1. Dal menu **Database SQL** per il database selezionato scegliere **Sincronizza con altri database**.

    :::image type="content" source="./media/sql-data-sync-sql-server-configure/sync-to-other-databases.png" alt-text = "Sync to other databases, Microsoft Azure portal":::

1. Nella pagina **Sincronizza con altri database** selezionare **Nuovo gruppo di sincronizzazione**. Verrà aperta la pagina **Nuovo gruppo di sincronizzazione** con il primo passaggio di **Crea gruppo di sincronizzazione**.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/new-sync-group-private-link.png" alt-text = "Set up new sync group with private link":::

   Nella pagina **Crea gruppo di sincronizzazione dati** cambiare le impostazioni seguenti:

   | Impostazione                        | Descrizione |
   | ------------------------------ | ------------------------------------------------- |
   | **Nome gruppo di sincronizzazione** | Immettere un nome per il nuovo gruppo di sincronizzazione. Questo nome è distinto dal nome del database stesso. |
   | **Database dei metadati di sincronizzazione** | Scegliere di creare un database (scelta consigliata) o usare un database esistente.<br/><br/>Se si sceglie **Nuovo database**, selezionare **Crea nuovo database**. Nella pagina **Database SQL**, quindi, specificare un nome per il nuovo database, configurarlo e selezionare **OK**.<br/><br/>Se si sceglie **Usa database esistente**, selezionare il database dall'elenco. |
   | **Sincronizzazione automatica** | Selezionare **Sì** o **No**.<br/><br/>Se si sceglie **Sì**, immettere un numero e selezionare **Secondi**, **Minuti**, **Ore** o **Giorni** nella sezione **Frequenza sincronizzazione**.<br/> La prima sincronizzazione inizia al termine dell'intervallo di tempo selezionato dopo il salvataggio della configurazione.|
   | **Risoluzione dei conflitti** | Selezionare **Priorità hub** o **Priorità membro**.<br/><br/>**Priorità hub** significa che, quando si verifica un conflitto, i dati nel database hub sovrascrivono i dati in conflitto nel database membro.<br/><br/>**Priorità membro** significa che, quando si verifica un conflitto, i dati nel database membro sovrascrivono i dati in conflitto nel database hub. |
   | **Usare il collegamento privato** | Scegliere un endpoint privato gestito dal servizio per stabilire una connessione sicura tra il servizio di sincronizzazione e il database hub. |

   > [!NOTE]
   > Microsoft consiglia di creare un nuovo database vuoto da usare come **Database dei metadati di sincronizzazione**. La sincronizzazione dati crea tabelle in questo database ed esegue un carico di lavoro frequente. Questo database viene condiviso come **database dei metadati di sincronizzazione** per tutti i gruppi di sincronizzazione in un'area e in una sottoscrizione selezionate. Non è possibile modificare il database o il nome di questo senza rimuovere tutti i gruppi e gli agenti di sincronizzazione nell'area.

   Selezionare **OK** e attendere che il gruppo di sincronizzazione venga creato e distribuito.
   
1. Nella pagina **Nuovo gruppo di sincronizzazione**, se è stata selezionata l'opzione **Usa collegamento privato**, sarà necessario approvare la connessione all'endpoint privato. Il collegamento nel messaggio di informazioni consente di passare all'esperienza di connessione all'endpoint privato in cui è possibile approvare la connessione. 

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/approve-private-link.png" alt-text = "Approve private link":::

## <a name="add-sync-members"></a>Aggiungere membri di sincronizzazione

Al termine della creazione e della distribuzione del nuovo gruppo di sincronizzazione, nella pagina **Nuovo gruppo di sincronizzazione** viene evidenziato il passaggio 2, **Aggiungi membri di sincronizzazione**.

Nella sezione **Database hub** immettere le credenziali esistenti per il server in cui si trova il database hub. Non immettere *nuove* credenziali in questa sezione.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo.png" alt-text = "Enter existing credentials for the hub database server":::

### <a name="to-add-a-database-in-azure-sql-database"></a>Per aggiungere un database nel database SQL di Azure

Nella sezione **Database membro** aggiungere facoltativamente un database nel database SQL di Azure al gruppo di sincronizzazione selezionando **Aggiungi un database SQL di Azure**. Verrà aperta la pagina **Configura database SQL di Azure**.
  
   :::image type="content" source="./media/sql-data-sync-sql-server-configure/step-two-configure.png" alt-text = "Add a database to the sync group":::
   
  Nella pagina **Configura database SQL di Azure** cambiare le impostazioni seguenti:

  | Impostazione                       | Descrizione |
  | ----------------------------- | ------------------------------------------------- |
  | **Nome membro di sincronizzazione** | Specificare un nome per il nuovo membro di sincronizzazione. Questo nome è distinto dal nome del database. |
  | **Sottoscrizione** | Selezionare la sottoscrizione di Azure associata ai fini della fatturazione. |
  | **Azure SQL Server** | Selezionare il server esistente. |
  | **Database SQL di Azure** | Selezionare il database esistente nel database SQL. |
  | **Direzioni sincronizzazione** | Selezionare **Sincronizzazione bidirezionale**, **Verso l'hub** o **Dall'hub**. |
  | **Nome utente** e **Password** | Immettere le credenziali esistenti per il server in cui si trova il database membro. Non immettere *nuove* credenziali in questa sezione. |
  | **Usare il collegamento privato** | Scegliere un endpoint privato gestito dal servizio per stabilire una connessione sicura tra il servizio di sincronizzazione e il database membro. |

  Selezionare **OK** e attendere che il nuovo membro di sincronizzazione venga creato e distribuito.

<a name="add-on-prem"></a>

### <a name="to-add-a-sql-server-database"></a>Per aggiungere un nuovo database di SQL Server

Nella sezione **Database membro** aggiungere facoltativamente un database di SQL Server al gruppo di sincronizzazione selezionando **Aggiungi un database locale**. Verrà visualizzata la pagina **Configura database locale**, in cui è possibile eseguire le operazioni seguenti:

1. Selezionare **Scegliere il gateway dell'agente di sincronizzazione**. Verrà aperta la pagina **Seleziona agente di sincronizzazione**.

   :::image type="content" source="./media/sql-data-sync-sql-server-configure/steptwo-agent.png" alt-text = "Creating a sync agent":::

1. Nella pagina **Seleziona agente di sincronizzazione** scegliere se usare un agente esistente o crearne uno.

   Se si sceglie **Agenti esistenti**, selezionare l'agente esistente dall'elenco.

   Se si sceglie **Consente di creare un nuovo agente**, eseguire le operazioni seguenti:

   1. Scaricare l'agente di sincronizzazione dati dal collegamento disponibile e installarlo nel computer in cui si trova SQL Server. È anche possibile scaricare l'agente direttamente da [SQL Azure Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

      > [!IMPORTANT]
      > È necessario aprire la porta TCP 1433 in uscita nel firewall per consentire all'agente client di comunicare con il server.

   1. Immettere un nome per l'agente.

   1. Selezionare **Crea e genera chiave** e copiare la chiave dell'agente negli Appunti.

   1. Selezionare **OK** per chiudere la pagina **Seleziona agente di sincronizzazione**.

1. Nel computer SQL Server individuare ed eseguire l'app dell'agente di sincronizzazione client.

   ![App dell'agente client di sincronizzazione dati](./media/sql-data-sync-sql-server-configure/datasync-preview-clientagent.png)

    1. Nell'app dell'agente di sincronizzazione selezionare **Submit Agent Key** (Invia chiave agente). Verrà aperta la finestra di dialogo **Sync Metadata Database Configuration** (Configurazione del database dei metadati di sincronizzazione).

    1. Nella finestra di dialogo **Sync Metadata Database Configuration** (Configurazione del database dei metadati di sincronizzazione) incollare la chiave dell'agente copiata dal portale di Azure. Specificare anche le credenziali esistenti per il server in cui si trova il database dei metadati. Se è stato creato un database dei metadati, tale database si trova nello stesso server del database hub. Selezionare **OK** e attendere che la configurazione venga completata.

        ![Immettere la chiave dell'agente e le credenziali del server](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-enterkey.png)

        > [!NOTE]
        > Se viene visualizzato un errore del firewall, creare una regola del firewall in Azure per consentire il traffico in ingresso dal computer SQL Server. È possibile creare manualmente la regola nel portale o in SQL Server Management Studio (SSMS). In SSMS connettersi al database hub in Azure immettendone il nome nel formato <nome_database_hub>.database.windows.net.

    1. Selezionare **Registra** per registrare un database SQL Server con l'agente. Verrà aperta la finestra di dialogo **Configurazione di SQL Server**.

        ![Aggiungere e configurare un database di SQL Server](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-adddb.png)

    1. Nella finestra di dialogo **Configurazione di SQL Server** scegliere di effettuare la connessione tramite l'autenticazione di SQL Server o l'autenticazione di Windows. Se si sceglie l'autenticazione di SQL Server, immettere le credenziali esistenti. Specificare il nome dell'istanza di SQL Server e il nome del database che si vuole sincronizzare e selezionare **Test connessione** per testare le impostazioni. Selezionare quindi **Salva**. Il database registrato verrà visualizzato nell'elenco.

        ![Il database di SQL Server è ora registrato](./media/sql-data-sync-sql-server-configure/datasync-preview-agent-dbadded.png)

    1. Chiudere l'app dell'agente di sincronizzazione client.

1. Nella pagina **Configura database locale** del portale selezionare **Selezionare il database**.

1. Nella pagina **Seleziona database**, nel campo **Nome membro di sincronizzazione**, specificare un nome per il nuovo membro di sincronizzazione. Questo nome è distinto dal nome del database stesso. Selezionare il database nell'elenco. Nel campo **Direzioni sincronizzazione** selezionare **Sincronizzazione bidirezionale**, **Verso l'hub** o **Dall'hub**.

    ![Selezionare il database locale](./media/sql-data-sync-sql-server-configure/datasync-preview-selectdb.png)

1. Selezionare **OK** per chiudere la finestra **Seleziona database**. Selezionare quindi **OK** per chiudere la pagina **Configura database locale** e attendere che il nuovo membro di sincronizzazione venga creato e distribuito. Selezionare infine **OK** per chiudere la pagina **Selezionare i membri di sincronizzazione**.

> [!NOTE]
> Per connettersi alla sincronizzazione dati SQL e all'agente locale, aggiungere il proprio nome utente al ruolo *DataSync_Executor*. Sincronizzazione dati crea questo ruolo nell'istanza di SQL Server.

## <a name="configure-sync-group"></a>Configurare il gruppo di sincronizzazione

Dopo la creazione e la distribuzione dei nuovi membri del gruppo di sincronizzazione, nella pagina **Nuovo gruppo di sincronizzazione** viene evidenziato il passaggio 3 **Configura gruppo di sincronizzazione**.

![Impostazioni del passaggio 3](./media/sql-data-sync-sql-server-configure/stepthree.png)

1. Nella pagina **Tabelle** selezionare un database dall'elenco dei membri del gruppo di sincronizzazione e selezionare **Aggiorna schema**.

1. Selezionare dall'elenco le tabelle da sincronizzare. Per impostazione predefinita, tutte le colonne sono selezionate. Disabilitare quindi le caselle di controllo corrispondenti alle colonne che non si vogliono sincronizzare. Assicurarsi di lasciare selezionata la colonna chiave primaria.

1. Selezionare **Salva**.

1. Per impostazione predefinita, i database vengono sincronizzati solo manualmente o tramite pianificazione. Per eseguire una sincronizzazione manuale, passare al database nel database SQL nel portale di Azure, selezionare **Sincronizza con altri database** e selezionare il gruppo di sincronizzazione. Verrà aperta la pagina **Sincronizzazione dati**. Selezionare **Sincronizza**.

    ![Sincronizzazione manuale](./media/sql-data-sync-sql-server-configure/datasync-sync.png)

## <a name="faq"></a>Domande frequenti

**La sincronizzazione dati SQL crea tabelle complete?**

Se le tabelle dello schema di sincronizzazione non sono presenti nel database di destinazione, la sincronizzazione dati SQL le crea usando le colonne selezionate dall'utente. Questa operazione tuttavia non ha come risultato uno schema pienamente fedele per i motivi seguenti:

- Nella tabella di destinazione vengono create solo le colonne selezionate dall'utente. Le colonne non selezionate vengono ignorate.
- Nella tabella di destinazione vengono creati solo gli indici di colonna selezionati. Per le colonne non selezionate, tali indici vengono ignorati.
- Gli indici nelle colonne di tipo XML non vengono creati.
- I vincoli CHECK non vengono creati.
- I trigger esistenti nelle tabelle di origine non vengono creati.
- Le viste e le stored procedure non vengono create.

A causa di questi limiti, è consigliabile eseguire le operazioni seguenti:

- Per gli ambienti di produzione, creare manualmente uno schema pienamente fedele.
- Quando si eseguono esperimenti con il servizio, usare la funzionalità di provisioning automatico.

**Perché sono visualizzate tabelle non create dall'utente?**

La sincronizzazione dati crea tabelle aggiuntive nel database utente per il rilevamento delle modifiche. Non eliminarle o la sincronizzazione dati cesserà di funzionare.

**I dati risultano convergenti dopo una sincronizzazione?**

Non necessariamente. Si supponga che in un gruppo di sincronizzazione con un hub e tre spoke (A, B e C), le sincronizzazioni siano da hub ad A, da hub a B e da hub a C. Se si apporta una modifica al database A *dopo* la sincronizzazione da hub ad A, tale modifica viene scritta nei database B e C solo dopo l'attività di sincronizzazione successiva.

**Come si ottengono le modifiche dello schema in un gruppo di sincronizzazione?**

Apportare e propagare tutte le modifiche allo schema manualmente.

1. Replicare manualmente le modifiche dello schema nell'hub e in tutti i membri di sincronizzazione.
1. Aggiornare lo schema di sincronizzazione.

Per aggiungere nuove tabelle e colonne:

Nuove tabelle e colonne non influiscono sulla sincronizzazione corrente e la sincronizzazione dati le ignora fino a quando non vengono aggiunte allo schema di sincronizzazione. Quando si aggiungono nuovi oggetti di database, rispettare questa sequenza:

1. Aggiungere le nuove tabelle o colonne all'hub e a tutti i membri di sincronizzazione.
1. Aggiungere le nuove tabelle o colonne allo schema di sincronizzazione.
1. Iniziare a inserire valori nelle nuove tabelle e colonne.

Per cambiare il tipo di dati di una colonna:

Quando si modifica il tipo di dati di una colonna esistente, la sincronizzazione dei dati continua a funzionare, fino a quando i nuovi valori sono conformi al tipo di dati originale definito nello schema di sincronizzazione. Se ad esempio si modifica il tipo nel database di origine da **int** a **bigint**, la sincronizzazione dati continua a funzionare fino a quando non si inserisce un valore troppo grande per il tipo di dati **int**. Per completare la modifica, replicare manualmente la modifica dello schema nell'hub e in tutti i membri di sincronizzazione e quindi aggiornare lo schema di sincronizzazione.

**Come si esporta e importa un database con la sincronizzazione dati?**

Dopo aver esportato un database come file con estensione *bacpac* e dopo aver importato quest'ultimo per creare un database, eseguire le operazioni seguenti per usare la sincronizzazione dati nel nuovo database:

1. Pulire gli oggetti di sincronizzazione dati e le tabelle aggiuntive nel nuovo database usando [questo script](https://github.com/vitomaz-msft/DataSyncMetadataCleanup/blob/master/Data%20Sync%20complete%20cleanup.sql). Lo script elimina dal database tutti gli oggetti di sincronizzazione dati richiesti.
1. Creare di nuovo il gruppo di sincronizzazione con il nuovo database. Se il gruppo di sincronizzazione precedente non è più necessario, eliminarlo.

**Dove è possibile trovare informazioni sull'agente client?**

Per le domande frequenti sull'agente client, vedere [Domande frequenti sull'agente](sql-data-sync-agent-overview.md#agent-faq).

**È necessario approvare manualmente il collegamento privato prima di iniziare a usarlo?**

Sì, è necessario approvare manualmente l'endpoint privato gestito dal servizio, nella pagina Connessioni a endpoint privato del portale di Azure durante la distribuzione del gruppo di sincronizzazione o usando PowerShell.

## <a name="next-steps"></a>Passaggi successivi

Complimenti. è stato creato un gruppo di sincronizzazione che include sia un'istanza di database SQL che un database SQL Server.

Per altre informazioni sulla sincronizzazione dati SQL, vedere:

- [Agente di sincronizzazione dei dati per la sincronizzazione dati SQL Azure](sql-data-sync-agent-overview.md)
- [Procedure consigliate](sql-data-sync-best-practices.md) e [Come risolvere i problemi di sincronizzazione dati SQL di Azure](sql-data-sync-troubleshoot.md)
- [Monitorare la sincronizzazione dati SQL con i log di Monitoraggio di Azure](./monitor-tune-overview.md)
- [Aggiornare lo schema di sincronizzazione con Transact-SQL](sql-data-sync-update-sync-schema.md) o [PowerShell](scripts/update-sync-schema-in-sync-group.md)

Per altre informazioni sul database SQL, vedere:

- [Panoramica del database SQL](sql-database-paas-overview.md)
- [Gestione del ciclo di vita del database](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))
