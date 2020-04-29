---
title: Domande frequenti - Backup di database SAP HANA in VM di Azure
description: In questo articolo vengono fornite le risposte alle domande più comuni sul backup dei database di SAP HANA con il servizio backup di Azure.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a46c4d6cccc00452a56567880400ef5779e6aed4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80155393"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Domande frequenti: eseguire il backup di database SAP HANA nelle VM di Azure

Questo articolo risponde a domande comuni sul backup di SAP HANA database usando il servizio backup di Azure.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Quanti backup completi sono supportati al giorno?

È supportato un solo backup completo al giorno. Non è possibile eseguire il backup differenziale e il backup completo attivato nello stesso giorno.

### <a name="do-successful-backup-jobs-create-alerts"></a>I processi di backup con esito positivo determinano la creazione di avvisi?

No. I processi di backup con esito positivo non generano avvisi. Gli avvisi vengono inviati solo per i processi di backup con esito negativo. Il comportamento dettagliato per gli avvisi del portale è illustrato [qui](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor). Tuttavia, se si è interessati ad avere avvisi anche per i processi riusciti, è possibile usare [monitoraggio di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>È possibile visualizzare i processi di backup pianificati nel menu processi di backup?

Il menu processo di backup mostrerà solo i processi di backup ad hoc. Per i processi pianificati, usare [monitoraggio di Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor).

### <a name="are-future-databases-automatically-added-for-backup"></a>I database futuri vengono aggiunti automaticamente per il backup?

No, questa operazione non è attualmente supportata.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Se si elimina un database da un'istanza, cosa accadrà ai backup?

Se un database viene eliminato da un'istanza di SAP HANA, vengono comunque tentati i backup del database. Ciò implica che il database eliminato inizia a essere visualizzato come non integro in **elementi di backup** ed è ancora protetto.
Il modo corretto per arrestare la protezione di questo database consiste nell'eseguire l' **arresto del backup con Elimina dati** in questo database.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Se si modifica il nome del database dopo che è stato protetto, quale sarà il comportamento?

Un database rinominato viene considerato come un nuovo database. Il servizio considererà pertanto questa situazione come se il database non venisse trovato e con i backup con esito negativo. Il database rinominato verrà visualizzato come un nuovo database e deve essere configurato per la protezione.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Quali sono i prerequisiti per eseguire il backup dei database SAP HANA in una macchina virtuale di Azure?

Per informazioni sui [prerequisiti](tutorial-backup-sap-hana-db.md#prerequisites) e [sullo script di pre-registrazione](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) , vedere le sezioni.

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>Quali autorizzazioni devono essere impostate per consentire ad Azure di eseguire il backup SAP HANA database?

L'esecuzione dello script di pre-registrazione consente di impostare le autorizzazioni necessarie per consentire ad Azure di eseguire il backup dei database SAP HANA. Per altre informazioni, [vedere lo script](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)di pre-registrazione.

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>Il backup del lavoro viene eseguito dopo la migrazione SAP HANA da 1,0 a 2,0?

Fare riferimento a [questa sezione](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) della Guida alla risoluzione dei problemi.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>È possibile configurare il backup di Azure HANA rispetto a un IP virtuale (Load Balancer) e non a una macchina virtuale?

Attualmente non è possibile configurare la soluzione in base a un indirizzo IP virtuale da solo. Per eseguire la soluzione è necessaria una macchina virtuale.

### <a name="i-have-a-sap-hana-system-replication-hsr-how-should-i-configure-backup-for-this-setup"></a>Si dispone di una replica di sistema SAP HANA (HSR), come è possibile configurare il backup per questa configurazione?

I nodi primari e secondari di HSR verranno considerati come due macchine virtuali singole e non correlate. È necessario configurare il backup nel nodo primario e quando si verifica il failover, è necessario configurare il backup nel nodo secondario, che ora diventa il nodo primario. Non è presente alcun ' failover automatico ' del backup nell'altro nodo.

## <a name="restore"></a>Restore

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Perché non è possibile visualizzare il sistema HANA a cui si vuole ripristinare il database?

Verificare che siano soddisfatti tutti i prerequisiti per l'istanza di ripristino per SAP HANA di destinazione. Per altre informazioni, vedere [prerequisiti-ripristinare database di SAP Hana in una macchina virtuale di Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Perché la sovrascrittura del ripristino del database non riesce per il database?

Verificare che l'opzione **forza sovrascrittura** sia selezionata durante il ripristino.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Perché viene visualizzato l'errore "i sistemi di origine e di destinazione per il ripristino sono incompatibili"?

Per informazioni sui tipi di ripristino attualmente supportati, vedere la nota SAP HANA [1642148](https://launchpad.support.sap.com/#/notes/1642148) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [eseguire il backup di SAP Hana database](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) in esecuzione in macchine virtuali di Azure.
