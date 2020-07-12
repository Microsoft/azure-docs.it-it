---
title: Procedure consigliate per la sicurezza di Azure Service Fabric
description: Procedure consigliate e considerazioni di progettazione per mantenere protetti i cluster e le applicazioni di Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 90ffd1c01411982f56aed3332c499aa0c10b8a94
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86257595"
---
# <a name="azure-service-fabric-security"></a>Sicurezza di Azure Service Fabric 

Per altre informazioni sulle [procedure consigliate per la sicurezza di Azure](../security/index.yml), vedere [Procedure consigliate per la sicurezza di Azure Service Fabric](../security/fundamentals/service-fabric-best-practices.md)

## <a name="key-vault"></a>Key Vault

[Azure Key Vault](../key-vault/index.yml) e il servizio di gestione dei segreti consigliato per le applicazioni e i cluster Azure Service Fabric.
> [!NOTE]
> Se i certificati/segreti da un insieme di credenziali delle chiavi vengono distribuiti in un set di scalabilità di macchine virtuali come segreto del set di scalabilità di macchine virtuali, l'insieme di credenziali delle chiavi e il set di scalabilità di macchine virtuali devono avere un percorso condiviso.

## <a name="create-certificate-authority-issued-service-fabric-certificate"></a>Creare un certificato di Service Fabric rilasciato da un'autorità di certificazione

Un certificato di Azure Key Vault può essere creato o importato in un insieme di credenziali delle chiavi. Quando viene creato un certificato di Key Vault, la chiave privata viene creata all'interno dell'insieme di credenziali delle chiavi e mai esposta al proprietario del certificato. Questi sono i modi per creare un certificato in Key Vault:

- Creare un certificato autofirmato per creare una coppia di chiavi pubblica e privata e associarla a un certificato. Il certificato viene firmato dalla propria chiave. 
- Creare un nuovo certificato manualmente per creare una coppia di chiavi pubblica e privata e generare una richiesta di firma del certificato X.509. La richiesta di firma può essere firmata dall'autorità di registrazione o di certificazione. Per completare il certificato KV in Key Vault, il certificato X.509 firmato può essere unito con la coppia di chiavi in sospeso. Anche se questo metodo richiede più passaggi, offre maggiore sicurezza perché la chiave privata viene creata e limitata a Key Vault. Il concetto è illustrato nel diagramma seguente. 

Per altri dettagli, vedere [Metodi di creazione dei certificati](../key-vault/certificates/create-certificate.md).

## <a name="deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets"></a>Distribuire i certificati di Key Vault nei set di scalabilità di macchine virtuali del cluster di Service Fabric

Per distribuire i certificati da un insieme di credenziali con percorso condiviso a un set di scalabilità di macchine virtuali, usare [osProfile](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosprofile) del set di scalabilità di macchine virtuali. Di seguito sono elencate le proprietà del modello di Resource Manager:

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

Per configurare la sicurezza dei nodi viene usato il modello [Estensioni set di scalabilità di macchine virtuali](/cli/azure/vmss/extension?view=azure-cli-latest) con editore Microsoft.Azure.ServiceFabric.
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

Per proteggere il cluster di Service Fabric in base al valore `Common Name` del certificato, usare la proprietà del modello di Resource Manager [certificateCommonNames](/rest/api/servicefabric/sfrp-model-clusterproperties#certificatecommonnames), come indicato di seguito:

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

I domini di Azure, ad esempio * \<YOUR SUBDOMAIN\> . cloudapp.Azure.com o \<YOUR SUBDOMAIN\> . trafficmanager.NET, sono di proprietà di Microsoft. Le autorità di certificazione non rilasceranno certificati per i domini a utenti non autorizzati. La maggior parte degli utenti dovrà acquistare un dominio da un registrar, o essere un amministratore di dominio autorizzato, per ottenere il rilascio di un certificato con tale nome comune da un'autorità di certificazione.

Per altri dettagli su come configurare il servizio DNS per risolvere il dominio in un indirizzo IP di Microsoft, vedere come configurare [DNS di Azure per ospitare il dominio](../dns/dns-delegate-domain-azure-dns.md).

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

Per [configurare un certificato di crittografia e crittografare segreti in cluster Windows](./service-fabric-application-secret-management-windows.md):

Generare un certificato autofirmato per crittografare il segreto:

```powershell
New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
```

Usare le istruzioni in [Distribuire i certificati di Key Vault nei set di scalabilità di macchine virtuali del cluster di Service Fabric](#deploy-key-vault-certificates-to-service-fabric-cluster-virtual-machine-scale-sets) per distribuire i certificati di Key Vault nei set di scalabilità di macchine virtuali del cluster di Service Fabric.

Crittografare il segreto con il comando PowerShell seguente e quindi aggiornare il manifesto dell'applicazione di Service Fabric con il valore crittografato:

``` powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Per [configurare un certificato di crittografia e crittografare segreti in cluster Linux](./service-fabric-application-secret-management-linux.md):

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

Dopo aver crittografato i valori protetti [specificare i segreti crittografati nell'applicazione di Service Fabric](./service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application) e [decrittografare i segreti crittografati dal codice del servizio](./service-fabric-application-secret-management.md#decrypt-encrypted-secrets-from-service-code).

## <a name="include-certificate-in-service-fabric-applications"></a>Includi certificato nelle applicazioni Service Fabric

Per consentire all'applicazione di accedere ai segreti, includere il certificato aggiungendo un elemento **SecretsCertificate** al manifesto dell'applicazione.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
## <a name="authenticate-service-fabric-applications-to-azure-resources-using-managed-service-identity-msi"></a>Autenticare le applicazioni di Service Fabric per le risorse di Azure usando l'identità del servizio gestita

Per informazioni sulle identità gestite per le risorse di Azure, vedere [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).
I cluster di Azure Service Fabric sono ospitati in set di scalabilità di macchine virtuali, che supportano l'[identità del servizio gestita](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
Per ottenere un elenco dei servizi per cui è possibile usare tale identità del servizio gestita per l'autenticazione, vedere [Servizi di Azure che supportano l'autenticazione di Azure Active Directory](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).


Per abilitare l'identità gestita assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali o per un set di scalabilità di macchine virtuali esistente, dichiarare la proprietà `"Microsoft.Compute/virtualMachinesScaleSets"` seguente:

```json
"identity": { 
    "type": "SystemAssigned"
}
```
Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vmss.md#system-assigned-managed-identity).

Se è stata creata un'[identità gestita assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity), dichiarare la risorsa seguente nel modello per assegnarla al set di scalabilità di macchine virtuali. Sostituire `\<USERASSIGNEDIDENTITYNAME\>` con il nome dell'identità gestita assegnata dall'utente che è stata creata:

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

Nel codice dell'applicazione Service Fabric [ottenere un token di accesso](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#get-a-token-using-http) per Azure Resource Manager rendendo un oggetto Rest simile al seguente:

```bash
access_token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true | python -c "import sys, json; print json.load(sys.stdin)['access_token']")

```

L'applicazione di Service Fabric può quindi usare il token di accesso per l'autenticazione per le risorse di Azure che supportano Active Directory.
L'esempio seguente mostra come eseguire questa operazione per la risorsa Cosmos DB:

```bash
cosmos_db_password=$(curl 'https://management.azure.com/subscriptions/<YOUR SUBSCRIPTION>/resourceGroups/<YOUR RG>/providers/Microsoft.DocumentDB/databaseAccounts/<YOUR ACCOUNT>/listKeys?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer $access_token" | python -c "import sys, json; print(json.load(sys.stdin)['primaryMasterKey'])")
```
## <a name="windows-security-baselines"></a>Elementi di base della sicurezza di Windows
Si [consiglia di implementare una configurazione standard del settore che sia ampiamente nota e ben collaudata, ad esempio le linee di base della sicurezza Microsoft, anziché creare manualmente una baseline](/windows/security/threat-protection/windows-security-baselines). un'opzione per il provisioning di questi nei set di scalabilità di macchine virtuali consiste nell'usare il gestore estensioni DSC (Desired state Configuration) di Azure per configurare le macchine virtuali in linea, in modo che eseguano il software di produzione.

## <a name="azure-firewall"></a>Firewall di Azure
Il [firewall di Azure è un servizio di sicurezza di rete gestito e basato sul cloud che protegge le risorse della rete virtuale di Azure. Si tratta di un firewall completamente con stato come servizio con disponibilità elevata incorporata e scalabilità illimitata del cloud.](../firewall/overview.md) in questo modo è possibile limitare il traffico HTTP/S in uscita a un elenco specificato di nomi di dominio completi (FQDN), inclusi i caratteri jolly. Questa funzionalità non richiede la terminazione TLS/SSL. Si consiglia di usare i [tag FQDN del firewall di Azure](../firewall/fqdn-tags.md) per gli aggiornamenti di Windows e di abilitare il traffico di rete verso gli endpoint di Microsoft Windows Update possono attraversare il firewall. [Distribuire il firewall di Azure con un modello](../firewall/deploy-template.md) fornisce un esempio per la definizione del modello di risorsa Microsoft. Network/azureFirewalls. Le regole del firewall comuni alle applicazioni Service Fabric consentono di eseguire le operazioni seguenti per la rete virtuale dei cluster:

- * download.microsoft.com
- * servicefabric.azure.com
- *.core.windows.net

Queste regole del firewall sono complementari ai gruppi di sicurezza di rete in uscita consentiti, che includono ServiceFabric e archiviazione, come destinazioni consentite dalla rete virtuale.

## <a name="tls-12"></a>TLS 1.2

Microsoft [Azure consiglia](https://azure.microsoft.com/updates/azuretls12/) a tutti i clienti di completare la migrazione verso soluzioni che supportano Transport Layer Security (TLS) 1,2 e per assicurarsi che TLS 1,2 venga usato per impostazione predefinita.

I servizi di Azure, tra cui [Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/microsoft-azure-service-fabric-6-3-refresh-release-cu1-notes/ba-p/791493), hanno completato il lavoro di progettazione per rimuovere la dipendenza dai protocolli TLS 1.0/1.1 e fornire supporto completo ai clienti che vogliono che i loro carichi di lavoro siano configurati per accettare e avviare solo connessioni TLS 1,2.

I clienti devono configurare i carichi di lavoro ospitati da Azure e le applicazioni locali che interagiscono con i servizi di Azure per usare TLS 1,2 per impostazione predefinita. Di seguito viene illustrato come [configurare Service Fabric nodi del cluster e le applicazioni](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md) per l'uso di una versione specifica di TLS.

## <a name="windows-defender"></a>Windows Defender 

Per impostazione predefinita, Windows Defender Antivirus è installato in Windows Server 2016. Per informazioni dettagliate, vedere [Windows Defender Antivirus in Windows Server 2016](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016). L'interfaccia utente viene installata per impostazione predefinita in alcuni SKU, ma non è obbligatoria. Per ridurre eventuali effetti sulle prestazioni e il sovraccarico di utilizzo delle risorse dovuti a Windows Defender e se i criteri di sicurezza consentono di escludere processi e i percorsi per il software open source, dichiarare le proprietà seguenti del modello di Resource Manager Estensioni set di scalabilità di macchine virtuali per escludere il cluster di Service Fabric dalle analisi:


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

## <a name="platform-isolation"></a>Isolamento piattaforma
Per impostazione predefinita, alle applicazioni Service Fabric viene concesso l'accesso al Runtime Service Fabric stesso, che si manifesta in forme diverse: [variabili di ambiente](service-fabric-environment-variables-reference.md) che puntano a percorsi di file nell'host corrispondente ai file dell'applicazione e dell'infrastruttura, un endpoint di comunicazione tra processi che accetta richieste specifiche dell'applicazione e il certificato client che l'infrastruttura prevede che l'applicazione usi per l'autenticazione. Nell'eventualità che il servizio ospiti un codice non attendibile, è consigliabile disabilitare questo accesso al runtime di SF, a meno che non sia necessario esplicitamente. L'accesso al runtime viene rimosso usando la dichiarazione seguente nella sezione policies del manifesto dell'applicazione: 

```xml
<ServiceManifestImport>
    <Policies>
        <ServiceFabricRuntimeAccessPolicy RemoveServiceFabricRuntimeAccess="true"/>
    </Policies>
</ServiceManifestImport>

```

## <a name="next-steps"></a>Passaggi successivi

* Creare un cluster in macchine virtuali o computer che eseguono Windows Server: [Service Fabric la creazione di cluster per Windows Server](service-fabric-cluster-creation-for-windows-server.md).
* Creare un cluster nelle VM o nei computer che eseguono Linux: [creare un cluster Linux](service-fabric-cluster-creation-via-portal.md).
* Informazioni sulle [opzioni di supporto di Service Fabric](service-fabric-support.md).

[Image1]: ./media/service-fabric-best-practices/generate-common-name-cert-portal.png
