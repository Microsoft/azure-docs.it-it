---
title: Usare Azure AD nel servizio Azure Kubernetes
description: Informazioni su come usare Azure AD in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 7f6cf503a459175e3109a515b666bbeaa3a25b4d
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550000"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integrazione Azure Active Directory gestita da AKS

L'integrazione di Azure AD gestita da AKS è progettata per semplificare l'esperienza di integrazione Azure AD, in cui gli utenti erano in precedenza necessari per creare un'app client, un'app Server e hanno richiesto il tenant Azure AD per concedere le autorizzazioni di lettura directory. Nella nuova versione, il provider di risorse AKS gestisce automaticamente le app client e server.

## <a name="azure-ad-authentication-overview"></a>Panoramica dell'autenticazione Azure AD

Gli amministratori del cluster possono configurare il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC) in base all'identità dell'utente o al gruppo di directory. L'autenticazione di Azure AD è disponibile per i cluster di servizio Azure Kubernetes con OpenID Connect. OpenID Connect è un livello di gestione delle identità basato sul protocollo OAuth 2.0. Per ulteriori informazioni su OpenID Connect, vedere la [documentazione relativa a Open ID Connect][open-id-connect].

Per altre informazioni sul flusso di integrazione di Azure AD, vedere la [documentazione relativa ai concetti di integrazione di Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Limitazioni 

* L'integrazione di Azure AD gestiti da AKS non può essere disabilitata
* i cluster abilitati per RBAC non Kubernetes non sono supportati per l'integrazione di Azure AD gestiti da AKS
* La modifica del tenant di Azure AD associato all'integrazione Azure AD gestita da AKS non è supportata

## <a name="prerequisites"></a>Prerequisiti

* L'interfaccia della riga di comando di Azure versione 2.11.0 o successiva
* Kubectl con una versione minima di [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) o [kubelogin](https://github.com/Azure/kubelogin)
* Se si usa [Helm](https://github.com/helm/helm), versione minima di Helm 3,3.

> [!Important]
> È necessario usare Kubectl con una versione minima di 1.18.1 o kubelogin. Se non si usa la versione corretta, si noteranno problemi di autenticazione.

Per installare kubectl e kubelogin, usare i comandi seguenti:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Usare [queste istruzioni](https://kubernetes.io/docs/tasks/tools/install-kubectl/) per altri sistemi operativi.

## <a name="before-you-begin"></a>Prima di iniziare

Per il cluster, è necessario un gruppo di Azure AD. Questo gruppo è necessario come gruppo di amministrazione per il cluster per concedere le autorizzazioni di amministratore del cluster. È possibile usare un gruppo di Azure AD esistente o crearne uno nuovo. Registrare l'ID oggetto del gruppo di Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Per creare un nuovo gruppo di Azure AD per gli amministratori del cluster, usare il comando seguente:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Creare un cluster AKS con Azure AD abilitato

Creare un cluster AKS usando i comandi dell'interfaccia della riga di comando seguenti.

Creare un gruppo di risorse di Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Creare un cluster AKS e abilitare l'accesso di amministrazione per il gruppo di Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Una corretta creazione di un cluster Azure AD gestito da AKS ha la sezione seguente nel corpo della risposta
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Una volta creato il cluster, è possibile iniziare ad accedervi.

## <a name="access-an-azure-ad-enabled-cluster"></a>Accedere a un cluster Azure AD abilitato

Per eseguire la procedura seguente, è necessario il ruolo predefinito [utente del cluster di servizi di Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) .

Ottenere le credenziali utente per accedere al cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Seguire le istruzioni per l'accesso.

Usare il comando kubectl Get Nodes per visualizzare i nodi nel cluster:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configurare il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](./azure-ad-rbac.md) per configurare gruppi di sicurezza aggiuntivi per i cluster.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Risoluzione dei problemi di accesso con Azure AD

> [!Important]
> La procedura descritta di seguito sta ignorando la normale autenticazione del gruppo di Azure AD. Utilizzarle solo in caso di emergenza.

Se non si dispone dell'accesso a un gruppo di Azure AD valido con accesso al cluster, è comunque possibile ottenere le credenziali di amministratore per accedere direttamente al cluster.

Per eseguire questa procedura, è necessario avere accesso al ruolo predefinito [Amministrazione cluster del servizio Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Abilitare l'integrazione di Azure AD gestiti da AKS nel cluster esistente

È possibile abilitare l'integrazione di Azure AD gestiti da AKS nel cluster esistente Kubernetes RBAC abilitato. Assicurarsi di impostare il gruppo di amministrazione per mantenere l'accesso al cluster.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Una corretta attivazione di un cluster Azure AD gestito da AKS ha la sezione seguente nel corpo della risposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Scaricare di nuovo le credenziali utente per accedere al cluster attenendosi alla procedura descritta [qui][access-cluster].

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Aggiornamento all'integrazione Azure AD gestita da AKS

Se il cluster usa l'integrazione Azure AD legacy, è possibile eseguire l'aggiornamento all'integrazione Azure AD gestita da AKS.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Una migrazione riuscita di un cluster di Azure AD gestito da AKS ha la sezione seguente nel corpo della risposta

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Se si vuole accedere al cluster, seguire [questa procedura.][access-cluster]

## <a name="non-interactive-sign-in-with-kubelogin"></a>Accesso non interattivo con kubelogin

Esistono alcuni scenari non interattivi, ad esempio le pipeline di integrazione continua, che non sono attualmente disponibili con kubectl. È possibile usare [`kubelogin`](https://github.com/Azure/kubelogin) per accedere al cluster con l'accesso all'entità servizio non interattivo.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Usare l'accesso condizionale con Azure AD e AKS

Quando si integrano Azure AD con il cluster AKS, è anche possibile usare [l'accesso condizionale][aad-conditional-access] per controllare l'accesso al cluster.

> [!NOTE]
> Azure AD l'accesso condizionale è una funzionalità di Azure AD Premium.

Per creare un esempio di criteri di accesso condizionale da usare con AKS, completare i passaggi seguenti:

1. Nella parte superiore del portale di Azure cercare e selezionare Azure Active Directory.
1. Nel menu per Azure Active Directory sul lato sinistro selezionare *applicazioni aziendali*.
1. Nel menu per le applicazioni aziendali sul lato sinistro selezionare *accesso condizionale*.
1. Nel menu per l'accesso condizionale sul lato sinistro selezionare *criteri* e quindi *nuovo criterio*.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Aggiunta di un criterio di accesso condizionale":::
1. Immettere un nome per il criterio, ad esempio *AKS-Policy*.
1. Selezionare *utenti e gruppi*, quindi in *Includi* Selezionare *Seleziona utenti e gruppi*. Scegliere gli utenti e i gruppi a cui si desidera applicare il criterio. Per questo esempio, scegliere lo stesso gruppo di Azure AD con l'accesso amministrativo al cluster.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Selezione di utenti o gruppi per applicare i criteri di accesso condizionale":::
1. Selezionare *app Cloud o azioni*, quindi in *Includi* Selezionare *Seleziona app*. Cercare *Azure Kubernetes Service* e selezionare *Azure Kubernetes Service AAD server*.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Selezione del server AD del servizio Kubernetes di Azure per l'applicazione dei criteri di accesso condizionale":::
1. In *Controlli di accesso* selezionare *Concedi*. Selezionare *Concedi accesso* , quindi Richiedi che i *dispositivi siano contrassegnati come conformi*.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Selezione per consentire solo i dispositivi conformi per i criteri di accesso condizionale":::
1. In *Abilita criteri* selezionare *on* , quindi *Crea*.
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Abilitazione dei criteri di accesso condizionale":::

Ottenere le credenziali utente per accedere al cluster, ad esempio:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Seguire le istruzioni per l'accesso.

Usare il `kubectl get nodes` comando per visualizzare i nodi nel cluster:

```azurecli-interactive
kubectl get nodes
```

Seguire le istruzioni per accedere di nuovo. Si noti che è presente un messaggio di errore indicante che l'accesso è stato eseguito correttamente, ma l'amministratore richiede che il dispositivo che richiede l'accesso sia gestito dal Azure AD per accedere alla risorsa.

Nella portale di Azure passare a Azure Active Directory, selezionare *applicazioni aziendali* , quindi in *attività* Selezionare *accessi*. Si noti una voce nella parte superiore con *lo stato* *failed* e un *accesso condizionale* di *Success*. Selezionare la voce quindi selezionare *accesso condizionale* in *Dettagli*. Si noti che i criteri di accesso condizionale sono elencati.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Voce di accesso non riuscita a causa di criteri di accesso condizionale":::

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sull' [integrazione RBAC di Azure per l'autorizzazione Kubernetes][azure-rbac-integration]
* Informazioni sull' [integrazione di Azure ad con KUBERNETES RBAC][azure-ad-rbac].
* Usare [kubelogin](https://github.com/Azure/kubelogin) per accedere alle funzionalità di autenticazione di Azure non disponibili in kubectl.
* Altre informazioni sui [concetti relativi all'identità AKS e Kubernetes][aks-concepts-identity].
* Usare i [modelli di Azure Resource Manager (ARM) ][aks-arm-template] per creare cluster gestiti da AKS Azure ad abilitati.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
