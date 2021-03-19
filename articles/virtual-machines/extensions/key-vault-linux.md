---
title: Estensione macchina virtuale di Azure Key Vault per Linux
description: Distribuire un agente che esegue l'aggiornamento automatico dei certificati di Key Vault nelle macchine virtuali tramite un'estensione macchina virtuale.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 9032bfca30ead56c91d7904e18b76753cf3b6dfc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582171"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Estensione macchina virtuale di Key Vault per Linux

L'estensione macchina virtuale di Key Vault offre l'aggiornamento automatico dei certificati archiviati in un insieme di credenziali delle chiavi di Azure. In particolare, l'estensione monitora un elenco di certificati osservati archiviati in insiemi di credenziali delle chiavi.  Quando rileva una modifica, l'estensione recupera e installa i certificati corrispondenti. L'estensione macchina virtuale di Key Vault è pubblicata e supportata da Microsoft, attualmente in macchine virtuali Linux. Questo documento descrive dettagliatamente le piattaforme, le configurazioni e le opzioni di distribuzione supportate per l'estensione macchina virtuale di Key Vault per Linux. 

### <a name="operating-system"></a>Sistema operativo

L'estensione macchina virtuale di Key Vault supporta queste distribuzioni Linux:

- Ubuntu-1804
- Suse-15 

> [!NOTE]
> Per ottenere le funzionalità di sicurezza estese, prepararsi all'aggiornamento dei sistemi Ubuntu-1604 e Debian-9 perché queste versioni raggiungono la fine del periodo di supporto designato.
> 

### <a name="supported-certificate-content-types"></a>Tipi di contenuto del certificato supportati

- PKCS #12
- PEM


## <a name="prerequisities"></a>Prerequisiti
  - Key Vault istanza con certificato. Vedere [creare un Key Vault](../../key-vault/general/quick-create-portal.md)
  - VM/VMSS deve avere l' [identità gestita](../../active-directory/managed-identities-azure-resources/overview.md) assegnata
  - I criteri di accesso Key Vault devono essere impostati con i segreti `get` e l' `list` autorizzazione per l'identità gestita VM/vmss per recuperare la parte del certificato di un segreto. Vedere [come eseguire l'autenticazione a Key Vault](../../key-vault/general/authentication.md) e [assegnare un criterio di accesso key Vault](../../key-vault/general/assign-access-policy-cli.md).
  -  VMSS deve avere l'impostazione Identity seguente: ` 
  "identity": {
  "type": "UserAssigned",
  "userAssignedIdentities": {
  "[parameters('userAssignedIdentityResourceId')]": {}
  }
  }
  `
  
 - L'estensione AKV deve avere questa impostazione: `
                 "authenticationSettings": {
                    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
                    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
                  }
   `
## <a name="key-vault-vm-extension-version"></a>Versione dell'estensione della macchina virtuale Key Vault
* Gli utenti Ubuntu-18,04 e SUSE-15 possono scegliere di aggiornare la versione dell'estensione della VM Key Vault a `V2.0` per usufruire della funzionalità di download della catena di certificati completa. I certificati dell'autorità emittente (intermedio e radice) verranno aggiunti al certificato foglia nel file PEM.

* Se si preferisce eseguire l'aggiornamento a `v2.0` , è necessario eliminare `v1.0` prima di tutto, quindi installare `v2.0` .
```
  az vm extension delete --name KeyVaultForLinux --resource-group ${resourceGroup} --vm-name ${vmName}
  az vm extension set -n "KeyVaultForLinux" --publisher Microsoft.Azure.KeyVault --resource-group "${resourceGroup}" --vm-name "${vmName}" –settings .\akvvm.json –version 2.0
```  
  Il flag-versione 2,0 è facoltativo perché per impostazione predefinita verrà installata la versione più recente.   

* Se la macchina virtuale dispone di certificati scaricati dalla versione 1.0, l'eliminazione dell'estensione AKVVM v 1.0 non eliminerà i certificati scaricati.  Dopo l'installazione di v 2.0, i certificati esistenti non verranno modificati.  È necessario eliminare i file del certificato o eseguire il rollover del certificato per ottenere il file PEM con catena completa nella macchina virtuale.




## <a name="extension-schema"></a>Schema dell'estensione

Il codice JSON seguente mostra lo schema per l'estensione di macchina virtuale Key Vault. L'estensione non richiede impostazioni protette e tutte le impostazioni sono considerate informazioni che non hanno impatto sulla sicurezza. L'estensione richiede un elenco di segreti monitorati, la frequenza di polling e l'archivio dei certificati di destinazione. In particolare:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <It is ignored on Linux>,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> Gli URL dei certificati osservati devono essere nel formato `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Ciò è dovuto al fatto che il percorso `/secrets` restituisce il certificato completo, inclusa la chiave privata, mentre il percorso `/certificates` non lo restituisce. Altre informazioni sui certificati sono disponibili qui: [Certificati Key Vault](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> La proprietà' authenticationSettings ' è **obbligatoria** solo per le macchine virtuali con **identità assegnate dall'utente**.
> Specifica l'identità da usare per l'autenticazione Key Vault.


### <a name="property-values"></a>Valori delle proprietà

| Nome | Valore/Esempio | Tipo di dati |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | Data |
| publisher | Microsoft.Azure.KeyVault | string |
| type | KeyVaultForLinux | string |
| typeHandlerVersion | 2.0 | INT |
| pollingIntervalInS | 3600 | string |
| certificateStoreName | Viene ignorato in Linux | string |
| linkOnRenewal | false | boolean |
| certificateStoreLocation  | /var/lib/waagent/Microsoft.Azure.KeyVault | string |
| requireInitialSync | true | boolean |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"] | Matrice di stringhe
| msiEndpoint | http://169.254.169.254/metadata/identity | string |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | string |


## <a name="template-deployment"></a>Distribuzione del modello

Le estensioni macchina virtuale di Azure possono essere distribuite con i modelli di Azure Resource Manager. I modelli sono uno strumento ideale per distribuire una o più macchine virtuali per cui è necessario l'aggiornamento dei certificati successivamente alla distribuzione. L'estensione può essere distribuita in singole macchine virtuali o in set di scalabilità di macchine virtuali. Lo schema e la configurazione sono comuni a entrambi i tipi di modello. 

La configurazione JSON per un'estensione macchina virtuale deve essere annidata all'interno del frammento delle risorse della macchina virtuale del modello, in particolare nell'oggetto `"resources": []` per il modello di macchina virtuale e nell'oggetto `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` nel caso di un set di scalabilità di macchine virtuali.

 > [!NOTE]
> Per l'autenticazione nell'insieme di credenziali delle chiavi, l'estensione della macchina virtuale richiede l'assegnazione dell'identità gestita dal sistema o dall'utente.  Vedere [come eseguire l'autenticazione a Key Vault e assegnare un criterio di accesso key Vault.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>Ordinamento delle dipendenze dell'estensione
L'estensione della macchina virtuale Key Vault supporta l'ordinamento delle estensioni, se configurato. Per impostazione predefinita, l'estensione segnala che è stata avviata correttamente non appena è iniziata l'esecuzione del polling. Tuttavia, può essere configurato in modo da attendere che l'elenco completo dei certificati sia stato scaricato correttamente prima di segnalare un avvio riuscito. Se altre estensioni dipendono dall'installazione completa del set di certificati prima dell'avvio, l'abilitazione di questa impostazione consentirà a tale estensione di dichiarare una dipendenza dall'estensione Key Vault. In questo modo le estensioni non verranno avviate fino a quando non sono stati installati tutti i certificati da cui dipendono. L'estensione tenterà di nuovo il download iniziale a tempo indefinito e rimarrà in uno `Transitioning` stato.

Per attivare questa impostazione, impostare quanto segue:
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```
> Si noti L'uso di questa funzionalità non è compatibile con un modello ARM che crea un'identità assegnata dal sistema e aggiorna un criterio di accesso Key Vault con tale identità. In questo modo si otterrà un deadlock perché i criteri di accesso dell'insieme di credenziali non possono essere aggiornati finché non sono state avviate tutte le estensioni. È invece consigliabile usare un' *identità MSI assegnata a un singolo utente* e pre-ACL degli insiemi di credenziali con tale identità prima della distribuzione.

## <a name="azure-powershell-deployment"></a>Distribuzione con Azure PowerShell
> [!WARNING]
> I client PowerShell spesso aggiungono `\` a `"` nel settings.jssu cui causerà akvvm_service avrà esito negativo con errore: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

È possibile usare Azure PowerShell per distribuire l'estensione di macchina virtuale Key Vault in una macchina virtuale o un set di scalabilità di macchine virtuali esistente. 

* Per distribuire l'estensione in una macchina virtuale:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "2.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Per distribuire l'estensione in un set di scalabilità di macchine virtuali:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "2.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Distribuzione dell'interfaccia della riga di comando di Azure

Per distribuire l'estensione macchina virtuale di Key Vault in una macchina virtuale o un set di scalabilità di macchine virtuali esistente è possibile usare l'interfaccia della riga di comando di Azure. 
 
* Per distribuire l'estensione in una macchina virtuale:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --version 2.0 `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* Per distribuire l'estensione in un set di scalabilità di macchine virtuali:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vmss-name "<vmssName>" `
        --version 2.0 `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```
Tenere presenti le restrizioni e i requisiti seguenti:
- Restrizioni relative all'insieme di credenziali:
  - Deve essere già presente al momento della distribuzione 
  - I criteri di accesso Key Vault devono essere impostati per l'identità VM/VMSS usando un'identità gestita. Vedere [come eseguire l'autenticazione a Key Vault](../../key-vault/general/authentication.md) e [assegnare un criterio di accesso key Vault](../../key-vault/general/assign-access-policy-cli.md).

### <a name="frequently-asked-questions"></a>Domande frequenti

* È previsto un limite per il numero di observedCertificates che è possibile configurare?
  No, Key Vault estensione della macchina virtuale non ha limiti per il numero di observedCertificates.


### <a name="troubleshoot"></a>Risolvere problemi

I dati sullo stato delle distribuzioni dell'estensione possono essere recuperati nel portale di Azure e tramite Azure PowerShell. Per visualizzare lo stato di distribuzione delle estensioni per una macchina virtuale specifica, eseguire il comando seguente tramite Azure PowerShell.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Interfaccia della riga di comando di Azure**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```
#### <a name="logs-and-configuration"></a>Log e configurazione

```
/var/log/waagent.log
/var/log/azure/Microsoft.Azure.KeyVault.KeyVaultForLinux/*
/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-<most recent version>/config/*
```
### <a name="using-symlink"></a>Uso del collegamento simbolico

I collegamenti simbolici o i collegamenti simbolici sono fondamentalmente collegamenti avanzati. Per evitare di monitorare la cartella e ottenere automaticamente il certificato più recente, è possibile usare questo collegamento simbolico `([VaultName].[CertificateName])` per ottenere la versione più recente del certificato in Linux.

### <a name="frequently-asked-questions"></a>Domande frequenti

* È previsto un limite per il numero di observedCertificates che è possibile configurare?
  No, Key Vault estensione della macchina virtuale non ha limiti per il numero di observedCertificates.

### <a name="support"></a>Supporto

Per ricevere assistenza in relazione a qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare l'opzione desiderata per ottenere supporto. Per informazioni sull'uso del supporto di Azure, leggere le [Domande frequenti sul supporto di Azure](https://azure.microsoft.com/support/faq/).
