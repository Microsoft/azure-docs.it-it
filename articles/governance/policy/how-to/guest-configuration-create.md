---
title: Come creare criteri di Configurazione guest per Windows
description: Informazioni su come creare criteri di Configurazione guest di Criteri di Azure per Windows.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 6eaefdbc28b8efc53dc7c4d46eb5d8a56d5be141
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106096598"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Come creare criteri di Configurazione guest per Windows

Prima di creare definizioni dei criteri personalizzate, è consigliabile leggere le informazioni generali nella pagina [Configurazione guest di Criteri di Azure](../concepts/guest-configuration.md).
 
Per informazioni sulla creazione di criteri di Configurazione guest per Linux, vedere la pagina [Come creare criteri di Configurazione guest per Linux](./guest-configuration-create-linux.md)

Quando si esegue il controllo di Windows, Configurazione guest usa un modulo risorse [DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview) per creare il file di configurazione. La configurazione DSC definisce la condizione in cui deve trovarsi il computer. Se la valutazione della configurazione ha esito negativo, viene attivato l'effetto dei criteri **auditIfNotExists** e il computer viene considerato **non conforme**.

È possibile usare la funzione [Configurazione guest di Criteri di Azure](../concepts/guest-configuration.md) solo per controllare le impostazioni all'interno dei computer. La correzione delle impostazioni all'interno dei computer non è ancora disponibile.

Usare le azioni seguenti per creare una configurazione personalizzata per la convalida dello stato di un computer Azure o non Azure.

> [!IMPORTANT]
> Le definizioni dei criteri personalizzati con la configurazione Guest negli ambienti Azure per enti pubblici e Azure Cina sono una funzionalità in anteprima.
>
> L'estensione Configurazione guest è necessaria per eseguire controlli nelle macchine virtuali di Azure. Per distribuire l'estensione su larga scala in tutti i computer Windows, assegnare le definizioni dei criteri seguenti: `Deploy prerequisites to enable Guest Configuration Policy on Windows VMs`
> 
> Non usare segreti o informazioni riservate nei pacchetti di contenuto personalizzati.

## <a name="install-the-powershell-module"></a>Installare il modulo PowerShell

Il modulo Configurazione guest automatizza il processo di creazione di contenuti personalizzati, tra cui:

- Creazione di un artefatto del contenuto di Configurazione guest (ZIP)
- Test automatizzato dell'artefatto
- Creazione di una definizione dei criteri
- Pubblicazione dei criteri

Il modulo può essere installato in un computer che esegue Windows, macOS o Linux con PowerShell 6.2 o versioni successive in esecuzione in locale oppure con [Azure Cloud Shell](https://shell.azure.com) o con l'[immagine Docker Azure PowerShell Core](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> La compilazione delle configurazioni non è ancora supportata in Linux.

### <a name="base-requirements"></a>Requisiti di base

Sistemi operativi in cui è possibile installare il modulo:

- Linux
- macOS
- Windows

Il modulo risorse Configurazione guest richiede il software seguente:

- PowerShell 6.2 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 o versione successiva. Se non è ancora installato, seguire [queste istruzioni](/powershell/azure/install-az-ps).
  - Sono necessari solo AZ modules ' AZ. Accounts ' è AZ. resources '.

### <a name="install-the-module"></a>Installare il modulo

Per installare il modulo **GuestConfiguration** in PowerShell:

1. Al prompt di PowerShell, eseguire il comando seguente:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Verificare che il modulo sia stato importato:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Criteri e artefatti di Configurazione guest per Windows

Configurazione guest usa PowerShell Desired State Configuration come astrazione del linguaggio per scrivere cosa controllare in Windows. L'agente carica un'istanza autonoma di PowerShell 6.2, quindi non si verifica alcun conflitto con l'utilizzo di PowerShell DSC in Windows PowerShell 5.1 e non è necessario preinstallare PowerShell 6.2 o versione successiva.

Per una panoramica dei concetti e della terminologia DSC, vedere [Panoramica di PowerShell DSC](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Differenze tra i moduli Configurazione guest e i moduli Windows PowerShell DSC

Quando la configurazione Guest controlla un computer, la sequenza di eventi è diversa da quella di Windows PowerShell DSC.

1. L'agente esegue prima di tutto `Test-TargetResource` per determinare se la configurazione è nello stato corretto.
1. Il valore booleano restituito dalla funzione determina se lo stato di Azure Resource Manager per l'assegnazione guest deve essere conforme o non conforme.
1. Il provider esegue `Get-TargetResource` per restituire lo stato corrente di ogni impostazione, in modo che siano disponibili informazioni dettagliate sia sul motivo per cui un computer non è conforme sia per confermare che lo stato corrente è conforme.

I parametri nei criteri di Azure che passano i valori alle assegnazioni di configurazione Guest devono essere di tipo _stringa_ . Non è possibile passare matrici tramite parametri, anche se la risorsa DSC supporta matrici.

### <a name="get-targetresource-requirements"></a>Requisiti di Get-TargetResource

La funzione `Get-TargetResource` presenta requisiti speciali per Configurazione guest, non necessari per Windows Desired State Configuration.

- La tabella hash restituita deve includere una proprietà denominata **Reasons**.
- La proprietà Reasons deve essere una matrice.
- Ogni elemento nella matrice deve essere una tabella hash con chiavi denominate **Code** e **Phrase**.

La proprietà Reasons viene usata dal servizio per standardizzare il modo in cui le informazioni vengono presentate quando un computer non è conforme. È possibile considerare ogni elemento di Reasons come un "motivo" per cui la risorsa non è conforme. La proprietà è una matrice perché una risorsa può non essere conforme per più di un motivo.

Le proprietà **Code** e **Phrase** sono richieste dal servizio. Quando si crea una risorsa personalizzata, impostare il testo (in genere StdOut) da mostrare come motivo per cui la risorsa non è conforme come valore di **Phrase**. La proprietà **Code** presenta requisiti di formattazione specifici, in modo che nei report le informazioni sulla risorsa con cui si esegue il controllo vengano visualizzate chiaramente. Questa soluzione rende estendibile Configurazione Guest. È possibile eseguire qualsiasi comando, a condizione che l'output possa essere restituito come valore stringa per la proprietà **Phrase**.

- **Code** (stringa): nome della risorsa, ripetuto, seguito da un nome breve senza spazi come identificatore per il motivo. Questi tre valori devono essere delimitati da due punti senza spazi.
  - Ad esempio, `registry:registry:keynotpresent`
- **Phrase** (stringa): testo leggibile che spiega il motivo per cui l'impostazione non è conforme.
  - Ad esempio, `The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

È necessario aggiungere la proprietà reasons al file MOF dello schema per la risorsa come classe incorporata.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

Se la risorsa ha proprietà obbligatorie, tali proprietà devono essere restituite anche da `Get-TargetResource` in parallelo con la `reasons` classe. Se `reasons` non è incluso, il servizio include un comportamento "catch-all" che confronta i valori di input `Get-TargetResource` e i valori restituiti da `Get-TargetResource` e fornisce un confronto dettagliato come `reasons` .

### <a name="configuration-requirements"></a>Requisiti di configurazione

Il nome della configurazione personalizzata deve essere coerente ovunque. Il nome del file con estensione zip per il pacchetto di contenuto, il nome della configurazione nel file MOF e il nome dell'assegnazione Guest nel modello di Azure Resource Manager (modello ARM) devono essere uguali.

### <a name="policy-requirements"></a>Requisiti dei criteri

La sezione Definizione dei criteri `metadata` deve includere due proprietà per il servizio di configurazione Guest per automatizzare il provisioning e la creazione di report delle assegnazioni di configurazione Guest. La `category` proprietà deve essere impostata su "Guest Configuration" e una sezione denominata `Guest Configuration` deve contenere informazioni sull'assegnazione della configurazione Guest. Il `New-GuestConfigurationPolicy` cmdlet crea automaticamente questo testo.
Vedere le istruzioni dettagliate in questa pagina.

Nell'esempio seguente viene illustrata la `metadata` sezione.

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

### <a name="scaffolding-a-guest-configuration-project"></a>Scaffolding di un progetto di Configurazione guest

Gli sviluppatori che vogliono accelerare le fasi iniziali e usare il codice di esempio possono installare un progetto della community denominato **Guest Configuration Project**. Il progetto installa un modello per il modulo [Plaster](https://github.com/powershell/plaster) PowerShell. Questo strumento può essere usato per lo scaffolding di un progetto con una configurazione funzionante e una risorsa di esempio, nonché un set di test [Pester](https://github.com/pester/pester) per convalidare il progetto. Il modello include anche gli strumenti di esecuzione attività per Visual Studio Code per automatizzare la creazione e la convalida del pacchetto di Configurazione guest. Per altre informazioni, vedere il progetto GitHub [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject).

Per altre informazioni sull'uso delle configurazioni in generale, vedere [Scrivere, compilare e applicare una configurazione](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Contenuti previsti di un artefatto di Configurazione guest

Il pacchetto completato viene usato da Configurazione guest per creare le definizioni di Criteri di Azure. Il pacchetto è costituito da:

- Configurazione DSC compilata come file MOF
- Cartella dei moduli
  - Modulo GuestConfiguration
  - Modulo DscNativeResources
  - Moduli risorse (Windows) DSC richiesti dal file MOF

I cmdlet di PowerShell facilitano la creazione del pacchetto. Non è necessaria alcuna cartella di livello radice o cartella delle versioni. Il formato del pacchetto deve essere un file con estensione zip e non può superare le dimensioni totali di 100 MB quando non è compresso.

### <a name="storing-guest-configuration-artifacts"></a>Archiviazione degli artefatti di Configurazione guest

Il pacchetto ZIP deve essere archiviato in un percorso accessibile dalle macchine virtuali gestite.
È ad esempio possibile usare repository GitHub, un repository di Azure o Archiviazione di Azure. Se si preferisce non rendere pubblico il pacchetto, è possibile includere un [token di firma di accesso condiviso](../../../storage/common/storage-sas-overview.md) nell'URL. È anche possibile implementare un [endpoint servizio](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) per i computer in una rete privata, anche se questa configurazione si applica solo all'accesso al pacchetto e non alla comunicazione con il servizio.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Procedura dettagliata per la creazione di criteri di controllo di Configurazione guest personalizzati per Windows

Creare una configurazione DSC per controllare le impostazioni. L'esempio di script di PowerShell seguente crea una configurazione denominata **AuditBitLocker**, importa il modulo risorse **PsDscResources** e usa la risorsa `Service` per eseguire il controllo per un servizio in esecuzione. Lo script di configurazione può essere eseguito da un computer Windows o macOS.

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

Eseguire questo script in un terminale di PowerShell o salvare il file con nome `config.ps1` nella cartella del progetto. Eseguirlo in PowerShell eseguendo `./config.ps1` nel terminale. Verrà creato un nuovo file MOF.

Il comando `Node AuditBitlocker` non è tecnicamente necessario, ma produce un file denominato `AuditBitlocker.mof` anziché il file `localhost.mof` predefinito. Un nome di file MOF che segue la configurazione consente di organizzare in modo semplice molti file quando si opera su larga scala.

Una volta compilato il file MOF, è necessario creare un pacchetto con i file di supporto. Il pacchetto completato viene usato da Configurazione guest per creare le definizioni di Criteri di Azure.

Il cmdlet `New-GuestConfigurationPackage` crea il pacchetto. I moduli necessari per la configurazione devono essere disponibili in `$Env:PSModulePath`. Parametri del cmdlet `New-GuestConfigurationPackage` durante la creazione del contenuto Windows:

- **Name**: nome del pacchetto di Configurazione guest.
- **Configuration**: percorso completo del documento di configurazione DSC compilato.
- **Path**: percorso della cartella di output. Questo parametro è facoltativo. Se non viene specificato, il pacchetto viene creato nella directory corrente.

Eseguire il comando seguente per creare un pacchetto usando la configurazione fornita nel passaggio precedente:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './AuditBitlocker/AuditBitlocker.mof'
```

Dopo aver creato il pacchetto di configurazione, ma prima di pubblicarlo in Azure, è possibile testare il pacchetto dalla workstation o dall'ambiente di integrazione continua e distribuzione continua (CI/CD). Il cmdlet di GuestConfiguration `Test-GuestConfigurationPackage` include nell'ambiente di sviluppo lo stesso agente usato nei computer Azure. Con questa soluzione, è possibile eseguire test di integrazione in locale prima del rilascio in ambienti cloud a pagamento.

Poiché l'agente valuta effettivamente l'ambiente locale, nella maggior parte dei casi è necessario eseguire il cmdlet Test sulla stessa piattaforma del sistema operativo che si prevede di controllare. Il test usa solo i moduli inclusi nel pacchetto di contenuto.

Parametri del cmdlet `Test-GuestConfigurationPackage`:

- **Name**: nome dei criteri di Configurazione guest.
- **Parameter**: parametri dei criteri forniti in formato di tabella hash.
- **Path**: percorso completo del pacchetto di Configurazione guest.

Eseguire il comando seguente per testare il pacchetto creato nel passaggio precedente:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Il cmdlet supporta anche l'input dalla pipeline di PowerShell. Inoltrare tramite pipe l'output del cmdlet `New-GuestConfigurationPackage` al cmdlet `Test-GuestConfigurationPackage`.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./AuditBitlocker/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Il passaggio successivo consiste nel pubblicare il file nell'archivio BLOB di Azure. Non sono previsti requisiti speciali per l'account di archiviazione, ma è consigliabile ospitare il file in un'area vicina ai computer. Se non si ha un account di archiviazione, usare l'esempio seguente. I comandi seguenti, incluso `Publish-GuestConfigurationPackage` , richiedono il `Az.Storage` modulo.

```azurepowershell-interactive
# Creates a new resource group, storage account, and container
New-AzResourceGroup -name myResourceGroupName -Location WestUS
New-AzStorageAccount -ResourceGroupName myResourceGroupName -Name myStorageAccountName -SkuName 'Standard_LRS' -Location 'WestUs' | New-AzStorageContainer -Name guestconfiguration -Permission Blob
```

Parametri del cmdlet `Publish-GuestConfigurationPackage`:

- **Path**: percorso del pacchetto da pubblicare
- **ResourceGroupName**: nome del gruppo di risorse in cui si trova l'account di archiviazione
- **StorageAccountName**: nome dell'account di archiviazione in cui deve essere pubblicato il pacchetto
- **StorageContainerName**: (valore predefinito: _guestconfiguration_) nome del contenitore di archiviazione nell'account di archiviazione
- **Force**: sovrascrivere il pacchetto esistente nell'account di archiviazione con lo stesso nome

L'esempio seguente pubblica il pacchetto nel nome del contenitore di archiviazione ' guestconfiguration '.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

Una volta creato e caricato un pacchetto di criteri personalizzati di Configurazione guest, creare la definizione dei criteri di Configurazione guest. Il cmdlet `New-GuestConfigurationPolicy` accetta un pacchetto di criteri personalizzati e crea una definizione dei criteri.

Parametri del cmdlet `New-GuestConfigurationPolicy`:

- **ContentUri**: URI http(s) pubblico del pacchetto di contenuto di Configurazione guest.
- **DisplayName**: nome visualizzato dei criteri.
- **Description**: descrizione dei criteri.
- **Parameter**: parametri dei criteri forniti in formato di tabella hash.
- **Version**: versione dei criteri.
- **Path**: percorso di destinazione in cui vengono create le definizioni dei criteri.
- **Platform**: piattaforma di destinazione (Windows/Linux) per i criteri e il pacchetto di contenuto di Configurazione guest.
- **Tag** aggiunge uno o più filtri di tag alla definizione dei criteri
- **Category** imposta il campo dei metadati della categoria nella definizione dei criteri

Nell'esempio seguente vengono create le definizioni dei criteri in un percorso specificato da un pacchetto di criteri personalizzati:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

I file seguenti vengono creati da `New-GuestConfigurationPolicy`:

- **auditIfNotExists.json**

L'output del cmdlet restituisce un oggetto contenente il nome visualizzato dell'iniziativa e il percorso dei file dei criteri.

Infine, pubblicare le definizioni dei criteri usando il cmdlet `Publish-GuestConfigurationPolicy`. Il cmdlet ha solo il parametro **Path** che punta al percorso dei file JSON creati da `New-GuestConfigurationPolicy`.

Per eseguire il comando Publish, è necessario l'accesso per la creazione di criteri in Azure. I requisiti di autorizzazione specifici sono documentati nella pagina [Panoramica di Criteri di Azure](../overview.md). Il ruolo predefinito migliore è **Collaboratore ai criteri delle risorse**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

Il cmdlet `Publish-GuestConfigurationPolicy` accetta il percorso dalla pipeline di PowerShell. Con questa funzionalità è possibile creare i file dei criteri e pubblicarli in un unico set di comandi inoltrati tramite pipe.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Con il criterio creato in Azure, l'ultimo passaggio consiste nell'assegnare la definizione. Vedere come assegnare la definizione con il [portale](../assign-policy-portal.md), l'interfaccia della riga di comando di [Azure](../assign-policy-azurecli.md)e [Azure PowerShell](../assign-policy-powershell.md).

### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtro dei criteri di Configurazione guest tramite tag

Le definizioni dei criteri create dai cmdlet nel modulo Configurazione guest possono includere un filtro per i tag. Il parametro **Tag** di `New-GuestConfigurationPolicy` supporta una matrice di tabelle hash in cui sono presenti singole voci di tag. I tag vengono aggiunti alla `If` sezione della definizione dei criteri e non possono essere modificati da un'assegnazione di criteri.

Di seguito è riportato un frammento di codice di esempio di una definizione dei criteri che filtra in base ai tag.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Uso di parametri nelle definizioni dei criteri di Configurazione guest personalizzate

Configurazione guest supporta l'override delle proprietà di una configurazione in fase di esecuzione. Ciò significa che i valori nel file MOF del pacchetto non devono essere considerati statici. I valori di sostituzione vengono forniti tramite criteri di Azure e non modificano la modalità di creazione o compilazione delle configurazioni.

I cmdlet `New-GuestConfigurationPolicy` e `Test-GuestConfigurationPolicyPackage` includono un parametro denominato **Parameter**. Questo parametro accetta una definizione di tabella hash che include tutti i dettagli su ogni parametro e crea le sezioni necessarie di ogni file usato per la definizione di Criteri di Azure.

Nell'esempio seguente viene creata una definizione dei criteri per controllare un servizio, in cui l'utente seleziona un'opzione in un elenco al momento dell'assegnazione dei criteri.

```azurepowershell-interactive
# This DSC Resource text:
Service 'UserSelectedNameExample'
      {
          Name = 'ParameterValue'
          Ensure = 'Present'
          State = 'Running'
      }

# Would require the following hashtable:
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'UserSelectedNameExample'                          # DSC configuration resource id (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>Estensione di Configurazione guest con strumenti di terze parti

I pacchetti di artefatti per Configurazione guest possono essere estesi per includere strumenti di terze parti.
Per l'estensione di Configurazione guest è necessario sviluppare due componenti.

- Una risorsa Desired State Configuration che gestisce tutte le attività correlate alla gestione dello strumento di terze parti
  - Installazione
  - Invoke
  - Conversione dell'output
- Contenuto con il formato corretto per l'utilizzo nativo da parte dello strumento

Per la risorsa DSC è necessario lo sviluppo personalizzato se una soluzione community non esiste già.
Le soluzioni della community possono essere individuate cercando in PowerShell Gallery il tag [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22).

> [!Note]
> L'estendibilità di Configurazione guest è uno scenario di tipo "Bring Your Own License". Prima dell'uso, assicurarsi che i termini e le condizioni degli strumenti di terze parti siano soddisfatti.

Dopo che la risorsa DSC è stata installata nell'ambiente di sviluppo, usare il parametro **FilesToInclude** per `New-GuestConfigurationPackage` per includere il contenuto per la piattaforma di terze parti nell'artefatto di contenuto.

## <a name="policy-lifecycle"></a>Ciclo di vita dei criteri

Se si vuole rilasciare un aggiornamento del criterio, apportare la modifica sia per il pacchetto di configurazione Guest che per i dettagli della definizione di criteri di Azure.

> [!NOTE]
> La `version` proprietà dell'assegnazione di configurazione Guest ha effetto solo sui pacchetti ospitati da Microsoft. La procedura consigliata per il controllo delle versioni del contenuto personalizzato consiste nell'includere la versione nel nome del file.

In primo luogo, quando `New-GuestConfigurationPackage` si esegue, specificare un nome per il pacchetto che lo renda univoco rispetto alle versioni precedenti. È possibile includere un numero di versione nel nome, ad esempio `PackageName_1.0.0` . Il numero in questo esempio viene utilizzato solo per rendere univoco il pacchetto, non per specificare che il pacchetto deve essere considerato più recente o meno recente di altri pacchetti.

In secondo luogo, aggiornare i parametri usati con il `New-GuestConfigurationPolicy` cmdlet attenendosi a ognuna delle spiegazioni riportate di seguito.

- **Version**: quando si esegue il cmdlet `New-GuestConfigurationPolicy`, è necessario specificare un numero di versione maggiore di quello attualmente pubblicato.
- **contentUri**: quando si esegue il `New-GuestConfigurationPolicy` cmdlet, è necessario specificare un URI per il percorso del pacchetto. Se si include una versione del pacchetto nel nome del file, il valore di questa proprietà verrà modificato in ogni versione.
- **contentHash**: questa proprietà viene aggiornata automaticamente dal cmdlet `New-GuestConfigurationPolicy`. Si tratta di un valore hash del pacchetto creato da `New-GuestConfigurationPackage`. La proprietà deve essere corretta per il file `.zip` da pubblicare. Se viene aggiornata solo la proprietà **contentUri**, l'estensione non accetterà il pacchetto di contenuto.

Il modo più semplice per rilasciare un pacchetto aggiornato consiste nel ripetere il processo descritto in questo articolo e fornire un numero di versione aggiornato. Questo processo garantisce che tutte le proprietà siano state aggiornate correttamente.

## <a name="optional-signing-guest-configuration-packages"></a>Facoltativo: Firma dei pacchetti di Configurazione guest

I criteri personalizzati di Configurazione guest usano l'hash SHA256 per verificare che il pacchetto dei criteri non sia stato modificato.
Facoltativamente, i clienti possono anche usare un certificato per firmare i pacchetti e forzare l'estensione Configurazione guest in modo da consentire solo il contenuto firmato.

Per attuare questo scenario, è necessario completare due passaggi. Eseguire il cmdlet per firmare il pacchetto di contenuto e accodare un tag ai computer che devono richiedere la firma del codice.

Per usare la funzionalità di convalida della firma, eseguire il cmdlet `Protect-GuestConfigurationPackage` per firmare il pacchetto prima di pubblicarlo. Questo cmdlet richiede un certificato di firma del codice.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametri del cmdlet `Protect-GuestConfigurationPackage`:

- **Path**: percorso completo del pacchetto di Configurazione guest.
- **Certificate**: certificato di firma del codice per firmare il pacchetto. Questo parametro è supportato solo quando si firma il contenuto per Windows.

L'agente GuestConfiguration richiede che la chiave pubblica del certificato sia presente in "Autorità di certificazione con radice trusted" nei computer Windows e nel percorso `/usr/local/share/ca-certificates/extra` nei computer Linux. Per consentire al nodo di verificare il contenuto firmato, installare la chiave pubblica del certificato nel computer prima di applicare i criteri personalizzati. Questo processo può essere eseguito usando qualsiasi tecnica all'interno della macchina virtuale o usando Criteri di Azure. Un modello di esempio è [disponibile qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
I criteri di accesso di Key Vault devono consentire al provider di risorse di calcolo di accedere ai certificati durante le distribuzioni. Per i passaggi dettagliati, vedere [Configurare Key Vault per le macchine virtuali in Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Di seguito è riportato un esempio di esportazione della chiave pubblica da un certificato di firma, per importarla quindi nel computer.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Una volta pubblicato il contenuto, accodare un tag con il nome `GuestConfigPolicyCertificateValidation` e il valore `enabled` a tutte le macchine virtuali in cui deve essere richiesta la firma del codice. Vedere gli [esempi di tag](../samples/built-in-policies.md#tags) per informazioni sulle modalità di distribuzione dei tag su larga scala usando Criteri di Azure. Una volta che il tag è stato inserito, la definizione dei criteri generata usando il cmdlet `New-GuestConfigurationPolicy` abilita il requisito tramite l'estensione Configurazione guest.

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni sul controllo delle macchine virtuali con [Configurazione guest](../concepts/guest-configuration.md).
- Vedere come [creare criteri a livello di codice](./programmatically-create.md).
- Leggere le informazioni su come [ottenere dati sulla conformità](./get-compliance-data.md).
