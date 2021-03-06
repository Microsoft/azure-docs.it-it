---
title: Eseguire il backup di un server Exchange System Center DPM
description: Informazioni su come eseguire il backup di un server di Exchange in Backup di Azure con System Center 2012 R2 DPM
ms.reviewer: kasinh
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: a89c37b8447b318c44faf0d4e0b1921d305e7f59
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519392"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Eseguire il backup di un server di Exchange in Backup di Azure con System Center 2012 R2 DPM

Questo articolo descrive come configurare un server di System Center 2012 R2 Data Protection Manager (DPM) per eseguire il backup di un server di Microsoft Exchange per Backup di Azure.  

## <a name="updates"></a>Aggiornamenti

Per registrare correttamente il server DPM con Backup di Azure, è necessario installare l'aggiornamento cumulativo più recente per System Center 2012 R2 DPM e la versione più recente dell'agente di Backup di Azure. Ottenere l'aggiornamento cumulativo più recente dal [catalogo Microsoft](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Per gli esempi in questo articolo, è installata la versione 2.0.8719.0 dell'agente di Backup di Azure e l'aggiornamento cumulativo 6 è installato in System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare, assicurarsi che tutti i [prerequisiti](backup-azure-dpm-introduction.md#prerequisites-and-limitations) per l'uso di Backup di Microsoft Azure per proteggere i carichi di lavoro siano stati soddisfatti. I prerequisiti includono i seguenti:

* È stato creato un insieme di credenziali per il backup nel sito di Azure.
* Le credenziali dell'agente e dell'insieme di credenziali sono state scaricate nel server DPM.
* L'agente è installato nel server DPM.
* Le credenziali dell'insieme di credenziali sono state usate per registrare il server DPM.
* Se si protegge Exchange 2016, eseguire l'aggiornamento a DPM 2012 R2 UR9 o versione successiva.

## <a name="dpm-protection-agent"></a>Agente protezione DPM

Per installare l'agente protezione DPM nel server di Exchange, seguire questi passaggi:

1. Assicurarsi che i firewall siano configurati correttamente. Vedere [Configurare le eccezioni del firewall per l'agente](/system-center/dpm/configure-firewall-settings-for-dpm).
2. Installare l'agente nel server Exchange selezionando **Gestione > agenti > installa** in Console amministrazione DPM. Per la procedura dettagliata, vedere [Installare l'agente protezione DPM](/system-center/dpm/deploy-dpm-protection-agent) .

## <a name="create-a-protection-group-for-the-exchange-server"></a>Creare un gruppo di protezione per il server di Exchange

1. Nella finestra Console amministrazione DPM selezionare **Protezione** e  quindi Selezionare Nuovo sulla barra multifunzione degli strumenti per aprire la procedura guidata Crea **nuovo gruppo protezione** dati.
2. Nella schermata **iniziale** della procedura guidata selezionare **Avanti.**
3. Nella schermata **Selezione tipo di gruppo protezione** dati selezionare **Server** e selezionare **Avanti.**
4. Selezionare il database del server Exchange da proteggere e selezionare **Avanti.**

   > [!NOTE]
   > Se si protegge Exchange 2013, controllare i prerequisiti di [Exchange 2013.](/system-center/dpm/back-up-exchange)
   >
   >

    Nell'esempio seguente è selezionato il database di Exchange 2010.

    ![Seleziona membri del gruppo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selezionare il metodo di protezione dati.

    Assegnare un nome al gruppo protezione dati e quindi selezionare entrambe le opzioni seguenti:

   * Protezione dati breve termine tramite: Disco.
   * Protezione dati online.
6. Selezionare **Avanti**.
7. Selezionare l'opzione **Esegui Eseutil per controllare l'integrità dei dati** se si vuole controllare l'integrità dei database di Exchange Server.

    Dopo aver selezionato questa opzione, la verifica coerenza dei backup verrà eseguita nel server DPM per evitare il traffico di I/O generato eseguendo il comando **eseutil** nel server Exchange.

   > [!NOTE]
   > Per usare questa opzione, è necessario copiare i file Ese.dll and Eseutil.exe nella directory C:\Programmi\Microsoft System Center 2012 R2\DPM\DPM\bin nel server DPM. In caso contrario, viene generato l'errore seguente:   
   > ![Errore di Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Selezionare **Avanti**.
9. Selezionare il database per **Copia backup** e quindi selezionare **Avanti.**

   > [!NOTE]
   > Se non si seleziona "Backup completo" per almeno una copia daG di un database, i log non verranno troncati.
   >
   >
10. Configurare gli obiettivi per **il backup a breve termine** e quindi selezionare **Avanti.**
11. Esaminare lo spazio disponibile su disco e quindi selezionare **Avanti.**
12. Selezionare l'ora in cui il server DPM creerà la replica iniziale e quindi selezionare **Avanti.**
13. Selezionare le opzioni di verifica coerenza e quindi **selezionare Avanti.**
14. Scegliere il database di cui si vuole eseguire il backup in Azure e quindi selezionare **Avanti.** Ad esempio:

    ![Specifica i dati da proteggere online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definire la pianificazione per **Backup di Azure** e quindi selezionare **Avanti.** Ad esempio:

    ![Specificare la pianificazione dei backup online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Tenere presente che i punti di ripristino online sono basati sui punti di ripristino di backup completo rapido. Pertanto, è necessario pianificare il punto di ripristino online dopo l'ora specificata per il punto di ripristino completo rapido.
    >
    >
16. Configurare i criteri di conservazione **per Backup di Azure** e quindi selezionare **Avanti.**
17. Scegliere un'opzione di replica online e selezionare **Avanti.**

    Un database di grandi dimensioni potrebbe richiedere molto tempo per creare il backup iniziale in rete. Per evitare questo problema, è possibile creare un backup offline.  

    ![Specificare i criteri di mantenimento online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confermare le impostazioni e quindi selezionare **Crea gruppo.**
19. Selezionare **Chiudi**.

## <a name="recover-the-exchange-database"></a>Ripristinare il database di Exchange

1. Per ripristinare un database di Exchange, **selezionare Ripristino** nella Console amministrazione DPM.
2. Individuare il database di Exchange che si vuole ripristinare.
3. Selezionare un punto di ripristino online dall'elenco a discesa *Ora ripristino* .
4. Selezionare **Ripristina** per avviare il **Ripristino guidato.**

Per i punti di ripristino online sono disponibili cinque tipi:

* **Ripristina nel percorso originale di Exchange Server:** i dati verranno ripristinati nel server di Exchange originale.
* **Ripristina in un altro database in un Server di Exchange:** i dati verranno ripristinati in un altro database in un altro server di Exchange.
* **Ripristina in un database di ripristino:** i dati verranno ripristinati in un database di ripristino di Exchange (RDB).
* **Copia in una cartella di rete:** i dati verranno ripristinati in una cartella di rete.
* **Copia su nastro:** se si ha una libreria di nastri o un'unità nastro autonoma collegata e configurata nel server DPM, il punto di ripristino verrà copiato su un nastro disponibile.

    ![Scegliere la replica online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Passaggi successivi

* [Backup di Azure - Domande frequenti](backup-azure-backup-faq.yml)
