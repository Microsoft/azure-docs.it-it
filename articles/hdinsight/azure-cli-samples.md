---
title: "Azure HDInsight: Esempi dell'interfaccia della riga di comando di Azure"
description: Esempi dell'interfaccia della riga di comando di Azure per attività comuni in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: sample
ms.date: 09/23/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: a289a0d963c8e20a3b861ff9964e8afd6665de22
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92542353"
---
# <a name="azure-hdinsight-azure-cli-samples"></a>Azure HDInsight: Esempi dell'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> [Esempi di .NET](hdinsight-sdk-dotnet-samples.md)
> [Esempi di Python](hdinsight-sdk-python-samples.md)
> [Esempi di Java](hdinsight-sdk-java-samples.md)

Questo articolo include script di esempio per le attività comuni. Per ogni esempio aggiornare le variabili con i valori appropriati e quindi eseguire il comando.

## <a name="prerequisites"></a>Prerequisiti

* Interfaccia della riga di comando di Azure. Per la procedura, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

* Facoltativo: Bash. Negli esempi di questo articolo si usa la shell Bash in Windows 10. Per la procedura di installazione, vedere [Guida all'installazione del sottosistema Windows per Linux per Windows 10](/windows/wsl/install-win10).  Con alcune piccole modifiche gli esempi funzioneranno anche da un prompt dei comandi di Windows.

## <a name="az-login"></a>az login

[Consente di accedere ad Azure](/cli/azure/reference-index#az-login).

```azurecli
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="az-hdinsight-create"></a>az hdinsight create

[Crea un nuovo cluster](/cli/azure/hdinsight#az-hdinsight-create).

### <a name="create-a-cluster-with-an-existing-storage-account"></a>Creare un cluster con un account di archiviazione esistente

```azurecli
# set variables
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=hadoop
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT
```

### <a name="create-a-cluster-with-the-enterprise-security-package-esp"></a>Creare un cluster con Enterprise Security Package (ESP)

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export subnet="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRG/providers/Microsoft.Network/virtualNetworks/MyVnet/subnets/subnet1"
export domain="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyRG/providers/Microsoft.AAD/domainServices/MyDomain.onmicrosoft.com"
export userAssignedIdentity="/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/MyMsiRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/MyMSI"
export domainAccount=MyAdminAccount@MyDomain.onmicrosoft.com
export groupDNS=MyGroup

az hdinsight create \
    --esp \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --subnet $subnet \
    --domain $domain \
    --assign-identity $userAssignedIdentity \
    --cluster-admin-account $domainAccount \
    --cluster-users-group-dns $groupDNS
```

### <a name="create-a-kafka-cluster-with-disk-encryption"></a>Creare un cluster Kafka con [crittografia dischi](./disk-encryption.md)

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=kafka
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export encryptionKeyName=kafkaClusterKey
export encryptionKeyVersion=00000000000000000000000000000000
export encryptionVaultUri=https://MyKeyVault.vault.azure.net
export userAssignedIdentity="/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/MyMsiRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/MyMSI"

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --workernode-data-disks-per-node 2 \
    --encryption-key-name $encryptionKeyName \
    --encryption-key-version $encryptionKeyVersion \
    --encryption-vault-uri $encryptionVaultUri \
    --assign-identity $userAssignedIdentity
```

### <a name="create-a-cluster-with-azure-data-lake-storage-gen2"></a>Creare un cluster con Azure Data Lake Storage Gen2

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export adlgen2=MyStorageAccount
export sami=MyMSI

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $adlgen2 \
    --storage-account-managed-identity $sami
```

### <a name="create-a-cluster-with-configuration-from-json-string"></a>Creare un cluster con la configurazione ottenuta dalla stringa JSON

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export clusterConfiguration="{'gateway':{'restAuthCredential.username':'admin'}}"

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --cluster-configuration $clusterConfiguration
```

### <a name="create-a-cluster-with-configuration-from-a-local-file"></a>Creare un cluster con la configurazione ottenuta da un file locale

```azurecli
export clusterName=CLUSTERNAME
export resourceGroupName=RESOURCEGROUPNAME
export clusterType=spark
export httpCredential='PASSWORD'
export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
export clusterConfiguration=@/mnt/c/HDI/config.json

az hdinsight create \
    --name $clusterName \
    --resource-group $resourceGroupName \
    --type $clusterType \
    --http-password $httpCredential \
    --storage-account $AZURE_STORAGE_ACCOUNT \
    --cluster-configuration $clusterConfiguration
```

## <a name="az-hdinsight-application-create"></a>az hdinsight application create

[Crea un'applicazione per un cluster HDInsight](/cli/azure/hdinsight/application#az-hdinsight-application-create).

### <a name="create-an-application-with-a-script-uri"></a>Creare un'applicazione con un URI di script

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters"
```

### <a name="create-an-application-with-a-script-uri-and-specified-edge-node-size"></a>Creare un'applicazione con un URI di script e le dimensioni del nodo perimetrale specificate

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'
export edgenodeSize=Standard_D4_v2

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters" \
    --edgenode-size $edgenodeSize
```

### <a name="create-an-application-with-https-endpoint"></a>Creare un'applicazione con un endpoint HTTPS

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export applicationName=MyApplication
export clusterName=CLUSTERNAME
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
export scriptActionName=MyScriptAction
export scriptParameters='"-version latest -port 20000"'
export destinationPort=8888
export subDomainSuffix=was

az hdinsight application create \
    --resource-group $resourceGroupName \
    --name $applicationName \
    --cluster-name $clusterName \
    --script-uri $scriptURI  \
    --script-action-name $scriptActionName \
    --script-parameters "$scriptParameters" \
    --destination-port $destinationPort \
    --sub-domain-suffix $subDomainSuffix
```

## <a name="az-hdinsight-script-action-execute"></a>az hdinsight script-action execute

[Eseguire azioni script sul cluster HDInsight specificato](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-execute).

### <a name="execute-a-script-action-and-persist-on-success"></a>Eseguire un'azione script e renderla permanente in caso di operazione riuscita

```azurecli
export resourceGroupName=RESOURCEGROUPNAME
export clusterName=CLUSTERNAME
export scriptActionName=MyScriptAction
export scriptURI=https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
export roles="headnode workernode"

az hdinsight script-action execute \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --name $scriptActionName \
    --script-uri $scriptURI  \
    --roles $roles \
    --persist-on-success
```