---
title: Provider di risorse e tipi di risorse
description: Vengono descritti i provider di risorse che supportano Azure Resource Manager. Vengono descritti gli schemi, le versioni API disponibili e le aree in cui è possibile ospitare le risorse.
ms.topic: conceptual
ms.date: 12/04/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6d114fdfae12dd9ee96a23e4dafc3847c6429d0c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745117"
---
# <a name="azure-resource-providers-and-types"></a>Provider e tipi di risorse di Azure

Quando si distribuiscono risorse, è spesso necessario recuperare informazioni sui provider e i tipi di risorse. Per archiviare chiavi e segreti sarà ad esempio necessario usare il provider di risorse Microsoft.KeyVault . Questo provider di risorse offre un tipo di risorsa denominato vaults per creare l'insieme di credenziali delle chiavi.

Il nome di un tipo di risorsa è nel formato: **{resource-provider}/{resource-type}**. Il tipo di risorsa per un insieme di credenziali delle chiavi è **Microsoft.KeyVault/vaults**.

In questo articolo vengono illustrate le operazioni seguenti:

* Visualizzare tutti i provider di risorse in Azure
* Controllare lo stato di registrazione di un provider di risorse
* Registrare un provider di risorse
* Visualizzare i tipi di risorse per un provider di risorse
* Visualizzare le località valide per un tipo di risorsa
* Visualizzare le versioni API valide per un tipo di risorsa

Per eseguire questa procedura, è possibile usare l'interfaccia della riga di comando di portale di Azure, Azure PowerShell o Azure.

Per un elenco con il mapping dei provider di risorse ai servizi di Azure, vedere [provider di risorse per i servizi di Azure](azure-services-resource-providers.md).

## <a name="register-resource-provider"></a>Registrare il provider di risorse

Prima di usare un provider di risorse, è necessario che la sottoscrizione di Azure sia registrata per il provider di risorse. La registrazione configura la sottoscrizione per l'uso con il provider di risorse. Alcuni provider di risorse sono registrati per impostazione predefinita. Quando si eseguono determinate azioni, gli altri provider di risorse vengono registrati automaticamente. Ad esempio, quando si crea una risorsa tramite il portale, il provider di risorse viene in genere registrato per l'utente. Per altri scenari, potrebbe essere necessario registrare manualmente un provider di risorse. Per un elenco dei provider di risorse registrati per impostazione predefinita, vedere [provider di risorse per i servizi di Azure](azure-services-resource-providers.md).

Questo articolo illustra come controllare lo stato di registrazione di un provider di risorse e registrarlo in base alle esigenze. È necessario disporre dell'autorizzazione per eseguire l' `/register/action` operazione per il provider di risorse. L'autorizzazione è inclusa nei ruoli collaboratore e proprietario.

> [!IMPORTANT]
> Registrare un provider di risorse solo quando si è pronti a usarlo. Il passaggio di registrazione consente di mantenere i privilegi minimi all'interno della sottoscrizione. Un utente malintenzionato non può usare i provider di risorse che non sono registrati.

Il codice dell'applicazione non deve bloccare la creazione di risorse per un provider di risorse che si trova nello stato di **registrazione** . Quando si registra il provider di risorse, l'operazione viene eseguita singolarmente per ogni area supportata. Per creare risorse in un'area, è necessario completare la registrazione solo in quell'area. Non bloccando il provider di risorse nello stato di registrazione, l'applicazione può continuare molto prima di attendere il completamento di tutte le aree.

Non è possibile annullare la registrazione di un provider di risorse quando i tipi di risorse del provider di risorse sono ancora presenti nella sottoscrizione.

## <a name="azure-portal"></a>Portale di Azure

### <a name="register-resource-provider"></a>Registrare il provider di risorse

Per visualizzare tutti i provider di risorse e lo stato di registrazione della propria sottoscrizione:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel menu portale di Azure cercare **sottoscrizioni**. Selezionare il tag tra le opzioni disponibili.

   :::image type="content" source="./media/resource-providers-and-types/search-subscriptions.png" alt-text="Cerca sottoscrizioni":::

1. Selezionare la sottoscrizione che si vuole visualizzare.

   :::image type="content" source="./media/resource-providers-and-types/select-subscription.png" alt-text="Selezionare le sottoscrizioni":::

1. Nel menu sinistro, in **Impostazioni**, scegliere **Provider di risorse**.

   :::image type="content" source="./media/resource-providers-and-types/select-resource-providers.png" alt-text="Selezionare i provider di risorse":::

6. Trovare il provider di risorse che si vuole registrare e selezionare **Register (registra**). Per mantenere i privilegi minimi nella sottoscrizione, registrare solo i provider di risorse che si è pronti a usare.

   :::image type="content" source="./media/resource-providers-and-types/register-resource-provider.png" alt-text="registrare i provider di risorse":::

### <a name="view-resource-provider"></a>Visualizza provider di risorse

Per visualizzare le informazioni relative uno specifico provider di risorse:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** nel menu del portale di Azure.
3. Nella casella **Tutti i servizi** immettere **esplora risorse** e quindi selezionare **Esplora risorse**.

    ![Selezionare Tutti i servizi](./media/resource-providers-and-types/select-resource-explorer.png)

4. Espandere **Provider** selezionando la freccia rivolta verso destra.

    ![Selezionare i provider](./media/resource-providers-and-types/select-providers.png)

5. Espandere un provider di risorse e un tipo di risorsa da visualizzare.

    ![Selezionare il tipo di risorsa](./media/resource-providers-and-types/select-resource-type.png)

6. Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Potrebbero inoltre essere presenti limitazioni nella sottoscrizione che impediscono l'utilizzo di alcune aree che supportano la risorsa. Resource Explorer visualizza le località valide per il tipo di risorsa.

    ![Visualizzare le località](./media/resource-providers-and-types/show-locations.png)

7. La versione dell'API corrisponde a una versione delle operazioni API REST che vengono rilasciate dal provider di risorse. Poiché un provider di risorse abilita nuove funzionalità, rilascia una nuova versione dell'API REST. Resource Explorer visualizza le versioni API valide per il tipo di risorsa.

    ![Visualizzare le versioni API](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

Per visualizzare tutti i provider di risorse in Azure e lo stato di registrazione di una sottoscrizione, usare il comando seguente:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Che restituisce risultati simili a:

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Per visualizzare tutti i provider di risorse registrati per la sottoscrizione, usare:

```azurepowershell-interactive
 Get-AzResourceProvider -ListAvailable | Where-Object RegistrationState -eq "Registered" | Select-Object ProviderNamespace, RegistrationState | Sort-Object ProviderNamespace
```

Per mantenere i privilegi minimi nella sottoscrizione, registrare solo i provider di risorse che si è pronti a usare. Per registrare un provider di risorse, usare:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Che restituisce risultati simili a:

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

Per visualizzare informazioni su un provider di risorse specifico, usare il comando seguente:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Che restituisce risultati simili a:

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Per visualizzare i tipi di risorse per un provider di risorse, usare il comando seguente:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Che restituisce:

```output
batchAccounts
operations
locations
locations/quotas
```

La versione dell'API corrisponde a una versione delle operazioni API REST che vengono rilasciate dal provider di risorse. Poiché un provider di risorse abilita nuove funzionalità, rilascia una nuova versione dell'API REST.

Per ottenere le versioni dell'API disponibili per un tipo di risorsa, usare il comando seguente:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Che restituisce:

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Potrebbero inoltre essere presenti limitazioni nella sottoscrizione che impediscono l'utilizzo di alcune aree che supportano la risorsa.

Per ottenere le località supportate per un tipo di risorsa, usare il comando seguente:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Che restituisce:

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Per visualizzare tutti i provider di risorse in Azure e lo stato di registrazione di una sottoscrizione, usare il comando seguente:

```azurecli-interactive
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Che restituisce risultati simili a:

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Per visualizzare tutti i provider di risorse registrati per la sottoscrizione, usare:

```azurecli-interactive
az provider list --query "sort_by([?registrationState=='Registered'].{Provider:namespace, Status:registrationState}, &Provider)" --out table
```

Per mantenere i privilegi minimi nella sottoscrizione, registrare solo i provider di risorse che si è pronti a usare. Per registrare un provider di risorse, usare:

```azurecli-interactive
az provider register --namespace Microsoft.Batch
```

Che restituisce un messaggio di registrazione in corso.

Per visualizzare informazioni su un provider di risorse specifico, usare il comando seguente:

```azurecli-interactive
az provider show --namespace Microsoft.Batch
```

Che restituisce risultati simili a:

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Per visualizzare i tipi di risorse per un provider di risorse, usare il comando seguente:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Che restituisce:

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

La versione dell'API corrisponde a una versione delle operazioni API REST che vengono rilasciate dal provider di risorse. Poiché un provider di risorse abilita nuove funzionalità, rilascia una nuova versione dell'API REST.

Per ottenere le versioni dell'API disponibili per un tipo di risorsa, usare il comando seguente:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Che restituisce:

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

Gestione risorse è supportato in tutte le aree, ma le risorse distribuite potrebbero non essere supportate in tutte le aree. Potrebbero inoltre essere presenti limitazioni nella sottoscrizione che impediscono l'utilizzo di alcune aree che supportano la risorsa.

Per ottenere le località supportate per un tipo di risorsa, usare il comando seguente:

```azurecli-interactive
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Che restituisce:

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla creazione di modelli di Gestione risorse, vedere Creazione di modelli di [Azure Resource Manager](../templates/template-syntax.md). 
* Per visualizzare gli schemi dei modelli dei provider di risorse, vedere le [informazioni di riferimento sui modelli](/azure/templates/).
* Per un elenco con il mapping dei provider di risorse ai servizi di Azure, vedere [provider di risorse per i servizi di Azure](azure-services-resource-providers.md).
* Per visualizzare le operazioni di un provider di risorse, vedere [Azure REST API](/rest/api/) (API REST di Azure).
