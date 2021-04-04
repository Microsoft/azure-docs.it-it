---
title: Montare una condivisione file NFS di Azure-File di Azure
description: Informazioni su come montare una condivisione del file System di rete.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8a993d9c1de35132198de5e3becc4f16d6a2a437
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621298"
---
# <a name="how-to-mount-an-nfs-file-share"></a>Come montare una condivisione file NFS

[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate nelle distribuzioni di Linux usando il protocollo SMB (Server Message Block Protocol) o NFS (Network File System). Questo articolo è incentrato sul montaggio con NFS. per informazioni dettagliate sul montaggio con SMB, vedere [usare file di Azure con Linux](storage-how-to-use-files-linux.md). Per informazioni dettagliate su ogni protocollo disponibile, vedere [protocolli di condivisione file di Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limitazioni

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Prerequisiti

- [Creare una condivisione NFS](storage-files-how-to-create-nfs-shares.md).

    > [!IMPORTANT]
    > È possibile accedere alle condivisioni NFS solo da reti attendibili. Le connessioni alla condivisione NFS devono avere origine da una delle origini seguenti:

- Usare una delle soluzioni di rete seguenti:
    - È possibile [creare un endpoint privato](storage-files-networking-endpoints.md#create-a-private-endpoint) (scelta consigliata) o [limitare l'accesso all'endpoint pubblico](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configurare una VPN da punto a sito (P2S) in Linux per l'uso con file di Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configurare una VPN da sito a sito per l'uso con file di Azure](storage-files-configure-s2s-vpn.md).
    - Configurare [ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="disable-secure-transfer"></a>Disabilitare il trasferimento sicuro

1. Accedere al portale di Azure e accedere all'account di archiviazione che contiene la condivisione NFS creata.
1. Selezionare **Configurazione**.
1. Selezionare **disabled** for **Secure Transfer required**.
1. Selezionare **Salva**.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="Screenshot della schermata di configurazione dell'account di archiviazione con trasferimento sicuro disabilitato.":::

## <a name="mount-an-nfs-share"></a>Montare una condivisione NFS

1. Una volta creata la condivisione file, selezionare la condivisione e selezionare **Connetti da Linux**.
1. Immettere il percorso di montaggio che si vuole usare, quindi copiare lo script.
1. Connettersi al client e usare lo script di montaggio fornito.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="Screenshot del pannello di connessione alla condivisione file":::

A questo punto è stata montata la condivisione NFS.

### <a name="validate-connectivity"></a>Convalidare la connettività

Se il montaggio non è riuscito, è possibile che l'endpoint privato non sia stato configurato correttamente o non sia accessibile. Per informazioni dettagliate sulla conferma della connettività, vedere la sezione [verificare la connettività](storage-files-networking-endpoints.md#verify-connectivity) dell'articolo endpoint di rete.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su File di Azure, vedere l'articolo [pianificazione di una distribuzione di file di Azure](storage-files-planning.md).
- Se si verificano problemi, vedere [risolvere i problemi relativi alle condivisioni file NFS di Azure](storage-troubleshooting-files-nfs.md).