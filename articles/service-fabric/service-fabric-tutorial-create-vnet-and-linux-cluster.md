---
title: Creare un cluster Linux Service Fabric in Azure
description: Informazioni su come distribuire un cluster Linux di Service Fabric in una rete virtuale di Azure esistente tramite l'interfaccia della riga di comando di Azure.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 77cc49c1b79e5c24e78a67a69493aa0b0059d565
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791072"
---
# <a name="deploy-a-linux-service-fabric-cluster-into-an-azure-virtual-network"></a>Distribuire un cluster Linux di Service Fabric in una rete virtuale di Azure

In questo articolo si apprenderà come distribuire un cluster Linux di Service Fabric in una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) usando l'interfaccia della riga di comando di Azure e un modello. Al termine, si ottiene un cluster in esecuzione nel cloud nel quale è possibile distribuire applicazioni. Per creare un cluster Windows tramite PowerShell, vedere [Creare un cluster sicuro di Windows in Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installare l' [interfaccia](service-fabric-cli.md) della riga di comando di Service Fabric
* Installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
* Per informazioni sui concetti chiave dei cluster, vedere [Panoramica dei cluster di Azure](service-fabric-azure-clusters-overview.md)
* [Pianificare e preparare](service-fabric-cluster-azure-deployment-preparation.md) la distribuzione di un cluster di produzione.

Le procedure seguenti creano un cluster di Service Fabric a sette nodi. Per calcolare i costi sostenuti dall'esecuzione di un cluster di Service Fabric in Azure usare il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Scaricare ed esplorare il modello

Scaricare i file del modello di Resource Manager seguenti:

Per Ubuntu 16,04 LTS:

* [AzureDeploy.js][template]
* [AzureDeploy.Parameters.js][parameters]

Per Ubuntu 18,04 LTS:

* [AzureDeploy.js][template2]
* [AzureDeploy.Parameters.js][parameters2]

Per Ubuntu 18,04 LTS la differenza tra i due modelli è 
* attributo **vmImageSku** impostato su "18,04-LTS"
* il **typeHandlerVersion** di ogni nodo viene impostato su 1,1
* Risorse di Microsoft. ServiceFabric/Clusters
   - **apiVersion** impostato su "2019-03-01" o versione successiva
   - proprietà **vmImage** impostata su "Ubuntu18_04"

Questo modello distribuisce un cluster sicuro di sette macchine virtuali e tre tipi di nodo in una rete virtuale.  Altri modelli di esempio sono disponibili su [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Il [AzureDeploy.jssu][template] distribuisce un numero di risorse, incluse le seguenti.

### <a name="service-fabric-cluster"></a>Cluster di Service Fabric

Nella risorsa **Microsoft.ServiceFabric/clusters** viene distribuito un cluster Linux con le caratteristiche seguenti:

* tre tipi di nodi
* Cinque nodi del tipo di nodo primario (configurabile nei parametri del modello), un nodo di ognuno degli altri tipi di nodi
* Sistema operativo: (Ubuntu 16,04 LTS/Ubuntu 18,04 LTS) (configurabile nei parametri del modello)
* Protezione con certificato (configurabile nei parametri del modello)
* Il [servizio DNS](service-fabric-dnsservice.md) è abilitato
* [Livello di durabilità](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) Bronze (configurabile nei parametri del modello)
* [Livello di affidabilità](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) dell'argento (configurabile nei parametri del modello)
* Endpoint di connessione client: 19000 (configurabile nei parametri del modello)
* Endpoint del gateway HTTP: 19080 (configurabile nei parametri del modello)

### <a name="azure-load-balancer"></a>Servizio di bilanciamento del carico di Azure

Nella risorsa **Microsoft.Network/loadBalancers** viene configurato un servizio di bilanciamento del carico e vengono impostate probe e regole per le porte seguenti:

* Endpoint di connessione client: 19000
* Endpoint del gateway HTTP: 19080
* Porta applicazione: 80
* Porta applicazione: 443

### <a name="virtual-network-and-subnet"></a>Rete virtuale e subnet

I nomi della rete virtuale e della subnet sono dichiarati nei parametri del modello.  così come gli spazi indirizzi della rete virtuale e della subnet. Questi ultimi vengono configurati nella risorsa **Microsoft.Network/virtualNetworks**:

* Spazio indirizzi della rete virtuale: 10.0.0.0/16
* Spazio indirizzi della subnet di Service Fabric: 10.0.2.0/24

Se sono necessarie altre porte dell'applicazione, si dovrà modificare la risorsa Microsoft.Network/loadBalancers in modo da consentire il traffico in ingresso.

## <a name="set-template-parameters"></a>Impostare i parametri del modello

Il file **file azuredeploy. Parameters** dichiara molti valori usati per distribuire il cluster e le risorse associate. Di seguito sono riportati alcuni dei parametri che potrebbe essere necessario modificare per la propria distribuzione:

|Parametro|Valore di esempio|Note|
|---|---||
|adminUserName|vmadmin| Nome utente amministratore per le VM del cluster. |
|adminPassword|Password#1234| Password amministratore per le VM del cluster.|
|clusterName|mysfcluster123| Nome del cluster. |
|posizione|southcentralus| Località del cluster. |
|certificateThumbprint|| <p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato.</p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere il valore di identificazione personale SHA1 del certificato, ad esempio "6190390162C988701DB5676EB81083EA608DCCF3". </p>|
|certificateUrlValue|| <p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato.</p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere l'URL del certificato, ad esempio "https:\//mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346".</p>|
|sourceVaultValue||<p>Il valore deve essere vuoto se si crea un certificato autofirmato o si specifica un file di certificato.</p><p>Per usare un certificato esistente precedentemente caricato in un insieme di credenziali delle chiavi, immettere il valore dell'insieme di credenziali di origine, ad esempio "/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT".</p>|

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Distribuire la rete virtuale e il cluster

Configurare quindi la topologia di rete e distribuire il cluster di Service Fabric. Il modello di Resource Manager **AzureDeploy.json** crea una rete virtuale e una subnet per Service Fabric. Il modello distribuisce anche un cluster in cui è abilitata la sicurezza basata su certificati.  Come certificato per i cluster di produzione usare un certificato di un'entità di certificazione (CA). Per proteggere i cluster di test è possibile usare un certificato autofirmato.

Il modello in questo articolo distribuisce un cluster che usa l'identificazione personale del certificato per identificare il certificato del cluster.  Nessun certificato può avere la stessa identificazione personale di un altro, il che rende più difficile gestire i certificati. Commutare un cluster distribuito dall'uso di identificazioni personali del certificato all'uso di nomi comuni del certificato rende molto più semplice la gestione dei certificati.  Per informazioni su come aggiornare il cluster per usare i nomi comuni dei certificati per la gestione dei certificati, vedere [Change cluster to certificate Common Name Management](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Creare un cluster usando un certificato esistente

Lo script seguente usa il comando [az sf cluster create](/cli/azure/sf/cluster) e il modello per distribuire un nuovo cluster protetto con un certificato esistente. Il comando crea anche un nuovo insieme di credenziali delle chiavi in Azure e carica il certificato.

```azurecli
ResourceGroupName="sflinuxclustergroup"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates\MyCertificate.pem"

# sign in to your Azure account and select your subscription
az login
az account set --subscription <guid>

# Create a new resource group for your deployment and give it a name and a location.
az group create --name $ResourceGroupName --location $Location

# Create the Service Fabric cluster.
az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --certificate-password $Password --certificate-file $CertPath \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName  \
   --template-file AzureDeploy.json --parameter-file AzureDeploy.Parameters.json
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Creare un cluster usando un nuovo certificato autofirmato

Lo script seguente usa il comando [az sf cluster create](/cli/azure/sf/cluster) e un modello per distribuire un nuovo cluster in Azure. Il comando crea anche un insieme di credenziali delle chiavi in Azure, aggiunge un nuovo certificato autofirmato all'insieme di credenziali delle chiavi e scarica il file del certificato in locale.

```azurecli
ResourceGroupName="sflinuxclustergroup"
ClusterName="sflinuxcluster"
Location="southcentralus"
Password="q6D7nN%6ck@6"
VaultName="linuxclusterkeyvault"
VaultGroupName="linuxclusterkeyvaultgroup"
CertPath="C:\MyCertificates"

az sf cluster create --resource-group $ResourceGroupName --location $Location \
   --cluster-name $ClusterName --template-file C:\temp\cluster\AzureDeploy.json \
   --parameter-file C:\temp\cluster\AzureDeploy.Parameters.json --certificate-password $Password \
   --certificate-output-folder $CertPath --certificate-subject-name $ClusterName.$Location.cloudapp.azure.com \
   --vault-name $VaultName --vault-resource-group $ResourceGroupName
```

## <a name="connect-to-the-secure-cluster"></a>Connettersi al cluster sicuro

Connettersi al cluster tramite l'interfaccia della riga di comando di Service Fabric `sfctl cluster select` con la chiave.  Usare solo l'opzione **--no-verify** per un certificato autofirmato.

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Verificare di essere connessi e che il cluster sia integro usando il comando `sfctl cluster health`.

```console
sfctl cluster health
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende passare subito all'articolo successivo, è opportuno [eliminare il cluster](./service-fabric-tutorial-delete-cluster.md) per evitare di sostenere costi.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [ridimensionare un cluster](service-fabric-tutorial-scale-cluster.md).

Il modello in questo articolo distribuisce un cluster che usa l'identificazione personale del certificato per identificare il certificato del cluster.  Nessun certificato può avere la stessa identificazione personale di un altro, il che rende più difficile gestire i certificati. Commutare un cluster distribuito dall'uso di identificazioni personali del certificato all'uso di nomi comuni del certificato rende molto più semplice la gestione dei certificati.  Per informazioni su come aggiornare il cluster per usare i nomi comuni dei certificati per la gestione dei certificati, vedere [Change cluster to certificate Common Name Management](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-3-NodeTypes-Secure/AzureDeploy.Parameters.json
[template2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.json
[parameters2]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Ubuntu-1804-3-NodeTypes-Secure/AzureDeploy.Parameters.json
