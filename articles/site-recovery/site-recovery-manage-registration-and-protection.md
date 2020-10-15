---
title: Rimuovere server e disabilitare la protezione | Documentazione Microsoft
description: Questo articolo descrive come annullare la registrazione di server da un insieme di credenziali di Site Recovery e per disabilitare la protezione per le macchine virtuali e i server fisici.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: sharrai
ms.openlocfilehash: a4f6c318a7521e1fbc03ff3a47e34e992cce44df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89424787"
---
# <a name="remove-servers-and-disable-protection"></a>Rimuovere server e disabilitare la protezione

Questo articolo descrive come annullare la registrazione di server da un insieme di credenziali dei servizi di ripristino e come disabilitare la protezione per i computer protetti da Site Recovery.


## <a name="unregister-a--configuration-server"></a>Annullare la registrazione di un server di configurazione

Se si replicano macchine virtuali VMware o server fisici Windows/Linux in Azure, è possibile annullare la registrazione di un server di configurazione non connesso da un insieme di credenziali come indicato di seguito:

1. [Disabilitare la protezione delle macchine virtuali](#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)
2. [Annullare l'associazione o eliminare](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) i criteri di replica.
3. [Eliminare il server di configurazione](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)

## <a name="unregister-a-vmm-server"></a>Annullare la registrazione di un server VMM

1. Arrestare la replica delle macchine virtuali nei cloud sul server VMM da rimuovere.
2. Eliminare i mapping di rete usati dai cloud nel server VMM da eliminare. In **infrastruttura Site Recovery**  >  **per System Center VMM**  >  **mapping di rete**fare clic con il pulsante destro del mouse sul mapping di rete > **Elimina**.
3. Prendere nota dell'ID del server VMM.
4. Annullare l'associazione dei criteri di replica dai cloud nel server VMM da rimuovere.  In **infrastruttura Site Recovery**  >  **per i criteri di replica di System Center VMM**fare  >   **Replication Policies**doppio clic sui criteri associati. Fare clic con il pulsante destro del mouse sul cloud > **Annulla associazione**.
5. Eliminare il server VMM o il nodo attivo. Nell' **infrastruttura Site Recovery**  >  per i server VMM**di System Center**  >  **VMM Servers**, fare clic con il pulsante destro del mouse sul server > **Elimina**.
6. Se il server VMM si trovava in stato disconnesso, scaricare ed eseguire lo [script di pulizia](https://aka.ms/asr-cleanup-script-vmm) sul server VMM. Aprire PowerShell con l'opzione **Esegui come amministratore** per modificare i criteri di esecuzione per l'ambito predefinito (LocalMachine). Nello script specificare l'ID del server VMM da rimuovere. Lo script rimuove le informazioni sulla registrazione e l'associazione del cloud dal server.
5. Eseguire lo script di pulizia su qualsiasi server VMM secondario.
6. Eseguire lo script di pulizia in qualsiasi altro nodo cluster VMM passivo in cui è installato il Provider.
7. Disinstallare manualmente il Provider nel server VMM. Se si dispone di un cluster, eseguire la rimozione da tutti i nodi.
8. Se la replica delle macchine virtuali veniva eseguita in Azure, è necessario disinstallare l'agente di Servizi di ripristino di Microsoft Azure dagli host Hyper-V nei cloud eliminati.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Annullare la registrazione di un host Hyper-V in un sito di Hyper-V

Gli host Hyper-V non gestiti da VMM vengono raccolti in un sito di Hyper-V. Rimuovere un host in un sito di Hyper-V nel modo seguente:

1. Disabilitare la replica per le VM Hyper-V presenti nell'host.
2. Annullare l'associazione dei criteri per il sito Hyper-V. In **infrastruttura Site Recovery**  >  **per i siti Hyper-V**  >   **criteri di replica**fare doppio clic sui criteri associati. Fare clic con il pulsante destro del mouse sul cloud > **Annulla associazione**.
3. Eliminare gli host Hyper-V. In **Site Recovery infrastruttura**  >  per gli host Hyper-v**di siti Hyper-v**  >  **Hyper-V Hosts**fare clic con il pulsante destro del mouse sul server > **Elimina**.
4. Eliminare il sito Hyper-V dopo che sono stati rimossi tutti gli host dal sito. In **Site Recovery infrastruttura**per siti Hyper-v siti  >  **For Hyper-V Sites**  >  **Hyper-v**fare clic con il pulsante destro del mouse sul sito > **Elimina**.
5. Se l'host Hyper-V si trovava nello stato **Disconnesso**, eseguire quindi lo script seguente in ogni host Hyper-V rimosso. Lo script pulisce le impostazioni del server e ne annulla la registrazione dall'insieme di credenziali.


```powershell
        pushd .
        try
        {
            $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
            $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
            $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
            $isAdmin=$principal.IsInRole($administrators)
            if (!$isAdmin)
            {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;
            }

            $error.Clear()
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'
            $idMgmtCloudContainerId='IdMgmtCloudContainerId'


            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
                {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                if($regNode.IdMgmtCloudContainerId -ne $null)
                {
                    "Removing IdMgmtCloudContainerId"
                    Remove-ItemProperty -Path $asrHivePath -Name $idMgmtCloudContainerId
                }
                "Registry keys removed."
            }

            # First retrieve all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {
            [system.exception]
            Write-Host "Error occurred" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd
```


## <a name="disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure"></a>Disabilitare la protezione per una VM VMware o un server fisico (da VMware ad Azure)

1. In elementi **protetti**  >  **elementi replicati**fare clic con il pulsante destro del mouse sul computer > **disabilitare la replica**.
2. Nella pagina **Disabilita replica** selezionare una di queste opzioni:
    - **Disabilita la replica e rimuovi (scelta consigliata)**. Questa opzione rimuove l'elemento replicato da Azure Site Recovery e arresta la replica per il computer. La configurazione della replica nel server di configurazione viene rimossa e la fatturazione di Site Recovery per questo server protetto viene interrotta. Questa opzione può essere usata solo quando il server di configurazione è connesso.
    - **Rimuovi**. Questa opzione dovrebbe essere usata solo se l'ambiente di origine viene eliminato o non è accessibile (perché non connesso). L'opzione rimuove l'elemento replicato da Azure Site Recovery, interrompendo la fatturazione. La configurazione della replica nel server di configurazione **non** verrà rimossa. 

> [!NOTE]
> Con entrambe le opzioni, il servizio Mobility non verrà disinstallato dai server protetti e dovrà essere disinstallato manualmente. Se si prevede di proteggere di nuovo il server usando lo stesso server di configurazione, si può omettere la disinstallazione del servizio Mobility.

> [!NOTE]
> Se è già stato eseguito il failover di una macchina virtuale ed è in esecuzione in Azure, tenere presente che la disabilitazione della protezione non rimuove o influisce sulla macchina virtuale sottoposta a failover.
## <a name="disable-protection-for-a-azure-vm-azure-to-azure"></a>Disabilitare la protezione per una VM di Azure (da Azure ad Azure)

-  In elementi **protetti**  >  **elementi replicati**fare clic con il pulsante destro del mouse sul computer > **disabilitare la replica**.
> [!NOTE]
> Il servizio di mobilità non verrà disinstallato dai server protetti e dovrà essere disinstallato manualmente. Se si prevede di proteggere di nuovo il server, si può omettere la disinstallazione del servizio di mobilità.

## <a name="disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure"></a>Disabilitare la protezione per una macchina virtuale Hyper-V (da Hyper-V ad Azure)

> [!NOTE]
> Usare questa procedura se si esegue la replica di VM Hyper-V in Azure senza un server VMM. Se la replica delle macchine virtuali viene eseguita con lo scenario **System Center VMM ad Azure**, seguire le istruzioni riportate in Disabilitare la protezione per una macchina virtuale Hyper-V con replica in Azure con lo scenario da System Center VMM ad Azure

1. In elementi **protetti**  >  **elementi replicati**fare clic con il pulsante destro del mouse sul computer > **disabilitare la replica**.
2. In **Disabilita replica** è possibile selezionare le opzioni seguenti:
   - **Disabilita la replica e Rimuovi (scelta consigliata)** : questa opzione rimuove l'elemento replicato da Azure Site Recovery e la replica per il computer viene arrestata. La configurazione della replica nella macchina virtuale locale verrà rimossa e la fatturazione di Site Recovery per questo server protetto viene interrotta.
   - **Rimuovi**. Questa opzione dovrebbe essere usata solo se l'ambiente di origine viene eliminato o non è accessibile (perché non connesso). L'opzione rimuove l'elemento replicato da Azure Site Recovery, interrompendo la fatturazione. La configurazione della replica nella macchina virtuale locale **non** verrà rimossa. 

    > [!NOTE]
    > Se è stata scelta l'opzione **Rimuovi**, eseguire quindi il set di script seguente per la pulizia del server Hyper-V locale delle impostazioni di replica.

    > [!NOTE]
    > Se è già stato eseguito il failover di una macchina virtuale ed è in esecuzione in Azure, tenere presente che la disabilitazione della protezione non rimuove o influisce sulla macchina virtuale sottoposta a failover.

1. Nel server host Hyper-V di origine, rimuovere la replica per la macchina virtuale. Sostituire SQLVM1 con il nome della macchina virtuale ed eseguire lo script da un'istanza amministrativa di PowerShell.

```powershell
    $vmName = "SQLVM1"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario"></a>Disabilitare la protezione per una macchina virtuale Hyper-V con replica in Azure con lo scenario da System Center VMM ad Azure

1. In elementi **protetti**  >  **elementi replicati**fare clic con il pulsante destro del mouse sul computer > **disabilitare la replica**.
2. In **Disabilita replica** selezionare una di queste opzioni:

   - **Disabilita la replica e Rimuovi (scelta consigliata)** : questa opzione rimuove l'elemento replicato da Azure Site Recovery e la replica per il computer viene arrestata. La configurazione della replica nella macchina virtuale locale viene rimossa e la fatturazione di Site Recovery per questo server protetto viene interrotta.
   - **Rimuovi**. Questa opzione dovrebbe essere usata solo se l'ambiente di origine viene eliminato o non è accessibile (perché non connesso). L'opzione rimuove l'elemento replicato da Azure Site Recovery, interrompendo la fatturazione. La configurazione della replica nella macchina virtuale locale **non** verrà rimossa. 

     > [!NOTE]
     > Se è stata scelta l'opzione **Rimuovi**, eseguire quindi gli script seguenti per la pulizia del server VMM locale delle impostazioni di replica.
3. Eseguire questo script nel server VMM di origine, usando PowerShell dalla console VMM. Sono necessari privilegi di amministratore. Sostituire il segnaposto **SQLVM1** con il nome della macchina virtuale.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. I passaggi sopra descritti cancellano le impostazioni di replica del server VMM. Per arrestare la replica per la macchina virtuale in esecuzione nel server host Hyper-V, eseguire questo script. Sostituire SQLVM1 con il nome della macchina virtuale e host01.contoso.com con il nome del server host Hyper-V.

```powershell
    $vmName = "SQLVM1"
    $hostName  = "host01.contoso.com"
    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
    $replicationService.RemoveReplicationRelationship($vm.__PATH)
```

## <a name="disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario"></a>Disabilitare la protezione per una macchina virtuale Hyper-V con replica in un server VMM secondario con lo scenario da System Center VMM a VMM

1. In elementi **protetti**  >  **elementi replicati**fare clic con il pulsante destro del mouse sul computer > **disabilitare la replica**.
2. In **Disabilita replica** selezionare una di queste opzioni:

   - **Disabilita la replica e Rimuovi (scelta consigliata)** : questa opzione rimuove l'elemento replicato da Azure Site Recovery e la replica per il computer viene arrestata. La configurazione della replica nella macchina virtuale locale viene rimossa e la fatturazione di Site Recovery per questo server protetto viene interrotta.
   - **Rimuovi**. Questa opzione dovrebbe essere usata solo se l'ambiente di origine viene eliminato o non è accessibile (perché non connesso). L'opzione rimuove l'elemento replicato da Azure Site Recovery, interrompendo la fatturazione. La configurazione della replica nella macchina virtuale locale **non** verrà rimossa. Eseguire il set di script seguente per la pulizia delle macchine virtuali locali delle impostazioni di replica.
     > [!NOTE]
     > Se è stata scelta l'opzione **Rimuovi**, eseguire quindi gli script seguenti per la pulizia del server VMM locale delle impostazioni di replica.

3. Eseguire questo script nel server VMM di origine, usando PowerShell dalla console VMM. Sono necessari privilegi di amministratore. Sostituire il segnaposto **SQLVM1** con il nome della macchina virtuale.

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Set-SCVirtualMachine -VM $vm -ClearDRProtection
    ```

4. Nel server VMM secondario eseguire questo script per la pulizia delle impostazioni per la macchina virtuale secondaria:

    ```powershell
    $vm = get-scvirtualmachine -Name "SQLVM1"
    Remove-SCVirtualMachine -VM $vm -Force
    ```

5. Nel server VMM secondario aggiornare le macchine virtuali nel server host Hyper-V in modo che la VM secondaria venga rilevata di nuovo nella console VMM.
6. I passaggi sopra descritti cancellano le impostazioni di replica nel server VMM. Se si vuole arrestare la replica per la macchina virtuale, eseguire lo script seguente nelle VM primarie e secondarie. Sostituire SQLVM1 con il nome della macchina virtuale.

    ```powershell
    Remove-VMReplication –VMName "SQLVM1"
    ```
