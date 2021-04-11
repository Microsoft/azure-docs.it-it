---
title: includere file
description: includere file
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: c003e765574d764b6653823e8554d0718ea85e0e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081100"
---
Una condivisione file di Azure viene archiviata nel cloud in un account di archiviazione di Azure.
Ecco un altro livello di considerazioni sulle prestazioni.

Se si dispone di condivisioni altamente attive (condivisioni usate da molti utenti e/o applicazioni), due condivisioni file di Azure potrebbero raggiungere il limite delle prestazioni di un account di archiviazione.

Una procedura consigliata consiste nel distribuire gli account di archiviazione con una condivisione file ciascuna.
È possibile raggruppare più condivisioni file di Azure nello stesso account di archiviazione, nel caso in cui si disponga di condivisioni di archiviazione oppure si prevede attività quotidiane ridotte.

Queste considerazioni si applicano a un accesso diretto al cloud (tramite una macchina virtuale di Azure) rispetto a Sincronizzazione file di Azure. Se si prevede di usare Sincronizzazione file di Azure solo in queste condivisioni, il raggruppamento di più in un unico account di archiviazione di Azure è corretto.

Se è stato creato un elenco delle condivisioni, è necessario eseguire il mapping di ogni condivisione all'account di archiviazione in cui risiederà.

Nella fase precedente è stato determinato il numero appropriato di condivisioni. In questo passaggio è stato creato un mapping degli account di archiviazione alle condivisioni file. Distribuire ora il numero appropriato di account di archiviazione di Azure con il numero appropriato di condivisioni file di Azure.

Assicurarsi che l'area di ogni account di archiviazione sia la stessa e che corrisponda all'area della risorsa del servizio di sincronizzazione archiviazione già distribuita.

> [!CAUTION]
> Se si crea una condivisione file di Azure con un limite di 100-TiB, tale condivisione può usare solo l'archiviazione con ridondanza locale o le opzioni di ridondanza dell'archiviazione con ridondanza della zona. Prendere in considerazione le esigenze di ridondanza dell'archiviazione prima di usare le condivisioni file 100-TiB.

Per impostazione predefinita, le condivisioni file di Azure vengono ancora create con un limite di 5 TiB. Poiché si stanno creando nuovi account di archiviazione, assicurarsi di seguire le [indicazioni per creare gli account di archiviazione che consentono le condivisioni file di Azure con limiti 100-TIB](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Un'altra considerazione quando si distribuisce un account di archiviazione è la ridondanza di archiviazione di Azure. Vedere [Opzioni di ridondanza di archiviazione di Azure](../articles/storage/common/storage-redundancy.md).

Anche i nomi delle risorse sono importanti. Ad esempio, se si raggruppano più condivisioni per il reparto risorse umane in un account di archiviazione di Azure, è necessario assegnare un nome all'account di archiviazione appropriato. Analogamente, quando si assegna un nome alle condivisioni file di Azure, è consigliabile usare nomi simili a quelli usati per le controparti locali.