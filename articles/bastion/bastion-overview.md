---
title: Azure Bastion | Microsoft Docs
description: Informazioni su Azure Bastion, che offre connettività RDP/SSH sicure e ininterrotta alle macchine virtuali senza esporre le porte RDP/SSH esternamente.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: 18672d76c262f8919489ccf132532c117666abe4
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492179"
---
# <a name="what-is-azure-bastion"></a>Che cos'è Azure Bastion?

Azure Bastion è un servizio distribuito che consente di connettersi a una macchina virtuale usano un browser e il portale di Azure. Azure Bastion è un nuovo servizio PaaS completamente gestito dalla piattaforma di cui si effettua il provisioning all'interno della rete virtuale. Offre connettività RDP/SSH sicura e ininterrotta con le macchine virtuali dal portale di Azure tramite TLS. Quando ci si connette tramite Azure Bastion, per le macchine virtuali non è necessario un indirizzo IP pubblico, un agente o un software client speciale.

Bastion offre connettività RDP e SSH sicura per tutte le macchine virtuali nella rete virtuale in cui ne viene effettuato il provisioning. L'uso di Azure Bastion consente di proteggere le macchine virtuali dall'esposizione delle porte RDP/SSH all'esterno, offrendo al tempo stesso l'accesso sicuro tramite RDP/SSH.

## <a name="architecture"></a>Architecture

La distribuzione di Azure Bastion viene effettuata per rete virtuale e non per sottoscrizione/account o macchina virtuale. Dopo aver effettuato il provisioning del servizio Azure Bastion nella rete virtuale, l'esperienza RDP/SSH è disponibile per tutte le macchine virtuali all'interno della stessa rete virtuale.

RDP e SSH sono alcuni dei mezzi fondamentali tramite i quali è possibile connettersi ai carichi di lavoro in esecuzione in Azure. L'esposizione delle porte RDP/SSH su Internet non è consigliabile e viene considerata una superficie di rischio significativa, spesso a causa di vulnerabilità del protocollo. Per contenere l'esposizione agli attacchi, è possibile distribuire bastion host (noti anche come jump server) sul lato pubblico della rete perimetrale. I server bastion host sono progettati e configurati per resistere agli attacchi. I server bastion offrono anche la connettività RDP e SSH per i carichi di lavoro dietro il bastion, nonché all'interno della rete.

![Architettura di Azure Bastion](./media/bastion-overview/architecture.png)

Questa figura illustra l'architettura di una distribuzione di Azure Bastion. In questo diagramma:

* L'host Bastion viene distribuito nella rete virtuale che contiene la subnet AzureBastionSubnet con prefisso minimo/27.
* L'utente si connette al portale di Azure tramite qualsiasi browser HTML5.
* L'utente seleziona la macchina virtuale a cui connettersi.
* Con un solo clic, la sessione RDP/SSH viene aperta nel browser.
* Non è richiesto alcun indirizzo IP pubblico nella macchina virtuale di Azure.

## <a name="key-features"></a>Funzionalità principali

Sono disponibili le funzionalità seguenti:

* **RDP e SSH direttamente nel portale di Azure:** è possibile accedere direttamente alla sessione RDP e SSH nel portale di Azure con una semplice esperienza con singolo clic.
* **Sessione remota su TLS e attraversamento del firewall per RDP/SSH:** Azure Bastion usa un client Web basato su HTML5 trasmesso automaticamente al dispositivo locale, in modo da poter accedere alla sessione RDP/SSH su TLS sulla porta 443, con attraversamento sicuro dei firewall aziendali.
* **Nessun indirizzo IP pubblico richiesto nella macchina virtuale di Azure:** Azure Bastion apre la connessione RDP/SSH alla macchina virtuale di Azure usando l'indirizzo IP privato nella macchina virtuale. Non è necessario un indirizzo IP pubblico nella macchina virtuale.
* **Non occorre gestire gruppi di sicurezza di rete:** Azure Bastion è un servizio PaaS completamente gestito dalla piattaforma di Azure con protezione avanzata internamente per offrire connettività RDP/SSH sicura. Non è necessario applicare alcun gruppo di sicurezza di rete nella subnet di Azure Bastion. Dato che Azure Bastion si connette alle macchine virtuali tramite un indirizzo IP privato, è possibile configurare i gruppi di sicurezza di rete per consentire la connettività RDP/SSH solo da Azure Bastion. In questo modo non è più necessario gestire gruppi di sicurezza di rete ogni volta che occorre connettersi in modo sicuro alle macchine virtuali.
* **Protezione dall'analisi delle porte:** non essendo necessario esporle alla rete Internet pubblica, le macchine virtuali sono protette dall'analisi delle porte eseguita da utenti non autorizzati e malintenzionati all'esterno della rete virtuale.
* **Protezione da exploit zero-day. Protezione avanzata in un'unica posizione:** Azure Bastion è un servizio PaaS completamente gestito dalla piattaforma. Essendo collocato sul perimetro della rete virtuale, non è necessario preoccuparsi della protezione avanzata di ognuna delle macchine virtuali nella rete virtuale. La piattaforma Azure protegge dagli exploit zero-day gestendo automaticamente la protezione avanzata di Azure Bastion e mantenendo sempre aggiornato il servizio.

## <a name="whats-new"></a><a name="new"></a>Novità

Sottoscrivere il feed RSS e visualizzare gli aggiornamenti più recenti delle funzionalità Azure Bastion nella pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Bastion).

## <a name="faq"></a>Domande frequenti

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Creare un host Azure Bastion e connettersi a una VM Windows](tutorial-create-host-portal.md).
* Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
