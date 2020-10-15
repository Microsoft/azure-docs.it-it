---
title: includere file
description: includere file
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 30bbd06e36ed1e03caa391165a8abc275f1899a7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87102748"
---
Per eliminare un contenitore di volumi, è necessario
 - eliminare i volumi nel contenitore di volumi. Se il contenitore del volume ha volumi associati, portarli prima offline. Seguire la procedura illustrata in [Portare un volume offline](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Quando i volumi sono offline, è possibile eliminarli. 
 - Eliminare i criteri di backup e gli snapshot cloud associati. Controllare se al contenitore di volumi sono associati criteri di backup e snapshot cloud. In caso affermativo, [eliminare i criteri di backup](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Questa operazione eliminerà anche gli snapshot cloud. 
 
Quando al contenitore di volumi non sono associati volumi, criteri di backup e snapshot cloud, è possibile eliminarlo. Seguire questa procedura per eliminare un contenitore del volume.

#### <a name="to-delete-a-volume-container"></a>Per eliminare un contenitore di volumi
1. Passare al servizio Gestione dispositivi StorSimple e fare clic su **Dispositivi**. Selezionare e fare clic sul dispositivo e quindi andare a **Impostazioni > Gestione > Contenitori dei volumi**.

    ![Pannello Contenitori dei volumi](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Nell'elenco tabulare di contenitori dei volumi selezionare il contenitore del volume che si vuole eliminare, fare clic con il pulsante destro del mouse su **...** e quindi scegliere **Elimina**.

    ![Eliminare un contenitore dei volumi](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Se a un contenitore di volumi non sono associati volumi, criteri di backup e snapshot cloud, è possibile eliminarlo. Quando viene richiesta la conferma, rivedere e selezionare la casella di controllo indicante l'impatto dell'eliminazione del contenitore del volume. Fare clic su **Elimina** per eliminare il contenitore del volume.

    ![Confermare l'eliminazione](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

L'elenco di contenitori dei volumi viene aggiornato per rispecchiare l'eliminazione del contenitore del volume.

![Screenshot della pagina del contenitore del volume. L'elenco tabulare di contenitori di volumi non contiene più il contenitore eliminato.](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


