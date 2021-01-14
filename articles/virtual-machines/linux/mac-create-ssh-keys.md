---
title: Creare e usare una coppia di chiavi SSH per le macchine virtuali Linux in Azure
description: Come creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure per migliorare la sicurezza del processo di autenticazione.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: 7a6971bce2ba4cb3e18455aad34e2d10b73dc066
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203423"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Azioni rapide: Creare e usare una coppia di chiavi SSH pubblica e privata per le macchine virtuali Linux in Azure

Con una coppia di chiavi SSH (Secure Shell) è possibile creare macchine virtuali (VM) in Azure che usano chiavi SSH per l'autenticazione. Questo articolo illustra come generare rapidamente e usare una coppia di file di chiavi pubblica e privata per le macchine virtuali Linux. È possibile completare questi passaggi con l'Azure Cloud Shell, un host macOS o Linux. 

> [!NOTE]
> Per impostazione predefinita, le macchine virtuali create con le chiavi SSH vengono configurate con le password disabilitate, aumentando considerevolmente la difficoltà degli attacchi basati su forza bruta per scoprire le password. 

Per altri scenari ed esempi, vedere [Procedura dettagliata per creare coppie di chiavi SSH](create-ssh-keys-detailed.md).

Per altri modi in cui generare e usare le chiavi SSH in un computer Windows, vedere [Come usare le chiavi SSH con Windows in Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Creare una coppia di chiavi SSH

Usare il comando `ssh-keygen` per generare i file di chiave pubblica e privata SSH. Per impostazione predefinita, questi file vengono creati nella directory ~/.ssh. È possibile specificare un percorso diverso e una password facoltativa (*passphrase*) per accedere al file di chiave privata. Se nel percorso specificato è presente una coppia di chiavi SSH con lo stesso nome, questi file vengono sovrascritti.

Il comando seguente crea una coppia di chiavi SSH usando la crittografia RSA e una lunghezza in bit pari a 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Se si usa l'[interfaccia della riga di comando di Azure](/cli/azure) per creare la macchina virtuale con il comando [az vm create](/cli/azure/vm#az-vm-create), facoltativamente è possibile creare i file della chiave SSH pubblica e privata usando con l'opzione `--generate-ssh-keys`. I file delle chiavi vengono archiviati nella directory ~/.ssh se non diversamente specificato con l'opzione `--ssh-dest-key-path`. Se esiste già una coppia di chiavi SSH e  `--generate-ssh-keys` viene usata l'opzione, non verrà generata una nuova coppia di chiavi, ma verrà usata la coppia di chiavi esistente. Nel comando seguente sostituire *VMname* e *RGname* con i propri valori:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Fornire una chiave SSH pubblica quando si distribuisce una macchina virtuale

Per creare una macchina virtuale Linux che usa le chiavi SSH per l'autenticazione, specificare la chiave SSH pubblica quando si crea la macchina virtuale tramite il portale di Azure, l'interfaccia della riga di comando di Azure, i modelli di Azure Resource Manager o altri metodi:

* [Creare una macchina virtuale Linux con il portale di Azure](quick-create-portal.md)
* [Creare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](quick-create-cli.md)
* [Creare una VM Linux usando un modello di Azure](create-ssh-secured-vm-from-template.md)

Se non si ha familiarità con il formato di una chiave pubblica SSH, è possibile visualizzare la chiave pubblica con il comando `cat` seguente, sostituendo `~/.ssh/id_rsa.pub` con il percorso e il nome del proprio file di chiave pubblica, se necessario:

```bash
cat ~/.ssh/id_rsa.pub
```

Un valore di chiave pubblica tipico è simile all'esempio seguente:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Se si copiano e si incollano i contenuti del file di chiave pubblica da usare nel portale di Azure o in un modello di Resource Manager, verificare di non copiare spazi finali. Per copiare una chiave pubblica in macOS, è possibile inviare tramite pipe il file della chiave pubblica a `pbcopy`. Analogamente in Linux è possibile inviare tramite pipe il file della chiave pubblica a programmi come `xclip`.

Per impostazione predefinita, la chiave pubblica che si inserisce nella macchina virtuale Linux in Azure viene archiviata in ~/.ssh/id_rsa.pub, a meno che non sia stato specificato un percorso diverso in fase di creazione della coppia di chiavi. Per usare l'[interfaccia della riga di comando di Azure 2.0](/cli/azure) per creare la macchina virtuale con una chiave pubblica esistente, specificare il valore e, facoltativamente, il percorso della chiave pubblica usando il comando [az vm create](/cli/azure/vm#az-vm-create) con l'opzione `--ssh-key-values`. Nel comando seguente sostituire *myVM*, *myResourceGroup*, *UbuntuLTS*, *azureuser* e *mysshkey.pub* con valori personalizzati:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Se si vogliono usare più chiavi SSH con la VM, è possibile immetterle in un elenco separato da spazi, ad esempio `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`.


## <a name="ssh-into-your-vm"></a>Usare SSH per connettersi alla macchina virtuale

Con la chiave pubblica distribuita nella macchina virtuale di Azure e la chiave privata nel sistema locale, stabilire una connessione SSH alla macchina virtuale usando l'indirizzo IP o il nome DNS della macchina virtuale. Nel comando seguente sostituire *azureuser* e *myvm.westus.cloudapp.azure.com* con il nome utente dell'amministratore e il nome di dominio completo (o indirizzo IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se è stata specificata una passphrase durante la creazione della coppia di chiavi, immettere tale passphrase quando viene richiesta durante il processo di accesso. La macchina virtuale viene aggiunta al file ~/.ssh/known_hosts e non verrà chiesto di connettersi di nuovo finché la chiave pubblica nella macchina virtuale di Azure non viene modificata o il nome server viene rimosso da ~/.ssh/known_hosts.

Se la macchina virtuale usa i criteri di accesso JIT, è necessario richiedere l'accesso per connettersi alla macchina virtuale. Per altre informazioni sui criteri JIT, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'uso di coppie di chiavi SSH, vedere [Procedura dettagliata per creare e gestire coppie di chiavi SSH](create-ssh-keys-detailed.md).

* In caso di difficoltà con le connessioni SSH alle macchine virtuali di Azure, vedere [Risolvere i problemi di connessione SSH a una macchina virtuale Linux di Azure](../troubleshooting/troubleshoot-ssh-connection.md).
