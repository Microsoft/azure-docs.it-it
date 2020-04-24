---
title: Panoramica di Consul
description: Ottenere una panoramica di Consul
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594210"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>Panoramica

[Console][consul] è una soluzione di rete del servizio compatibile con più data center per connettere e proteggere i servizi tra piattaforme di Runtime. [Connect][consul-features] è il componente che fornisce funzionalità di mesh di servizio.

## <a name="architecture"></a>Architecture

Consul fornisce un piano dati composto da [sidecar][consul-sidecar] basati su [inviato][envoy-proxy]per impostazione predefinita. Console dispone di un'architettura proxy collegabile. Questi proxy intelligenti controllano tutto il traffico di rete all'interno e all'esterno delle app e dei carichi di lavoro in rete.

Il piano di controllo gestisce la configurazione e i criteri tramite i [componenti][consul-architecture]seguenti:

- **Server** : agente console in esecuzione in modalità server che gestisce lo stato del cluster console.

- **Client** : agente console in esecuzione in modalità client Lightweight. Ogni nodo di calcolo deve disporre di un agente client in esecuzione. Questa configurazione e i criteri dei broker client tra i carichi di lavoro e la configurazione di console. 

Il diagramma dell'architettura seguente illustra il modo in cui i vari componenti all'interno del piano dati e del piano di controllo interagiscono.

![Panoramica dei componenti e dell'architettura di console.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Criteri di selezione

Quando si valuta console per i carichi di lavoro, è importante comprendere e considerare le aree seguenti:

- [Principi Consoli](#consul-principles)
- [Capabilities](#capabilities)
- [Scenari](#scenarios)


### <a name="consul-principles"></a>Principi Consoli

I principi seguenti [guidano][consul-principles] il progetto console:

- **Basato sull'API** : codificare tutti i criteri e la configurazione.

- **Esegui e Connetti** i carichi di lavoro in qualsiasi luogo-Connetti tra piattaforme di runtime (Kubernetes, VM, senza server).

- **Estendi e integra** -Connetti in modo sicuro i carichi di lavoro all'interno dell'infrastruttura.


### <a name="capabilities"></a>Capabilities

Consul fornisce il set di funzionalità seguente:

- **Mesh** : Gateway (più Data Center), macchine virtuali (nodi fuori cluster), sincronizzazione dei servizi, opzione di debug predefinita

- **Proxy: inviato** , proxy predefinito, plug-in, proxy L4 disponibile per i carichi di lavoro di Windows

- **Gestione del traffico** -routing, suddivisione, risoluzione

- **Criteri** -intenzioni, ACL

- **Sicurezza** : autorizzazione, autenticazione, crittografia, identità basate su SPIFFE, CA esterna (insieme di credenziali), gestione dei certificati e rotazione

- **Osservabilità** : metriche, dashboard dell'interfaccia utente, Prometeo, grafana


### <a name="scenarios"></a>Scenari

Consul è particolarmente indicato per gli scenari seguenti:

- Estensione dei carichi di lavoro connessi a console esistenti

- Requisiti di conformità relativi alla gestione dei certificati

- Mesh del servizio multicluster

- Carichi di lavoro basati su VM da includere nella rete mesh del servizio



## <a name="next-steps"></a>Passaggi successivi

La documentazione seguente descrive come è possibile installare console in Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Installare console in Azure Kubernetes Service (AKS)][consul-install]

È anche possibile esplorare ulteriormente le funzionalità e l'architettura di Consul:

- [Funzionalità console][consul-features]
- [Architettura Consul][consul-architecture]
- [Console-funzionamento della connessione][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
