---
title: Procedure consigliate per la gestione dell'identità
titleSuffix: Azure Kubernetes Service
description: Procedure consigliate per l'operatore del cluster per la gestione dell'autenticazione e dell'autorizzazione per i cluster nel servizio Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: a63a756448f9c7202c79c3b4625fc99d4a90dc52
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014058"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Procedure consigliate per l'autenticazione e l'autorizzazione nel servizio Azure Kubernetes (AKS)

Durante la distribuzione e la gestione dei cluster nel servizio Azure Kubernetes (AKS), è necessario implementare opportune modalità di gestione dell'accesso a risorse e servizi. Senza questi controlli, gli account possono avere accesso alle risorse e ai servizi di cui non hanno bisogno. Può essere inoltre difficile tenere traccia del set di credenziali usato per apportare le modifiche.

Questo articolo sulle procedure consigliate è incentrato su come un operatore del cluster può gestire l'accesso e l'identità per i cluster servizio Azure Kubernetes. In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Autenticare gli utenti del cluster servizio Azure Kubernetes con Azure Active Directory
> * Controllare l'accesso alle risorse con il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC)
> * Usare il controllo degli accessi in base al ruolo di Azure per controllare in modo granulare l'accesso alla risorsa AKS e l'API Kubernetes su larga scala, oltre che al kubeconfig.
> * Usare un'identità gestita per autenticare i pod stessi con altri servizi

## <a name="use-azure-active-directory"></a>Uso di Azure Active Directory

**Indicazioni sulle procedure consigliate**. Distribuire i cluster servizio Azure Kubernetes con l'integrazione di Azure AD. L'uso di Azure AD consente di centralizzare il componente di gestione delle identità. Qualsiasi modifica all'account utente o allo stato del gruppo viene aggiornata automaticamente nell'accesso al cluster servizio Azure Kubernetes. Usare ruoli o ClusterRole e associazioni, come descritto nella sezione successiva, per assegnare a utenti o gruppi il minor numero di autorizzazioni richieste.

Gli sviluppatori e i proprietari delle applicazioni del cluster Kubernetes hanno bisogno di accedere a risorse diverse. Kubernetes non offre una soluzione di gestione delle identità per controllare quali utenti possono interagire con determinate risorse. In alternativa, in genere si integra il cluster con una soluzione di gestione delle identità esistente. Azure Active Directory (AD) offre una soluzione di gestione delle identità di livello aziendale e può essere integrato con i cluster servizio Azure Kubernetes.

Con i cluster integrati con Azure AD in servizio Azure Kubernetes, vengono creati *ruoli* o *ClusterRole* che definiscono le autorizzazioni di accesso alle risorse. Si *associano* quindi i ruoli a utenti o gruppi da Azure AD. Queste Kubernetes controllo degli accessi in base al ruolo (Kubernetes RBAC) sono illustrate nella sezione successiva. L'integrazione di Azure AD e la modalità di controllo dell'accesso alle risorse possono essere visualizzate nel diagramma seguente:

![Autenticazione a livello di cluster per l'integrazione di Azure Active Directory con servizio Azure Kubernetes](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Lo sviluppatore viene autenticato con Azure AD.
1. L'endpoint di emissione del token di Azure AD emette il token di accesso.
1. Lo sviluppatore esegue un'azione utilizzando il token di Azure AD, ad esempio `kubectl create pod`
1. Kubernetes convalida il token con Azure Active Directory e recupera le appartenenze al gruppo dello sviluppatore.
1. Vengono applicati il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC) e i criteri del cluster.
1. La richiesta dello sviluppatore ha esito positivo o negativo a seconda della precedente convalida dell'appartenenza al gruppo di Azure AD e dei criteri e del controllo degli accessi in base al ruolo di Kubernetes.

Per creare un cluster AKS che usa Azure AD, vedere [Integrare Azure Active Directory con il servizio Azure Kubernetes][aks-aad].

## <a name="use-kubernetes-role-based-access-control-kubernetes-rbac"></a>Usare il controllo degli accessi in base al ruolo Kubernetes (Kubernetes RBAC)

**Indicazioni sulle procedure consigliate**. Usare i controlli degli accessi in base al ruolo di Kubernetes per definire le autorizzazioni di utenti o gruppi relativamente alle risorse del cluster. Creare ruoli e associazioni che assegnano il numero minimo di autorizzazioni richieste. Consentire l'integrazione con Azure AD in modo che qualsiasi modifica allo stato dell'utente o all'appartenenza al gruppo venga automaticamente aggiornata e l'accesso alle risorse del cluster sia corrente.

In Kubernetes è possibile fornire un controllo granulare dell'accesso alle risorse nel cluster. Le autorizzazioni vengono definite a livello di cluster o a spazi dei nomi specifici. È possibile definire quali risorse possono essere gestite e con quali autorizzazioni. Questi ruoli vengono quindi applicati a utenti o gruppi con un'associazione. Per altre informazioni su *ruoli*, *ClusterRole* e *associazioni*, vedere [Opzioni di accesso e identità per il servizio Azure Kubernetes][aks-concepts-identity].

Ad esempio, è possibile creare un ruolo che conceda l'accesso completo alle risorse dello spazio dei nomi denominato *finance-app*, come illustrato nell'esempio di manifesto YAML seguente:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

Viene quindi creato un oggetto RoleGroup che associa il Azure AD utente *developer1 \@ contoso.com* al Metodo Role, come illustrato nel manifesto YAML seguente:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

Quando *developer1 \@ contoso.com* viene autenticato nel cluster AKS, dispone delle autorizzazioni complete per le risorse nello spazio dei nomi *Finance-app* . In questo modo, l'accesso alle risorse viene separato e controllato logicamente. Il controllo degli accessi in base al ruolo di Kubernetes deve essere usato in combinazione con l'integrazione di Azure AD, come descritto nella sezione precedente.

Per informazioni su come usare i gruppi di Azure AD per controllare l'accesso alle risorse di Kubernetes usando il controllo degli accessi in base al ruolo, vedere [controllare l'accesso alle risorse del cluster usando il controllo degli accessi in base al ruolo e Azure Active Directory identità][azure-ad-rbac]

## <a name="use-azure-rbac"></a>Usa RBAC di Azure 
**Procedure consigliate** : usare il controllo degli accessi in base al ruolo di Azure per definire le autorizzazioni minime necessarie per gli utenti o i gruppi in una o più sottoscrizioni.

Per il funzionamento completo di un cluster AKS sono necessari due livelli di accesso: 
1. Accedere alla risorsa AKS nella sottoscrizione di Azure. Questo livello di accesso consente di controllare le operazioni di ridimensionamento o di aggiornamento del cluster usando le API AKS, oltre a eseguire il pull dei kubeconfig.
Per informazioni su come controllare l'accesso alla risorsa AKS e al kubeconfig, vedere [limitare l'accesso al file di configurazione del cluster](control-kubeconfig-access.md).

2. Accesso all'API Kubernetes. Questo livello di accesso è controllato da [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-kubernetes-rbac) (tradizionalmente) o dall'integrazione di Azure RBAC con AKS per l'autorizzazione Kubernetes.
Per informazioni su come concedere in modo granulare le autorizzazioni all'API Kubernetes usando RBAC di Azure, vedere usare il controllo [degli](manage-azure-rbac.md)accessi in base al ruolo di Azure

## <a name="use-pod-identities"></a>Usare le identità del pod

**Indicazioni sulle procedure consigliate**. Non usare credenziali fisse all'interno dei pod o delle immagini del contenitore, essendo a rischio di esposizione o uso improprio. In alternativa, usare le identità del pod per richiedere automaticamente l'accesso tramite una soluzione centrale di gestione delle identità di Azure AD. Le identità pod sono destinate all'uso solo con i pod Linux e le immagini contenitore.

Quando i pod richiedono l'accesso ad altri servizi di Azure, ad esempio Cosmos DB, Key Vault o Archiviazione BLOB, il pod necessita delle credenziali di accesso. Queste credenziali di accesso possono essere definite con l'immagine del contenitore o inserite come un segreto Kubernetes, ma devono essere create e assegnate manualmente. Spesso, le credenziali sono riutilizzate tra i pod e non vengono ruotate regolarmente.

Le identità gestite per le risorse di Azure, attualmente implementate come progetto open source AKS associato, consentono di richiedere automaticamente l'accesso ai servizi tramite Azure AD. Le credenziali per i pod non vengono definite manualmente, ma è possibile richiedere un token di accesso in tempo reale da usare per accedere solo ai relativi servizi assegnati. In servizio Azure Kubernetes vengono distribuiti due componenti dall'operatore del cluster per consentire ai pod di usare le identità gestite:

* Il **server NMI (Node Management Identity)** è un pod che viene eseguito come DaemonSet su ogni nodo nel cluster servizio Azure Kubernetes. Il server NMI ascolta le richieste del pod ai servizi di Azure.
* Il **controller MIC (Managed Identity Controller)** è un pod centrale che dispone di autorizzazioni per eseguire query nel server API Kubernetes e verifica la presenza di un mapping delle identità di Azure corrispondente a un pod.

Quando i pod richiedono l'accesso a un servizio di Azure, le regole di rete reindirizzano il traffico verso il server NMI (Node Management Identity). Il server NMI identifica i pod che richiedono l'accesso ai servizi di Azure in base al relativo indirizzo remoto ed esegue una query nel controller MIC (Managed Identity Controller). Il MIC verifica la presenza dei mapping delle identità di Azure nel cluster servizio Azure Kubernetes e il server NMI richiede quindi un token di accesso da Azure Active Directory (AD) in base al mapping delle identità del pod. Azure AD fornisce l'accesso al server NMI, che viene restituito al pod. Questo token di accesso può essere quindi usato dal pod per richiedere l'accesso ai servizi di Azure.

Nell'esempio seguente uno sviluppatore crea un pod che usa un'identità gestita per richiedere l'accesso al database SQL di Azure:

![Le identità del pod consentono a un pod di richiedere automaticamente l'accesso ad altri servizi](media/operator-best-practices-identity/pod-identities.png)

1. L'operatore del cluster crea innanzitutto un account del servizio che può essere usato per eseguire il mapping delle identità quando i pod richiedono l'accesso ai servizi.
1. Il server NMI e il MIC vengono distribuiti per inoltrare le richieste dei pod per i token di accesso ad Azure AD.
1. Uno sviluppatore distribuisce un pod con un'identità gestita che richiede un token di accesso tramite il server NMI.
1. Il token viene restituito al Pod e usato per accedere al database SQL di Azure

> [!NOTE]
> Le identità dei Pod gestiti sono un progetto open source e non sono supportate dal supporto tecnico di Azure.

Per usare le identità del pod, vedere l'articolo sulle [identità di Azure Active Directory per le applicazioni Kubernetes][aad-pod-identity].

## <a name="next-steps"></a>Passaggi successivi

Questo articolo sulle procedure consigliate ha illustrato l'autenticazione e l'autorizzazione per il cluster e le risorse. Per implementare alcune di queste procedure consigliate, vedere gli articoli seguenti:

* [Integrare Azure Active Directory con servizio Azure Kubernetes][aks-aad]
* [Usare identità gestite per le risorse di Azure con servizio Azure Kubernetes][aad-pod-identity]

Per altre informazioni sulle operazioni cluster in servizio Azure Kubernetes, vedere le procedure consigliate seguenti:

* [Isolamento cluster e multi-tenant][aks-best-practices-cluster-isolation]
* [Funzionalità di base dell'utilità di pianificazione di Kubernetes][aks-best-practices-scheduler]
* [Funzionalità avanzate dell'utilità di pianificazione di Kubernetes][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
