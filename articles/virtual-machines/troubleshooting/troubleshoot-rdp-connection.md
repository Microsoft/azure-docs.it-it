---
title: Non è possibile connettersi tramite RDP a una macchina virtuale Windows in Azure | Documentazione Microsoft
description: Risoluzione dei problemi di connessione con Desktop remoto di una macchina virtuale Windows in Azure
keywords: Errore di desktop remoto, errore di connessione al desktop remoto, impossibile connettersi alla macchina virtuale, risoluzione dei problemi di desktop remoto
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: akjosh
ms.openlocfilehash: ba6dda86475456b6797d27e11727d70261be2e1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91439990"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Risolvere i problemi di connessione con Desktop remoto di una macchina virtuale di Azure
La mancata connessione Remote Desktop Protocol (RDP) alla macchina virtuale di Azure basata su Windows può avere varie cause, impedendo l'accesso alla VM. Il problema può dipendere dal servizio di desktop remoto nella VM, dalla connessione di rete o dal client di desktop remoto nel computer host. Questo articolo illustra alcuni dei metodi più comuni per risolvere i problemi di connessione RDP. 

Per ricevere assistenza in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Passaggi rapidi per la risoluzione dei problemi
Dopo ogni passaggio della procedura di risoluzione dei problemi, tentare la riconnessione alla VM:

1. Ripristinare la configurazione del desktop remoto.
2. Controllare le regole degli endpoint del gruppo di sicurezza di rete/Servizi cloud.
3. Esaminare i log della console della VM.
4. Reimpostare la scheda di interfaccia di rete per la macchina virtuale.
5. Controllare l'integrità delle risorse della VM.
6. Reimpostare la password della VM.
7. Riavviare la VM.
8. Ripetere la distribuzione della VM.

Continuare la lettura per procedure dettagliate e altre informazioni. Verificare che le apparecchiature di rete locali quali router e firewall non blocchino la porta TCP in uscita 3389, come indicato negli [scenari dettagliati di risoluzione dei problemi RDP](detailed-troubleshoot-rdp.md).

> [!TIP]
> Se il pulsante **Connetti** della VM è disattivato nel portale e non si è connessi ad Azure tramite una connessione [Express Route](../../expressroute/expressroute-introduction.md) o [VPN da sito a sito](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), per poter usare il protocollo RDP è necessario creare un indirizzo IP pubblico e assegnarlo alla VM. Altre informazioni sono disponibili in [Indirizzi IP pubblici in Azure](../../virtual-network/public-ip-addresses.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Risoluzione dei problemi di connessione RDP
È possibile risolvere i problemi delle macchine virtuali create usando il modello di distribuzione di Resource Manager con uno dei metodi seguenti:

* Portale di Azure, indicato per ripristinare rapidamente la configurazione della connessione RDP o le credenziali dell'utente nel caso in cui non siano stati installati gli strumenti di Azure.
* Azure PowerShell: indicato se si ha familiarità con i prompt di PowerShell e si intende ripristinare rapidamente la configurazione della connessione RDP o le credenziali utente usando i cmdlet di Azure PowerShell.

Altri passaggi per la risoluzione dei problemi delle macchine virtuali create usando [modello di distribuzione classico](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Risolvere i problemi usando il portale di Azure
Dopo ogni passaggio della procedura di risoluzione dei problemi, ritentare di connettersi alla VM. Se ancora non è possibile connettersi, procedere al passaggio successivo.

1. **Ripristinare la connessione RDP**. Questa procedura di risoluzione dei problemi consente di ripristinare la configurazione della connessione RDP quando, ad esempio, le connessioni remote sono disabilitate o le regole di Windows Firewall la bloccano.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Reimposta password**. Impostare la **Modalità** su **Reset configuration only** (Reimposta solo configurazione) quindi fare clic sul pulsante **Aggiorna**:
   
    ![Reimpostare la configurazione di R D nella portale di Azure.](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Verificare le regole del gruppo di sicurezza di rete**. Usare la [verifica del flusso IP](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) per verificare che una regola in un gruppo di sicurezza di rete blocchi il traffico verso o da una macchina virtuale. È anche possibile esaminare le regole del gruppo di sicurezza effettive per verificare che la regola NSG di consenso in ingresso esista e abbia la priorità per la porta RDP, ovvero la porta 3389 predefinita. Per altre informazioni, vedere [Uso di regole di sicurezza effettive per risolvere i problemi di flusso del traffico delle VM](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Rivedere la diagnostica di avvio della macchina virtuale**. Questa procedura di risoluzione dei problemi esamina i log della console della macchina virtuale per stabilire se la macchina virtuale segnala un problema. Poiché la diagnostica di avvio non è abilitata su tutte le macchine virtuali, la procedura può essere facoltativa.
   
    Le procedure di risoluzione dei problemi specifici non rientrano nell'ambito di questo articolo, ma potrebbero segnalare un problema di più ampia portata che ha effetto sulla connessione RDP. Per altre informazioni sull'esame dei log della console e delle schermate della macchina virtuale, vedere l'articolo sulla [diagnostica di avvio per le macchine virtuali](boot-diagnostics.md).

4. **Reimpostare la scheda di interfaccia di rete per la macchina virtuale**. Per altre informazioni, vedere [How to reset NIC for Azure Windows VM](./reset-network-interface.md) (Come reimpostare la scheda di interfaccia di rete per la VM Windows di Azure).
5. **Controllare l'integrità delle risorse della VM**. Questa procedura di risoluzione dei problemi verifica l'assenza di problemi noti della piattaforma Azure che potrebbero avere effetto sulla connessione alla macchina virtuale.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Integrità delle risorse**. Una macchina virtuale integra viene indicata come **Disponibile**:
   
    ![Verificare l'integrità delle risorse V M nell'portale di Azure.](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Reimpostare le credenziali utente**. Questa procedura di risoluzione dei problemi consente di reimpostare la password di un account amministratore locale se non si è sicuri o si sono dimenticate le credenziali.  Dopo aver eseguito l'accesso alla VM, è necessario reimpostare la password per l'utente.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Reimposta password**. Verificare che la **Modalità** sia impostata su **Reimpostazione password**, quindi immettere il nome utente e una nuova password. Infine fare clic sul pulsante **Aggiorna**:
   
    ![Reimpostare le credenziali utente nel portale di Azure.](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Riavviare la VM**. Questa procedura di risoluzione dei problemi consente di correggere eventuali problemi sottostanti riscontrati dalla macchina virtuale.
   
    Selezionare la macchina virtuale nel portale di Azure e fare clic sulla scheda **Panoramica**. Fare clic sul pulsante **Riavvia**:
   
    ![Riavviare la versione M nella portale di Azure.](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Ripetere la distribuzione della VM**. Questa procedura di risoluzione dei problemi ripete la distribuzione della macchina virtuale in un altro host all'interno di Azure per correggere eventuali problemi relativi alla rete o alla piattaforma sottostante.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Ridistribuisci** e quindi su **Ridistribuisci**:
   
    ![Ripetere la distribuzione della macchina virtuale nel portale di Azure](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    Al termine di questa operazione i dati temporanei del disco vanno persi e gli indirizzi IP dinamici associati alla macchina virtuale vengono aggiornati.

9. **Verificare il routing**. Usare la funzionalità [Hop successivo](../../network-watcher/diagnose-vm-network-routing-problem.md) di Network Watcher per verificare che una route non impedisca il routing del traffico da o verso una macchina virtuale. È anche possibile esaminare le route per determinare tutte quelle valide per un'interfaccia di rete. Per altre informazioni, vedere [Uso di route valide per risolvere i problemi relativi al flusso di traffico delle macchine virtuali](../../virtual-network/diagnose-network-routing-problem.md).

10. Assicurarsi che ogni firewall locale o firewall nel computer consenta il traffico TCP 3389 in uscita verso Azure.

Se continuano a verificarsi errori RDP, è possibile [aprire una richiesta di supporto](https://azure.microsoft.com/support/options/) o leggere i [passaggi e concetti relativi alla risoluzione dettagliata dei problemi RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-using-azure-powershell"></a>Risoluzione dei problemi con Azure PowerShell
Se non è già stato fatto, [installare e configurare la versione più recente di Azure PowerShell](/powershell/azure/).

Negli esempi seguenti vengono usate alcune variabili, ad esempio `myResourceGroup`, `myVM` e `myVMAccessExtension`. Sostituire i nomi e i percorsi delle variabili con i valori personalizzati.

> [!NOTE]
> Reimpostare le credenziali utente e la configurazione della connessione RDP usando il cmdlet [Set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) di PowerShell. Negli esempi seguenti `myVMAccessExtension` è un nome specificato come parte del processo. Se in precedenza è stato usato il VMAccessAgent, è possibile ottenere il nome dell'estensione esistente usando `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` per controllare le proprietà della VM. Esaminare quindi la sezione dell'output relativa alle estensioni per visualizzare il nome.

Dopo ogni passaggio della procedura di risoluzione dei problemi, ritentare di connettersi alla VM. Se ancora non è possibile connettersi, procedere al passaggio successivo.

1. **Ripristinare la connessione RDP**. Questa procedura di risoluzione dei problemi consente di ripristinare la configurazione della connessione RDP quando, ad esempio, le connessioni remote sono disabilitate o le regole di Windows Firewall la bloccano.
   
    L'esempio seguente ripristina la connessione RDP in una macchina virtuale denominata `myVM` nella località `WestUS` e nel gruppo di risorse `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Verificare le regole del gruppo di sicurezza di rete**. Questo passaggio verifica che le regole del gruppo di sicurezza di rete consentono il traffico RDP. La porta RDP predefinita è la porta TCP 3389. Quando si crea la macchina virtuale, non è possibile creare automaticamente una regola che consenta il traffico RDP.
   
    Innanzitutto assegnare tutti i dati di configurazione del gruppo di sicurezza di rete alla variabile `$rules`. L'esempio seguente ottiene informazioni sul gruppo di sicurezza di rete denominato `myNetworkSecurityGroup` nel gruppo di risorse denominato `myResourceGroup`:
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Visualizzare quindi le regole configurate per questo gruppo di sicurezza di rete. Assicurarsi che esista una regola che consente le connessioni in ingresso sulla porta TCP 3389, come indicato di seguito:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    L'esempio seguente illustra una regola di sicurezza valida che consente il traffico RDP. È visualizzata la corretta configurazione di `Protocol`, `DestinationPortRange`, `Direction` e `Access`:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Se non è presente una regola che consente il traffico RDP, [creare una regola del gruppo di sicurezza di rete](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Consentire il traffico sulla porta TCP 3389.
3. **Reimpostare le credenziali utente**. Questa procedura di risoluzione dei problemi consente di reimpostare la password dell'account amministratore locale specificata se non si è sicuri o si sono dimenticate le credenziali.
   
    Innanzitutto specificare il nome utente e una nuova password assegnando le credenziali alla variabile `$cred`, come indicato di seguito:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Aggiornare quindi le credenziali nella macchina virtuale. L'esempio seguente aggiorna le credenziali di una macchina virtuale denominata `myVM` nella località `WestUS` e nel gruppo di risorse `myResourceGroup`:
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Riavviare la VM**. Questa procedura di risoluzione dei problemi consente di correggere eventuali problemi sottostanti riscontrati dalla macchina virtuale.
   
    L'esempio seguente riavvia la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Ripetere la distribuzione della VM**. Questa procedura di risoluzione dei problemi ripete la distribuzione della macchina virtuale in un altro host all'interno di Azure per correggere eventuali problemi relativi alla rete o alla piattaforma sottostante.
   
    L'esempio seguente ridistribuisce la macchina virtuale denominata `myVM` nella località `WestUS` e nel gruppo di risorse `myResourceGroup`:
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Verificare il routing**. Usare la funzionalità [Hop successivo](../../network-watcher/diagnose-vm-network-routing-problem.md) di Network Watcher per verificare che una route non impedisca il routing del traffico da o verso una macchina virtuale. È anche possibile esaminare le route per determinare tutte quelle valide per un'interfaccia di rete. Per altre informazioni, vedere [Uso di route valide per risolvere i problemi relativi al flusso di traffico delle macchine virtuali](../../virtual-network/diagnose-network-routing-problem.md).

7. Assicurarsi che ogni firewall locale o firewall nel computer consenta il traffico TCP 3389 in uscita verso Azure.

Se continuano a verificarsi errori RDP, è possibile [aprire una richiesta di supporto](https://azure.microsoft.com/support/options/) o leggere i [passaggi e concetti relativi alla risoluzione dettagliata dei problemi RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Risolvere i problemi delle VM create con il modello di distribuzione classica

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Dopo ogni passaggio della procedura di risoluzione dei problemi, tentare la riconnessione alla VM.

1. **Ripristinare la connessione RDP**. Questa procedura di risoluzione dei problemi consente di ripristinare la configurazione della connessione RDP quando, ad esempio, le connessioni remote sono disabilitate o le regole di Windows Firewall la bloccano.
   
    Selezionare la macchina virtuale nel portale di Azure. Fare clic sul pulsante  **Altro** quindi su **Reimposta accesso remoto**:
   
    ![Reimpostare la configurazione di R D nella portale di Azure per V M usando il modello di distribuzione classica.](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Controllare gli endpoint di Servizi cloud**. Questa procedura di risoluzione dei problemi verifica che gli endpoint di Servizi cloud consentano il traffico RDP. La porta RDP predefinita è la porta TCP 3389. Quando si crea la macchina virtuale, non è possibile creare automaticamente una regola che consenta il traffico RDP.
   
   Selezionare la macchina virtuale nel portale di Azure. Fare clic sul pulsante **Endpoint** per visualizzare gli endpoint configurati per la macchina virtuale. Verificare l'esistenza di endpoint che consentono il traffico RDP sulla porta TCP 3389.
   
   L'esempio seguente mostra endpoint validi che consentono il traffico RDP:
   
   ![Verificare gli endpoint dei servizi cloud nella portale di Azure per V M usando il modello di distribuzione classica.](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Se non è presente un endpoint che consente il traffico RDP, [creare un endpoint di Servizi cloud](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints). Consentire il traffico TCP sulla porta privata 3389.
3. **Rivedere la diagnostica di avvio della macchina virtuale**. Questa procedura di risoluzione dei problemi esamina i log della console della macchina virtuale per stabilire se la macchina virtuale segnala un problema. Poiché la diagnostica di avvio non è abilitata su tutte le macchine virtuali, la procedura può essere facoltativa.
   
    Le procedure di risoluzione dei problemi specifici non rientrano nell'ambito di questo articolo, ma potrebbero segnalare un problema di più ampia portata che ha effetto sulla connessione RDP. Per altre informazioni sull'esame dei log della console e delle schermate della macchina virtuale, vedere l'articolo sulla [diagnostica di avvio per le macchine virtuali](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Controllare l'integrità delle risorse della VM**. Questa procedura di risoluzione dei problemi verifica l'assenza di problemi noti della piattaforma Azure che potrebbero avere effetto sulla connessione alla macchina virtuale.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Integrità delle risorse**. Una macchina virtuale integra viene indicata come **Disponibile**:
   
    ![Verificare l'integrità delle risorse V M nell'portale di Azure per V M usando il modello di distribuzione classica.](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Reimpostare le credenziali utente**. Questa procedura consente di reimpostare la password dell'account amministratore locale specificata se non si è sicuri o si sono dimenticate le credenziali.  Dopo aver eseguito l'accesso alla VM, è necessario reimpostare la password per l'utente.
   
    Selezionare la macchina virtuale nel portale di Azure. Scorrere verso il basso nel riquadro delle impostazioni fino alla sezione **Supporto e risoluzione dei problemi**, nella parte inferiore dell'elenco. Fare clic sul pulsante **Reimposta password**. Immettere il nome utente e una nuova password. Infine fare clic sul pulsante **Salva**:
   
    ![Reimpostare le credenziali utente nel portale di Azure per V M usando il modello di distribuzione classica.](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Riavviare la VM**. Questa procedura di risoluzione dei problemi consente di correggere eventuali problemi sottostanti riscontrati dalla macchina virtuale.
   
    Selezionare la macchina virtuale nel portale di Azure e fare clic sulla scheda **Panoramica**. Fare clic sul pulsante **Riavvia**:
   
    ![Riavviare la versione M nella portale di Azure per la V M usando il modello di distribuzione classica.](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Assicurarsi che ogni firewall locale o firewall nel computer consenta il traffico TCP 3389 in uscita verso Azure.

Se continuano a verificarsi errori RDP, è possibile [aprire una richiesta di supporto](https://azure.microsoft.com/support/options/) o leggere i [passaggi e concetti relativi alla risoluzione dettagliata dei problemi RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="troubleshoot-specific-rdp-errors"></a>Risolvere errori specifici della connessione RDP
Quando si tenta di connettersi alla VM tramite RDP, potrebbe essere visualizzato un messaggio di errore specifico. Di seguito sono riportati i messaggi di errore più comuni:

* [La sessione remota è stata disconnessa perché non sono disponibili server licenze di Desktop remoto per il rilascio della licenza](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Desktop remoto: impossibile rilevare il "nome" del computer](troubleshoot-specific-rdp-errors.md#rdpname).
* [Si è verificato un errore di autenticazione. Impossibile contattare l'autorità di sicurezza locale](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Eventi di sicurezza di Windows: le credenziali non funzionavano](troubleshoot-specific-rdp-errors.md#wincred).
* [Il computer non è in grado di connettersi al computer remoto](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Risorse aggiuntive
Se non si è verificato alcuno di questi errori e non è ancora possibile connettersi alla VM tramite Desktop remoto, leggere [la guida dettagliata alla risoluzione dei problemi per Desktop remoto](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per la risoluzione dei problemi di accesso alle applicazioni in esecuzione in una VM, vedere [Troubleshoot access to an application running on an Azure VM](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json) (Risolvere i problemi di accesso a un'applicazione in esecuzione in una macchina virtuale di Azure).
* Se si verificano problemi relativi all'uso di SSH (Secure Shell) per la connessione a una VM Linux in Azure, vedere [Risoluzione dei problemi di connessione SSH a una macchina virtuale Linux di Azure](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
