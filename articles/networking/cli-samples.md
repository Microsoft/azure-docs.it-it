---
title: Esempi dell'interfaccia della riga di comando di Azure-rete
description: Informazioni sugli esempi di interfaccia della riga di comando di Azure per la rete, inclusi esempi per la connettività tra le risorse di Azure ed esempi per il bilanciamento del carico e direzione del traffico
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: c038f0d238646f43b93ba2a2c6a1120ab5feccee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87497291"
---
# <a name="azure-cli-samples-for-networking"></a>Esempi dell'interfaccia della riga di comando di Azure per la rete

La tabella seguente include collegamenti a script Bash compilati tramite l'interfaccia della riga di comando di Azure.

| Script | Descrizione |
|-|-|
|**Connettività tra le risorse di Azure**||
| [Creare una rete virtuale per le applicazioni multilivello](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea una rete virtuale con subnet front-end e back-end. Il traffico verso la subnet front-end è limitato a HTTP e SSH, mentre il traffico verso la subnet back-end è limitato a MySQL sulla porta 3306. |
| [Eseguire il peering di due reti virtuali](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea e connette due reti virtuali nella stessa area. |
| [Instradare il traffico attraverso un'appliance virtuale di rete](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea una rete virtuale con subnet front-end e back-end e una VM che è in grado di instradare il traffico tra le due subnet. |
| [Filtrare il traffico della VM in ingresso e in uscita](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea una rete virtuale con subnet front-end e back-end. Il traffico di rete in ingresso alla subnet front-end è limitato a HTTP, HTTPS e SSH. Non è consentito il traffico in uscita verso Internet dalla subnet di back-end. |
|**Bilanciamento del carico e direzione del traffico**||
| [Eseguire il bilanciamento del carico per più siti Web sulle VM](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Crea due VM con più configurazioni IP, unite a un set di disponibilità di Azure, accessibili tramite un servizio di bilanciamento del carico di Azure. |
| [Dirigere il traffico su più aree per la disponibilità elevata delle applicazioni](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Crea due piani di servizio app, due app Web, un profilo di gestione traffico e due endpoint di gestione traffico. |
| | |
