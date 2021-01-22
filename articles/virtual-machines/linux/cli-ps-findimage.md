---
title: Selezionare immagini di VM Linux con l'interfaccia della riga di comando di Azure
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per determinare il server di pubblicazione, l'offerta, la SKU e la versione delle immagini di macchine virtuali del Marketplace.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 8954ad03bd5f539e9dcfbb4249f4e7cc1cf0bc7f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685124"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Trovare immagini di macchine virtuali Linux in Azure Marketplace con l'interfaccia della riga di comando di Azure

Questo argomento descrive come usare l'interfaccia della riga di comando di Azure per trovare immagini di macchine virtuali in Azure Marketplace. Usare queste informazioni per specificare un'immagine del Marketplace quando si crea una macchina virtuale a livello di codice mediante l'interfaccia della riga di comando, modelli di Resource Manager o altri strumenti.

Sfogliare poi le immagini e le offerte disponibili usando la vetrina di [Azure Marketplace](https://azuremarketplace.microsoft.com/), il [portale di Azure](https://portal.azure.com) o [Azure PowerShell](../windows/cli-ps-findimage.md). 

Assicurarsi di aver effettuato l'accesso a un account Azure ( `az login` ).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="deploy-from-a-vhd-using-purchase-plan-parameters"></a>Eseguire la distribuzione da un VHD usando i parametri del piano di acquisto

Se si dispone di un disco rigido virtuale esistente creato con un'immagine di Azure Marketplace a pagamento, potrebbe essere necessario fornire le informazioni sul piano di acquisto quando si crea una nuova macchina virtuale da tale disco rigido virtuale. 

Se è ancora presente la macchina virtuale originale o un'altra macchina virtuale creata usando la stessa immagine del Marketplace, è possibile ottenere il nome del piano, l'editore e le informazioni sul prodotto usando il comando [AZ VM Get-instance-View](/cli/azure/vm#az_vm_get_instance_view). Questo esempio Mostra come ottenere una macchina virtuale denominata *myVM* nel gruppo di risorse *myResourceGroup* e quindi visualizzare le informazioni sul piano di acquisto.

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

Se non sono state rilevate informazioni sul piano prima dell'eliminazione della macchina virtuale originale, è possibile archiviare una [richiesta di supporto](https://ms.portal.azure.com/#create/Microsoft.Support). Saranno necessari il nome della macchina virtuale, l'ID sottoscrizione e il timestamp dell'operazione di eliminazione.

Dopo aver ottenuto le informazioni sul piano, è possibile creare la nuova macchina virtuale usando il `--attach-os-disk` parametro per specificare il disco rigido virtuale.

```azurecli-interactive
az vm create \
   --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```

## <a name="deploy-a-new-vm-using-purchase-plan-parameters"></a>Distribuire una nuova VM usando i parametri del piano di acquisto

Se si dispone già di informazioni sull'immagine, è possibile distribuirla utilizzando il `az vm create` comando. In questo esempio viene distribuita una VM con l'immagine RabbitMQ Certified by BitNami:

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

Se viene visualizzato un messaggio relativo all'accettazione delle condizioni dell'immagine, vedere la sezione [accettare i termini](#accept-the-terms) più avanti in questo articolo.

## <a name="list-popular-images"></a>Elencare immagini popolari

Eseguire il comando [az vm image list](/cli/azure/vm/image), senza l'opzione `--all`, per visualizzare un elenco di immagini di VM popolari in Azure Marketplace. Ad esempio, eseguire il comando seguente per visualizzare un elenco memorizzato nella cache di immagini popolari in formato tabella:

```azurecli
az vm image list --output table
```

L'output include l'URN dell'immagine (il valore nella colonna *Urn*). Se si vuole creare una VM con tali immagini popolari in Marketplace, è possibile specificare in alternativa l'*alias URN*, un formato abbreviato come ad esempio *UbuntuLTS*.

```output
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>Trovare immagini specifiche

Per trovare un'immagine di VM specifica in Marketplace, usare il comando `az vm image list` con l'opzione `--all`. Questa versione del comando richiede del tempo per essere completata e può restituire un output lungo, pertanto l'elenco si filtra in genere in base a `--publisher` o a un altro parametro. 

Ad esempio, il comando che segue visualizza tutte le offerte Debian. Tenere presente che senza l'opzione `--all`, la ricerca viene eseguita solo nella cache locale delle immagini comuni:

```azurecli
az vm image list --offer Debian --all --output table 

```

Output parziale: 

```output
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

Applicare filtri simili con le opzioni `--location`, `--publisher` e `--sku`. È possibile cercare corrispondenze parziali in base a un filtro, ad esempio cercare `--offer Deb` per trovare tutte le immagini Debian.

Se non si indica una posizione specifica con l'opzione `--location`, vengono restituiti i valori relativi alla posizione predefinita. Per impostare un percorso predefinito diverso eseguire `az configure --defaults location=<location>`.

Ad esempio, il comando seguente elenca tutti gli SKU di Debian 8 nella posizione Europa occidentale:

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

Output parziale:

```output
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>Esplorare le immagini
 
Un altro modo per trovare un'immagine in una posizione è l'esecuzione in sequenza dei comandi [az vm image list-publishers](/cli/azure/vm/image), [az vm image list-offers](/cli/azure/vm/image) e [az vm image list-skus](/cli/azure/vm/image) . Con questi comandi si determinano questi valori:

1. Elencando gli editori di immagini.
2. Elencando le offerte di un determinato editore.
3. Elencando le SKU di una determinata offerta.

Quindi, è possibile scegliere una versione da distribuire per uno SKU selezionato.

Ad esempio, il comando seguente elenca i server di pubblicazione di immagini nella posizione Stati Uniti occidentali (westus):

```azurecli
az vm image list-publishers --location westus --output table
```

Output parziale:

```output
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

Usare queste informazioni per individuare le offerte di un server di pubblicazione specifico. Ad esempio, per quanto riguarda l'editore *Canonical* nella posizione Stati Uniti occidentali, è possibile trovare le relative offerte eseguendo `azure vm image list-offers`. Passare la posizione e il server di pubblicazione come nell'esempio seguente:

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

Output:

```output
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
Come si può vedere, nell'area degli Stati Uniti occidentali Canonical pubblica l'offerta *UbuntuServer* in Azure. Ma quali sono le SKU? Per ottenere questi valori, eseguire `azure vm image list-skus` e impostare la posizione l'editore e l'offerta individuati:

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

Output:

```output
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

Usare infine il comando `az vm image list` per trovare una versione specifica della SKU voluta, ad esempio, *18.04-LTS*:

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

Output parziale:

```output
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

A questo punto è possibile scegliere con precisione l'immagine da usare prendendo nota del valore URN. Passare questo valore con il parametro `--image` quando si crea una macchina virtuale con il [az vm create](/cli/azure/vm). Facoltativamente, è possibile sostituire il numero di versione nell'URN con "latest", che rappresenta sempre la versione più recente dell'immagine. 

Se si distribuisce una macchina virtuale con un modello di Resource Manager, impostare singolarmente i parametri dell'immagine nelle proprietà `imageReference`. Vedere le [informazioni di riferimento sul modello](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Visualizzare le proprietà del piano

Per visualizzare informazioni sul piano di acquisto di un'immagine, eseguire il comando [az vm image show](/cli/azure/image). Se la proprietà `plan` nell'output non è `null`, l'immagine presenta condizioni che è necessario accettare prima della distribuzione a livello di codice.

L'immagine Canonical Ubuntu Server 18.04 LTS, ad esempio, non ha condizioni aggiuntive, perché l'informazione `plan` è `null`:

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

Output:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

Eseguendo un comando analogo per l'immagine RabbitMQ certificata da Bitnami vengono mostrate le proprietà `plan` seguenti: `name`, `product` e `publisher`. (Alcune immagini hanno anche una `promotion code` proprietà). Per distribuire questa immagine, vedere le sezioni seguenti per accettare le condizioni e abilitare la distribuzione a livello di codice.

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
Output:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

## <a name="accept-the-terms"></a>Accettare le condizioni

Per visualizzare e accettare le condizioni di licenza, usare il comando [az vm image accept-terms](/cli/azure/vm/image?). Quando si accettano le condizioni, si abilita la distribuzione a livello di codice nella sottoscrizione. È sufficiente accettare le condizioni per l'immagine una sola volta per ogni sottoscrizione. Ad esempio:

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

L'output include un `licenseTextLink` alle condizioni di licenza e indica che il valore di `accepted` è `true`:

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

## <a name="next-steps"></a>Passaggi successivi
Per creare rapidamente una macchina virtuale usando le informazioni relative all'immagine, vedere [Creare e gestire VM Linux con l'interfaccia della riga di comando di Azure](tutorial-manage-vm.md).
