---
title: Distribuire Criteri di Azure nelle sottoscrizioni delegate su larga scala
description: Informazioni sul modo in cui la gestione risorse delegate di Azure consente di distribuire una definizione e un'assegnazione dei criteri tra più tenant.
ms.date: 11/8/2019
ms.topic: how-to
ms.openlocfilehash: 0e3af1728b4927874fa599bad256798f39721e22
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636530"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Distribuire Criteri di Azure nelle sottoscrizioni delegate su larga scala

I provider di servizi potrebbero aver integrato più tenant di clienti per la gestione risorse delegate di Azure. [Azure Lighthouse](../overview.md) consente ai provider di servizi di eseguire operazioni su larga scala tra più tenant contemporaneamente, rendendo più efficienti le attività di gestione.

Questo argomento illustra come usare [Criteri di Azure](../../governance/policy/index.yml) per distribuire una definizione e un'assegnazione dei criteri tra più tenant usando i comandi di PowerShell. In questo esempio, la definizione dei criteri garantisce che gli account di archiviazione siano protetti consentendo solo il traffico HTTPS.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Usare Azure Resource Graph per eseguire query tra tenant di clienti

È possibile usare [Azure Resource Graph](../../governance/resource-graph/index.yml) per eseguire query su tutte le sottoscrizioni nei tenant di clienti gestiti. In questo esempio verranno identificati gli account di archiviazione in queste sottoscrizioni che attualmente non richiedono il traffico HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Distribuire criteri tra più tenant di clienti

L'esempio seguente illustra come usare un [modello di Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) per distribuire una definizione e un'assegnazione dei criteri tra sottoscrizioni delegate in più tenant di clienti. Questa definizione di criteri richiede che tutti gli account di archiviazione usino il traffico HTTPS, impedendo la creazione di nuovi account di archiviazione non conformi e contrassegnando gli account di archiviazione esistenti senza l'impostazione come non conforme.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Convalidare la distribuzione dei criteri

Dopo aver distribuito il modello di Azure Resource Manager, è possibile verificare che la definizione dei criteri sia stata applicata correttamente provando a creare un account di archiviazione con **EnableHttpsTrafficOnly** impostato su **false** in una delle sottoscrizioni Delegate. Per via dell'assegnazione dei criteri, non è possibile creare questo account di archiviazione.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine, rimuovere la definizione dei criteri e l'assegnazione creata dalla distribuzione.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Criteri di Azure](../../governance/policy/index.yml).
- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
