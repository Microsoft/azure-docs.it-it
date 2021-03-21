---
title: Limitare l'accesso a kubeconfig nel servizio Azure Kubernetes
description: Informazioni su come controllare l'accesso al file di configurazione di Kubernetes (kubeconfig) per gli amministratori e gli utenti di cluster
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 77b9988557106ef460d3b222ef85eb29e08f31c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693985"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Usare il controllo degli accessi in base al ruolo di Azure per definire l'accesso al file di configurazione Kubernetes in Azure Kubernetes Service (AKS)

È possibile interagire con i cluster Kubernetes tramite lo strumento `kubectl`. L'interfaccia della riga di comando di Azure offre un modo semplice per ottenere le credenziali di accesso e le informazioni di configurazione per la connessione ai cluster del servizio Azure Kubernetes tramite `kubectl`. Per limitare gli utenti che possono ottenere le informazioni sulla configurazione Kubernetes (*kubeconfig*) e per limitare le autorizzazioni che hanno a disposizione, è possibile usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).

Questo articolo illustra come assegnare i ruoli di Azure che limitano chi può ottenere le informazioni di configurazione per un cluster AKS.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo presuppone che si disponga di un cluster del servizio Azure Kubernetes esistente. Se è necessario un cluster del servizio Azure Kubernetes, vedere la guida di avvio rapido sul servizio Azure Kubernetes [Uso dell'interfaccia della riga di comando di Azure][aks-quickstart-cli] oppure [Uso del portale di Azure][aks-quickstart-portal].

Questo articolo richiede anche l'esecuzione dell'interfaccia della riga di comando di Azure versione 2.0.65 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Autorizzazioni per i ruoli del cluster disponibili

Quando si interagisce con un cluster del servizio Azure Kubernetes tramite lo strumento `kubectl`, viene usato un file di configurazione che definisce le informazioni di connessione del cluster. Questo file di configurazione viene in genere archiviato in *~/.Kube/config*. È possibile definire più cluster in questo file *kubeconfig* . Per passare da un cluster a un altro, si usa il comando [kubectl config use-context][kubectl-config-use-context].

Il comando [az aks get-credentials][az-aks-get-credentials] consente di ottenere le credenziali di accesso per un cluster del servizio Azure Kubernetes e di unirle nel file *kubeconfig*. Per controllare l'accesso a queste credenziali, è possibile usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Questi ruoli di Azure consentono di definire chi può recuperare il file *kubeconfig* e quali autorizzazioni hanno quindi all'interno del cluster.

I due ruoli predefiniti sono:

* **Ruolo di amministratore del cluster del servizio Azure Kubernetes**  
  * Consente l'accesso alla chiamata API *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action*. Questa chiamata API [elenca le credenziali di amministratore del cluster][api-cluster-admin].
  * Scarica *kubeconfig* per il ruolo *clusterAdmin*.
* **Ruolo di utente del cluster del servizio Azure Kubernetes**
  * Consente l'accesso alla chiamata API *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action*. Questa chiamata API [elenca le credenziali di utente del cluster][api-cluster-user].
  * Scarica *kubeconfig* per il ruolo *clusterUser*.

Questi ruoli di Azure possono essere applicati a un utente o a un gruppo di Azure Active Directory (AD).

> [!NOTE]
> Nei cluster che usano Azure AD, gli utenti con il ruolo *clusterUser* hanno un file *kubeconfig* vuoto che richiede un accesso. Una volta effettuato l'accesso, gli utenti possono accedere in base alle impostazioni Azure AD utente o gruppo. Gli utenti con il ruolo *clusterAdmin* hanno accesso amministrativo.
>
> I cluster che non utilizzano Azure AD utilizzano solo il ruolo *clusterAdmin* .

## <a name="assign-role-permissions-to-a-user-or-group"></a>Assegnare autorizzazioni ruolo a un utente o a un gruppo

Per assegnare uno dei ruoli disponibili, è necessario ottenere l'ID di risorsa del cluster AKS e l'ID del Azure AD account utente o gruppo. I comandi di esempio seguenti:

* Ottenere l'ID risorsa cluster usando il comando [AZ AKS Show][az-aks-show] per il cluster denominato *myAKSCluster* nel gruppo di risorse *myResourceGroup* . Specificare il nome del cluster e del gruppo di risorse in base alle esigenze.
* Usare i comandi [AZ account Show][az-account-show] e [AZ ad User Show][az-ad-user-show] per ottenere l'ID utente.
* Infine, assegnare un ruolo usando il comando [AZ Role Assignment create][az-role-assignment-create] .

L'esempio seguente assegna il *ruolo di amministratore del cluster di servizi Kubernetes di Azure* a un singolo account utente:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!IMPORTANT]
> In alcuni casi, il *User.Name* nell'account è diverso da quello di *userPrincipalName*, ad esempio con Azure ad utenti Guest:
>
> ```output
> $ az account show --query user.name -o tsv
> user@contoso.com
> $ az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv
> user_contoso.com#EXT#@contoso.onmicrosoft.com
> ```
>
> In questo caso, impostare il valore di *ACCOUNT_UPN* su *userPrincipalName* dall'utente Azure ad. Ad esempio, se l'account *User.Name* è *User \@ contoso.com*:
> 
> ```azurecli-interactive
> ACCOUNT_UPN=$(az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv)
> ```

> [!TIP]
> Se si desidera assegnare autorizzazioni a un gruppo di Azure AD, aggiornare il `--assignee` parametro illustrato nell'esempio precedente con l'ID oggetto per il *gruppo* anziché un *utente*. Per ottenere l'ID oggetto per un gruppo, usare il comando [AZ ad Group Show][az-ad-group-show] . Nell'esempio seguente viene ottenuto l'ID oggetto per il gruppo di Azure AD denominato *AppDev*: `az ad group show --group appdev --query objectId -o tsv`

È possibile modificare l'assegnazione precedente specificando il *Ruolo utente del cluster* in base alle esigenze.

L'output di esempio seguente mostra che l'assegnazione di ruolo è stata creata correttamente:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Ottenere e verificare le informazioni di configurazione

Con i ruoli di Azure assegnati, usare il comando [AZ AKS Get-credentials][az-aks-get-credentials] per ottenere la definizione *kubeconfig* per il cluster AKS. L'esempio seguente ottiene le credenziali *--admin*, che funzionano correttamente se all'utente è stato assegnato il *Ruolo amministratore del cluster*:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

È quindi possibile usare il comando [kubectl config view][kubectl-config-view] per verificare che le informazioni *context* per il cluster indichino che sono state applicate le informazioni di configurazione per l'amministratore:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Rimuovere le autorizzazioni per i ruoli

Per rimuovere le assegnazioni di ruolo, usare il comando [az role assignment delete][az-role-assignment-delete]. Specificare l'ID account e l'ID risorsa cluster, come ottenuto nei comandi precedenti. Se il ruolo è stato assegnato a un gruppo anziché a un utente, specificare l'ID oggetto gruppo appropriato anziché l'ID oggetto account per il `--assignee` parametro:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Passaggi successivi

Per aumentare il livello di sicurezza dell'accesso ai cluster del servizio Azure Kubernetes, [integrare l'autenticazione di Azure Active Directory][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
