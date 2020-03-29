---
title: Accedere al pannello di controllo di Avere vFXT - Azure
description: Come connettersi al cluster vFXT e al pannello di controllo basato su browser di Avere per configurare Avere vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: fe2fc062f690498f3d1f588887279aa33d2434b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75416139"
---
# <a name="access-the-vfxt-cluster"></a>Accedere al cluster vFXT

Per regolare le impostazioni del cluster e monitorare il cluster, utilizzare il Pannello di controllo Di Avere. Il pannello di controllo di Avere è un'interfaccia grafica basata su browser per il cluster.

Poiché il cluster vFXT si trova all'interno di una rete virtuale privata, è necessario creare un tunnel SSH o utilizzare un altro metodo per raggiungere l'indirizzo IP di gestione del cluster.

I passaggi di base sono due:

1. Creare una connessione tra la workstation e la rete virtuale privataCreate a connection between your workstation and the private virtual network
1. Caricare il pannello di controllo del cluster in un Web browser

> [!NOTE]
> In questo articolo si presuppone di aver già impostato un indirizzo IP pubblico nel controller del cluster o in un'altra macchina virtuale all'interno della rete virtuale del cluster. Questo articolo descrive come usare tale macchina virtuale come host per accedere al cluster. Se si utilizza una VPN o ExpressRoute per l'accesso alla rete virtuale, passare a [Connettersi al Pannello di controllo Di Avere](#connect-to-the-avere-control-panel-in-a-browser).

Prima della connessione, assicurarsi che la coppia di chiavi pubblica/privata SSH usata durante la creazione del controller del cluster sia installata nel computer locale. Per istruzioni, vedere la documentazione sulle chiavi SSH per [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) o per [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys). Se è stata utilizzata una password anziché una chiave pubblica, verrà richiesto di immetterla quando ci si connette.

## <a name="create-an-ssh-tunnel"></a>Creazione di un tunnel SSH

È possibile creare un tunnel SSH dalla riga di comando di un sistema client Windows 10 o Basato su Linux.You can create an SSH tunnel from the command line of a Linux-based Windows 10 client system.

Utilizzare un comando di tunneling SSH con questo formato:

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Questo comando esegue la connessione all'indirizzo IP di gestione del cluster tramite l'indirizzo IP del controller del cluster.

Esempio:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

L'autenticazione avviene automaticamente se si è usata la chiave SSH pubblica per creare il cluster e la chiave corrispondente è installata nel sistema client. Se si è usata una password, il sistema chiederà di specificarla.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Connettersi al pannello di controllo di Avere in un browser

Questo passaggio usa un Web browser per connettersi all'utilità di configurazione nel cluster vFXT.

* Per una connessione tunnel SSH, aprire il Web browser e passare a `https://127.0.0.1:8443`.

  Ci si è connessi all'indirizzo IP del cluster al momento della creazione del tunnel, pertanto è sufficiente usare l'indirizzo IP localhost nel browser. Se è stata usata una porta locale diversa dalla 8443, usare invece il numero di porta.

* Se si usa una VPN o ExpressRoute per raggiungere il cluster, passare all'indirizzo IP di gestione del cluster nel browser. Esempio: ``https://203.0.113.51``

A seconda del browser, potrebbe essere necessario fare clic su **Avanzate** e verificare che sia sicuro procedere alla pagina.

Immettere il nome utente `admin` e la password amministrativa specificata in fase di creazione del cluster.

![Screenshot della finestra di accesso di Avere popolata con il nome utente "admin" e una password](media/avere-vfxt-gui-login.png)

Fare clic su **Login** (Accedi) o premere INVIO sulla tastiera.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver effettuato l'accesso al pannello di controllo del cluster, abilitare il [supporto](avere-vfxt-enable-support.md).
