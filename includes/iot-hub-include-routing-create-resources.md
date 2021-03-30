---
title: includere file
description: includere file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 5680bd1c3fadab8edcde1634c708ca8683c0e99c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "76021166"
---
## <a name="create-base-resources"></a>Creare le risorse di base

Prima di poter configurare il routing dei messaggi, è necessario creare un hub IoT, un account di archiviazione e una coda del bus di servizio. Queste risorse possono essere create usando uno dei quattro articoli disponibili nella prima parte di questa esercitazione: il portale di Azure, un modello di Azure Resource Manager, l'interfaccia della riga di comando di Azure o Azure PowerShell.

Usare lo stesso gruppo di risorse e lo stesso percorso per tutte le risorse. Alla fine, poi, è possibile rimuovere tutte le risorse in un unico passaggio tramite l'eliminazione del gruppo di risorse.

Di seguito è riportato un riepilogo dei passaggi da eseguire nelle sezioni seguenti: 

1. Creare un [gruppo di risorse](../articles/azure-resource-manager/management/overview.md).

2. Creare un hub IoT nel livello S1. Aggiungere un gruppo di consumer all'hub IoT Il gruppo di consumer è usato da Analisi di flusso di Azure quando si recuperano i dati.

   > [!NOTE]
   > Per completare questa esercitazione, è necessario utilizzare un hub IoT in un livello a pagamento. Il livello gratuito consente solo di configurare un endpoint e questa esercitazione ne richiede di più.
   > 

3. Creare un account di archiviazione V1 standard con la replica Standard_LRS.

4. Creare uno spazio dei nomi del bus di servizio e una coda

5. Creare un'identità del dispositivo per il dispositivo simulato che invia messaggi all'hub dell'utente. Salvare la chiave per la fase del test. Se si crea un modello di Resource Manager, questa operazione viene eseguita dopo la distribuzione del modello.