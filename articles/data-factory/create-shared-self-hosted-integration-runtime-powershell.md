---
title: Creare un runtime di integrazione self-hosted condiviso con PowerShell
description: Informazioni su come creare un runtime di integrazione self-hosted condiviso in Azure Data Factory, in modo che possano accedervi più data factory.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 16feeb124859fa6199303d9d590fa0a286033ef9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389434"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Creare un runtime di integrazione self-hosted condiviso in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questa guida illustra come creare un runtime di integrazione self-hosted condiviso in Azure Data Factory. È quindi possibile usare il runtime di integrazione self-hosted condiviso in un'altra data factory.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Creare un runtime di integrazione self-hosted condiviso in Azure Data Factory

È possibile usare nuovamente un'infrastruttura del runtime di integrazione self-hosted esistente già configurata in una data factory. Questo riuso consente di creare un runtime di integrazione self-hosted collegato in un data factory diverso facendo riferimento a un runtime di integrazione self-hosted condiviso esistente.

Per visualizzare un'introduzione e una dimostrazione di questa funzionalità, guardare il video di 12 minuti seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **IR condiviso**: un runtime di integrazione self-hosted originale che viene eseguito in un'infrastruttura fisica.  
- **IR collegato**: un IR che fa riferimento a un altro IR condiviso. Il runtime di integrazione collegato è un IR logico e usa l'infrastruttura di un altro runtime di integrazione self-hosted condiviso.

## <a name="create-a-shared-self-hosted-ir-using-azure-data-factory-ui"></a>Creare un runtime di integrazione self-hosted condiviso usando l'interfaccia utente di Azure Data Factory

Per creare un runtime di integrazione self-hosted condiviso usando l'interfaccia utente di Azure Data Factory, è possibile seguire questa procedura:

1. Nel runtime di integrazione self-hosted da condividere selezionare **Concedi autorizzazione a un'altra data factory** e nella pagina "installazione di Integration Runtime" selezionare la data factory in cui si vuole creare il runtime di integrazione collegato.
      
    ![Pulsante per concedere l'autorizzazione nella scheda di condivisione](media/create-self-hosted-integration-runtime/grant-permissions-IR-sharing.png)  
    
2. Si noti e si copia il precedente "ID risorsa" del runtime di integrazione self-hosted da condividere.
         
3. Nella data factory per cui sono state concesse le autorizzazioni creare un nuovo runtime di integrazione self-hosted (collegato) e immettere l'ID risorsa.
      
    ![Pulsante per la creazione di un runtime di integrazione self-hosted](media/create-self-hosted-integration-runtime/create-linkedir-1.png)
   
    ![Pulsante per la creazione di un runtime di integrazione self-hosted collegato](media/create-self-hosted-integration-runtime/create-linkedir-2.png) 

    ![Caselle per nome e ID risorsa](media/create-self-hosted-integration-runtime/create-linkedir-3.png)

## <a name="create-a-shared-self-hosted-ir-using-azure-powershell"></a>Creare un runtime di integrazione self-hosted condiviso usando Azure PowerShell

Per creare un runtime di integrazione self-hosted condiviso usando Azure PowerShell, è possibile seguire questa procedura: 
1. Creare una data factory. 
1. Creare un runtime di integrazione self-hosted.
1. Condividere il runtime di integrazione self-hosted con altre data factory.
1. Creare un runtime di integrazione collegato.
1. Revocare la condivisione.

### <a name="prerequisites"></a>Prerequisiti 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Sottoscrizione di Azure**. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

- **Azure PowerShell**. Seguire le istruzioni fornite in [Installare Azure PowerShell in Windows con PowerShellGet](/powershell/azure/install-az-ps). Usare PowerShell per eseguire uno script per creare un runtime di integrazione self-hosted che può essere condiviso con altre data factory. 

> [!NOTE]  
> Per un elenco delle aree di Azure in cui Data Factory è attualmente disponibile, selezionare le aree di interesse in [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory).

### <a name="create-a-data-factory"></a>Creare una data factory

1. Avviare Windows PowerShell Integrated Scripting Environment (ISE).

1. Creare le variabili. Copiare e incollare lo script seguente. Sostituire le variabili, ad esempio **SubscriptionName** e **ResourceGroupName**, con gli effettivi valori: 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. Eseguire l'accesso e selezionare una sottoscrizione. Aggiungere il codice seguente allo script per eseguire l'accesso e selezionare la sottoscrizione di Azure:

    ```powershell
    Connect-AzAccount
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

1. Creare un gruppo di risorse e una data factory.

    > [!NOTE]  
    > Questo passaggio è facoltativo. Se è già presente una data factory, ignorare questo passaggio. 

    Creare un [Gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite come gruppo. L'esempio seguente crea un gruppo di risorse denominato `myResourceGroup` nell'area Europa occidentale: 

    ```powershell
    New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Eseguire questo comando per creare una data factory: 

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

### <a name="create-a-self-hosted-integration-runtime"></a>Creare un runtime di integrazione self-hosted

> [!NOTE]  
> Questo passaggio è facoltativo. Se è già presente un runtime di integrazione self-hosted che si vuole condividere con altre data factory, ignorare questo passaggio.

Eseguire questo comando per creare un runtime di integrazione self-hosted:

```powershell
$SharedIR = Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

#### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>Ottenere la chiave di autenticazione per il runtime di integrazione e registrare un nodo

Eseguire questo comando per ottenere la chiave di autenticazione per il runtime di integrazione self-hosted:

```powershell
Get-AzDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

La risposta contiene la chiave di autenticazione per il runtime di integrazione self-hosted. Questa chiave viene usata al momento della registrazione del nodo del runtime di integrazione.

#### <a name="install-and-register-the-self-hosted-integration-runtime"></a>Installare e registrare il runtime di integrazione self-hosted

1. Scaricare il programma di installazione del runtime di integrazione self-hosted da [Azure Data Factory Integration Runtime](https://aka.ms/dmg).

2. Eseguire il programma scaricato per installare l'integrazione self-hosted in un computer locale.

3. Registrare la nuova integrazione self-hosted con la chiave di autenticazione ottenuta in uno dei passaggi precedenti.

### <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>Condividere il runtime di integrazione self-hosted con un'altra data factory

#### <a name="create-another-data-factory"></a>Creare un'altra data factory

> [!NOTE]  
> Questo passaggio è facoltativo. Se è già presente la data factory per la condivisione, ignorare questo passaggio. Tuttavia, per aggiungere o rimuovere assegnazioni di ruolo ad altri data factory, è necessario disporre `Microsoft.Authorization/roleAssignments/write` `Microsoft.Authorization/roleAssignments/delete` delle autorizzazioni e, ad esempio [amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator) o [proprietario](../role-based-access-control/built-in-roles.md#owner).

```powershell
$factory = Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
#### <a name="grant-permission"></a>Concedere un'autorizzazione

Concedere l'autorizzazione alla data factory che deve accedere al runtime di integrazione self-hosted creato e registrato.

> [!IMPORTANT]  
> Non ignorare questo passaggio.

```powershell
New-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

### <a name="create-a-linked-self-hosted-integration-runtime"></a>Creare un runtime di integrazione self-hosted collegato

Eseguire questo comando per creare un runtime di integrazione self-hosted collegato:

```powershell
Set-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

È ora possibile usare questo runtime di integrazione collegato in qualsiasi servizio collegato. Per eseguire le attività, il runtime di integrazione collegato usa il runtime di integrazione condiviso.

### <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>Revocare la condivisione del runtime di integrazione da una data factory

Per revocare l'accesso di una data factory al runtime di integrazione condiviso, è possibile eseguire il comando seguente:

```powershell
Remove-AzRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionName 'Contributor' `
    -Scope $SharedIR.Id
```

Per rimuovere il runtime di integrazione collegato esistente, è possibile eseguire il comando seguente sul runtime di integrazione condiviso:

```powershell
Remove-AzDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

### <a name="monitoring"></a>Monitoraggio

#### <a name="shared-ir"></a>IR condiviso

![Selezioni per trovare un runtime di integrazione condiviso](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Monitorare un runtime di integrazione condivisa](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>IR collegato

![Selezioni per trovare un runtime di integrazione collegato](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Monitorare un runtime di integrazione collegato](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)


### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitazioni note della condivisione del runtime di integrazione self-hosted

* Il data factory in cui viene creato un runtime di integrazione collegato deve avere un' [identità gestita](../active-directory/managed-identities-azure-resources/overview.md). Per impostazione predefinita, le data factory create nel portale di Azure o i cmdlet di PowerShell hanno un'identità gestita creata in modo implicito. Tuttavia, quando viene creata una data factory tramite un modello di Azure Resource Manager o un SDK, è necessario impostare la proprietà **Identity** in modo esplicito. Questa impostazione garantisce che Gestione risorse crei una data factory contenente un'identità gestita.

* Il Data Factory .NET SDK che supporta questa funzionalità deve essere la versione 1.1.0 o successiva.

* Per concedere l'autorizzazione, è necessario il ruolo proprietario o il ruolo proprietario ereditato nel data factory in cui è presente il runtime di integrazione condiviso.

* La funzionalità di condivisione funziona solo per le data factory nello stesso tenant Azure AD.

* Per Azure AD [utenti Guest](../active-directory/governance/manage-guest-access-with-access-reviews.md), la funzionalità di ricerca nell'interfaccia utente, che elenca tutte le data factory usando una parola chiave di ricerca, non funziona. Tuttavia, finché l'utente Guest è il proprietario del data factory, è possibile condividere il runtime di integrazione senza la funzionalità di ricerca. Per l'identità gestita del data factory che deve condividere il runtime di integrazione, immettere l'identità gestita nella casella **assegnazione autorizzazione** e selezionare **Aggiungi** nell'interfaccia utente di data factory.

  > [!NOTE]
  > Questa funzionalità è disponibile solo in Data Factory V2.


### <a name="next-steps"></a>Passaggi successivi

- Vedere [Concetti relativi al runtime di integrazione in Azure Data Factory](./concepts-integration-runtime.md).

- Informazioni su come [creare un runtime di integrazione self-hosted nel portale di Azure](./create-self-hosted-integration-runtime.md).