---
title: Architetture di riferimento del rendering di Azure
description: Architetture per l'uso di Azure Batch e altri servizi di Azure per estendere un farm di rendering locale tramite burst nel cloud
ms.date: 02/07/2019
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: 784fd89c3dea88e25a2058713897c7a655c8a3af
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726520"
---
# <a name="reference-architectures-for-azure-rendering"></a>Architetture di riferimento per il rendering di Azure

Questo articolo illustra i diagrammi di architettura di alto livello per gli scenari in cui estendere, o "eseguire il burst", il farm di rendering locale in Azure. Gli esempi includono opzioni diverse per i servizi di calcolo, rete e archiviazione di Azure.

## <a name="hybrid-with-nfs-or-cfs"></a>Configurazione ibrida con NFS o CFS

Il diagramma seguente illustra uno scenario ibrido che include i servizi di Azure seguenti:

* **Calcolo**: pool di Azure Batch o set di scalabilità di macchine virtuali.

* **Rete** - Locale: Azure ExpressRoute o VPN. Azure: rete virtuale di Azure.

* **Archiviazione** - File di input e output: NFS o CFS con macchine virtuali di Azure, sincronizzati con l'archiviazione locale tramite Sincronizzazione file di Azure o RSync. In alternativa: Avere vFXT per inserire o trasferire i file da dispositivi NAS locali con NFS.

  ![Bursting del cloud: configurazione ibrida con NFS o CFS](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>Configurazione ibrida con Blobfuse

Il diagramma seguente illustra uno scenario ibrido che include i servizi di Azure seguenti:

* **Calcolo**: pool di Azure Batch o set di scalabilità di macchine virtuali.

* **Rete** - Locale: Azure ExpressRoute o VPN. Azure: rete virtuale di Azure.

* **Archiviazione** - File di input e output: archivio BLOB, montato per calcolare le risorse tramite Azure Blobfuse.

  ![Bursting del cloud: configurazione ibrida con Blobfuse](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>Calcolo e archiviazione ibridi

Il diagramma seguente illustra uno scenario ibrido completamente connesso per l'archiviazione e il calcolo e include i servizi di Azure seguenti:

* **Calcolo**: pool di Azure Batch o set di scalabilità di macchine virtuali.

* **Rete** - Locale: Azure ExpressRoute o VPN. Azure: rete virtuale di Azure.

* **Archiviazione** - Cross-premise: Avere vFXT. Archiviazione facoltativa di file locali tramite Azure Data Box nell'archiviazione BLOB oppure Avere FXT locale per l'accelerazione NAS.

  ![Bursting del cloud: configurazione e archiviazione ibride](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'uso dell'[Esecuzione del rendering dei manager](batch-rendering-render-managers.md) con Azure Batch.

* Altre informazioni sulle opzioni per il [Rendering in Azure](batch-rendering-service.md).