---
title: Creare e gestire Azure Cosmos DB tramite PowerShell
description: Usare Azure Powershell per gestire gli account, i database, i contenitori e le unità elaborate di Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/23/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 978f37d08275de704dd01c0251dde42665fca552
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79222098"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Gestire le risorse dell'API SQL di Azure Cosmos DB tramite PowerShell

La guida seguente descrive come usare PowerShell per creare script e automatizzare la gestione delle risorse di Azure Cosmos DB, come account, database, contenitori e unità elaborate. La gestione di Azure Cosmos DB viene eseguita tramite il cmdlet AzResource direttamente nel provider di risorse di Azure Cosmos DB. Per visualizzare tutte le proprietà che è possibile gestire con PowerShell per il provider di risorse di Azure Cosmos DB, vedere [Schema del provider di risorse di Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Per la gestione multipiattaforma dell'account Azure Cosmos DB, è possibile usare l'[interfaccia della riga di comando di Azure](manage-with-cli.md), l'[API REST][rp-rest-api] o il [portale di Azure](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introduzione

Seguire le istruzioni indicate in [Come installare e configurare Azure PowerShell][powershell-install-configure] per installare e accedere all'account Azure in PowerShell.

* Per eseguire i comandi seguenti senza richiedere la conferma dell'utente, aggiungere il flag `-Force` al comando.
* Tutti i comandi seguenti sono sincroni.

## <a name="azure-cosmos-accounts"></a>Account Azure Cosmos

Le sezioni seguenti illustrano come gestire l'account Azure Cosmos:

* [Creare un account Azure Cosmos](#create-account)
* [Aggiornare un account Azure Cosmos](#update-account)
* [Elencare tutti gli account Azure Cosmos in una sottoscrizione](#list-accounts)
* [Ottenere un account Azure Cosmos](#get-account)
* [Eliminare un account Azure Cosmos](#delete-account)
* [Aggiornare i tag per un account Azure Cosmos](#update-tags)
* [Elencare le chiavi per un account Azure Cosmos](#list-keys)
* [Rigenerare le chiavi per un account Azure Cosmos](#regenerate-keys)
* [Elencare le stringhe di connessione per un account Azure Cosmos](#list-connection-strings)
* [Modificare la priorità di failover di un account Azure Cosmos](#modify-failover-priority)
* [Attivare un failover manuale per un account Azure Cosmos](#trigger-manual-failover)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> Creare un account Azure Cosmos

Questo comando crea un account del database Azure Cosmos con il [criterio di coerenza][distribute-data-globally] bounded-staleness in [più aree](consistency-levels.md).

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Il nome dell'account Azure Cosmos. Deve essere composto da un numero di caratteri compreso tra 3 e 31, tutti in minuscolo, inclusi caratteri alfanumerici e il carattere '-'.
* `$location` La posizione della risorsa dell'account Azure Cosmos.
* `$locations` Le aree di replica per l'account del database. È necessaria un'area di scrittura per ogni account del database, con un valore di priorità di failover pari a 0.
* `$consistencyPolicy` Il livello di coerenza predefinito dell'account Azure Cosmos. Per altre informazioni, vedere [Livelli di coerenza in Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` I valori di proprietà passati al provider Azure Resource Manager di Cosmos DB per il provisioning dell'account.

Gli account Azure Cosmos possono essere configurati con il firewall IP oltre che con gli endpoint servizio di rete virtuale. Per informazioni su come configurare il firewall IP per Azure Cosmos DB, vedere [Configurare il firewall IP](how-to-configure-firewall.md).  Per altre informazioni su come abilitare gli endpoint di servizio per Azure Cosmos DB, vedere [Configurare l'accesso da reti virtuali](how-to-configure-vnet-service-endpoint.md).

### <a name="list-all-azure-cosmos-accounts-in-a-subscription"></a><a id="list-accounts"></a> Elencare tutti gli account Azure Cosmos in una sottoscrizione

Questo comando consente di elencare tutti gli account Azure Cosmos in una sottoscrizione.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> Ottenere le proprietà di un account Azure Cosmos

Questo comando consente di ottenere le proprietà di un account Azure Cosmos esistente.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Aggiornare un account Azure Cosmos

Questo comando consente di aggiornare le proprietà di un account del database Azure Cosmos. Le proprietà che possono essere aggiornate includono:

* Aggiunta o rimozione di aree
* Modifica dei criteri di coerenza predefiniti
* Modifica del filtro dell'intervallo IP
* Modifica delle configurazioni di reti virtuali
* Abilitazione del multimaster

> [!NOTE]
> Non è possibile aggiungere o rimuovere simultaneamente aree `locations` e cambiare l'ordine delle proprietà per un account Azure Cosmos. La modifica delle aree deve essere eseguita come operazione distinta rispetto a qualsiasi altra modifica apportata alla risorsa dell'account.
> [!NOTE]
> Questo comando consente di aggiungere e rimuovere aree, ma non di modificare le priorità di failover o attivare un failover manuale. Vedere [Modificare la priorità di failover](#modify-failover-priority) e [Attivare un failover manuale](#trigger-manual-failover).

```azurepowershell-interactive
# Create an account with 2 regions
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$accountName = "mycosmosaccount" # must be lower case and < 31 characters

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false },
    @{ "locationName"="South Central US"; "failoverPriority"=2, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```
### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-master"></a> Abilitare più aree di scrittura per un account Azure Cosmos

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Eliminare un account Azure Cosmos

Questo comando consente di eliminare un account Azure Cosmos esistente.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Aggiornare i tag per un account Azure Cosmos

L'esempio seguente illustra come impostare i [tag delle risorse di Azure][azure-resource-tags] per un account Azure Cosmos.

> [!NOTE]
> Questo comando può essere combinato con i comandi di creazione o aggiornamento aggiungendo il flag `-Tags` con il parametro corrispondente.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> Elencare le chiavi dell'account

Quando si crea un account Azure Cosmos DB, il servizio genera due chiavi di accesso principali che possono essere usate per l'autenticazione quando si accede all'account di Azure Cosmos DB. Generando due chiavi di accesso, Azure Cosmos DB consente di rigenerare le chiavi senza interruzioni dell'account Azure Cosmos DB. Sono disponibili anche chiavi di sola lettura per l'autenticazione delle operazioni di sola lettura. Esistono due chiavi di lettura/scrittura (primaria e secondaria) e due chiavi di sola lettura (primaria e secondaria).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Write-Host "PrimaryKey =" $keys.primaryMasterKey
Write-Host "SecondaryKey =" $keys.secondaryMasterKey
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> Elencare le stringhe di connessione

Per gli account di MongoDB, è possibile recuperare la stringa di connessione per connettere l'app MongoDB all'account del database usando il comando seguente.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> Rigenerare le chiavi dell'account

Le chiavi di accesso a un account Azure Cosmos dovrebbero essere rigenerate periodicamente per mantenere più al sicuro le connessioni. All'account sono assegnate una chiave di accesso primaria e una secondaria. In questo modo i client possono mantenere l'accesso quando una delle chiavi viene rigenerata. Esistono quattro tipi di chiavi per un account Azure Cosmos: Primary, Secondary, PrimaryReadonly e SecondaryReadonly

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> Abilitare il failover automatico

Consente il failover di un account Cosmos nell'area secondaria se l'area primaria diventa non disponibile.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> Modificare la priorità di failover

Per gli account configurati con failover automatico, è possibile cambiare l'ordine in cui Cosmos alzerà di livello le repliche secondarie a primarie nel caso quelle primarie diventassero indisponibili.

Per l'esempio, si supponga la priorità di failover corrente, `West US 2 = 0`, `East US 2 = 1`, `South Central US = 2`.

> [!CAUTION]
> Se si cambia `locationName` per `failoverPriority=0`, verrà attivato un failover manuale per un account Azure Cosmos. Qualsiasi altra modifica della priorità non attiverà un failover.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="South Central US"; "failoverPriority"=1 },
    @{ "locationName"="East US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> Attivare un failover manuale

Per gli account configurati con failover manuale, è possibile eseguire il failover e alzare di livello qualsiasi replica secondaria a primaria modificando `failoverPriority=0`. Questa operazione può essere usata per avviare un'esercitazione sul ripristino di emergenza per testarne la pianificazione.

Per l'esempio seguente, si presuppone che l'account abbia la priorità di failover corrente impostata su `West US 2 = 0` e `East US 2 = 1` e che le aree vengano invertite.

> [!CAUTION]
> Se si cambia `locationName` per `failoverPriority=0`, verrà attivato un failover manuale per un account Azure Cosmos. Qualsiasi altra modifica della priorità non attiverà un failover.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="South Central US"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="West US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Database Azure Cosmos DB

Le sezioni seguenti illustrano come gestire il database Azure Cosmos:

* [Creare un database Azure Cosmos](#create-db)
* [Creare un database Azure Cosmos DB con unità elaborate condivise](#create-db-ru)
* [Ottenere le unità elaborate di un database Azure Cosmos](#get-db-ru)
* [Elencare tutti i database Azure Cosmos in un account](#list-db)
* [Ottenere un singolo database Azure Cosmos](#get-db)
* [Eliminare un database Azure Cosmos](#delete-db)

### <a name="create-an-azure-cosmos-database"></a><a id="create-db"></a>Creare un database Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a name="create-an-azure-cosmos-database-with-shared-throughput"></a><a id="create-db-ru"></a>Creare un database Azure Cosmos DB con unità elaborate condivise

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a name="get-the-throughput-of-an-azure-cosmos-database"></a><a id="get-db-ru"></a>Ottenere le unità elaborate di un database Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a name="get-all-azure-cosmos-databases-in-an-account"></a><a id="list-db"></a>Ottenere tutti i database Azure Cosmos in un account

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a name="get-a-single-azure-cosmos-database"></a><a id="get-db"></a>Ottenere un singolo database Azure Cosmos

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a name="delete-an-azure-cosmos-database"></a><a id="delete-db"></a>Eliminare un database Azure Cosmos

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Contenitore Azure Cosmos

Le sezioni seguenti illustrano come gestire il contenitore Azure Cosmos:

* [Creare un contenitore Azure Cosmos](#create-container)
* [Creare un contenitore Azure Cosmos con una chiave di partizione grande](#create-container-big-pk)
* [Ottenere le unità elaborate di un contenitore Azure Cosmos](#get-container-ru)
* [Creare un contenitore Azure Cosmos con unità elaborate condivise](#create-container-ru)
* [Creare un contenitore Azure Cosmos con indicizzazione personalizzata](#create-container-custom-index)
* [Creare un contenitore Azure Cosmos con indicizzazione disattivata](#create-container-no-index)
* [Creare un contenitore Azure Cosmos con chiave univoca e TTL](#create-container-unique-key-ttl)
* [Creare un contenitore Azure Cosmos con risoluzione dei conflitti](#create-container-lww)
* [Elencare tutti i contenitori Azure Cosmos in un database](#list-containers)
* [Ottenere un singolo contenitore Azure Cosmos in un database](#get-container)
* [Eliminare un contenitore Azure Cosmos](#delete-container)

### <a name="create-an-azure-cosmos-container"></a><a id="create-container"></a>Creare un contenitore Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="create-an-azure-cosmos-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Creare un contenitore Azure Cosmos con una chiave di partizione grande

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="get-the-throughput-of-an-azure-cosmos-container"></a><a id="get-container-ru"></a>Ottenere le unità elaborate di un contenitore Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a name="create-an-azure-cosmos-container-with-shared-throughput"></a><a id="create-container-ru"></a>Creare un contenitore Azure Cosmos con unità elaborate condivise

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a name="create-an-azure-cosmos-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Creare un contenitore Azure Cosmos con un criterio di indicizzazione personalizzato

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="create-an-azure-cosmos-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Creare un contenitore Azure Cosmos con indicizzazione disattivata

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="create-an-azure-cosmos-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Creare un contenitore Azure Cosmos con chiave univoca e TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 86400;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="create-an-azure-cosmos-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Creare un contenitore Azure Cosmos con risoluzione dei conflitti

Per creare un criterio di risoluzione dei conflitti per usare una stored procedure, impostare `"mode"="custom"` e impostare il percorso di risoluzione come nome della stored procedure, `"conflictResolutionPath"="myResolverStoredProcedure"`. Per scrivere tutti i conflitti in ConflictsFeed e gestirli separatamente, impostare `"mode"="custom"` e `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a name="list-all-azure-cosmos-containers-in-a-database"></a><a id="list-containers"></a>Elencare tutti i contenitori Azure Cosmos in un database

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a name="get-a-single-azure-cosmos-container-in-a-database"></a><a id="get-container"></a>Ottenere un singolo contenitore Azure Cosmos in un database

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a name="delete-an-azure-cosmos-container"></a><a id="delete-container"></a>Eliminare un contenitore Azure Cosmos

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Passaggi successivi

* [Tutti gli esempi di PowerShell](powershell-samples.md)
* [Come gestire un account Azure Cosmos](how-to-manage-database-account.md)
* [Creare un contenitore Azure Cosmos](how-to-create-container.md)
* [Configurare la durata (TTL) in Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
