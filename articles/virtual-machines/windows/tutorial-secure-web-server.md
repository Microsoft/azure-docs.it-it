---
title: 'Esercitazione: Proteggere un server Web Windows con i certificati TLS/SSL in Azure'
description: In questa esercitazione viene illustrato come usare Azure PowerShell per proteggere una macchina virtuale Windows che esegue il server Web IIS con certificati TLS/SSL archiviati in Azure Key Vault.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 3b65d04b383fdc0a409e23ab6b6649604be502c6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525588"
---
# <a name="tutorial-secure-a-web-server-on-a-windows-virtual-machine-in-azure-with-tlsssl-certificates-stored-in-key-vault"></a>Esercitazione: Proteggere un server Web in una macchina virtuale Windows in Azure con i certificati TLS/SSL archiviati in Key Vault

> [!NOTE]
> Attualmente questa documentazione si applica solo alle immagini generalizzate. Se si prova a seguire questa esercitazione usando un disco specializzato, si riceverà un errore. 

Per proteggere i server Web, è possibile usare un certificato Transport Layer Security (TLS), precedentemente noto come Secure Sockets Layer (SSL), per crittografare il traffico Web. Questi certificati TLS/SSL possono essere archiviati in Azure Key Vault e consentono distribuzioni sicure dei certificati nelle macchine virtuali Windows in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un Azure Key Vault
> * Generare o caricare un certificato in Key Vault
> * Creare una macchina virtuale e installare il server Web IIS
> * Inserire il certificato nella macchina virtuale e configurare IIS con un'associazione TLS


## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.


## <a name="overview"></a>Panoramica
Azure Key Vault consente di proteggere chiavi di crittografia, chiavi private, certificati e password. Key Vault semplifica il processo di gestione dei certificati e consente di mantenere il controllo delle chiavi che accedono a tali certificati. È possibile creare un certificato autofirmato in Key Vault o caricarne uno esistente, un certificato attendibile di cui si è già proprietari.

Invece di usare un'immagine di macchina virtuale personalizzata che include certificati incorporati, si inseriscono i certificati in una macchina virtuale in esecuzione. Questo processo assicura che, durante la distribuzione, in un server Web vengano installati i certificati più aggiornati. Se si rinnova o sostituisce un certificato, non è necessario creare anche una nuova immagine di macchina virtuale personalizzata. I certificati più recenti vengono automaticamente inseriti quando si creano macchine virtuali aggiuntive. Durante l'intero processo, i certificati non lasciano mai la piattaforma Azure e non vengono mai esposti in uno script, in una cronologia della riga di comando o in un modello.


## <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault
Per poter creare un'istanza di Key Vault e i certificati, creare prima di tutto un gruppo di risorse con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroupSecureWeb* nella località *Stati Uniti orientali*:

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Creare quindi un insieme di credenziali delle chiavi con [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Ogni Key Vault deve avere un nome univoco in lettere minuscole. Nell'esempio seguente, sostituire `mykeyvault` con il nome univoco del proprio Key Vault:

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Generare un certificato e archiviarlo in Key Vault
Per la produzione è necessario importare un certificato valido firmato da un provider attendibile con il comando [Import-AzKeyVaultCertificate](/powershell/module/az.keyvault/import-azkeyvaultcertificate). Per questa esercitazione, l'esempio seguente illustra come generare un certificato autofirmato con il comando [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/add-azkeyvaultcertificate) che usi i criteri dei certificati predefiniti da [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/new-azkeyvaultcertificatepolicy). 

```azurepowershell-interactive
$policy = New-AzKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale
Impostare nome utente e password dell'amministratore della macchina virtuale con il comando [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1):

```azurepowershell-interactive
$cred = Get-Credential
```

A questo punto è possibile creare la VM con [New-AzVM](/powershell/module/az.compute/new-azvm). L'esempio seguente crea una macchina virtuale denominata *myVM* nell'area *EastUS*. Se non esistono già, vengono create le rispettive risorse di rete di supporto. Per consentire il traffico Web sicuro, il cmdlet apre anche la porta *443*.

```azurepowershell-interactive
# Create a VM
New-AzVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Per creare la macchina virtuale sono necessari alcuni minuti. L'ultimo passaggio usa l'estensione per script personalizzati di Azure per installare il server Web IIS con [Set-AzVmExtension](/powershell/module/az.compute/set-azvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Aggiungere un certificato alla macchina virtuale da Key Vault
Per aggiungere un certificato da Key Vault a una macchina virtuale, ottenere l'ID del certificato con [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret). Aggiungere il certificato alla VM con [Add-AzVMSecret](/powershell/module/az.compute/add-azvmsecret):

```azurepowershell-interactive
$certURL=(Get-AzKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Configurare IIS per usare il certificato
Usare di nuovo l'estensione per script personalizzati con [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) per aggiornare la configurazione di IIS. Questo aggiornamento applica il certificato inserito da Key Vault a IIS e configura l'associazione Web:

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Testare l'applicazione Web protetta
Ottenere l'indirizzo IP pubblico della macchina virtuale con [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). L'esempio seguente ottiene l'indirizzo IP `myPublicIP` creato in precedenza:

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

È ora possibile aprire un Web browser e immettere `https://<myPublicIP>` bella barra degli indirizzi. Per accettare l'avviso di sicurezza se si è usato un certificato autofirmato, selezionare **Dettagli** e quindi **Continua per la pagina Web**:

![Accettare l'avviso di sicurezza del Web browser](./media/tutorial-secure-web-server/browser-warning.png)

Il sito Web IIS protetto viene quindi visualizzato come illustrato nell'esempio seguente:

![Visualizzare il sito protetto IIS in esecuzione](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è protetto un server Web IIS con un certificato TLS/SSL archiviato in Azure Key Vault. Si è appreso come:

> [!div class="checklist"]
> * Creare un Azure Key Vault
> * Generare o caricare un certificato in Key Vault
> * Creare una macchina virtuale e installare il server Web IIS
> * Inserire il certificato nella macchina virtuale e configurare IIS con un'associazione TLS

Seguire questo collegamento per vedere esempi di script predefiniti delle macchine virtuali.

> [!div class="nextstepaction"]
> [Esempi di script delle macchine virtuali Windows](./powershell-samples.md)
