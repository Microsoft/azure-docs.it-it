---
title: Informazioni su criteri di Azure per il servizio Azure Kubernetes
description: Informazioni su come i criteri di Azure usano Rego e Open Policy Agent per gestire i cluster nel servizio Azure Kubernetes.
ms.date: 03/18/2020
ms.topic: conceptual
ms.openlocfilehash: f6c70d676914cf861ecc378efc4ec23a78879f6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187716"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Comprendere i criteri di Azure per il servizio Azure Kubernetes

Criteri di Azure si integra con il [servizio Azure Kubernetes](../../../aks/intro-kubernetes.md) (AKS) per applicare misure e misure di sicurezza su larga scala nei cluster in modo centralizzato e coerente.
Estendendo l'uso di [Gatekeeper](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated) V2, un _webhook del controller di ammissione_ per [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), criteri di Azure consente di gestire e segnalare lo stato di conformità delle risorse di Azure e dei cluster AKS da un'unica posizione.

> [!NOTE]
> Criteri di Azure per AKS è in anteprima limitata e supporta solo le definizioni predefinite dei criteri.

## <a name="overview"></a>Panoramica

Per abilitare e usare criteri di Azure per AKS con il cluster AKS, intraprendere le azioni seguenti:

- [Acconsenti esplicitamente alle funzionalità di anteprima](#opt-in-for-preview)
- [Installare il componente aggiuntivo criteri di Azure](#installation-steps)
- [Assegnare una definizione di criteri per AKS](#built-in-policies)
- [Attendi convalida](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Consenso esplicito per l'anteprima

Prima di installare il componente aggiuntivo di criteri di Azure o di abilitare le funzionalità del servizio, la sottoscrizione deve abilitare il provider di risorse **Microsoft. servizio contenitore** e il provider di risorse **Microsoft. PolicyInsights** , quindi essere approvati per partecipare all'anteprima. Per partecipare all'anteprima, seguire questa procedura nell'portale di Azure o con l'interfaccia della riga di comando di Azure:

- Portale di Azure:

  1. Registrare i provider di risorse **Microsoft. servizio contenitore** e **Microsoft. PolicyInsights** . Per i passaggi, vedere [provider e tipi di risorse](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

  1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

     :::image type="content" source="../media/rego-for-aks/search-policy.png" alt-text="Cercare Criteri di Azure in Tutti i servizi" border="false":::

  1. Selezionare **Anteprima join** sul lato sinistro della pagina Criteri di Azure.

     :::image type="content" source="../media/rego-for-aks/join-aks-preview.png" alt-text="Aggiungere i criteri per l'anteprima di AKS" border="false":::

  1. Selezionare la riga della sottoscrizione che si vuole aggiungere all'anteprima.

  1. Selezionare il pulsante **consenso esplicito** nella parte superiore dell'elenco di sottoscrizioni.

- Interfaccia della riga di comando di Azure:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataPlaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Componente aggiuntivo criteri di Azure

Il _componente aggiuntivo criteri di Azure_ per Kubernetes connette il servizio criteri di Azure al controller di ammissione Gatekeeper. Il componente aggiuntivo, installato nello spazio dei nomi di _criteri di Azure_ , comporta le funzioni seguenti:

- Verifica con criteri di Azure per le assegnazioni al cluster AKS
- Scarica e memorizza nella cache i dettagli dei criteri, inclusa la definizione del criterio _rego_ , come **configmaps**
- Esegue un controllo di conformità dell'analisi completa sul cluster AKS
- Riporta i dettagli di controllo e conformità a criteri di Azure

### <a name="installing-the-add-on"></a>Installazione del componente aggiuntivo

#### <a name="prerequisites"></a>Prerequisiti

Prima di installare il componente aggiuntivo nel cluster AKS, è necessario installare l'estensione di anteprima. Questa operazione viene eseguita con l'interfaccia della riga di comando di Azure:

1. È necessaria l'interfaccia della riga di comando di Azure versione 2.0.62 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

1. Il cluster AKS deve avere la versione _1,10_ o successiva. Usare lo script seguente per convalidare la versione del cluster AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installare la versione _0.4.0_ dell'estensione di anteprima dell'interfaccia della riga `aks-preview`di comando di Azure per AKS,:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Se in precedenza è stata installata l'estensione _AKS-Preview_ , installare gli aggiornamenti tramite `az extension update --name aks-preview` il comando.

#### <a name="installation-steps"></a>Procedura di installazione

Una volta completati i prerequisiti, installare il componente aggiuntivo criteri di Azure nel cluster AKS che si vuole gestire.

- Portale di Azure

  1. Avviare il servizio AKS nella portale di Azure facendo clic su **tutti i servizi**, quindi cercando e selezionando **Servizi Kubernetes**.

  1. Selezionare uno dei cluster AKS.

  1. Selezionare **criteri (anteprima)** sul lato sinistro della pagina del servizio Kubernetes.

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="Criteri del cluster AKS" border="false":::

  1. Nella pagina principale selezionare il pulsante **Abilita componente aggiuntivo** .

     :::image type="content" source="../media/rego-for-aks/enable-policy-add-on.png" alt-text="Abilitare i criteri di Azure per il componente aggiuntivo AKS" border="false":::

     > [!NOTE]
     > Se il pulsante **Abilita componente** aggiuntivo è disattivato, la sottoscrizione non è ancora stata aggiunta all'anteprima. Per i passaggi necessari, vedere [consenso esplicito per l'anteprima](#opt-in-for-preview) .

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Frequenza di convalida e Reporting

Il componente aggiuntivo archivia i criteri di Azure per le modifiche alle assegnazioni dei criteri ogni 5 minuti. Durante questo ciclo di aggiornamento, il componente aggiuntivo rimuove tutti i _configmaps_ nello spazio dei nomi di _criteri di Azure_ , quindi ricrea il _Configmaps_ per l'uso del gatekeeper.

> [!NOTE]
> Anche se un _amministratore del cluster_ può disporre dell'autorizzazione per lo spazio dei nomi di criteri di _Azure_ , non è consigliabile né supportato per apportare modifiche allo spazio dei nomi. Eventuali modifiche manuali apportate andranno perse durante il ciclo di aggiornamento.

Ogni 5 minuti, il componente aggiuntivo chiama per eseguire un'analisi completa del cluster. Dopo aver raccolto i dettagli dell'analisi completa e di eventuali valutazioni in tempo reale da parte del gatekeeper delle modifiche apportate al cluster, il componente aggiuntivo segnala i risultati ai criteri di Azure per l'inclusione nei [Dettagli di conformità](../how-to/get-compliance-data.md) come qualsiasi assegnazione di criteri di Azure. Durante il ciclo di controllo vengono restituiti solo i risultati per le assegnazioni di criteri attive.

## <a name="policy-language"></a>Lingua dei criteri

La struttura del linguaggio di criteri di Azure per la gestione di AKS segue quella dei criteri esistenti. L'effetto _EnforceRegoPolicy_ viene usato per gestire i cluster AKS e accetta le proprietà dei _Dettagli_ specifiche per l'uso di OPA e Gatekeeper V2. Per informazioni dettagliate ed esempi, vedere l'effetto [EnforceRegoPolicy](effects.md#enforceregopolicy) .

Come parte della proprietà _Details. Policy_ nella definizione dei criteri, i criteri di Azure passano l'URI di un criterio rego al componente aggiuntivo. Rego è il linguaggio supportato da OPA e Gatekeeper per convalidare o mutare una richiesta al cluster Kubernetes. Grazie al supporto di uno standard esistente per la gestione Kubernetes, i criteri di Azure consentono di riutilizzare le regole esistenti e di associarle ai criteri di Azure per un'esperienza di creazione di report di conformità cloud unificata. Per ulteriori informazioni, vedere [che cos'è rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Criteri predefiniti

Per trovare i criteri predefiniti per la gestione di AKS usando il portale di Azure, seguire questa procedura:

1. Avviare il servizio criteri di Azure nella portale di Azure. Selezionare **tutti i servizi** nel riquadro a sinistra e quindi cercare e selezionare **criteri**.

1. Nel riquadro sinistro della pagina Criteri di Azure selezionare **definizioni**.

1. Dall'elenco a discesa categoria, usare **Select All (Seleziona tutto** ) per cancellare il filtro e quindi selezionare **servizio Kubernetes**.

1. Selezionare la definizione dei criteri, quindi selezionare il pulsante **assegna** .

> [!NOTE]
> Quando si assegnano i criteri di Azure per la definizione AKS, l' **ambito** deve includere la risorsa cluster AKS.

In alternativa, usare la Guida introduttiva [assegnare un criterio al portale](../assign-policy-portal.md) per trovare e assegnare un criterio AKS. Cercare una definizione dei criteri Kubernetes invece dell'esempio ' macchine virtuali di controllo '.

> [!IMPORTANT]
> I criteri predefiniti nel servizio Category **Kubernetes** sono utilizzabili solo con AKS.

## <a name="logging"></a>Registrazione

### <a name="azure-policy-add-on-logs"></a>Log del componente aggiuntivo di criteri di Azure

Come controller/contenitore Kubernetes, il componente aggiuntivo criteri di Azure mantiene i log nel cluster AKS. I log vengono esposti nella pagina **Insights** del cluster AKS. Per altre informazioni, vedere [comprendere le prestazioni del cluster AKS con monitoraggio di Azure per i contenitori](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Log Gatekeeper

Per abilitare i log di Gatekeeper per le nuove richieste di risorse, seguire i passaggi in [abilitare ed esaminare i log del nodo master di Kubernetes in AKS](../../../aks/view-master-logs.md).
Ecco una query di esempio per visualizzare gli eventi negati sulle nuove richieste di risorse:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Per visualizzare i log dei contenitori gatekeeper, seguire i passaggi descritti in [abilitare ed esaminare i log del nodo master di Kubernetes in AKS](../../../aks/view-master-logs.md) e selezionare l'opzione _Kube-apiserver_ nel riquadro **impostazioni di diagnostica** .

## <a name="remove-the-add-on"></a>Rimuovere il componente aggiuntivo

Per rimuovere il componente aggiuntivo criteri di Azure dal cluster AKS, usare l'interfaccia della riga di comando portale di Azure o Azure:

- Portale di Azure

  1. Avviare il servizio AKS nella portale di Azure facendo clic su **tutti i servizi**, quindi cercando e selezionando **Servizi Kubernetes**.

  1. Selezionare il cluster AKS in cui si vuole disabilitare il componente aggiuntivo criteri di Azure.

  1. Selezionare **criteri (anteprima)** sul lato sinistro della pagina del servizio Kubernetes.

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="Criteri del cluster AKS" border="false":::

  1. Nella pagina principale selezionare il pulsante **Disabilita componente aggiuntivo** .

     :::image type="content" source="../media/rego-for-aks/disable-policy-add-on.png" alt-text="Disabilitare i criteri di Azure per il componente aggiuntivo AKS" border="false":::

- Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Dati di diagnostica raccolti dal componente aggiuntivo criteri di Azure

Il componente aggiuntivo criteri di Azure per Kubernetes raccoglie i dati di diagnostica del cluster limitati. Questi dati diagnostici sono dati tecnici essenziali correlati al software e alle prestazioni. Viene usata nei modi seguenti:

- Mantieni aggiornato il componente aggiuntivo criteri di Azure
- Mantieni il componente aggiuntivo di criteri di Azure protetto, affidabile, efficiente
- Migliorare il componente aggiuntivo criteri di Azure per l'analisi aggregata dell'utilizzo del componente aggiuntivo

Le informazioni raccolte dal componente aggiuntivo non sono dati personali. Sono attualmente raccolti i dettagli seguenti:

- Versione dell'agente componente aggiuntivo criteri di Azure
- Tipo di cluster
- Area del cluster
- Gruppo di risorse cluster
- ID risorsa cluster
- ID sottoscrizione cluster
- Sistema operativo cluster (ad esempio: Linux)
- Città del cluster (ad esempio: Seattle)
- Stato o provincia del cluster (ad esempio: Washington)
- Paese o regione del cluster (esempio: Stati Uniti)
- Eccezioni/errori rilevati dal componente aggiuntivo criteri di Azure durante l'installazione dell'agente durante la valutazione dei criteri
- Numero di criteri Gatekeeper non installati dal componente aggiuntivo criteri di Azure

## <a name="next-steps"></a>Passaggi successivi

- Esaminare gli esempi in [esempi di criteri di Azure](../samples/index.md).
- Esaminare la [struttura della definizione dei criteri](definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](effects.md).
- Informazioni su come [creare criteri a livello di codice](../how-to/programmatically-create.md).
- Informazioni su come [ottenere i dati di conformità](../how-to/get-compliance-data.md).
- Informazioni su come monitorare e [aggiornare le risorse non conformi](../how-to/remediate-resources.md).
- Esaminare le funzionalità di un gruppo di gestione con [organizzare le risorse con i gruppi di gestione di Azure](../../management-groups/overview.md).