---
title: Configurare DHCPv6 per macchine virtuali Linux
titleSuffix: Azure Load Balancer
description: Questo articolo illustra come configurare DHCPv6 per macchine virtuali Linux.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, dual stack, ip pubblico, ipv6 nativo, mobili, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: d8bd62bab627beb70a8fcba276bf8c2eca309c45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006747"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Configurare DHCPv6 per macchine virtuali Linux


Per alcune delle immagini di macchina virtuale Linux in Azure Marketplace, DHCPv6 non è configurato per impostazione predefinita. Per supportare IPv6, è necessario configurare DHCPv6 nella distribuzione del sistema operativo Linux usato. Le diverse distribuzioni Linux richiedono modalità diverse per configurare DHCPv6 perché usano pacchetti diversi.

> [!NOTE]
> Le immagini recenti di SUSE Linux e CoreOS in Azure Marketplace sono state preconfigurate con DHCPv6. Quando si usano queste immagini, non sono necessarie altre modifiche.

Questo documento descrive come abilitare DHCPv6 in modo che la macchina virtuale Linux ottenga un indirizzo IPv6.

> [!WARNING]
> La modifica non corretta dei file di configurazione di rete può causare la perdita dell'accesso di rete alla macchina virtuale. È consigliabile testare le modifiche alla configurazione nei sistemi non di produzione. Le istruzioni riportate in questo articolo sono state testate sulle versioni più recenti delle immagini Linux in Azure Marketplace. Per istruzioni più dettagliate, consultare la documentazione per la versione di Linux usata.

## <a name="ubuntu"></a>Ubuntu

1. Modificare il file */etc/dhcp/dhclient6.conf* e aggiungere la riga seguente:

    ```config
    timeout 10;
    ```

2. Modificare la configurazione della rete per l'interfaccia eth0 con la configurazione seguente:

   * In **Ubuntu 12.04 e 14.04** modificare il file */etc/network/interfaces.d/eth0.cfg*. 
   * In **Ubuntu 16.04** modificare il file */etc/network/interfaces.d/50-cloud-init.cfg*.

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

A partire da Ubuntu 17,10, il meccanismo di configurazione di rete predefinito è [Netplan]( https://netplan.io).  Al momento dell'installazione/creazione dell'istanza, NETPLAN legge la configurazione di rete dai file di configurazione YAML in questa posizione:/{lib e così via, Esegui}/Netplan/*. yaml.

Includere un'istruzione *dhcp6: true* per ogni interfaccia Ethernet nella configurazione.  Ad esempio:

```config
network:
  version: 2
  ethernets:
    eno1:
      dhcp6: true
```

Durante l'avvio anticipato, il Netplan "Network renderer" scrive la configurazione in/Run per passare il controllo dei dispositivi al daemon di rete specificato per informazioni di riferimento su NETPLAN, vedere https://netplan.io/reference .
 
## <a name="debian"></a>Debian

1. Modificare il file */etc/dhcp/dhclient6.conf* e aggiungere la riga seguente:

    ```config
    timeout 10;
    ```

2. Modificare il file */etc/network/interfaces* e aggiungere la configurazione seguente:

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS e Oracle Linux

1. Modificare il file */etc/sysconfig/network* e aggiungere il parametro seguente:

    ```config
    NETWORKING_IPV6=yes
    ```

2. Modificare il file */etc/sysconfig/network-scripts/ifcfg-eth0* e aggiungere i due parametri seguenti:

    ```config
    IPV6INIT=yes
    DHCPV6C=yes
    ```

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 e openSUSE 13

Le immagini SLES (SUSE Linux Enterprise Server) e openSUSE recenti in Azure sono state preconfigurate con DHCPv6. Quando si usano queste immagini, non sono necessarie altre modifiche. Con una macchina virtuale basata su un'immagine SUSE personalizzata o precedente, eseguire le operazioni seguenti:

1. Installare il pacchetto `dhcp-client` , se necessario:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Modificare il file */etc/sysconfig/network/ifcfg-eth0* e aggiungere il parametro seguente:

    ```config
    DHCLIENT6_MODE='managed'
    

3. Renew the IPv6 address:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 e openSUSE Leap

Le immagini recenti di SLES e openSUSE in Azure sono state preconfigurate con DHCPv6. Quando si usano queste immagini, non sono necessarie altre modifiche. Con una macchina virtuale basata su un'immagine SUSE personalizzata o precedente, eseguire le operazioni seguenti:

1. Modificare il file */etc/sysconfig/network/ifcfg-eth0* e sostituire il parametro `#BOOTPROTO='dhcp4'` con il valore seguente:

    ```config
    BOOTPROTO='dhcp'
    ```

2. Aggiungere il parametro seguente al file */etc/sysconfig/network/ifcfg-eth0*:

    ```config
    DHCLIENT6_MODE='managed'
    ```

3. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Le immagini recenti di CoreOS in Azure sono state preconfigurate con DHCPv6. Quando si usano queste immagini, non sono necessarie altre modifiche. Con una macchina virtuale basata su un'immagine CoreOS personalizzata o precedente, eseguire le operazioni seguenti:

1. Modificare il file */etc/systemd/network/10_dhcp.network*:

    ```config
    [Match]
    eth0

    [Network]
    DHCP=ipv6
    ```

2. Rinnovare l'indirizzo IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
