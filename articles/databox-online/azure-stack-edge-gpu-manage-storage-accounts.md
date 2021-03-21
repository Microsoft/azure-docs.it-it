---
title: Gestione degli account di archiviazione della GPU Pro Azure Stack Edge | Microsoft Docs
description: Viene descritto come usare la portale di Azure per gestire l'account di archiviazione nel Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 2d9520c8f97171dbf2f46aa2c94b9e1e280ed86c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201271"
---
# <a name="use-the-azure-portal-to-manage-edge-storage-accounts-on-your-azure-stack-edge-pro"></a>Usare il portale di Azure per gestire gli account di archiviazione di Edge in Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo descrive come gestire gli account di archiviazione perimetrali in Azure Stack Edge Pro. È possibile gestire il Azure Stack Edge Pro tramite la portale di Azure o tramite l'interfaccia utente Web locale. Usare il portale di Azure per aggiungere o eliminare gli account di archiviazione perimetrali nel dispositivo.

## <a name="about-edge-storage-accounts"></a>Informazioni sugli account di archiviazione perimetrale

È possibile trasferire i dati dal dispositivo Azure Stack Edge Pro tramite i protocolli SMB, NFS o REST. Per trasferire i dati nell'archiviazione BLOB usando le API REST, è necessario creare account di archiviazione perimetrale sul Azure Stack Edge Pro. 

Gli account di archiviazione perimetrali aggiunti nel dispositivo Azure Stack Edge Pro vengono mappati agli account di archiviazione di Azure. Tutti i dati scritti negli account di archiviazione perimetrale vengono automaticamente inseriti nel cloud.

Di seguito è riportato un diagramma che illustra in dettaglio i due tipi di account e la modalità di flusso dei dati da ognuno di questi account ad Azure:

![Diagramma per gli account di archiviazione BLOB](media/azure-stack-edge-gpu-manage-storage-accounts/ase-blob-storage.svg)

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Aggiungere un account di archiviazione Edge
> * Eliminare un account di archiviazione perimetrale


## <a name="add-an-edge-storage-account"></a>Aggiungere un account di archiviazione Edge

Per creare un account di archiviazione Edge, seguire questa procedura:

[!INCLUDE [Add an Edge storage account](../../includes/azure-stack-edge-gateway-add-storage-account.md)]

## <a name="delete-an-edge-storage-account"></a>Eliminare un account di archiviazione perimetrale

Per eliminare un account di archiviazione perimetrale, seguire questa procedura.

1. Passare a **Configuration > account di archiviazione** nella risorsa. Dall'elenco degli account di archiviazione selezionare l'account di archiviazione che si vuole eliminare. Dalla barra dei comandi superiore selezionare **Elimina account di archiviazione**.

    ![Vai all'elenco degli account di archiviazione](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-1.png)

2. Nel pannello **Elimina account di archiviazione** verificare l'account di archiviazione da eliminare e selezionare **Elimina**.

    ![Confermare ed eliminare un account di archiviazione](media/azure-stack-edge-gpu-manage-storage-accounts/delete-edge-storage-account-2.png)

L'elenco degli account di archiviazione viene aggiornato per riflettere l'eliminazione.


## <a name="add-delete-a-container"></a>Aggiunta, eliminazione di un contenitore

È anche possibile aggiungere o eliminare i contenitori per questi account di archiviazione.

Per aggiungere un contenitore, seguire questa procedura:

1. Selezionare l'account di archiviazione che si vuole gestire. Dalla barra dei comandi superiore selezionare **+ Aggiungi contenitore**.

    ![Selezionare l'account di archiviazione per aggiungere il contenitore](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-1.png)

2. Specificare un nome per il contenitore. Questo contenitore viene creato nell'account di archiviazione perimetrale, oltre che nell'account di archiviazione di Azure mappato a questo account. 

    ![Aggiungi contenitore Edge](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-2.png)

L'elenco dei contenitori viene aggiornato in modo da riflettere il contenitore appena aggiunto.

![Elenco aggiornato di contenitori](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-4.png)

È ora possibile selezionare un contenitore da questo elenco e selezionare **+ Elimina contenitore** dalla barra dei comandi superiore. 

![Eliminare un contenitore](media/azure-stack-edge-gpu-manage-storage-accounts/add-container-3.png)

## <a name="sync-storage-keys"></a>Sincronizzare le chiavi di archiviazione

È possibile sincronizzare le chiavi di accesso per gli account di archiviazione perimetrali (locali) nel dispositivo. 

Per sincronizzare la chiave di accesso dell'account di archiviazione, seguire questa procedura:

1. Nella risorsa selezionare l'account di archiviazione che si vuole gestire. Dalla barra dei comandi superiore selezionare **Sincronizza chiave di archiviazione**.

    ![Selezionare la chiave di archiviazione di sincronizzazione](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-1.png)

2. Alla richiesta di conferma selezionare **Sì**.

    ![Selezionare Sync storage Key 2](media/azure-stack-edge-gpu-manage-storage-accounts/sync-storage-key-2.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [gestire gli utenti dal portale di Azure](azure-stack-edge-gpu-manage-users.md).
