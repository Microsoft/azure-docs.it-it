---
title: Considerazioni sulla sicurezza | Microsoft Docs
description: Questo argomento fornisce indicazioni generali per la protezione di SQL Server in esecuzione in una macchina virtuale di Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 7ff77a407dfa87e408170573249876bbefee0abe
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558577"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Considerazioni sulla sicurezza per SQL Server nelle macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo argomento include linee guida di sicurezza generali che consentono di stabilire l'accesso sicuro alle istanze di SQL Server in una macchina virtuale (VM) di Azure.

Azure è conforme a molteplici normative e standard di settore che possono favorire la compilazione di una soluzione compatibile con SQL Server in esecuzione in una macchina virtuale. Per informazioni sulla conformità alle normative con Azure, vedere il [Centro protezione di Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-virtual-machine"></a>Controllare l'accesso alla macchina virtuale SQL

Quando si crea la macchina virtuale SQL Server, è importante controllare attentamente chi ha accesso al computer e a SQL Server. Come prassi generale è consigliabile eseguire le operazioni seguenti:

- Limitare l'accesso a SQL Server solo alle applicazioni e ai client che ne hanno necessità.
- Seguire le procedure consigliate per la gestione di account utente e password.

Le sezioni seguenti offrono alcuni suggerimenti e alcune considerazioni su questi punti.

## <a name="secure-connections"></a>Connessioni sicure

Quando si crea una macchina virtuale di SQL Server con un'immagine della raccolta, l'opzione **SQL Server Connectivity** (Connettività SQL Server) offre la possibilità di scegliere tra **Locale (all'interno della macchina virtuale)** , **Privata (solo all'interno della rete virtuale)** o **Pubblico (Internet)** .

![Connettività di SQL Server](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

Per ottimizzare la sicurezza, scegliere l'opzione più restrittiva per lo scenario in uso. Se ad esempio si esegue un'applicazione che accede a SQL Server nella stessa VM, **Locale** è la scelta più sicura. Se si esegue un'applicazione Azure che richiede l'accesso alla SQL Server, il **privato** protegge la comunicazione con SQL Server solo all'interno della [rete virtuale di Azure](../../../virtual-network/virtual-networks-overview.md)specificata. Se è richiesto l'accesso **Pubblico** (Internet) alla VM di SQL Server, assicurarsi di seguire le altre procedure consigliate in questo argomento per ridurre la superficie di attacco.

Le opzioni selezionate nel portale usano le regole di sicurezza in ingresso nel [gruppo di sicurezza di rete](../../../active-directory/identity-protection/concept-identity-protection-security-overview.md) (NSG) di VM per consentire o negare il traffico di rete alla macchina virtuale. È possibile modificare o creare nuove regole NSG in ingresso per consentire il traffico verso la porta SQL Server (porta predefinita 1433). È anche possibile specificare indirizzi IP specifici che sono autorizzati a comunicare tramite questa porta.

![Regole dei gruppi di sicurezza di rete](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

Oltre alle regole NSG per limitare il traffico di rete, è anche possibile usare Windows Firewall nella macchina virtuale.

Se si usano gli endpoint con un modello di distribuzione classico, rimuovere tutti gli endpoint nella macchina virtuale che non vengono usati. Per istruzioni sull'uso di ACL con gli endpoint, vedere [Gestire l'elenco di controllo di accesso su un endpoint](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Questa operazione non è necessaria per le macchine virtuali che usano la Azure Resource Manager.

Occorre infine considerare di abilitare le connessioni crittografate per l'istanza del motore di database SQL Server nella macchina virtuale di Azure. Configurare l'istanza di SQL server con un certificato firmato. Per altre informazioni, vedere [Abilitazione di connessioni crittografate al Motore di database](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) e [Sintassi della stringa di connessione](/dotnet/framework/data/adonet/connection-string-syntax).

## <a name="encryption"></a>Crittografia

Managed Disks offre Server-Side crittografia e crittografia dischi di Azure. La [crittografia lato server](../../../virtual-machines/disk-encryption.md) offre la crittografia dei dati inattivi e protegge i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. [Crittografia dischi di Azure](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) usa la tecnologia BitLocker o DM-Crypt e si integra con Azure Key Vault per crittografare sia il disco del sistema operativo che i dischi dati. 

## <a name="use-a-non-default-port"></a>Usare una porta diversa da quella predefinita

Per impostazione predefinita, SQL Server è in ascolto sulla porta 1433 che tutti conoscono. Per una maggiore sicurezza, configurare SQL Server per l'ascolto su una porta non predefinita, ad esempio la porta 1401. Se si esegue il provisioning di un'immagine della raccolta di SQL Server nel portale di Azure, è possibile specificare questa porta nel pannello **Impostazioni di SQL Server**.

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Per configurare questa porta dopo il provisioning, sono disponibili due opzioni:

- Per le macchine virtuali di Resource Manager è possibile selezionare **Sicurezza** dalla [ risorsa macchine virtuali SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Da qui è possibile modificare la porta.

  ![Modificare la porta TCP nel portale](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- Per le VM classiche o per le VM di SQL Server di cui non è stato eseguito il provisioning con il portale, è possibile configurare la porta manualmente tramite connessione remota alla VM. Per i passaggi di configurazione, vedere [Configure a Server to Listen on a Specific TCP Port](/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port) (Configurare un server per l'ascolto su una porta TCP specifica). Se si usa questa tecnica manuale, è necessario anche aggiungere una regola di Windows Firewall per consentire il traffico in ingresso su tale porta TCP.

> [!IMPORTANT]
> Specificare una porta non predefinita è una buona idea se la porta di SQL Server è aperta a connessioni Internet pubbliche.

Quando SQL Server è in ascolto su una porta non predefinita, è necessario specificare la porta quando si esegue la connessione. Si consideri ad esempio uno scenario in cui l'indirizzo IP del server è 13.55.255.255 e SQL Server è in ascolto sulla porta 1401. Per connettersi a SQL Server, è necessario specificare `13.55.255.255,1401` nella stringa di connessione.

## <a name="manage-accounts"></a>Gestisci account

È necessario fare in modo che gli utenti malintenzionati non indovinino facilmente i nomi di account o le password. Usare a tal fine i suggerimenti seguenti:

- Creare un account amministratore locale univoco non denominato **Amministratore**.

- Usare password complesse per tutti gli account. Per altre informazioni sulla creazione di password complesse, vedere l'articolo [Crea una password complessa](https://support.microsoft.com/account-billing/how-to-create-a-strong-password-for-your-microsoft-account-f67e4ddd-0dbe-cd75-cebe-0cfda3cf7386).

- Per impostazione predefinita, in Azure viene selezionata l'autenticazione di Windows durante l'installazione della macchina virtuale di SQL Server. L'account di accesso **SA** è pertanto disabilitato e viene assegnata una password tramite il programma di installazione. È consigliabile non usare o abilitare l'account di accesso **SA**. Se è necessario avere un account di accesso SQL, usare una delle strategie seguenti:

  - Creare un account SQL con un nome univoco che abbia appartenenza **sysadmin**. È possibile creare questo account dal portale attivando **Autenticazione di SQL Server** durante il provisioning.

    > [!TIP] 
    > Se non si attiva l'autenticazione di SQL Server durante il provisioning, è necessario modificare manualmente la modalità di autenticazione in **Autenticazione di SQL Server e di Windows**. Per altre informazioni, vedere [Modifica della modalità di autenticazione del server](/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Se è necessario usare l'account di accesso **SA**, attivarlo dopo il provisioning e assegnare una nuova password complessa.

## <a name="additional-best-practices"></a>Procedure consigliate aggiuntive

Oltre alle procedure descritte in questo argomento, è consigliabile rivedere e implementare sia le procedure consigliate di sicurezza locali tradizionali che le procedure consigliate di sicurezza delle macchine virtuali. 

Per altre informazioni sulle procedure di sicurezza locali, vedere [Considerazioni sulla sicurezza per un'installazione di SQL Server](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) e il [Centro sicurezza](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Per altre informazioni sulla sicurezza delle macchine virtuali, vedere la [panoramica sulla sicurezza delle macchine virtuali ](../../../security/fundamentals/virtual-machines-overview.md).


## <a name="next-steps"></a>Passaggi successivi

Se si è interessati anche alle procedure consigliate per le prestazioni, vedere [procedure consigliate per le prestazioni per SQL Server in macchine virtuali di Azure](performance-guidelines-best-practices.md).

Per altri argomenti relativi all'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). In caso di domande sulle macchine virtuali SQL Server, vedere le [domande frequenti](frequently-asked-questions-faq.md).