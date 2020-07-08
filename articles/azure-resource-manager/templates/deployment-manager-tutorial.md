---
title: Distribuire modelli con Azure Deployment Manager
description: Informazioni su come usare modelli di Resource Manager con Azure Deployment Manager per distribuire risorse di Azure.
author: mumian
ms.date: 12/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 43291bdaa277c06262be2d7bb5ba8d3f61ff35ea
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056889"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Esercitazione: Usare Azure Deployment Manager con modelli di Resource Manager (anteprima pubblica)

Questo articolo illustra come usare [Azure Deployment Manager](./deployment-manager-overview.md) per distribuire le applicazioni in più aree. Se si preferisce un approccio più veloce, l'[avvio rapido di Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart) crea le configurazioni necessarie nella sottoscrizione e consente di personalizzare gli elementi per distribuire un'applicazione in più aree. L'avvio rapido esegue le stesse attività, come accade in questa esercitazione.

Per usare Deployment Manager è necessario creare due modelli.

* **Modello di topologia**: descrive le risorse di Azure che costituiscono l'applicazione e la posizione in cui devono essere distribuite.
* **Modello di implementazione**: descrive i passaggi da eseguire durante la distribuzione delle applicazioni.

> [!IMPORTANT]
> Se la sottoscrizione è contrassegnata in modo che Canary testi nuove funzionalità di Azure, è possibile usare Azure Deployment Manager solo per distribuire le aree di Canary. 

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Informazioni sullo scenario
> * Scaricare i file per l'esercitazione
> * Preparare gli artefatti
> * Creare l'identità gestita definita dall'utente
> * Creare il modello di topologia del servizio
> * Creare il modello di implementazione
> * Distribuire i modelli
> * Verificare la distribuzione
> * Distribuire la versione più recente
> * Pulire le risorse

Risorse aggiuntive:

* Il [riferimento all'API REST di Azure Deployment Manager](/rest/api/deploymentmanager/).
* [Esercitazione: Usare il controllo integrità in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Esperienza nello sviluppo di [modelli di Azure Resource Manager](overview.md).
* Azure PowerShell. Per altre informazioni, vedere [Get started with Azure PowerShell](/powershell/azure/get-started-azureps) (Introduzione ad Azure PowerShell).
* Cmdlet di Deployment Manager. Per installare questi cmdlet in versione non definitiva, è necessaria l'ultima versione di PowerShellGet. Per ottenere l'ultima versione, vedere [Installazione di PowerShellGet](/powershell/scripting/gallery/installing-psget). Al termine dell'installazione di PowerShellGet, chiudere la finestra di PowerShell. Aprire una nuova finestra di PowerShell con privilegi elevati e usare il comando seguente:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>Informazioni sullo scenario

Il modello di topologia del servizio descrive le risorse di Azure che costituiscono il servizio e la posizione in cui devono essere distribuite. La definizione della topologia del servizio presenta la gerarchia seguente:

* Topologia del servizio
  * Servizi
    * Unità di servizio

Il diagramma seguente illustra la topologia del servizio usata in questa esercitazione:

![Esercitazione su Azure Deployment Manager - Diagramma dello scenario](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Sono presenti due servizi allocati nelle località Stati Uniti occidentali e Stati Uniti orientali.  Ogni servizio include due unità di servizio: un'applicazione Web front-end e un account di archiviazione per il back-end. Le definizioni delle unità di servizio contengono collegamenti ai file del modello e dei parametri per creare le applicazioni Web e gli account di archiviazione.

## <a name="download-the-tutorial-files"></a>Scaricare i file per l'esercitazione

1. Scaricare [i modelli e gli artefatti](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) usati in questa esercitazione.
2. Decomprimere i file nel computer locale in uso.

Nella cartella radice sono presenti due cartelle:

* **ADMTemplates** contiene i modelli di Deployment Manager, che includono:
  * CreateADMServiceTopology.json
  * CreateADMServiceTopology.Parameters.json
  * CreateADMRollout.json
  * CreateADMRollout.Parameters.json
* **ArtifactStore** contiene sia gli artefatti modello che gli artefatti binari. Vedere [Preparare gli artefatti](#prepare-the-artifacts).

Si noti che sono presenti due set di modelli.  Un set include i modelli di Deployment Manager usati per distribuire la topologia del servizio e l'implementazione, mentre l'altro set viene chiamato dalle unità di servizio per creare i servizi Web e gli account di archiviazione.

## <a name="prepare-the-artifacts"></a>Preparare gli artefatti

La cartella ArtifactStore inclusa nel download contiene due cartelle:

![Esercitazione su Azure Deployment Manager - Diagramma dell'origine degli artefatti](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* La cartella **templates** contiene gli artefatti modello. **1.0.0.0** e **1.0.0.1** rappresentano le due versioni degli artefatti binari. In ogni versione è presente una cartella per ogni servizio (corrispondente a Stati Uniti orientali e Stati Uniti occidentali). Ogni servizio include una coppia di file del modello e dei parametri per creare un account di archiviazione e un'altra coppia per creare un'applicazione Web. Il modello dell'applicazione Web chiama un pacchetto compresso contenente i file dell'applicazione Web. Il file compresso è un artefatto binario archiviato nella cartella binaries.
* La cartella **binaries** contiene gli artefatti binari. **1.0.0.0** e **1.0.0.1** rappresentano le due versioni degli artefatti binari. In ogni versione è incluso un file ZIP per creare l'applicazione Web nella località Stati Uniti occidentali e un altro file ZIP per creare l'applicazione Web nella località Stati Uniti orientali.

Le due versioni (1.0.0.0 e 1.0.0.1) vengono usate per la [distribuzione della revisione](#deploy-the-revision). Nonostante esistano due versioni sia degli artefatti modello che degli artefatti binari, solo gli artefatti binari presentano differenze tra le due versioni. Nella pratica, gli artefatti binari vengono aggiornati con maggiore frequenza rispetto agli artefatti modello.

1. Aprire **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json** in un editor di testo. È un modello di base per la creazione di un account di archiviazione.
2. Aprire **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**.

    ![Esercitazione su Azure Deployment Manager - Modello per la creazione dell'applicazione Web](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Il modello chiama un pacchetto di distribuzione contenente i file dell'applicazione Web. In questa esercitazione il pacchetto compresso contiene solo un file index.html.
3. Aprire **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**.

    ![Esercitazione su Azure Deployment Manager - Parametri del modello per la creazione dell'applicazione Web: containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Il valore di deployPackageUri è il percorso del pacchetto di distribuzione. Il parametro contiene una variabile **$containerRoot**. Il valore di $containerRoot viene specificato nel [modello di implementazione](#create-the-rollout-template) concatenando il percorso di firma di accesso condiviso dell'origine degli artefatti, la radice degli artefatti e deployPackageUri.
4. Aprire **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    Il codice HTML visualizza informazioni sulla località e la versione. Il file binario nella cartella 1.0.0.1 mostra "Version 1.0.0.1". Dopo aver distribuito il servizio, si può passare a queste pagine.
5. Esaminare gli altri file di artefatto, per comprendere meglio lo scenario.

Gli artefatti modello vengono usati dal modello di topologia del servizio, mentre gli artefatti binari vengono usati dal modello di implementazione. Sia il modello di topologia che il modello di implementazione definiscono una risorsa di Azure usata come origine degli artefatti, ossia per associare Resource Manager agli artefatti modello e binari usati nella distribuzione. Per semplificare l'esercitazione, viene usato un unico account di archiviazione per archiviare sia gli artefatti modello che gli artefatti binari. Entrambe le origini degli artefatti puntano allo stesso account di archiviazione.

Eseguire lo script di PowerShell seguente per creare un gruppo di risorse, creare un contenitore di archiviazione, creare un contenitore BLOB, caricare i file scaricati e quindi creare un token di firma di accesso condiviso.

> [!IMPORTANT]
> **projectName** nello script di PowerShell viene usato per generare nomi per i servizi di Azure che vengono distribuiti in questa esercitazione. I diversi servizi di Azure hanno diversi requisiti sui nomi. Per garantire la riuscita della distribuzione, scegliere un nome che contenga meno di 12 caratteri, con solo lettere minuscole e numeri.
> Salvare una copia del nome del progetto. Usare lo stesso projectName per tutta l'esercitazione.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

Creare una copia dell'URL con il token di firma di accesso condiviso. Questo URL è necessario per popolare un campo nei due file dei parametri, ovvero il file dei parametri della topologia e il file dei parametri dell'implementazione.

Aprire il contenitore dal portale di Azure e verificare che i file e le cartelle **binaries** e **templates** siano caricati.

## <a name="create-the-user-assigned-managed-identity"></a>Creare l'identità gestita assegnata dall'utente

Più avanti nell'esercitazione si distribuisce un'implementazione. Pe eseguire le azioni di distribuzione, ad esempio per distribuire le applicazioni Web e l'account di archiviazione, è necessaria un'identità gestita assegnata dall'utente. A questa identità devono essere concessi l'accesso alla sottoscrizione di Azure in cui si distribuisce il servizio e autorizzazioni sufficienti per completare la distribuzione degli artefatti.

È necessario creare un'identità gestita assegnata dall'utente e configurare il controllo di accesso per la sottoscrizione.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Creare un'[identità gestita assegnata dall'utente](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. Nel portale selezionare **Sottoscrizioni** nel menu a sinistra e quindi la sottoscrizione.
4. Selezionare **Controllo di accesso (IAM)** e quindi **Aggiungi assegnazione di ruolo**.
5. Immettere o selezionare i valori seguenti:

    ![Esercitazione su Azure Deployment Manager - Controllo di accesso con identità gestita assegnata dall'utente](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Ruolo**: assegnare autorizzazioni sufficienti per completare la distribuzione degli artefatti (applicazioni Web e account di archiviazione). In questa esercitazione selezionare **Collaboratore**. Nella pratica è opportuno limitare le autorizzazioni al minimo.
    * **Assegna accesso a**: selezionare **Identità gestita assegnata dall'utente**.
    * Selezionare l'identità gestita assegnata dall'utente creata in precedenza nell'esercitazione.
6. Selezionare **Salva**.

## <a name="create-the-service-topology-template"></a>Creare il modello di topologia del servizio

Aprire **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>Parametri

Il modello contiene i parametri seguenti:

* **projectName**: questo nome viene usato per creare i nomi delle risorse di Deployment Manager. Se si usa "jdoe", ad esempio, il nome della topologia del servizio sarà **jdoe**ServiceTopology.  I nomi delle risorse vengono definiti nella sezione variables di questo modello.
* **azureResourcelocation**: per semplificare l'esercitazione, tutte le risorse condividono questa località a meno che non venga specificato diversamente.
* **artifactSourceSASLocation**: URI di firma di accesso condiviso del contenitore BLOB in cui vengono archiviati i file del modello e dei parametri delle unità di servizio per la distribuzione.  Vedere [Preparare gli artefatti](#prepare-the-artifacts).
* **templateArtifactRoot**: percorso di offset del contenitore BLOB in cui sono archiviati i modelli e i parametri. Il valore predefinito è **templates/1.0.0.0**. Non modificare questo valore a meno che non si voglia modificare la struttura di cartelle illustrata in [Preparare gli artefatti](#prepare-the-artifacts). In questa esercitazione vengono usati percorsi relativi.  Il percorso completo viene costruito concatenando **artifactSourceSASLocation**, **templateArtifactRoot** e **templateArtifactSourceRelativePath** (o **parametersArtifactSourceRelativePath**).
* **targetSubscriptionID**: ID sottoscrizione in cui verranno distribuite e fatturate le risorse di Deployment Manager. In questa esercitazione usare il proprio ID sottoscrizione.

### <a name="the-variables"></a>Variabili

La sezione variables definisce i nomi delle risorse, le località di Azure per i due servizi **Service WUS** e **Service EUS** e i percorsi degli artefatti:

![Esercitazione su Azure Deployment Manager - Variabili del modello di topologia](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Confrontare i percorsi degli artefatti con la struttura di cartelle caricata nell'account di archiviazione. Si noti che i percorsi degli artefatti sono percorsi relativi. Il percorso completo viene costruito concatenando **artifactSourceSASLocation**, **templateArtifactRoot** e **templateArtifactSourceRelativePath** (o **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>Risorse

A livello di radice sono definite due risorse: un'*origine degli artefatti* e una *topologia del servizio*.

La definizione dell'origine degli artefatti è la seguente:

![Esercitazione su Azure Deployment Manager - Risorse nel modello di topologia: origine degli artefatti](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Lo screenshot seguente mostra solo alcune parti delle definizioni della topologia del servizio, dei servizi e delle unità di servizio:

![Esercitazione su Azure Deployment Manager - Risorse nel modello di topologia: topologia del servizio](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId**: viene usato per associare la risorsa origine degli artefatti alla risorsa topologia del servizio.
* **dependsOn**: tutte le risorse della topologia del servizio dipendono dalla risorsa origine degli artefatti.
* **artifacts**: punta agli artefatti modello.  Vengono usati percorsi relativi. Il percorso completo viene costruito concatenando i valori di artifactSourceSASLocation e artifactRoot (definiti nell'origine degli artefatti) e templateArtifactSourceRelativePath (o parametersArtifactSourceRelativePath).

### <a name="topology-parameters-file"></a>File dei parametri della topologia

Creare un file dei parametri da usare con il modello di topologia.

1. Aprire **\ADMTemplates\CreateADMServiceTopology.Parameters** in Visual Studio Code o in qualsiasi editor di testo.
2. Specificare i valori dei parametri.

    * **projectName**: immettere una stringa di 4-5 caratteri. Questo nome viene usato per creare nomi univoci per le risorse di Azure.
    * **azureResourceLocation**: se non si ha familiarità con le località di Azure, in questa esercitazione usare **centralus**.
    * **artifactSourceSASLocation**: immettere l'URI di firma di accesso condiviso della directory radice (contenitore BLOB) in cui vengono archiviati i file del modello e dei parametri delle unità di servizio per la distribuzione.  Vedere [Preparare gli artefatti](#prepare-the-artifacts).
    * **templateArtifactRoot**: a meno che non si modifichi la struttura di cartelle degli artefatti, in questa esercitazione usare **templates/1.0.0.0**.

> [!IMPORTANT]
> Il modello di topologia e il modello di implementazione condividono alcuni parametri comuni, che devono avere gli stessi valori. Tali parametri sono **projectName**, **azureResourceLocation** e **artifactSourceSASLocation** (in questa esercitazione, entrambe le origini degli artefatti condividono lo stesso account di archiviazione).

## <a name="create-the-rollout-template"></a>Creare il modello di implementazione

Aprire **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>Parametri

Il modello contiene i parametri seguenti:

![Esercitazione su Azure Deployment Manager - Parametri del modello di implementazione](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **projectName**: questo nome viene usato per creare i nomi delle risorse di Deployment Manager. Se si usa "jdoe", ad esempio, il nome dell'implementazione sarà **jdoe**Rollout.  I nomi vengono definiti nella sezione variables del modello.
* **azureResourcelocation**: per semplificare l'esercitazione, tutte le risorse di Deployment Manager condividono questa località a meno che non venga specificato diversamente.
* **artifactSourceSASLocation**: URI di firma di accesso condiviso della directory radice (contenitore BLOB) in cui vengono archiviati i file del modello e dei parametri delle unità di servizio per la distribuzione.  Vedere [Preparare gli artefatti](#prepare-the-artifacts).
* **binaryArtifactRoot**:  il valore predefinito è **binaries/1.0.0.0**. Non modificare questo valore a meno che non si voglia modificare la struttura di cartelle illustrata in [Preparare gli artefatti](#prepare-the-artifacts). In questa esercitazione vengono usati percorsi relativi.  Il percorso completo viene costruito concatenando **artifactSourceSASLocation**, **binaryArtifactRoot** e il valore di **deployPackageUri** specificato in CreateWebApplicationParameters.json.  Vedere [Preparare gli artefatti](#prepare-the-artifacts).
* **managedIdentityID**: identità gestita assegnata dall'utente che esegue le azioni di distribuzione. Vedere [Creare l'identità gestita assegnata dall'utente](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Variabili

La sezione variables definisce i nomi delle risorse. Verificare che il nome della topologia del servizio, i nomi dei servizi e quelli delle unità di servizio corrispondano ai nomi definiti nel [modello di topologia](#create-the-service-topology-template).

![Esercitazione su Azure Deployment Manager - Variabili del modello di implementazione](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Risorse

A livello di radice sono definite tre risorse: un'origine degli artefatti, un passaggio e un'implementazione.

La definizione dell'origine degli artefatti è identica a quella presente nel modello di topologia.  Per altre informazioni, vedere [Creare il modello di topologia del servizio](#create-the-service-topology-template).

Lo screenshot seguente mostra la definizione del passaggio di attesa:

![Esercitazione su Azure Deployment Manager - Risorse del modello di implementazione: passaggio di attesa](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Per la durata viene usato lo [standard ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (con lettere maiuscole obbligatorie) è un esempio di un'attesa di 1 minuto.

Lo screenshot seguente mostra solo alcune parti della definizione dell'implementazione:

![Esercitazione su Azure Deployment Manager - Risorse del modello di implementazione: implementazione](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**: la risorsa implementazione dipende dalla risorsa origine degli artefatti e dagli eventuali passaggi definiti.
* **artifactSourceId**: viene usato per associare la risorsa origine degli artefatti alla risorsa implementazione.
* **targetServiceTopologyId**: viene usato per associare la risorsa topologia del servizio alla risorsa implementazione.
* **deploymentTargetId**: è l'ID risorsa dell'unità di servizio della risorsa topologia del servizio.
* **preDeploymentSteps** e **postDeploymentSteps**: contengono i passaggi dell'implementazione. Nel modello viene chiamato un passaggio di attesa.
* **dependsOnStepGroups**: configurare le dipendenze tra i gruppi di passaggi.

### <a name="rollout-parameters-file"></a>File dei parametri dell'implementazione

Creare un file dei parametri da usare con il modello di implementazione.

1. Aprire **\ADMTemplates\CreateADMRollout.Parameters** in Visual Studio Code o in qualsiasi editor di testo.
2. Specificare i valori dei parametri.

    * **projectName**: immettere una stringa di 4-5 caratteri. Questo nome viene usato per creare nomi univoci per le risorse di Azure.
    * **azureResourceLocation**: specificare una località di Azure.
    * **artifactSourceSASLocation**: immettere l'URI di firma di accesso condiviso della directory radice (contenitore BLOB) in cui vengono archiviati i file del modello e dei parametri delle unità di servizio per la distribuzione.  Vedere [Preparare gli artefatti](#prepare-the-artifacts).
    * **binaryArtifactRoot**: a meno che non si modifichi la struttura di cartelle degli artefatti, in questa esercitazione usare **binaries/1.0.0.0**.
    * **managedIdentityID**: immettere l'identità gestita assegnata dall'utente. Vedere [Creare l'identità gestita assegnata dall'utente](#create-the-user-assigned-managed-identity). La sintassi è:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Il modello di topologia e il modello di implementazione condividono alcuni parametri comuni, che devono avere gli stessi valori. Tali parametri sono **projectName**, **azureResourceLocation** e **artifactSourceSASLocation** (in questa esercitazione, entrambe le origini degli artefatti condividono lo stesso account di archiviazione).

## <a name="deploy-the-templates"></a>Distribuire i modelli

Per distribuire i modelli è possibile usare Azure PowerShell.

1. Eseguire lo script per distribuire la topologia del servizio.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    Se si esegue questo script da una sessione di PowerShell diversa rispetto a quella in cui è stato eseguito lo script [Preparare gli artefatti](#prepare-the-artifacts), è necessario ripopolare prima le variabili, che includono **$resourceGroupName** e **$filePath**.

    > [!NOTE]
    > `New-AzResourceGroupDeployment` è una chiamata asincrona. Il messaggio di operazione riuscita indica solo che la distribuzione è stata avviata correttamente. Per verificare la distribuzione, vedere i passaggi 2 e 4 di questa procedura.

2. Verificare che la creazione della topologia del servizio e delle risorse sottolineate sia stata completata usando il portale di Azure:

    ![Esercitazione su Azure Deployment Manager - Risorse della topologia del servizio distribuite](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Per visualizzare le risorse deve essere selezionata l'opzione **Mostra tipi nascosti**.

3. <a id="deploy-the-rollout-template"></a>Distribuire il modello di implementazione:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Controllare lo stato di avanzamento dell'implementazione con lo script PowerShell seguente:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Per poter eseguire questo cmdlet, è prima di tutto necessario che siano installati i cmdlet PowerShell di Deployment Manager. Vedere Prerequisiti. L'opzione -Verbose può essere usata per visualizzare l'output completo.

    L'esempio seguente mostra lo stato di esecuzione:

    ```
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Al termine della distribuzione dell'implementazione, verranno visualizzati altri due gruppi di risorse che sono stati creati, uno per ogni servizio.

## <a name="verify-the-deployment"></a>Verificare la distribuzione

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Passare alle nuove applicazioni Web nei gruppi di risorse creati dalla distribuzione dell'implementazione.
3. Aprire l'applicazione Web in un Web browser. Verificare la località e la versione nel file index.html.

## <a name="deploy-the-revision"></a>Distribuire la revisione

Quando è disponibile una nuova versione (1.0.0.1), è possibile ridistribuire l'applicazione Web usando la procedura seguente.

1. Aprire CreateADMRollout.Parameters.json.
2. Aggiornare **binaryArtifactRoot** in **binaries/1.0.0.1**.
3. Ridistribuire l'implementazione come illustrato in [Distribuire i modelli](#deploy-the-rollout-template).
4. Verificare la distribuzione come illustrato in [Verificare la distribuzione](#verify-the-deployment). Nella pagina Web verrà visualizzata la versione 1.0.0.1.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Usare il campo **Filtra per nome** per limitare l'elenco ai gruppi di risorse creati in questa esercitazione, che saranno 3-4:

    * **&lt;projectName>rg**, che contiene le risorse di Deployment Manager.
    * **&lt;projectName>ServiceWUSrg**, che contiene le risorse definite da ServiceWUS.
    * **&lt;projectName>ServiceEUSrg**, che contiene le risorse definite da ServiceEUS.
    * Gruppo di risorse per l'identità gestita definita dall'utente.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.
5. Ripetere gli ultimi due passaggi per eliminare gli altri gruppi di risorse creati con questa esercitazione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come usare Azure Deployment Manager. Per integrare il monitoraggio dell'integrità in Azure Deployment Manager, vedere [Esercitazione: Usare il controllo integrità in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
