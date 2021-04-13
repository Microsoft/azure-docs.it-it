---
title: Usare gli script di distribuzione nei modelli | Microsoft Docs
description: usare gli script di distribuzione nei modelli di Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: jgao
ms.openlocfilehash: 3240cce34a6fa645986a58ab43b28ad38485e97b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308966"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Usare gli script di distribuzione nei modelli ARM

Informazioni su come usare gli script di distribuzione nei modelli di risorse di Azure (modelli ARM). Con un nuovo tipo di risorsa denominato `Microsoft.Resources/deploymentScripts` , gli utenti possono eseguire script nelle distribuzioni di modelli ed esaminare i risultati dell'esecuzione. Questi script possono essere usati per eseguire passaggi personalizzati, ad esempio:

- Aggiungere utenti a una directory
- Eseguire operazioni sul piano dati, ad esempio, copiare i BLOB o il database di inizializzazione
- Cercare e convalidare un codice di licenza
- Creare un certificato autofirmato
- Creare un oggetto in Azure AD
- Cercare blocchi di indirizzi IP dal sistema personalizzato

I vantaggi dello script di distribuzione:

- Semplifica la scrittura di codice, l'uso e l'esecuzione del debug. È possibile sviluppare script di distribuzione negli ambienti di sviluppo preferiti. Gli script possono essere incorporati in modelli o in file di script esterni.
- È possibile specificare la piattaforma e il linguaggio dello script. Attualmente gli script di distribuzione di Azure PowerShell e dell'interfaccia della riga di comando di Azure nell'ambiente Linux sono supportati.
- Consente di specificare gli argomenti della riga di comando da passare allo script.
- Può specificare gli output dello script e passarli nuovamente alla distribuzione.

La risorsa script di distribuzione è disponibile solo nelle aree in cui è disponibile l'istanza di contenitore di Azure.  Vedere [Disponibilità di risorse per Istanze di Azure Container nelle aree di Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Per l'esecuzione e la risoluzione dei problemi degli script sono necessari un account di archiviazione e un'istanza di contenitore. Sono disponibili le opzioni per specificare un account di archiviazione esistente. In caso contrario, l'account di archiviazione e l'istanza di contenitore vengono creati automaticamente dal servizio script. Le due risorse create automaticamente vengono in genere eliminate dal servizio script quando l'esecuzione dello script di distribuzione raggiunge uno stato finale. Le risorse verranno addebitate fino a quando non vengono eliminate. Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).

> [!IMPORTANT]
> L'API della risorsa deploymentScripts versione 2020-10-01 supporta [OnBehalfofTokens (OBO)](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md). Con OBO, il servizio script di distribuzione usa il token dell'entità di distribuzione per creare le risorse sottostanti per l'esecuzione di script di distribuzione, tra cui l'istanza di contenitore di Azure, l'account di archiviazione di Azure e le assegnazioni di ruolo per l'identità gestita. Nella versione precedente dell'API, l'identità gestita viene usata per creare queste risorse.
> La logica di ripetizione dei tentativi per l'accesso ad Azure ora è incorporata nello script wrapper. Se si concedono le autorizzazioni nello stesso modello in cui si eseguono gli script di distribuzione. Il servizio script di distribuzione esegue nuovi tentativi di accesso per 10 minuti con intervallo di 10 secondi fino a quando non viene replicata l'assegnazione di ruolo identità gestita.

## <a name="configure-the-minimum-permissions"></a>Configurare le autorizzazioni minime

Per l'API dello script di distribuzione versione 2020-10-01 o successiva, l'entità di distribuzione viene usata per creare le risorse sottostanti necessarie per l'esecuzione della risorsa dello script di distribuzione, ovvero un account di archiviazione e un'istanza di contenitore di Azure. Se lo script deve eseguire l'autenticazione in Azure ed eseguire azioni specifiche di Azure, è consigliabile fornire lo script con un'identità gestita assegnata dall'utente. L'identità gestita deve avere l'accesso necessario per completare l'operazione nello script.

Per configurare le autorizzazioni con privilegi minimi, è necessario:

- Assegnare un ruolo personalizzato con le proprietà seguenti all'entità di distribuzione:

  ```json
  {
    "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
    "description": "Configure least privilege for the deployment principal in deployment script",
    "type": "customRole",
    "IsCustom": true,
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/*",
          "Microsoft.ContainerInstance/containerGroups/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/deploymentScripts/*"
        ],
      }
    ],
    "assignableScopes": [
      "[subscription().id]"
    ]
  }
  ```

  Se l'archiviazione di Azure e i provider di risorse dell'istanza di contenitore di Azure non sono stati registrati, è necessario anche aggiungere `Microsoft.Storage/register/action` e `Microsoft.ContainerInstance/register/action` .

- Se viene usata un'identità gestita, l'entità di distribuzione deve avere il ruolo di **operatore di identità gestito** , ovvero un ruolo predefinito, assegnato alla risorsa di identità gestita.

## <a name="sample-templates"></a>Modelli di esempio

Il codice JSON seguente è un esempio. Per ulteriori informazioni, vedere lo [schema del modello](/azure/templates/microsoft.resources/deploymentscripts)più recente.

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> L'esempio è a scopo dimostrativo. Le proprietà `scriptContent` e `primaryScriptUri` non possono coesistere in un modello.

> [!NOTE]
> _ScriptContent_ Mostra uno script con più righe.  Il portale di Azure e la pipeline di Azure DevOps non possono analizzare uno script di distribuzione con più righe. È possibile concatenare i comandi di PowerShell (usando punti e virgola o _\\ r \\ n_ o _\\ n_) in una sola riga o usare la `primaryScriptUri` proprietà con un file di script esterno. Sono disponibili molti strumenti gratuiti di escape e di escape per le stringhe JSON. ad esempio [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html).

Dettagli sui valori delle proprietà:

- `identity`: Per l'API dello script di distribuzione versione 2020-10-01 o successiva, un'identità gestita assegnata dall'utente è facoltativa, a meno che non sia necessario eseguire azioni specifiche di Azure nello script.  Per la versione API 2019-10-01-Preview, è necessaria un'identità gestita perché il servizio script di distribuzione lo usa per eseguire gli script. Attualmente è supportata solo l'Identità gestita assegnata dall'utente di Azure.
- `kind`: specificare il tipo di script. Attualmente sono supportati gli script Azure PowerShell e dell'interfaccia della riga di comando di Azure. I valori sono **AzurePowerShell** e **AzureCLI**.
- `forceUpdateTag`: La modifica di questo valore tra le distribuzioni di modelli forza la ripetizione dell'esecuzione dello script di distribuzione. Se si usano le `newGuid()` funzioni o `utcNow()` , entrambe le funzioni possono essere usate solo nel valore predefinito per un parametro. Per altre informazioni, vedere [Eseguire lo script più di una volta](#run-script-more-than-once).
- `containerSettings`: Specificare le impostazioni per personalizzare l'istanza di contenitore di Azure. Lo script di distribuzione richiede una nuova istanza di contenitore di Azure. Non è possibile specificare un'istanza di contenitore di Azure esistente. Tuttavia, è possibile personalizzare il nome del gruppo di contenitori usando `containerGroupName` . Se non specificato, il nome del gruppo viene generato automaticamente.
- `storageAccountSettings`: Specificare le impostazioni per l'uso di un account di archiviazione esistente. Se `storageAccountName` viene omesso, viene creato automaticamente un account di archiviazione. Vedere [Usare un account di archiviazione esistente](#use-existing-storage-account).
- `azPowerShellVersion`/`azCliVersion`: Specificare la versione del modulo da usare. Vedere un elenco di [versioni di Azure PowerShell supportate](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Vedere un elenco delle [versioni supportate dell'interfaccia](https://mcr.microsoft.com/v2/azure-cli/tags/list)della riga di comando di Azure.

  >[!IMPORTANT]
  > Lo script di distribuzione usa le immagini CLI disponibili di Microsoft Container Registry. La certificazione di un'immagine dell'interfaccia della riga di comando per uno script di distribuzione richiede circa un mese. Non usare le versioni dell'interfaccia della riga di comando rilasciate negli ultimi 30 giorni. Per trovare le date di rilascio delle immagini, vedere [Note sulla versione dell'interfaccia della riga di comando di Azure](/cli/azure/release-notes-azure-cli). Se viene utilizzata una versione non supportata, nel messaggio di errore vengono elencate le versioni supportate.

- `arguments`: Specificare i valori del parametro. I valori sono separati da uno spazio.

  Gli script di distribuzione suddividono gli argomenti in una matrice di stringhe richiamando la chiamata di sistema [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) . Questo passaggio è necessario perché gli argomenti vengono passati come [proprietà del comando](/rest/api/container-instances/containergroups/createorupdate#containerexec) a un'istanza di contenitore di Azure e la proprietà Command è una matrice di stringa.

  Se gli argomenti contengono caratteri di escape, usare [JsonEscaper](https://www.jsonescaper.com/) per eseguire il doppio escape dei caratteri. Incollare la stringa di escape originale nello strumento, quindi selezionare **escape**.  Lo strumento restituisce una doppia stringa con caratteri di escape. Nel modello di esempio precedente, ad esempio, l'argomento è `-name \"John Dole\"` . La stringa di escape è `-name \\\"John Dole\\\"` .

  Per passare un parametro di modello ARM di tipo Object come argomento, convertire l'oggetto in una stringa usando la funzione [String ()](./template-functions-string.md#string) e quindi usare la funzione [Replace ()](./template-functions-string.md#replace) per sostituire Any `\"` in `\\\"` . Ad esempio:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  Per ulteriori informazioni, vedere il [modello di esempio](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- `environmentVariables`: Specificare le variabili di ambiente da passare allo script. Per altre informazioni, vedere [Sviluppare script di distribuzione](#develop-deployment-scripts).
- `scriptContent`: specificare il contenuto dello script. Per eseguire uno script esterno, usare `primaryScriptUri`. Per esempi, vedere [Usare script inline](#use-inline-scripts) e [Usare script esterni](#use-external-scripts).
- `primaryScriptUri`: Specificare un URL accessibile pubblicamente per lo script di distribuzione primario con le estensioni di file supportate. Per altre informazioni, vedere [use External scripts](#use-external-scripts).
- `supportingScriptUris`: Specificare una matrice di URL accessibili pubblicamente per supportare i file che vengono chiamati in `scriptContent` o `primaryScriptUri` . Per altre informazioni, vedere [use External scripts](#use-external-scripts).
- `timeout`: specificare il tempo di esecuzione dello script massimo consentito nel [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Il valore predefinito è **P1D**.
- `cleanupPreference`. Specificare la preferenza per la pulizia delle risorse di distribuzione quando l'esecuzione dello script si trova in uno stato terminale. L'impostazione predefinita è **Sempre**, che indica l'eliminazione delle risorse nonostante lo stato del terminale (Riuscito, Non riuscito, Annullato). Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).
- `retentionInterval`: Specificare l'intervallo per cui il servizio mantiene le risorse dello script di distribuzione dopo che l'esecuzione dello script di distribuzione raggiunge uno stato terminale. Le risorse dello script di distribuzione verranno eliminate alla scadenza di tale durata. La durata è basata sul [modello ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). L'intervallo di conservazione è compreso tra 1 e 26 ore (PT26H). Questa proprietà viene usata quando l'opzione `cleanupPreference` è impostata su **OnExpiration**. Per altre informazioni, vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Altri esempi

- [Esempio 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): creare un insieme di credenziali delle chiavi e usare lo script di distribuzione per assegnare un certificato all'insieme di credenziali delle chiavi.
- [Esempio 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): creare un gruppo di risorse a livello di sottoscrizione, creare un insieme di credenziali delle chiavi nel gruppo di risorse e quindi usare lo script di distribuzione per assegnare un certificato all'insieme di credenziali delle chiavi.
- [Esempio 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): creare un'identità gestita assegnata dall'utente, assegnare il ruolo Collaboratore all'identità a livello di gruppo di risorse, creare un insieme di credenziali delle chiavi e usare lo script di distribuzione per assegnare un certificato all'insieme di credenziali delle chiavi.

## <a name="use-inline-scripts"></a>Usare script inline

Il modello seguente include una risorsa definita con il tipo `Microsoft.Resources/deploymentScripts`. La parte evidenziata è lo script inline.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Poiché gli script di distribuzione inline sono racchiusi tra virgolette doppie, le stringhe all'interno degli script di distribuzione devono essere precedute da una barra rovesciata (**&#92;**) o racchiuse tra virgolette singole. È anche possibile prendere in considerazione l'uso della sostituzione di stringhe come illustrato nell'esempio JSON precedente.

Lo script richiede un parametro e genera l'output del valore del parametro. `DeploymentScriptOutputs` viene usato per archiviare gli output. Nella sezione Outputs (output) la `value` riga Mostra come accedere ai valori archiviati. `Write-Output` è usato a scopo di debug. Per informazioni su come accedere al file di output, vedere [monitorare e risolvere i problemi degli script di distribuzione](#monitor-and-troubleshoot-deployment-scripts). Per le descrizioni delle proprietà, vedere [Modelli di esempio](#sample-templates).

Per eseguire lo script, selezionare **Prova** per aprire Cloud Shell e quindi incollare il codice seguente nel riquadro della shell.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

L'output è simile al seguente:

![Output dello script di distribuzione del modello di Resource Manager hello world](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Usare script esterni

Oltre agli script inline, è anche possibile usare file di script esterni. Sono supportati solo gli script di PowerShell primari con l'estensione di file _ps1_. Per gli script dell'interfaccia della riga di comando, gli script primari possono avere estensioni (o senza estensione), purché gli script siano script Bash validi. Per usare file di script esterni, sostituire `scriptContent` con `primaryScriptUri`. Ad esempio:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Per ulteriori informazioni, vedere il [modello di esempio](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

I file di script esterni devono essere accessibili. Per proteggere i file di script archiviati negli account di archiviazione di Azure, generare un token di firma di accesso condiviso e includerlo nell'URI del modello. Impostare l'ora di scadenza in modo da garantire un tempo sufficiente per completare la distribuzione. Per altre informazioni, vedere [distribuire un modello ARM privato con token SAS](./secure-template-with-sas-token.md).

L'utente è responsabile di garantire l'integrità degli script a cui viene fatto riferimento dallo script di distribuzione, `primaryScriptUri` o `supportingScriptUris` . Fare riferimento solo a script attendibili.

## <a name="use-supporting-scripts"></a>Usare script di supporto

È possibile separare le logiche complesse in uno o più file di script di supporto. Se necessario, la proprietà `supportingScriptUris` consente di fornire una matrice di URI ai file di script di supporto:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

I file script di supporto possono essere chiamati sia da script inline che da file di script primari. I file di script di supporto non prevedono alcuna restrizione sull'estensione di file.

I file di supporto vengono copiati in `azscripts/azscriptinput` in fase di esecuzione. Usare il percorso relativo per fare riferimento ai file di supporto da script inline e file di script primari.

## <a name="work-with-outputs-from-powershell-script"></a>Usare gli output dello script di PowerShell

Il modello seguente mostra come passare i valori tra due `deploymentScripts` risorse:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

Nella prima risorsa si definisce una variabile denominata, che `$DeploymentScriptOutputs` viene usata per archiviare i valori di output. Per accedere al valore di output da un'altra risorsa all'interno del modello, usare:

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>Usare gli output dello script dell'interfaccia della riga di comando

Diverso dallo script di distribuzione di PowerShell, il supporto CLI/bash non espone una variabile comune per archiviare gli output di script, ma è presente una variabile di ambiente denominata `AZ_SCRIPTS_OUTPUT_PATH` che archivia il percorso in cui risiede il file di output dello script. Se uno script di distribuzione viene eseguito da un modello di Resource Manager, questa variabile di ambiente viene impostata automaticamente dalla shell Bash.

Gli output dello script di distribuzione devono essere salvati nel `AZ_SCRIPTS_OUTPUT_PATH` percorso e gli output devono essere un oggetto stringa JSON valido. Il contenuto del file deve essere salvato come coppia chiave-valore. Una matrice di stringhe, ad esempio, viene archiviata come `{ "MyResult": [ "foo", "bar"] }` .  Archiviare solo i risultati della matrice, ad esempio `[ "foo", "bar" ]` , non è valido.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

Nell'esempio precedente viene usato [jq](https://stedolan.github.io/jq/). Viene fornito con le immagini del contenitore. Vedere [Configurare l'ambiente di sviluppo](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Usa account di archiviazione esistente

Per l'esecuzione e la risoluzione dei problemi degli script sono necessari un account di archiviazione e un'istanza di contenitore. Sono disponibili le opzioni per specificare un account di archiviazione esistente. In caso contrario, l'account di archiviazione e l'istanza di contenitore vengono creati automaticamente dal servizio script. Requisiti per l'uso di un account di archiviazione esistente:

- I tipi di account di archiviazione supportati sono:

    | SKU             | Tipo supportato     |
    |-----------------|--------------------|
    | Premium_LRS     | FileStorage        |
    | Premium_ZRS     | FileStorage        |
    | Standard_GRS    | Archiviazione, archiviazione V2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Archiviazione, archiviazione V2 |
    | Standard_RAGRS  | Archiviazione, archiviazione V2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Queste combinazioni supportano le condivisioni file. Per altre informazioni, vedere [creare una condivisione file di Azure](../../storage/files/storage-how-to-create-file-share.md) e [i tipi di account di archiviazione](../../storage/common/storage-account-overview.md).

- Le regole del firewall dell'account di archiviazione non sono ancora supportate. Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../../storage/common/storage-network-security.md).
- L'entità di distribuzione deve avere le autorizzazioni per gestire l'account di archiviazione, che include le condivisioni file di lettura, creazione ed eliminazione.

Per specificare un account di archiviazione esistente, aggiungere il codice JSON seguente all'elemento Property di `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: specificare il nome dell'account di archiviazione.
- `storageAccountKey`: specificare una delle chiavi dell'account di archiviazione. Per recuperare la chiave, è possibile usare la funzione [listKeys ()](./template-functions-resource.md#listkeys) . Ad esempio:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Vedere [Modelli di esempio](#sample-templates) per un esempio di definizione di `Microsoft.Resources/deploymentScripts` completo.

Quando viene usato un account di archiviazione esistente, il servizio script crea una condivisione file con un nome univoco. Vedere [Pulire le risorse dello script di distribuzione](#clean-up-deployment-script-resources) per informazioni su come il servizio script pulisce la condivisione file.

## <a name="develop-deployment-scripts"></a>Sviluppare script di distribuzione

### <a name="handle-non-terminating-errors"></a>Gestire errori non fatali

È possibile controllare il modo in cui PowerShell risponde agli errori non fatali usando la `$ErrorActionPreference` variabile nello script di distribuzione. Se la variabile non è impostata nello script di distribuzione, il servizio script utilizzerà il valore predefinito **continue (continua**).

Il servizio script imposta lo stato di provisioning delle risorse su **non riuscito** quando lo script rileva un errore nonostante l'impostazione di `$ErrorActionPreference` .

### <a name="pass-secured-strings-to-deployment-script"></a>Passare stringhe protette allo script di distribuzione

L'impostazione delle variabili di ambiente (EnvironmentVariable) nelle istanze di contenitore consente di offrire la configurazione dinamica dell'applicazione o dello script eseguiti dal contenitore. Lo script di distribuzione gestisce variabili di ambiente non protette e protette in modo analogo all'istanza di contenitore di Azure. Per altre informazioni, vedere [Impostare variabili di ambiente in istanze di contenitore](../../container-instances/container-instances-environment-variables.md#secure-values). Per un esempio, vedere [modelli di esempio](#sample-templates).

La dimensione massima consentita per le variabili di ambiente è 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Monitorare e risolvere i problemi degli script di distribuzione

Il servizio script crea un [account di archiviazione](../../storage/common/storage-account-overview.md) (a meno che non si specifichi un account di archiviazione esistente) e un'[istanza di contenitore](../../container-instances/container-instances-overview.md) per l'esecuzione dello script. Se queste risorse vengono create automaticamente dal servizio script, entrambe le risorse hanno il `azscripts` suffisso nei nomi delle risorse.

![Nomi delle risorse dello script di distribuzione del modello di Resource Manager](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Lo script utente, i risultati dell'esecuzione e il file stdout vengono archiviati nelle condivisioni file dell'account di archiviazione. È presente una cartella denominata `azscripts` . Nella cartella sono disponibili altre due cartelle per i file di input e di output: `azscriptinput` e `azscriptoutput` .

La cartella di output contiene un file _executionresult.json_ e il file di output dello script. È possibile visualizzare il messaggio di errore dell'esecuzione dello script in _executionresult.json_. Il file di output viene creato solo quando lo script viene eseguito correttamente. La cartella di input contiene un file di script di sistema di PowerShell e i file di script di distribuzione dell'utente. È possibile sostituire il file di script di distribuzione dell'utente con uno modificato ed eseguire nuovamente lo script di distribuzione dall'istanza di contenitore di Azure.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Dopo aver distribuito una risorsa di script di distribuzione, la risorsa viene elencata sotto il gruppo di risorse nella portale di Azure. La schermata seguente mostra la pagina **Panoramica** di una risorsa script di distribuzione:

![Panoramica del portale di script di distribuzione del modello di Gestione risorse](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

La pagina Panoramica Visualizza alcune informazioni importanti della risorsa, ad esempio **stato di provisioning**, **account di archiviazione**, istanza del **contenitore** e **log**.

Dal menu a sinistra è possibile visualizzare il contenuto dello script di distribuzione, gli argomenti passati allo script e l'output. È anche possibile esportare un modello per lo script di distribuzione, incluso lo script di distribuzione.

### <a name="use-powershell"></a>Usare PowerShell

Utilizzando Azure PowerShell, è possibile gestire gli script di distribuzione nell'ambito della sottoscrizione o del gruppo di risorse:

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): Ottiene o elenca gli script di distribuzione.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): Ottiene il log di un'esecuzione dello script di distribuzione.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): rimuove uno script di distribuzione e le risorse associate.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): Salva il log di un'esecuzione dello script di distribuzione su disco.

L' `Get-AzDeploymentScript` output è simile al seguente:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure

Usando l'interfaccia della riga di comando di Azure, è possibile gestire gli script di distribuzione nell'ambito della sottoscrizione o del gruppo

- [AZ Deployment-scripts Delete](/cli/azure/deployment-scripts#az-deployment-scripts-delete): eliminare uno script di distribuzione.
- [AZ Deployment-scripts list](/cli/azure/deployment-scripts#az-deployment-scripts-list): elenca tutti gli script di distribuzione.
- [AZ Deployment-scripts show](/cli/azure/deployment-scripts#az-deployment-scripts-show): recuperare uno script di distribuzione.
- [AZ Deployment-scripts show-log](/cli/azure/deployment-scripts#az-deployment-scripts-show-log): Mostra i log di script di distribuzione.

L'output del comando list è simile al seguente:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>Usare l'API REST

È possibile ottenere le informazioni sulla distribuzione delle risorse dello script di distribuzione a livello di gruppo di risorse e a livello di sottoscrizione usando l'API REST:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

Nell'esempio seguente viene usato [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

L'output è simile a:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

L'API REST seguente restituisce il log:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

Funziona solo prima che vengano eliminate le risorse dello script di distribuzione.

Per visualizzare la risorsa deploymentScripts nel portale, selezionare **Mostra tipi nascosti**:

![Script di distribuzione del modello di Resource Manager, mostra tipi nascosti, portale](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Pulire le risorse dello script di distribuzione

Per l'esecuzione e la risoluzione dei problemi degli script sono necessari un account di archiviazione e un'istanza di contenitore. Sono disponibili opzioni per specificare un account di archiviazione esistente. In caso contrario, l'account di archiviazione e l'istanza di contenitore vengono creati automaticamente dal servizio script. Le due risorse create automaticamente vengono eliminate dal servizio script quando l'esecuzione dello script di distribuzione raggiunge uno stato finale. Le risorse verranno addebitate fino a quando non vengono eliminate. Per informazioni sui prezzi, vedere [Prezzi di Istanze di Container](https://azure.microsoft.com/pricing/details/container-instances/) e [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).

Il ciclo di vita di queste risorse è controllato dalle proprietà seguenti nel modello:

- `cleanupPreference`: Pulisce la preferenza quando l'esecuzione dello script si trova in uno stato terminale. I valori supportati sono:

  - **Sempre**: Eliminare le risorse create automaticamente una volta che l'esecuzione dello script si trova in uno stato terminale. Se viene usato un account di archiviazione esistente, il servizio script elimina la condivisione file creata nell'account di archiviazione. Poiché la `deploymentScripts` risorsa può essere ancora presente dopo la pulizia delle risorse, il servizio script rende persistenti i risultati dell'esecuzione dello script, ad esempio stdout, gli output e il valore restituito prima che le risorse vengano eliminate.
  - **OnSuccess**: Eliminare le risorse create automaticamente solo quando l'esecuzione dello script ha esito positivo. Se viene usato un account di archiviazione esistente, il servizio script rimuove la condivisione file solo quando l'esecuzione dello script ha esito positivo. È comunque possibile accedere alle risorse per trovare le informazioni di debug.
  - **Onexpireation**: eliminare le risorse create automaticamente solo quando l' `retentionInterval` impostazione è scaduta. Se viene usato un account di archiviazione esistente, il servizio script rimuove la condivisione file, ma mantiene l'account di archiviazione.

- `retentionInterval`: Specificare l'intervallo di tempo durante il quale verrà mantenuta una risorsa di script e dopo la scadenza e l'eliminazione.

> [!NOTE]
> Non è consigliabile usare l'account di archiviazione e l'istanza di contenitore generati dal servizio script per altri scopi. Le due risorse potrebbero essere rimosse a seconda del ciclo di vita dello script.

L'istanza del contenitore e l'account di archiviazione vengono eliminati in base a `cleanupPreference` . Tuttavia, se lo script non riesce e `cleanupPreference` non è impostato su **Always**, il processo di distribuzione mantiene automaticamente il contenitore in esecuzione per un'ora. Questa ora può essere usata per risolvere i problemi relativi allo script. Se si desidera lasciare il contenitore in esecuzione dopo le distribuzioni riuscite, aggiungere un passaggio di sospensione allo script. Ad esempio, aggiungere [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) alla fine dello script. Se non si aggiunge il passaggio di sospensione, il contenitore viene impostato su uno stato terminale e non è possibile accedervi anche se non è ancora stato eliminato.

## <a name="run-script-more-than-once"></a>Eseguire lo script più di una volta

L'esecuzione dello script di distribuzione è un'operazione idempotente. Se nessuna delle `deploymentScripts` proprietà della risorsa (incluso lo script inline) viene modificata, lo script non viene eseguito quando si ridistribuisce il modello. Il servizio script di distribuzione confronta i nomi delle risorse nel modello con le risorse esistenti nello stesso gruppo di risorse. Se si vuole eseguire lo stesso script di distribuzione più volte, sono disponibili due opzioni:

- Modificare il nome della `deploymentScripts` risorsa. Usare, ad esempio, la funzione di modello [utcNow](./template-functions-date.md#utcnow) come nome della risorsa o come parte del nome della risorsa. Se si modifica il nome della risorsa, viene creata una nuova `deploymentScripts` risorsa. È opportuno mantenere una cronologia dell'esecuzione dello script.

    > [!NOTE]
    > La `utcNow` funzione può essere usata solo nel valore predefinito per un parametro.

- Specificare un valore diverso nella proprietà del modello `forceUpdateTag`. Ad esempio, usare `utcNow` come valore.

> [!NOTE]
> Scrivere script di distribuzione idempotenti. In questo modo si garantisce che, in caso di esecuzione accidentale, non provocheranno modifiche al sistema. Ad esempio, se si usa lo script di distribuzione per creare una risorsa di Azure, verificare che la risorsa non esista prima di crearla, in modo che lo script abbia esito positivo o la risorsa non venga creata nuovamente.

## <a name="configure-development-environment"></a>Configurare l'ambiente di sviluppo

È possibile usare un'immagine contenitore preconfigurata come ambiente di sviluppo dello script di distribuzione. Per altre informazioni, vedere [configurare l'ambiente di sviluppo per gli script di distribuzione nei modelli](./deployment-script-template-configure-dev.md).

Una volta testato correttamente lo script, è possibile utilizzarlo come script di distribuzione nei modelli.

## <a name="deployment-script-error-codes"></a>Codici di errore dello script di distribuzione

| Codice di errore | Descrizione |
|------------|-------------|
| DeploymentScriptInvalidOperation | La definizione di risorsa dello script di distribuzione nel modello contiene nomi di proprietà non validi. |
| DeploymentScriptResourceConflict | Non è possibile eliminare una risorsa dello script di distribuzione che si trova nello stato non terminale e l'esecuzione non ha superato 1 ora. In alternativa, non è possibile eseguire di nuovo lo stesso script di distribuzione con lo stesso identificatore di risorsa (stessa sottoscrizione, nome del gruppo di risorse e nome della risorsa), ma contenuto del corpo dello script diverso nello stesso momento. |
| DeploymentScriptOperationFailed | Operazione script di distribuzione non riuscita internamente. Contattare il supporto tecnico Microsoft. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | La chiave di accesso non è stata specificata per l'account di archiviazione esistente.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Un gruppo di contenitori creato dal servizio script di distribuzione è stato modificato esternamente e sono stati aggiunti contenitori non validi. |
| DeploymentScriptContainerGroupInNonterminalState | Due o più risorse dello script di distribuzione usano lo stesso nome dell'istanza di contenitore di Azure nello stesso gruppo di risorse e una di esse non ha ancora completato l'esecuzione. |
| DeploymentScriptStorageAccountInvalidKind | L'account di archiviazione esistente del tipo BlobBlobStorage o BlobStorage non supporta le condivisioni file e non può essere usato. |
| DeploymentScriptStorageAccountInvalidKindAndSku | L'account di archiviazione esistente non supporta le condivisioni file. Per un elenco dei tipi di account di archiviazione supportati, vedere [usare l'account di archiviazione esistente](#use-existing-storage-account). |
| DeploymentScriptStorageAccountNotFound | L'account di archiviazione non esiste o è stato eliminato da un processo o da uno strumento esterno. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | L'account di archiviazione specificato dispone di un endpoint del servizio. Un account di archiviazione con un endpoint di servizio non è supportato. |
| DeploymentScriptStorageAccountInvalidAccessKey | Chiave di accesso non valida specificata per l'account di archiviazione esistente. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Formato della chiave dell'account di archiviazione non valido. Vedere [gestire le chiavi di accesso dell'account di archiviazione](../../storage/common/storage-account-keys-manage.md). |
| DeploymentScriptExceededMaxAllowedTime | Il tempo di esecuzione dello script di distribuzione ha superato il valore di timeout specificato nella definizione della risorsa dello script di distribuzione. |
| DeploymentScriptInvalidOutputs | L'output dello script di distribuzione non è un oggetto JSON valido. |
| DeploymentScriptContainerInstancesServiceLoginFailure | L'identità gestita assegnata dall'utente non è stata in grado di eseguire l'accesso dopo 10 tentativi con intervallo di 1 minuto. |
| DeploymentScriptContainerGroupNotFound | Un gruppo di contenitori creato dal servizio script di distribuzione è stato eliminato da uno strumento o processo esterno. |
| DeploymentScriptDownloadFailure | Non è stato possibile scaricare uno script di supporto. Vedere [usare uno script di supporto](#use-supporting-scripts).|
| DeploymentScriptError | Lo script dell'utente ha generato un errore. |
| DeploymentScriptBootstrapScriptExecutionFailed | Lo script di bootstrap ha generato un errore. Lo script bootstrap è lo script di sistema che orchestra l'esecuzione dello script di distribuzione. |
| DeploymentScriptExecutionFailed | Errore sconosciuto durante l'esecuzione dello script di distribuzione. |
| DeploymentScriptContainerInstancesServiceUnavailable | Quando si crea l'istanza di contenitore di Azure (ACI), ACI ha generato un errore di servizio non disponibile. |
| DeploymentScriptContainerGroupInNonterminalState | Quando si crea l'istanza di contenitore di Azure (ACI), un altro script di distribuzione usa lo stesso nome ACI nello stesso ambito, ovvero la stessa sottoscrizione, il nome del gruppo di risorse e il nome della risorsa. |
| DeploymentScriptContainerGroupNameInvalid | Il nome dell'istanza di contenitore di Azure (ACI) specificato non soddisfa i requisiti di ACI. Vedere [risolvere i problemi comuni nelle istanze di contenitore di Azure](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come usare gli script di distribuzione. Per esaminare un'esercitazione relativa a uno script di distribuzione:

> [!div class="nextstepaction"]
> [Esercitazione: Usare gli script di distribuzione nei modelli di Azure Resource Manager](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Modulo Learn: estendere i modelli ARM usando gli script di distribuzione](/learn/modules/extend-resource-manager-template-deployment-scripts/)
