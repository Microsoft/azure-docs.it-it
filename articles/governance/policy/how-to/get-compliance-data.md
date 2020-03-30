---
title: Ottenere i dati di conformità ai criteri
description: Le valutazioni e gli effetti di Criteri di Azure determinano la conformità. Informazioni su come ottenere i dettagli di conformità delle risorse di Azure.Learn how to get the compliance details of your Azure resources.
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280638"
---
# <a name="get-compliance-data-of-azure-resources"></a>Ottenere i dati di conformità delle risorse di AzureGet compliance data of Azure resources

Uno dei maggiori vantaggi di Criteri di Azure è rappresentato dalle informazioni dettagliate e dai controlli che offre sulle risorse in una sottoscrizione o in un [gruppo di gestione](../../management-groups/overview.md) di sottoscrizioni. Questi controlli possono essere esercitati in molti modi diversi, ad esempio impedendo la creazione di risorse nel percorso errato, imponendo un uso dei tag coerente e comune o controllando che per le risorse esistenti ci siano configurazioni e impostazioni appropriate. In tutti i casi, i dati vengono generati dai criteri di Azure per consentire di comprendere lo stato di conformità dell'ambiente.

Esistono diversi modi per accedere alle informazioni sulla conformità generate dalle assegnazioni di criteri e iniziative:

- Uso del portale di [AzureUsing the Azure portal](#portal)
- Tramite script da [riga di comando](#command-line)

Prima di esaminare i metodi disponibili per creare report sulla conformità, è opportuno comprendere quando vengono aggiornate le informazioni sulla conformità e la frequenza e gli eventi che attivano un ciclo di valutazione.

> [!WARNING]
> Se lo stato di conformità viene segnalato come **Non registrato**, verificare che il provider di risorse **Microsoft.PolicyInsights** sia registrato e che l'utente disponga delle autorizzazioni appropriate per il controllo degli accessi in base al ruolo, come descritto in RBAC in Criteri di [Azure.](../overview.md#rbac-permissions-in-azure-policy)

## <a name="evaluation-triggers"></a>Trigger di valutazione

I risultati di un ciclo di valutazione completato sono disponibili nel provider di risorse `Microsoft.PolicyInsights` attraverso le operazioni `PolicyStates` e `PolicyEvents`. Per altre informazioni sulle operazioni dell'API REST di Azure Policy Insights, vedere Analisi dei criteri di Azure.For more information about the operations of the Azure Policy Insights REST API, see [Azure Policy Insights](/rest/api/policy-insights/).

Le valutazioni delle iniziative e dei criteri assegnati sono il risultato di diversi eventi:

- Un criterio o un'iniziativa è stata appena assegnata a un ambito. L'applicazione dell'assegnazione all'ambito definito richiede circa 30 minuti. Dopo l'applicazione, ha inizio il ciclo di valutazione per le risorse in tale ambito sulla base del criterio o dell'iniziativa appena assegnata e, a seconda degli effetti usati dal criterio o dall'iniziativa, le risorse sono contrassegnate come conformi o non conformi. La valutazione di un criterio o un'iniziativa estesa rispetto a un ambito di risorse di grandi dimensioni può richiedere tempo. Di conseguenza, non esiste un tempo predefinito di completamento di un ciclo di valutazione. Dopo il completamento, i risultati di conformità aggiornati sono disponibili nel portale e negli SDK.

- Un criterio o un'iniziativa già assegnata a un ambito viene aggiornata. Il ciclo di valutazione e la tempistica per questo scenario sono gli stessi di quelli per una nuova assegnazione a un ambito.

- Una risorsa viene distribuita a un ambito con un'assegnazione tramite Resource Manager, REST, l'interfaccia della riga di comando di Azure o Azure PowerShell. In questo scenario l'evento di effetto (Append, Audit, Deny, Deploy) e le informazioni sullo stato conforme per la singola risorsa diventano disponibili nel portale e negli SDK dopo circa 15 minuti. Questo evento non causa una valutazione di altre risorse.

- Ciclo di valutazione della conformità standard. Le assegnazioni vengono automaticamente rivalutate ogni 24 ore. Un criterio o un'iniziativa estesa di molte risorse può richiedere tempo, pertanto non è prevedibile quando verrà completato il ciclo di valutazione. Dopo il completamento, i risultati di conformità aggiornati sono disponibili nel portale e negli SDK.

- Il provider di risorse [configurazione guest](../concepts/guest-configuration.md) viene aggiornato con i dettagli relativi alla conformità da una risorsa gestita.

- Analisi su richiesta

### <a name="on-demand-evaluation-scan"></a>Analisi di valutazione su richiesta

Un'analisi di valutazione di una sottoscrizione o un gruppo di risorse può essere avviata con una chiamata all'API REST. Questa analisi è un processo asincrono. Di conseguenza, l'endpoint REST per avviare l'analisi non attende finché l'analisi non è stata completata per rispondere. Invece, fornisce un URI per eseguire una query dello stato della valutazione richiesta.

In ogni URI dell'API REST vengono usate variabili che è necessario sostituire con i propri valori:

- `{YourRG}`: sostituire con il nome del gruppo di risorse
- `{subscriptionId}`: sostituire con l'ID sottoscrizione

L'analisi supporta la valutazione delle risorse in una sottoscrizione o in un gruppo di risorse. Avviare un'analisi per ambito con un comando **POST** dell'API REST usando le strutture URI seguenti:

- Subscription

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Resource group

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

La chiamata restituisce uno stato **202-Accepted**. L'intestazione della risposta include una proprietà **Posizione** con il formato seguente:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` viene generato in modo statico per l'ambito richiesto. Se un ambito sta già eseguendo un'analisi su richiesta, non viene avviata una nuova analisi. Al contrario, alla nuova richiesta viene fornito lo stesso URI  di `{ResourceContainerGUID}` **posizione** per lo stato. Un comando **GET** dell'API REST per l'URI di **Posizione** restituisce un **202-accepted** mentre è in corso la valutazione. Quando l'analisi di valutazione è stata completata, restituisce uno stato **200 OK**. Il corpo di un'analisi completata è una risposta JSON con lo stato:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Funzionamento della conformità

In un'assegnazione, una risorsa risulta **Non conforme** se non segue le regole dei criteri o delle iniziative.
La tabella seguente illustra il funzionamento dei diversi effetti dei criteri in base alla valutazione della condizione per lo stato di conformità risultante:

| Stato della risorsa | Effetto | Valutazione dei criteri | Stato di conformità |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Non conforme |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Conforme |
| Nuovo | Audit, AuditIfNotExist\* | True | Non conforme |
| Nuovo | Audit, AuditIfNotExist\* | False | Conforme |

\* Gli effetti Append, DeployIfNotExist e AuditIfNotExist richiedono che l'istruzione IF sia TRUE.
Richiedono inoltre che la condizione di esistenza sia FALSE per lo stato non conforme. Se è TRUE, la condizione IF attiva la valutazione della condizione di esistenza per le risorse correlate.

Ad esempio, si supponga di avere un gruppo di risorse, ContosoRG, con alcuni account di archiviazione (evidenziati in rosso) esposti su reti pubbliche.

![Account di archiviazione esposti su reti pubbliche](../media/getting-compliance-data/resource-group01.png)

In questo esempio, è necessario essere ben consapevoli dei rischi di sicurezza. Dopo avere creato un'assegnazione dei criteri, questa viene valutata per tutti gli account di archiviazione nel gruppo di risorse ContosoRG. Controlla i tre account di archiviazione non conformi, modificandone di conseguenza lo stato impostandolo su **Non conforme**.

![Account di archiviazione non conformi controllati](../media/getting-compliance-data/resource-group03.png)

Oltre a **Conforme** e **Non conforme**, i criteri e le risorse possono avere altri tre stati:

- **In conflitto:** esistono due o più criteri con regole in conflitto. Ad esempio, due criteri aggiungono lo stesso tag con valori diversi.
- **Non avviato:** il ciclo di valutazione non è stato avviato per il criterio o la risorsa.
- **Non registrato:** il provider di risorse dei criteri di Azure non è stato registrato o l'account connesso non dispone dell'autorizzazione per leggere i dati di conformità.

Criteri di Azure usa i campi **type** e **name** nella definizione per determinare se una risorsa corrisponde. Se la risorsa corrisponde, viene considerata applicabile e il suo stato è **Conforme** o **Non conforme**. Se la sola proprietà presente nella definizione è **tipo** o **nome**, tutte le risorse sono considerate applicabili e vengono valutate.

La percentuale di conformità viene determinata dividendo le risorse **conformi** per il _ totale delle risorse_.
Per _totale delle risorse_ si intende la somma delle risorse **conformi**, **non conformi** e **in conflitto**. I valori di conformità complessiva corrispondono alla somma delle risorse distinte **conformi** divisa per la somma di tutte le risorse distinte. Nell'immagine seguente sono presenti 20 risorse distinte applicabili, di cui una sola **Non conforme**. La conformità complessiva delle risorse è pari al 95% (19 su 20).

![Esempio di conformità dei criteri dalla pagina Conformità](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portale

Il portale di Azure illustra un'esperienza grafica di visualizzazione e comprensione dello stato di conformità nell'ambiente in uso. Nella pagina **Criteri** l'opzione **Panoramica** fornisce i dettagli per gli ambiti disponibili sulla conformità dei criteri e delle iniziative. Oltre allo stato di conformità e al conteggio per assegnazione, contiene un grafico che mostra la conformità negli ultimi sette giorni. La pagina **Conformità** contiene buona parte di queste stesse informazioni (eccetto il grafico), ma fornisce altre opzioni di ordinamento e applicazione di filtri.

![Esempio di pagina Conformità criteri di AzureExample of Azure Policy Compliance page](../media/getting-compliance-data/compliance-page.png)

Dal momento che un criterio o un'iniziativa può essere assegnata a diversi ambiti, la tabella include l'ambito di ogni assegnazione e il tipo di definizione assegnato. È anche indicato il numero di risorse non conformi e di criteri non conformi per ogni assegnazione. Quando si fa clic su un criterio o su un'iniziativa nella tabella, viene fornita un'analisi più approfondita della conformità per quella particolare assegnazione.

![Esempio di pagina Dettagli conformità criteri di AzureExample of Azure Policy Compliance Details page](../media/getting-compliance-data/compliance-details.png)

L'elenco delle risorse nella scheda **Conformità risorsa** mostra lo stato di valutazione delle risorse esistenti per l'assegnazione corrente. Il valore predefinito della scheda è **Non conforme**, ma è possibile applicare un filtro.
Gli eventi (Append, Audit, Deny, Deploy) attivati dalla richiesta di creazione di una risorsa sono visualizzati nella scheda **Eventi**.

> [!NOTE]
> Per un criterio del motore AKS, la risorsa visualizzata è il gruppo di risorse.

![Esempio di eventi di conformità dei criteri di AzureExample of Azure Policy Compliance events](../media/getting-compliance-data/compliance-events.png)

Per le risorse [in modalità Provider](../concepts/definition-structure.md#resource-provider-modes) di risorse, nella scheda **Conformità risorse** selezionare la risorsa o fare clic con il pulsante destro del mouse sulla riga e scegliere Visualizza **dettagli conformità** per aprire i dettagli di conformità del componente. Questa pagina offre anche schede per visualizzare i criteri assegnati a questa risorsa, gli eventi, gli eventi dei componenti e la cronologia delle modifiche.

![Esempio di dettagli di conformità dei componenti criteri di AzureExample of Azure Policy Component compliance details](../media/getting-compliance-data/compliance-components.png)

Nella pagina Conformità risorse fare clic con il pulsante destro del mouse sulla riga dell'evento su cui si desidera raccogliere ulteriori dettagli e selezionare **Mostra registri attività**. La pagina del log attività viene aperta e pre-filtrata con la ricerca contenente i dettagli per l'assegnazione e gli eventi. Il log attività offre un contesto aggiuntivo e altre informazioni su tali eventi.

![Esempio di log attività di conformità dei criteri di AzureExample of Azure Policy Compliance Activity Log](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Comprendere la non conformità

<a name="change-history-preview"></a>

Quando si determina che una risorsa non è **conforme,** esistono molti motivi possibili. Per determinare il motivo per cui una risorsa non è **conforme** o per individuare la modifica responsabile, vedere Determinare la [non conformità](./determine-non-compliance.md).

## <a name="command-line"></a>Riga di comando

Le stesse informazioni disponibili nel portale possono essere recuperate con l'API REST (incluso [ARMClient](https://github.com/projectkudu/ARMClient)), Azure PowerShell e l'interfaccia della riga di comando di Azure (anteprima).
Per i dettagli completi sull'API REST, vedere le informazioni di riferimento di Analisi dei criteri di [Azure.For](/rest/api/policy-insights/) full details on the REST API, see the Azure Policy Insights reference. Le pagine di riferimento sull'API REST includono un pulsante verde "Prova", che consente di provare ogni operazione direttamente nel browser.

Usare ARMClient o uno strumento simile per gestire l'autenticazione in Azure per gli esempi di API REST.

### <a name="summarize-results"></a>Riepilogare i risultati

L'API REST consente di eseguire il riepilogo per contenitore, definizione o assegnazione. Ecco un esempio di riepilogo a livello di sottoscrizione usando Riepiloga per sottoscrizione di Azure Policy Insight:Here is an example of summarization at the subscription level using Azure Policy Insight's [Summarize For Subscription:](/rest/api/policy-insights/policystates/summarizeforsubscription)

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

L'output riepiloga la sottoscrizione. Nell'output di esempio seguente la conformità riepilogata è in corrispondenza di **value.results.nonCompliantResources** e **value.results.nonCompliantPolicies**. La richiesta fornisce maggiori dettagli, tra cui ogni assegnazione che ha costituito i numeri non conformi e le informazioni sulla definizione per ogni assegnazione. Ogni oggetto criteri nella gerarchia fornisce un **queryResultsUri** che può essere usato per ottenere altri dettagli a quel livello.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Eseguire query per le risorse

Nell'esempio precedente, **value.policyAssignments.policyDefinitions.results.queryResultsUri** fornisce un URI di esempio per tutte le risorse non conformi per una definizione di criteri specifica. Esaminando il valore **$filter**, IsCompliant è uguale (eq) a false, PolicyAssignmentId viene specificato per la definizione dei criteri e quindi PolicyDefinitionId stesso. Il motivo dell'inclusione di PolicyAssignmentId nel filtro è che PolicyDefinitionId potrebbe essere presente in diverse assegnazioni di criteri o iniziative con ambiti diversi. Specificando sia PolicyAssignmentId che PolicyDefinitionId, è possibile indicare in modo esplicito i risultati cercati. In precedenza per PolicyStates è stato usato **latest**, che imposta automaticamente una finestra temporale **from** e **to** delle ultime 24 ore.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Per brevità, la risposta di esempio seguente è stata tagliata in una singola risorsa non conforme. La risposta dettagliata contiene diversi dati che riguardano la risorsa, il criterio (o l'iniziativa) e l'assegnazione. Si noti che è anche possibile visualizzare quali parametri di assegnazione sono stati passati alla definizione dei criteri.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Visualizzare eventi

Quando si crea o si aggiorna una risorsa, viene generato un risultato di valutazione dei criteri. I risultati sono chiamati _eventi criteri_. Usare l'Uri seguente per visualizzare gli eventi criteri recenti associati alla sottoscrizione.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

I risultati saranno simili all'esempio seguente:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Per altre informazioni sull'esecuzione di query sugli eventi dei criteri, vedere l'articolo di riferimento [sugli eventi dei criteri](/rest/api/policy-insights/policyevents) di Azure.For more information about querying policy events, see the Azure Policy Events reference article.

### <a name="azure-powershell"></a>Azure PowerShell

Il modulo di Azure PowerShell per Criteri di Azure è disponibile in PowerShell Gallery come [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Grazie all'uso di PowerShellGet, è possibile installare il modulo usando `Install-Module -Name Az.PolicyInsights` (assicurarsi di avere la versione più recente di [Azure PowerShell](/powershell/azure/install-az-ps) installata):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Il modulo include i cmdlet seguenti:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Esempio: ottenere il riepilogo dello stato per i criteri più assegnati con il maggior numero di risorse non conformi.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Esempio: ottenere il record di stato per la risorsa valutata più di recente (l'impostazione predefinita è per timestamp in ordine decrescente).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Esempio: ottenere i dettagli per tutte le risorse di rete virtuale non conformi.

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Esempio: ottenere gli eventi correlati alle risorse di rete virtuale non conformi che si sono verificati dopo una data specifica.

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Il campo **PrincipalOid** può essere usato per ottenere un utente specifico con il cmdlet `Get-AzADUser` di Azure PowerShell. Sostituire **{principalOid}** con la risposta ottenuta dall'esempio precedente.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

Se si dispone di `AzureActivity` un'area di lavoro di Log [Analytics](../../../log-analytics/log-analytics-overview.md) con la soluzione Activity Log [Analytics](../../../azure-monitor/platform/activity-log-collect.md) associata alla sottoscrizione, è anche possibile visualizzare i risultati di non conformità del ciclo di valutazione usando semplici query Kusto e la `AzureActivity` tabella. Con i dettagli dei log di Monitoraggio di Azure è possibile configurare gli avvisi in modo da individuare le risorse non conformi.


![Conformità dei criteri di Azure usando i log di Monitoraggio di AzureAzure Policy Compliance using Azure Monitor logs](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Passaggi successivi

- Esaminare esempi in [Esempi di criteri di Azure](../samples/index.md).Review examples at Azure Policy samples .
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Rivedere [Informazioni sugli effetti dei criteri](../concepts/effects.md).
- Comprendere come creare criteri a livello di [codice.](programmatically-create.md)
- Informazioni su come [correggere le risorse non conformi.](remediate-resources.md)
- Esaminare le informazioni su cui si trova un gruppo di gestione con Organizzare le risorse con i gruppi di gestione di Azure.Review what a management group is with [Organize your resources with Azure management groups](../../management-groups/overview.md).