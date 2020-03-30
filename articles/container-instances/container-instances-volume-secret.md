---
title: Montare il volume segreto nel gruppo di contenitoriMount secret volume to container group
description: Informazioni su come montare un volume segreto per archiviare informazioni sensibili per l'accesso da parte delle istanze di contenitore
ms.topic: article
ms.date: 07/19/2018
ms.openlocfilehash: 913e3d147519bc73c3c57b8da383f9d373f3666d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78249942"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Montare un volume segreto in Istanze di Azure Container

Usare un volume *segreto* per fornire informazioni sensibili ai contenitori di un gruppo di contenitori. Il volume *segreto* archivia i segreti nei file all'interno del volume a cui i contenitori del gruppo di contenitori possono accedere. Archiviando segreti in un volume *segreto* è possibile evitare di aggiungere dati sensibili, quali chiavi SSH o credenziali del database, al codice dell'applicazione.

Tutti i volumi *segreti* sono supportati da [tmpfs][tmpfs], un file system supportato da RAM; i relativi contenuti non vengono mai scritti in risorse di archiviazione non volatili.

> [!NOTE]
> I volumi *segreti* attualmente sono limitati ai contenitori Linux. Per informazioni su come passare variabili di ambiente sicure per i contenitori Windows e Linux, vedere [Impostare variabili di ambiente](container-instances-environment-variables.md). Mentre stiamo lavorando per portare tutte le funzionalità per i contenitori di Windows, è possibile trovare le differenze di piattaforma correnti nella [panoramica](container-instances-overview.md#linux-and-windows-containers).

## <a name="mount-secret-volume---azure-cli"></a>Montare un volume segreto - Interfaccia della riga di comando di Azure

Per distribuire un contenitore con uno o più segreti tramite l'interfaccia della riga di comando di Azure, includere i parametri `--secrets` e `--secrets-mount-path` nel comando [az container create][az-container-create]. Questo esempio monta un volume *segreto* costituito da due segreti, "mysecret1" e "mysecret2", in `/mnt/secrets`:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

L'output di [az container exec][az-container-exec] seguente mostra l'apertura di una shell nel contenitore in esecuzione, l'elencazione dei file all'interno del volume segreto e la successiva visualizzazione dei relativi contenuti:

```azurecli
az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Montare un volume segreto - YAML

È possibile distribuire gruppi di contenitori con l'interfaccia della riga di comando di Azure e un [modello YAML](container-instances-multi-container-yaml.md). La distribuzione mediante il modello YAML è il metodo preferito quando si distribuiscono gruppi di contenitori costituiti da più contenitori.

Quando si distribuisce un modello YAML, i valori del segreto devono essere **con codifica Base64** nel modello. Tuttavia, i valori del segreto sono visualizzati in testo non crittografato all'interno dei file nel contenitore.

Il modello YAML seguente definisce un gruppo di contenitori con un solo contenitore che monta un volume *segreto* in `/mnt/secrets`. Il volume segreto ha due segreti, "mysecret1" e "mysecret2".

```yaml
apiVersion: '2018-10-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Per eseguire la distribuzione con il modello YAML, salvare il codice YAML precedente in un file denominato `deploy-aci.yaml`, quindi eseguire il comando [az container create][az-container-create] con il parametro `--file`:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Montare un volume segreto - Resource Manager

Oltre alla distribuzione dall'interfaccia della riga di comando e da YAML, è possibile distribuire un gruppo di contenitori usando un [modello di Resource Manager](/azure/templates/microsoft.containerinstance/containergroups) di Azure.

Popolare innanzitutto la matrice `volumes` nella sezione `properties` del gruppo di contenitori del modello. Quando si esegue la distribuzione con un modello di Resource Manager, i valori del segreto devono essere **con codifica Base64** nel modello. Tuttavia, i valori del segreto sono visualizzati in testo non crittografato all'interno dei file nel contenitore.

Successivamente per ogni contenitore del gruppo di contenitori in cui si desidera montare il volume *segreto*, popolare la matrice `volumeMounts` nella sezione `properties` della definizione del contenitore.

Il modello di Resource Manager seguente definisce un gruppo di contenitori con un solo contenitore che monta un volume *segreto* in `/mnt/secrets`. Il volume segreto ha due segreti, "mysecret1" e "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Per eseguire la distribuzione con il modello di Resource Manager, salvare il codice JSON precedente in un file denominato `deploy-aci.json`, quindi eseguire il comando [az group deployment create][az-group-deployment-create] con il parametro `--template-file`:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Passaggi successivi

### <a name="volumes"></a>Volumi

Informazioni su come montare altri tipi di volume in Istanze di Azure Container:

* [Mount an Azure file share in Azure Container Instances](container-instances-volume-azure-files.md) (Montare una condivisione file di Azure in Istanze di Azure Container)
* [Montare un volume emptyDir in Istanze di Azure Container](container-instances-volume-emptydir.md)
* [Montare un volume gitRepo in Istanze di Azure Container](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Variabili di ambiente sicure

Un altro metodo per fornire informazioni riservate ai contenitori (inclusi i contenitori Windows) è tramite l'uso di [variabili di ambiente sicure](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
