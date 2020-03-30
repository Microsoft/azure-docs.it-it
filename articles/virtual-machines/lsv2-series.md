---
title: Serie Lsv2 - Macchine virtuali di Azure
description: Specifiche per le macchine virtuali della serie Lsv2.
services: virtual-machines
author: sasha-melamed
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 103e19d6e299956b5ee1ad45b577e25f9f2de1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164033"
---
# <a name="lsv2-series"></a>Serie Lsv2

La serie Lsv2 offre velocità effettiva elevata, bassa latenza, risorse di archiviazione NVMe locali con mapping diretto in esecuzione sul [processore AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) con un turbo boost "all core" di 2,55 GHz e un turbo boost massimo di 3 GHz. Le macchine virtuali Serie Lsv2 sono disponibili in dimensioni comprese tra 8 e 80 vCPU in una configurazione multi-thread simultanea.  Sono disponibili 8 GiB di memoria per ogni vCPU e un dispositivo NVMe SSD M.2 da 1,92 TB per 8 vCPU, fino a 19,2 TB (10 x 1,92 TB) disponibili nella versione L80s v2.

> [!NOTE]
> Le macchine virtuali della serie Lsv2 sono ottimizzate per usare il disco locale nel nodo collegato direttamente alla macchina virtuale anziché usare dischi dati durevoli. Questo consente un maggior numero di operazioni di I/O al secondo o un maggiore velocità effettiva per i carichi di lavoro. Le serie Lsv2 e Ls non supportano la creazione di una cache locale per aumentare le operazioni di I/O al secondo raggiungibili da dischi dati durevoli.
>
> L'elevata velocità effettiva e le operazioni di I/O al secondo del disco locale rendono le macchine virtuali della serie Lsv2 ideali per gli archivi NoSQL, ad esempio Apache Cassandra e MongoDB, replicando i dati tra più macchine virtuali per ottenere la persistenza in caso di errore di una singola macchina virtuale.
>
> Per altre informazioni, vedere Ottimizzare le prestazioni nelle macchine virtuali della serie Lsv2 per [Windows](../virtual-machines/windows/storage-performance.md) o [Linux.](../virtual-machines/linux/storage-performance.md)  

Unità di terapia intensiva: 150-175

Archiviazione Premium: supportata

Memorizzazione nella cache di archiviazione Premium: non supportata

Live Migration: non supportato

Aggiornamenti di conservazione della memoria: non supportatiMemory Preserving Updates: Not Supported

| Dimensione | vCPU | Memoria (GiB) | Disco temporaneo<sup>1</sup> (GiB) | Dischi NVMe<sup>2</sup> | Velocità effettiva del disco NVMe 3 (IOPS di lettura/MBps)NVMe Disk throughput<sup>3</sup> (Read IOPS/MBps) | Velocità effettiva massima disco dati non memorizzati nella cache (IOPs/MBps)<sup>4Max</sup> uncached data disk disk throughput (IOPs/MBps) 4 | Numero massimo di dischi dati | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1 x 1,92 TB  | 400000/2000  | 8000/160   | 16 | 2 / 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2 x 1,92 TB  | 800000/4000  | 16000/320  | 32 | 4 / 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4 x 1,92 TB  | 1.5M/8000    | 32000/640  | 32 | 8 / 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x1,92 TB  | 2.2M/14000   | 48000/960  | 32 | 8 / 16000 |
| Standard_L64s_v2  | 64 | 512 | 640 |  8 x 1,92 TB  | 2.9M/16000   | 64000/1280 | 32 | 8 / 16000 |
| STANDARD_L80S_V2<sup>5</sup> | 80 | 640 | 800 | 10 x 1,92 TB | 3.8M/20000 | 80000/1400 | 32 | 8 / 16000 |

<sup>1</sup> Le macchine virtuali serie Lsv2 hanno un disco risorse temporaneo basato su SCSI standard per l'uso di un file di scambio/paging del sistema operativo (D: in Windows, /dev/sdb in Linux). Il disco offre 80 GiB di archiviazione, 4.000 IOPS e una velocità di trasferimento di 80 MBps ogni 8 vCPU (ad esempio, il modello Standard_L80s_v2 fornisce 800 GiB a 40.000 IOPS e 800 MBPS). In questo modo, le unità NVMe possono essere completamente dedicate all'utilizzo dell'applicazione. Questo disco è temporaneo e tutti i dati andranno persi al momento dell'arresto/deallocazione.

<sup>2</sup> I dischi NVMe locali sono temporanei: i dati dei dischi andranno persi se si arresta/dealloca la macchina virtuale.

<sup>3</sup> La tecnologia Hyper-V NVMe Direct offre un accesso senza limitazione alle unità NVMe locali mappate in modo sicuro nello spazio della macchina virtuale guest.  Per ottenere le massime prestazioni è necessario usare la build WS2019 più recente o Ubuntu 18.04 o 16.04 disponibile in Azure Marketplace.  Le prestazioni di scrittura variano in base alle dimensioni di I/O, al caricamento delle unità e all'utilizzo della capacità.

<sup>4</sup> Le macchine virtuali serie Lsv2 non prevedono la cache dell'host per il disco dati perché non genera alcun vantaggio per i carichi di lavoro Lsv2.  Possono tuttavia prevedere l'opzione disco del sistema operativo temporaneo della macchina virtuale di Azure (fino a 30 GiB).

<sup>5</sup> macchine virtuali con più di 64 vCPU richiedono uno di questi sistemi operativi guest supportati:

- Windows Server 2016 o versione successiva
- Ubuntu 16.04 LTS o versione successiva, con kernel tuned Azure (4.15 kernel o versione successiva)Ubuntu 16.04 LTS or later, with Azure tuned kernel (4.15 kernel or later)
- SLES 12 SP2 o versione successiva
- RHEL o CentOS versione 6.7 a 6.10, con il pacchetto LIS fornito da Microsoft 4.3.1 (o versione successiva) installato
- RHEL o CentOS versione 7.3, con il pacchetto LIS 4.2.1 (o versione successiva) fornito da Microsoft installato
- RHEL o CentOS versione 7.6 o successiva
- Oracle Linux con UEK4 o versioni successive
- Debian 9 con kernel backports, Debian 10 o versioni successive
- CoreOS con un kernel 4.14 o versione successiva

## <a name="size-table-definitions"></a>Definizioni delle tabelle delle dimensioni

- La capacità di archiviazione viene visualizzata in unità di GiB o 1.024^3 byte. Quando si confrontano dischi misurati in GB (1.000^3 byte) con dischi misurati in GiB (1.024^3), tenere presente che i valori di capacità specificati in GiB potrebbero apparire inferiori. Ad esempio, 1.023 GiB = 1.098,4 GB
- La velocità effettiva del disco viene misurata in operazioni di input/output al secondo (IOPS) e MBps, dove il valore di MBps corrisponde a 10^6 byte al secondo.
- Se si vogliono ottenere prestazioni ottimali per le macchine virtuali, è necessario limitare il numero di dischi dati a 2 per ogni vCPU.
- La **larghezza di banda della rete prevista** è la [larghezza di banda aggregata massima allocata per ogni tipo di macchina virtuale](../virtual-network/virtual-machine-network-throughput.md) in tutte le schede di interfaccia di rete, per tutte le destinazioni. I limiti massimi non sono garantiti, ma hanno lo scopo di fornire indicazioni per la selezione del tipo di macchina virtuale corretto per l'applicazione desiderata. Le prestazioni di rete effettive dipenderanno da svariati fattori, tra cui congestione della rete, carichi dell'applicazione e impostazioni di rete. Per informazioni sull'ottimizzazione della velocità effettiva della rete, vedere [Ottimizzazione della velocità effettiva di rete per Windows e Linux](../virtual-network/virtual-network-optimize-network-bandwidth.md). Per realizzare le prestazioni di rete previste in Linux o Windows, potrebbe essere necessario selezionare una versione specifica o ottimizzare la macchina virtuale. Per ulteriori informazioni, vedere [Come eseguire test affidabili della velocità effettiva della macchina virtuale](../virtual-network/virtual-network-bandwidth-testing.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
