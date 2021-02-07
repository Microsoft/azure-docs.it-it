---
title: Autorizzazioni del piano di protezione DDoS di Azure
description: Informazioni su come gestire le autorizzazioni in un piano di protezione.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: df53062c7c897493a47d88ea2873f9710b9825bf
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806257"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>Gestire i piani di protezione DDoS: autorizzazioni e restrizioni

Un piano di protezione DDoS funziona in diverse aree e sottoscrizioni. Lo stesso piano può essere collegato a reti virtuali di altre sottoscrizioni in aree diverse, in tutto il tenant. Alla sottoscrizione a cui è associato il piano viene addebitato il costo mensile relativo al piano, nonché i costi in eccedenza, nel caso in cui gli indirizzi IP pubblici protetti siano più di 100. Per altre informazioni sui prezzi di DDoS, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="prerequisites"></a>Prerequisiti

- Prima di eseguire i passaggi di questa esercitazione, è necessario creare prima di tutto un [piano di protezione standard DDoS di Azure](manage-ddos-protection.md).

## <a name="permissions"></a>Autorizzazioni

Per lavorare con i piani di protezione DDoS, l'account deve essere assegnato al ruolo [Collaboratore Rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un ruolo [personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le operazioni appropriate elencate nella tabella seguente:

| Azione                                            | Nome                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | Leggere un piano di protezione DDoS              |
| Microsoft.Network/ddosProtectionPlans/write       | Creare o aggiornare un piano di protezione DDoS  |
| Microsoft.Network/ddosProtectionPlans/delete      | Eliminare un piano di protezione DDoS            |
| Microsoft.Network/ddosProtectionPlans/join/action | Aggiungere un piano di Protezione DDoS              |

Per abilitare la protezione DDoS per una rete virtuale, all'account devono anche essere assegnate le appropriate [operazioni per le reti virtuali](../virtual-network/manage-virtual-network.md#permissions).

## <a name="azure-policy"></a>Criteri di Azure

Per la maggior parte delle organizzazioni, non è necessaria la creazione di più piani. Non è possibile spostare un piano tra le sottoscrizioni. Se si vuole modificare la sottoscrizione di un piano, è necessario eliminare il piano esistente e crearne uno nuovo.

Per i clienti che hanno diverse sottoscrizioni e che vogliono garantire la distribuzione di un singolo piano nel tenant per il controllo dei costi, è possibile usare i criteri di Azure per [limitare la creazione dei piani standard di protezione DDoS di Azure](https://aka.ms/ddosrestrictplan). Questo criterio bloccherà la creazione di qualsiasi piano DDoS, a meno che la sottoscrizione non sia stata precedentemente contrassegnata come un'eccezione. Questo criterio Visualizza anche un elenco di tutte le sottoscrizioni che hanno un piano DDoS distribuito, ma non devono essere contrassegnate come non conformi.


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare e configurare i dati di telemetria per il piano di protezione DDoS, continuare con le esercitazioni.

> [!div class="nextstepaction"]
> [Visualizzare e configurare i dati di telemetria della protezione DDoS](telemetry.md)
