---
title: Gestione delle condivisioni GPU Pro Azure Stack Edge | Microsoft Docs
description: Viene descritto come usare la portale di Azure per gestire le condivisioni sulla GPU di Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 8fbf765168fd848a2ae349badf4017289b5a4380
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952268"
---
# <a name="use-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>Usare portale di Azure per gestire le condivisioni nella versione Pro di Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Questo articolo descrive come gestire le condivisioni in Azure Stack Edge Pro. È possibile gestire il Azure Stack Edge Pro tramite la portale di Azure o tramite l'interfaccia utente Web locale. Usare il portale di Azure per aggiungere, eliminare o aggiornare le condivisioni o per sincronizzare la chiave di archiviazione dell'account di archiviazione associato alle condivisioni.

## <a name="about-shares"></a>Informazioni sulle condivisioni

Per trasferire i dati in Azure, è necessario creare condivisioni sul Azure Stack Edge Pro. Le condivisioni aggiunte al dispositivo Azure Stack Edge Pro possono essere condivisioni locali o condivisioni che effettuano il push dei dati nel cloud.

 - **Condivisioni locali**: usare queste condivisioni quando si desidera che i dati vengano elaborati localmente nel dispositivo.
 - **Condivisioni**: usare queste condivisioni quando si desidera che i dati del dispositivo vengano inseriti automaticamente nell'account di archiviazione nel cloud. Tutte le funzioni cloud, ad esempio le **chiavi di archiviazione** di **aggiornamento** e sincronizzazione, si applicano alle condivisioni.


## <a name="add-a-share"></a>Aggiungere una condivisione

Eseguire i passaggi seguenti nel portale di Azure per creare una condivisione.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **Gateway > condivisioni**. Selezionare **+ Aggiungi condivisione** sulla barra dei comandi.

    ![Selezionare Aggiungi condivisione](media/azure-stack-edge-j-series-manage-shares/add-share-1.png)

2. In **Aggiungi condivisione** specificare le impostazioni della condivisione. Specificare un nome univoco per la condivisione.
    
    I nomi di condivisioni possono contenere solo numeri, lettere minuscole e trattini. Il nome condivisione deve avere una lunghezza compresa fra 3 e 63 caratteri e iniziare con una lettera o un numero. Ogni trattino deve essere preceduto e seguito da un carattere diverso da un trattino.

3. Selezionare un **Tipo** per la condivisione. Il tipo può essere **SMB** o **NFS**. L'impostazione predefinita è SMB. SMB è l'opzione standard per i client Windows, mentre NFS è l'opzione usata per i client Linux. Le opzioni visualizzate variano leggermente a seconda che si scelgano condivisioni SMB o NFS.

4. Specificare un **account di archiviazione** in cui risiede la condivisione. Nell'account di archiviazione viene creato un contenitore con il nome della condivisione, se non esiste già. Se esiste già un contenitore, viene usato quello.

5. Dall'elenco a discesa scegliere il **servizio di archiviazione** da BLOB in blocchi, BLOB di pagine o file. Il tipo di servizio scelto dipende dal formato che si vuole applicare ai dati che risiederanno in Azure. Ad esempio, in questo caso, si vuole che i dati si trovino come BLOB in blocchi in Azure, quindi si seleziona **BLOB in blocchi**. Se si sceglie il **BLOB di pagine**, è necessario assicurarsi che i dati siano allineati a 512 byte. Usare il **BLOB di pagine** per i dischi rigidi virtuali o VHDX sempre allineati a 512 byte.

6. Questo passaggio varia a seconda che si stia creando una condivisione SMB o NFS.
    - **Se si crea una condivisione SMB**: nel campo **Utente locale con tutti i privilegi** selezionare **Crea nuovo** o **Usa esistente**. Se si crea un nuovo utente locale, compilare i campi **Nome utente**, **Password** e Conferma password. Vengono così assegnate le autorizzazioni all'utente locale. Dopo aver assegnato le autorizzazioni in questa fase, è possibile modificarle con Esplora file.

        ![Aggiunta di una condivisione SMB](media/azure-stack-edge-j-series-manage-shares/add-smb-share.png)

        Se si seleziona Consenti solo operazioni di lettura per i dati di questa condivisione, sarà possibile specificare utenti di sola lettura.
    - **Se si crea una condivisione NFS**: occorre specificare l'**indirizzo IP dei client autorizzati** ad accedere alla condivisione.

        ![Aggiunta di una condivisione NFS](media/azure-stack-edge-j-series-manage-shares/add-nfs-share.png)

7. Per accedere facilmente alle condivisioni dai moduli di calcolo Edge, usare il punto di montaggio locale. Selezionare **Usa la condivisione con il calcolo Edge** per montare automaticamente la condivisione dopo che è stata creata. Quando questa opzione è selezionata, inoltre, il modulo Edge può usare il calcolo con il punto di montaggio locale.

8. Fare clic su **Crea** per creare la condivisione. Viene ricevuta una notifica che indica che la creazione della condivisione è in corso. Dopo la creazione della condivisione con le impostazioni specificate, il pannello **condivisioni** viene aggiornato per riflettere la nuova condivisione.

## <a name="add-a-local-share"></a>Aggiungere una condivisione locale

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **Gateway > condivisioni**. Selezionare **+ Aggiungi condivisione** sulla barra dei comandi.

    ![Selezionare Aggiungi condivisione 2](media/azure-stack-edge-j-series-manage-shares/add-local-share-1.png)

2. In **Aggiungi condivisione** specificare le impostazioni della condivisione. Specificare un nome univoco per la condivisione.
    
    I nomi delle condivisioni possono contenere solo numeri, lettere minuscole e maiuscole e trattini. Il nome condivisione deve avere una lunghezza compresa fra 3 e 63 caratteri e iniziare con una lettera o un numero. Ogni trattino deve essere preceduto e seguito da un carattere diverso da un trattino.

3. Selezionare un **Tipo** per la condivisione. Il tipo può essere **SMB** o **NFS**. L'impostazione predefinita è SMB. SMB è l'opzione standard per i client Windows, mentre NFS è l'opzione usata per i client Linux. Le opzioni visualizzate variano leggermente a seconda che si scelgano condivisioni SMB o NFS.

   > [!IMPORTANT]
   > Verificare che per l'account di archiviazione di Azure in uso non siano impostati criteri di immutabilità se lo si usa con un dispositivo Azure Stack Edge Pro o Data Box Gateway. Per altre informazioni, vedere [Impostare e gestire i criteri di immutabilità per l'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

4. Per accedere facilmente alle condivisioni dai moduli di calcolo Edge, usare il punto di montaggio locale. Selezionare **Usa la condivisione con il calcolo Edge** per consentire al modulo Edge di usare il calcolo con il punto di montaggio locale.

5. Selezionare **Configura come condivisione locale Edge**. I dati nelle condivisioni locali rimarranno in locale nel dispositivo. È possibile elaborare questi dati in locale.

6. Nel campo **Utente locale con tutti i privilegi** selezionare **Crea nuovo** o **Usa esistente**.

7. Selezionare **Create** (Crea). 

    ![Creare una condivisione locale](media/azure-stack-edge-j-series-manage-shares/add-local-share-2.png)

    Viene visualizzata una notifica per indicare che la creazione della condivisione è in corso. Dopo la creazione della condivisione con le impostazioni specificate, il pannello **condivisioni** viene aggiornato per riflettere la nuova condivisione.

    ![Visualizzare gli aggiornamenti del pannello Condivisioni](media/azure-stack-edge-j-series-manage-shares/add-local-share-3.png)
    
    Selezionare la condivisione per visualizzare il punto di montaggio locale per i moduli di calcolo Edge relativi a questa condivisione.

    ![Visualizzare i dettagli della condivisione locale](media/azure-stack-edge-j-series-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Montare una condivisione

Se è stata creata una condivisione prima di configurare il calcolo nel dispositivo Azure Stack Edge Pro, sarà necessario montare la condivisione. Per montare una condivisione, seguire questa procedura.


1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **Gateway > condivisioni**. Nell'elenco delle condivisioni selezionare la condivisione da montare. Per la condivisione selezionata, nella colonna **Usata per il calcolo** verrà visualizzato lo stato **Disabilitata**.

    ![Selezionare condivisione](media/azure-stack-edge-j-series-manage-shares/mount-share-1.png)

2. Selezionare **Montaggio**.

    ![Selezionare Montaggio](media/azure-stack-edge-j-series-manage-shares/mount-share-2.png)

3. Alla richiesta di conferma selezionare **Sì**. La condivisione verrà montata.

    ![Confermare il montaggio](media/azure-stack-edge-j-series-manage-shares/mount-share-3.png)

4. Dopo il montaggio della condivisione, passare all'elenco delle condivisioni. Lo stato della condivisione visualizzato nella colonna **Usata per il calcolo** sarà **Abilitata**.

    ![Condivisione montata](media/azure-stack-edge-j-series-manage-shares/mount-share-4.png)

5. Selezionare di nuovo la condivisione per visualizzarne il punto di montaggio locale. Il modulo di calcolo Edge usa questo punto di montaggio locale per la condivisione.

    ![Punto di montaggio locale per la condivisione](media/azure-stack-edge-j-series-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>Smontare una condivisione

Per smontare una condivisione, seguire questa procedura nel portale di Azure.

1. Nella portale di Azure passare alla risorsa di Azure Stack Edge, quindi passare a **Gateway > condivisioni**.

    ![Selezionare Condividi 2](media/azure-stack-edge-j-series-manage-shares/unmount-share-1.png)

2. Nell'elenco delle condivisioni selezionare la condivisione da smontare. È opportuno assicurarsi che la condivisione che si smonta non venga usata da alcun modulo. Se la condivisione viene usata da un modulo, verranno rilevati problemi con il modulo corrispondente. Selezionare **Smonta**.

    ![Selezionare Smonta](media/azure-stack-edge-j-series-manage-shares/unmount-share-2.png)

3. Alla richiesta di conferma selezionare **Sì**. La condivisione verrà smontata.

    ![Confermare lo smontaggio](media/azure-stack-edge-j-series-manage-shares/unmount-share-3.png)

4. Dopo lo smontaggio della condivisione, passare all'elenco delle condivisioni. Lo stato della condivisione visualizzato nella colonna **Usata per il calcolo** sarà **Disabilitata**.

    ![Condivisione smontata](media/azure-stack-edge-j-series-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>Eliminare una condivisione

Eseguire i passaggi seguenti nel portale di Azure per eliminare una condivisione.

1. Nell'elenco delle condivisioni fare clic sulla condivisione da eliminare.

    ![Selezionare Condividi 3](media/azure-stack-edge-j-series-manage-shares/delete-share-1.png)

2. Fare clic su **Elimina**.

    ![Clic su Elimina](media/azure-stack-edge-j-series-manage-shares/delete-share-2.png)

3. Quando viene richiesta la conferma, fare clic su **Sì**.

    ![Conferma dell'eliminazione](media/azure-stack-edge-j-series-manage-shares/delete-share-3.png)

L'elenco di condivisioni viene aggiornato in base all'eliminazione.

## <a name="refresh-shares"></a>Aggiornare le condivisioni

La funzionalità di aggiornamento consente di aggiornare il contenuto di una condivisione. Quando si aggiorna una condivisione, viene avviata una ricerca per trovare tutti gli oggetti Azure, inclusi BLOB e file che sono stati aggiunti al cloud dopo l'ultimo aggiornamento. Questi file aggiuntivi vengono quindi scaricati per aggiornare il contenuto della condivisione nel dispositivo.

> [!IMPORTANT]
> - Non è possibile aggiornare le condivisioni locali.
> - Le autorizzazioni e gli elenchi di controllo di accesso (ACL) non vengono mantenuti tra le operazioni di aggiornamento. 

Eseguire i passaggi seguenti nel portale di Azure per aggiornare una condivisione.

1.  Passare a **Condivisioni** nel portale di Azure. Fare clic sulla condivisione che si vuole aggiornare.

    ![Selezionare la condivisione 4](media/azure-stack-edge-j-series-manage-shares/refresh-share-1.png)

2.  Fare clic su **Aggiorna**. 

    ![Fare clic su Aggiorna](media/azure-stack-edge-j-series-manage-shares/refresh-share-2.png)
 
3.  Quando viene richiesta la conferma, fare clic su **Sì**. Viene avviato un processo di aggiornamento del contenuto della condivisione locale.

    ![Confermare l'aggiornamento](media/azure-stack-edge-j-series-manage-shares/refresh-share-3.png)
 
4.   Durante l'aggiornamento, l'opzione di aggiornamento non è disponibile nel menu di scelta rapida. Fare clic sulla notifica del processo per visualizzare lo stato del processo di aggiornamento.

5.   Il tempo necessario per aggiornare dipende dal numero di file nel contenitore di Azure, nonché dai file nel dispositivo. Dopo aver completato l'aggiornamento, viene aggiornato il timestamp di condivisione. Anche se l'aggiornamento include gli errori parziali, l'operazione è considerata riuscita e il timestamp viene aggiornato. Anche i log degli errori di aggiornamento vengono aggiornati.

![Timestamp aggiornato](media/azure-stack-edge-j-series-manage-shares/refresh-share-4.png)
 
Se si verifica un errore, viene generato un avviso. L'avviso illustra in dettaglio la causa e i suggerimenti per correggere il problema. L'avviso contiene anche collegamenti a un file con il riepilogo completo degli errori, inclusi i file che non è stato possibile aggiornare o eliminare.

## <a name="sync-pinned-files"></a>Sincronizzare i file bloccati 

Per sincronizzare automaticamente i file bloccati, attenersi alla procedura seguente nel portale di Azure:
 
1. Selezionare un account di archiviazione di Azure esistente. 

2. Passare a **contenitori** e selezionare **+ contenitore** per creare un contenitore. Assegnare al contenitore il nome *newContainer*. Impostare il **livello di accesso pubblico** sul contenitore.

    ![Sincronizzazione automatica per i file aggiunti 1](media/azure-stack-edge-j-series-manage-shares/image-1.png)

3. Selezionare il nome del contenitore e impostare i metadati seguenti:  

    - Nome = "aggiunto" 
    - Value = "true" 

    ![Sincronizzazione automatica per i file aggiunti 2](media/azure-stack-edge-j-series-manage-shares/image-2.png)
 
4. Creare una nuova condivisione nel dispositivo. Eseguirne il mapping al contenitore bloccato scegliendo l'opzione contenitore esistente. Contrassegnare la condivisione come di sola lettura. Creare un nuovo utente e specificare il nome utente e una password corrispondente per questa condivisione.  

    ![Sincronizzazione automatica per file bloccati 3](media/azure-stack-edge-j-series-manage-shares/image-3.png)
 
5. Dal portale di Azure passare al contenitore creato. Caricare il file che si desidera aggiungere al newContainer con i metadati impostati su bloccato. 

6. Selezionare **Aggiorna dati** in portale di Azure per il dispositivo per scaricare i criteri di aggiunta per il contenitore di archiviazione di Azure specifico.  

    ![Sincronizzazione automatica per i file aggiunti 4](media/azure-stack-edge-j-series-manage-shares/image-4.png)
 
7. Accedere alla nuova condivisione creata nel dispositivo. Il file caricato nell'account di archiviazione viene ora scaricato nella condivisione locale. 

    Ogni volta che il dispositivo è disconnesso o riconnesso, attiva l'aggiornamento. L'aggiornamento dedurrà solo i file che sono stati modificati. 


## <a name="sync-storage-keys"></a>Sincronizzare le chiavi di archiviazione

Se le chiavi dell'account di archiviazione sono state ruotate, sarà necessario sincronizzare le chiavi di accesso alle risorse di archiviazione. La sincronizzazione consente al dispositivo di ottenere le chiavi più recenti per l'account di archiviazione.

Eseguire i passaggi seguenti nel portale di Azure per sincronizzare la chiave di accesso alle risorse di archiviazione.

1. Passare a **Panoramica** nella risorsa. Nell'elenco delle condivisioni, scegliere e fare clic su una condivisione associata all'account di archiviazione che è necessario sincronizzare.

    ![Selezionare la condivisione con l'account di archiviazione appropriato](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-1.png)

2. Fare clic su **Sincronizza chiavi di archiviazione**. Fare clic su **Sì** alla richiesta di conferma.

     ![Selezionare Sincronizza chiavi di archiviazione](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-2.png)

3. Uscire dalla finestra di dialogo dopo aver completato la sincronizzazione.

>[!NOTE]
> È sufficiente eseguire questa operazione una sola volta per un account di archiviazione specificato. Non è necessario ripetere questa azione per tutte le condivisioni associate allo stesso account di archiviazione.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire gli utenti dal portale di Azure](azure-stack-edge-j-series-manage-users.md).
