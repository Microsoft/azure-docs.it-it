---
title: Aggiornare un cluster per l'uso del nome comune del certificato
description: Informazioni su come passare dall'uso di identificazioni personali del certificato all'uso di nomi comuni del certificato in un cluster di Service Fabric.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: a90290430616302dbbe9ab9cf717510070936529
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86247915"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>Passare dall'uso di identificazioni personali del certificato all'uso di nomi comuni del certificato in un cluster
Nessun certificato può avere la stessa identificazione digitale di un altro, il che rende difficile eseguire il rollover o gestire il certificato del cluster. Più certificati, tuttavia, possono avere lo stesso nome comune o lo stesso oggetto.  Commutare un cluster distribuito dall'uso di identificazioni personali del certificato all'uso di nomi comuni del certificato rende molto più semplice la gestione dei certificati. In questo articolo viene descritto come aggiornare un cluster di Service Fabric in esecuzione per usare il nome comune del certificato anziché l'identificazione personale del certificato.

>[!NOTE]
> Se nel modello sono dichiarate due identificazioni personali, è necessario eseguire due distribuzioni.  La prima distribuzione viene eseguita prima di seguire i passaggi descritti in questo articolo.  La prima distribuzione imposta la proprietà **thumbprint** nel modello sul certificato in uso e rimuove la proprietà **thumbprintSecondary**.  Per la seconda distribuzione, seguire i passaggi descritti in questo articolo.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Ottenere un certificato
Ottenere innanzitutto un certificato da un'[autorità di certificazione](https://wikipedia.org/wiki/Certificate_authority).  Il nome comune del certificato deve essere usato per il dominio personalizzato di cui si è proprietari e deve essere acquistato da un registrar di dominio. Ad esempio "azureservicefabricbestpractices.com"; gli utenti che non sono dipendenti Microsoft non possono effettuare il provisioning di certificati per i domini MS, pertanto non è possibile usare i nomi DNS del bilanciamento del carico o di Gestione traffico come nomi comuni per il certificato e quindi sarà necessario eseguire il provisioning di una [zona DNS di Azure](../dns/dns-delegate-domain-azure-dns.md) affinché il dominio personalizzato sia risolvibile in Azure. È anche possibile dichiarare il dominio personalizzato di cui si è proprietari come "managementEndpoint" del cluster se si desidera che il portale rifletta l'alias di dominio personalizzato per il cluster.

A scopo di test, è possibile ottenere un certificato firmato da un'autorità di certificazione disponibile o aperta.

> [!NOTE]
> I certificati autofirmati, inclusi quelli generati quando si distribuisce un cluster di Service Fabric nel portale di Azure, non sono supportati. 

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Caricare il certificato e installarlo nel set di scalabilità
In Azure, un cluster di Service Fabric è distribuito in un set di scalabilità di macchine virtuali.  Aggiornare il certificato a un insieme di credenziali delle chiavi, quindi installarlo nel set di scalabilità di macchine virtuali in cui è in esecuzione il cluster.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> I segreti del set di scalabilità non supportano lo stesso ID risorsa per due segreti separati, perché ogni segreto è una risorsa univoca con controllo delle versioni. 

## <a name="download-and-update-the-template-from-the-portal"></a>Scaricare e aggiornare il modello dal portale
Il certificato è stato installato sul set di scalabilità sottostante, ma è necessario aggiornare anche il cluster di Service Fabric perché usi tale certificato e il relativo nome comune.  A questo punto, scaricare il modello per la distribuzione del cluster.  Accedere al [portale di Azure](https://portal.azure.com) e passare al gruppo di risorse che ospita il cluster.  In **Impostazioni**, selezionare **Distribuzioni**.  Selezionare la distribuzione più recente e fare clic su **Visualizza modello**.

![Visualizzare un modello][image1]

Scaricare i file JSON di modello e i parametri sul computer locale.

Prima di tutto, aprire il file dei parametri in un editor di testo e aggiungere il seguente valore di parametro:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

Aprire quindi il file di modello in un editor di testo e apportare tre aggiornamenti per supportare il nome comune del certificato.

1. Nella sezione **parametri**, aggiungere un parametro *certificateCommonName*:
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    Prendere in considerazione anche la rimozione del *CertificateThumbprint*, a cui potrebbe non essere più fatto riferimento nel modello di gestione risorse.

2. Nella risorsa **Microsoft.Compute/virtualMachineScaleSets**, aggiornare l'estensione macchina virtuale perché nelle impostazioni del certificato venga usato il nome comune anziché l'identificazione personale.  In **virtualMachineProfile** -> **extensionProfile** -> **Extensions** -> **Properties** -> **Settings** -> **Certificate**, Add `"commonNames": ["[parameters('certificateCommonName')]"],` e Remove `"thumbprint": "[parameters('certificateThumbprint')]",` .
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  Nella risorsa **Microsoft.ServiceFabric/clusters**, aggiornare la versione dell'API in "2018-02-01".  Aggiungere anche un'impostazione **certificateCommonNames** con una proprietà **commonNames** e rimuovere l'impostazione **certificato** con la proprietà identificazione personale come illustrato nell'esempio seguente:
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": ""
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
    ```

Per ulteriori informazioni, vedere la pagina relativa alla [distribuzione di un cluster Service fabric che utilizza il nome comune del certificato anziché l'identificazione personale.](./service-fabric-create-cluster-using-cert-cn.md)

## <a name="deploy-the-updated-template"></a>Distribuire il modello aggiornato
Distribuire nuovamente il modello aggiornato dopo aver apportato le modifiche.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulla [sicurezza del cluster](service-fabric-cluster-security.md).
* Informazioni su come [eseguire il rollover di un certificato di cluster](service-fabric-cluster-rollover-cert-cn.md)
* [Aggiornare e gestire i certificati dei cluster](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
