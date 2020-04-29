---
title: includere il file
description: File di inclusione
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 51b687dc2a6264eb12362616429746c9b182c3b1
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2020
ms.locfileid: "68323875"
---
> [!NOTE]
> Quando si crea un account Batch, è possibile scegliere tra due modalità di *allocazione del pool*, ovvero **sottoscrizione utente** e **servizio Batch**. Nella maggior parte dei casi si userà la modalità predefinita servizio Batch, nella quale i pool vengono allocati in background nelle sottoscrizioni gestite da Azure. Nella modalità sottoscrizione utente alternativa, le macchine virtuali e le altre risorse di Batch vengono create direttamente nella sottoscrizione in fase di creazione di un pool. La modalità sottoscrizione utente è obbligatoria se si vogliono creare pool di Batch usando [istanze di macchina virtuale riservate di Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/). Per creare un account Batch in modalità Sottoscrizione utente, è anche necessario registrare la sottoscrizione in Azure Batch e associare l'account ad Azure Key Vault.