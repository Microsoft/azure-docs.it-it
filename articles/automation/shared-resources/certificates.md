---
title: Asset di tipo certificato in Automazione di Azure
description: I certificati sono in modo sicuro in Automazione di Azure in modo che siano accessibili tramite runbook o configurazioni DSC per l'autenticazione in risorse di Azure e di terze parti.  Questo articolo illustra nel dettaglio i certificati e spiega come usarli nella creazione testuale e grafica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a66f73e028594cf90f1fa1765910a3df3adbad1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849480"
---
# <a name="certificate-assets-in-azure-automation"></a>Asset di tipo certificato in Automazione di Azure

I certificati vengono archiviati in modo sicuro in Automazione di Azure in modo che possano essere accessibili da runbook o configurazioni DSC usando l'attività Get-AzureRmAutomationCertificate per le risorse di Azure Resource Manager.Certificates are stored securely in Azure Automation so can be accessed by runbooks or DSC configurations using the **Get-AzureRmAutomationCertificate** activity for Azure Resource Manager resources. Questa funzionalità consente di creare runbook e configurazioni DSC che usano certificati per l'autenticazione oppure consente di aggiungerli a risorse di Azure o di terze parti.

>[!NOTE]
>Gli asset sicuri in Automazione di Azure includono credenziali, certificati, connessioni e variabili crittografate. Questi asset vengono crittografati e archiviati in Automazione di Azure usando una chiave univoca generata per ogni account di automazione. Questa chiave è archiviata in un Key Vault gestito dal sistema. Prima di archiviare un asset sicuro, la chiave viene caricata da Key Vault e quindi usata per crittografare l'asset. Questo processo è gestito dall'Automazione di Azure.

## <a name="azurerm-powershell-cmdlets"></a>Cmdlet di PowerShell AzureRM

Per AzureRM, per creare e gestire asset di credenziali di automazione con Windows PowerShell, vengono usati i cmdlet della tabella seguente. Vengono spediti come parte del [modulo AzureRM.Automation](/powershell/azure/overview), disponibile per l'utilizzo nei runbook di automazione e nelle configurazioni DSC.

|Cmdlet|Descrizione|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Recupera le informazioni su un certificato da usare in un runbook o in una configurazione DSC. È possibile recuperare solo il certificato stesso dall'attività Get-AutomationCertificate.|
|[Nuovo-AzureRmAutomationCertificateNew-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Crea un nuovo certificato in Automazione di Azure.|
[Rimuovere-AzureRmAutomationCertificateRemove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Rimuove un certificato da Automazione di Azure.|
|[Set-AzureRmAutomationCertificateSet-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Imposta le proprietà per un certificato esistente, inclusi il caricamento del file del certificato e l'impostazione della password per un file con estensione pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Carica un certificato di servizio per il servizio cloud specificato.|

## <a name="activities"></a>attività

Le attività incluse nella tabella seguente vengono usate per accedere ai certificati in un runbook e nelle configurazioni DSC.

| attività | Descrizione |
|:---|:---|
|Get-AutomationCertificate|Ottiene un certificato da usare in un runbook o in una configurazione DSC. Restituisce un oggetto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> È consigliabile evitare di usare le variabili nel parametro –Name di **Get-AutomationCertificate** in un runbook o una configurazione DSC perché questo può rendere complessa l'individuazione delle dipendenze tra i runbook o le configurazioni DSC e le variabili di automazione in fase di progettazione.

## <a name="python2-functions"></a>Funzioni Python2

La funzione nella tabella seguente viene usata per accedere ai certificati in un runbook Python2.

| Funzione | Descrizione |
|:---|:---|
| automationassets.get_automation_certificate | Recupera informazioni su un asset certificato. |

> [!NOTE]
> È necessario importare il modulo **automationassets** all'inizio del runbook Python per poter accedere alle funzioni dell'asset.

## <a name="creating-a-new-certificate"></a>Creazione di un nuovo certificato

Quando si crea un nuovo certificato, si carica un file con estensione cer o pfx in Automazione di Azure. Se si contrassegna il certificato come esportabile, sarà possibile trasferirlo all'esterno dell'archivio di certificati di Automazione di Azure. Se non è esportabile, può essere utilizzato solo per la firma all'interno del runbook o della configurazione DSC. Automazione di Azure richiede che il certificato abbia un provider: **Microsoft Enhanced RSA and AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Per creare un nuovo certificato con il portale di Azure

1. Dall'account Automazione, fai clic sul riquadro **Risorse** per aprire la pagina **Risorse.**
2. Fare clic sul riquadro **Certificati** per aprire la pagina **Certificati.**
3. Fare clic su **Aggiungi un certificato** nella parte superiore della pagina.
4. Digitare un nome per il certificato nella casella **Nome**.
5. Per cercare un file con estensione cer o pfx, fare clic su **Selezionare un file** in **Caricare un file di certificato**. Se si seleziona un file con estensione pfx, specificare una password e se è possibile esportarlo.
6. Fare clic su **Crea** per salvare il nuovo asset di certificato.

### <a name="to-create-a-new-certificate-with-powershell"></a>Per creare un nuovo certificato con PowerShellTo create a new certificate with PowerShell

L'esempio che segue mostra come creare un nuovo certificato di automazione e come contrassegnarlo come esportabile. Verrà importato un file con estensione pfx esistente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Creare un nuovo certificato con il modello di Resource ManagerCreate a new certificate with Resource Manager template

L'esempio seguente illustra come distribuire un certificato all'account di automazione usando un modello di Resource Manager tramite PowerShell:The following example demonstrates how to deploy a certificate to your Automation Account using a Resource Manager template through PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Utilizzo di un certificato

Per usare un certificato, usare l'attività **Get-AutomationCertificate**. Non è possibile usare il cmdlet [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) perché restituisce informazioni sull'asset del certificato, ma non sul certificato stesso.

### <a name="textual-runbook-sample"></a>Esempio di Runbook testuale

Il codice di esempio seguente mostra come aggiungere un certificato a un servizio cloud in un Runbook. In questo esempio la password viene recuperata da una variabile di automazione crittografata.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Esempio di Runbook grafico

Per aggiungere un **Get-AutomationCertificate** a un runbook grafico, fare clic con il pulsante destro del mouse sul certificato nel riquadro Libreria e selezionare **Aggiungi a canvas**.

![Aggiungere il certificato all'area di disegno](../media/certificates/automation-certificate-add-to-canvas.png)

La figura seguente mostra un esempio dell'uso di un certificato in un runbook grafico. Si tratta dello stesso dell'esempio precedente che illustra come aggiungere un certificato a un servizio cloud da un runbook testuale.

![Esempio di creazione grafica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Esempio Python2

L'esempio seguente illustra come accedere ai certificati nei runbook Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sull'utilizzo di collegamenti per controllare il flusso logico delle attività per le quali è progettato il runbook, vedere [Collegamenti nella creazione grafica](../automation-graphical-authoring-intro.md#links-and-workflow). 
