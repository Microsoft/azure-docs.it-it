---
title: Comprendere il funzionamento degli effetti
description: Le definizioni di Criteri di Azure hanno diversi effetti che determinano in che modo viene gestita e segnalata la conformità.
ms.date: 05/20/2020
ms.topic: conceptual
ms.openlocfilehash: 6c2dc8303b630eb01de5c3ad9e3504dfec5256bc
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746892"
---
# <a name="understand-azure-policy-effects"></a>Informazioni sugli effetti di Criteri di Azure

Ogni definizione di criteri in Criteri di Azure ha un effetto. Questo effetto determina cosa accade quando viene valutata la corrispondenza della regola dei criteri. Il comportamento degli effetti varia a seconda che riguardino una nuova risorsa, una risorsa aggiornata o una risorsa esistente.

Attualmente questi effetti sono supportati in una definizione dei criteri:

- [Append](#append)
- [Controllo](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Nega](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabilitato](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (anteprima)
- [EnforceRegoPolicy](#enforceregopolicy) (anteprima)
- [Modificare](#modify)

## <a name="order-of-evaluation"></a>Ordine di valutazione

Le richieste per creare o aggiornare una risorsa tramite Azure Resource Manager vengono valutate per prime da Criteri di Azure. Criteri di Azure crea un elenco di tutte le assegnazioni che si applicano alla risorsa e valuta quindi la risorsa rispetto a ogni definizione. Criteri di Azure elabora diversi effetti prima di passare la richiesta al provider di risorse appropriato. In questo modo si evita l'elaborazione non necessaria da parte di un provider di risorse quando una risorsa non soddisfa i controlli di governance progettati di Criteri di Azure.

- **Disabled** viene verificato per primo, per determinare se valutare la regola dei criteri.
- Vengono quindi valutati **Append** e **Modify**. Dal momento che entrambi possono modificare la richiesta, una modifica apportata potrebbe impedire l'attivazione di un effetto Audit o Deny.
- Successivamente viene valutato **Deny**. La valutazione di Deny prima di Audit impedisce la doppia registrazione di una risorsa non desiderata.
- **Audit** viene quindi valutato prima che la richiesta venga passata al provider di risorse.

Dopo che il provider di risorse restituisce un codice di riuscita, vengono valutati **AuditIfNotExists** e **DeployIfNotExists** per determinare se è necessaria un'ulteriore registrazione della conformità o un'altra azione.

Attualmente non è previsto alcun ordine di valutazione per gli effetti **EnforceOPAConstraint** o **EnforceRegoPolicy**.

## <a name="append"></a>Accoda

Append viene usato per aggiungere altri campi alla risorsa richiesta durante la creazione o l'aggiornamento. Un esempio comune è la specifica di indirizzi IP consentiti per una risorsa di archiviazione.

> [!IMPORTANT]
> Append è destinato all'uso con proprietà di non tag. Sebbene Append possa aggiungere tag a una risorsa durante una richiesta di creazione o aggiornamento, è consigliabile usare l'effetto [Modify](#modify) per i tag.

### <a name="append-evaluation"></a>Valutazione di Append

Append viene valutato prima che la richiesta venga elaborata da un provider di risorse durante la creazione o l'aggiornamento di una risorsa. Append aggiunge campi alla risorsa quando viene soddisfatta la condizione **if** della regola dei criteri. Se l'effetto Append sostituisce un valore nella richiesta originale con un valore diverso, agisce come effetto Deny e rifiuta la richiesta. Per accodare un nuovo valore a una matrice esistente, usare la versione **\[\*\]** dell'alias.

Quando una definizione dei criteri che usa l'effetto Append viene eseguita come parte di un ciclo di valutazione, non apporta modifiche alle risorse già esistenti. Al contrario, contrassegna qualsiasi risorsa che soddisfi la condizione **if** come non conforme.

### <a name="append-properties"></a>Proprietà di Append

Un effetto Append ha solo una matrice **details** obbligatoria. Essendo una matrice, **details** può richiedere una coppia **campo/valore** singola o coppie multiple. Fare riferimento alla [struttura di definizione](definition-structure.md#fields) per l'elenco dei campi accettabili.

### <a name="append-examples"></a>Esempi di Append

Esempio 1: coppia **campo/valore** singola che usa un [alias](definition-structure.md#aliases) diverso da **\[\*\]** con un **valore** di matrice per impostare le regole IP in un account di archiviazione. Quando l'alias diverso da **\[\*\]** è una matrice, l'effetto accoda il **valore** come intera matrice. Se la matrice esiste già, si verifica un evento Deny per effetto del conflitto.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Esempio 2: coppia **campo/valore** singola che usa un [alias](definition-structure.md#aliases) **\[\*\]** con un **valore** di matrice per impostare le regole IP in un account di archiviazione. Usando l'alias **\[\*\]** , l'effetto accoda il **valore** a una matrice potenzialmente già esistente. Se la matrice non esiste, viene creata.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```




## <a name="audit"></a>Audit

Audit viene usato per creare un evento di avviso nel log attività quando viene valutata una risorsa non conforme, ma non arresta la richiesta.

### <a name="audit-evaluation"></a>Valutazione di Audit

Audit è l'ultimo effetto controllato da Criteri di Azure durante la creazione o l'aggiornamento di una risorsa. La risorsa viene quindi inviata da Criteri di Azure al provider di risorse. Audit funziona allo stesso modo per una richiesta di risorse e un ciclo di valutazione. Criteri di Azure aggiunge un'operazione `Microsoft.Authorization/policies/audit/action` al log attività e contrassegna la risorsa come non conforme.

### <a name="audit-properties"></a>Proprietà di Audit

L'effetto Audit non ha proprietà aggiuntive da usare nella condizione **then** della definizione dei criteri.

### <a name="audit-example"></a>Esempio di Audit

Esempio: uso dell'effetto Audit.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists consente il controllo sulle risorse che corrispondono alla condizione **if**, ma non ha i componenti specificati in **details** nella condizione **then**.

### <a name="auditifnotexists-evaluation"></a>Valutazione di AuditIfNotExists

AuditIfNotExists viene eseguito dopo che un provider di risorse ha gestito una richiesta di creazione o aggiornamento di risorse e ha restituito un codice di stato con esito positivo. L'effetto Audit si verifica se non ci sono risorse correlate o se le risorse definite da **ExistenceCondition** non restituiscono true. Criteri di Azure aggiunge un'operazione `Microsoft.Authorization/policies/audit/action` al log attività allo stesso modo dell'effetto Audit. Quando è attivato, la risorsa che ha soddisfatto la condizione **if** è la risorsa contrassegnata come non conforme.

### <a name="auditifnotexists-properties"></a>Proprietà di AuditIfNotExists

La proprietà **details** degli effetti AuditIfNotExists ha tutte le sottoproprietà che definiscono le risorse correlate a cui corrispondere.

- **Type** [obbligatorio]
  - Specifica il tipo della risorsa correlata a cui corrispondere.
  - Se **details.type** è un tipo di risorsa sotto la condizione di risorsa **if**, il criterio esegue una query per le risorse di questo **tipo** nell'ambito della risorsa valutata. In caso contrario, il criterio esegue la query all'interno dello stesso gruppo di risorse della risorsa valutata.
- **Name** (facoltativo)
  - Specifica il nome esatto della risorsa a cui corrispondere e fa sì che il criterio recuperi una risorsa specifica invece di tutte le risorse del tipo specificato.
  - Quando i valori della condizione per **if.field.type** e **then.details. type** corrispondono, allora **Name** diventa _obbligatorio_ e deve essere `[field('name')]`. Tuttavia, deve essere considerato invece un effetto [audit](#audit).
- **ResourceGroupName** (facoltativo)
  - Consente che la corrispondenza della risorsa correlata provenga da un gruppo di risorse diverso.
  - Non è applicabile se **type** è una risorsa sottostante la risorsa della condizione **if**.
  - L'impostazione predefinita è il gruppo di risorse della risorsa della condizione **if**.
- **ExistenceScope** (facoltativo)
  - I valori consentiti sono _Subscription_ e _ResourceGroup_.
  - Imposta l'ambito dove recuperare la risorsa correlata a cui corrispondere.
  - Non è applicabile se **type** è una risorsa sottostante la risorsa della condizione **if**.
  - Per _ResourceGroup_, si limita al gruppo di risorse della risorsa della condizione **if** o al gruppo di risorse specificato in **ResourceGroupName**.
  - Per _Subscription_, esegue una query nell'intera sottoscrizione per la risorsa correlata.
  - L'impostazione predefinita è _ResourceGroup_.
- **ExistenceCondition** (facoltativo)
  - Se non specificato, qualsiasi risorsa correlata di **type** soddisfa l'effetto e non attiva il controllo.
  - Usa lo stesso linguaggio della regola dei criteri per la condizione **if**, ma viene valutato singolarmente rispetto a ogni risorsa correlata.
  - Se qualsiasi risorsa correlata corrispondente restituisce true, l'effetto è soddisfatto e non attiva il controllo.
  - Può usare [field()] per controllare l'equivalenza con i valori nella condizione **if**.
  - Ad esempio, può essere usato per convalidare che la risorsa padre (nella condizione **if**) sia nello stesso percorso della risorsa della risorsa correlata corrispondente.

### <a name="auditifnotexists-example"></a>Esempio di AuditIfNotExists

Esempio: valuta le macchine virtuali per determinare se esiste l'estensione antimalware, quindi consente di controllare quando manca.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deny"></a>Nega

Deny viene usato per impedire una richiesta di risorse che non corrisponde agli standard definiti tramite una definizione dei criteri e che genera un errore della richiesta.

### <a name="deny-evaluation"></a>Valutazione di Deny

Quando si crea o si aggiorna una risorsa corrispondente, Deny impedisce la richiesta prima che sia inviata al provider di risorse. La richiesta viene restituita come `403 (Forbidden)`. Nel portale l'accesso negato può essere visualizzato come stato della distribuzione impedita dall'assegnazione dei criteri.

Durante la valutazione di risorse esistenti, le risorse che corrispondono a una definizione di criteri Deny vengono contrassegnate come non conformi.

### <a name="deny-properties"></a>Proprietà di Deny

L'effetto Deny non ha proprietà aggiuntive da usare nella condizione **then** della definizione dei criteri.

### <a name="deny-example"></a>Esempio di Deny

Esempio: uso dell'effetto Deny.

```json
"then": {
    "effect": "deny"
}
```


## <a name="deployifnotexists"></a>DeployIfNotExists

Analogamente ad AuditIfNotExists, una definizione dei criteri DeployIfNotExists esegue una distribuzione di modelli quando la condizione viene soddisfatta.

> [!NOTE]
> I [Modelli nidificati](../../../azure-resource-manager/templates/linked-templates.md#nested-template) sono supportati con **deployIfNotExists** ma i [Modelli collegati](../../../azure-resource-manager/templates/linked-templates.md#linked-template) non sono attualmente supportati.

### <a name="deployifnotexists-evaluation"></a>Valutazione di DeployIfNotExists

DeployIfNotExists viene eseguito per circa 15 minuti dopo che un provider di risorse ha gestito una richiesta di creazione o aggiornamento di risorse e ha restituito un codice di stato con esito positivo. Una distribuzione di modello si verifica se non ci sono risorse correlate o se le risorse definite da **ExistenceCondition** non restituiscono true.
La durata della distribuzione dipende dalla complessità delle risorse incluse nel modello.

Durante un ciclo di valutazione, le definizioni dei criteri con un effetto DeployIfNotExists che corrispondono alle risorse vengono contrassegnate come non conformi, ma non vengono eseguite azioni sulla risorsa stessa. È possibile correggere le risorse non conformi esistenti con un'[attività di correzione](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>Proprietà di DeployIfNotExists

La proprietà **details** dell'effetto DeployIfNotExists ha tutte le sottoproprietà che definiscono le risorse correlate a cui corrispondere e la distribuzione di modelli da eseguire.

- **Type** [obbligatorio]
  - Specifica il tipo della risorsa correlata a cui corrispondere.
  - Inizia cercando di recuperare una risorsa sottostante la risorsa della condizione **if**, quindi esegue una query all'interno dello stesso gruppo di risorse come risorsa della condizione **if**.
- **Name** (facoltativo)
  - Specifica il nome esatto della risorsa a cui corrispondere e fa sì che il criterio recuperi una risorsa specifica invece di tutte le risorse del tipo specificato.
  - Quando i valori della condizione per **if.field.type** e **then.details. type** corrispondono, allora **Name** diventa _obbligatorio_ e deve essere `[field('name')]`.
- **ResourceGroupName** (facoltativo)
  - Consente che la corrispondenza della risorsa correlata provenga da un gruppo di risorse diverso.
  - Non è applicabile se **type** è una risorsa sottostante la risorsa della condizione **if**.
  - L'impostazione predefinita è il gruppo di risorse della risorsa della condizione **if**.
  - Se viene eseguita una distribuzione di modelli, la distribuzione avviene nel gruppo di risorse di questo valore.
- **ExistenceScope** (facoltativo)
  - I valori consentiti sono _Subscription_ e _ResourceGroup_.
  - Imposta l'ambito dove recuperare la risorsa correlata a cui corrispondere.
  - Non è applicabile se **type** è una risorsa sottostante la risorsa della condizione **if**.
  - Per _ResourceGroup_, si limita al gruppo di risorse della risorsa della condizione **if** o al gruppo di risorse specificato in **ResourceGroupName**.
  - Per _Subscription_, esegue una query nell'intera sottoscrizione per la risorsa correlata.
  - L'impostazione predefinita è _ResourceGroup_.
- **ExistenceCondition** (facoltativo)
  - Se non specificato, qualsiasi risorsa correlata di **type** soddisfa l'effetto e non attiva la distribuzione.
  - Usa lo stesso linguaggio della regola dei criteri per la condizione **if**, ma viene valutato singolarmente rispetto a ogni risorsa correlata.
  - Se qualsiasi risorsa correlata corrispondente restituisce true, l'effetto è soddisfatto e non attiva la distribuzione.
  - Può usare [field()] per controllare l'equivalenza con i valori nella condizione **if**.
  - Ad esempio, può essere usato per convalidare che la risorsa padre (nella condizione **if**) sia nello stesso percorso della risorsa della risorsa correlata corrispondente.
- **roleDefinitionIds** [required]
  - Questa proprietà deve contenere una matrice di stringhe che corrispondono all'ID ruolo di controllo degli accessi in base al ruolo accessibile dalla sottoscrizione. Per altre informazioni, vedere [Correzione: configurare la definizione dei criteri](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (facoltativo)
  - I valori consentiti sono _Subscription_ e _ResourceGroup_.
  - Imposta il tipo di distribuzione da attivare. _Subscription_ indica una [distribuzione a livello di sottoscrizione](../../../azure-resource-manager/templates/deploy-to-subscription.md), _ResourceGroup_ indica una distribuzione a un gruppo di risorse.
  - Una proprietà _location_ deve essere specificata in _Deployment_ quando si usano distribuzioni a livello di sottoscrizione.
  - L'impostazione predefinita è _ResourceGroup_.
- **Deployment** [obbligatorio]
  - Questa proprietà deve contenere la distribuzione di modelli completa passata all'API PUT `Microsoft.Resources/deployments`. Per altre informazioni, vedere l'[API REST per le distribuzioni](/rest/api/resources/deployments).

  > [!NOTE]
  > Tutte le funzioni all'interno della proprietà **Deployment** vengono valutate come componenti del modello, non del criterio. L'eccezione è costituita dalla proprietà **parameters** che passa i valori dai criteri al modello. **value** in questa sezione sotto un nome di parametro del modello viene usato per eseguire il passaggio del valore (vedere _fullDbName_ nell'esempio di DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Esempio di DeployIfNotExists

Esempio: valuta i database SQL Server per determinare se transparentDataEncryption è abilitato. In caso contrario, viene eseguita una distribuzione per abilitarlo.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="disabled"></a>Disabled

Questo effetto è utile per gli scenari di test o quando la definizione dei criteri ha parametrizzato l'effetto. Grazie a questa flessibilità è possibile disabilitare una singola assegnazione invece di disabilitare tutte le assegnazioni di quei criteri.

Un'alternativa all'effetto Disabled è **enforcementMode, che è impostato nell'assegnazione dei criteri.
Quando **enforcementMode** viene _disabilitato_, le risorse vengono comunque valutate. La registrazione, ad esempio i log attività, e l'effetto del criterio non si verificano. Per altre informazioni, vedere [assegnazione di criteri - modalità di imposizione](./assignment-structure.md#enforcement-mode).


## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Questo effetto viene usato con una _modalità_di definizione di criteri di `Microsoft.Kubernetes.Data`. Viene usato per passare le regole di controllo dell'ammissione di Gatekeeper v3 definite con [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) in [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) per i cluster Kubernetes in Azure.

> [!NOTE]
> [Criteri di Azure per Kubernetes](./policy-for-kubernetes.md) è in anteprima e supporta solo i pool di nodi Linux e le definizioni dei criteri predefinite.

### <a name="enforceopaconstraint-evaluation"></a>Valutazione EnforceOPAConstraint

Il controller di ammissione di Open Policy Agent valuta tutte le nuove richieste nel cluster in tempo reale.
Ogni 15 minuti, viene completata un'analisi completa del cluster e i risultati vengono segnalati a Criteri di Azure.

### <a name="enforceopaconstraint-properties"></a>Proprietà EnforceOPAConstraint

Nella proprietà **details** dell'effetto EnforceOPAConstraint sono presenti le sottoproprietà che descrivono la regola di controllo dell'ammissione Gatekeeper v3.

- **constraintTemplate** [obbligatorio]
  - Il modello Constraint CustomResourceDefinition (CRD) che definisce nuovi vincoli. Il modello definisce la logica Rego, lo schema Constraint e i parametri Constraint passati tramite **valori** da Criteri di Azure.
- **constraint** [obbligatorio]
  - L'implementazione CRD del modello Constraint. Usa i parametri passati tramite **valori** come `{{ .Values.<valuename> }}`. Nell'esempio seguente questi valori sono `{{ .Values.cpuLimit }}` e `{{ .Values.memoryLimit }}`.
- **valori** [facoltativo]
  - Definisce tutti i parametri e i valori da passare a Constraint. Ogni valore deve esistere nel modello Constraint CRD.

### <a name="enforceopaconstraint-example"></a>Esempio EnforceOPAConstraint

Esempio: Regola di controllo dell'ammissione di Gatekeeper v3 per impostare i limiti delle risorse di memoria e CPU del contenitore in Kubernetes.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Questo effetto viene usato con una _modalità_di definizione dei criteri di `Microsoft.ContainerService.Data`. Viene usato per passare le regole di controllo dell'ammissione di Gatekeeper v2 definite con [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) in [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) nei [servizi Azure Kubernetes](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Criteri di Azure per Kubernetes](./policy-for-kubernetes.md) è in anteprima e supporta solo i pool di nodi Linux e le definizioni dei criteri predefinite. Le definizioni dei criteri predefinite sono nella categoria **Kubernetes**. Le definizioni dei criteri in anteprima limitata con l'effetto **EnforceRegoPolicy** e la categoria **Servizio Kubernetes** sono in fase di _deprecazione_. Usare l'effetto aggiornato [EnforceOPAConstraint](#enforceopaconstraint).

### <a name="enforceregopolicy-evaluation"></a>Valutazione EnforceRegoPolicy

Il controller di ammissione di Open Policy Agent valuta tutte le nuove richieste nel cluster in tempo reale.
Ogni 15 minuti, viene completata un'analisi completa del cluster e i risultati vengono segnalati a Criteri di Azure.

### <a name="enforceregopolicy-properties"></a>Proprietà EnforceRegoPolicy

Nella proprietà **details** dell'effetto EnforceRegoPolicy sono presenti le sottoproprietà che descrivono la regola di controllo dell'ammissione Gatekeeper v2.

- **policyId** [obbligatorio]
  - Un nome univoco passato come parametro alla regola di controllo dell'ammissione Rego.
- **policy** [obbligatorio]
  - Specifica l'URI della regola di controllo dell'ammissione Rego.
- **policyParameters** [facoltativo]
  - Definisce tutti i parametri e i valori da passare al criterio Rego.

### <a name="enforceregopolicy-example"></a>Esempio EnforceRegoPolicy

Esempio: Regola di controllo di ammissione Gatekeeper V2 per consentire solo le immagini del contenitore specificate in AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="modify"></a>Modifica

Modify viene usato per aggiungere, aggiornare o rimuovere tag in una risorsa durante la creazione o l'aggiornamento. Un esempio comune è l'aggiornamento di tag per le risorse come costCenter. Un criterio Modify deve sempre avere `mode` impostato su _Indicizzato_, a meno che la risorsa di destinazione non sia un gruppo di risorse. È possibile correggere le risorse non conformi esistenti con un'[attività di correzione](../how-to/remediate-resources.md). Una regola Modify singola può avere un numero qualsiasi di operazioni.

> [!IMPORTANT]
> Modify è attualmente disponibile solo per l'uso con tag. Se si gestiscono i tag, è consigliabile usare Modify anziché Append in quanto Modify fornisce tipi di operazione aggiuntivi e la possibilità di correggere le risorse esistenti. Tuttavia, Append è consigliabile se non è possibile creare un'identità gestita.

### <a name="modify-evaluation"></a>Valutazione di Modify

Modify viene valutato prima che la richiesta venga elaborata da un provider di risorse durante la creazione o l'aggiornamento di una risorsa. Modify aggiunge o aggiorna tag nella risorsa quando viene soddisfatta la condizione **if** della regola dei criteri.

Quando una definizione dei criteri che usa l'effetto Modify viene eseguita come parte di un ciclo di valutazione, non apporta modifiche alle risorse già esistenti. Al contrario, contrassegna qualsiasi risorsa che soddisfi la condizione **if** come non conforme.

### <a name="modify-properties"></a>Proprietà Modify

La proprietà **details** dell'effetto Modify include tutte le sottoproprietà che definiscono le autorizzazioni necessarie per la correzione e le **operazioni** usate per aggiungere, aggiornare o rimuovere i valori dei tag.

- **roleDefinitionIds** [required]
  - Questa proprietà deve contenere una matrice di stringhe che corrispondono all'ID ruolo di controllo degli accessi in base al ruolo accessibile dalla sottoscrizione. Per altre informazioni, vedere [Correzione: configurare la definizione dei criteri](../how-to/remediate-resources.md#configure-policy-definition).
  - Il ruolo definito deve includere tutte le operazioni concesse al ruolo [Contributor](../../../role-based-access-control/built-in-roles.md#contributor).
- **operations** [obbligatorio]
  - Una matrice di tutte le operazioni di tag da completare sulle risorse corrispondenti.
  - Proprietà:
    - **operation** [obbligatorio]
      - Definisce l'azione da intraprendere su una risorsa corrispondente. Le opzioni sono: _addOrReplace_, _Add_, _Remove_. _Add_ si comporta in modo analogo all'effetto [Append](#append).
    - **field** [obbligatorio]
      - Il tag per aggiungere, sostituire o rimuovere. I nomi di tag devono rispettare la stessa convenzione di denominazione per altri [campi](./definition-structure.md#fields).
    - **value** (facoltativo)
      - Il valore su cui impostare i tag.
      - Questa proprietà è obbligatoria se **operation** è _addOrReplace_ o _Add_.

### <a name="modify-operations"></a>Modificare le operazioni

Le matrice di proprietà **operations** consente di modificare diversi tag in modi diversi da una singola definizione di criteri. Ogni operazione è costituita da proprietà **operation**, **field**, e **value**. Operation determina l'attività di correzione per i tag, field determina quale tag viene modificato e value definisce la nuova impostazione per il tag. L'esempio seguente apporta le modifiche seguenti ai tag:

- Imposta il tag `environment` su "Test", anche se esiste già con un valore diverso.
- Rimuove il tag `TempResource`.
- Imposta il tag `Dept` sul parametro dei criteri _DeptName_ configurato nell'assegnazione dei criteri.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

Nella proprietà **operation** sono disponibili le opzioni seguenti:

|Operazione |Descrizione |
|-|-|
|addOrReplace |Aggiunge il tag e il valore definiti alla risorsa, anche se il tag esiste già con un valore diverso. |
|Add |Aggiunge il tag e il valore definiti alla risorsa. |
|Rimuovere |Rimuove il tag definito dalla risorsa. |

### <a name="modify-examples"></a>Esempi di Modify

Esempio 1: Aggiungere il tag `environment` e sostituire i tag `environment` esistenti con "Test":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Esempio 2: Rimuovere il tag `env` e aggiungere il tag `environment` o sostituire i tag `environment` esistenti con un valore con parametri:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```



## <a name="layering-policy-definitions"></a>Disposizione delle definizioni dei criteri su più livelli

Una risorsa potrebbe essere interessata da diverse assegnazioni. Queste assegnazioni possono essere nello stesso ambito o in ambiti diversi. È anche probabile che ognuna di queste assegnazioni abbia un effetto diverso definito. La condizione e l'effetto per ogni criterio vengono valutati in modo indipendente. Ad esempio:

- Criterio 1
  - Limita la posizione delle risorse a "westus"
  - Assegnato alla sottoscrizione A
  - Effetto Deny
- Criterio 2
  - Limita la posizione delle risorse a "eastus"
  - Assegnato al gruppo di risorse B nella sottoscrizione A
  - Effetto Audit
  
Questa configurazione produrrebbe il risultato seguente:

- Qualsiasi risorsa già presente nel gruppo di risorse B in "eastus" è conforme al criterio 2 e non conforme al criterio 1
- Qualsiasi risorsa già presente nel gruppo di risorse B non in "eastus" è conforme al criterio 2 e non conforme al criterio 1 se non in "westus"
- Qualsiasi nuova risorsa nella sottoscrizione A non in "westus" viene rifiutata dal criterio 1
- Qualsiasi nuova risorsa nella sottoscrizione A e nel gruppo di risorse B in "westus" è creata e non conforme al criterio 2

Se entrambi i criteri 1 e 2 avessero l'effetto Deny, la situazione diventerebbe la seguente:

- Qualsiasi risorsa già presente nel gruppo di risorse B non in "eastus" non è conforme al criterio 2
- Qualsiasi risorsa già presente nel gruppo di risorse B non in "westus" non è conforme al criterio 1
- Qualsiasi nuova risorsa nella sottoscrizione A non in "westus" viene rifiutata dal criterio 1
- Qualsiasi nuova risorsa nel gruppo di risorse B della sottoscrizione A viene negata

Ogni assegnazione viene valutata singolarmente. Di conseguenza, non c'è alcuna possibilità di inserire per sbaglio una risorsa a causa delle differenze nell'ambito. Il risultato finale della disposizione delle definizioni dei criteri su più livelli è considerato **cumulativo più restrittivo**. Ad esempio, se entrambi i criteri 1 e 2 avessero un effetto Deny, una risorsa verrebbe bloccata dalle definizioni dei criteri sovrapposte e in conflitto. Se è ancora necessario che la risorsa venga creata nell'ambito di destinazione, rivedere le esclusioni per ogni assegnazione per verificare che le assegnazioni dei criteri corretti interessino gli ambiti corretti.

## <a name="next-steps"></a>Passaggi successivi

- Vedere gli esempi in [Esempi di Criteri di Azure](../samples/index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](definition-structure.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Informazioni su come [ottenere dati sulla conformità](../how-to/get-compliance-data.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
- Rivedere le caratteristiche di un gruppo di gestione illustrate in [Organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).
