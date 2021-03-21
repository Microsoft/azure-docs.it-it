---
title: Creare e caricare un'immagine di OpenBSD
description: Informazioni su come creare e caricare un disco rigido virtuale (VHD) che contiene il sistema operativo OpenBSD per creare una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure
author: gbowerman
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 3437fcd2d1d521aad237ecf6d3f7937b4835d376
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554599"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Creare e caricare un'immagine disco OpenBSD in Azure
Questo articolo descrive come creare e caricare un disco rigido virtuale (VHD) che contiene il sistema operativo OpenBSD. Dopo il caricamento, è possibile usarlo come immagine personalizzata per creare una macchina virtuale in Azure tramite l'interfaccia della riga di comando di Azure.


## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

* **Una sottoscrizione Azure**: se non è già disponibile un account, è possibile crearne uno in pochi minuti. Se si dispone di un abbonamento MSDN, vedere [credito Azure mensile per Sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Altrimenti, vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).  
* **Interfaccia della riga di comando di Azure**: assicurarsi di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e di aver eseguito l'accesso a un account Azure tramite il comando [az login](/cli/azure/reference-index).
* **Sistema operativo OpenBSD installato in un file VHD** : è necessario installare un sistema operativo OpenBSD supportato ([6,6 versione amd64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) in un disco rigido virtuale. Sono disponibili vari strumenti per la creazione di file VHD. Ad esempio, per creare il file VHD e installare il sistema operativo, è possibile usare soluzioni di virtualizzazione come Hyper-V. Per istruzioni, vedere su come installare e usare Hyper-V, vedere [Installare Hyper-V e creare una macchina virtuale](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).


## <a name="prepare-openbsd-image-for-azure"></a>Preparare l'immagine OpenBSD per Azure
Nella macchina virtuale in cui è stato installato il sistema operativo OpenBSD 6.1 con l'aggiunta del supporto Hyper-V, completare le procedure seguenti:

1. Se DHCP non è stato abilitato durante l'installazione, abilitare il servizio come segue:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Configurare una console seriale come segue:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Configurare l'installazione del pacchetto come segue:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Per impostazione predefinita, l'utente `root` è disabilitato nelle macchine virtuali in Azure. Gli utenti possono eseguire comandi con privilegi elevati usando il comando `doas` nella VM OpenBSD. Doas è abilitato per impostazione predefinita. Per altre informazioni, vedere [doas.conf](https://man.openbsd.org/doas.conf.5). 

5. Installare e configurare i prerequisiti per l'agente di Azure come segue:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. La versione più recente dell'agente di Azure è sempre disponibile in [GitHub](https://github.com/Azure/WALinuxAgent/releases). Installare l'agente come segue:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Dopo aver installato l'agente di Azure, è consigliabile verificare che sia in esecuzione come segue:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Eseguire il deprovisioning del sistema per pulire il sistema e renderlo idoneo per un nuovo provisioning. Il comando seguente elimina anche l'ultimo account utente di cui è stato effettuato il provisioning e i dati associati:

    ```sh
    waagent -deprovision+user -force
    ```

Ora è possibile arrestare la macchina virtuale.


## <a name="prepare-the-vhd"></a>Preparare il disco rigido virtuale
Il formato VHDX non è supportato in Azure, solo nei **VHD fissi**. È possibile convertire il disco in formato VHD fisso tramite la console di gestione di Hyper-V o il cmdlet [convert-vhd](/powershell/module/hyper-v/convert-vhd) di PowerShell. Di seguito è riportato un esempio.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Creare e caricare risorse di archiviazione
Creare prima un gruppo di risorse con [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Per caricare il disco rigido virtuale, creare un account di archiviazione con il comando [az storage account create](/cli/azure/storage/account). Il nome dell'account di archiviazione deve essere univoco; assegnare quindi all'account il proprio nome. L'esempio seguente crea un account di archiviazione denominato *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Per controllare l'accesso all'account di archiviazione, ottenere la chiave di archiviazione con [az storage account keys list](/cli/azure/storage/account/keys) come segue:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Per separare in modo logico i dischi rigidi virtuali caricati, creare un contenitore nell'account di archiviazione con [az storage container create](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Caricare infine il disco rigido virtuale con [az storage blob upload](/cli/azure/storage/blob) come segue:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Creare una macchina virtuale dal disco rigido virtuale
È possibile creare una macchina virtuale con un [script di esempio](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-vhd) o direttamente con [az vm create](/cli/azure/vm). Per specificare il disco rigido virtuale OpenBSD caricato, usare il parametro `--image` come segue:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Ottenere l'indirizzo IP per la VM OpenBSD con [az vm list-ip-addresses](/cli/azure/vm) come segue:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Ora è possibile connettersi alla VM OpenBSD tramite SSH nel modo usuale:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul supporto di Hyper-V in OpenBSD 6.1, vedere [OpenBSD 6.1](https://www.openbsd.org/61.html) e [hyperv.4](https://man.openbsd.org/hyperv.4).

Per creare una macchina virtuale dal disco gestito, vedere [az disk](/cli/azure/disk).