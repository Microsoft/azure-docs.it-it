---
title: Esercitazione - Creare un cluster di Azure Red Hat OpenShift 4
description: Informazioni su come creare un cluster Microsoft Azure Red Hat OpenShift usando l'interfaccia della riga di comando di Azure
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 10/26/2020
ms.openlocfilehash: 55c1b6f6c6690f0c8f00a8a2469834781f35fb3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449799"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-4-cluster"></a>Esercitazione: Creare un cluster di Azure Red Hat OpenShift 4

In questa esercitazione, la prima di tre su questo argomento, viene preparato l'ambiente per la creazione di un cluster di Azure Red Hat OpenShift che esegue OpenShift 4 e viene creato un cluster. Si apprenderà come:
> [!div class="checklist"]
> * Configurare i prerequisiti 
> * Creare la rete virtuale e le subnet necessarie
> * Distribuire un cluster

## <a name="before-you-begin"></a>Prima di iniziare

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.6.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Azure Red Hat OpenShift richiede almeno 40 core per creare ed eseguire un cluster OpenShift. La quota di risorse di Azure predefinita per una nuova sottoscrizione di Azure non soddisfa questo requisito. Per richiedere un aumento del limite di risorse, vedere [Quota standard: aumentare i limiti per serie di macchine virtuali](../azure-portal/supportability/per-vm-quota-requests.md).

Il segreto ARO pull non modifica il costo della Licenza RH OpenShift per ARO.

### <a name="verify-your-permissions"></a>Verificare le autorizzazioni

Durante questa esercitazione si creerà un gruppo di risorse che conterrà la rete virtuale per il cluster. È necessario avere le autorizzazioni di Collaboratore e Amministratore Accesso utenti oppure le autorizzazioni di Proprietario, direttamente sulla rete virtuale oppure sul gruppo di risorse o sulla sottoscrizione che lo contiene.

Serviranno anche autorizzazioni di Azure Active Directory sufficienti per consentire agli strumenti di creare un'applicazione e un'entità servizio per conto del cluster.

### <a name="register-the-resource-providers"></a>Registrare i provider di risorse

1. Se sono presenti più sottoscrizioni di Azure, specificare l'ID sottoscrizione pertinente:

    ```azurecli-interactive
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registrare il provider di risorse `Microsoft.RedHatOpenShift`:

    ```azurecli-interactive
    az provider register -n Microsoft.RedHatOpenShift --wait
    ```
    
1. Registrare il provider di risorse `Microsoft.Compute`:

    ```azurecli-interactive
    az provider register -n Microsoft.Compute --wait
    ```
    
1. Registrare il provider di risorse `Microsoft.Storage`:

    ```azurecli-interactive
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registrare il provider di risorse `Microsoft.Authorization`:

    ```azurecli-interactive
    az provider register -n Microsoft.Authorization --wait
    ```

### <a name="get-a-red-hat-pull-secret-optional"></a>Ottenere un segreto pull di Red Hat (facoltativo)

Un segreto pull di Red Hat consente al cluster di accedere ai registri contenitori di Red Hat unitamente a contenuto aggiuntivo. Questo passaggio è facoltativo ma consigliato.

1. [Passare al portale di gestione cluster di Red Hat OpenShift](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) ed eseguire l'accesso.

   È necessario accedere all'account Red Hat o creare un nuovo account Red Hat con l'indirizzo di posta elettronica aziendale e accettare i termini e le condizioni.

1. Fare clic su **Download pull secret** (Scarica segreto pull) e scaricare un segreto pull da usare con il cluster Azure Red Hat OpenShift (ARO).

    Conservare il file `pull-secret.txt` salvato in una posizione sicura. Il file verrà usato per ogni creazione di cluster se è necessario creare un cluster che includa gli esempi o gli operatori per Red Hat o i partner certificati.

    Quando si esegue il comando `az aro create`, è possibile fare riferimento al segreto di pull usando il parametro `--pull-secret @pull-secret.txt`. Eseguire `az aro create` dalla directory in cui è stato archiviato il file `pull-secret.txt`. In caso contrario, sostituire `@pull-secret.txt` con `@/path/to/my/pull-secret.txt`.

    Se si copia il segreto pull o vi si fa riferimento in altri script, il segreto pull deve essere formattato come stringa JSON valida.

### <a name="prepare-a-custom-domain-for-your-cluster-optional"></a>Preparare un dominio personalizzato per il cluster (facoltativo)

Quando si esegue il comando `az aro create`, è possibile specificare un dominio personalizzato per il cluster con il parametro `--domain foo.example.com`.

Se si specifica un dominio personalizzato per il cluster, tenere presente quanto segue:

* Dopo aver creato il cluster, è necessario creare 2 record A DNS nel server DNS per il parametro `--domain` specificato:
    * **api**, che punta all'indirizzo IP del server API
    * **\*.apps**, che punta all'indirizzo IP del traffico in ingresso
    * Recuperare questi valori eseguendo il comando seguente dopo la creazione del cluster: `az aro show -n -g --query '{api:apiserverProfile.ip, ingress:ingressProfiles[0].ip}'`.

* La console OpenShift sarà disponibile a un indirizzo URL come `https://console-openshift-console.apps.example.com`, invece di quello del dominio predefinito `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.

* Per impostazione predefinita, OpenShift usa certificati autofirmati per tutte le route create in domini personalizzati `*.apps.example.com`.  Se si sceglie di usare un DNS personalizzato dopo la connessione al cluster, è necessario attenersi alla documentazione di OpenShift per [configurare un'autorità di certificazione personalizzata per il controller del traffico in ingresso](https://docs.openshift.com/container-platform/4.6/security/certificates/replacing-default-ingress-certificate.html) e un'[autorità di certificazione personalizzata per il server API](https://docs.openshift.com/container-platform/4.6/security/certificates/api-server.html).

### <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Creare una rete virtuale contenente due subnet vuote

Successivamente, verrà creata una rete virtuale contenente due subnet vuote. Se si dispone di una rete virtuale esistente che soddisfi le proprie esigenze, è possibile ignorare questo passaggio.

1. **Impostare le variabili seguenti nell'ambiente della shell in cui si eseguiranno i comandi `az`.**

   ```console
   LOCATION=eastus                 # the location of your cluster
   RESOURCEGROUP=aro-rg            # the name of the resource group where you want to create your cluster
   CLUSTER=cluster                 # the name of your cluster
   ```

2. **Creare un gruppo di risorse.**

   Un gruppo di risorse di Azure è un gruppo logico in cui le risorse di Azure vengono distribuite e gestite. Quando si crea un gruppo di risorse, viene chiesto di specificare una posizione. È la località in cui vengono archiviati i metadati del gruppo di risorse e anche quella in cui vengono eseguite le risorse in Azure se non si specifica un'altra area durante la creazione della risorsa. Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create).
    
   > [!NOTE] 
   > Azure Red Hat OpenShift non è disponibile in tutte le aree in cui è possibile creare un gruppo di risorse di Azure. Per informazioni su dove è supportato Azure Red Hat OpenShift, vedere [Available regions](https://azure.microsoft.com/en-gb/global-infrastructure/services/?products=openshift) (Aree disponibili).

   ```azurecli-interactive
   az group create \
     --name $RESOURCEGROUP \
     --location $LOCATION
   ```

   L'output di esempio seguente mostra il gruppo di risorse creato correttamente:

   ```json
   {
     "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
     "location": "eastus",
     "name": "aro-rg",
     "properties": {
       "provisioningState": "Succeeded"
     },
     "type": "Microsoft.Resources/resourceGroups"
   }
   ```

2. **Creare una rete virtuale.**

   I cluster di Azure Red Hat OpenShift che eseguono OpenShift 4 richiedono una rete virtuale con due subnet vuote, per i nodi master e di lavoro. È possibile creare una nuova rete virtuale per questo o usare una rete virtuale esistente.

   Creare una nuova rete virtuale nello stesso gruppo di risorse creato in precedenza:

   ```azurecli-interactive
   az network vnet create \
      --resource-group $RESOURCEGROUP \
      --name aro-vnet \
      --address-prefixes 10.0.0.0/22
   ```

   L'output di esempio seguente mostra la rete virtuale creata correttamente:

   ```json
   {
     "newVNet": {
       "addressSpace": {
         "addressPrefixes": [
           "10.0.0.0/22"
         ]
       },
       "dhcpOptions": {
         "dnsServers": []
       },
       "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
       "location": "eastus",
       "name": "aro-vnet",
       "provisioningState": "Succeeded",
       "resourceGroup": "aro-rg",
       "type": "Microsoft.Network/virtualNetworks"
     }
   }
   ```

3. **Aggiungere una subnet vuota per i nodi master.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name master-subnet \
     --address-prefixes 10.0.0.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

4. **Aggiungere una subnet vuota per i nodi di lavoro.**

   ```azurecli-interactive
   az network vnet subnet create \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --name worker-subnet \
     --address-prefixes 10.0.2.0/23 \
     --service-endpoints Microsoft.ContainerRegistry
   ```

5. **[Disabilitare i criteri per gli endpoint privati della subnet](../private-link/disable-private-link-service-network-policy.md) nella subnet master**. Questa operazione è necessaria per consentire al servizio di connettersi e gestire il cluster.

   ```azurecli-interactive
   az network vnet subnet update \
     --name master-subnet \
     --resource-group $RESOURCEGROUP \
     --vnet-name aro-vnet \
     --disable-private-link-service-network-policies true
   ```

## <a name="create-the-cluster"></a>Creare il cluster

Eseguire il comando seguente per creare un cluster. Se si sceglie di usare una delle opzioni seguenti, modificare il comando di conseguenza:
* Facoltativamente, è possibile passare il [segreto pull di Red Hat](#get-a-red-hat-pull-secret-optional) che consente al cluster di accedere ai registri contenitori di Red Hat unitamente a contenuto aggiuntivo. Aggiungere l'argomento `--pull-secret @pull-secret.txt` al comando.
* Se si vuole, è possibile [usare un dominio personalizzato](#prepare-a-custom-domain-for-your-cluster-optional). Aggiungere l'argomento `--domain foo.example.com` al comando, sostituendo `foo.example.com` con il dominio personalizzato.

> [!NOTE]
> Se si aggiungono argomenti facoltativi al comando, chiudere l'argomento nella riga precedente del comando con una barra rovesciata finale.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
```

Dopo l'esecuzione di `az aro create`, la creazione di un cluster richiede in genere circa 35 minuti.

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:
> [!div class="checklist"]
> * Configurare i prerequisiti e creare la rete virtuale e le subnet necessarie
> * Distribuire un cluster

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Connettersi a un cluster di Azure Red Hat OpenShift](tutorial-connect-cluster.md)
