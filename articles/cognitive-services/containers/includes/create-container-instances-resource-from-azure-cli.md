---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa dell'istanza di contenitore di Azure dall'interfaccia della riga di comando di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 87007d3df3fe44ab04a330b09b8e495ec4b47e54
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97865963"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Creare una risorsa dell'istanza di contenitore di Azure dall'interfaccia della riga di comando di Azure

Il YAML seguente definisce la risorsa dell'istanza di contenitore di Azure. Copiare e incollare il contenuto in un nuovo file, denominato `my-aci.yaml` e sostituire i valori commentati con quelli personalizzati. Vedere il [formato del modello][template-format] per YAML valido. Vedere i [repository del contenitore e le immagini][repositories-and-images] per i nomi delle immagini disponibili e il repository corrispondente. Per altre informazioni sul riferimento YAML per le istanze di contenitore, vedere informazioni di [riferimento su YAML: istanze di contenitore di Azure][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
properties:
  imageRegistryCredentials: # This is only required if you are pulling a non-public image that requires authentication to access. For example Text Analytics for health.
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Non tutti i percorsi hanno la stessa disponibilità di CPU e memoria. Vedere la tabella [location and resources][location-to-resource] per l'elenco delle risorse disponibili per i contenitori per località e sistema operativo.

Ci affidiamo al file YAML creato per il [`az container create`][azure-container-create] comando. Dall'interfaccia della riga di comando di Azure, eseguire il `az container create` comando sostituendo `<resource-group>` con il proprio. Inoltre, per proteggere i valori in una distribuzione YAML, fare riferimento a [valori sicuri][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

L'output del comando è `Running...` se valido, dopo qualche minuto l'output viene modificato in una stringa JSON che rappresenta la risorsa ACI appena creata. L'immagine del contenitore è più che probabile che non sia disponibile per un periodo di tempo, ma la risorsa viene ora distribuita.

> [!TIP]
> Prestare particolare attenzione alle posizioni delle offerte di servizi cognitivi di Azure in anteprima pubblica, poiché YAML deve essere modificato di conseguenza in modo da corrispondere al percorso.

[azure-container-create]: /cli/azure/container#az-container-create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
