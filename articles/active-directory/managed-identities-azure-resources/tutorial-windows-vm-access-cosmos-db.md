---
title: "Esercitazione: Usare un'identità gestita per accedere ad Azure Cosmos DB - Windows - Azure AD"
description: Esercitazione che illustra come usare un'identità gestita assegnata dal sistema in una macchina virtuale Windows per accedere ad Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04508f1aa8ee9d6b4f730f57c60d959fab209122
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093804"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Esercitazione: Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere ad Azure Cosmos DB

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Questa esercitazione illustra come usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere a Cosmos DB. Si apprenderà come:

> [!div class="checklist"]
> * Creare un account Cosmos DB
> * Concedere l'accesso alle chiavi di accesso dell'account Cosmos DB a un'identità gestita assegnata dal sistema per la macchina virtuale Windows
> * Ottenere un token di accesso usando l'identità gestita assegnata dal sistema della macchina virtuale Windows per chiamare Azure Resource Manager
> * Ottenere le chiavi di accesso da Azure Resource Manager per eseguire le chiamate a Cosmos DB

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con la funzionalità delle identità gestite per le risorse di Azure, vedere questa [panoramica](overview.md). 
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le attività richieste di creazione delle risorse e gestione dei ruoli, l'account deve disporre delle autorizzazioni "Proprietario" nell'ambito appropriato (sottoscrizione o gruppo di risorse). Se è necessaria assistenza per l'assegnazione di ruolo, vedere [assegnare i ruoli di Azure per gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md).
- Installare la versione più recente di [Azure PowerShell](/powershell/azure/install-az-ps)
- È anche necessaria una macchina virtuale Windows con identità gestite assegnate dal sistema abilitate.
  - Se è necessario creare una macchina virtuale per questa esercitazione, è possibile seguire l'articolo [Creare una macchina virtuale con l'identità assegnata dal sistema abilitata](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

## <a name="create-a-cosmos-db-account"></a>Creare un account Cosmos DB 

Se non ne è già disponibile uno, creare un account Cosmos DB. È possibile ignorare questo passaggio e usare un account Cosmos DB esistente. 

1. Fare clic sul pulsante **+ Crea una risorsa** visualizzato nell'angolo in alto a sinistra nel portale di Azure.
2. Fare clic su **Database**, quindi su **Azure Cosmos DB**. Verrà visualizzato un nuovo pannello "Nuovo account".
3. Immettere un **ID** per l'account Cosmos DB, da usare in un secondo momento.  
4. **API** deve essere impostato su "SQL". L'approccio descritto in questa esercitazione può essere usato con gli altri tipi di API disponibili, ma i passaggi illustrati si riferiscono all'API SQL.
5. Verificare che le impostazioni in **Sottoscrizione** e **Gruppo di risorse** corrispondano a quelle specificate al momento della creazione della macchina virtuale nel passaggio precedente.  Selezionare un **Percorso** in cui è disponibile Cosmos DB.
6. Fare clic su **Crea**.

### <a name="create-a-collection"></a>Creare una raccolta 

Successivamente, aggiungere una raccolta di dati nell'account Cosmos DB su cui è possibile eseguire query nei passaggi successivi.

1. Passare all'account Cosmos DB appena creato.
2. Nella scheda **Panoramica** fare clic sul pulsante **+/Aggiungi raccolta**. Verrà visualizzato il pannello "Aggiungi raccolta".
3. Assegnare alla raccolta un ID database e un ID raccolta, selezionare una capacità di archiviazione, immettere una chiave di partizione, specificare un valore di velocità effettiva, quindi fare clic su **OK**.  Per questa esercitazione, è sufficiente usare "Test" come ID database e ID raccolta, selezionare una capacità di archiviazione fissa e una velocità effettiva più bassa (400 UR/sec).  


## <a name="grant-access"></a>Concedere l'accesso

Questa sezione illustra come concedere l'accesso alle chiavi di accesso dell'account Cosmos DB a un'identità gestita assegnata dal sistema per la macchina virtuale Windows. Cosmos DB non supporta l'autenticazione di Azure AD in modo nativo. È tuttavia possibile usare un'identità gestita assegnata dal sistema per recuperare una chiave di accesso di Cosmos DB da Resource Manager e usarla per accedere a Cosmos DB. In questo passaggio si concede l'accesso alle chiavi dell'account Cosmos DB all'identità gestita assegnata dal sistema per la macchina virtuale Windows.

Per concedere all'identità gestita assegnata dal sistema per la macchina virtuale Windows l'accesso all'account Cosmos DB in Azure Resource Manager tramite PowerShell, aggiornare i valori seguenti:

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>`

Cosmos DB supporta due livelli di granularità quando si usano le chiavi di accesso: accesso all'account in lettura/scrittura e in sola lettura.  Assegnare il ruolo `DocumentDB Account Contributor` se si vogliono ottenere chiavi di lettura/scrittura per l'account oppure il ruolo `Cosmos DB Account Reader Role` per ottenere chiavi di sola lettura.  Per questa esercitazione assegnare `Cosmos DB Account Reader Role`:

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> Tenere presente che se non si riesce a eseguire un'operazione, è possibile che non si disponga delle autorizzazioni appropriate. Se si vuole accedere in scrittura alle chiavi, è necessario usare un ruolo di Azure come Collaboratore Account DocumentDB oppure creare un ruolo personalizzato. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Azure Cosmos DB](../../cosmos-db/role-based-access-control.md)

## <a name="access-data"></a>Accedere ai dati

Questa sezione illustra come chiamare Azure Resource Manager usando un token di accesso per l'identità gestita assegnata dal sistema della macchina virtuale Windows. Il resto dell'esercitazione prevede che le operazioni vengano svolte dalla macchina virtuale creata in precedenza. 

È necessario installare la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) nella macchina virtuale Windows.

### <a name="get-an-access-token"></a>Ottenere un token di accesso

1. Nel portale di Azure passare a **Macchine virtuali** selezionare la propria VM Windows, quindi nella pagina **Panoramica** fare clic su **Connetti** nella parte superiore. 
2. In **Nome utente** e **Password** immettere i valori specificati al momento della creazione della macchina virtuale Windows. 
3. Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire PowerShell nella sessione remota.
4. Usando Invoke-WebRequest di PowerShell, eseguire una richiesta all'endpoint locale delle identità gestite per le risorse di Azure per ottenere un token di accesso per Azure Resource Manager.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > Il valore del parametro "risorsa" deve corrispondere esattamente a quello previsto da Azure AD. Quando si usa l'ID risorsa di Azure Resource Manager, è necessario includere la barra finale nell'URI.
    
   Estrarre quindi l'elemento "Content", archiviato come stringa in formato JSON (JavaScript Object Notation) nell'oggetto $response. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   Estrarre poi il token di accesso dalla risposta.
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>Ottenere le chiavi di accesso 

Questa sezione illustra come ottenere le chiavi di accesso da Azure Resource Manager per eseguire le chiamate a Cosmos DB. Viene usato PowerShell per chiamare Resource Manager usando il token di accesso recuperato in precedenza per recuperare la chiave di accesso dell'account Cosmos DB. Dopo avere ottenuto la chiave di accesso, è possibile eseguire una query su Cosmos DB. Sostituire le voci seguenti con i propri valori:

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>` 
- Sostituire il valore `<ACCESS TOKEN>` con il token di accesso recuperato in precedenza. 

>[!NOTE]
>Se si desidera recuperare le chiavi di lettura/scrittura, usare il tipo di operazione della chiave `listKeys`.  Per recuperare le chiavi di sola lettura, usare il tipo di operazione della chiave `readonlykeys`. Se non è possibile usare 'listkeys', verificare che all'identità gestita sia stato assegnato il [ruolo appropriato](../../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role).

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/readonlykeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
La risposta restituisce l'elenco di chiavi.  Ad esempio, se si ottengono chiavi di sola lettura:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Ora che si è in possesso della chiave di accesso per l'account Cosmos DB, è possibile trasferirla a un SDK Cosmos DB ed eseguire chiamate per accedere all'account.  Per un esempio rapido, è possibile passare la chiave di accesso all'interfaccia della riga di comando di Azure.  È possibile ottenere il valore `<COSMOS DB CONNECTION URL>` dalla scheda **Panoramica** nel pannello dell'account Cosmos DB nel portale di Azure.  Sostituire `<ACCESS KEY>` con il valore ottenuto in precedenza:

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Questo comando dell'interfaccia della riga di comando restituisce i dettagli sulla raccolta:

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```


## <a name="disable"></a>Disabilitazione

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare un'identità assegnata dal sistema per una macchina virtuale Windows per accedere a Cosmos DB.  Per altre informazioni su Cosmos DB, consultare:

> [!div class="nextstepaction"]
>[Panoramica di Azure Cosmos DB](../../cosmos-db/introduction.md)