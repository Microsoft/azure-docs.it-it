---
title: Importare immagini del contenitore
description: Importare immagini del contenitore in un registro Azure Container usando le API di Azure, senza bisogno di eseguire comandi di Docker.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 66c3a8b19e2288c1f8720dd4fe79f348a11f052e
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660496"
---
# <a name="import-container-images-to-a-container-registry"></a>Importare immagini del contenitore in un registro contenitori

È possibile importare (copiare) facilmente immagini del contenitore in un registro Azure Container senza usare comandi di Docker. Ad esempio, è possibile importare immagini da un registro di sviluppo in un registro di produzione oppure copiare immagini di base da un registro pubblico.

Registro Azure Container supporta alcuni scenari comuni di copia di immagini da un registro esistente:

* Importazione da un registro pubblico

* Importazione da un altro registro Azure Container, nella stessa sottoscrizione di Azure o in una diversa

* Importazione da un registro contenitori privato non di Azure

L'importazione di immagini in un registro Azure Container offre i vantaggi seguenti rispetto all'uso dei comandi dell'interfaccia della riga di comando di Docker:

* Poiché l'ambiente client non necessita di un'installazione locale di Docker, importare qualsiasi immagine del contenitore, indipendentemente dal tipo di sistema operativo supportato.

* Quando si importano immagini multiarchitettura (ad esempio immagini ufficiali di Docker), vengono copiate le immagini di tutte le architetture e le piattaforme specificate nell'elenco di manifesti.

* Non è necessario che l'accesso ai registri di origine e di destinazione usi gli endpoint pubblici dei registri.

Per importare immagini del contenitore seguendo le procedure di questo articolo, è necessario eseguire l'interfaccia della riga di comando di Azure in Azure Cloud Shell o localmente (si consiglia la versione 2.0.55 o una versione successiva). Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

> [!NOTE]
> Se occorre distribuire immagini del contenitore identiche tra più aree di Azure, Registro Azure Container supporta anche la [replica geografica](container-registry-geo-replication.md). Con la replica geografica di un registro di sistema (è necessario il livello di servizio Premium), è possibile servire più aree con nomi identici per le immagini e i tag da un unico registro.
>

## <a name="prerequisites"></a>Prerequisiti

Se non si ha già un registro Azure Container, crearne uno. Per i passaggi, vedere [Guida introduttiva: creare un registro contenitori privato usando l'interfaccia della](container-registry-get-started-azure-cli.md)riga di comando di Azure.

Per importare un'immagine in un registro contenitori di Azure, l'identità deve avere le autorizzazioni di scrittura per il registro di sistema di destinazione (almeno ruolo Collaboratore o un ruolo personalizzato che consente l'azione importImage). Vedere [Ruoli e autorizzazioni di Registro Azure Container](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Importazione da un registro pubblico

### <a name="import-from-docker-hub"></a>Importare immagini dall'hub Docker

Usare ad esempio il comando [az acr import][az-acr-import] per importare l'immagine multiarchitettura `hello-world:latest` dall'hub Docker in un registro denominato *myregistry*. Poiché `hello-world` è un'immagine ufficiale dell'hub Docker, si trova nel repository `library` predefinito. Includere il nome del repository e, se si vuole, un tag nel valore del parametro dell'immagine `--source`. Si può anche identificare un'immagine in base al relativo digest del manifesto invece che in base a un tag, in modo da garantire una particolare versione dell'immagine.
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

È possibile verificare che più manifesti siano associati a questa immagine eseguendo il comando `az acr repository show-manifests`:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

L'esempio seguente importa un'immagine pubblica dal repository `tensorflow` nell'hub Docker:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Importare immagini dal registro contenitori di Microsoft

Ad esempio, importare l' `ltsc2019` immagine di Windows Server Core dal `windows` repository in Microsoft container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-another-azure-container-registry"></a>Importazione da un altro registro Azure Container

È possibile importare un'immagine da un altro registro Azure Container usando le autorizzazioni di Azure Active Directory integrate.

* È necessario che l'identità disponga delle autorizzazioni Azure Active Directory per la lettura dal registro di sistema di origine (ruolo lettore) e per l'importazione nel registro di sistema di destinazione (ruolo Collaboratore o un [ruolo personalizzato](container-registry-roles.md#custom-roles) che consente l'azione importImage).

* Il registro può trovarsi nella stessa sottoscrizione di Azure o in una sottoscrizione diversa nello stesso tenant di Active Directory.

* L' [accesso pubblico](container-registry-access-selected-networks.md#disable-public-network-access) al registro di sistema di origine potrebbe essere disabilitato. Se l'accesso pubblico è disabilitato, specificare il registro di sistema di origine tramite l'ID risorsa anziché il nome del server di accesso del registro di sistema.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importare immagini da un registro nella stessa sottoscrizione

Ad esempio, importare l'immagine `aci-helloworld:latest` dal registro di origine *mysourceregistry* a *myregistry* nella stessa sottoscrizione di Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

Nell'esempio seguente l'immagine viene importata in `aci-helloworld:latest` *Registro* di sistema da un *mysourceregistry* del registro di sistema di origine in cui l'accesso all'endpoint pubblico del registro di sistema è disabilitato. Specificare l'ID risorsa del registro di origine con il parametro `--registry`. Si noti che il `--source` parametro specifica solo il repository di origine e il tag, non il nome del server di accesso del registro di sistema.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

L'esempio seguente importa un'immagine in base al digest del manifesto (hash SHA-256, rappresentato come `sha256:...`) anziché in base a un tag:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importare immagini da un registro in un'altra sottoscrizione

Nell'esempio seguente *mysourceregistry* è in una sottoscrizione diversa da *myregistry* nello stesso tenant di Active Directory. Specificare l'ID risorsa del registro di origine con il parametro `--registry`. Si noti che il `--source` parametro specifica solo il repository di origine e il tag, non il nome del server di accesso del registro di sistema.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importare immagini da un registro usando le credenziali dell'entità servizio

Per importare immagini da un registro a cui non è possibile accedere mediante le autorizzazioni di Active Directory, si possono usare le credenziali dell'entità servizio (se disponibili). Specificare l'ID app e la password di un'[entità servizio](container-registry-auth-service-principal.md) di Active Directory con accesso ACRPull al registro di origine. L'uso di un'entità servizio è utile per i sistemi di compilazione e altri sistemi automatici che devono importare immagini nel registro.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importazione da un registro contenitori privato non di Azure

Per importare un'immagine da un registro privato, specificare credenziali che consentano l'accesso pull al registro. Ad esempio, eseguire il pull di un'immagine da un registro privato di Docker: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come importare immagini del contenitore in un registro Azure Container da un registro pubblico o da un altro registro privato. Per altre opzioni di importazione di immagini, vedere la documentazione di riferimento sul comando [az acr import][az-acr-import]. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
