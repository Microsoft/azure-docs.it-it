---
title: Architettura di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Architettura della distribuzione di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 041da4198b0bdd040a4916008a1135aa2e2a5f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614532"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architettura di SAP HANA (istanze Large) in Azure

In generale, nella soluzione SAP HANA in Azure (istanze Large) il livello applicazione SAP si trova nelle VM. Il livello database si trova nell'hardware configurato per SAP TDI, a sua volta collocato in un modulo per istanze Large nella stessa area di Azure connessa a IaaS di Azure.

> [!NOTE]
> Distribuire il livello applicazione SAP nella stessa area di Azure del livello DBMS di SAP. Questa regola è ben documentata nelle informazioni pubblicate relative ai carichi di lavoro SAP in Azure. 

L'architettura complessiva di SAP HANA in Azure (istanze Large) offre una configurazione hardware con certificazione SAP TDI che consiste in un server non virtualizzato, bare metal e a elevate prestazioni per il database SAP HANA. Offre inoltre la capacità e la flessibilità di Azure per ridimensionare le risorse per il livello applicazione SAP in base alle esigenze.

![Panoramica dell'architettura di SAP HANA in Azure (istanze di grandi dimensioni)](./media/hana-overview-architecture/image1-architecture.png)

L'architettura illustrata è suddivisa in tre sezioni.

- **Sezione destra**: mostra un'infrastruttura locale che esegue diverse applicazioni nei data center in modo che gli utenti finali possano accedere ad applicazioni line-of-business, come SAP. Idealmente, questa infrastruttura locale è connessa ad Azure con [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Sezione centrale**: mostra l'infrastruttura IaaS di Azure e, in questo scenario, l'uso di VM per l'hosting di SAP o di altre applicazioni che usano SAP HANA come sistema DBMS. Le istanze di HANA più piccole che funzionano con la memoria delle VM vengono distribuite nelle VM con il relativo livello applicazione. Per altre informazioni sulle macchine virtuali, vedere [Macchine virtuali](https://azure.microsoft.com/services/virtual-machines/).

   I servizi di rete di Azure vengono usati per raggruppare i sistemi SAP insieme ad altre applicazioni in reti virtuali che si connettono ai sistemi locali e a SAP HANA in Azure (istanze Large).

   Per le applicazioni e i database SAP NetWeaver che supportano l'esecuzione in Azure, vedere [SAP Support Note #1928533 - SAP applications on Azure: Supported products and Azure VM types](https://launchpad.support.sap.com/#/notes/1928533) (Nota di supporto SAP 1928533 - Applicazioni SAP in Azure: prodotti supportati e tipi di VM di Azure). Per informazioni su come distribuire soluzioni SAP in Azure, vedere:

  -  [Usare SAP nelle macchine virtuali Windows](../../virtual-machines-windows-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  -  [Usare soluzioni SAP nelle macchine virtuali di Azure](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

- **Sezione sinistra**: mostra l'hardware con certificazione SAP HANA TDI nel modulo per istanze Large in Azure. Le unità di istanze large di HANA sono connesse alle reti virtuali della sottoscrizione di Azure usando la stessa tecnologia della connettività da locale ad Azure. Al 2019 maggio è stata introdotta un'ottimizzazione che consente di comunicare tra le unità di istanze large di HANA e le macchine virtuali di Azure senza coinvolgere il gateway ExpressRoute. Questa ottimizzazione denominata percorso rapido ExpressRoute viene visualizzata in questa architettura (linee rosse). 

Il modulo per istanze di grandi dimensioni di Azure include i componenti seguenti:

- **Calcolo**: server basati su diversi tipi di processori Intel Xeon che forniscono le funzionalità di calcolo necessarie e sono SAP Hana certificate.
- **Rete**: un'infrastruttura di rete ad alta velocità unificata che interconnette i componenti di calcolo, archiviazione e LAN.
- **Archiviazione**: un'infrastruttura di archiviazione a cui si accede tramite un'infrastruttura di rete unificata. La capacità di archiviazione fornita dipende dalla specifica configurazione di SAP HANA in Azure (istanze Large) distribuita. Maggiore capacità di archiviazione è disponibile a un costo mensile aggiuntivo.

Nell'infrastruttura multi-tenant del modulo per istanze di grandi dimensioni i clienti vengono distribuiti come tenant isolati. Al momento della distribuzione del tenant si specifica una sottoscrizione di Azure nell'ambito della propria iscrizione ad Azure. Questa è la sottoscrizione a cui vengono fatturati i costi delle istanze Large di HANA. Questi tenant hanno una relazione 1:1 con la sottoscrizione di Azure. Per una rete, è possibile accedere a un'unità di istanze Large di HANA distribuita in un singolo tenant all'interno di un'area di Azure da diverse reti virtuali appartenenti a diverse sottoscrizioni di Azure. Tali sottoscrizioni devono appartenere alla stessa iscrizione ad Azure. 

Analogamente alle VM, SAP HANA in Azure (istanze Large) è disponibile in più aree di Azure. Per offrire funzionalità di ripristino di emergenza, è possibile fornire il consenso esplicito. I diversi moduli per istanze Large in un'area geopolitica sono collegati tra loro. Ad esempio, i moduli per istanze Large di HANA in Stati Uniti occidentali e Stati Uniti orientali sono connessi tramite un collegamento di rete dedicato ai fini della replica per il ripristino di emergenza. 

Così come è possibile scegliere tra diversi tipi di VM con Macchine virtuali di Azure, è possibile scegliere tra diversi SKU di istanze Large di HANA progettati per diversi tipi di carichi di lavoro di SAP HANA. SAP applica rapporti tra memoria e socket del processore per diversi carichi di lavoro in base alle generazioni del processore Intel. La tabella seguente illustra i tipi di SKU disponibili.

È possibile trovare gli SKU disponibili in [SKU disponibili per HLI](hana-available-skus.md).

**Passaggi successivi**
- Vedere [Architettura di rete di SAP HANA (istanze Large)](hana-network-architecture.md)