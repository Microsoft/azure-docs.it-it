---
title: Creare un modello di generatore di immagini di Azure (anteprima)
description: Informazioni su come creare un modello da usare con Azure Image Builder.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: c13ace67f18b619d5ad86106ecb648db722be9fa
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792446"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Anteprima: creare un modello di generatore di immagini di Azure 

Azure Image Builder usa un file con estensione JSON per passare informazioni al servizio Image Builder. In questo articolo verranno esaminate le sezioni del file JSON, in modo che sia possibile crearne di personalizzate. Per visualizzare esempi di file con estensione JSON completi, vedere [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Questo è il formato di modello di base:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Tipo e versione API

`type` È il tipo di risorsa, che deve essere `"Microsoft.VirtualMachineImages/imageTemplates"`. Il `apiVersion` cambierà nel tempo durante la modifica dell'API, ma dovrebbe `"2019-05-01-preview"` essere per l'anteprima.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Percorso

Il percorso è l'area in cui verrà creata l'immagine personalizzata. Per l'anteprima di Image Builder sono supportate le aree seguenti:

- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-occidentali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Europa settentrionale
- Europa occidentale


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Per impostazione predefinita, il generatore di immagini userà una macchina virtuale di compilazione "Standard_D1_v2", è possibile eseguire l'override di questo, ad esempio, se si vuole personalizzare un'immagine per una VM GPU, è necessaria una dimensione della VM GPU. Operazione facoltativa.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Per impostazione predefinita, Image Builder non modificherà le dimensioni dell'immagine, utilizzerà le dimensioni dell'immagine di origine. È possibile aumentare le dimensioni del disco del sistema operativo (Win e Linux), questo è facoltativo e il valore 0 indica che la dimensione dell'immagine di origine è uguale a quella di. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Se non si specifica alcuna proprietà di VNET, il generatore di immagini creerà il proprio VNET, l'indirizzo IP pubblico e il NSG. L'indirizzo IP pubblico viene usato per il servizio per comunicare con la macchina virtuale di compilazione. Tuttavia, se non si vuole un IP pubblico o si vuole che il generatore di immagini possa accedere alle risorse VNET esistenti, ad esempio i server di configurazione (DSC, chef, Puppet, Ansible), le condivisioni file e così via, è possibile specificare un VNET. Per ulteriori informazioni, vedere la [documentazione relativa alla rete](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder), che è facoltativa.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Tag

Si tratta di coppie chiave/valore che è possibile specificare per l'immagine generata.

## <a name="depends-on-optional"></a>Dipende da (facoltativo)

Questa sezione facoltativa può essere usata per garantire che le dipendenze vengano completate prima di procedere. 

```json
    "dependsOn": [],
```

Per altre informazioni, vedere [definire le dipendenze delle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identità
Per impostazione predefinita, Image Builder supporta l'uso di script o la copia di file da più posizioni, ad esempio GitHub e archiviazione di Azure. Per usarli, devono essere accessibili pubblicamente.

È anche possibile usare un'identità gestita assegnata dall'utente di Azure, definita dall'utente, per consentire a Generatore di immagini di accedere ad archiviazione di Azure, purché all'identità sia stato concesso un minimo di "lettore dati BLOB di archiviazione" nell'account di archiviazione di Azure. Ciò significa che non è necessario rendere i BLOB di archiviazione accessibili esternamente o configurare i token SAS.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Per un esempio completo, vedere [usare un'identità gestita assegnata dall'utente di Azure per accedere ai file in archiviazione di Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Supporto di Image Builder per un'identità assegnata dall'utente: • supporta solo una singola identità • non supporta i nomi di dominio personalizzati

Per altre informazioni, vedere informazioni [sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Per altre informazioni sulla distribuzione di questa funzionalità, vedere [configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure con l'interfaccia della riga di comando di](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)Azure.

## <a name="properties-source"></a>Proprietà: origine

La `source` sezione contiene informazioni sull'immagine di origine che verrà usata da Image Builder.

L'API richiede un'SourceType ' che definisce l'origine per la compilazione dell'immagine, attualmente sono disponibili tre tipi:
- PlatformImage-indica che l'immagine di origine è un'immagine del Marketplace.
- ManagedImage: usare questo oggetto quando si inizia da un'immagine gestita normale.
- SharedImageVersion: viene usato quando si usa una versione di immagine in una raccolta di immagini condivise come origine.

### <a name="iso-source"></a>Origine ISO
Questa funzionalità è deprecata da Image Builder, perché ora sono disponibili [Immagini di sottoscrizione Bring your own](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos). esaminare le sequenze temporali seguenti:
    * 31 marzo 2020: i modelli di immagine con origini ISO RHEL saranno più accettati dal provider di risorse.
    * 30 aprile 2020: i modelli di immagine che contengono origini ISO RHEL non verranno più elaborati.

### <a name="platformimage-source"></a>Origine PlatformImage 
Azure Image Builder supporta le immagini di Windows Server e client e Linux in Azure Marketplace. per l'elenco completo, vedere [qui](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) . 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Le proprietà sono le stesse usate per creare le VM usando AZ CLI, eseguire il comando seguente per ottenere le proprietà: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

È possibile usare ' Latest ' nella versione, la versione viene valutata quando si verifica la compilazione dell'immagine, non quando viene inviato il modello. Se si usa questa funzionalità con la destinazione raccolta immagini condivisa, è possibile evitare di inviare nuovamente il modello ed eseguire di nuovo la compilazione dell'immagine a intervalli, in modo che le immagini vengano ricreate dalle immagini più recenti.

### <a name="managedimage-source"></a>Origine ManagedImage

Imposta l'immagine di origine come immagine gestita esistente di un disco rigido virtuale generalizzato o di una macchina virtuale. L'immagine gestita di origine deve essere di un sistema operativo supportato e trovarsi nella stessa area del modello di generatore di immagini di Azure. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` Deve essere il ResourceID dell'immagine gestita. Consente `az image list` di elencare le immagini disponibili.


### <a name="sharedimageversion-source"></a>Origine SharedImageVersion
Imposta l'immagine di origine in una versione di immagine esistente in una raccolta di immagini condivise. La versione dell'immagine deve essere di un sistema operativo supportato e l'immagine deve essere replicata nella stessa area del modello di generatore di immagini di Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` Deve essere ResourceId della versione dell'immagine. Usare [AZ sig Image-Version list](/cli/azure/sig/image-version#az-sig-image-version-list) per elencare le versioni delle immagini.

## <a name="properties-buildtimeoutinminutes"></a>Proprietà: buildTimeoutInMinutes

Per impostazione predefinita, il generatore di immagini viene eseguito per 240 minuti. Successivamente, il timeout e l'arresto, indipendentemente dal fatto che la compilazione dell'immagine sia stata completata o meno. Se viene raggiunto il timeout, verrà visualizzato un errore simile al seguente:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Se non si specifica un valore buildTimeoutInMinutes o lo si imposta su 0, verrà usato il valore predefinito. È possibile aumentare o diminuire il valore, fino al massimo di 960mins (16hrs). Per Windows, non è consigliabile impostare questo valore al di sotto di 60 minuti. Se si rileva il timeout, esaminare i [log](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)per verificare se il passaggio di personalizzazione è in attesa di un input dell'utente. 

Se è necessario più tempo per il completamento delle personalizzazioni, impostarlo su quello che si ritiene necessario, con un lieve overhead. Tuttavia, non impostarla troppo elevata perché potrebbe essere necessario attendere il timeout prima di visualizzare un errore. 


## <a name="properties-customize"></a>Proprietà: Personalizza

Image Builder supporta più "personalizzatori". I personalizzatori sono funzioni usate per personalizzare l'immagine, ad esempio l'esecuzione di script o il riavvio dei server. 

Quando si `customize`USA: 
- È possibile usare più personalizzatori, ma devono avere un valore univoco `name`.
- I personalizzatori vengono eseguiti nell'ordine specificato nel modello.
- Se un verbi ha esito negativo, l'intero componente di personalizzazione avrà esito negativo e segnalerà un errore.
- Si consiglia vivamente di testare accuratamente lo script prima di utilizzarlo in un modello. Il debug dello script nella propria macchina virtuale sarà più semplice.
- Non inserire dati sensibili negli script. 
- I percorsi di script devono essere accessibili pubblicamente, a meno che non si usi [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
La sezione Customize è una matrice. Il generatore di immagini di Azure eseguirà le personalizzazioni in ordine sequenziale. Eventuali errori in qualsiasi verbi non riusciranno a eseguire il processo di compilazione. 
 
 
### <a name="shell-customizer"></a>Verbi Shell

Il verbi della shell supporta l'esecuzione di script della shell, che devono essere accessibili pubblicamente per poter accedere al IB.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Supporto del sistema operativo: Linux 
 
Personalizza proprietà:

- **tipo** : Shell 
- **nome** -nome per tenere traccia della personalizzazione 
- **scriptUri** -URI per il percorso del file 
- matrice **inline** di comandi della shell, separati da virgole.
- **sha256Checksum** : valore del checksum SHA256 del file. questa operazione viene generata localmente, quindi il generatore di immagini esegue il checksum e la convalida.
    * Per generare il sha256Checksum, usando un terminale in Mac/Linux eseguire:`sha256sum <fileName>`


Per eseguire i comandi con privilegi avanzati, è necessario che siano preceduti da `sudo`.

> [!NOTE]
> Quando si esegue la shell verbi con RHEL ISO source, è necessario assicurarsi che la prima Shell di personalizzazione gestisca la registrazione con un server di diritti di Red Hat prima che venga eseguita la personalizzazione. Al termine della personalizzazione, lo script deve annullare la registrazione con il server di diritti.

### <a name="windows-restart-customizer"></a>Riavvio di Windows verbi 
Il verbi di riavvio consente di riavviare una macchina virtuale Windows e attendere che torni online, consentendo di installare software che richiede un riavvio.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Supporto del sistema operativo: Windows
 
Personalizza proprietà:
- **Tipo**: WindowsRestart
- **restartCommand** : comando per eseguire il riavvio (facoltativo). Il valore predefinito è `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** : comando per verificare se il riavvio è stato completato (facoltativo). 
- **restartTimeout** -timeout di riavvio specificato come stringa di grandezza e unità. Ad esempio, `5m` (5 minuti) o `2h` (2 ore). Il valore predefinito è:' 5m '

### <a name="linux-restart"></a>Riavvio di Linux  
Non è disponibile alcun riavvio di Linux verbi, tuttavia, se si installano i driver o i componenti che richiedono un riavvio, è possibile installarli e richiamare un riavvio usando la shell verbi, il timeout SSH di 20 minuti per la macchina virtuale di compilazione.

### <a name="powershell-customizer"></a>Verbi di PowerShell 
La shell verbi supporta l'esecuzione di script di PowerShell e il comando inline. gli script devono essere accessibili pubblicamente per poter accedere al IB.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Supporto del sistema operativo: Windows e Linux

Personalizza proprietà:

- **digitare** : PowerShell.
- **scriptUri** -URI per il percorso del file di script di PowerShell. 
- **inline** : comandi inline da eseguire, separati da virgole.
- **validExitCodes** : codici validi e facoltativi che possono essere restituiti dal comando script/inline. in questo modo si eviterà l'errore del comando script/inline.
- **runElevated** : facoltativo, booleano, supporto per l'esecuzione di comandi e script con autorizzazioni elevate.
- **sha256Checksum** : valore del checksum SHA256 del file. questa operazione viene generata localmente, quindi il generatore di immagini esegue il checksum e la convalida.
    * Per generare il sha256Checksum, usando PowerShell in Windows [Get-hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Verbi file

Il file verbi consente al generatore di immagini di scaricare un file da un archivio GitHub o Azure. Se si dispone di una pipeline di compilazione immagine che si basa sugli artefatti di compilazione, è possibile impostare il file verbi per il download dalla condivisione di compilazione e spostare gli artefatti nell'immagine.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Supporto del sistema operativo: Linux e Windows 

Proprietà file verbi:

- **SourceUri** : un endpoint di archiviazione accessibile. può trattarsi di GitHub o di archiviazione di Azure. È possibile scaricare un solo file, non un'intera directory. Se è necessario scaricare una directory, usare un file compresso, quindi decomprimerla usando la shell o i personalizzatori PowerShell. 
- **destinazione** : è il percorso di destinazione completo e il nome file. Qualsiasi percorso e sottodirectory a cui viene fatto riferimento deve esistere, utilizzare la shell o i personalizzatori PowerShell per configurarli in anticipo. Per creare il percorso, è possibile usare i personalizzatori di script. 

Questa operazione è supportata da directory Windows e percorsi Linux, ma esistono alcune differenze: 
- Sistema operativo Linux: l'unico generatore di immagini di percorso in cui è possibile scrivere è/tmp.
- Windows: nessuna restrizione del percorso, ma il percorso deve esistere.
 
 
Se si verifica un errore durante il tentativo di scaricare il file o inserirlo in una directory specificata, il passaggio di personalizzazione avrà esito negativo e sarà presente nel file customization. log.

> [!NOTE]
> Il file verbi è adatto solo per il download di file di piccole dimensioni, < 20MB. Per i download di file di grandi dimensioni usare uno script o un comando inline, usare il codice per scaricare i `wget` file `curl`, ad esempio `Invoke-WebRequest`, Linux o, Windows.

I file nel file verbi possono essere scaricati dall'archiviazione di Azure tramite [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Windows Update verbi
Questo verbi si basa sul [provisioning della community Windows Update](https://packer.io/docs/provisioners/community-supported.html) per Packer, un progetto open source gestito dalla community di Packer. Microsoft verifica e convalida il provisioning con il servizio Generatore di immagini e supporta l'analisi dei problemi relativi all'IT e il lavoro per risolvere i problemi, ma il progetto open source non è ufficialmente supportato da Microsoft. Per la documentazione dettagliata su e la guida per il provisioning di Windows Update, vedere il repository del progetto.
 
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
Supporto del sistema operativo: Windows

Personalizza proprietà:
- **tipo** : windowsupdate.
- **searchCriteria** -facoltativo, definisce il tipo di aggiornamenti installati (consigliato, importante e così via), BrowseOnly = 0 e disinstallato = 0 (scelta consigliata) è il valore predefinito.
- **Filters** : facoltativo consente di specificare un filtro per includere o escludere gli aggiornamenti.
- **updateLimit** : facoltativo, definisce il numero di aggiornamenti che è possibile installare, valore predefinito 1000.
 
 

### <a name="generalize"></a>Generalizzazione 
Per impostazione predefinita, il generatore di immagini di Azure eseguirà anche il codice di deprovisioning alla fine di ogni fase di personalizzazione dell'immagine, in modo da generalizzare l'immagine. Generalizzare è un processo in cui l'immagine è configurata in modo che possa essere riutilizzata per creare più macchine virtuali. Per le macchine virtuali Windows, Azure Image Builder USA Sysprep. Per Linux, Azure Image Builder esegue "waagent-deprovision". 

I comandi che gli utenti di generatore di immagini generalizzano potrebbero non essere adatti per ogni situazione, quindi Generatore immagini di Azure consentirà di personalizzare questo comando, se necessario. 

Se si esegue la migrazione della personalizzazione esistente e si usano comandi sysprep/waagent diversi, è possibile usare i comandi generici del generatore di immagini e, se la creazione della macchina virtuale non riesce, usare i comandi Sysprep o waagent personalizzati.

Se il generatore di immagini di Azure crea correttamente un'immagine personalizzata di Windows e si crea una macchina virtuale da tale immagine, scoprire che la creazione della macchina virtuale ha esito negativo o non viene completata correttamente, sarà necessario esaminare la documentazione di Windows Server Sysprep o generare una richiesta di supporto con il team di supporto di Windows Server Sysprep Customer Services, che può risolvere e consigliare l'utilizzo corretto di Sysprep.


#### <a name="default-sysprep-command"></a>Comando Sysprep predefinito
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Comando di deprovisioning predefinito di Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Override dei comandi
Per eseguire l'override dei comandi, usare i provisioner di script PowerShell o Shell per creare i file di comando con il nome esatto del file e inserirli nelle directory corrette:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/DeprovisioningScript.sh

Il generatore di immagini leggerà questi comandi, che verranno scritti nei log AIB,' Customization. log '. Vedere [risoluzione dei problemi relativi](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) alla procedura di raccolta dei log.
 
## <a name="properties-distribute"></a>Proprietà: Distribuisci

Azure Image Builder supporta tre destinazioni di distribuzione: 

- immagine gestita da **managedImage** .
- **sharedImage** -raccolta di immagini condivise.
- **VHD** -VHD in un account di archiviazione.

È possibile distribuire un'immagine in entrambi i tipi di destinazione nella stessa configurazione. vedere gli [esempi](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Poiché è possibile avere più di una destinazione a cui distribuire, Image Builder mantiene uno stato per ogni destinazione di distribuzione a cui è possibile accedere eseguendo una `runOutputName`query su.  È `runOutputName` un oggetto di cui è possibile eseguire una query dopo la distribuzione per ottenere informazioni su tale distribuzione. Ad esempio, è possibile eseguire una query sul percorso del disco rigido virtuale o delle aree in cui è stata replicata la versione dell'immagine oppure la versione dell'immagine SIG creata. Si tratta di una proprietà di ogni destinazione di distribuzione. Il `runOutputName` deve essere univoco per ogni destinazione di distribuzione. Di seguito è riportato un esempio che esegue una query su una distribuzione della raccolta immagini condivisa:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

Output:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribuisci: managedImage

L'output dell'immagine sarà una risorsa immagine gestita.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Distribuisci proprietà:
- **tipo** : managedImage 
- **imageId** : ID risorsa dell'immagine di destinazione, formato previsto:/subscriptions/\<subscriptionId>/ResourceGroups/\<destinationResourceGroupName>/Providers/Microsoft.Compute/images/\<ImageName>
- **location** : percorso dell'immagine gestita.  
- **runOutputName** : nome univoco per l'identificazione della distribuzione.  
- **artifactTags** : Tag della coppia di valori chiave specificati dall'utente facoltativo.
 
 
> [!NOTE]
> Il gruppo di risorse di destinazione deve esistere.
> Se si vuole che l'immagine venga distribuita in un'area diversa, aumenterà il tempo di distribuzione. 

### <a name="distribute-sharedimage"></a>Distribuisci: sharedImage 
La raccolta di immagini condivise di Azure è un nuovo servizio di gestione delle immagini che consente di gestire la replica dell'area immagine, il controllo delle versioni e la condivisione di immagini personalizzate. Azure Image Builder supporta la distribuzione con questo servizio, in modo che sia possibile distribuire immagini alle aree supportate dalle raccolte immagini condivise. 
 
Una raccolta di immagini condivise è costituita da: 
 
- Raccolta-contenitore per più immagini condivise. Una raccolta viene distribuita in un'area.
- Definizioni di immagine: raggruppamento concettuale per le immagini. 
- Versioni delle immagini: tipo di immagine usato per la distribuzione di una macchina virtuale o di un set di scalabilità. Le versioni delle immagini possono essere replicate in altre aree in cui devono essere distribuite le macchine virtuali.
 
Prima di poter distribuire nella raccolta immagini, è necessario creare una raccolta e una definizione di immagine, vedere [Immagini condivise](shared-images.md). 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Distribuire le proprietà per le raccolte di immagini condivise:

- **tipo** -sharedImage  
- **galleryImageId** : ID della raccolta di immagini condivise. Il formato è:/subscriptions/\<SubscriptionId>/resourcegroups/\<resourceGroupName>/Providers/Microsoft.Compute/Galleries/\<sharedImageGalleryName>/images/\<imageGalleryName>.
- **runOutputName** : nome univoco per l'identificazione della distribuzione.  
- **artifactTags** : Tag della coppia di valori chiave specificati dall'utente facoltativo.
- **replicationRegions** : matrice di aree per la replica. Una delle aree deve essere l'area in cui è distribuita la raccolta.
 
> [!NOTE]
> È possibile usare Azure Image Builder in un'area diversa per la raccolta, ma il servizio Generatore di immagini di Azure dovrà trasferire l'immagine tra i Data Center e questa operazione richiederà più tempo. Il generatore di immagini esegue automaticamente la versione dell'immagine, in base a un intero monotonico, non è possibile specificarla al momento. 

### <a name="distribute-vhd"></a>Distribuisci: VHD  
È possibile eseguire l'output in un disco rigido virtuale. È quindi possibile copiare il disco rigido virtuale e usarlo per la pubblicazione in Azure MarketPlace oppure usare con Azure Stack.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
Supporto del sistema operativo: Windows e Linux

Distribuisci parametri VHD:

- **tipo** : VHD.
- **runOutputName** : nome univoco per l'identificazione della distribuzione.  
- **tag** : Tag della coppia di valori chiave specificati dall'utente facoltativo.
 
Azure Image Builder non consente all'utente di specificare un percorso dell'account di archiviazione, ma è possibile eseguire una query sullo `runOutputs` stato di per ottenere il percorso.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Dopo aver creato il disco rigido virtuale, copiarlo in un percorso diverso, appena possibile. Il disco rigido virtuale viene archiviato in un account di archiviazione nel gruppo di risorse temporaneo creato quando il modello di immagine viene inviato al servizio Azure Image Builder. Se si elimina il modello di immagine, il disco rigido virtuale verrà perso. 
 
## <a name="next-steps"></a>Passaggi successivi

Sono disponibili file Sample. JSON per diversi scenari in [GitHub di Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder).
 
