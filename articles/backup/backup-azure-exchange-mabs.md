---
title: Eseguire il backup di Exchange Server con server di Backup di Azure
description: Informazioni su come eseguire il backup di un server di Exchange in Backup di Azure con il server di Backup di Azure
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 03/24/2017
ms.openlocfilehash: 1d7d28d813df82a5e1ea0fe424bba2ef5a9a2684
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80421341"
---
# <a name="back-up-an-exchange-server-to-azure-with-azure-backup-server"></a>Eseguire il backup di un server Exchange in Azure con il server di Backup di Azure

Questo articolo descrive come configurare il server di Backup di Microsoft Azure (MABS) per eseguire il backup di un server Microsoft Exchange in Azure.  

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare, assicurarsi che il Server di Backup di Azure sia [installato e pronto](backup-azure-microsoft-azure-backup.md).

## <a name="mabs-protection-agent"></a>Agente protezione MABS

Per installare l'agente protezione MABS nel server di Exchange, seguire questi passaggi:

1. Assicurarsi che i firewall siano configurati correttamente. Vedere [Configurare le eccezioni del firewall per l'agente](https://docs.microsoft.com/system-center/dpm/configure-firewall-settings-for-dpm?view=sc-dpm-2019).
2. Per installare l'agente nel server di Exchange, fare clic su **Gestione > Agenti > Installa** nella Console amministrazione MABS. Per la procedura dettagliata, vedere [Installare l'agente protezione MABS](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019) .

## <a name="create-a-protection-group-for-the-exchange-server"></a>Creare un gruppo di protezione per il server di Exchange

1. Nella Console amministrazione MABS fare clic su **Protezione** e quindi fare clic su **Nuovo** nella barra multifunzione per aprire la procedura guidata **Crea nuovo gruppo protezione dati**.
2. Nella schermata **iniziale** della procedura guidata fare clic su **Avanti**.
3. Nella schermata **Selezione tipo di gruppo protezione** dati selezionare **Server** e fare clic su **Avanti**.
4. Selezionare il database di Exchange Server che si vuole proteggere e fare clic su **Avanti**.

   > [!NOTE]
   > Se si vuole proteggere Exchange 2013, controllare i [Prerequisiti di Exchange 2013](https://docs.microsoft.com/system-center/dpm/back-up-exchange?view=sc-dpm-2016).
   >
   >

    Nell'esempio seguente è selezionato il database di Exchange 2010.

    ![Seleziona membri del gruppo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selezionare il metodo di protezione dati.

    Assegnare un nome al gruppo protezione dati e quindi selezionare entrambe le opzioni seguenti:

   * Protezione dati breve termine tramite: Disco.
   * Protezione dati online.
6. Fare clic su **Avanti**.
7. Selezionare l'opzione **Esegui Eseutil per controllare l'integrità dei dati** se si vuole controllare l'integrità dei database di Exchange Server.

    Dopo aver selezionato questa opzione, la verifica coerenza dei backup verrà eseguita su MAB per evitare il traffico di I/O generato eseguendo il comando **eseutil** sul server Exchange.

   > [!NOTE]
   > Per usare questa opzione, è necessario copiare i file di Ese.dll e di Eseutil.exe nella directory c:\Programmi\Microsoft Azure Backup\DPM\DPM\bin nel server MAB. In caso contrario, viene generato l'errore seguente:   
   > ![Errore di Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Fare clic su **Avanti**.
9. Selezionare il database per **Backup di copia**, quindi fare clic su **Avanti**.

   > [!NOTE]
   > Se non si seleziona "backup completo" per almeno una copia DAG di un database, i log non verranno troncati.
   >
   >
10. Configurare gli obiettivi per **Backup a breve termine**, quindi fare clic su **Avanti**.
11. Controllare lo spazio disponibile su disco e quindi fare clic su **Avanti**.
12. Selezionare l'ora in cui il server MAB creerà la replica iniziale e quindi fare clic su **Avanti**.
13. Selezionare le opzioni di verifica coerenza e quindi fare clic su **Avanti**.
14. Scegliere il database di cui si vuole eseguire il backup in Azure e quindi fare clic su **Avanti**. Ad esempio:

    ![Specifica i dati da proteggere online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definire la pianificazione per **Backup di Azure**, quindi fare clic su **Avanti**. Ad esempio:

    ![Specificare la pianificazione dei backup online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Tenere presente che i punti di ripristino online sono basati sui punti di ripristino di backup completo rapido. Pertanto, è necessario pianificare il punto di ripristino online dopo il tempo specificato per il punto di ripristino completo rapido.
    >
    >
16. Configurare i criteri di conservazione per **Backup di Azure**, quindi fare clic su **Avanti**.
17. Scegliere un'opzione di replica online e fare clic su **Avanti**.

    Un database di grandi dimensioni potrebbe richiedere molto tempo per creare il backup iniziale in rete. Per evitare questo problema, è possibile creare un backup offline.  

    ![Specificare i criteri di mantenimento online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Verificare le impostazioni e quindi fare clic su **Crea gruppo**.
19. Fare clic su **Close**.

## <a name="recover-the-exchange-database"></a>Ripristinare il database di Exchange

1. Per ripristinare un database di Exchange, fare clic su **Ripristino** nella Console amministrazione MAB.
2. Individuare il database di Exchange che si vuole ripristinare.
3. Selezionare un punto di ripristino online dall'elenco a discesa *Ora ripristino* .
4. Fare clic su **Ripristina** per avviare il **Ripristino guidato**.

Per i punti di ripristino online sono disponibili cinque tipi:

* **Ripristina nel percorso originale di Exchange Server:** i dati verranno ripristinati nel server di Exchange originale.
* **Ripristina in un altro database in un Server di Exchange:** i dati verranno ripristinati in un altro database in un altro server di Exchange.
* **Ripristina in un database di ripristino:** i dati verranno ripristinati in un database di ripristino di Exchange (RDB).
* **Copia in una cartella di rete:** i dati verranno ripristinati in una cartella di rete.
* **Copia su nastro:** se si ha una libreria di nastri o un'unità nastro autonoma collegata e configurata in MABS, il punto di recupero verrà copiato su un nastro disponibile.

    ![Scegliere la replica online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Passaggi successivi

* [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)
