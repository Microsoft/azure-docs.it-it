---
title: Configurare Gestione remota Windows dopo la creazione della macchina virtuale di Azure Azure Marketplace
description: Illustra come configurare Gestione remota Windows (WinRM) dopo la creazione di una macchina virtuale ospitata in Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: dsindona
ms.openlocfilehash: b80325594eedb87293c31de3236bb4690eb89e05
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273019"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Configurare WinRM dopo la creazione della macchina virtuale

> [!IMPORTANT]
> A partire dal 13 aprile 2020, inizieremo a spostare la gestione delle offerte di Macchine virtuali di Azure nel Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in [Creare un'offerta di macchina virtuale](https://aka.ms/CreateAzureVMoffer) di Azure per gestire le offerte migrate.

Questo articolo illustra come configurare una macchina virtuale (VM) esistente ospitata in Azure per abilitare WinRM tramite HTTPS.  Questa configurazione si applica solo alle macchine virtuali basate su Windows e richiede il processo in due passaggi riportato di seguito:

1. Abilitare il traffico di porta per WinRM tramite il protocollo HTTPS.  Questa impostazione per la macchina virtuale verrà configurata nel portale di Azure.
2. Configurare la macchina virtuale per abilitare WinRM eseguendo gli script di PowerShell forniti.


## <a name="enabling-port-traffic"></a>Abilitazione del traffico di porta

Il protocollo Gestione remota Windows su HTTPS usa la porta 5986, che non è abilitata per impostazione predefinita nelle macchine virtuali Windows preconfigurate offerte in Azure Marketplace. Per abilitare questo protocollo, usare la procedura seguente per aggiungere una nuova regola al gruppo di sicurezza di rete (NSG) con il [portale di Azure](https://portal.azure.com).  Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview).

1. Passare al pannello **Macchine virtuali >**   < *nome* >   vm **> Impostazioni/Rete**.
2. Fare clic sul nome del gruppo sicurezza di rete (in questo esempio **testvm11002**) per visualizzare le relative proprietà:

    ![Proprietà del gruppo di sicurezza di rete](./media/nsg-properties.png)
 
3. In **Impostazioni** selezionare **Regole di sicurezza in ingresso** per visualizzare questo pannello.
4. Fare clic su **+ Aggiungi** per creare una nuova regola denominata `WinRM_HTTPS` per la porta TCP 5986.

    ![Aggiungere una regola di sicurezza di rete in ingresso](./media/nsg-new-rule.png)

5. Fare clic su **OK** dopo aver terminato di specificare i valori.  L'elenco delle regole di sicurezza in ingresso dovrebbe contenere le nuove voci seguenti.

    ![Elenco delle regole di sicurezza di rete in ingresso](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Configurare la macchina virtuale per abilitare WinRM 

Usare la procedura seguente per abilitare e configurare la funzionalità Gestione remota Windows nella macchina virtuale Windows.   

1. Stabilire una connessione Desktop remoto alla macchina virtuale ospitata in Azure.  Per altre informazioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  I passaggi rimanenti verranno eseguiti nella macchina virtuale.
2. Scaricare i file seguenti e salvarli in una cartella nella macchina virtuale:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Aprire la **Console di PowerShell** con privilegi elevati (**Esegui come amministratore**). 
4. Eseguire il comando seguente, specificando il parametro obbligatorio: nome di dominio completo (FQDN) per la macchina virtuale: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Script di configurazione di PowerShell 1](./media/powershell-file1.png)

    Questo script dipende dagli altri due file nella stessa cartella.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato WinRM, si è pronti per [distribuire la macchina virtuale dai dischi rigidi virtuali che la costituiscono](./cpp-deploy-vm-vhd.md).
