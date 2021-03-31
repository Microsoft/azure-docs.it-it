---
title: Che cos'è applicazione Azure controller di ingresso gateway?
description: Questo articolo fornisce un'introduzione al controller di ingresso del gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2564fd38056241fd48f58f5f6039bf64f92b6741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714409"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Che cos'è il controller di ingresso del gateway applicazione?
Il controller di ingresso del gateway applicazione (AGIC) è un'applicazione Kubernetes, che consente ai clienti di [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) di sfruttare il servizio di bilanciamento del carico L7 del [gateway applicazione](https://azure.microsoft.com/services/application-gateway/) nativo di Azure per esporre il software cloud a Internet. AGIC monitora il cluster Kubernetes in cui è ospitato e aggiorna continuamente un gateway applicazione, in modo che i servizi selezionati siano esposti a Internet.

Il controller di ingresso viene eseguito nel proprio pod sul AKS del cliente. AGIC monitora un subset di risorse Kubernetes per le modifiche. Lo stato del cluster AKS viene convertito in configurazione specifica del gateway applicazione e applicato al [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Vantaggi del controller di ingresso del gateway applicazione
AGIC consente di eliminare la necessità di un altro servizio di bilanciamento del carico/IP pubblico davanti al cluster AKS ed evita più hop nel percorso di DataPath prima che le richieste raggiungano il cluster AKS. Il gateway applicazione comunica con i pod usando direttamente il proprio indirizzo IP privato e non richiede i servizi Deport o KubeProxy. Offre inoltre prestazioni migliori per le distribuzioni.

Il controller di ingresso è supportato esclusivamente da SKU Standard_v2 e WAF_v2, che offre anche vantaggi per la scalabilità automatica. Il gateway applicazione può reagire in risposta a un aumento o una riduzione del carico di traffico e della scalabilità di conseguenza, senza l'utilizzo di risorse dal cluster AKS.

L'uso del gateway applicazione oltre a AGIC consente anche di proteggere il cluster AKS fornendo i criteri TLS e la funzionalità Web Application Firewall (WAF).

![Gateway applicazione Azure + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC viene configurato tramite la [risorsa di ingresso](https://kubernetes.io/docs/user-guide/ingress/)Kubernetes, insieme al servizio e alle distribuzioni/pod. Offre una serie di funzionalità, sfruttando il servizio di bilanciamento del carico L7 del gateway applicazione nativo di Azure. Ecco alcuni esempi:
  - Routing basato su URL
  - Affinità basata sui cookie
  - Terminazione TLS
  - TLS end-to-end
  - Supporto per siti Web pubblici, privati e ibridi
  - web application firewall integrata

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Differenza tra la distribuzione Helm e la Add-On AKS
Esistono due modi per distribuire AGIC per il cluster AKS. Il primo modo consiste nell'usare Helm; il secondo avviene tramite AKS come componente aggiuntivo. Il vantaggio principale della distribuzione di AGIC come componente aggiuntivo AKS è che è molto più semplice rispetto alla distribuzione tramite Helm. Per una nuova configurazione, è possibile distribuire un nuovo gateway applicazione e un nuovo cluster AKS con AGIC abilitato come componente aggiuntivo in una sola riga nell'interfaccia della riga di comando di Azure. Il componente aggiuntivo è anche un servizio completamente gestito, che offre vantaggi aggiuntivi come gli aggiornamenti automatici e un maggiore supporto. Entrambi i modi di distribuire AGIC (componente aggiuntivo Helm e AKS) sono completamente supportati da Microsoft. Il componente aggiuntivo consente inoltre una migliore integrazione con AKS come componente aggiuntivo di prima classe.

Il componente aggiuntivo AGIC è ancora distribuito come Pod nel cluster AKS del cliente. Tuttavia, esistono alcune differenze tra la versione della distribuzione Helm e la versione del componente aggiuntivo di AGIC. Di seguito è riportato un elenco delle differenze tra le due versioni: 
  - Non è possibile modificare i valori di distribuzione Helm nel componente aggiuntivo AKS:
    - `verbosityLevel` verrà impostato su 5 per impostazione predefinita
    - `usePrivateIp` verrà impostato su false per impostazione predefinita. Questa operazione può essere sovrascritta dall' [annotazione use-Private-IP](ingress-controller-annotations.md#use-private-ip)
    - `shared` non è supportato nel componente aggiuntivo 
    - `reconcilePeriodSeconds` non è supportato nel componente aggiuntivo
    - `armAuth.type` non è supportato nel componente aggiuntivo
  - AGIC distribuito tramite Helm supporta ProhibitedTargets, che significa che AGIC può configurare il gateway applicazione in modo specifico per i cluster AKS senza influire su altri backend esistenti. Il componente aggiuntivo AGIC attualmente non supporta questa operazione. 
  - Poiché il componente aggiuntivo AGIC è un servizio gestito, i clienti verranno aggiornati automaticamente alla versione più recente del componente aggiuntivo AGIC, a differenza di AGIC distribuiti tramite Helm dove il cliente deve aggiornare manualmente AGIC. 

> [!NOTE]
> I clienti possono distribuire un solo componente aggiuntivo AGIC per ogni cluster AKS e ogni componente aggiuntivo AGIC attualmente può essere destinato a un solo gateway applicazione. Per le distribuzioni che richiedono più di un AGIC per cluster o più AGICs destinati a un gateway applicazione, continuare a usare AGIC distribuito tramite Helm. 

## <a name="next-steps"></a>Passaggi successivi
- [**Aks Add-On la distribuzione Greenfield**](tutorial-ingress-controller-add-on-new.md): istruzioni sull'installazione del componente aggiuntivo AGIC, AKS e del gateway applicazione in un'infrastruttura Slate vuota.
- [**Aks Add-On distribuzione Brownfield**](tutorial-ingress-controller-add-on-existing.md): installare il componente aggiuntivo AGIC in un cluster AKS con un gateway applicazione esistente.
- [**Distribuzione Helm Greenfield**](ingress-controller-install-new.md): installare AGIC tramite Helm, il nuovo cluster AKS e il nuovo gateway applicazione nell'infrastruttura Slate vuota.
- [**Distribuzione di Helm Brownfield**](ingress-controller-install-existing.md): distribuire AGIC tramite Helm in un cluster AKS esistente e un gateway applicazione.