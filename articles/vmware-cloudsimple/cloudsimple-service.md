---
title: Soluzione VMware di Azure di CloudSimple-Service
description: Informazioni sul servizio CloudSimple con la sua panoramica. La creazione del servizio consente di acquistare nodi, riservare nodi e creare cloud privati.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8f32197eda4fc7632e883fd21dd6e1caa0dbd24b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898743"
---
# <a name="cloudsimple-service-overview"></a>Panoramica del servizio CloudSimple

Il servizio CloudSimple consente di usare la soluzione VMware di Azure con CloudSimple.  La creazione del servizio consente di acquistare nodi, riservare nodi e creare cloud privati.  Si crea il servizio CloudSimple in ogni area di Azure in cui è disponibile il servizio CloudSimple. Il servizio definisce la rete perimetrale della soluzione VMware di Azure tramite CloudSimple. La rete perimetrale supporta servizi che includono VPN, ExpressRoute e connettività Internet ai cloud privati.

## <a name="gateway-subnet"></a>Subnet gateway

È necessaria una subnet del gateway per ogni servizio CloudSimple ed è univoca per l'area in cui è stata creata. La subnet del gateway viene usata quando si crea la rete perimetrale e richiede un blocco CIDR/28.  Lo spazio degli indirizzi della subnet del gateway deve essere univoco. Non deve sovrapporsi ad alcuna rete che comunica con l'ambiente CloudSimple. Le reti che comunicano con CloudSimple includono reti locali e rete virtuale di Azure.  Una subnet del gateway non può essere eliminata dopo la creazione.  La subnet del gateway viene rimossa quando il servizio viene eliminato.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [creare un servizio CloudSimple in Azure](quickstart-create-cloudsimple-service.md).
