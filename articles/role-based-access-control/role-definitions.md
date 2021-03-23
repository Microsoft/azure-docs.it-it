---
title: Informazioni sulle definizioni dei ruoli di Azure-RBAC di Azure
description: Informazioni sulle definizioni dei ruoli di Azure nel controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per la gestione degli accessi con granularità fine delle risorse di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 03/22/2021
ms.author: rolyon
ms.custom: ''
ms.openlocfilehash: 5b2ec3289d187997763ee0d9280a777d4fa1f396
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801758"
---
# <a name="understand-azure-role-definitions"></a>Informazioni sulle definizioni dei ruoli di Azure

Se si sta provando a comprendere il funzionamento di un ruolo di Azure o se si crea un [ruolo personalizzato di Azure](custom-roles.md), è utile comprendere come vengono definiti i ruoli. Questo articolo illustra in dettaglio le definizioni di ruolo e presenta alcuni esempi.

## <a name="role-definition"></a>Definizione di ruolo

Una *definizione di ruolo* è una raccolta di autorizzazioni, A volte viene semplicemente chiamato un *ruolo*. Una definizione di ruolo elenca le operazioni che è possibile eseguire, ad esempio lettura, scrittura ed eliminazione, È inoltre possibile elencare le operazioni escluse dalle operazioni consentite o dalle operazioni correlate ai dati sottostanti.

Di seguito viene illustrato un esempio delle proprietà in una definizione di ruolo quando viene visualizzato utilizzando Azure PowerShell:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Di seguito è riportato un esempio delle proprietà in una definizione di ruolo quando viene visualizzato usando il portale di Azure, l'interfaccia della riga di comando di Azure o l'API REST:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

La tabella seguente descrive il significato delle proprietà del ruolo.

| Proprietà | Descrizione |
| --- | --- |
| `Name`</br>`roleName` | Nome visualizzato del ruolo. |
| `Id`</br>`name` | ID univoco del ruolo. I ruoli predefiniti hanno lo stesso ID ruolo nei cloud. |
| `IsCustom`</br>`roleType` | Indica se questo è un ruolo personalizzato. Impostare su `true` o `CustomRole` per i ruoli personalizzati. Impostare su `false` o `BuiltInRole` per i ruoli predefiniti. |
| `Description`</br>`description` | Descrizione del ruolo. |
| `Actions`</br>`actions` | Matrice di stringhe che specifica le operazioni di gestione che il ruolo consente di eseguire. |
| `NotActions`</br>`notActions` | Matrice di stringhe che specifica le operazioni di gestione che sono escluse dalle `Actions` consentite. |
| `DataActions`</br>`dataActions` | Matrice di stringhe che specifica le operazioni sui dati che il ruolo consente di eseguire sui dati all'interno dell'oggetto. |
| `NotDataActions`</br>`notDataActions` | Matrice di stringhe che specifica le operazioni sui dati che sono escluse dalle `DataActions` consentite. |
| `AssignableScopes`</br>`assignableScopes` | Matrice di stringhe che specifica gli ambiti disponibili per l'assegnazione del ruolo. |

### <a name="operations-format"></a>Formato delle operazioni

Le operazioni vengono specificate con stringhe che hanno il formato seguente:

- `{Company}.{ProviderName}/{resourceType}/{action}`

La parte `{action}` di una stringa relativa a un'operazione specifica il tipo di operazioni che è possibile eseguire su un tipo di risorsa. In `{action}` possono ad esempio essere elencate le sottostringhe seguenti:

| Sottostringa azione    | Descrizione         |
| ------------------- | ------------------- |
| `*` | Il carattere jolly concede l'accesso a tutte le operazioni che corrispondono alla stringa. |
| `read` | Abilita le operazioni di lettura (GET). |
| `write` | Abilita le operazioni di scrittura (PUT o PATCH). |
| `action` | Abilita operazioni personalizzate come il riavvio delle macchine virtuali (POST). |
| `delete` | Abilita le operazioni di eliminazione (DELETE). |

### <a name="role-definition-example"></a>Esempio di definizione di ruolo

Di seguito è illustrata la definizione del ruolo [collaboratore](built-in-roles.md#contributor) visualizzata nell'interfaccia della riga di comando Azure PowerShell e Azure. L'operazione identificata dal carattere jolly (`*`) in `Actions` indica che l'entità di sicurezza assegnata a questo ruolo può eseguire qualsiasi azione, anche quelle definite in futuro in seguito all'aggiunta di nuovi tipi di risorse da parte di Azure. Le operazioni in `NotActions` vengono sottratte da `Actions`. Nel caso del ruolo [collaboratore](built-in-roles.md#contributor) , `NotActions` rimuove la capacità di questo ruolo di gestire l'accesso alle risorse e gestire anche le assegnazioni di Azure Blueprint.

Ruolo Collaboratore visualizzato in Azure PowerShell:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Ruolo Collaboratore visualizzato nell'interfaccia della riga di comando di Azure:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management-and-data-operations"></a>Operazioni di gestione e dati

Il controllo degli accessi in base al ruolo per le operazioni di gestione è specificato nelle proprietà `Actions` e `NotActions` di una definizione di ruolo. Di seguito sono riportati alcuni esempi di operazioni di gestione in Azure:

- Gestire l'accesso a un account di archiviazione
- Creare, aggiornare o eliminare un contenitore BLOB
- Eliminare un gruppo di risorse e tutte le risorse incluse nel gruppo

L'accesso di gestione non viene ereditato dai dati, a condizione che il metodo di autenticazione del contenitore sia impostato su "Azure AD account utente" e non su "chiave di accesso". Questa separazione impedisce ai ruoli con caratteri jolly (`*`) di avere accesso illimitato ai dati. Se ad esempio un utente ha un ruolo [Lettore](built-in-roles.md#reader) in una sottoscrizione, può visualizzare l'account di archiviazione, ma per impostazione predefinita non può visualizzare i dati sottostanti.

In precedenza, il controllo degli accessi in base al ruolo non veniva usato per le operazioni sui dati. L'autorizzazione per le operazioni sui dati variava a seconda del provider di risorse. Lo stesso modello di autorizzazione per il controllo degli accessi in base al ruolo utilizzato per le operazioni di gestione è stato esteso alle operazioni sui dati.

Per supportare le operazioni sui dati, sono state aggiunte nuove proprietà dei dati alla definizione del ruolo. Le operazioni sui dati vengono specificate nelle proprietà `DataActions` e `NotDataActions`. Con l'aggiunta di queste proprietà di dati viene mantenuta la separazione tra gestione e dati. In tal modo, si impedisce alle assegnazioni di ruolo correnti con caratteri jolly (`*`) di accedere immediatamente ai dati. Ecco alcune operazioni sui dati che possono essere specificate in `DataActions` e `NotDataActions`:

- Leggere un elenco dei BLOB in un contenitore
- Scrivere un BLOB di archiviazione in un contenitore
- Eliminare un messaggio in una coda

Di seguito è illustrata la definizione del ruolo [lettore dati BLOB di archiviazione](built-in-roles.md#storage-blob-data-reader) , che include le operazioni in entrambe le `Actions` `DataActions` proprietà e. Questo ruolo consente di leggere il contenitore BLOB e anche i dati di BLOB sottostanti.

Ruolo lettore dati BLOB di archiviazione come visualizzato in Azure PowerShell:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Ruolo lettore dati BLOB di archiviazione visualizzato nell'interfaccia della riga di comando di Azure:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

Alle proprietà `DataActions` e `NotDataActions` possono essere aggiunte solo operazioni sui dati. I provider di risorse identificano le operazioni sui dati impostando la proprietà `isDataAction` su `true`. Per un elenco delle operazioni in cui `isDataAction` è `true`, vedere [Operazioni dei provider di risorse](resource-provider-operations.md). In una definizione di ruolo che non prevede operazioni sui dati non è necessario che siano presenti le proprietà `DataActions` e `NotDataActions`.

Le autorizzazioni per tutte le chiamate API delle operazioni di gestione vengono gestite da Azure Resource Manager. Le autorizzazioni per le chiamate API delle operazioni sui dati vengono gestite da un provider di risorse o da Azure Resource Manager.

### <a name="data-operations-example"></a>Esempio di operazioni sui dati

Per comprendere meglio il funzionamento delle operazioni di gestione e di quelle sui dati, è possibile considerare un esempio specifico. Ad Alice è stato assegnato il ruolo [Proprietario](built-in-roles.md#owner) nell'ambito della sottoscrizione. A Bob è stato assegnato il ruolo di [collaboratore dati BLOB di archiviazione](built-in-roles.md#storage-blob-data-contributor) in un ambito dell'account di archiviazione. L'esempio è illustrato dal diagramma seguente.

![Il controllo degli accessi in base al ruolo è stato esteso per supportare sia le operazioni di gestione sia quelle sui dati](./media/role-definitions/rbac-management-data.png)

Il ruolo [proprietario](built-in-roles.md#owner) per Alice e il ruolo di [collaboratore dati BLOB di archiviazione](built-in-roles.md#storage-blob-data-contributor) per Bob hanno le seguenti azioni:

Proprietario

&nbsp;&nbsp;&nbsp;&nbsp;actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Collaboratore ai dati del BLOB di archiviazione

&nbsp;&nbsp;&nbsp;&nbsp;actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;dataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Poiché Alice ha un'azione con carattere jolly ( `*` ) nell'ambito di una sottoscrizione, le relative autorizzazioni ereditano per consentire l'esecuzione di tutte le operazioni di gestione. Alice può leggere, scrivere ed eliminare i contenitori. Tuttavia non può eseguire operazioni sui dati senza eseguire passaggi aggiuntivi. Ad esempio, per impostazione predefinita, non può leggere i BLOB all'interno di un contenitore. Per leggere i BLOB, Alice deve prima recuperare le chiavi di accesso alle risorse di archiviazione e usarle per accedere ai BLOB.

Le autorizzazioni di Bob sono limitate solo a `Actions` e `DataActions` specificate nel ruolo di [collaboratore dati BLOB di archiviazione](built-in-roles.md#storage-blob-data-contributor) . In base al ruolo, Bob può eseguire sia operazioni di gestione sia operazioni sui dati. Ad esempio, Bob può leggere, scrivere ed eliminare i contenitori nell'account di archiviazione specificato ed è anche in grado di leggere, scrivere ed eliminare i BLOB.

Per altre informazioni sulla gestione e sulla sicurezza del piano dati per l'archiviazione, vedere [Guida alla sicurezza di Archiviazione di Azure](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>Quali strumenti supportano l'uso dei ruoli di Azure per le operazioni sui dati?

Per visualizzare ed eseguire le operazioni sui dati, è necessario disporre delle versioni corrette degli strumenti o degli SDK:

| Strumento  | Versione  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 o versione successiva |
| [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) | 2.0.30 o successiva |
| [Azure per .NET](/dotnet/azure/) | 2.8.0-Preview o successiva |
| [Azure SDK per Go](/azure/go/azure-sdk-go-install) | 15.0.0 o successiva |
| [Azure per Java](/java/azure/) | 1.9.0 o successiva |
| [Azure per Python](/azure/python/) | 0.40.0 o successiva |
| [Azure SDK per Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 o successiva |

Per visualizzare e usare le operazioni di dati di API REST, è necessario impostare il parametro **api-version** per la seguente versione o versioni successive:

- 2018-07-01

## <a name="actions"></a>Azioni

L'autorizzazione `Actions` specifica le operazioni di gestione che il ruolo consente di eseguire. Si tratta di una raccolta di stringhe di operazione che identificano operazioni a protezione diretta dei provider di risorse di Azure. Di seguito sono riportati alcuni esempi di operazioni di gestione che possono essere usate in `Actions`.

> [!div class="mx-tableFixed"]
> | Stringa operazione    | Descrizione         |
> | ------------------- | ------------------- |
> | `*/read` | Concede l'accesso a operazioni di lettura per tutti i tipi di risorse di tutti i provider di risorse di Azure.|
> | `Microsoft.Compute/*` | Concede l'accesso a tutte le operazioni per tutti i tipi di risorse nel provider di risorse Microsoft.Compute.|
> | `Microsoft.Network/*/read` | Concede l'accesso alle operazioni di lettura per tutti i tipi di risorse nel provider di risorse Microsoft.Network.|
> | `Microsoft.Compute/virtualMachines/*` | Concede l'accesso a tutte le operazioni delle macchine virtuali e ai relativi tipi di risorse figlio.|
> | `microsoft.web/sites/restart/Action` | Concede l'accesso per il riavvio di un'app Web.|

## <a name="notactions"></a>NotActions

L' `NotActions` autorizzazione specifica le operazioni di gestione sottratte o escluse dall'oggetto consentito con `Actions` un carattere jolly ( `*` ). Utilizzare l' `NotActions` autorizzazione se il set di operazioni che si desidera consentire è più facilmente definito sottraendo da `Actions` che hanno un carattere jolly ( `*` ). L'accesso concesso da un ruolo (ovvero le autorizzazioni effettive) viene calcolato sottraendo le operazioni `NotActions` alle operazioni `Actions`.

`Actions - NotActions = Effective management permissions`

La tabella seguente illustra due esempi delle autorizzazioni valide per un'operazione con caratteri jolly [Microsoft. CostManagement](resource-provider-operations.md#microsoftcostmanagement) :

> [!div class="mx-tableFixed"]
> | Actions | NotActions | Autorizzazioni di gestione valide |
> | --- | --- | --- |
> | `Microsoft.CostManagement/exports/*` | *nessuna* | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/delete`</br>`Microsoft.CostManagement/exports/run/action` |
> | `Microsoft.CostManagement/exports/*` | `Microsoft.CostManagement/exports/delete` | `Microsoft.CostManagement/exports/action`</br>`Microsoft.CostManagement/exports/read`</br>`Microsoft.CostManagement/exports/write`</br>`Microsoft.CostManagement/exports/run/action` |

> [!NOTE]
> Se a un utente si assegna un ruolo che esclude un'operazione in `NotActions` e quindi si assegna un secondo ruolo che concede l'accesso alla stessa operazione, l'utente può eseguire tale operazione. `NotActions` non è una regola di negazione. È semplicemente un modo comodo per creare un set di operazioni consentite quando è necessario escludere operazioni specifiche.
>

### <a name="differences-between-notactions-and-deny-assignments"></a>Differenze tra notacts e Deny assegnazioni

`NotActions` e le assegnazioni di negazione non sono uguali e servono a scopi diversi. `NotActions` sono un modo pratico per sottrarre azioni specifiche da un'operazione con carattere jolly ( `*` ).

Le assegnazioni di rifiuto impediscono agli utenti di eseguire azioni specifiche, anche se un'assegnazione di ruolo concede loro l'accesso. Per altre informazioni, vedere [Informazioni sulle assegnazioni di rifiuto di Azure](deny-assignments.md).

## <a name="dataactions"></a>DataActions

L'autorizzazione `DataActions` specifica le operazioni sui dati che il ruolo consente di eseguire sui dati all'interno dell'oggetto. Ad esempio, se un utente dispone dell'accesso in lettura ai dati di BLOB per un account di archiviazione, può leggere i BLOB all'interno di tale account. Di seguito sono riportati alcuni esempi di operazioni sui dati che possono essere usate in `DataActions`.

> [!div class="mx-tableFixed"]
> | Stringa operazione    | Descrizione         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Restituisce un BLOB o un elenco di BLOB. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Restituisce il risultato della scrittura su un BLOB. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Restituisce un messaggio. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Restituisce un messaggio o il risultato della scrittura o dell'eliminazione di un messaggio. |

## <a name="notdataactions"></a>NotDataActions

L' `NotDataActions` autorizzazione specifica le operazioni sui dati sottratte o escluse dall'oggetto consentito con `DataActions` un carattere jolly ( `*` ). Utilizzare l' `NotDataActions` autorizzazione se il set di operazioni che si desidera consentire è più facilmente definito sottraendo da `DataActions` che hanno un carattere jolly ( `*` ). L'accesso concesso da un ruolo (ovvero le autorizzazioni effettive) viene calcolato sottraendo le operazioni `NotDataActions` alle operazioni `DataActions`. Ogni provider di risorse fornisce il rispettivo set di API per consentire l'esecuzione delle operazioni sui dati.

`DataActions - NotDataActions = Effective data permissions`

Nella tabella seguente sono riportati due esempi delle autorizzazioni valide per un'operazione con caratteri jolly [Microsoft. storage](resource-provider-operations.md#microsoftstorage) :

> [!div class="mx-tableFixed"]
> | DataActions | NotDataActions | Autorizzazioni dati valide |
> | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | *nessuna* | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete`</br> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/write`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action`</br>`Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action` |

> [!NOTE]
> Se a un utente si assegna un ruolo che esclude un'operazione sui dati in `NotDataActions` e quindi si assegna un secondo ruolo che concede l'accesso alla stessa operazione, l'utente può eseguire tale operazione. `NotDataActions` non è una regola di negazione. È semplicemente un modo comodo per creare un set di operazioni sui dati consentite quando è necessario escludere operazioni specifiche.
>

## <a name="assignablescopes"></a>AssignableScopes

La `AssignableScopes` proprietà specifica gli ambiti (gruppi di gestione, sottoscrizioni o gruppi di risorse) per i quali è disponibile questa definizione di ruolo. È possibile rendere disponibile il ruolo per l'assegnazione solo nei gruppi di gestione, nelle sottoscrizioni o nei gruppi di risorse che lo richiedono. È necessario utilizzare almeno un gruppo di gestione, una sottoscrizione o un gruppo di risorse.

Per i ruoli predefiniti, `AssignableScopes` è impostato sull'ambito radice (`"/"`), per indicare che il ruolo è disponibile per l'assegnazione in tutti gli ambiti. Ecco alcuni esempi di ambiti assegnabili validi:

> [!div class="mx-tableFixed"]
> | Il ruolo è disponibile per l'assegnazione | Esempio |
> |----------|---------|
> | Un'unica sottoscrizione | `"/subscriptions/{subscriptionId1}"` |
> | Due sottoscrizioni | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Gruppo di risorse di rete | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Un gruppo di gestione | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Gruppo di gestione e sottoscrizione | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Tutti gli ambiti (si applica solo ai ruoli predefiniti) | `"/"` |

Per informazioni sui `AssignableScopes` ruoli personalizzati, vedere [ruoli personalizzati di Azure](custom-roles.md).

## <a name="next-steps"></a>Passaggi successivi

* [Ruoli predefiniti di Azure](built-in-roles.md)
* [Ruoli personalizzati di Azure](custom-roles.md)
* [Operazioni del provider di risorse di Azure](resource-provider-operations.md)
