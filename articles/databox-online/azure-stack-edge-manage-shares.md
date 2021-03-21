---
title: Azure Stack Edge Pro-gestione delle condivisioni FPGA | Microsoft Docs
description: Viene descritto come usare la portale di Azure per gestire le condivisioni nel Azure Stack Edge Pro-FPGA.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/04/2021
ms.author: alkohli
ms.openlocfilehash: 7fbb5ca43d2877e2e14914b71df7cc1bcdf27f88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898236"
---
# <a name="use-the-azure-portal-to-manage-shares-on-azure-stack-edge-pro-fpga"></a>Usare il portale di Azure per gestire le condivisioni in un FPGA Pro Azure Stack Edge 

Questo articolo descrive come gestire le condivisioni nel dispositivo FPGA Azure Stack Edge Pro. È possibile gestire il dispositivo FPGA Azure Stack Edge Pro tramite il portale di Azure o tramite l'interfaccia utente Web locale. Usare il portale di Azure per aggiungere, eliminare o aggiornare le condivisioni o per sincronizzare la chiave di archiviazione dell'account di archiviazione associato alle condivisioni.

## <a name="about-shares"></a>Informazioni sulle condivisioni

Per trasferire i dati in Azure, è necessario creare condivisioni nell'FPGA di Azure Stack Edge Pro. Le condivisioni aggiunte al dispositivo Azure Stack Edge Pro possono essere condivisioni locali o condivisioni che effettuano il push dei dati nel cloud.

 - **Condivisioni locali**: usare queste condivisioni quando si desidera che i dati vengano elaborati localmente nel dispositivo.
 - **Condivisioni**: usare queste condivisioni quando si desidera che i dati del dispositivo vengano inseriti automaticamente nell'account di archiviazione nel cloud. Tutte le funzioni cloud, ad esempio le **chiavi di archiviazione** di **aggiornamento** e sincronizzazione, si applicano alle condivisioni.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Aggiungere una condivisione
> * Eliminare una condivisione
> * Aggiornare le condivisioni
> * Sincronizzare la chiave di archiviazione

## <a name="add-a-share"></a>Aggiungere una condivisione

Eseguire i passaggi seguenti nel portale di Azure per creare una condivisione.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **Cloud Storage Gateway**. Passare a **condivisioni** , quindi selezionare **+ Aggiungi condivisione** sulla barra dei comandi.

    ![Selezionare Aggiungi condivisione](media/azure-stack-edge-manage-shares/add-share-1.png)

2. In **Aggiungi condivisione** specificare le impostazioni della condivisione. Specificare un nome univoco per la condivisione.
    
    I nomi di condivisioni possono contenere solo numeri, lettere minuscole e trattini. Il nome condivisione deve avere una lunghezza compresa fra 3 e 63 caratteri e iniziare con una lettera o un numero. Ogni trattino deve essere preceduto e seguito da un carattere diverso da un trattino.

3. Selezionare un **Tipo** per la condivisione. Il tipo può essere **SMB** o **NFS**. L'impostazione predefinita è SMB. SMB è l'opzione standard per i client Windows, mentre NFS è l'opzione usata per i client Linux. Le opzioni visualizzate variano leggermente a seconda che si scelgano condivisioni SMB o NFS.

4. Specificare un **account di archiviazione** in cui risiede la condivisione. Nell'account di archiviazione viene creato un contenitore con il nome della condivisione, se non esiste già. Se esiste già un contenitore, viene usato quello.

5. Dall'elenco a discesa scegliere il **servizio di archiviazione** da BLOB in blocchi, BLOB di pagine o file. Il tipo di servizio scelto dipende dal formato che si vuole applicare ai dati che risiederanno in Azure. Ad esempio, in questo caso, si vuole che i dati si trovino come BLOB in blocchi in Azure, quindi si seleziona **BLOB in blocchi**. Se si sceglie il **BLOB di pagine**, è necessario assicurarsi che i dati siano allineati a 512 byte. Usare il **BLOB di pagine** per i dischi rigidi virtuali o VHDX sempre allineati a 512 byte.

   > [!IMPORTANT]
   > Verificare che per l'account di archiviazione di Azure in uso non siano impostati criteri di immutabilità se lo si usa con un dispositivo Azure Stack Edge Pro o Data Box Gateway. Per altre informazioni, vedere [Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB](../storage/blobs/storage-blob-immutability-policies-manage.md).

6. Questo passaggio varia a seconda che si stia creando una condivisione SMB o NFS.
   - **Se si crea una condivisione SMB**: nel campo **Utente locale con tutti i privilegi** selezionare **Crea nuovo** o **Usa esistente**. Se si crea un nuovo utente locale, compilare i campi **Nome utente**, **Password** e Conferma password. Vengono così assegnate le autorizzazioni all'utente locale. Dopo aver assegnato le autorizzazioni in questa fase, è possibile modificarle con Esplora file.

      ![Aggiunta di una condivisione SMB](media/azure-stack-edge-manage-shares/add-smb-share.png)

        Se si seleziona Consenti solo operazioni di lettura per i dati di questa condivisione, sarà possibile specificare utenti di sola lettura.
   - **Se si crea una condivisione NFS**: occorre specificare l'**indirizzo IP dei client autorizzati** ad accedere alla condivisione.

      ![Aggiunta di una condivisione NFS](media/azure-stack-edge-manage-shares/add-nfs-share.png)

7. Per accedere facilmente alle condivisioni dai moduli di calcolo Edge, usare il punto di montaggio locale. Selezionare **Usa la condivisione con il calcolo Edge** per montare automaticamente la condivisione dopo che è stata creata. Quando questa opzione è selezionata, inoltre, il modulo Edge può usare il calcolo con il punto di montaggio locale.

8. Fare clic su **Crea** per creare la condivisione. Viene ricevuta una notifica che indica che la creazione della condivisione è in corso. Dopo la creazione della condivisione con le impostazioni specificate, il pannello **condivisioni** viene aggiornato per riflettere la nuova condivisione.

## <a name="add-a-local-share"></a>Aggiungere una condivisione locale

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **cloud storage gateway > shares**. Selezionare **+ Aggiungi condivisione** sulla barra dei comandi.

   ![Selezionare Aggiungi condivisione 2](media/azure-stack-edge-manage-shares/add-local-share-1.png)

2. In **Aggiungi condivisione** specificare le impostazioni della condivisione. Specificare un nome univoco per la condivisione.
    
    I nomi di condivisioni possono contenere solo numeri, lettere minuscole e trattini. Il nome condivisione deve avere una lunghezza compresa fra 3 e 63 caratteri e iniziare con una lettera o un numero. Ogni trattino deve essere preceduto e seguito da un carattere diverso da un trattino.

3. Selezionare un **Tipo** per la condivisione. Il tipo può essere **SMB** o **NFS**. L'impostazione predefinita è SMB. SMB è l'opzione standard per i client Windows, mentre NFS è l'opzione usata per i client Linux. Le opzioni visualizzate variano leggermente a seconda che si scelgano condivisioni SMB o NFS.

4. Per accedere facilmente alle condivisioni dai moduli di calcolo Edge, usare il punto di montaggio locale. Selezionare **Usa la condivisione con il calcolo Edge** per consentire al modulo Edge di usare il calcolo con il punto di montaggio locale.

5. Selezionare **Configura come condivisione locale Edge**. I dati nelle condivisioni locali rimarranno in locale nel dispositivo. È possibile elaborare questi dati in locale.

6. Nel campo **Utente locale con tutti i privilegi** selezionare **Crea nuovo** o **Usa esistente**.

7. Selezionare **Crea**. 

   ![Creare una condivisione locale](media/azure-stack-edge-manage-shares/add-local-share-2.png)

    Viene visualizzata una notifica per indicare che la creazione della condivisione è in corso. Dopo la creazione della condivisione con le impostazioni specificate, il pannello **condivisioni** viene aggiornato per riflettere la nuova condivisione.

   ![Visualizzare gli aggiornamenti del pannello Condivisioni](media/azure-stack-edge-manage-shares/add-local-share-3.png)
    


## <a name="mount-a-share"></a>Montare una condivisione

Se è stata creata una condivisione prima di configurare il calcolo nel dispositivo Azure Stack Edge Pro, sarà necessario montare la condivisione. Per montare una condivisione, seguire questa procedura.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **cloud storage gateway > shares**. Nell'elenco delle condivisioni selezionare la condivisione da montare. Per la condivisione selezionata, nella colonna **Usata per il calcolo** verrà visualizzato lo stato **Disabilitata**.

   ![Selezionare Condividi 3](media/azure-stack-edge-manage-shares/select-share-mount.png)

2. Selezionare **Montaggio**.

   ![Selezionare Montaggio](media/azure-stack-edge-manage-shares/select-mount.png)

3. Alla richiesta di conferma selezionare **Sì**. La condivisione verrà montata.

   ![Confermare il montaggio](media/azure-stack-edge-manage-shares/confirm-mount.png)

4. Dopo il montaggio della condivisione, passare all'elenco delle condivisioni. Lo stato della condivisione visualizzato nella colonna **Usata per il calcolo** sarà **Abilitata**.

   ![Condivisione montata](media/azure-stack-edge-manage-shares/share-mounted.png)

5. Selezionare di nuovo la condivisione per visualizzarne il punto di montaggio locale. Il modulo di calcolo Edge usa questo punto di montaggio locale per la condivisione.

   ![Punto di montaggio locale per la condivisione](media/azure-stack-edge-manage-shares/share-mountpoint.png) 

## <a name="unmount-a-share"></a>Smontare una condivisione

Per smontare una condivisione, seguire questa procedura nel portale di Azure.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **cloud storage gateway > shares**.

   ![Selezionare la condivisione 4](media/azure-stack-edge-manage-shares/select-share-unmount.png)

2. Nell'elenco delle condivisioni selezionare la condivisione da smontare. È opportuno assicurarsi che la condivisione che si smonta non venga usata da alcun modulo. Se la condivisione viene usata da un modulo, verranno rilevati problemi con il modulo corrispondente. Selezionare **Smonta**.

   ![Selezionare Smonta](media/azure-stack-edge-manage-shares/select-unmount.png)

3. Alla richiesta di conferma selezionare **Sì**. La condivisione verrà smontata.

   ![Confermare lo smontaggio](media/azure-stack-edge-manage-shares/confirm-unmount.png)

4. Dopo lo smontaggio della condivisione, passare all'elenco delle condivisioni. Lo stato della condivisione visualizzato nella colonna **Usata per il calcolo** sarà **Disabilitata**.

   ![Condivisione smontata](media/azure-stack-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Eliminare una condivisione

Eseguire i passaggi seguenti nel portale di Azure per eliminare una condivisione.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **cloud storage gateway > shares**. Nell'elenco delle condivisioni fare clic sulla condivisione da eliminare.

   ![Selezionare la condivisione 5](media/azure-stack-edge-manage-shares/delete-share-1.png)

2. Selezionare **Elimina**.

   ![Selezionare Elimina](media/azure-stack-edge-manage-shares/delete-share-2.png)

3. Quando viene richiesta la conferma, fare clic su **Sì**.

   ![Conferma dell'eliminazione](media/azure-stack-edge-manage-shares/delete-share-3.png)

L'elenco di condivisioni viene aggiornato in base all'eliminazione.

## <a name="refresh-shares"></a>Aggiornare le condivisioni

La funzionalità di aggiornamento consente di aggiornare il contenuto di una condivisione. Quando si aggiorna una condivisione, viene avviata una ricerca per trovare tutti gli oggetti Azure, inclusi BLOB e file che sono stati aggiunti al cloud dopo l'ultimo aggiornamento. Questi file aggiuntivi vengono quindi scaricati per aggiornare il contenuto della condivisione nel dispositivo.

> [!IMPORTANT]
>
> - Non è possibile aggiornare le condivisioni locali.
> - Le autorizzazioni e gli elenchi di controllo di accesso (ACL) non vengono mantenuti tra le operazioni di aggiornamento. 

Eseguire i passaggi seguenti nel portale di Azure per aggiornare una condivisione.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **cloud storage gateway > shares**. Fare clic sulla condivisione che si vuole aggiornare.

   ![Selezionare Condividi 6](media/azure-stack-edge-manage-shares/refresh-share-1.png)

2. Selezionare **Aggiorna dati**.

   ![Selezionare Aggiorna](media/azure-stack-edge-manage-shares/refresh-share-2.png)
 
3. Alla richiesta di conferma selezionare **Sì**. Viene avviato un processo di aggiornamento del contenuto della condivisione locale.

   ![Confermare l'aggiornamento](media/azure-stack-edge-manage-shares/refresh-share-3.png)

4. Durante l'aggiornamento, l'opzione di aggiornamento non è disponibile nel menu di scelta rapida. Fare clic sulla notifica del processo per visualizzare lo stato del processo di aggiornamento.

5. Il tempo necessario per aggiornare dipende dal numero di file nel contenitore di Azure, nonché dai file nel dispositivo. Dopo aver completato l'aggiornamento, viene aggiornato il timestamp di condivisione. Anche se l'aggiornamento include gli errori parziali, l'operazione è considerata riuscita e il timestamp viene aggiornato. Anche i log degli errori di aggiornamento vengono aggiornati.

   ![Timestamp aggiornato](media/azure-stack-edge-manage-shares/refresh-share-4.png)
 
Se si verifica un errore, viene generato un avviso. L'avviso illustra in dettaglio la causa e i suggerimenti per correggere il problema. L'avviso contiene anche collegamenti a un file con il riepilogo completo degli errori, inclusi i file che non è stato possibile aggiornare o eliminare.

## <a name="sync-storage-keys"></a>Sincronizzare le chiavi di archiviazione

Se le chiavi dell'account di archiviazione sono state ruotate, sarà necessario sincronizzare le chiavi di accesso alle risorse di archiviazione. La sincronizzazione consente al dispositivo di ottenere le chiavi più recenti per l'account di archiviazione.

Eseguire i passaggi seguenti nel portale di Azure per sincronizzare la chiave di accesso alle risorse di archiviazione.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **cloud storage gateway > shares**. Nell'elenco delle condivisioni, scegliere e fare clic su una condivisione associata all'account di archiviazione che è necessario sincronizzare.

    ![Selezionare la condivisione con l'account di archiviazione appropriato](media/azure-stack-edge-manage-shares/sync-storage-key-1.png)

2. Fare clic su **Sincronizza chiavi di archiviazione**. Fare clic su **Sì** alla richiesta di conferma.

     ![Selezionare Sincronizza chiavi di archiviazione](media/azure-stack-edge-manage-shares/sync-storage-key-2.png)

3. Uscire dalla finestra di dialogo dopo aver completato la sincronizzazione.

>[!NOTE]
> È sufficiente eseguire questa operazione una sola volta per un account di archiviazione specificato. Non è necessario ripetere questa azione per tutte le condivisioni associate allo stesso account di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire gli utenti dal portale di Azure](azure-stack-edge-manage-users.md).