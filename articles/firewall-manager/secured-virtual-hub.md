---
title: Che cos'è un hub virtuale protetto?
description: Informazioni sugli hub virtuali protetti
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 56e0d40bcbfb97f57b63dc82da1a6604f83dffad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563623"
---
# <a name="what-is-a-secured-virtual-hub"></a>Che cos'è un hub virtuale protetto?

Un hub virtuale è una rete virtuale gestita da Microsoft che consente la connettività da altre risorse. Quando un hub virtuale viene creato da una rete WAN virtuale nel portale di Azure, vengono creati come componenti un hub virtuale VNet e gateway (facoltativo).

Un hub virtuale *protetto* è un [Hub WAN virtuale di Azure](../virtual-wan/virtual-wan-about.md#resources) con criteri di sicurezza e routing associati configurati da gestione firewall di Azure. USA Hub virtuali protetti per creare con facilità architetture con hub e spoke e transitive con servizi di sicurezza nativi per la governance e la protezione del traffico. 

È possibile usare un hub virtuale protetto come VNet centrale gestito senza connettività locale. Sostituisce la VNet centrale che in precedenza era necessaria per una distribuzione del firewall di Azure. Poiché l'hub virtuale protetto fornisce il routing automatico, non è necessario configurare UdR (route definite dall'utente) per instradare il traffico attraverso il firewall.

È anche possibile usare hub virtuali protetti come parte di un'architettura WAN virtuale completa. Questa architettura offre connettività di ramo protetta, ottimizzata e automatizzata a e tramite Azure. È possibile scegliere i servizi per proteggere e gestire il traffico di rete, inclusi il firewall di Azure e altri provider di sicurezza come servizio (SECar) di terze parti.

## <a name="create-a-secured-virtual-hub"></a>Creare un hub virtuale protetto

Usando gestione firewall nella portale di Azure è possibile creare un nuovo hub virtuale protetto o convertire un hub virtuale esistente creato in precedenza tramite la rete WAN virtuale di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per creare un hub virtuale protetto e usarlo per proteggere e governare una rete hub-spoke, vedere [esercitazione: proteggere la rete cloud con Azure Firewall Manager usando il portale di Azure](secure-cloud-network.md).