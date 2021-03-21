---
title: Creazione di un controller di ingresso con un nuovo gateway applicazione
description: Questo articolo fornisce informazioni su come distribuire un controller di ingresso del gateway applicazione con un nuovo gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 8be5ac75e2da3eaeae300fd36e152a24c9777e64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593740"
---
# <a name="how-to-install-an-application-gateway-ingress-controller-agic-using-a-new-application-gateway"></a>Come installare un controller di ingresso del gateway applicazione (AGIC) usando un nuovo gateway applicazione

Le istruzioni seguenti presuppongono che il controller di ingresso del gateway applicazione (AGIC) venga installato in un ambiente senza componenti preesistenti.

## <a name="required-command-line-tools"></a>Strumenti da riga di comando necessari

È consigliabile usare [Azure cloud Shell](https://shell.azure.com/) per tutte le operazioni della riga di comando riportate di seguito. Avviare la shell da shell.azure.com o facendo clic sul collegamento:

[![Incorpora avvio](https://shell.azure.com/images/launchcloudshell.png "Avviare Azure Cloud Shell")](https://shell.azure.com)

In alternativa, avviare Cloud Shell da portale di Azure usando l'icona seguente:

![Avvio del portale](./media/application-gateway-ingress-controller-install-new/portal-launch-icon.png)

Il [Azure cloud Shell](https://shell.azure.com/) dispone già di tutti gli strumenti necessari. Se si sceglie di usare un altro ambiente, assicurarsi che siano installati gli strumenti da riga di comando seguenti:

* `az` -INTERFACCIA della riga di comando di Azure: [istruzioni di installazione](/cli/azure/install-azure-cli)
* `kubectl` -Strumento da riga di comando Kubernetes: [istruzioni di installazione](https://kubernetes.io/docs/tasks/tools/install-kubectl)
* `helm` -Gestione pacchetti Kubernetes: [istruzioni di installazione](https://github.com/helm/helm/releases/latest)
* `jq` -processore JSON da riga di comando: [istruzioni di installazione](https://stedolan.github.io/jq/download/)


## <a name="create-an-identity"></a>Creare un'identità

Attenersi alla procedura seguente per creare un [oggetto entità servizio](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)Azure Active Directory (AAD). Registrare i `appId` valori, `password` e, che `objectId` verranno usati nei passaggi seguenti.

1. Creare un'entità servizio Active Directory ([altre informazioni su RBAC di Azure](../role-based-access-control/overview.md)):
    ```azurecli
    az ad sp create-for-rbac --skip-assignment -o json > auth.json
    appId=$(jq -r ".appId" auth.json)
    password=$(jq -r ".password" auth.json)
    ```
    I `appId` valori e dell' `password` output JSON verranno usati nei passaggi seguenti


1. Usare l'oggetto dell' `appId` output del comando precedente per ottenere la `objectId` della nuova entità servizio:
    ```azurecli
    objectId=$(az ad sp show --id $appId --query "objectId" -o tsv)
    ```
    L'output di questo comando è `objectId` , che verrà usato nel modello di Azure Resource Manager seguente

1. Creare il file dei parametri che verrà usato nella distribuzione del modello di Azure Resource Manager in un secondo momento.
    ```bash
    cat <<EOF > parameters.json
    {
      "aksServicePrincipalAppId": { "value": "$appId" },
      "aksServicePrincipalClientSecret": { "value": "$password" },
      "aksServicePrincipalObjectId": { "value": "$objectId" },
      "aksEnableRBAC": { "value": false }
    }
    EOF
    ```
    Per distribuire un cluster **KUBERNETES RBAC** abilitato, impostare il `aksEnableRBAC` campo su `true`

## <a name="deploy-components"></a>Distribuisci componenti
Questo passaggio consente di aggiungere alla sottoscrizione i componenti seguenti:

- [Servizio Azure Kubernetes](../aks/intro-kubernetes.md)
- [Gateway applicazione](./overview.md) V2
- [Rete virtuale](../virtual-network/virtual-networks-overview.md) con 2 [Subnet](../virtual-network/virtual-networks-overview.md)
- [Indirizzo IP pubblico](../virtual-network/virtual-network-public-ip-address.md)
- [Identità gestita](../active-directory/managed-identities-azure-resources/overview.md), che verrà usata dall' [identità pod di AAD](https://github.com/Azure/aad-pod-identity/blob/master/README.md)

1. Scaricare il modello di Azure Resource Manager e modificare il modello in base alle esigenze.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/deploy/azuredeploy.json -O template.json
    ```

1. Distribuire il modello di Azure Resource Manager usando `az cli` . Questa operazione può richiedere fino a 5 minuti.
    ```azurecli
    resourceGroupName="MyResourceGroup"
    location="westus2"
    deploymentName="ingress-appgw"

    # create a resource group
    az group create -n $resourceGroupName -l $location

    # modify the template as needed
    az deployment group create \
            -g $resourceGroupName \
            -n $deploymentName \
            --template-file template.json \
            --parameters parameters.json
    ```

1. Al termine della distribuzione, scaricare l'output della distribuzione in un file denominato `deployment-outputs.json` .
    ```azurecli
    az deployment group show -g $resourceGroupName -n $deploymentName --query "properties.outputs" -o json > deployment-outputs.json
    ```

## <a name="set-up-application-gateway-ingress-controller"></a>Configurare il controller di ingresso del gateway applicazione

Con le istruzioni riportate nella sezione precedente, abbiamo creato e configurato un nuovo cluster AKS e un gateway applicazione. A questo punto è possibile distribuire un'app di esempio e un controller di ingresso nella nuova infrastruttura di Kubernetes.

### <a name="setup-kubernetes-credentials"></a>Configurare le credenziali di Kubernetes
Per la procedura seguente è necessario configurare il comando [kubectl](https://kubectl.docs.kubernetes.io/) , che verrà usato per connettersi al nuovo cluster Kubernetes. [Cloud Shell](https://shell.azure.com/) è `kubectl` già installato. `az`Per ottenere le credenziali per Kubernetes, si userà l'interfaccia della riga di comando.

Ottenere le credenziali per il AKS appena distribuito ([altre](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)informazioni):
```azurecli
# use the deployment-outputs.json created after deployment to get the cluster name and resource group name
aksClusterName=$(jq -r ".aksClusterName.value" deployment-outputs.json)
resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)

az aks get-credentials --resource-group $resourceGroupName --name $aksClusterName
```

### <a name="install-aad-pod-identity"></a>Installare l'identità pod di AAD
  Azure Active Directory identità Pod fornisce l'accesso basato su token ai [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md).

  L' [identità pod di AAD](https://github.com/Azure/aad-pod-identity) aggiungerà i componenti seguenti al cluster Kubernetes:
   * [Definizioni di risorse personalizzate](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) di Kubernetes: `AzureIdentity`, `AzureAssignedIdentity`, `AzureIdentityBinding`
   * Componente [Controller identità gestita](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
   * Componente [Identità gestita del nodo](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)


Per installare l'identità pod di AAD nel cluster:

   - *KUBERNETES RBAC abilitato* Cluster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
     ```

   - *KUBERNETES RBAC disabilitato* Cluster AKS

     ```bash
     kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
     ```

### <a name="install-helm"></a>Installare Helm
[Helm](../aks/kubernetes-helm.md) è una gestione pacchetti per Kubernetes. Verrà usato per installare il `application-gateway-kubernetes-ingress` pacchetto:

1. Installare [Helm](../aks/kubernetes-helm.md) ed eseguire il comando seguente per aggiungere il `application-gateway-kubernetes-ingress` pacchetto Helm:

    - *KUBERNETES RBAC abilitato* Cluster AKS

        ```bash
        kubectl create serviceaccount --namespace kube-system tiller-sa
        kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
        helm init --tiller-namespace kube-system --service-account tiller-sa
        ```

    - *KUBERNETES RBAC disabilitato* Cluster AKS

        ```bash
        helm init
        ```

1. Aggiungere il repository Helm AGIC:
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

### <a name="install-ingress-controller-helm-chart"></a>Installare il grafico Helm del controller di ingresso

1. Usare il `deployment-outputs.json` file creato in precedenza e creare le variabili seguenti.
    ```bash
    applicationGatewayName=$(jq -r ".applicationGatewayName.value" deployment-outputs.json)
    resourceGroupName=$(jq -r ".resourceGroupName.value" deployment-outputs.json)
    subscriptionId=$(jq -r ".subscriptionId.value" deployment-outputs.json)
    identityClientId=$(jq -r ".identityClientId.value" deployment-outputs.json)
    identityResourceId=$(jq -r ".identityResourceId.value" deployment-outputs.json)
    ```
1. Scaricare Helm-config. YAML, che configurerà AGIC:
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    In alternativa, copiare il file YAML seguente: 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. Modificare il file Helm-config. YAML appena scaricato e compilare le sezioni `appgw` e `armAuth` .
    ```bash
    sed -i "s|<subscriptionId>|${subscriptionId}|g" helm-config.yaml
    sed -i "s|<resourceGroupName>|${resourceGroupName}|g" helm-config.yaml
    sed -i "s|<applicationGatewayName>|${applicationGatewayName}|g" helm-config.yaml
    sed -i "s|<identityResourceId>|${identityResourceId}|g" helm-config.yaml
    sed -i "s|<identityClientId>|${identityClientId}|g" helm-config.yaml

    # You can further modify the helm config to enable/disable features
    nano helm-config.yaml
    ```

   Valori:
     - `verbosityLevel`: imposta il livello di dettaglio dell'infrastruttura di registrazione di AGIC. Per i valori possibili, vedere [Livelli di registrazione](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels).
     - `appgw.subscriptionId`: ID sottoscrizione di Azure in cui risiede il gateway applicazione. Esempio: `a123b234-a3b4-557d-b2df-a0bc12de1234`
     - `appgw.resourceGroup`: Nome del gruppo di risorse di Azure in cui è stato creato il gateway applicazione. Esempio: `app-gw-resource-group`
     - `appgw.name`: nome del gateway applicazione. Esempio: `applicationgatewayd0f0`
     - `appgw.shared`: il valore predefinito di questo flag booleano deve essere `false`. Impostare su `true` se è necessario un [gateway applicazione condiviso](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
     - `kubernetes.watchNamespace`: specificare lo spazio dei nomi che AGIC deve controllare. Potrebbe trattarsi di un valore stringa singolo o di un elenco delimitato da virgole di spazi dei nomi.
    - `armAuth.type`: può essere `aadPodIdentity` o `servicePrincipal`
    - `armAuth.identityResourceID`: ID risorsa dell'identità gestita di Azure
    - `armAuth.identityClientId`: ID client dell'identità. Vedere di seguito per altre informazioni sull'identità
    - `armAuth.secretJSON`: Necessario solo quando si sceglie il tipo di segreto dell'entità servizio (quando `armAuth.type` è stato impostato su `servicePrincipal` ) 


   > [!NOTE]
   > `identityResourceID`E `identityClientID` sono valori creati durante i passaggi di [distribuzione dei componenti](ingress-controller-install-new.md#deploy-components) e possono essere ottenuti di nuovo usando il comando seguente:
   > ```azurecli
   > az identity show -g <resource-group> -n <identity-name>
   > ```
   > `<resource-group>` nel comando precedente è il gruppo di risorse del gateway applicazione. `<identity-name>` nome dell'identità creata. È possibile elencare tutte le identità per una determinata sottoscrizione usando: `az identity list`


1. Installare il pacchetto del controller di ingresso del gateway applicazione:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

## <a name="install-a-sample-app"></a>Installare un'app di esempio
Ora che sono installati il gateway applicazione, AKS e AGIC, è possibile installare un'app di esempio tramite [Azure cloud Shell](https://shell.azure.com/):

```yaml
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: aspnetapp
  labels:
    app: aspnetapp
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP

---

apiVersion: v1
kind: Service
metadata:
  name: aspnetapp
spec:
  selector:
    app: aspnetapp
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80

---

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: aspnetapp
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aspnetapp
          servicePort: 80
EOF
```

In alternativa, è possibile:

* Scaricare il file YAML sopra:

```bash
curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
```

* Applicare il file YAML:

```bash
kubectl apply -f aspnetapp.yaml
```


## <a name="other-examples"></a>Altri esempi
Questa [Guida](ingress-controller-expose-service-over-http-https.md) dettagliata contiene altri esempi su come esporre un servizio AKS tramite http o HTTPS a Internet con il gateway applicazione.
