---
title: Esempi di Azure PowerShell - Service Fabric
description: Informazioni su come creare e gestire cluster, app e servizi di Azure Service Fabric usando PowerShell.
ms.topic: sample
ms.date: 11/29/2018
ms.custom: mvc
ms.openlocfilehash: 4b85fd604eb27f0963af882b41e823d800005dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86187099"
---
# <a name="azure-service-fabric-powershell-samples"></a>Esempi di PowerShell per Azure Service Fabric

La tabella seguente include collegamenti a esempi di script PowerShell che creano e gestiscono servizi, applicazioni e cluster di Service Fabric.

[!INCLUDE [links to azure CLI and service fabric CLI](../../includes/service-fabric-powershell.md)]

| Script | Descrizione |
|-|-|
| **Creare cluster** ||
| [Creare un cluster (Azure)](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)| Crea un cluster di Azure Service Fabric. |
| **Gestire cluster, nodi e infrastruttura** ||
| [Aggiungere un certificato dell'applicazione](./scripts/service-fabric-powershell-add-application-certificate.md)| Crea un certificato X509 per Key Vault e lo distribuisce in un set di scalabilità di macchine virtuali nel cluster. |
| [Aggiornare l'intervallo di porte RDP per le macchine virtuali del cluster](./scripts/service-fabric-powershell-change-rdp-port-range.md)|Modifica l'intervallo di porte RDP nelle macchine virtuali del nodo del cluster in un cluster distribuito.|
| [Aggiornare l'utente e la password dell'amministratore per le macchine virtuali del nodo cluster](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) | Aggiorna il nome utente e la password dell'amministratore per le macchine virtuali del nodo cluster. |
| [Aprire una porta nel servizio di bilanciamento del carico](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) | Apre una porta dell'applicazione nel servizio di bilanciamento del carico di Azure per consentire traffico in ingresso su una porta specifica. |
| [Creare una regola del gruppo di sicurezza di rete di ingresso](./scripts/service-fabric-powershell-add-nsg-rule.md) | Crea una regola del gruppo di sicurezza di rete in ingresso per consentire traffico in ingresso nel cluster su una porta specifica. |
| **Gestire le applicazioni** ||
| [Distribuire un'applicazione](./scripts/service-fabric-powershell-deploy-application.md)| Distribuire un'applicazione in un cluster.|
| [Aggiornare un'applicazione](./scripts/service-fabric-powershell-upgrade-application.md)| Aggiorna un'applicazione.|
| [Rimuovere un'applicazione](./scripts/service-fabric-powershell-remove-application.md)| Rimuovere un'applicazione da un cluster.|
