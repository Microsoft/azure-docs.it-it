---
title: Instradare il traffico attraverso un'appliance virtuale di rete - Esempio di script dell'interfaccia della riga di comando di Azure
description: Esempio di script dell'interfaccia della riga di comando di Azure - Instradare il traffico attraverso un'appliance virtuale di rete.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: cc539800cb9cb75ecc69f0d7ae923ed3edfd2ad0
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87492293"
---
# <a name="route-traffic-through-a-network-virtual-appliance---azure-cli-script-sample"></a>Instradare il traffico attraverso un'appliance virtuale di rete - Esempio di script dell'interfaccia della riga di comando di Azure

Questo script di esempio crea una rete virtuale con subnet front-end e back-end. Crea anche una VM con inoltro IP attivato per instradare il traffico tra le due subnet. Dopo aver eseguito lo script è possibile distribuire il software di rete, ad esempio un'applicazione firewall, nella VM.

È possibile eseguire lo script da Azure [Cloud Shell](https://shell.azure.com/bash) o da un'installazione locale dell'interfaccia della riga di comando di Azure. Se si usa l'interfaccia della riga di comando in locale, per questo script è necessaria la versione 2.0.28 o successiva. Per trovare la versione installata, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando in locale, è anche necessario eseguire `az login` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, una rete virtuale e i gruppi di sicurezza di rete. Ogni comando della tabella seguente include collegamenti alla documentazione specifica del comando:

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az network vnet create](/cli/azure/network/vnet) | Consente di creare una rete virtuale e una subnet front-end di Azure. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Consente di creare le subnet back-end e di rete perimetrale. |
| [az network public-ip create](/cli/azure/network/public-ip) | Consente di creare un indirizzo IP pubblico per accedere alla macchina virtuale da Internet. |
| [az network nic create](/cli/azure/network/nic) | Consente di creare un'interfaccia di rete virtuale e attiva l'inoltro IP. |
| [az network nsg create](/cli/azure/network/nsg) | Consente di creare un gruppo di sicurezza di rete. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Consente di creare regole del gruppo di sicurezza di rete per le porte HTTP e HTTPS in ingresso alla VM. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet)| Consente di associare il gruppo di sicurezza di rete e le tabelle di route alle subnet. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Consente di creare una tabella di route per tutte le route. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Consente di creare le route per instradare il traffico tra subnet e Internet attraverso la macchina virtuale. |
| [az vm create](/cli/azure/vm) | Consente di creare una macchina virtuale e vi connette la NIC. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione. |
| [az group delete](/cli/azure/group) | Consente di eliminare un gruppo di risorse e tutte le risorse in esso contenute. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della rete virtuale sono disponibili in [Virtual network CLI samples](../cli-samples.md) (Esempi di interfaccia della riga di comando della rete virtuale).
