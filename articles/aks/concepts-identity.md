---
title: Concetti - Accesso e identità nei servizi Azure Kubernetes
description: Informazioni su accesso e identità nel servizio Azure Kubernetes, inclusi integrazione di Azure Active Directory, controllo degli accessi in base al ruolo di Kubernetes e ruoli e associazioni.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259604"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Opzioni di accesso e identità per il servizio Azure Kubernetes

Esistono diversi modi per eseguire l'autenticazione con i cluster Kubernetes e proteggerli. Usando i controlli degli accessi in base al ruolo, è possibile concedere agli utenti o ai gruppi l'accesso solo alle risorse necessarie. Con il servizio Azure Kubernetes, è possibile migliorare ulteriormente la struttura di autorizzazioni e la sicurezza usando Azure Active Directory. Questi approcci consentono di proteggere i carichi di lavoro dell'applicazione e i dati dei clienti.

Questo articolo introduce i principali concetti utili per l'autenticazione e l'assegnazione di autorizzazioni nel servizio Azure Kubernetes:

- [Account del servizio Kubernetes](#kubernetes-service-accounts)
- [Integrazione di Azure Active Directory](#azure-active-directory-integration)
- [Controlli degli accessi in base al ruolo](#role-based-access-controls-rbac)
- [Role e ClusterRole](#roles-and-clusterroles)
- [RoleBinding e ClusterRoleBinding](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Account del servizio Kubernetes

Uno dei tipi di utenti primari in Kubernetes è un *account del servizio*. Un account del servizio esiste ed è gestito nell'API Kubernetes. Le credenziali per gli account del servizio vengono archiviate come segreti di Kubernetes e possono quindi essere usate da pod autorizzati a comunicare con il server dell'API. La maggior parte delle richieste dell'API fornisce un token di autenticazione per un account del servizio o un account utente normale.

Gli account utente normali consentono un accesso più tradizionale per amministratori o sviluppatori umani, non solo per servizi e processi. Kubernetes non fornisce una soluzione di gestione delle identità in cui vengono archiviati gli account utente regolari e le password. ma è possibile integrare in Kubernetes soluzioni di gestione delle identità esterne. Per i cluster del servizio Azure Kubernetes, la soluzione integrata di gestione delle identità è Azure Active Directory.

Per altre informazioni sulle opzioni di gestione delle identità in Kubernetes, vedere [Kubernetes authentication][kubernetes-authentication] (Autenticazione di Kubernetes).

## <a name="azure-active-directory-integration"></a>Integrazione di Azure Active Directory

È possibile migliorare la sicurezza dei cluster del servizio Azure Kubernetes con l'integrazione di Azure Active Directory (AD). Azure Active Directory (Azure AD), nato da decenni di esperienza nella gestione delle identità aziendali, è un servizio directory e di gestione delle identità basato sul cloud multi-tenant, che combina i principali servizi directory, la gestione dell'accesso alle applicazioni e la protezione delle identità. Con Azure AD, è possibile integrare le identità locali nei cluster del servizio Azure Kubernetes per offrire un'unica origine per la sicurezza e la gestione degli account.

![Integrazione di Azure Active Directory con i cluster del servizio Azure Kubernetes](media/concepts-identity/aad-integration.png)

Con i cluster del servizio Azure Kubernetes integrati in AD Azure, è possibile concedere agli utenti o ai gruppi l'accesso alle risorse di Kubernetes all'interno di uno spazio dei nomi o nel cluster. Per ottenere un contesto di configurazione `kubectl`, un utente può eseguire il comando [az servizio Azure Kubernetes get-credentials][az-aks-get-credentials]. Quando un utente interagisce quindi con il cluster del servizio Azure Kubernetes usando `kubectl`, gli viene chiesto di accedere con le credenziali di Azure AD. Questo approccio fornisce un'unica origine per la gestione degli account utente e le credenziali della password. L'utente può accedere solo risorse definite dall'amministratore del cluster.

L'autenticazione di Azure AD nei cluster del servizio Azure Kubernetes usa OpenID Connect, un livello di identità basato sul protocollo OAuth 2.0. OAuth 2.0 definisce i meccanismi per ottenere e usare i token di accesso per accedere alle risorse protette e OpenID Connect implementa l'autenticazione come estensione al processo di autorizzazione di OAuth 2.0. Per altre informazioni su OpenID Connect, vedere la [documentazione di OpenID Connect][openid-connect]. Per verificare i token di autenticazione ottenuti da Azure AD tramite OpenID Connect, i cluster del servizio Azure Kubernetes usano l'autenticazione del token del webhook di Kubernetes. Per altre informazioni, vedere la [documentazione sull'autenticazione del token del webhook][webhook-token-docs].

## <a name="role-based-access-controls-rbac"></a>Controlli degli accessi in base al ruolo

Per fornire un filtro granulare delle azioni che gli utenti possono eseguire, Kubernetes usa i controlli degli accessi in base al ruolo. Questo meccanismo di controllo consente di assegnare agli utenti o ai gruppi di utenti l'autorizzazione per eseguire operazioni come la creazione o la modifica di risorse oppure la visualizzazione di log generati dall'esecuzione dei carichi di lavoro dell'applicazione. È possibile definire l'ambito di queste autorizzazioni in un singolo spazio dei nomi o concederle nell'intero cluster del servizio Azure Kubernetes. Con il controllo degli accessi in base al ruolo, si creano i *ruoli* per definire le autorizzazioni e quindi si assegnano tali ruoli agli utenti con le *associazioni dei ruoli*.

Per altre informazioni, vedere [Uso di autorizzazioni del controllo degli accessi in base al ruolo][kubernetes-rbac].

### <a name="azure-role-based-access-controls-rbac"></a>Controlli degli accessi in base al ruolo di Azure
Un meccanismo aggiuntivo per il controllo dell'accesso alle risorse è costituito dai controlli degli accessi in base al ruolo di Azure. Il controllo degli accessi in base al ruolo di Kubernetes è progettato per usare le risorse nel cluster del servizio Azure Kubernetes, mentre il controllo degli accessi in base al ruolo di Azure è progettato per usare le risorse nella sottoscrizione di Azure. Con il controllo degli accessi in base al ruolo di Azure si crea una *definizione del ruolo* che determina le autorizzazioni da applicare. Questa definizione del ruolo viene quindi assegnata a un utente o a un gruppo per un particolare *ambito*, che può essere una singola risorsa o gruppo di risorse oppure l'intera sottoscrizione.

Per altre informazioni, vedere [Informazioni sul controllo degli accessi in base al ruolo di Azure][azure-rbac].

## <a name="roles-and-clusterroles"></a>Role e ClusterRole

Prima di assegnare le autorizzazioni agli utenti con il controllo degli accessi in base al ruolo di Kubernetes, si definiscono tali autorizzazioni come *Role*. I ruoli di Kubernetes *concedono* le autorizzazioni. Non è possibile *negare* un'autorizzazione.

I ruoli vengono usati per concedere le autorizzazioni all'interno di uno spazio dei nomi. Se è necessario concedere le autorizzazioni nell'intero cluster o alle risorse cluster all'esterno di un determinato spazio dei nomi, è invece possibile usare i *ClusterRole*.

Un ClusterRole concede le autorizzazioni alle risorse nello stesso modo, ma può essere applicato alle risorse di tutto il cluster, non a uno specifico spazio dei nomi.

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBinding e ClusterRoleBinding

Dopo aver definito i ruoli per concedere le autorizzazioni alle risorse, si assegnano tali autorizzazioni di controllo degli accessi in base al ruolo di Kubernetes con un *RoleBinding*. Se il cluster del servizio Azure Kubernetes viene integrato con Azure Active Directory, le associazioni consentono di concedere agli utenti di Azure AD le autorizzazioni per eseguire azioni all'interno del cluster.

Le associazioni dei ruoli vengono usate per assegnare i ruoli per un determinato spazio dei nomi. Questo approccio consente di separare logicamente un singolo cluster del servizio Azure Kubernetes e gli utenti potranno accedere solo alle risorse dell'applicazione nello spazio dei nomi assegnato. Se è necessario associare i ruoli nell'intero cluster o alle risorse cluster all'esterno di un determinato spazio dei nomi, è invece possibile usare i *ClusterRoleBinding*.

Un ClusterRoleBinding associa i ruoli agli utenti nello stesso modo, ma può essere applicato alle risorse di tutto il cluster, non a uno specifico spazio dei nomi. Questo approccio consente di concedere agli amministratori o ai tecnici del supporto l'accesso a tutte le risorse nel cluster del servizio Azure Kubernetes.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Azure AD e il controllo degli accessi in base al ruolo di Kubernetes, vedere [Integrare Azure Active Directory con il servizio Azure Kubernetes][aks-aad].

Per le procedure consigliate associate, vedere [Procedure consigliate per l'autenticazione e l'autorizzazione in AKS][operator-best-practices-identity].

Per altre informazioni sui concetti fondamentali relativi a Kubernetes e al servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Cluster e carichi di lavoro di Kubernetes/servizio Azure Kubernetes][aks-concepts-clusters-workloads]
- [Sicurezza di Kubernetes/servizio Azure Kubernetes][aks-concepts-security]
- [Kubernetes / Reti virtuali in servizio Azure Kubernetes][aks-concepts-network]
- [Archiviazione in Kubernetes/servizio Azure Kubernetes][aks-concepts-storage]
- [Ridimensionamento in Kubernetes/servizio Azure Kubernetes][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
