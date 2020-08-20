---
title: Gestire e monitorare SQL Server database in una macchina virtuale di Azure
description: Questo articolo descrive come gestire e monitorare SQL Server database in esecuzione in una macchina virtuale di Azure.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: ada367e94b75c30a98bedf5848b248cadfe9acc2
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88659586"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gestire e monitorare i database SQL Server di cui è stato eseguito il backup

Questo articolo descrive le attività comuni per la gestione e il monitoraggio di database SQL Server in esecuzione in una macchina virtuale (VM) di Azure e di cui viene eseguito il backup in un insieme di credenziali di servizi di ripristino di backup di Azure da parte del servizio [backup di Azure](backup-overview.md) . Si apprenderà come monitorare i processi e gli avvisi, arrestare e riprendere la protezione dei database, eseguire i processi di backup e annullare la registrazione di una macchina virtuale dai backup.

Se non sono ancora stati configurati backup per i database di SQL Server, vedere [eseguire il backup di SQL Server database in macchine virtuali di Azure](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorare i processi di backup manuali nel portale

Backup di Azure Mostra tutti i processi attivati manualmente nel portale dei **processi di backup** . I processi visualizzati in questo portale includono l'individuazione e la registrazione dei database, nonché le operazioni di backup e ripristino.

![Portale dei processi di backup](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Il portale dei **processi di backup** non Mostra i processi di backup pianificati. Usare SQL Server Management Studio per monitorare i processi di backup pianificati, come descritto nella sezione successiva.
>

Per informazioni dettagliate sugli scenari di monitoraggio, vedere [monitoraggio nella portale di Azure](backup-azure-monitoring-built-in-monitor.md) e [monitoraggio tramite](backup-azure-monitoring-use-azuremonitor.md)monitoraggio di Azure.  

## <a name="view-backup-alerts"></a>Visualizzare gli avvisi di backup

Con i backup del log eseguiti ogni 15 minuti, monitorare i processi di backup può essere faticoso. Backup di Azure semplifica il monitoraggio inviando avvisi di posta elettronica. Gli avvisi di posta elettronica sono:

- Attivati per tutti gli errori di backup.
- Consolidati a livello di database in base al codice di errore.
- Inviati solo per il primo errore di backup di un database.

Per monitorare gli avvisi di backup del database:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel dashboard dell'insieme di credenziali, selezionare **Avvisi ed eventi**.

   ![Selezionare Avvisi ed eventi](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. In **Avvisi ed eventi**, selezionare **avvisi di Backup**.

   ![Selezionare Avvisi di backup](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Interrompere la protezione di un database di SQL Server

È possibile interrompere i backup di un database di SQL Server in due modi:

- Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero.
- Arrestare tutti i processi di backup futuri e lasciare intatti i punti di ripristino.

Se si sceglie di lasciare invariati i punti di ripristino, tenere presente quanto segue:

- Tutti i punti di ripristino rimarranno invariati per sempre, tutte le eliminazioni verranno arrestate in caso di arresto della protezione con conservazione dei dati.
- Verranno addebitati i costi per l'istanza protetta e lo spazio di archiviazione utilizzato. Per altre informazioni, vedere [Prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).
- Se si elimina un'origine dati senza arrestare i backup, i nuovi backup avranno esito negativo. I punti di ripristino precedenti scadranno in base ai criteri, ma un ultimo punto di ripristino verrà sempre mantenuto fino a quando non si arrestano i backup ed eliminano i dati.

Per interrompere la protezione per un database:

1. Nel dashboard dell'insieme di credenziali, selezionare **Elementi di backup**.

2. In **tipo di gestione di backup**selezionare **SQL in macchina virtuale di Azure**.

    ![Selezionare SQL nella macchina virtuale di Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Selezionare il database per cui si desidera interrompere la protezione.

    ![Selezionare il database per interrompere la protezione](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Nel menu del database, selezionare **Interrompi backup**.

    ![Selezionare Interrompi backup](./media/backup-azure-sql-database/stop-db-button.png)

5. Nel menu **Interrompi Backup**, scegliere se mantenere o eliminare i dati. L'aggiunta di un motivo e commento è facoltativa.

    ![Mantenere o eliminare dati dal menu Interrompi backup](./media/backup-azure-sql-database/stop-backup-button.png)

6. Selezionare **Interrompi backup**.

> [!NOTE]
>
>Per ulteriori informazioni sull'opzione Elimina dati, vedere le domande frequenti seguenti:
>
>- [Se si elimina un database da un'istanza protetta in modo automatico, cosa accade ai backup?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Se si interrompe l'operazione di backup di un database autoprotetto, quale sarà il comportamento?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Riprendere la protezione per un database SQL

Quando si arresta la protezione per il database SQL, se si seleziona l'opzione **Mantieni dati di backup** , sarà possibile riprendere la protezione in un secondo momento. Se non si conservano i dati di backup, non è possibile riprendere la protezione.

Per riprendere la protezione per un database SQL:

1. Aprire l'elemento di backup e selezionare **Riprendi backup**.

    ![Selezionare Riprendi backup per riprendere la protezione del database](./media/backup-azure-sql-database/resume-backup-button.png)

2. Selezionare un criterio dal menu **Criteri di backup** e quindi selezionare **Salva**.

## <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

È possibile eseguire diversi tipi di backup su richiesta:

- Backup completo
- Backup completo solo copia
- Backup differenziale
- Backup dei log

Anche se è necessario specificare la durata di conservazione per il backup completo di sola copia, il periodo di mantenimento dati per il backup completo su richiesta verrà impostato automaticamente su 45 giorni dall'ora corrente.

Per ulteriori informazioni, vedere [SQL Server tipi di backup](backup-architecture.md#sql-server-backup-types).

## <a name="modify-policy"></a>Modifica dei criteri

Modificare i criteri per modificare la frequenza di backup o il periodo di mantenimento dati.

> [!NOTE]
> Eventuali modifiche apportate al periodo di conservazione verranno applicate in modo retrospettivo a tutti i punti di ripristino meno recenti oltre a quelli nuovi.

Nel dashboard dell'insieme di credenziali passare a **Gestisci**  >  **criteri di backup** e scegliere il criterio che si vuole modificare.

  ![Gestire i criteri di backup](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Modificare i criteri di backup](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

La modifica dei criteri ha effetto su tutti gli elementi di backup associati e attiva processi **Configura protezione** corrispondenti.

### <a name="inconsistent-policy"></a>Criteri non coerenti

In alcuni casi, un'operazione di modifica dei criteri può causare una versione non **coerente** dei criteri per alcuni elementi di backup. Ciò si verifica quando il criterio **Configura protezione** corrispondente ha esito negativo per l'elemento di backup dopo l'attivazione di un'operazione di modifica dei criteri. Nella vista dell'elemento di backup viene visualizzato quanto segue:

  ![Criteri non coerenti](./media/backup-azure-sql-database/inconsistent-policy.png)

È possibile correggere la versione dei criteri per tutti gli elementi interessati con un solo clic:

  ![Correggi criteri non coerenti](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Annullare un’istanza SQL &Server

Annullare la registrazione di un'istanza di SQL Server dopo aver disabilitato la protezione ma prima di eliminare l'insieme di credenziali:

1. Nel dashboard dell'insieme di credenziali, in **Gestisci**, selezionare **Infrastruttura di backup**.  

   ![Selezionare Infrastruttura di backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. In **Server di gestione** selezionare **Server protetti**.

   ![Selezionare Server protetti](./media/backup-azure-sql-database/protected-servers.png)

3. In **Server protetti** selezionare il server di cui si vuole annullare la registrazione. Per eliminare l'insieme di credenziali, è necessario annullare la registrazione di tutti i server.

4. Fare clic con il pulsante destro del mouse sul server protetto e selezionare **Annulla registrazione**.

   ![Selezionare Elimina](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Ripetere la registrazione dell'estensione nella macchina virtuale SQL Server

In alcuni casi, l'estensione del carico di lavoro nella macchina virtuale può essere interessata per un motivo o un altro. In questi casi, tutte le operazioni attivate nella macchina virtuale inizieranno ad avere esito negativo. Potrà quindi essere necessario registrare nuovamente l'estensione nella macchina virtuale. L'operazione di **ripetizione della registrazione** reinstalla l'estensione di backup del carico di lavoro nella macchina virtuale affinché le operazioni continuino. È possibile trovare questa opzione in **infrastruttura di backup** nell'insieme di credenziali di servizi di ripristino.

![Server protetti nell'infrastruttura di backup](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Questa opzione deve essere usata con attenzione. Quando viene attivato in una macchina virtuale con un'estensione già integra, questa operazione causerà il riavvio dell'estensione. Ciò può comportare l'esito negativo di tutti i processi in corso. Prima di attivare l'operazione di ripetizione della registrazione, verificare la presenza di uno o più [sintomi](backup-sql-server-azure-troubleshoot.md#re-registration-failures).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere la pagina relativa alla [risoluzione dei problemi di backup in un database SQL Server](backup-sql-server-azure-troubleshoot.md).
