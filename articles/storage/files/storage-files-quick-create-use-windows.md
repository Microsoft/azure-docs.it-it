---
title: Creare e usare una condivisione file di Azure in macchine virtuali Windows
description: Creare e usare una condivisione di File di Azure nel portale di Azure. Connetterla a una VM Windows, connettersi alla condivisione e caricarvi un file.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5a3c664f6c6c0532ef915357cfbcbc8228202502
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718240"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Avvio rapido: Creare e gestire una condivisione file di Azure con macchine virtuali Windows

L'articolo illustra i passaggi di base per la creazione e l'uso di una condivisione file di Azure. In questa guida introduttiva, particolare attenzione è dedicata a come configurare rapidamente una condivisione file di Azure in modo da sperimentare il funzionamento del servizio. Se sono necessarie istruzioni più dettagliate per la creazione e l'uso di condivisioni file di Azure nel proprio ambiente, vedere [Usare una condivisione file di Azure con Windows](storage-how-to-use-files-windows.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Preparare l'ambiente

In questa guida introduttiva si configurano gli elementi seguenti:

- Un account di archiviazione di Azure e una condivisione file di Azure
- Una VM Windows Server 2016 Datacenter

### <a name="create-a-storage-account"></a>Creare un account di archiviazione

Prima di poter utilizzare una condivisione file di Azure, è necessario creare un account di archiviazione di Azure. Un account di archiviazione per utilizzo generico v2 consente l'accesso a tutti i servizi di Archiviazione di Azure: BLOB, file, code e tabelle. La guida introduttiva crea un account di archiviazione per utilizzo generico v2, ma i passaggi per creare qualsiasi tipo di account di archiviazione sono simili. Un account di archiviazione può contenere un numero illimitato di condivisioni. In una condivisione può essere archiviato un numero illimitato di file, fino ai limiti di capacità dell'account di archiviazione.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure

Quindi, creare una condivisione file.

1. Una volta completata la distribuzione dell'account di archiviazione di Azure, selezionare **Vai alla risorsa**.
1. Selezionare **Condivisioni file** nel riquadro dell'account di archiviazione.

    ![Selezionare Condivisioni file.](./media/storage-files-quick-create-use-windows/click-files.png)

1. Selezionare **+ Condivisione file**.

    ![Selezionare + condivisione file per creare una nuova condivisione file.](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Assegnare alla nuova condivisione file il nome *qsfileshare,* immettere "1" per **Quota**, lasciare selezionata l'opzione **Ottimizzazione** transazione e selezionare **Crea**. La quota può essere massima di 5 TiB (100 TiB, con condivisioni file di grandi dimensioni abilitate), ma per questa guida introduttiva sono necessari solo 1 GiB.
1. Creare un nuovo file txt chiamato *qsTestFile* nel computer locale.
1. Selezionare la nuova condivisione file, quindi nel percorso della condivisione file, selezionare **Carica**.

    ![Caricare un file.](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Passare al percorso in cui è stato creato il file .txt > selezionare *qsTestFile.txt* > selezionare **Carica**.

Finora, sono stati creati un account di archiviazione e una condivisione file di Azure contenente un solo file in Azure. Quindi si creerà la macchina virtuale di Azure con Windows Server 2016 Datacenter per rappresentare il server locale in questa guida introduttiva.

### <a name="deploy-a-vm"></a>Distribuire una macchina virtuale

1. In seguito, espandere il menu a sinistra del portale e scegliere **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
1. Nella casella di ricerca sopra l'elenco delle **Azure Marketplace,** cercare e selezionare **Windows Server 2016 Datacenter**.
1. Nella scheda **Informazioni di base** in **Dettagli del progetto** selezionare il gruppo di risorse creato per questa guida introduttiva.

   ![Immettere le informazioni di base sulla macchina virtuale nel pannello del portale.](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. In **Dettagli dell'istanza** denominare la VM *qsVM*.
1. Lasciare invariate le impostazioni predefinite per **Area**, **Opzioni di disponibilità**, **Immagine** e **Dimensioni**.
1. In **Account amministratore** aggiungere un nome **utente** e immettere una password **per** la macchina virtuale.
1. Sotto **Regole porta in ingresso**, scegliere **Consentire porte selezionate**, quindi selezionare **RDP (3389)** e **HTTP** dall'elenco a discesa.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**. Il completamento della creazione di una nuova macchina virtuale richiederà alcuni minuti.

1. Una volta completata la distribuzione della macchina virtuale, selezionare **Vai alla risorsa**.

A questo punto, è stata creata una nuova macchina virtuale e collegato un disco dati. Ora è necessario connettersi alla macchina virtuale.

### <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

1. Selezionare **Connetti** nella pagina delle proprietà della macchina virtuale.

   ![Connettersi a una macchina virtuale di Azure dal portale](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. Nella pagina **Connetti alla macchina virtuale** mantenere le opzioni predefinite per la connessione con **Indirizzo IP** sul **numero di porta** *3389* e selezionare **Scarica file RDP**.
1. Aprire il file RDP scaricato e selezionare **Connetti** quando richiesto.
1. Nella finestra **Sicurezza di Windows** selezionare **Altre opzioni** e quindi **Usa un altro account**. Digitare il nome utente come *localhost\nomeutente*, dove &lt;nomeutente&gt; è il nome utente dell'amministratore VM creato per la macchina virtuale. Immettere la password creata per la macchina virtuale e quindi selezionare **OK**.

   ![Altre opzioni](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Selezionare **Sì** o **Continua** per procedere con la connessione.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Eseguire il mapping della condivisione file di Azure a un'unità di Windows

1. Nel portale di Azure passare alla condivisione file *qsfileshare* e selezionare **Connetti**.
1. Selezionare una lettera di unità, quindi copiare il contenuto della seconda casella e incollarlo nel Blocco **note.**

   :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Screenshot che mostra il contenuto della casella da copiare e incollare nel Blocco note." lightbox="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png":::

1. Nella macchina virtuale aprire **PowerShell** e incollare il contenuto del Blocco **note,** quindi premere INVIO per eseguire il comando. Deve eseguire il mapping dell'unità.

## <a name="create-a-share-snapshot"></a>Creare uno snapshot della condivisione

Ora che è stato eseguito il mapping dell'unità, è possibile creare uno snapshot.

1. Nel portale passare alla condivisione file, selezionare **Snapshot** e quindi **+ Aggiungi snapshot.**

   ![Selezionare snapshot nella sezione operazioni e quindi selezionare Aggiungi snapshot.](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. Nella macchina virtuale, aprire il file *qstestfile.txt* e digitare "questo file è stato modificato" > salvare e chiudere il file.
1. Creare un altro snapshot.

## <a name="browse-a-share-snapshot"></a>Esplorare uno snapshot di condivisione

1. Nella condivisione file selezionare **Snapshot**.
1. Nel **pannello Snapshot** selezionare il primo snapshot nell'elenco.

   ![Snapshot selezionato nell'elenco dei timestamp](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. Aprire lo snapshot e *selezionare* qsTestFile.txt.

## <a name="restore-from-a-snapshot"></a>Ripristino da uno snapshot

1. Nel pannello dello snapshot della condivisione file fare clic con il pulsante destro del mouse su *qsTestFile* e scegliere il pulsante **Ripristina**.

    :::image type="content" source="media/storage-files-quick-create-use-windows/restore-share-snapshot.png" alt-text="Screenshot del pannello snapshot, qstestfile selezionato, ripristino evidenziato.":::

1. Selezionare **Sovrascrivi file originale**.

   ![Screenshot del popup di ripristino, sovrascrivere il file originale selezionato.](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Nella macchina virtuale, aprire il file. È stata ripristinata la versione non modificata.

## <a name="delete-a-share-snapshot"></a>Eliminare uno snapshot di condivisione

1. Nella condivisione file selezionare **Snapshot**.
1. Nel pannello **Snapshot selezionare** l'ultimo snapshot nell'elenco e selezionare **Elimina**.

   ![Screenshot del pannello snapshot, ultimo snapshot selezionato, pulsante Elimina evidenziato.](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Usare uno snapshot di condivisione in Windows

Proprio come con gli snapshot VSS in locale, è possibile visualizzare gli snapshot dalla condivisione file di Azure montata usando la scheda Versioni precedenti.

1. In Esplora file, cercare la condivisione montata.

   ![Condivisione montata in Esplora File](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Selezionare *qsTestFile.txt* e > fare clic con il pulsante destro del mouse e scegliere **Proprietà** dal menu.

   ![Fare clic con il pulsante destro del mouse sul menu per scegliere una directory specifica](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Selezionare **Versioni precedenti** per visualizzare l'elenco di snapshot di condivisione per questa directory.

1. Selezionare **Apri** per aprire lo snapshot.

   ![Scheda Versioni precedenti](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Eseguire il ripristino da una versione precedente

1. Selezionare **Ripristina**. Questa azione copia il contenuto dell'intera directory in modo ricorsivo nel percorso originale al momento della creazione dello snapshot di condivisione.

   ![Pulsante Ripristina nel messaggio di avviso](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)
    
    > [!NOTE]
    > Se il file non è stato modificato, non verrà visualizzata una versione precedente per tale file perché tale file è la stessa versione dello snapshot. Questo comportamento è coerente con il funzionamento in un file server Windows.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare una condivisione file di Azure con Windows](storage-how-to-use-files-windows.md)
