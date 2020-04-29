---
title: Configurare chiavi gestite dal cliente per l'account Azure Cosmos DB
description: Informazioni su come configurare le chiavi gestite dal cliente per l'account Azure Cosmos DB con Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 8f58887a056c8ca0cd175a44127556562338de38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450033"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Configurare le chiavi gestite dal cliente per l'account Azure Cosmos con Azure Key Vault

> [!NOTE]
> A questo punto, è necessario richiedere l'accesso per usare questa funzionalità. A tale scopo, contattare [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com).

I dati archiviati nell'account Azure Cosmos vengono crittografati automaticamente e facilmente con le chiavi gestite da Microsoft (**chiavi gestite dal servizio**). Facoltativamente, è possibile scegliere di aggiungere un secondo livello di crittografia con le chiavi gestite (**chiavi gestite dal cliente**).

![Livelli di crittografia relativi ai dati dei clienti](./media/how-to-setup-cmk/cmk-intro.png)

È necessario archiviare le chiavi gestite dal cliente in [Azure Key Vault](../key-vault/general/overview.md) e fornire una chiave per ogni account Azure Cosmos abilitato con chiavi gestite dal cliente. Questa chiave viene usata per crittografare tutti i dati archiviati nell'account.

> [!NOTE]
> Attualmente, le chiavi gestite dal cliente sono disponibili solo per i nuovi account Azure Cosmos. È necessario configurarli durante la creazione dell'account.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Registrare il provider di risorse Azure Cosmos DB per la sottoscrizione di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/), passare alla sottoscrizione di Azure e selezionare **provider di risorse** nella scheda **Impostazioni** :

   ![Voce "provider di risorse" dal menu a sinistra](./media/how-to-setup-cmk/portal-rp.png)

1. Cercare il provider di risorse **Microsoft. DocumentDB** . Verificare che il provider di risorse sia già contrassegnato come registrato. In caso contrario, scegliere il provider di risorse e selezionare **Register (registra**):

   ![Registrazione del provider di risorse Microsoft. DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Configurare l'istanza di Azure Key Vault

Per utilizzare chiavi gestite dal cliente con Azure Cosmos DB è necessario impostare due proprietà nell'istanza di Azure Key Vault che si prevede di utilizzare per ospitare le chiavi di crittografia. Queste proprietà includono l' **eliminazione** temporanea e **non vengono rieliminate**. Queste proprietà non sono abilitate per impostazione predefinita. È possibile abilitarli usando PowerShell o l'interfaccia della riga di comando di Azure.

Per informazioni su come abilitare queste proprietà in un'istanza di Azure Key Vault esistente, vedere le sezioni "Abilitazione dell'eliminazione temporanea" e "Abilitazione della protezione dell'eliminazione" in uno degli articoli seguenti:

- [Come usare l'eliminazione temporanea con PowerShell](../key-vault/general/soft-delete-powershell.md)
- [Come usare l'eliminazione temporanea con l'interfaccia della riga di comando di Azure](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Aggiungere un criterio di accesso all'istanza di Azure Key Vault

1. Dal portale di Azure passare all'istanza di Azure Key Vault che si intende usare per ospitare le chiavi di crittografia. Selezionare **criteri di accesso** dal menu a sinistra:

   !["Criteri di accesso" dal menu a sinistra](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Selezionare **+ Aggiungi criteri di accesso**.

1. Nel menu a discesa **autorizzazioni chiave** selezionare le autorizzazioni **Get**, **Unwrap Key**e **Wrap Key** :

   ![Selezione delle autorizzazioni corrette](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. In **Seleziona entità**selezionare **Nessuno selezionato**. Quindi, cercare l'entità **Azure Cosmos DB** e selezionarla (per facilitarne l'individuazione, è anche possibile eseguire la ricerca in base all' `a232010e-820c-4083-83bb-3ace5fc29d0b` ID entità: per qualsiasi area di Azure, ad eccezione delle aree `57506a73-e302-42a9-b869-6f12d9ec29e9`di Azure per enti pubblici in cui l'ID principale è). Infine, scegliere **Seleziona** nella parte inferiore. Se l'entità **Azure Cosmos DB** non è presente nell'elenco, potrebbe essere necessario registrare nuovamente il provider di risorse **Microsoft. DocumentDB** come descritto nella sezione [registrare il provider di risorse](#register-resource-provider) di questo articolo.

   ![Selezionare l'entità di Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Selezionare **Aggiungi** per aggiungere i nuovi criteri di accesso.

## <a name="generate-a-key-in-azure-key-vault"></a>Generare una chiave in Azure Key Vault

1. Dal portale di Azure passare all'istanza di Azure Key Vault che si intende usare per ospitare le chiavi di crittografia. Quindi, selezionare **chiavi** dal menu a sinistra:

   ![Voce "chiavi" nel menu a sinistra](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Selezionare **genera/importa**, specificare un nome per la nuova chiave e selezionare le dimensioni della chiave RSA. Per una sicurezza ottimale, è consigliabile usare almeno 3072. Quindi selezionare **Crea**:

   ![Creare una nuova chiave](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Dopo aver creato la chiave, selezionare la chiave appena creata e quindi la versione corrente.

1. Copiare l'identificatore di **chiave**della chiave, eccetto la parte dopo l'ultima barra:

   ![Copia dell'identificatore di chiave della chiave](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Creare un nuovo account Azure Cosmos

### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Quando si crea un nuovo account Azure Cosmos DB dal portale di Azure, scegliere **chiave gestita dal cliente** nel passaggio di **crittografia** . Nel campo **URI chiave** incollare l'identificatore URI/chiave della chiave di Azure Key Vault copiata nel passaggio precedente:

![Impostazione dei parametri CMK nel portale di Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Uso di Azure PowerShell

Quando si crea un nuovo account Azure Cosmos DB con PowerShell:

- Passare l'URI della chiave di Azure Key Vault copiato in precedenza sotto la proprietà **keyVaultKeyUri** in **PropertyObject**.

- Usare **2019-12-12** come versione dell'API.

> [!IMPORTANT]
> È necessario impostare il `Location` parametro in modo esplicito per l'account da creare correttamente con le chiavi gestite dal cliente.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-an-azure-resource-manager-template"></a>Uso di un modello di Azure Resource Manager

Quando si crea un nuovo account Azure Cosmos tramite un modello di Azure Resource Manager:

- Passare l'URI della chiave di Azure Key Vault copiato in precedenza sotto la proprietà **keyVaultKeyUri** nell'oggetto **Properties** .

- Usare **2019-12-12** come versione dell'API.

> [!IMPORTANT]
> È necessario impostare il `Location` parametro in modo esplicito per l'account da creare correttamente con le chiavi gestite dal cliente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

Distribuire il modello con lo script di PowerShell seguente:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a>Uso dell'interfaccia della riga di comando di Azure

Quando si crea un nuovo account Azure Cosmos tramite l'interfaccia della riga di comando di Azure, passare l'URI della chiave di Azure Key Vault copiato in precedenza sotto il parametro **--Key-URI** .

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Sono previsti costi aggiuntivi per l'uso delle chiavi gestite dal cliente?

Sì. Per tenere conto del carico di calcolo aggiuntivo necessario per gestire la crittografia e la decrittografia dei dati con chiavi gestite dal cliente, tutte le operazioni eseguite sull'account Azure Cosmos utilizzano un aumento del 25% delle [unità richiesta](./request-units.md).

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Quali dati vengono crittografati con le chiavi gestite dal cliente?

Tutti i dati archiviati nell'account Azure Cosmos vengono crittografati con le chiavi gestite dal cliente, tranne i metadati seguenti:

- Nomi degli account di Azure Cosmos DB [, dei database e dei contenitori](./account-overview.md#elements-in-an-azure-cosmos-account)

- Nomi delle [stored procedure](./stored-procedures-triggers-udfs.md)

- Percorsi delle proprietà dichiarati nei [criteri di indicizzazione](./index-policy.md)

- Valori delle [chiavi di partizione](./partitioning-overview.md) dei contenitori

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Le chiavi gestite dal cliente sono supportate per gli account Azure Cosmos esistenti?

Questa funzionalità è attualmente disponibile solo per i nuovi account.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>È previsto un piano per supportare una maggiore granularità rispetto alle chiavi a livello di account?

Non attualmente, ma vengono considerate le chiavi a livello di contenitore.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>In che modo le chiavi gestite dal cliente influiscono su un backup?

Azure Cosmos DB esegue [backup regolari e automatici](./online-backup-and-restore.md) dei dati archiviati nell'account. Questa operazione esegue il backup dei dati crittografati. Per utilizzare il backup ripristinato, è necessaria la chiave di crittografia utilizzata al momento del backup. Ciò significa che non è stata effettuata alcuna revoca e che la versione della chiave usata al momento del backup sarà ancora abilitata.

### <a name="how-do-i-revoke-an-encryption-key"></a>Ricerca per categorie revocare una chiave di crittografia?

La revoca della chiave viene eseguita disabilitando la versione più recente della chiave:

![Disabilitare la versione di una chiave](./media/how-to-setup-cmk/portal-akv-rev2.png)

In alternativa, per revocare tutte le chiavi da un'istanza di Azure Key Vault, è possibile eliminare i criteri di accesso concessi all'entità Azure Cosmos DB:

![Eliminazione dei criteri di accesso per l'entità Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Quali operazioni sono disponibili dopo la revoca di una chiave gestita dal cliente?

L'unica operazione possibile quando la chiave di crittografia è stata revocata è l'eliminazione dell'account.

## <a name="next-steps"></a>Passaggi successivi

- Scopri di più sulla [crittografia dei dati in Azure Cosmos DB](./database-encryption-at-rest.md).
- Ottenere una panoramica dell' [accesso sicuro ai dati in Cosmos DB](secure-access-to-data.md).
