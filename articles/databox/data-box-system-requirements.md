---
title: Requisiti di sistema di Microsoft Azure Data Box | Microsoft Docs
description: Informazioni sui requisiti relativi al software e alla rete per Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 496069ebf64340bc55f03df8dc15304b4888bec0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200292"
---
# <a name="azure-data-box-system-requirements"></a>Requisiti di sistema di Azure Data Box

Questo articolo descrive i requisiti di sistema importanti per Microsoft Azure Data Box e per i client che si connettono a Data Box. Prima di distribuire Data Box è consigliabile leggere attentamente queste informazioni e quindi farvi riferimento, se necessario, durante la distribuzione e il successivo funzionamento.

I requisiti di sistema includono:

* **Requisiti software per gli host che si connettono a Data Box:** descrizione delle piattaforme supportate, dei browser per l'interfaccia utente Web locale, dei client SMB e degli eventuali requisiti aggiuntivi per gli host che possono connettersi a Data Box.
* **Requisiti di rete per Data Box:** informazioni sui requisiti di rete per il funzionamento ottimale di Data Box.


## <a name="software-requirements"></a>Requisiti software

I requisiti software includono le informazioni sui sistemi operativi supportati, sui browser supportati per l'interfaccia utente Web locale e sui client SMB.

### <a name="supported-operating-systems-for-clients"></a>Sistemi operativi supportati per i client

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-filesystems-for-linux-clients"></a>Filesystem supportati per i client Linux

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]


> [!IMPORTANT] 
> La connessione a condivisioni di Data Box non è supportata tramite REST per gli ordini di esportazione. 

### <a name="supported-storage-accounts"></a>Account di archiviazione supportati

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]


### <a name="supported-storage-types"></a>Tipi di archivio supportati

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Web browser supportati

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Requisiti di rete

Il data center disponga di una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a questa velocità è possibile usare un collegamento dati a 1 GbE per copiare i dati, ma la velocità dell'operazione ne risentirà.

### <a name="port-requirements"></a>Requisiti delle porte

La tabella seguente elenca le porte che devono essere aperte nel firewall per consentire il traffico SMB o NFS. In questa tabella, *in* o *in ingresso* fa riferimento alla direzione da cui le richieste client in ingresso accedono al dispositivo. *Out* In uscita *o in uscita si* intende la direzione in cui il dispositivo data box invia i dati all'esterno, oltre la distribuzione: ad esempio, in uscita verso Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Passaggi successivi

* [Distribuire Azure Data Box](data-box-deploy-ordered.md)
