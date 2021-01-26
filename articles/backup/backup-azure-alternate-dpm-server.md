---
title: Recuperare dati da un server di Backup di Azure
description: Recuperare i dati che sono stati protetti in un insieme di credenziali di Servizi di ripristino da qualsiasi server di Backup di Azure registrato in tale insieme di credenziali.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: ed8c937f97ec7a74662a8b46a354b0a6db39a2b0
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806010"
---
# <a name="recover-data-from-azure-backup-server"></a>Ripristinare i dati da un server di Backup di Azure

È possibile usare il server di Backup di Azure per recuperare i dati di un backup in un insieme di credenziali di Servizi di ripristino. Il processo per eseguire l'operazione è integrato nella console di gestione del server di Backup di Azure, quindi è simile al flusso di lavoro di ripristino per gli altri componenti di Backup di Azure.

> [!NOTE]
> Questo articolo è applicabile a [System Center Data Protection Manager 2012 R2 con UR7 o versione successiva](https://support.microsoft.com/kb/3065246), combinato con l'[agente di Backup di Azure Backup più recente](https://aka.ms/azurebackup_agent).
>
>

Per ripristinare i dati da un server di Backup di Azure:

1. Dalla scheda **ripristino** della console di gestione server di backup di Azure selezionare **"Aggiungi DPM esterno"** (nella parte superiore sinistra della schermata).

    ![Aggiungi DPM esterno](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Scaricare il nuovo **insieme di credenziali** da quello associato al **server di Backup di Azure** i cui dati sono in fase di ripristino, scegliere il server di Backup di Azure dall'elenco di server di Backup di Azure registrati con l'insieme delle credenziali per Servizi di ripristino e fornire la **passphrase di crittografia** associata al server i cui dati sono in fase di ripristino.

    ![Credenziali DPM esterne](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Solo i server di Backup di Azure associati allo stesso insieme di credenziali per la registrazione possono recuperare i dati.
   >
   >

    Dopo aver aggiunto il server di Backup di Azure esterno, è possibile esplorare i dati del server di Backup di Azure esterno e di quello locale dalla scheda **Ripristino**.
3. Individuare l'elenco dei server di produzione protetti dal server di Backup di Azure esterno disponibili e selezionare l'origine dati appropriata.

    ![Cercare nel Server DPM esterno](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selezionare **il mese e l'anno** dall’elenco a discesa **Punti di ripristino**, selezionare la **Data di ripristino** necessaria per quando è stato creato il punto di ripristino, quindi scegliere l'**Ora ripristino**.

    Viene visualizzato un elenco di file e cartelle nel riquadro inferiore che può essere esplorato e ripristinato in qualsiasi posizione.

    ![Punti di ripristino del Server DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Fare clic con il pulsante destro del mouse sull'elemento appropriato e selezionare **Ripristina**.

    ![Ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/recover.png)
6. Verificare la **Selezione ripristino**. Verificare la data e ora della copia di backup da ripristinare, nonché l'origine da cui è stata creata la copia di backup. Se la selezione non è corretta, selezionare **Annulla** per tornare alla scheda ripristino per selezionare il punto di ripristino appropriato. Se la selezione è corretta, fare clic su **Avanti**.

    ![Riepilogo del ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selezionare **Ripristina in un percorso alternativo**. **Cercare** la posizione corretta per il ripristino.

    ![Posizione alternativa del ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Scegliere l'opzione relativa a **Crea copia**, **Ignora** o **Sovrascrivi**.

   * **Crea copia** : crea una copia del file se si verifica un conflitto di nomi.
   * **Ignora** : se si verifica un conflitto di nomi, il file non viene ripristinato, lasciando il file originale.
   * Sovrascrivi: se si verifica un conflitto di nomi **, sovrascrive la** copia esistente del file.

     Scegliere l'opzione appropriata per **Ripristina protezione**. È possibile applicare le impostazioni di sicurezza del computer di destinazione in cui i dati vengono ripristinati o le impostazioni di sicurezza che erano applicabili al prodotto nel momento in cui è stato creato il punto di ripristino.

     Identificare se viene inviata una **Notifica** dopo che il ripristino è stato completato correttamente.

     ![Notifiche di ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. La schermata di **Riepilogo** elenca le opzioni scelte finora. Dopo aver selezionato **Ripristina**, i dati vengono ripristinati nel percorso locale appropriato.

    ![Riepilogo opzioni di ripristino DPM esterno](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Il processo di ripristino può essere monitorato nella scheda **Monitoraggio** del server di Backup di Azure.
   >
   >

    ![Monitoraggio del ripristino](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. È possibile selezionare **Cancella DPM esterno** nella scheda **ripristino** del server DPM per rimuovere la visualizzazione del server DPM esterno.

    ![Deselezionare DPM esterno](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Risoluzione dei problemi relativi ai messaggi di errore

| No. | Messaggio di errore | Passaggi per la risoluzione dei problemi |
|:---:|:--- |:--- |
| 1. |Questo server non è registrato nell'insieme di credenziali specificate per le credenziali dell'insieme di credenziali. |**Causa:** questo errore viene visualizzato quando il file di archivio dell'insieme di credenziali selezionato non appartiene a un insieme di credenziali di Servizi di ripristino associato al server di Backup di Azure in cui viene tentato il ripristino. <br> **Risoluzione:** scaricare il file di archivio delle credenziali dall'insieme di credenziali di Servizi di ripristino nel quale il server di Backup di Azure è registrato. |
| 2. |I dati ripristinabili non sono disponibili o il server selezionato non è un server DPM. |**Motivo:** Non sono presenti altri server di backup di Azure registrati nell'insieme di credenziali di servizi di ripristino oppure i server non hanno ancora caricato i metadati o il server selezionato non è un server di Backup di Azure (usando Windows Server o Windows client). <br> **Risoluzione:** se sono presenti altri server di Backup di Azure registrati per l'insieme di credenziali di Servizi di ripristino, assicurarsi che sia installato l'agente di Backup di Azure più recente. <br>Se sono presenti altri server di Backup di Azure registrati nell'insieme di credenziali di Servizi di ripristino, attendere un giorno dopo l'installazione per avviare il processo di ripristino. I processi notturni caricheranno i metadati per tutti i backup protetti nel cloud. I dati saranno disponibili per il ripristino. |
| 3. |Nessun altro server DPM viene registrato nell'insieme di credenziali. |**Causa:** non ci sono altri server di Backup di Azure registrati nell'insieme di credenziali da cui si sta tentando il ripristino.<br>**Risoluzione:** se sono presenti altri server di Backup di Azure registrati per l'insieme di credenziali di Servizi di ripristino, assicurarsi che sia installato l'agente di Backup di Azure più recente.<br>Se sono presenti altri server di Backup di Azure registrati nell'insieme di credenziali di Servizi di ripristino, attendere un giorno dopo l'installazione per avviare il processo di ripristino. I processi notturni caricano i metadati per tutti i backup protetti nel cloud. I dati saranno disponibili per il ripristino. |
| 4. |La passphrase di crittografia fornita non corrisponde alla passphrase associata al server seguente: **\<server name>** |**Motivo:** La passphrase di crittografia usata nel processo di crittografia dei dati dai dati del server di Backup di Azure di cui è in corso il ripristino non corrisponde alla passphrase di crittografia fornita. L'agente non è in grado di decrittografare i dati, quindi il ripristino non riesce.<br>**Risoluzione:** Specificare esattamente la stessa passphrase di crittografia associata all'server di Backup di Azure i cui dati sono in fase di ripristino. |

## <a name="next-steps"></a>Passaggi successivi

Leggere le altre domande frequenti:

* [Domande comuni](backup-azure-vm-backup-faq.yml) sui backup di macchine virtuali di Azure
* [Domande comuni](backup-azure-file-folder-backup-faq.md) sull'agente di Backup di Azure
