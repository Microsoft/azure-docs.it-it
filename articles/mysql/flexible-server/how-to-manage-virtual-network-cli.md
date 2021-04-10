---
title: Gestire le reti virtuali-interfaccia della riga di comando di Azure-database di Azure per MySQL-server flessibile
description: Creare e gestire reti virtuali per database di Azure per MySQL-server flessibile usando l'interfaccia della riga di comando di Azure
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: e60f9010b8fbfb3766aea6b5cffa1e7c001c6eca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106735"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Creare e gestire reti virtuali per database di Azure per MySQL-server flessibile usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica

Il server flessibile del Database di Azure per MySQL supporta due tipi di metodi di connettività di rete che si escludono a vicenda per la connessione al server flessibile. Le due opzioni sono:

- Accesso pubblico (indirizzi IP consentiti)
- Accesso privato (integrazione rete virtuale)

In questo articolo verrà illustrata la creazione di un server MySQL con **accesso privato (VNet Integration) tramite l'** interfaccia della riga di comando di Azure. Con *l'accesso privato (VNet Integration)*, è possibile distribuire il server flessibile nella propria [rete virtuale di Azure](../../virtual-network/virtual-networks-overview.md). Le reti virtuali di Azure forniscono comunicazioni di rete private e sicure. In accesso privato, le connessioni al server MySQL sono limitate solo all'interno della rete virtuale. Per ulteriori informazioni, vedere [accesso privato (integrazione VNet)](./concepts-networking.md#private-access-vnet-integration).

Nel server flessibile database di Azure per MySQL è possibile distribuire il server solo in una rete virtuale e una subnet durante la creazione del server. Quando il server flessibile viene distribuito in una rete virtuale e in una subnet, non è possibile spostarlo in un'altra rete virtuale, subnet o *accesso pubblico (indirizzi IP consentiti)*.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account.

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile aprire Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **INVIO** per eseguirli.

Se si preferisce installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Prerequisiti

È necessario accedere all'account con il comando [az login](/cli/azure/reference-index#az-login). Prendere nota della proprietà **ID** , che fa riferimento all' **ID sottoscrizione** per l'account Azure.

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](/cli/azure/account#az-account-set). Prendere nota del valore **ID** del comando **AZ login** output da usare come valore per l'argomento **Subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>Creare un server flessibile per database di Azure per MySQL con CLI
È possibile usare il `az mysql flexible-server` comando per creare il server flessibile con *accesso privato (VNet Integration)*. Questo comando usa l'accesso privato (VNet Integration) come metodo di connettività predefinito. Verranno create una rete virtuale e una subnet se non ne viene fornita una. È anche possibile fornire la rete virtuale e la subnet già esistenti usando l'ID subnet. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Sono disponibili diverse opzioni per creare un server flessibile usando l'interfaccia della riga di comando, come illustrato negli esempi seguenti.

>[!Important]
> Se si usa questo comando, la subnet verrà delegata a **Microsoft. DBforMySQL/flexibleServers**. Con questa delega solo i server flessibili di Database di Azure per MySQL possono usare tale subnet. Gli altri tipi di risorsa di Azure non possono trovarsi nella subnet delegata.
>

Vedere la [documentazione di riferimento](/cli/azure/mysql/flexible-server) dell'interfaccia della riga di comando di Azure per l'elenco completo dei parametri CLI configurabili. Ad esempio, nei comandi seguenti è possibile specificare facoltativamente il gruppo di risorse.

- Creare un server flessibile usando la rete virtuale predefinita, la subnet con il prefisso dell'indirizzo predefinito
    ```azurecli-interactive
    az mysql flexible-server create
    ```
- Creare un server flessibile usando la rete virtuale e la subnet già esistenti. Se la rete virtuale e la subnet specificate non esistono, verrà creata la rete virtuale e la subnet con prefisso di indirizzo predefinito.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```

- Creare un server flessibile usando una rete virtuale, una subnet e un ID subnet già esistenti. Alla subnet specificata non deve essere distribuita alcuna altra risorsa e la subnet verrà delegata a **Microsoft. DBforMySQL/flexibleServers**, se non è già stata delegata.
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > La rete virtuale e la subnet devono trovarsi nella stessa area e nella stessa sottoscrizione del server flessibile.
<
- Creare un server flessibile usando la nuova rete virtuale e la subnet con prefisso di indirizzo non predefinito.
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --address-prefixes 10.0.0.0/24 --subnet mySubnet --subnet-prefixes 10.0.0.0/24
    ```
Vedere la [documentazione di riferimento](/cli/azure/mysql/flexible-server) dell'interfaccia della riga di comando di Azure per l'elenco completo dei parametri CLI configurabili.


## <a name="next-steps"></a>Passaggi successivi
- Scopri di più sulla [rete nel database di Azure per il server flessibile MySQL](./concepts-networking.md).
- [Creare e gestire la rete virtuale del server flessibile di Database di Azure per MySQL tramite il portale di Azure](./how-to-manage-virtual-network-portal.md).
- Ulteriori informazioni sulla [rete virtuale di server flessibili per database di Azure per MySQL](./concepts-networking.md#private-access-vnet-integration).