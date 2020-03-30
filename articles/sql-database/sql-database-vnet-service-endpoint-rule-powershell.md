---
title: PowerShell per gli endpoint della rete virtuale e le regole per i database singoli e in pool
description: Fornisce script di PowerShell per creare e gestire gli endpoint del servizio virtuale per il database SQL di Azure e lo synapse di Azure.Provides PowerShell scripts to create and manage Virtual Service endpoints for your Azure SQL Database and Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, vanto
ms.date: 03/12/2019
tags: azure-synapse
ms.openlocfilehash: 1e8ec394eab1df0aebe394b8acebc74c7ed49e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124693"
---
# <a name="powershell--create-a-virtual-service-endpoint-and-vnet-rule-for-sql"></a>PowerShell: Creare un endpoint del servizio virtuale e una regola di rete virtuale per SQL

*Le regole* di rete virtuale sono una funzionalità di sicurezza del firewall che controlla se il server di database per i singoli database e il pool elastico nel [database SQL](sql-database-technical-overview.md) di Azure o per i database in [Azure Synapse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) accetta le comunicazioni inviate da determinate subnet nelle reti virtuali.

> [!IMPORTANT]
> Questo articolo si applica al server SQL di Azure e sia al database SQL che al data warehouse in Azure Synapse creati nel server SQL di Azure.This article applies to Azure SQL server, and both SQL Database and data warehouse in Azure Synapse that are created on the Azure SQL server. Per semplicità, il database SQL viene usato quando si fa riferimento sia al database SQL che allo synapsi di Azure.For simplicity, SQL Database is used when referring to both SQL Database and Azure Synapse. Le informazioni di questo articolo *non* sono valide per la distribuzione di un'**istanza gestita** nel database SQL di Azure perché non ha un endpoint di servizio associato.

Questo articolo fornisce e descrive uno script di PowerShell che esegue le azioni seguenti:

1. Creare un *endpoint del servizio virtuale* di Microsoft Azure nella subnet.
2. Aggiungere l'endpoint al firewall del server di database SQL di Azure per creare una *regola di rete virtuale*.

Le ragioni per la creazione di una regola sono descritte in [Virtual Service endpoints for Azure SQL Database][sql-db-vnet-service-endpoint-rule-overview-735r] (Endpoint del servizio virtuale per il database SQL di Azure).

> [!TIP]
> Se si necessita semplicemente di valutare o aggiungere l'endpoint del servizio virtuale *nome del tipo* per il database SQL alla subnet, è possibile passare direttamente a uno [script di PowerShell più diretto](#a-verify-subnet-is-endpoint-ps-100).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Il modulo di PowerShell Azure Resource Manager è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Per questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono sostanzialmente identici.

## <a name="major-cmdlets"></a>Cmdlet principali

In questo articolo viene sottolineato il cmdlet **New-AzSqlServerVirtualNetworkRule** che aggiunge l'endpoint di subnet all'elenco di controllo di accesso (ACL) del server di database SQL di Azure, creando in tal modo una regola.

Nell'elenco seguente viene illustrata la sequenza di altri cmdlet *principali* che è necessario eseguire per preparare la chiamata a **New-AzSqlServerVirtualNetworkRule**. In questo articolo queste chiamate si verificano nello [Script 3 "Regola di rete virtuale"](#a-script-30):

1. [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig): Crea un oggetto subnet.
2. [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork): Crea la rete virtuale, assegnandole la subnet.
3. [Set-AzVirtualNetworkSubnetConfig:](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetworkSubnetConfig)assegna un endpoint del servizio virtuale alla subnet.
4. [Set-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/Set-azVirtualNetwork)rende persistenti gli aggiornamenti apportati alla rete virtuale.
5. [New-AzSqlServerVirtualNetworkRule:](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlservervirtualnetworkrule)dopo che la subnet è un endpoint, aggiunge la subnet come regola di rete virtuale all'ACL del server di database SQL di Azure.New-AzSqlServerVirtualNetworkRule : After your subnet is an endpoint, adds your subnet as a virtual network rule, in the ACL of your Azure SQL Database server.
   - Questo cmdlet offre il parametro **-IgnoreMissingVNetServiceEndpoint**, a partire dal modulo PowerShell di Azure RM versione 5.1.1.

## <a name="prerequisites-for-running-powershell"></a>Prerequisiti per l'esecuzione di PowerShell

- È già possibile accedere ad Azure, ad esempio tramite il [portale di Azure][http-azure-portal-link-ref-477t].
- È già possibile eseguire script di PowerShell.

> [!NOTE]
> Verificare che gli endpoint di servizio siano attivati per la rete virtuale o la subnet che si vuole aggiungere al server, altrimenti la creazione della regola del firewall della rete virtuale avrà esito negativo.

## <a name="one-script-divided-into-four-chunks"></a>Uno script suddiviso in quattro blocchi

Lo script di PowerShell di dimostrazione è suddiviso in una sequenza di script di dimensioni minori. Questa divisione semplifica l'apprendimento e assicura flessibilità. Gli script devono essere eseguiti nella sequenza indicata. Se ora non si ha tempo sufficiente per eseguire gli script, l'output del test effettivo è visualizzato dopo lo script 4.

<a name="a-script-10" />

### <a name="script-1-variables"></a>Script 1: variabili

Questo primo script di PowerShell assegna valori alle variabili. Gli script successivi dipendono da queste variabili.

> [!IMPORTANT]
> Prima di eseguire questo script, è possibile modificare i valori, se necessario. Ad esempio, se si dispone già di un gruppo di risorse, è consigliabile modificarne il nome in base al valore assegnato.
>
> Il nome della sottoscrizione deve essere modificato nello script.

### <a name="powershell-script-1-source-code"></a>Codice sorgente dello script 1 di PowerShell

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.
$SubscriptionName = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```

<a name="a-script-20" />

### <a name="script-2-prerequisites"></a>Script 2: prerequisiti

Questo script è di preparazione per lo script successivo, in cui si verifica l'azione dell'endpoint. Lo script crea automaticamente gli elementi elencati di seguito, solo se ancora non esistono. Se si è certi dell'esistenza di questi elementi, è possibile ignorare lo script 2:

- Gruppo di risorse di Azure
- Server di database SQL di Azure

### <a name="powershell-script-2-source-code"></a>Codice sorgente dello script 2 di PowerShell

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer) {
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";
    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials) {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else {
    Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; 
}

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```

<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Script 3: creare un endpoint e una regola

Questo script crea una rete virtuale con una subnet, quindi assegna il tipo di endpoint **Microsoft.Sql** alla subnet. Infine, lo script aggiunge la subnet all'elenco di controllo di accesso (ACL) del server di database SQL, creando una regola.

### <a name="powershell-script-3-source-code"></a>Codice sorgente dello script 3 di PowerShell

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Network-Rule".';
```

<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Script 4: pulizia

Questo script finale elimina le risorse create per la dimostrazione dagli script precedenti. Tuttavia, lo script chiede conferma prima di eliminare le risorse seguenti:

- Server di database SQL di Azure
- Gruppo di risorse di Azure

È possibile eseguire lo script 4 in qualsiasi momento dopo il completamento dello script 1.

### <a name="powershell-script-4-source-code"></a>Codice sorgente dello script 4 di PowerShell

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno) {
    Write-Host "Remove the Azure SQL DB server.";

    Remove-AzSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;

    Write-Host "Remove the Azure Resource Group.";

    Remove-AzResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else {
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```

<a name="a-actual-output" />

<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Verificare che la subnet sia un endpoint

Alla subnet potrebbe essere già stato assegnato il nome del tipo **Microsoft.Sql**; ciò significa che si tratta già di un endpoint del servizio virtuale. È possibile usare il [portale di Azure][http-azure-portal-link-ref-477t] per creare una regola di rete virtuale dall'endpoint.

Nel caso in cui non si sia certi che la subnet abbia il nome del tipo **Microsoft.Sql**, è possibile eseguire lo script di PowerShell seguente per completare queste azioni:

1. Verificare che la subnet abbia il nome del tipo **Microsoft.Sql**.
2. Se assente, assegnare il nome del tipo (facoltativo).
    - Lo script richiede la *conferma* dell'utente prima di applicare il nome del tipo assente.

### <a name="phases-of-the-script"></a>Fasi dello script

Di seguito sono riepilogate le fasi dello script di PowerShell:

1. Accedere al proprio account Azure (richiesto una sola volta per ogni sessione di PowerShell).  Assegnare le variabili.
2. Cercare la rete virtuale e quindi la subnet.
3. Verificare se la subnet è contrassegnata come tipo di server endpoint **Microsoft.Sql**.
4. Aggiungere un endpoint del servizio virtuale del nome del tipo **Microsoft.Sql** nella subnet.

> [!IMPORTANT]
> Prima di eseguire questo script, è necessario modificare i valori assegnati alle variabili $- all'inizio dello script.

### <a name="direct-powershell-source-code"></a>Codice sorgente diretto di PowerShell

Questo script di PowerShell non esegue l'aggiornamento, a meno che non si risponda affermativamente in caso di richiesta di una conferma. Lo script può aggiungere il nome del tipo **Microsoft.Sql** alla subnet, ma solo nel caso in cui la subnet non lo abbia già.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.
$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.
# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null) {
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null) {
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?
$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++) {
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb) {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null) {
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else {
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.
$vnet = Set-AzVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++) {
    $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

<!-- Link references: -->
[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
