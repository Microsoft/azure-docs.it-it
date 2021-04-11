---
title: Introduzione a FreeBSD in Azure
description: Informazioni sull'uso delle macchine virtuali FreeBSD in Azure
author: thomas1206
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: mimckitt
ms.openlocfilehash: da51fabaaa3c02137770f0b2d9a851b1f6702980
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044050"
---
# <a name="introduction-to-freebsd-on-azure"></a>Introduzione a FreeBSD in Azure
Questo articolo offre una panoramica dell'esecuzione di una macchina virtuale FreeBSD in Azure.

## <a name="overview"></a>Panoramica
FreeBSD per Microsoft Azure è un avanzato sistema operativo impiegato per potenziare moderne piattaforme incorporate, server e desktop.

Microsoft Corporation sta creando immagini di FreeBSD disponibili in Azure con l'[agente guest della macchina virtuale di Azure](https://github.com/Azure/WALinuxAgent/) preconfigurato. Attualmente, sono disponibili come immagini le seguenti versioni di FreeBSD:

- FreeBSD 10.4 in Azure Marketplace
- FreeBSD 11.2 in Azure Marketplace
- FreeBSD 12.0 in Azure Marketplace

L'agente è responsabile della comunicazione tra la VM FreeBSD e l'infrastruttura di Azure per operazioni quali il provisioning della VM al primo uso (nome utente, password o chiave SSH, nome host e così via) e l'abilitazione di funzionalità per estensioni di VM selettive.

Per quanto riguarda le versioni future di FreeBSD, la strategia consiste nel rimanere aggiornati e rendere disponibili le versioni più recenti subito dopo la pubblicazione da parte del team di progettazione di FreeBSD.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Creare una VM FreeBSD tramite l'interfaccia della riga di comando di Azure su FreeBSD
Prima di tutto è necessario installare l'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli) in un computer FreeBSD con il comando seguente.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Se bash non è installato nel computer FreeBSD, eseguire il seguente comando prima dell'installazione. 

```bash
sudo pkg install bash
```

Se python non è installato nel computer FreeBSD, eseguire i seguenti comandi prima dell'installazione. 

```bash
sudo pkg install python38
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.8 /usr/local/bin/python
```

Durante l'installazione, viene visualizzata la seguente richiesta: `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Se si risponde `y` e si immette `/etc/rc.conf` come `a path to an rc file to update`, può verificarsi il problema `ERROR: [Errno 13] Permission denied`. Per risolvere questo problema assegnare i diritti di scrittura all'utente corrente per il file `etc/rc.conf`.

A questo punto è possibile accedere ad Azure e creare la VM FreeBSD. Di seguito è riportato un esempio di creazione di una VM FreeBSD 11.0. È anche possibile aggiungere il parametro `--public-ip-address-dns-name` con un nome DNS univoco globale per un indirizzo IP pubblico appena creato. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Quindi è possibile accedere alla VM FreeBSD mediante l'indirizzo IP visualizzato nell'output della distribuzione mostrata in precedenza. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Estensioni di VM per FreeBSD
Di seguito sono descritte le estensioni di VM supportate in FreeBSD.

### <a name="vmaccess"></a>VMAccess
Con l'estensione [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) è possibile:

* Reimpostare la password dell'utente sudo originale.
* Creare un nuovo utente sudo con la password specificata.
* Impostare la chiave host pubblica con la chiave specificata.
* Reimpostare la chiave host pubblica specificata durante il provisioning di VM se non è stata indicata la chiave host.
* Aprire la porta SSH (22) e ripristinare sshd_config se reset_ssh è impostato su true.
* Rimuovere l'utente esistente.
* Controllare i dischi.
* Riparare un disco aggiunto.

### <a name="customscript"></a>CustomScript
Con l'estensione [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) è possibile:

* Se disponibili, scaricare gli script personalizzati dall'archivio di Azure o dall'archivio pubblico esterno, ad esempio GitHub.
* Eseguire lo script di punto di ingresso.
* Supportare comandi inline.
* Convertire automaticamente la nuova riga stile Windows in script della shell e Python.
* Rimuovere automaticamente un carattere BOM negli script della shell e Python.
* Proteggere i dati sensibili in CommandToExecute.

> [!NOTE]
> FreeBSD VM supporta solo CustomScript versione 1. x per adesso.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticazione: nomi utente, password e chiavi SSH
Quando si crea una macchina virtuale FreeBSD usando il portale di Azure, è necessario immettere un nome utente, una password o una chiave pubblica SSH.
I nomi utente per la distribuzione di una macchina virtuale FreeBSD in Azure non devono corrispondere ai nomi degli account di sistema (UID <100) già presenti nella macchina virtuale, ad esempio "root".
È attualmente supportata solo la chiave RSA SSH. Una chiave SSH su più righe deve iniziare con `---- BEGIN SSH2 PUBLIC KEY ----` e terminare con `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Ottenere privilegi utente avanzati
L'account utente specificato durante la distribuzione di istanze di macchine virtuali in Azure è un account con privilegi. Il pacchetto di sudo è stato installato nell'immagine FreeBSD pubblicata.
Dopo aver eseguito l'accesso usando questo account utente, è possibile eseguire comandi come utente ROOT usando la sintassi del comando.

```
$ sudo <COMMAND>
```

Facoltativamente, è possibile ottenere una shell radice usando `sudo -s`.

## <a name="known-issues"></a>Problemi noti
L'[agente guest per macchine virtuali di Azure](https://github.com/Azure/WALinuxAgent/) versione 2.2.2 presenta un [problema noto](https://github.com/Azure/WALinuxAgent/pull/517), che provoca un errore di provisioning per la macchina virtuale FreeBSD in Azure. La correzione è stata acquisita da [Agente guest di macchine virtuali di Azure](https://github.com/Azure/WALinuxAgent/) versione 2.2.3 e successive. 

## <a name="next-steps"></a>Passaggi successivi
* Passare ad [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/thefreebsdfoundation.freebsd-12_2?tab=Overview) per creare una VM FreeBSD.
