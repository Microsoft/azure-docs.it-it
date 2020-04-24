---
title: Gestire i database SAP HANA sottoposti a backup in macchine virtuali di Azure
description: In questo articolo vengono illustrate le attività comuni per la gestione e il monitoraggio di database SAP HANA in esecuzione in macchine virtuali di Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 89fd7f23163d301817e767771257d9bc6f4ed526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480063"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Gestire e monitorare i database SAP HANA di cui è stato eseguito il backup

Questo articolo descrive le attività comuni per la gestione e il monitoraggio di database SAP HANA in esecuzione in una macchina virtuale (VM) di Azure e di cui viene eseguito il backup in un insieme di credenziali di servizi di ripristino di backup di Azure da parte del servizio [backup di Azure](https://docs.microsoft.com/azure/backup/backup-overview) . Si apprenderà come monitorare i processi e gli avvisi, attivare un backup su richiesta, modificare i criteri, arrestare e riprendere la protezione del database e annullare la registrazione di una macchina virtuale dai backup.

Se non sono ancora stati configurati backup per i database di SAP HANA, vedere [eseguire il backup di SAP Hana database in macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorare i processi di backup manuali nel portale

Backup di Azure Mostra tutti i processi attivati manualmente nella sezione **processi di backup** in portale di Azure.

![Sezione processi di backup](./media/sap-hana-db-manage/backup-jobs.png)

I processi visualizzati in questo portale includono l'individuazione e la registrazione del database, nonché le operazioni di backup e ripristino. I processi pianificati, inclusi i backup del log, non sono illustrati in questa sezione. Anche i backup attivati manualmente dal SAP HANA native clients, ovvero studio/Cockpit/DBA Cockpit, non vengono visualizzati qui.

![Elenco processi di backup](./media/sap-hana-db-manage/backup-jobs-list.png)

Per altre informazioni sul monitoraggio, vedere [monitoraggio nella portale di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) e [monitoraggio tramite](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)monitoraggio di Azure.

## <a name="view-backup-alerts"></a>Visualizzare gli avvisi di backup

Gli avvisi rappresentano un modo semplice per monitorare i backup dei database SAP HANA. Gli avvisi consentono di concentrarsi sugli eventi a cui si è maggiormente interessati senza perdersi nella moltitudine di eventi generati da un backup. Backup di Azure consente di impostare gli avvisi ed è possibile monitorarli come segue:

* Accedere al [portale di Azure](https://portal.azure.com/).
* Nel dashboard dell'insieme di credenziali selezionare **avvisi di backup**.

  ![Avvisi di backup nel dashboard dell'insieme di credenziali](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Sarà possibile visualizzare gli avvisi:

  ![Elenco di avvisi di backup](./media/sap-hana-db-manage/backup-alerts-list.png)

* Fare clic sugli avvisi per visualizzare altri dettagli:

  ![Dettagli dell'avviso](./media/sap-hana-db-manage/alert-details.png)

Attualmente, backup di Azure consente l'invio di avvisi tramite posta elettronica. Questi avvisi sono:

* Attivato per tutti gli errori di backup.
* Consolidato a livello di database in base al codice di errore.
* Inviato solo per il primo errore di backup di un database.

ToTo ulteriori informazioni sul monitoraggio, vedere [monitoraggio nella portale di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) e [monitoraggio tramite](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)monitoraggio di Azure.

## <a name="management-operations"></a>Operazioni di gestione

Backup di Azure consente di semplificare la gestione di un database SAP HANA sottoposto a backup con una grande quantità di operazioni di gestione supportate. Queste operazioni sono descritte in modo più dettagliato nelle sezioni riportate di seguito.

### <a name="run-an-on-demand-backup"></a>Eseguire un backup su richiesta

I backup vengono eseguiti in base alla pianificazione dei criteri. È possibile eseguire un backup su richiesta nel modo seguente:

1. Selezionare **Elementi di backup** dal menu dell'insieme di credenziali.
2. In **elementi di backup**selezionare la macchina virtuale che esegue il database di SAP Hana, quindi fare clic su **Esegui backup ora**.
3. In **backup ora**usare il controllo calendario per selezionare l'ultimo giorno in cui deve essere mantenuto il punto di ripristino. Fare quindi clic su **OK**.
4. Monitorare le notifiche del portale. È possibile monitorare lo stato di avanzamento del processo nel dashboard dell'insieme di credenziali > **processi** > **di backup in corso**. A seconda delle dimensioni del database, la creazione del backup iniziale potrebbe richiedere qualche minuto.

### <a name="hana-native-client-integration"></a>Integrazione di HANA Native Client

A questo punto, i backup completi su richiesta attivati da uno dei client nativi HANA verranno visualizzati come backup completo nella pagina **elementi di backup** .

![Ultimi backup eseguiti](./media/sap-hana-db-manage/last-backups.png)

Questi backup completi ad hoc vengono visualizzati anche nell'elenco dei punti di ripristino per il ripristino.

![Elenco di punti di ripristino](./media/sap-hana-db-manage/list-restore-points.png)

### <a name="run-sap-hana-native-client-backup-on-a-database-with-azure-backup-enabled"></a>Eseguire SAP HANA backup di Native client in un database con backup di Azure abilitato

Se si vuole eseguire un backup locale (usando HANA Studio/pozzetto) di un database di cui viene eseguito il backup con backup di Azure, eseguire le operazioni seguenti:

1. Attendere il completamento di tutti i backup completi o del log per il database. Verificare lo stato in SAP HANA Studio/Cockpit.
2. Disabilitare i backup del log e impostare il catalogo di backup sul file system per il database pertinente.
3. A tale scopo, fare doppio clic su **SystemDB** > **Configuration** > **Select database** > **Filter (log)**.
4. Impostare **enable_auto_log_backup** su **No**.
5. Impostare **log_backup_using_backint** su **false**.
6. Eseguire un backup completo su richiesta del database.
7. Attendere il completamento del backup completo e del catalogo.
8. Ripristinare le impostazioni precedenti a quelle per Azure:
   * Impostare **enable_auto_log_backup** su **Sì**.
   * Impostare **log_backup_using_backint** su **true**.

### <a name="change-policy"></a>Modificare i criteri

È possibile modificare i criteri sottostanti per un elemento di backup SAP HANA.

* Nel dashboard dell'insieme di credenziali passare a **elementi di backup**:

  ![Seleziona elementi di backup](./media/sap-hana-db-manage/backup-items.png)

* Scegliere **SAP Hana nella macchina virtuale di Azure**

  ![Scegliere SAP HANA nella macchina virtuale di Azure](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Scegliere l'elemento di backup di cui si desidera modificare i criteri sottostanti
* Fare clic sui criteri di backup esistenti

  ![Selezionare i criteri di backup esistenti](./media/sap-hana-db-manage/existing-backup-policy.png)

* Modificare i criteri scegliendo dall'elenco. Se necessario, [creare un nuovo criterio di backup](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database#create-a-backup-policy) .

  ![Scegliere un criterio dall'elenco a discesa](./media/sap-hana-db-manage/choose-backup-policy.png)

* Salva le modifiche

  ![Salva le modifiche](./media/sap-hana-db-manage/save-changes.png)

* La modifica dei criteri avrà un effetto su tutti gli elementi di backup associati e sul trigger corrispondenti processi di **configurazione della protezione** .

>[!NOTE]
> Eventuali modifiche apportate al periodo di conservazione verranno applicate in modo retrospettivo a tutti i punti di ripristino meno recenti oltre a quelli nuovi.
>
> Non è possibile usare i criteri di backup incrementali per i database SAP HANA. Il backup incrementale non è attualmente supportato per questi database.

### <a name="modify-policy"></a>Modifica criterio

Modificare i criteri per modificare i tipi di backup, le frequenze e il periodo di mantenimento dati.

>[!NOTE]
>Eventuali modifiche apportate al periodo di conservazione verranno applicate in modo retroattivo a tutti i punti di ripristino precedenti, oltre a quelli nuovi.

1. Nel dashboard dell'insieme di credenziali passare a **gestisci > criteri di backup** e scegliere il criterio che si vuole modificare.

   ![Scegliere i criteri da modificare](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Selezionare **Modifica**.

   ![Selezione modifica](./media/sap-hana-db-manage/modify-policy.png)

1. Scegliere la frequenza per i tipi di backup.

   ![Scegliere la frequenza di backup](./media/sap-hana-db-manage/choose-frequency.png)

La modifica dei criteri avrà un effetto su tutti gli elementi di backup associati e sul trigger corrispondenti processi di **configurazione della protezione** .

### <a name="inconsistent-policy"></a>Criteri non coerenti

Occasionalmente, un'operazione di modifica dei criteri può causare una versione non **coerente** dei criteri per alcuni elementi di backup. Ciò si verifica quando il processo di **configurazione della protezione** corrispondente non riesce per l'elemento di backup dopo l'attivazione di un'operazione di modifica dei criteri. Viene visualizzato come segue nella visualizzazione dell'elemento di backup:

![Criteri non coerenti](./media/sap-hana-db-manage/inconsistent-policy.png)

È possibile correggere la versione dei criteri per tutti gli elementi interessati con un solo clic:

![Correggi versione criteri](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Arrestare la protezione per un database SAP HANA

È possibile arrestare la protezione di un database SAP HANA in due modi:

* Interrompere tutti i processi di backup futuri ed eliminare tutti i punti di recupero.
* Arrestare tutti i processi di backup futuri mantenendo tuttavia invariati i punti di ripristino.

Se si sceglie di lasciare invariati i punti di ripristino, tenere presente quanto segue:

* Tutti i punti di ripristino rimarranno invariati per sempre, tutte le eliminazioni verranno arrestate in caso di arresto della protezione con conservazione dei dati.
* Verranno addebitati l'istanza protetta e l'archiviazione utilizzata. Per altre informazioni, vedere [prezzi di backup di Azure](https://azure.microsoft.com/pricing/details/backup/).
* Se si elimina un'origine dati senza arrestare i backup, i nuovi backup avranno esito negativo.

Per interrompere la protezione per un database:

* Nel dashboard dell'insieme di credenziali selezionare **elementi di backup**.
* In **tipo di gestione di backup**selezionare **SAP Hana nella macchina virtuale di Azure**

  ![Selezionare SAP HANA in una macchina virtuale di Azure](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Selezionare il database per il quale si desidera arrestare la protezione:

  ![Selezionare il database per arrestare la protezione](./media/sap-hana-db-manage/select-database.png)

* Nel menu del database, selezionare **Interrompi backup**.

  ![Selezionare Interrompi backup](./media/sap-hana-db-manage/stop-backup.png)

* Scegliere se mantenere o eliminare i dati dal menu **Interrompi backup** . Se lo si desidera, specificare un motivo e un commento.

  ![Selezionare Mantieni o Elimina dati](./media/sap-hana-db-manage/retain-backup-data.png)

* Selezionare **Arresta backup**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Riprendere la protezione per un database SAP HANA

Quando si arresta la protezione per il database di SAP HANA, se si seleziona l'opzione **Mantieni dati di backup** , sarà possibile riprendere la protezione in un secondo momento. Se non si conservano i dati di cui è stato eseguito il backup, non sarà possibile riprendere la protezione.

Per riprendere la protezione per un database SAP HANA:

* Aprire l'elemento di backup e selezionare **Riprendi backup**.

   ![Seleziona Riprendi backup](./media/sap-hana-db-manage/resume-backup.png)

* Selezionare un criterio dal menu **Criteri di backup** e quindi selezionare **Salva**.

### <a name="upgrading-from-sap-hana-10-to-20"></a>Aggiornamento da SAP HANA 1,0 a 2,0

Informazioni su come continuare il backup per un database di SAP HANA [dopo l'aggiornamento da SAP HANA 1,0 a 2,0](backup-azure-sap-hana-database-troubleshoot.md#upgrading-from-sap-hana-10-to-20).

### <a name="upgrading-without-a-sid-change"></a>Aggiornamento senza una modifica del SID

Informazioni su come continuare il backup di un database di SAP HANA il cui [SID non è stato modificato dopo l'aggiornamento](backup-azure-sap-hana-database-troubleshoot.md#upgrading-without-an-sid-change).

### <a name="unregister-an-sap-hana-instance"></a>Annulla la registrazione di un'istanza di SAP HANA

Annullare la registrazione di un'istanza di SAP HANA dopo aver disabilitato la protezione ma prima di eliminare l'insieme di credenziali:

* Nel dashboard dell'insieme di credenziali, in **Gestisci**, selezionare **infrastruttura di backup**.

   ![Selezionare Infrastruttura di backup](./media/sap-hana-db-manage/backup-infrastructure.png)

* Selezionare il **tipo di gestione di backup** come **carico di lavoro in una macchina virtuale di Azure**

   ![Selezionare il tipo di gestione di backup come carico di lavoro in una macchina virtuale di Azure](./media/sap-hana-db-manage/backup-management-type.png)

* In **server protetti**selezionare l'istanza di di cui annullare la registrazione. Per eliminare l'insieme di credenziali, è necessario annullare la registrazione di tutti i server e le istanze.

* Fare clic con il pulsante destro del mouse sull'istanza protetta e scegliere **Annulla registrazione**.

   ![Selezionare Annulla registrazione](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Registrare nuovamente l'estensione nella macchina virtuale del server SAP HANA

A volte l'estensione del carico di lavoro nella macchina virtuale può essere interessata per un motivo o un altro. In questi casi, tutte le operazioni attivate nella macchina virtuale inizieranno ad avere esito negativo. Potrebbe quindi essere necessario registrare nuovamente l'estensione nella macchina virtuale. L'operazione di ripetizione della registrazione reinstalla l'estensione di backup del carico di lavoro nella macchina virtuale affinché le operazioni continuino.

Usare questa opzione con cautela: quando viene attivato in una macchina virtuale con un'estensione già integra, questa operazione causerà il riavvio dell'estensione. Questo potrebbe causare l'esito negativo di tutti i processi in corso. Verificare la presenza di uno o più [sintomi](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) prima di attivare l'operazione di ripetizione della registrazione.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [risolvere i problemi comuni durante il backup SAP Hana database.](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
