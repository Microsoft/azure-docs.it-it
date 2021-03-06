---
title: azcopy logout | Microsoft Docs
description: Questo articolo fornisce informazioni di riferimento per il comando azcopy logout.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 20d1ee643f9864fb95ebdc9215cd0dab13a0fb5c
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503100"
---
# <a name="azcopy-logout"></a>azcopy logout

Disconnette l'utente e termina l'accesso Archiviazione di Azure risorse.

## <a name="synopsis"></a>Riepilogo

Questo comando rimuoverà tutte le informazioni di accesso memorizzate nella cache per l'utente corrente.

```azcopy
azcopy logout [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire dati con AzCopy e Archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

## <a name="options"></a>Opzioni

|Opzione|Descrizione|
|--|--|
|-h, --help|Visualizzare il contenuto della Guida per il comando logout.|

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--cap-mbps float|Consente di fisso la velocità di trasferimento, in megabit al secondo. La velocità effettiva momentanea può variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è massima.|
|--output-type string|Formato dell'output del comando. Le opzioni disponibili includono: text, json. Il valore predefinito è "text".|
|--trusted-microsoft-suffixes string   |Specifica suffissi di dominio aggiuntivi a Azure Active Directory possono essere inviati i token di accesso.  Il valore predefinito è '*.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Tutti gli elementi elencati di seguito vengono aggiunti all'impostazione predefinita. Per motivi di sicurezza, è consigliabile inserire Microsoft Azure domini qui. Separare più voci con punti e virgola.|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
