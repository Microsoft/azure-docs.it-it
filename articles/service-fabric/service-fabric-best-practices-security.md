---
title: Procedure consigliate per la sicurezza di Azure Service Fabric
description: Procedure consigliate e considerazioni di progettazione per garantire la protezione dei cluster e delle applicazioni di Azure Service Fabric.Best practices and design considerations for keeping Azure Service Fabric clusters and applications secure.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: dcdc338bdcdb2c04f6b8894ccb358bc773b95c07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258928"
---
# <a name="azure-service-fabric-security"></a>Sicurezza di Azure Service Fabric 

Per altre informazioni sulle [procedure consigliate per la sicurezza di Azure](https://docs.microsoft.com/azure/security/), vedere [Procedure consigliate per la sicurezza di Azure Service Fabric](https://docs.microsoft.com/azure/security/fundamentals/service-fabric-best-practices)

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) e il servizio di gestione dei segreti consigliato per le applicazioni e i cluster Azure Service Fabric.
> [!NOTE]
> Se i certificati/segreti da un insieme di credenziali delle chiavi vengono distribuiti in un set di scalabilità di macchine virtuali come segreto del set di scalabilità di macchine virtuali, l'insieme di credenziali delle chiavi e il set di scalabilità di macchine virtuali devono avere un percorso condiviso.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Creare un certificato di Service Fabric rilasciato da un'autorità di certificazione

Un certificato di Azure Key Vault può essere creato o importato in un insieme di credenziali delle chiavi. Quando viene creato un certificato di Key Vault, la chiave privata viene creata all'interno dell'insieme di credenziali delle chiavi e mai esposta al proprietario del certificato. Questi sono i modi per creare un certificato in Key Vault:

- Creare un certificato autofirmato per creare una coppia di chiavi pubblica e privata e associarla a un certificato. Il certificato viene firmato dalla propria chiave. 
- Creare un nuovo certificato manualmente per creare una coppia di chiavi pubblica e privata e generare una richiesta di firma del certificato X.509. La richiesta di firma può essere firmata dall'autorità di registrazione o di certificazione. Per completare il certificato KV in Key Vault, il certificato X.509 firmato può essere unito con la coppia di chiavi in sospeso. Anche se questo metodo richiede più passaggi, offre maggiore sicurezza perché la chiave privata viene creata e limitata a Key Vault. Il concetto è illustrato nel diagramma seguente. 

Per altri dettagli, vedere [Metodi di creazione dei certificati](https://docs.microsoft.com/azure/key-vault/create-certificate).

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Distribuire i certificati di Key Vault nei set di scalabilità di macchine virtuali del cluster di Service Fabric

Per distribuire i certificati da un insieme di credenziali con percorso condiviso a un set di scalabilità di macchine virtuali, usare [osProfile](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) del set di scalabilità di macchine virtuali. Di seguito sono elencate le proprietà del modello di Resource Manager:

```json
"secrets": [
   {
       "sourceVault": {
           "id": "[parameters('sourceVaultValue')]"
       },
       "vaultCertificates": [
          {
              "certificateStore": "[parameters('certificateStoreValue')]",
              "certificateUrl": "[parameters('certificateUrlValue')]"
          }
       ]
   }
]
```

> [!NOTE]
> L'insieme di credenziali deve essere abilitato per la distribuzione dei modelli di Resource Manager.

## <a name="apply-an-access-control-list-acl-to-your-certificate-for-your-service-fabric-cluster"></a>Applicare un elenco di controllo di accesso (ACL) al certificato per il cluster di Service Fabric

Per configurare la sicurezza dei nodi viene usato il modello [Estensioni set di scalabilità di macchine virtuali](https://docs.microsoft.com/cli/azure/vmss/extension?view=azure-cli-latest) con editore Microsoft.Azure.ServiceFabric.
Per applicare un ACL ai certificati per i processi del cluster di Service Fabric, usare le proprietà del modello di Resource Manager seguenti:

```json
"certificate": {
   "commonNames": [
       "[parameters('certificateCommonName')]"
   ],
   "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

## <a name="secure-a-service-fabric-cluster-certificate-by-common-name"></a>Proteggere un certificato del cluster di Service Fabric in base al nome comune

Per proteggere il cluster di Service Fabric in base al valore `Common Name` del certificato, usare la proprietà del modello di Resource Manager [certificateCommonNames](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames), come indicato di seguito:

```json
"certificateCommonNames": {
    "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
        }
    ],
    "x509StoreName": "[parameters('certificateStoreValue')]"
}
```

> [!NOTE]
> I cluster di Service Fabric useranno il primo certificato valido trovato nell'archivio certificati dell'host. In Windows, questo sarà il certificato con la data di scadenza più recente corrispondente al nome comune e all'identificazione digitale dell'autorità emittente.

I domini di Azure, ad esempio *\<SOTTODOMINIO\>.cloudapp.azure.com o \<SOTTODOMINIO\>.trafficmanager.net, sono di proprietà di Microsoft. Le autorità di certificazione non rilasceranno certificati per i domini a utenti non autorizzati. La maggior parte degli utenti dovrà acquistare un dominio da un registrar, o essere un amministratore di dominio autorizzato, per ottenere il rilascio di un certificato con tale nome comune da un'autorità di certificazione.

Per altri dettagli su come configurare il servizio DNS per risolvere il dominio in un indirizzo IP di Microsoft, vedere come configurare [DNS di Azure per ospitare il dominio](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns).

> [!NOTE]
> Dopo aver delegato i server dei nomi di domini ai server dei nomi di zona di DNS di Azure, aggiungere i due record seguenti alla zona DNS:
> - Un record 'A' per un dominio APEX che non è un `Alias record set` per tutti gli indirizzi IP che verranno risolti dal dominio personalizzato.
> - Un record "C" per i sottodomini Microsoft di cui è stato effettuato il provisioning che NON sono un `Alias record set`. Ad esempio, è possibile usare il nome DNS di Gestione traffico o Load Balancer.

Per aggiornare il portale in modo da visualizzare un nome DNS personalizzato per il cluster di Service Fabric `"managementEndpoint"`, aggiornare le proprietà del modello di Resource Manager del cluster di Service Fabric seguenti:

```json
 "managementEndpoint": "[concat('https://<YOUR CUSTOM DOMAIN>:',parameters('nt0fabricHttpGatewayPort'))]",
```

## <a name="encrypting-service-fabric-package-secret-values"></a>Crittografia dei valori dei segreti dei pacchetti di Service Fabric

I valori comuni crittografati nei pacchetti di Service Fabric includono le credenziali di Registro Azure Container, le variabili di ambiente, le impostazioni e chiavi dell'account di archiviazione del plug-in Azure Volume.

Per [configurare un certificato di crittografia e crittografare segreti in cluster Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-windows):

Generare un certificato autofirmato per crittografare il segreto:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Usare le istruzioni in [Distribuire i certificati di Key Vault nei set di scalabilità di macchine virtuali del cluster di Service Fabric](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) per distribuire i certificati di Key Vault nei set di scalabilità di macchine virtuali del cluster di Service Fabric.

Crittografare il segreto con il comando PowerShell seguente e quindi aggiornare il manifesto dell'applicazione di Service Fabric con il valore crittografato:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Per [configurare un certificato di crittografia e crittografare segreti in cluster Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management-linux):

Generare un certificato autofirmato per crittografare i segreti:

```bash
user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
user@linux:~$ cat TestCert.prv >> TestCert.pem
```

Usare le istruzioni in [Distribuire i certificati di Key Vault nei set di scalabilità di macchine virtuali del cluster di Service Fabric](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) per distribuire i certificati di Key Vault nei set di scalabilità di macchine virtuali del cluster di Service Fabric.

Crittografare il segreto usando i comandi seguenti e quindi aggiornare il manifesto dell'applicazione di Service Fabric con il valore crittografato:

```bash
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt -o plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```

Dopo aver crittografato i valori protetti [specificare i segreti crittografati nell'applicazione di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#specify-encrypted-secrets-in-an-application) e [decrittografare i segreti crittografati dal codice del servizio](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Includi certificato nelle applicazioni Service Fabric

Per concedere all'applicazione l'accesso ai segreti, includere il certificato aggiungendo un elemento **SecretsCertificate** al manifesto dell'applicazione.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Autenticare le applicazioni di Service Fabric per le risorse di Azure usando l'identità del servizio gestita

Per informazioni sulle identità gestite per le risorse di Azure, vedere [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).
I cluster di Azure Service Fabric sono ospitati in set di scalabilità di macchine virtuali, che supportano l'[identità del servizio gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-managed-identities-for-azure-resources).
Per ottenere un elenco dei servizi per cui è possibile usare tale identità del servizio gestita per l'autenticazione, vedere [Servizi di Azure che supportano l'autenticazione di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-msi#azure-services-that-support-azure-ad-authentication).


Per abilitare l'identità gestita assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali o per un set di scalabilità di macchine virtuali esistente, dichiarare la proprietà `"Microsoft.Compute/virtualMachinesScaleSets"` seguente:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss#system-assigned-managed-identity).

Se è stata creata un'[identità gestita assegnata dall'utente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm#create-a-user-assigned-managed-identity), dichiarare la risorsa seguente nel modello per assegnarla al set di scalabilità di macchine virtuali. Sostituire `\<USERASSIGNEDIDENTITYNAME\>` con il nome dell'identità gestita assegnata dall'utente che è stata creata:

```json
"identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
        "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
    }
}
```

Prima che l'applicazione di Service Fabric possa usare un'identità gestita, è necessario concedere autorizzazioni per le risorse di Azure necessarie per l'autenticazione.
I comandi seguenti consentono di concedere l'accesso a una risorsa di Azure:

```bash
principalid=$(az resource show --id /subscriptions/<YOUR SUBSCRIPTON>/resourceGroups/<YOUR RG>/providers/Microsoft.Compute/virtualMachineScaleSets/<YOUR SCALE SET> --api-version 2018-06-01 | python -c "import sys, json; print(json.load(sys.stdin)['identity']['principalId'])")

az role assignment create --assignee $principalid --role 'Contributor' --scope "/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/<PROVIDER NAME>/<RESOURCE TYPE>/<RESOURCE NAME>"
```

Nel codice dell'applicazione Service Fabric ottenere un token di accesso per Azure Resource Manager rendendo un'applicazione REST simile al seguente:In your Service Fabric application code, [obtain an access token](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token#get-a-token-using-http) for Azure Resource Manager by making a REST all similar to the following:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

L'applicazione di Service Fabric può quindi usare il token di accesso per l'autenticazione per le risorse di Azure che supportano Active Directory.
L'esempio seguente mostra come eseguire questa operazione per la risorsa Cosmos DB:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Elementi di base della sicurezza di Windows
Si consiglia di implementare una configurazione standard del settore ampiamente [nota e ben testata, ad esempio le linee di base di sicurezza Microsoft, anziché creare una linea di base.](https://docs.microsoft.com/windows/security/threat-protection/windows-security-baselines) un'opzione per il provisioning di questi set di scalabilità in Virtual Machine Scale Sets consiste nell'usare il gestore dell'estensione DSC (Desired State Configuration) di Azure per configurare le macchine virtuali man mano che vengono online, in modo che eseguano il software di produzione.

## <a name="azure-firewall"></a>Firewall di Azure
Firewall di Azure è un servizio di sicurezza di rete gestito basato su cloud che protegge le risorse della rete virtuale di [Azure.Azure Firewall is a managed, cloud-based network security service that protects your Azure Virtual Network resources. Si tratta di un firewall completamente con stato come servizio con disponibilità elevata integrata e scalabilità cloud senza restrizioni.](https://docs.microsoft.com/azure/firewall/overview) ciò consente di limitare il traffico HTTP/S in uscita a un elenco specificato di nomi di dominio completi (FQDN), inclusi i caratteri jolly. Questa funzionalità non richiede la terminazione SSL. È consigliabile sfruttare [i tag FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags) di Azure Firewall per gli aggiornamenti di Windows e abilitare il traffico di rete verso gli endpoint di Microsoft Windows Update può passare attraverso il firewall. [Deploy Azure Firewall usando un modello](https://docs.microsoft.com/azure/firewall/deploy-template) offre un esempio per la definizione del modello di risorsa Microsoft.Network/azureFirewalls.Deploy Azure Firewall using a template provides a sample for Microsoft.Network/azureFirewalls resource template definition. Le regole del firewall comuni alle applicazioni Service Fabric consentono quanto segue per la rete virtuale dei cluster:Firewall rules common to Service Fabric Applications is to allow the following for your clusters virtual network:

- Download.microsoft.com z
- Servicefabric.azure.com
- *.core.windows.net

Queste regole del firewall completano i gruppi di sicurezza di rete in uscita consentiti, che includono ServiceFabric e Archiviazione, come destinazioni consentite dalla rete virtuale.

## <a name="tls-12"></a>TLS 1.2
[Stg](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

## <a name="windows-defender"></a>Windows Defender 

Per impostazione predefinita, Windows Defender Antivirus è installato in Windows Server 2016. Per informazioni dettagliate, vedere [Windows Defender Antivirus in Windows Server 2016](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). L'interfaccia utente viene installata per impostazione predefinita in alcuni SKU, ma non è obbligatoria. Per ridurre eventuali effetti sulle prestazioni e il sovraccarico di utilizzo delle risorse dovuti a Windows Defender e se i criteri di sicurezza consentono di escludere processi e i percorsi per il software open source, dichiarare le proprietà seguenti del modello di Resource Manager Estensioni set di scalabilità di macchine virtuali per escludere il cluster di Service Fabric dalle analisi:


```json
 {
    "name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
    "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
            "AntimalwareEnabled": "true",
            "Exclusions": {
                "Paths": "[concat(parameters('svcFabData'), ';', parameters('svcFabLogs'), ';', parameters('svcFabRuntime'))]",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
            },
            "RealtimeProtectionEnabled": "true",
            "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
            }
        },
        "protectedSettings": null
    }
}
```

> [!NOTE]
> Se non si usa Windows Defender, fare riferimento alla documentazione dell'antimalware in uso per informazioni sulle regole di configurazione. Windows Defender non è supportato in Linux.

## <a name="platform-isolation"></a>Isolamento della piattaforma
Per impostazione predefinita, alle applicazioni Service Fabric viene concesso l'accesso al runtime di Service Fabric stesso, che si manifesta in forme diverse: variabili di [ambiente](service-fabric-environment-variables-reference.md) che puntano a percorsi di file nell'host corrispondenti ai file dell'applicazione e di infrastruttura, un endpoint di comunicazione tra processi che accetta richieste specifiche dell'applicazione e il certificato client che Fabric prevede che l'applicazione utilizzi per autenticarsi. Nell'eventualità che il servizio ospita codice non attendibile, è consigliabile disabilitare questo accesso al runtime SF, a meno che non sia esplicitamente necessario. L'accesso al runtime viene rimosso utilizzando la dichiarazione seguente nella sezione Criteri del manifesto dell'applicazione: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster in macchine virtuali o in computer che eseguono Windows Server: [Creazione cluster di Service Fabric per Windows Server.](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster in macchine virtuali o in computer che eseguono Linux: [Creare un cluster Linux.](service-fabric-cluster-creation-via-portal.md)
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
