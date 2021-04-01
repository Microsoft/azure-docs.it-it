---
title: Accedere alla soluzione VMware di Azure tramite CloudSimple da locale
titleSuffix: Azure VMware Solution by CloudSimple
description: Accesso alla soluzione VMware di Azure da CloudSimple dalla rete locale tramite un firewall
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6ff057d99e29c7c6fe30e77f38a0bff265dbe7bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86998893"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Accesso all'ambiente cloud privato di CloudSimple e alle applicazioni da locale

È possibile configurare una connessione dalla rete locale a CloudSimple usando Azure ExpressRoute o la VPN da sito a sito.  Accedere al cloud privato di CloudSimple e ai carichi di lavoro eseguiti nel cloud privato usando la connessione.  È possibile controllare quali porte vengono aperte sulla connessione usando un firewall nella rete locale.  Questo articolo illustra alcuni dei requisiti tipici delle porte delle applicazioni.  Per tutte le altre applicazioni, fare riferimento alla documentazione dell'applicazione per i requisiti delle porte.

## <a name="ports-required-for-accessing-vcenter"></a>Porte necessarie per l'accesso a vCenter

Per accedere alla gestione del cloud privato vCenter e NSX-T, le porte definite nella tabella seguente devono essere aperte nel firewall locale.  

| Porta       | Source (Sorgente)                           | Destination                      | Scopo                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | server DNS locali          | Server DNS del cloud privato        | Obbligatorio per l'invio della ricerca DNS di *AZ.cloudsimple.io* ai server DNS del cloud privato dalla rete locale.       |
| 53 (UDP)   | Server DNS del cloud privato        | server DNS locali          | Obbligatorio per l'invio della ricerca DNS di nomi di dominio locali dal cloud privato vCenter ai server DNS locali. |
| 80 (TCP)   | Rete locale              | Rete di gestione del cloud privato | Obbligatorio per il reindirizzamento dell'URL vCenter da *http* a *https*.                                                           |
| 443 (TCP)  | Rete locale              | Rete di gestione del cloud privato | Obbligatorio per accedere alla gestione vCenter e NSX-T dalla rete locale.                                             |
| 8000 (TCP) | Rete locale              | Rete di gestione del cloud privato | Obbligatorio per vMotion di macchine virtuali da locale a cloud privato.                                            |
| 8000 (TCP) | Rete di gestione del cloud privato | Rete locale              | Obbligatorio per vMotion di macchine virtuali dal cloud privato a locale.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Porte necessarie per l'uso di Active Directory locale come origine di identità

Per configurare Active Directory locale come origine di identità nel cloud privato vCenter, è necessario aprire le porte definite nella tabella.  Per la procedura di configurazione, vedere [usare Azure ad come provider di identità per vCenter in CloudSimple private cloud](./azure-ad.md) .

| Porta         | Source (Sorgente)                           | Destination                                         | Scopo                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Server DNS del cloud privato        | server DNS locali                             | Obbligatorio per l'invio della ricerca DNS di nomi di dominio di Active Directory locali dal cloud privato vCenter ai server DNS locali.          |
| 389 (TCP/UDP) | Rete di gestione del cloud privato | Controller di dominio di Active Directory locali     | Obbligatorio per la comunicazione LDAP dal server vCenter del cloud privato ai controller di dominio Active Directory per l'autenticazione utente.                |
| 636 (TCP)     | Rete di gestione del cloud privato | Controller di dominio di Active Directory locali     | Necessaria per la comunicazione LDAP sicura (LDAPs) dal server vCenter del cloud privato ai controller di dominio Active Directory per l'autenticazione utente. |
| 3268 (TCP)    | Rete di gestione del cloud privato | Server di catalogo globale di Active Directory locale | Obbligatorio per la comunicazione LDAP in una distribuzione di controller multidominio.                                                                        |
| 3269 (TCP)    | Rete di gestione del cloud privato | Server di catalogo globale di Active Directory locale | Obbligatorio per la comunicazione LDAPs in una distribuzione di controller multidominio.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Porte comuni necessarie per accedere alle macchine virtuali del carico di lavoro

Per accedere alle macchine virtuali del carico di lavoro in esecuzione nel cloud privato, è necessario aprire le porte nel firewall locale.  La tabella seguente mostra alcune delle porte comuni necessarie e il relativo scopo.  Per tutti i requisiti di porta specifici dell'applicazione, fare riferimento alla documentazione dell'applicazione.

| Porta         | Source (Sorgente)                         | Destination                          | Scopo                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Rete locale            | Rete del carico di lavoro del cloud privato       | Proteggere l'accesso alla Shell alle macchine virtuali Linux in esecuzione nel cloud privato.              |
| 3389 (TCP)    | Rete locale            | Rete del carico di lavoro del cloud privato       | Desktop remoto per macchine virtuali Windows in esecuzione in un cloud privato.                 |
| 80 (TCP)      | Rete locale            | Rete del carico di lavoro del cloud privato       | Accesso a tutti i server Web distribuiti in macchine virtuali in esecuzione nel cloud privato.        |
| 443 (TCP)     | Rete locale            | Rete del carico di lavoro del cloud privato       | Accesso a tutti i server Web protetti distribuiti in macchine virtuali in esecuzione nel cloud privato. |
| 389 (TCP/UDP) | Rete del carico di lavoro del cloud privato | Rete di Active Directory locale | Aggiungere le macchine virtuali del carico di lavoro Windows al dominio di Active Directory locale.       |
| 53 (UDP)      | Rete del carico di lavoro del cloud privato | Rete locale                  | Accesso al servizio DNS per le macchine virtuali del carico di lavoro nei server DNS locali.         |

## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire VLAN e subnet](./create-vlan-subnet.md)
* [Connettersi alla rete locale usando Azure ExpressRoute](./on-premises-connection.md)
* [Configurare una VPN da sito a sito da locale](./vpn-gateway.md)
