---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 746198f87e23cd7aca2a3177c23974917cb4b12a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025935"
---
#### <a name="to-create-a-new-service"></a>Per creare un nuovo servizio

1. Usare le credenziali dell'account Microsoft per accedere al [portale di Azure](https://portal.azure.com/).

2. Nel portale di Azure fare clic su **Crea una risorsa** e quindi fare clic su **Visualizza tutto** nel Marketplace.

    ![Creare Gestione dispositivi StorSimple](./media/storsimple-8000-create-new-service/createssdevman1.png)

    Cercare _StorSimple Physical_ (Dispositivi fisici StorSimple). Selezionare e fare clic su **Serie di dispositivi fisici StorSimple** e quindi fare clic su **Crea**. In alternativa, nella portale di Azure fare clic su **+** e quindi in **archiviazione** fare clic su **serie di dispositivi fisici StorSimple**.

    ![Creazione di StorSimple Gestione dispositivi 2](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. Nel pannello **Gestione dispositivi StorSimple** seguire questa procedura:

   1. Specificare un **Nome della risorsa** univoco per il servizio. Si tratta di un nome descrittivo che può essere usato per identificare il servizio. Il nome può contenere da 2 a 50 caratteri che possono essere lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero.

   2. Selezionare una sottoscrizione nell'elenco a discesa **Sottoscrizione**. La sottoscrizione viene collegata all'account di fatturazione. Questo campo non è presente se si dispone di una sola sottoscrizione.

   3. Per **Gruppo di risorse**, selezionare **Usa esistente** o **Crea nuovo**. Per altre informazioni, vedere [Azure resource groups](../articles/azure-resource-manager/management/manage-resource-groups-portal.md) (Gruppi di risorse di Azure).

   4. In **Località** specificare una località per il servizio. In generale, scegliere un percorso più vicino all'area geografica in cui si vuole distribuire il dispositivo. È anche possibile tenere in considerazione quanto segue:

      * Se si dispone di carichi di lavoro esistenti in Azure che si intende distribuire con il dispositivo StorSimple, usare tale data center.
      * Il servizio Gestione dispositivi StorSimple e la risorsa di archiviazione di Azure possono trovarsi in due posizioni distinte. In tal caso, è necessario creare l'account di Gestione dispositivi StorSimple e di Archiviazione di Azure separatamente. Per creare un account di archiviazione di Azure, passare al servizio Archiviazione di Azure nel portale di Azure e seguire la procedura descritta in [Creare un account di archiviazione](../articles/storage/common/storage-account-create.md). Dopo aver creato l'account, aggiungerlo al servizio Gestione dispositivi StorSimple seguendo la procedura descritta in [Configurare un nuovo account di archiviazione per il servizio](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service).

   5. Selezionare **Crea un nuovo account di archiviazione** per creare automaticamente un account di archiviazione con il servizio. Specificare un nome per questo account di archiviazione. Se è necessario che i dati siano in un percorso diverso, deselezionare questa casella.

   6. Selezionare **Aggiungi al dashboard** se si vuole inserire un collegamento rapido al servizio nel dashboard.

   7. Fare clic su **Crea** per creare il servizio Gestione dispositivi StorSimple.

       ![Creazione di StorSimple Gestione dispositivi 3](./media/storsimple-8000-create-new-service/createssdevman2.png)

La creazione del servizio richiede alcuni minuti. Al termine della creazione del servizio, verrà visualizzata una notifica e verrà aperto il pannello del nuovo servizio.

![Creazione di StorSimple Gestione dispositivi 4](./media/storsimple-8000-create-new-service/createssdevman5.png)