---
title: Opzioni di autenticazione del registro
description: Opzioni di autenticazione per un registro contenitori di Azure privato, incluso l'accesso con un'identità di Azure Active Directory, l'uso di entità servizio e l'uso di credenziali di amministratore facoltative.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: d12895502ecd30991fbef836903a8ceea445b770
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285502"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Eseguire l'autenticazione con un Registro Azure Container

Esistono diversi modi per eseguire l'autenticazione con un Registro Azure Container, ognuno dei quali è applicabile a uno o più scenari di utilizzo del registro.

Le modalità consigliate includono l'autenticazione a un registro direttamente tramite un [singolo account di accesso](#individual-login-with-azure-ad), oppure le applicazioni e gli agenti di orchestrazione dei contenitori possono eseguire l'autenticazione automatica o senza test tramite un' [entità servizio](#service-principal)Azure Active Directory (Azure ad).

## <a name="authentication-options"></a>Opzioni di autenticazione

Nella tabella seguente sono elencati i metodi di autenticazione disponibili e gli scenari tipici. Per informazioni dettagliate, vedere contenuti collegati.

| Metodo                               | Come eseguire l'autenticazione                                           | Scenari                                                            | Controllo degli accessi in base al ruolo di Azure                             | Limitazioni                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Identità di Active Directory singola](#individual-login-with-azure-ad)                | `az acr login` nell'interfaccia della riga di comando di Azure                             | Push/pull interattivo da parte di sviluppatori e tester                                    | Sì                              | Il token AD deve essere rinnovato ogni 3 ore     |
| [Entità servizio Active Directory](#service-principal)                  | `docker login`<br/><br/>`az acr login` nell'interfaccia della riga di comando di Azure<br/><br/> Impostazioni di accesso del registro di sistema nelle API o negli strumenti<br/><br/> [Kubernetes segreto pull](container-registry-auth-kubernetes.md)                                           | Push automatico dalla pipeline CI/CD<br/><br/> Pull automatico in Azure o servizi esterni  | Sì                              | La scadenza predefinita della password SP è 1 anno       |                                                           
| [Eseguire l'integrazione con AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Connetti il registro di sistema durante la creazione o l'aggiornamento del cluster AKS  | Pull automatico nel cluster AKS                                                  | No, solo accesso pull             | Disponibile solo con il cluster AKS            |
| [Identità gestita per le risorse di Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` nell'interfaccia della riga di comando di Azure                                       | Push automatico dalla pipeline CI/CD di Azure<br/><br/> Pull automatico nei servizi di Azure<br/><br/>   | Sì                              | Usare solo da servizi di Azure selezionati che [supportano identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Utente amministratore](#admin-account)                            | `docker login`                                          | Push/pull interattivo per singolo sviluppatore o tester<br/><br/>Distribuzione del portale dell'immagine dal registro di sistema al servizio app Azure o alle istanze di contenitore di Azure                      | No, sempre pull e Push Access  | Account singolo per registro di sistema, non consigliato per più utenti         |
| [Token di accesso con ambito repository](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` nell'interfaccia della riga di comando di Azure   | Push/pull interattivo nel repository per singolo sviluppatore o tester<br/><br/> Push/Pull automatico nel repository da un singolo sistema o dispositivo esterno                  | Sì                              | Attualmente non integrato con identità di Active Directory  |

## <a name="individual-login-with-azure-ad"></a>Accesso individuale con Azure AD

Quando si usa direttamente il registro di sistema, ad esempio il pull di immagini e il push di immagini da una workstation di sviluppo a un registro creato, effettuare l'autenticazione con la propria identità di Azure. Accedere all'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) con [AZ login](/cli/azure/reference-index#az-login), quindi eseguire il comando [AZ ACR login](/cli/azure/acr#az-acr-login) :

```azurecli
az login
az acr login --name <acrName>
```

Quando si esegue l'accesso con `az acr login`, l'interfaccia della riga di comando usa il token creato con l'esecuzione di `az login` per l'autenticazione della sessione con il registro. Per completare il flusso di autenticazione, è necessario che l'interfaccia della riga di comando Docker e il daemon Docker siano installati e in esecuzione nell'ambiente in uso. `az acr login` Usa il client Docker per impostare un token di Azure Active Directory nel `docker.config` file. Dopo aver effettuato l'accesso in questo modo, le credenziali vengono memorizzate nella cache e i successivi comandi `docker` nella sessione non richiedono il nome utente o la password.

> [!TIP]
> Usare anche `az acr login` per autenticare una singola identità quando si desidera effettuare il push o il pull di elementi diversi dalle immagini Docker nel registro di sistema, ad esempio gli [artefatti OCI](container-registry-oci-artifacts.md).  

Per l'accesso al registro di sistema, il token usato da `az acr login` è valido per **3 ore**, quindi è consigliabile accedere sempre al registro di sistema prima di eseguire un `docker` comando. In caso di scadenza del token, è possibile aggiornarlo usando di nuovo il comando `az acr login` per eseguire nuovamente l'autenticazione. 

`az acr login`L'uso di con le identità di Azure fornisce il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/role-assignments-portal.md). Per alcuni scenari, potrebbe essere necessario accedere a un registro con la propria identità personale in Azure AD o configurare altri utenti di Azure con [autorizzazioni e ruoli di Azure](container-registry-roles.md)specifici. Per gli scenari tra servizi o per gestire le esigenze di un gruppo di lavoro o di un flusso di lavoro di sviluppo in cui non si vuole gestire l'accesso singolo, è anche possibile accedere con un' [identità gestita per le risorse di Azure](container-registry-authentication-managed-identity.md).

### <a name="az-acr-login-with---expose-token"></a>AZ ACR login con--Expose-token

In alcuni casi, potrebbe essere necessario eseguire l'autenticazione con `az acr login` quando il daemon Docker non è in esecuzione nell'ambiente in uso. Ad esempio, potrebbe essere necessario eseguire `az acr login` in uno script in Azure cloud Shell, che fornisce l'interfaccia della riga di comando di Docker, ma non esegue il daemon docker.

Per questo scenario, eseguire `az acr login` prima con il `--expose-token` parametro. Questa opzione espone un token di accesso anziché l'accesso tramite l'interfaccia della riga di comando di Docker.

```azurecli
az acr login --name <acrName> --expose-token
```

Output Visualizza il token di accesso, abbreviato qui:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 
Per l'autenticazione del registro di sistema, è consigliabile archiviare le credenziali del token in un luogo sicuro e seguire le procedure consigliate per gestire l' [accesso a Docker](https://docs.docker.com/engine/reference/commandline/login/). Ad esempio, archiviare il valore del token in una variabile di ambiente:

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

Eseguire quindi `docker login` , passando `00000000-0000-0000-0000-000000000000` come nome utente e usando il token di accesso come password:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```

## <a name="service-principal"></a>Entità servizio

Se si assegna un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) al registro, l'applicazione o il servizio può usarla per eseguire l'autenticazione headless. Le entità servizio consentono il [controllo degli accessi in base al ruolo di Azure (RBAC)](../role-based-access-control/role-assignments-portal.md) a un registro ed è possibile assegnare più entità servizio a un registro. L'uso di più entità servizio consente di definire un accesso diverso per applicazioni diverse.

I ruoli disponibili per un registro contenitori includono:

* **AcrPull**: pull

* **AcrPush**: pull e push

* **Proprietario**: pull, push e assegnazione di ruoli ad altri utenti

Per un elenco completo dei ruoli, vedere [Ruoli e autorizzazioni di Registro Azure Container](container-registry-roles.md).

Per gli script dell'interfaccia della riga di comando per creare un'entità servizio per l'autenticazione con un registro contenitori di Azure e altre linee guida, vedere [autenticazione container Registry di Azure con entità servizio](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Account amministratore

Ogni registro contenitori include un account utente amministratore che, per impostazione predefinita, è disabilitato. È possibile abilitare l'utente amministratore e gestirne le credenziali nel portale di Azure oppure tramite l'interfaccia della riga di comando o altri strumenti di Azure. L'account amministratore dispone di autorizzazioni complete per il registro di sistema.

L'account amministratore è attualmente necessario per alcuni scenari per la distribuzione di un'immagine da un registro contenitori a determinati servizi di Azure. Ad esempio, l'account amministratore è necessario quando si usa il portale di Azure per distribuire un'immagine del contenitore da un registro direttamente in [istanze di contenitore di Azure](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) o [app Web di Azure per contenitori](container-registry-tutorial-deploy-app.md).

> [!IMPORTANT]
> L'account amministratore è pensato per consentire l'accesso al registro a un singolo utente, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore tra più utenti. Tutti gli utenti che si autenticano con l'account amministratore vengono visualizzati come un unico utente con accesso di tipo push e pull al registro. Se si modifica o si disattiva questo account, tutti gli utenti che ne usano le credenziali non potranno più accedere al registro. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità.
>

L'account amministratore è dotato di due password, entrambe rigenerabili. L'uso di due password consente di mantenere la connessione al registro usando una password mentre si rigenera l'altra. Se l'account amministratore è abilitato, è possibile passare il nome utente e una password al comando `docker login` quando richiesto, per eseguire l'autenticazione di base al registro. Ad esempio:

```
docker login myregistry.azurecr.io 
```

Per le procedure consigliate per gestire le credenziali di accesso, vedere le informazioni di riferimento sul comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

Per consentire a un utente amministratore di accedere a un registro esistente, è possibile usare il parametro `--admin-enabled` del comando [az acr update](/cli/azure/acr#az-acr-update) nell'interfaccia della riga di comando di Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Per abilitare l'utente amministratore nel portale di Azure, passare al registro interessato, selezionare **Chiavi di accesso** in **IMPOSTAZIONI**, quindi selezionare **Abilita** in **Utente amministratore**.

![Abilitare l'interfaccia utente dell'utente amministratore nel portale di Azure][auth-portal-01]

## <a name="next-steps"></a>Passaggi successivi

* [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
