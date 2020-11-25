---
title: Estrazione frasi chiave la configurazione di Kubernetes e i passaggi di distribuzione
titleSuffix: Azure Cognitive Services
description: Estrazione frasi chiave la configurazione di Kubernetes e i passaggi di distribuzione
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 27c78566877f27e80ae5ae27c5250f228c7ae676
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017868"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>Distribuire il contenitore Estrazione frasi chiave in un cluster AKS

1. Aprire l'interfaccia della riga di comando di Azure e accedere ad Azure.

    ```azurecli
    az login
    ```

1. Accedere al cluster AKS. Sostituire `your-cluster-name` e `your-resource-group` con i valori appropriati.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Dopo l'esecuzione di questo comando, viene segnalato un messaggio simile al seguente:

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Se sono disponibili più sottoscrizioni per l'account Azure e il `az aks get-credentials` comando restituisce un errore, un problema comune è che si sta usando una sottoscrizione non corretta. Impostare il contesto della sessione dell'interfaccia della riga di comando di Azure in modo che usi la stessa sottoscrizione con cui sono state create le risorse e riprovare.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Aprire l'editor di testo desiderato. In questo esempio viene usato Visual Studio Code.

    ```console
    code .
    ```

1. All'interno dell'editor di testo creare un nuovo file denominato *chiave phrase. YAML* e incollare il codice YAML seguente. Assicurarsi di sostituire `billing/value` e `apikey/value` con le proprie informazioni.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. Salvare il file e chiudere l'editor di testo.
1. Eseguire il `apply` comando Kubernetes con il file di *chiave phrase. YAML* come destinazione:

    ```console
    kubectl apply -f keyphrase.yaml
    ```

    Quando il comando applica correttamente la configurazione della distribuzione, viene visualizzato un messaggio simile all'output seguente:

    ```output
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. Verificare che il POD sia stato distribuito:

    ```console
    kubectl get pods
    ```

    Output per lo stato di esecuzione del Pod:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Verificare che il servizio sia disponibile e ottenere l'indirizzo IP.

    ```console
    kubectl get services
    ```

    Output per lo stato di esecuzione del servizio di *frasi chiave* nel pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
