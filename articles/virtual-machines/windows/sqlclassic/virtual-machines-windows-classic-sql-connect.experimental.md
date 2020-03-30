---
title: Connettersi a una macchina virtuale di SQL Server in Azure (distribuzione classica) | Microsoft Docs
description: Informazioni su come connettersi a SQL Server in esecuzione su una macchina virtuale di Azure. In questo argomento viene usato il modello di distribuzione classica. Gli scenari variano a seconda della configurazione di rete e della posizione del client.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental: true
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 1c2d5ae5d85624ea172eb9a95d4086dd71287c4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978219"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Connettersi a una macchina virtuale di SQL Server in Azure (distribuzione classica)
> [!div class="op_single_selector"]
> * [Gestione risorse](../sql/virtual-machines-windows-sql-connect.md)
> * [Classico](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Panoramica
Questo argomento descrive la modalità di connessione all'istanza di SQL Server in esecuzione su una macchina virtuale di Azure. Illustra alcuni [scenari di connettività generali](#connection-scenarios) e quindi descrive la [procedura dettagliata per la configurazione della connettività di SQL Server in una macchina virtuale di Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Azure include due diversi modelli di distribuzione per la creazione e l'utilizzo delle risorse: [Resource Manager e Classic](../../../azure-resource-manager/management/deployment-models.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.  Se si usano macchine virtuali di Resource Manager, vedere [Connect to a SQL Server Virtual Machine on Azure using Resource Manager](../sql/virtual-machines-windows-sql-connect.md)(Connettersi a una macchina virtuale SQL Server su Azure tramite Resource Manager).

## <a name="connection-scenarios"></a>Scenari di connessione
La modalità di connessione di un client a SQL Server in esecuzione in una macchina virtuale varia a seconda della posizione del client e della configurazione tra il computer e la rete. Tali scenari includono:

* [Configurare e connettersi a più macchine virtuali di SQL Server nello stesso servizio cloud di Azure](#connect-to-sql-server-in-the-same-cloud-service)
* [Connettersi a SQL Server tramite Internet](#connect-to-sql-server-over-the-internet)
* [Connettersi a SQL Server nella stessa rete virtuale](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Prima di connettersi con uno di questi metodi, è necessario seguire i [passaggi in questo articolo per configurare la connettività](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Configurare e connettersi a più macchine virtuali di SQL Server nello stesso servizio cloud di Azure
È possibile creare più macchine virtuali nello stesso servizio cloud. Per comprendere meglio questo scenario, vedere [Come connettere le macchine virtuali con una rete virtuale o un servizio cloud](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service). In questo caso un client su una macchina virtuale tenta di connettersi a SQL Server in esecuzione su un'altra macchina virtuale nello stesso servizio cloud.

In questo scenario è possibile connettersi usando il **nome** della macchina virtuale, anche visualizzato come **Nome computer** o **hostname** nel portale. Questo è il nome specificato durante la creazione della macchina virtuale. Ad esempio, se la macchina virtuale SQL è stata denominata **mysqlvm**, una macchina virtuale client nello stesso servizio cloud potrebbe usare la stringa di connessione seguente per connettersi:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Connettersi a SQL Server tramite Internet
Se si desidera connettersi al motore di database di SQL Server tramite Internet, è necessario creare un endpoint della macchina virtuale per le comunicazioni TCP in ingresso. In questo passaggio di configurazione di Azure, il traffico della porta TCP in ingresso viene indirizzato a una porta TCP accessibile alla macchina virtuale.

Per connettersi tramite Internet è necessario usare il nome DNS e il numero di porta dell'endpoint della macchina virtuale (configurati più avanti in questo articolo). Per trovare il nome DNS, passare al portale di Azure e selezionare **Macchine virtuali (classico).** Selezionare quindi la macchina virtuale. Il **Nome DNS** è visualizzato nella sezione **Panoramica**.

Si consideri ad esempio una macchina virtuale classica denominata **mysqlvm** con nome DNS **mysqlvm7777.cloudapp.net** ed endpoint di macchina virtuale **57500**. Supponendo che la connettività sia configurata correttamente, la stringa di connessione seguente può essere usata per accedere alla macchina virtuale da qualsiasi posizione in Internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Anche se questa stringa di connessione abilita la connettività per i client tramite Internet, non significa che chiunque può connettersi all'istanza di SQL Server. I client esterni dovranno disporre del nome utente e della password corretti. Per una maggiore sicurezza, non usare la nota porta 1433 per l'endpoint pubblico della macchina virtuale. Se possibile, è consigliabile aggiungere un ACL all'endpoint per limitare il traffico ai soli client autorizzati. Per istruzioni sull'uso di ACL con gli endpoint, vedere [Gestire l'elenco di controllo di accesso su un endpoint](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Quando si usa questa tecnica per comunicare con SQL Server, tutti i dati in uscita dal data center di Azure sono soggetti ai normali [prezzi dei trasferimenti dei dati in uscita](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Connettersi a SQL Server nella stessa rete virtuale
[rete virtuale](../../../virtual-network/virtual-networks-overview.md) supporta scenari aggiuntivi. È possibile connettere le macchine virtuali nella stessa rete virtuale, anche se si trovano in servizi cloud diversi. Con una [VPN da sito a sito](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)è possibile creare un'architettura ibrida che connette le macchine virtuali a computer e reti locali.

Le reti virtuali consentono inoltre di aggiungere le macchine virtuali di Azure a un dominio. L'aggiunta a un dominio è l'unico modo per usare l'autenticazione di Windows con SQL Server. Gli altri scenari di connessione richiedono l'autenticazione SQL con nomi utente e password.

Se si prevede di configurare un ambiente di dominio e l'autenticazione di Windows, non è necessario configurare l'endpoint pubblico o l'autenticazione SQL e gli account di accesso. In questo scenario è possibile connettersi all'istanza di SQL Server specificando il nome della macchina virtuale SQL Server nella stringa di connessione. L'esempio seguente presuppone che sia stata configurata l'autenticazione di Windows e che all'utente sia stato concesso l'accesso all'istanza di SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Procedura per la configurazione della connettività di SQL Server in una macchina virtuale di Azure
I passaggi seguenti illustrano come connettersi all'istanza di SQL Server su Internet mediante SQL Server Management Studio (SSMS). Tuttavia, gli stessi passaggi si applicano per rendere accessibile la macchina virtuale di SQL Server per le applicazioni in esecuzione sia in locale che in Azure.

Prima di poter eseguire la connessione all'istanza di SQL Server da un’altra VM o da Internet, è necessario completare le seguenti attività:

* [Creare un endpoint TCP per la macchina virtuale](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Aprire le porte TCP in Windows Firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Configurare SQL Server per l'ascolto sul protocollo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Configurare SQL Server per l'autenticazione in modalità mista](#configure-sql-server-for-mixed-mode-authentication)
* [Creare account di accesso di autenticazione di SQL ServerCreate SQL Server authentication logins](#create-sql-server-authentication-logins)
* [Determinare il nome DNS della macchina virtuale](#determine-the-dns-name-of-the-virtual-machine)
* [Connettersi al Motore di databaseDatabase Engine da un altro computerConnect to the Motore di databaseDatabase Engine from another computer](#connect-to-the-database-engine-from-another-computer)

Il percorso di connessione è riepilogato nel diagramma seguente:

![Connessione a una macchina virtuale di SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Passaggi successivi
Se si intende anche usare gruppi di disponibilità AlwaysOn per la disponibilità elevata e il ripristino di emergenza, è consigliabile implementare un listener. I client del database si connettono al listener anziché connettersi direttamente a una delle istanze di SQL Server. Il listener indirizza i client alla replica primaria nel gruppo di disponibilità. Per altre informazioni, vedere l'articolo relativo alla [configurazione di un listener di ILB per gruppi di disponibilità AlwaysOn in Azure](../classic/ps-sql-int-listener.md).

È importante esaminare tutte le procedure consigliate sulla sicurezza per SQL Server in esecuzione in una macchina virtuale di Azure. Per altre informazioni, vedere [Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](../sql/virtual-machines-windows-sql-security.md).

[Esplorare il percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) per SQL Server in macchine virtuali di Azure. 

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [SQL Server in Macchine virtuali di Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

