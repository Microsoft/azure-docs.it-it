---
title: 'Soluzione VMware di Azure di CloudSimple: configurare DNS e DHCP del carico di lavoro per il cloud privato'
description: Viene descritto come configurare DNS e DHCP per le applicazioni e i carichi di lavoro in esecuzione nell'ambiente cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d7531a22d5b0f44fdaae837a75d17ea18852acf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87077297"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Configurare applicazioni e carichi di lavoro DNS e DHCP nel cloud privato CloudSimple

Le applicazioni e i carichi di lavoro in esecuzione in un ambiente cloud privato richiedono la risoluzione dei nomi e i servizi DHCP per la ricerca e l'assegnazione di indirizzi IP.  Per fornire questi servizi, è necessaria un'infrastruttura DHCP e DNS corretta.  È possibile configurare una macchina virtuale per fornire questi servizi nell'ambiente cloud privato.  

## <a name="prerequisites"></a>Prerequisiti

* Un gruppo di porte distribuite con VLAN configurata
* Instrada il programma di installazione a server DNS locali o basati su Internet
* Modello di macchina virtuale o ISO per creare una macchina virtuale

## <a name="linux-based-dns-server-setup"></a>Configurazione del server DNS basato su Linux

Linux offre diversi pacchetti per la configurazione dei server DNS.  Di seguito è [riportato un esempio di installazione di DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) con istruzioni per la configurazione di un server DNS BIND open source.

## <a name="windows-based-setup"></a>Installazione basata su Windows

Negli argomenti Microsoft viene descritto come configurare un server Windows come server DNS e come server DHCP.

* [Windows Server come server DNS](/windows-server/networking/dns/dns-top)
* [Windows Server come server DHCP](/windows-server/networking/technologies/dhcp/dhcp-top)
