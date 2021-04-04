---
title: Soluzione VMware di Azure di CloudSimple-accedere a vSphere client
description: Viene descritto come accedere a vCenter del cloud privato.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2e62042a84f7ac2615762e3d9b036e4340bd8c73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899117"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Accedere al portale vCenter del cloud privato

È possibile avviare il portale vCenter del cloud privato dal portale portale di Azure o CloudSimple.  vCenter Portal consente di gestire l'infrastruttura VMware nel cloud privato.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario stabilire la connessione di rete e la risoluzione dei nomi DNS deve essere abilitata per l'accesso al portale di vCenter.  È possibile stabilire una connessione di rete al cloud privato usando una delle opzioni seguenti.

* [Connettersi da locale a CloudSimple usando ExpressRoute](on-premises-connection.md)
* [Configurare una connessione VPN al cloud privato CloudSimple](set-up-vpn.md)

Per configurare la risoluzione dei nomi DNS per i componenti dell'infrastruttura VMware del cloud privato, vedere [configurare il DNS per la risoluzione dei nomi per l'accesso al cloud privato vCenter dalle workstation locali](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Accedere a vCenter da portale di Azure

È possibile avviare il portale vCenter del cloud privato da portale di Azure.

1. Selezionare **Tutti i servizi**.

2. Cercare i **Servizi CloudSimple**.

3. Selezionare il servizio CloudSimple del cloud privato a cui si vuole connettersi.

4. Nella pagina **Panoramica** fare clic su **Visualizza cloud privati VMware**

    ![Panoramica del servizio CloudSimple](media/cloudsimple-service-overview.png)

5. Selezionare il cloud privato dall'elenco dei cloud privati e fare clic su **avvia client vSphere**.

    ![Avviare vSphere client](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Accedere a vCenter dal portale di CloudSimple

È possibile avviare il portale vCenter del cloud privato dal portale di CloudSimple.

1. Accedere al [portale di CloudSimple](access-cloudsimple-portal.md).

2. Dalle **risorse** selezionare il cloud privato a cui si vuole accedere e fare clic su **avvia client vSphere**.

    ![Avviare vSphere Client-Resources](media/cloudsimple-portal-resources-launch-vcenter.png)

3. È anche possibile avviare il portale vCenter dalla schermata di riepilogo del cloud privato.

    ![Avviare vSphere Client-Summary](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare e gestire VLAN/subnet per i cloud privati](create-vlan-subnet.md)
* [Modello di autorizzazione del cloud privato CloudSimple di VMware vCenter](learn-private-cloud-permissions.md)