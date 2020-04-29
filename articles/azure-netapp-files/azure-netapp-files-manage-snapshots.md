---
title: Gestire gli snapshot tramite Azure NetApp Files | Microsoft Docs
description: Questo articolo descrive come creare snapshot per un volume o come eseguire il ripristino da uno snapshot a un nuovo volume tramite Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: 48055a774808aea86452e8410b7e717f5019d172
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78267910"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gestire gli snapshot tramite Azure NetApp Files

È possibile usare Azure NetApp Files per creare manualmente uno snapshot su richiesta per un volume o un ripristino da uno snapshot in un nuovo volume. Il servizio Azure NetApp Files non crea automaticamente snapshot del volume.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Creare uno snapshot su richiesta per un volume

Gli snapshot possono essere creati solo su richiesta. Al momento non sono supportati i criteri degli snapshot.

1.  Nel pannello Volume fare clic su **Snapshot**.

    ![Passare agli snapshot](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Fare clic su **+ Aggiungi snapshot** per creare uno snapshot su richiesta per un volume.

    ![Aggiungi snapshot](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Nella finestra Nuovo snapshot, specificare un nome per il nuovo snapshot che si sta creando.   

    ![Nuovo snapshot](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Fare clic su **OK**. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Ripristinare uno snapshot in un nuovo volume

Attualmente, è possibile ripristinare uno snapshot solo in un nuovo volume. 
1. Andare al pannello **Manage Snapshots** (Gestisci snapshot) nel pannello Volume per visualizzare l'elenco degli snapshot. 
2. Selezionare lo snapshot da ripristinare.  
3. Fare clic con il destro del mouse sul nome dello snapshot e selezionare **Ripristina in nuovo volume** dall'opzione di menu.  

    ![Ripristinare snapshot in un nuovo volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Nella finestra Nuovo volume, fornire le informazioni relative al nuovo volume:  
    * **Nome**   
        Specificare il nome per il volume che si sta creando.  
        
        Il nome deve essere univoco all'interno di un gruppo di risorse. Deve essere composto da almeno tre caratteri.  È possibile usare qualsiasi carattere alfanumerico.

    * **Percorso file**     
        Specificare il percorso del file che verrà usato per creare il percorso di esportazione per il nuovo volume. Il percorso di esportazione viene usato per montare il volume e accedervi.   
        
        Una destinazione di montaggio è l'endpoint dell'indirizzo IP del servizio NFS. Viene generata automaticamente.   
        
        Il nome di percorso di file può contenere solo lettere, numeri e trattini ("-"). Il nome deve avere una lunghezza compresa tra 16 e 40 caratteri. 

    * **Quota**  
        Specificare la quantità di spazio di archiviazione logico allocato al volume.  

        Il campo **Quota disponibile** mostra la quantità di spazio inutilizzato nel pool di capacità scelto che è possibile usare per la creazione di un nuovo volume. Le dimensioni del nuovo volume non devono superare la quota disponibile.

    *   **Rete virtuale**  
        Specificare la rete virtuale di Azure da cui si vuole accedere al volume.  
        Per la rete virtuale specificata è necessario delegare una subnet ad Azure NetApp Files. Azure NetApp Files è accessibile solo dalla stessa rete virtuale o da una rete virtuale presente nella stessa area del volume tramite il peering delle reti virtuali. È possibile accedere al volume dalla rete locale tramite Express route. 

    * **Subnet**  
        Specificare la subnet desiderata per il volume.  
        La subnet specificata deve essere delegata al servizio Azure NetApp Files. È possibile creare una nuova subnet selezionando **Crea nuovo** sotto il campo Subnet.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Fare clic su **OK**.   
    Il nuovo volume in cui viene ripristinato lo snapshot viene visualizzato nel pannello Volumi.

## <a name="next-steps"></a>Passaggi successivi

[Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
