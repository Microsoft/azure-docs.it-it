---
title: Gestire i segreti dell'applicazione Azure Service Fabric mesh
description: Come gestire i segreti dell'applicazione per poter creare e distribuire in modo sicuro un'applicazione Service Fabric Mesh.
ms.date: 4/2/2019
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3be0c2b21c3405f4f42b2ff4d02ca95c78956de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626961"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Gestire i segreti delle applicazioni Azure Service Fabric Mesh

> [!IMPORTANT]
> L'anteprima di Azure Service Fabric mesh è stata ritirata. Le nuove distribuzioni non saranno più consentite tramite l'API Service Fabric mesh. Il supporto per le distribuzioni esistenti continuerà fino al 28 aprile 2021.
> 
> Per informazioni dettagliate, vedere il [ritiro anteprima di Azure Service Fabric mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Service Fabric Mesh supporta i segreti come risorse di Azure. Un segreto di Service Fabric Mesh può essere costituito da informazioni di testo riservate, come stringhe di connessione per l'archiviazione, password o altri valori che devono essere archiviati e trasmessi in modo sicuro. Questo articolo illustra come usare il servizio di archiviazione sicura di Service Fabric per distribuire e gestire i segreti.

Un segreto dell'applicazione Mesh è costituto da:
* Una risorsa **Segreti**, ossia un contenitore in cui sono archiviati i segreti di testo. I segreti contenuti nella risorsa **Segreti** vengono archiviati e trasmessi in modo sicuro.
* Una o più risorse **Segreti/Valori** archiviate nel contenitore di risorse **Segreti**. Ogni risorsa **Segreti/Valori** è contraddistinta da un numero di versione. Non è possibile modificare una versione di una risorsa **Segreti/Valori**, si può solo aggiungere una nuova versione.

Il processo di gestione dei segreti è costituito dai passaggi seguenti:
1. Dichiarare una risorsa dei **segreti** di rete in un file YAML o JSON del modello di risorsa di Azure usando le definizioni di inlinedValue Kind e SecretsStoreRef ContentType.
2. Dichiarare le risorse di **segreti/valori** mesh in un file YAML o JSON del modello di risorsa di Azure che verrà archiviato nella risorsa **Secrets** (dal passaggio 1).
3. Modificare l'applicazione Mesh in modo che faccia riferimento ai valori dei segreti Mesh.
4. Distribuire o eseguire l'aggiornamento in sequenza dell'applicazione Mesh per utilizzare i valori dei segreti.
5. Usare i comandi dell'interfaccia della riga di comando di Azure "az" per la gestione del ciclo di vita del servizio di archiviazione sicura.

## <a name="declare-a-mesh-secrets-resource"></a>Dichiarare una risorsa Segreti di Mesh
Una risorsa di segreti mesh viene dichiarata in un file JSON o YAML del modello di risorsa di Azure usando la definizione di tipo inlinedValue. La risorsa Segreti di Mesh supporta i segreti originati dal servizio di archiviazione sicura. 
>
Di seguito è riportato un esempio di dichiarazione di risorse Segreti di Mesh in un file JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    }
  ]
}
```
Di seguito è riportato un esempio di dichiarazione di risorse Segreti di Mesh in un file YAML:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>Dichiarare risorse Segreti/Valori di Mesh
Le risorse Segreti/Valori di Mesh hanno una dipendenza dalle risorse Segreti di Mesh definite nel passaggio precedente.

Relativamente alla relazione tra i campi "value:" e "name:" della sezione "resources", la seconda parte della stringa "name:" delimitata da un segno di due punti è il numero di versione usato per un segreto e il nome che precede i due punti deve corrispondere al valore del segreto Mesh per cui ha una dipendenza. Ad esempio, per l'elemento ```name: mysecret:1.0``` il numero di versione è 1.0 e il nome ```mysecret``` deve corrispondere al valore ```"value": "mysecret"``` definito in precedenza.

>
Di seguito è riportato un esempio di dichiarazione di risorse Segreti/Valori di Mesh in un file JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx/2019.02.28",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/sfbpHttpsCertificate.pfx"
      ],
      "properties": {
        "value": "[parameters('sfbpHttpsCertificate')]"
      }
    }
  ],
}
```
Di seguito è riportato un esempio di dichiarazione di risorse Segreti/Valori di Mesh in un file YAML:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Modificare l'applicazione Mesh in modo che faccia riferimento ai valori dei segreti Mesh
Le applicazioni Service Fabric Mesh devono riconoscere le due stringhe seguenti per poter utilizzare i valori dei segreti del servizio di archiviazione sicura:
1. Microsoft.ServiceFabricMesh/Secrets.name contiene il nome del file e conterrà il valore dei segreti in testo non crittografato.
2. La variabile di ambiente Windows o Linux "Fabric_SettingPath" contiene il percorso della directory in cui saranno accessibili i file che contengono i valori dei segreti del servizio di archiviazione sicura. Questo percorso è "C:\Impostazioni" per le applicazioni Mesh ospitate in Windows e "/var/settings" per le applicazioni Mesh ospitate in Linux.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Distribuire o usare un aggiornamento in sequenza in modo che l'applicazione Mesh possa utilizzare i valori dei segreti
La creazione di Segreti e/o Segreti/Valori con versione è limitata alle distribuzioni dichiarate da un modello di risorse. L'unico modo per creare queste risorse è passando un file JSON o YAML di modello di risorsa tramite il comando **az mesh deployment**, come segue:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Comandi dell'interfaccia della riga di comando di Azure per la gestione del ciclo di vita del servizio di archiviazione sicura

### <a name="create-a-new-secrets-resource"></a>Creare una nuova risorsa Segreti
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Passare **template-file** o **template-uri** (ma non entrambi).

Ad esempio:
- az mesh deployment create --c:\MyMeshTemplates\SecretTemplate1.txt
- AZ mesh Deployment create--https: \/ /www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Visualizzare un segreto
Restituisce la descrizione del segreto (ma non il valore).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Eliminare un segreto

- Un segreto non può essere eliminato finché un'applicazione Mesh gli fa riferimento.
- Eliminando una risorsa Segreti vengono eliminate tutte le versioni della risorsa Segreti/Valori.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Elencare i segreti in una sottoscrizione
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Elencare i segreti in un gruppo di risorse
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Elencare tutte le versioni di un segreto
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Visualizzare il valore della versione del segreto
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Eliminare il valore della versione del segreto
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Passaggi successivi 
Per altre informazioni su mesh Service Fabric leggere la panoramica:
- [Panoramica di mesh Service Fabric](service-fabric-mesh-overview.md)
