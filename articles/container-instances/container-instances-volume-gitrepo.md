---
title: Montare il volume gitRepo al gruppo di contenitori
description: Informazioni su come montare un volume gitRepo per clonare un repository Git in Istanze di Container
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5f361b0bbd4a02f754e47c8321a664af05036601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260707"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Montare un volume gitRepo in Istanze di Azure Container

Informazioni su come montare un volume *gitRepo* per clonare un repository Git in Istanze di Container.

> [!NOTE]
> Il montaggio di un volume *gitRepo* è attualmente limitato ai contenitori di Linux. Mentre stiamo lavorando per riunire tutte le funzionalità nei contenitori di Windows, è possibile trovare le attuali differenze della piattaforma nella [Panoramica](container-instances-overview.md#linux-and-windows-containers).

## <a name="gitrepo-volume"></a>Volume gitRepo

Il volume *gitRepo* monta una directory e clona il repository Git specificato al suo interno all'avvio del contenitore. Usando un volume *gitRepo* in Istanze di Container, è possibile evitare di aggiungere il codice per eseguire questa operazione nelle applicazioni.

Quando si monta un volume *gitRepo*, è possibile impostare tre proprietà per configurare il volume:

| Proprietà | Obbligatoria | Descrizione |
| -------- | -------- | ----------- |
| `repository` | Sì | URL completo, incluso `http://` o `https://`, del repository Git da clonare.|
| `directory` | No | Directory in cui clonare il repository. Il percorso non deve contenere né iniziare con "`..`".  Se si specifica "`.`", il repository viene clonato nella directory del volume. In caso contrario il repository Git viene clonato in una sottodirectory del nome dato all'interno della directory del volume. |
| `revision` | No | Hash commit della revisione da clonare. Se non specificato, la revisione `HEAD` viene clonata. |

## <a name="mount-gitrepo-volume-azure-cli"></a>Montare un volume gitRepo: interfaccia della riga di comando di Azure

Per montare un volume gitRepo quando si distribuiscono le istanze di contenitore con l'[interfaccia della riga di comando di Azure](/cli/azure), fornire i parametri `--gitrepo-url` e `--gitrepo-mount-path` al comando [az container create][az-container-create]. Facoltativamente, è possibile specificare la directory del volume in cui eseguire la clonazione (`--gitrepo-dir`) e l'hash commit della revisione da clonare (`--gitrepo-revision`).

Questo comando di esempio Clona l'applicazione di esempio Microsoft [ACI-HelloWorld][aci-helloworld] in `/mnt/aci-helloworld` nell'istanza del contenitore:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

Per verificare che il volume gitRepo sia stato montato, avviare una shell nel contenitore con [az container exec][az-container-exec] ed elencare la directory:

```azurecli
az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
```

```output
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>Montare un volume gitRepo: Resource Manager

Per montare un volume gitRepo quando si distribuiscono le istanze di contenitore con un [modello di Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups), popolare la matrice `volumes` nella sezione `properties` del gruppo di contenitore del modello. Successivamente, per ogni contenitore del gruppo di contenitori in cui si desidera montare il volume *gitRepo*, popolare la matrice `volumeMounts` nella sezione `properties` della definizione del contenitore.

Ad esempio, il modello di Resource Manager seguente crea un gruppo di contenitori costituito da un solo contenitore. Il contenitore clona due repository GitHub specificati dai blocchi del volume *gitRepo*. Il secondo volume include proprietà aggiuntive che specificano una directory in cui eseguire la clonazione e l'hash commit di una revisione specifica da clonare.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

La struttura della directory risultante dei due repository clonati definiti nel modello precedente è:

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Per un esempio di distribuzione di Istanze di Container con un modello di Azure Resource Manager, vedere [Deploy multi-container groups in Azure Container Instances](container-instances-multi-container-group.md) (Distribuire gruppi multicontenitore in Istanze di Container Azure).

## <a name="private-git-repo-authentication"></a>Autenticazione del repository privato

Per montare un volume gitRepo per un repository Git privato, specificare le credenziali nell'URL del repository. In genere, le credenziali sono sotto forma di un nome utente e un token di accesso personale (PAT) che concede l'accesso con ambito al repository.

Ad esempio, il parametro dell'interfaccia della riga di comando di Azure `--gitrepo-url` per un repository di GitHub privato sarà simile al seguente (dove "gituser" è il nome utente di GitHub e "abcdef1234fdsa4321abcdef" è il token di accesso personale dell'utente):

```console
--gitrepo-url https://gituser:abcdef1234fdsa4321abcdef@github.com/GitUser/some-private-repository
```

Per un repository Git di Azure Repos, specificare il nome utente (come nell'esempio seguente, è possibile usare "azurereposuser") in combinazione con un token di accesso personale valido:

```console
--gitrepo-url https://azurereposuser:abcdef1234fdsa4321abcdef@dev.azure.com/your-org/_git/some-private-repository
```

Per altre informazioni sui token di accesso personale per GitHub e Azure Repos, vedere gli argomenti seguenti:

GitHub: [Creazione di un token di accesso personale per la riga di comando][pat-github]

Azure Repos: [Create personal access tokens to authenticate access][pat-repos] (Creare token di accesso personale per autenticare l'accesso)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come montare altri tipi di volume in Istanze di Azure Container:

* [Mount an Azure file share in Azure Container Instances](container-instances-volume-azure-files.md) (Montare una condivisione file di Azure in Istanze di Azure Container)
* [Montare un volume emptyDir in Istanze di Azure Container](container-instances-volume-emptydir.md)
* [Montare un volume segreto in Istanze di Azure Container](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld
[pat-github]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[pat-repos]: /azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
