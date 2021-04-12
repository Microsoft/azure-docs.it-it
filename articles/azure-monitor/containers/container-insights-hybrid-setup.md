---
title: Configurare cluster Kubernetes ibridi con informazioni dettagliate sul contenitore | Microsoft Docs
description: Questo articolo descrive come configurare il contenitore informazioni dettagliate per monitorare i cluster Kubernetes ospitati in Azure Stack o in un altro ambiente.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d2692b4a634d60ef62339f68277591d711260712
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711247"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Configurare cluster Kubernetes ibridi con informazioni dettagliate sul contenitore

Il contenitore Insights offre un'esperienza di monitoraggio avanzata per il servizio Azure Kubernetes (AKS) e il [motore AKS in Azure](https://github.com/Azure/aks-engine), che è un cluster Kubernetes self-Managed ospitato in Azure. Questo articolo descrive come abilitare il monitoraggio dei cluster Kubernetes ospitati all'esterno di Azure e ottenere un'esperienza di monitoraggio simile.

## <a name="supported-configurations"></a>Configurazioni supportate

Le configurazioni seguenti sono ufficialmente supportate con informazioni dettagliate sul contenitore. Se si dispone di una versione diversa di Kubernetes e versioni del sistema operativo, inviare un messaggio di posta elettronica a askcoin@microsoft.com .

- Ambienti

    - Kubernetes locale
    - Motore AKS in Azure e Azure Stack. Per altre informazioni, vedere [motore AKS su Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versione 4 e successive, in locale o in altri ambienti cloud.

- Le versioni di Kubernetes e i criteri di supporto corrispondono alle versioni di [AKS supportate](../../aks/supported-kubernetes-versions.md).

- Sono supportati i seguenti runtime di contenitori: i Runtime compatibili con Docker, Moby e CRI, ad esempio CRI-O e contenitori.

- La versione del sistema operativo Linux per i nodi master e di lavoro supportati sono: Ubuntu (18,04 LTS e 16,04 LTS) e Red Hat Enterprise Linux CoreOS 43,81.

- Controllo di accesso supportato: Kubernetes RBAC e non RBAC

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di disporre degli elementi seguenti:

- Un'[area di lavoro Log Analytics](../logs/design-logs-deployment.md).

    Il contenitore Insights supporta un'area di lavoro Log Analytics nelle aree elencate in [prodotti di](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)Azure in base all'area. Per creare un'area di lavoro personalizzata, è possibile crearla tramite [Azure Resource Manager](../logs/resource-manager-workspace.md), tramite [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)o nel [portale di Azure](../logs/quick-create-workspace.md).

    >[!NOTE]
    >L'abilitazione del monitoraggio di più cluster con lo stesso nome del cluster nella stessa area di lavoro Log Analytics non è supportata. I nomi dei cluster devono essere univoci.
    >

- L'utente è membro del **ruolo di collaboratore log Analytics** per abilitare il monitoraggio dei contenitori. Per ulteriori informazioni su come controllare l'accesso a un'area di lavoro Log Analytics, vedere [gestire l'accesso all'area di lavoro e ai dati di log](../logs/manage-access.md).

- Per visualizzare i dati di monitoraggio, è necessario avere [*log Analytics ruolo Reader*](../logs/manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro log Analytics, configurato con informazioni dettagliate sul contenitore.

- [Helm client](https://helm.sh/docs/using_helm/) to onboarding the container Insights grafico per il cluster Kubernetes specificato.

- Le informazioni di configurazione del proxy e del firewall seguenti sono necessarie per la comunicazione tra la versione in contenitori dell'agente di Log Analytics per Linux e il monitoraggio di Azure:

    |Risorsa agente|Porte |
    |------|---------|
    |*.ods.opinsights.azure.com |Porta 443 |
    |*.oms.opinsights.azure.com |Porta 443 |
    |*. dc.services.visualstudio.com |Porta 443 |

- `cAdvisor secure port: 10250`Per raccogliere le metriche delle prestazioni, l'agente in contenitori richiede l'apertura o l'apertura di Kubelet in `unsecure port :10255` tutti i nodi del cluster. Se non è già configurato, è consigliabile configurare `secure port: 10250` nel cAdvisor di Kubelet.

- Per poter comunicare con il servizio API Kubernetes all'interno del cluster, è necessario specificare le variabili di ambiente seguenti nel contenitore per la raccolta dei dati di inventario, `KUBERNETES_SERVICE_HOST` e `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>La versione minima dell'agente supportata per il monitoraggio dei cluster Kubernetes ibridi è ciprod10182019 o successiva.

## <a name="enable-monitoring"></a>Abilitare il monitoraggio

L'abilitazione di container Insights per il cluster ibrido Kubernetes consiste nell'eseguire i passaggi seguenti nell'ordine.

1. Configurare l'area di lavoro Log Analytics con la soluzione container Insights.   

2. Abilitare il grafico HELM di container Insights con Log Analytics area di lavoro.

Per altre informazioni sulle soluzioni di monitoraggio in monitoraggio di Azure, vedere [qui](../../azure-monitor/insights/solutions.md).

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Come aggiungere la soluzione contenitori di monitoraggio di Azure

È possibile distribuire la soluzione con il modello di Azure Resource Manager fornito usando il cmdlet Azure PowerShell o con l'interfaccia della riga di comando di `New-AzResourceGroupDeployment` Azure.

Se non si ha familiarità con il concetto di distribuzione delle risorse tramite un modello, vedere:

- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md)

- [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../../azure-resource-manager/templates/deploy-cli.md)

Se si sceglie di usare l'interfaccia della riga di comando di Azure, è prima necessario installarla ed eseguirla in locale. È necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.59 o successiva. Per identificare la versione in uso, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Questo metodo include due modelli JSON. Un modello JSON specifica la configurazione per abilitare il monitoraggio e l'altro contiene i valori dei parametri da configurare per specificare quanto segue:

- **workspaceResourceId** : l'ID di risorsa completo dell'area di lavoro log Analytics.
- **workspaceRegion** : area in cui viene creata l'area di lavoro, definita anche **percorso** nelle proprietà dell'area di lavoro durante la visualizzazione dalla portale di Azure.

Per identificare prima di tutto l'ID risorsa completo dell'area di lavoro di Log Analytics necessaria per il `workspaceResourceId` valore del parametro nella **containerSolutionParams.jssu** file, seguire questa procedura e quindi eseguire il cmdlet di PowerShell o l'interfaccia della riga di comando di Azure per aggiungere la soluzione.

1. Elencare tutte le sottoscrizioni a cui si ha accesso usando il comando seguente:

    ```azurecli
    az account list --all -o table
    ```

    L'output sarà simile al seguente:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Copiare il valore per **SubscriptionId**.

2. Passare alla sottoscrizione che ospita l'area di lavoro Log Analytics usando il comando seguente:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Nell'esempio seguente viene visualizzato l'elenco delle aree di lavoro nelle sottoscrizioni nel formato JSON predefinito.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Nell'output trovare il nome dell'area di lavoro, quindi copiare l'ID risorsa completo dell'area di lavoro Log Analytics sotto l' **ID** campo.

4. Copiare e incollare nel file la sintassi JSON seguente:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Salvare il file come containerSolution.jsin una cartella locale.

6. Incollare nel file la sintassi JSON seguente:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Modificare i valori per **workspaceResourceId** usando il valore copiato nel passaggio 3 e per **workspaceRegion** copiare il valore **Region** dopo l'esecuzione del comando dell'interfaccia della riga di comando di Azure [AZ monitor log-Analytics Workspace Show](/cli/azure/monitor/log-analytics/workspace#az-monitor-log-analytics-workspace-list&preserve-view=true).

8. Salvare il file come containerSolutionParams.jsin una cartella locale.

9. A questo punto è possibile distribuire il modello.

   - Per eseguire la distribuzione con Azure PowerShell, usare i comandi seguenti nella cartella che contiene il modello:

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Per la distribuzione con l'interfaccia della riga di comando di Azure, eseguire i comandi seguenti:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       Il completamento della modifica della configurazione può richiedere alcuni minuti. Al termine dell'operazione, viene visualizzato un messaggio simile al seguente in cui è incluso il risultato:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Dopo aver abilitato il monitoraggio, possono essere necessari circa 15 minuti prima di poter visualizzare le metriche di integrità per il cluster.

## <a name="install-the-helm-chart"></a>Installare il grafico HELM

In questa sezione si installa l'agente in contenitori per informazioni dettagliate sul contenitore. Prima di procedere, è necessario identificare l'ID dell'area di lavoro necessario per il `omsagent.secret.wsid` parametro e la chiave primaria necessaria per il `omsagent.secret.key` parametro. È possibile identificare queste informazioni attenendosi alla procedura seguente, quindi eseguire i comandi per installare l'agente usando il grafico HELM.

1. Eseguire il comando seguente per identificare l'ID dell'area di lavoro:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    Nell'output trovare il nome dell'area di lavoro sotto il **nome** del campo, quindi copiare l'ID dell'area di lavoro log Analytics nel campo **CustomerID**.

2. Eseguire il comando seguente per identificare la chiave primaria per l'area di lavoro:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    Nell'output trovare la chiave primaria nel campo **primarySharedKey**, quindi copiare il valore.

>[!NOTE]
>I comandi seguenti sono applicabili solo per Helm versione 2. L'uso del `--name` parametro non è applicabile con Helm versione 3. 

>[!NOTE]
>Se il cluster Kubernetes comunica tramite un server proxy, configurare il parametro `omsagent.proxy` con l'URL del server proxy. Se il cluster non comunica tramite un server proxy, non è necessario specificare questo parametro. Per ulteriori informazioni, vedere [configure proxy endpoint](#configure-proxy-endpoint) più avanti in questo articolo.

3. Aggiungere il repository dei grafici di Azure all'elenco locale eseguendo il comando seguente:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Installare il grafico eseguendo il comando seguente:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Se l'area di lavoro Log Analytics è in Azure Cina 21Vianet, eseguire il comando seguente:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Se l'area di lavoro Log Analytics è in Azure US Gov, eseguire questo comando:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>Abilitare il grafico Helm usando il modello API

È possibile specificare un addon nel file JSON della specifica del cluster del motore AKS, noto anche come modello API. In questo addon fornire la versione con codifica Base64 di `WorkspaceGUID` e `WorkspaceKey` dell'area di lavoro log Analytics in cui sono archiviati i dati di monitoraggio raccolti. È possibile trovare `WorkspaceGUID` e `WorkspaceKey` usando i passaggi 1 e 2 della sezione precedente.

Le definizioni API supportate per il cluster di hub Azure Stack sono disponibili in questo esempio: [kubernetes-container-monitoring_existing_workspace_id_and_key.jssu](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). In particolare, trovare la proprietà **addons** in **kubernetesConfig**:

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>Configurare la raccolta dati dell'agente

Con il grafico versione 1.0.0, le impostazioni di raccolta dei dati dell'agente vengono controllate dal ConfigMap. Vedere la documentazione sulle impostazioni di raccolta dati di Agent [qui](container-insights-agent-config.md).

Dopo aver distribuito correttamente il grafico, è possibile esaminare i dati per il cluster Kubernetes ibrido in informazioni dettagliate sul contenitore dal portale di Azure.  

>[!NOTE]
>La latenza di inserimento è circa tra cinque e dieci minuti dall'agente per eseguire il commit nell'area di lavoro di Azure Log Analytics. Lo stato del cluster Mostra il valore **Nessun dato** o **sconosciuto** fino a quando non sono disponibili tutti i dati di monitoraggio necessari in monitoraggio di Azure.

## <a name="configure-proxy-endpoint"></a>Configurare l'endpoint proxy

A partire dalla versione 2.7.1, il grafico supporterà la specifica dell'endpoint proxy con il `omsagent.proxy` parametro Chart. Ciò consente di comunicare tramite il server proxy. La comunicazione tra l'agente di contenitore Insights e il monitoraggio di Azure può essere un server proxy HTTP o HTTPS ed è supportata sia l'autenticazione anonima che quella di base (nome utente/password).

Il valore di configurazione proxy ha la sintassi seguente: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Se il server proxy non richiede l'autenticazione, è comunque necessario specificare un nome utente/password di pseudoclasse. Può trattarsi di qualsiasi nome utente o password.

|Proprietà| Descrizione |
|--------|-------------|
|Protocollo | http o https |
|utente | Nome utente facoltativo per l'autenticazione proxy |
|password | Password facoltativa per l'autenticazione proxy |
|proxyhost | Indirizzo o FQDN del server proxy |
|port | Numero di porta facoltativo per il server proxy |

ad esempio `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Se si specifica il protocollo come **http**, le richieste HTTP vengono create usando la connessione protetta SSL/TLS. Il server proxy deve supportare i protocolli SSL/TLS.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante il tentativo di abilitare il monitoraggio per il cluster Kubernetes ibrido, copiare lo script di PowerShell [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) e salvarlo in una cartella nel computer. Questo script viene fornito per facilitare il rilevamento e la risoluzione dei problemi rilevati. Di seguito sono riportati i problemi che è stato progettato per rilevare e tentare la correzione:

- L'area di lavoro Log Analytics specificata è valida
- L'area di lavoro Log Analytics è configurata con la soluzione container Insights. In caso contrario, configurare l'area di lavoro.
- OmsAgent REPLICASET pod in esecuzione
- OmsAgent daemonset pod in esecuzione
- Il servizio integrità OmsAgent è in esecuzione
- Il Log Analytics ID e la chiave dell'area di lavoro configurati nell'agente in contenitori corrispondono all'area di lavoro con cui è configurata l'analisi.
- Verificare che tutti i nodi di lavoro Linux dispongano dell' `kubernetes.io/role=agent` etichetta per pianificare il Pod RS. Se non esiste, aggiungerlo.
- Convalida `cAdvisor secure port:10250` o `unsecure port: 10255` è aperto in tutti i nodi del cluster.

Per eseguire con Azure PowerShell, usare i comandi seguenti nella cartella che contiene lo script:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Passaggi successivi

Con il monitoraggio abilitato per raccogliere lo stato e l'utilizzo delle risorse del cluster Kubernetes ibrido e dei carichi di lavoro in esecuzione su di essi, Scopri [come usare](container-insights-analyze.md) le informazioni dettagliate sul contenitore.