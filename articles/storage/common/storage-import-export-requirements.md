---
title: Requisiti per il servizio Importazione/Esportazione di Azure | Microsoft Docs
description: Informazioni sui requisiti hardware e software per il servizio Importazione/Esportazione di Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255314"
---
# <a name="azure-importexport-system-requirements"></a>Requisiti di sistema per Importazione/Esportazione di Azure

Questo articolo descrive i requisiti essenziali per il servizio Importazione/Esportazione di Azure. È consigliabile esaminare attentamente queste informazioni prima di usare il servizio Importazione/Esportazione e tornare a farvi riferimento durante l'uso del servizio ogni volta che è necessario.

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Per preparare i dischi rigidi tramite lo strumento WAImportExport, sono supportati i **sistemi operativi a 64 bit seguenti che supportano Crittografia unità BitLocker**.


|Piattaforma |Versione |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Altri software obbligatori per il client Windows

|Piattaforma |Versione |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Account di archiviazione supportati

Il servizio Importazione/Esportazione di Azure supporta i tipi di account di archiviazione seguenti:

- Account di archiviazione standard per utilizzo generico V2 (scelta consigliata per la maggior parte degli scenari)
- Account di archiviazione BLOB
- Account di archiviazione per utilizzo generico v1 (distribuzione classica o di Azure Resource Manager),

Per altre informazioni sugli account di archiviazione, vedere [Panoramica degli account di archiviazione di Azure](storage-account-overview.md).

Ogni processo può essere usato per trasferire dati da o verso un solo account di archiviazione. In altre parole, un singolo processo di importazione/esportazione non può estendersi su più account di archiviazione. Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](storage-account-create.md).

> [!IMPORTANT]
> Il servizio importazione/esportazione di Azure non supporta gli account di archiviazione in cui è stata abilitata la funzionalità degli endpoint del [servizio rete virtuale](../../virtual-network/virtual-network-service-endpoints-overview.md). 

## <a name="supported-storage-types"></a>Tipi di archivio supportati

Con il servizio Importazione/Esportazione di Azure sono supportati i tipi di archiviazione inclusi nell'elenco seguente.


|Processo  |Servizio di archiviazione |Supportato  |Non supportato  |
|---------|---------|---------|---------|
|Importa     |  Archiviazione BLOB di Azure <br><br> Archiviazione file di Azure       | BLOB in blocchi e BLOB di pagine supportati <br><br> File supportati          |
|Export     |   Archiviazione BLOB di Azure       | BLOB in blocchi, BLOB di pagine e BLOB di aggiunta supportati         | File di Azure non supportato


## <a name="supported-hardware"></a>Hardware supportato

Per il servizio Importazione/Esportazione di Azure è necessario usare dischi per copiare i dati.

### <a name="supported-disks"></a>Dischi supportati

Con il servizio Importazione/Esportazione sono supportati i dischi inclusi nell'elenco seguente.


|Tipo di disco  |Dimensioni  |Supportato |
|---------|---------|---------|
|SSD    |   2,5"      |SATA III          |
|HDD     |  2,5"<br>3,5"       |SATA II, SATA III         |

I tipi di dischi seguenti non sono supportati:
- USBs.
- HDD esterno con adattatore USB incorporato.
- Dischi che si trovano all'interno della custodia di un disco rigido esterno.

Un singolo processo di importazione/esportazione può avere:
- Un massimo di 10 HDD/SSD.
- Una combinazione di HDD/SSD di qualsiasi dimensione.

Un numero elevato di unità può essere distribuito tra più processi e non esistono limiti al numero di processi che è possibile creare. Per i processi di importazione, viene elaborato solo il primo volume di dati sull'unità. Il volume di dati deve essere formattato con NTFS.

Per la preparazione dei dischi rigidi e la copia dei dati tramite lo strumento WAImportExport, è possibile usare adattatori USB esterni. La maggior parte degli adattatori USB pronti all'uso di versione 3.0 o successiva dovrebbero essere adatti a tale scopo.


## <a name="next-steps"></a>Passaggi successivi

* [Configurare lo strumento WAImportExport](storage-import-export-tool-how-to.md)
* [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
* [Esempio di API REST del servizio Importazione/Esportazione di Azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
