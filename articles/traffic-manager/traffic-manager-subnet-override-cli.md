---
title: Override della subnet di gestione traffico di Azure con l'interfaccia della riga di comando Microsoft Docs
description: Questo articolo illustra come usare l'override della subnet di gestione traffico per eseguire l'override del metodo di routing di un profilo di gestione traffico per indirizzare il traffico a un endpoint in base all'indirizzo IP dell'utente finale tramite un intervallo IP predefinito per i mapping degli endpoint.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 2e289728c7fde9b98256d079d45067aba1d4d805
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211329"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Sostituzione della subnet di gestione traffico con l'interfaccia della riga

L'override della subnet di gestione traffico consente di modificare il metodo di routing di un profilo.  L'aggiunta di una sostituzione consente di indirizzare il traffico in base all'indirizzo IP dell'utente finale con un intervallo di indirizzi IP predefinito al mapping degli endpoint. 

## <a name="how-subnet-override-works"></a>Funzionamento dell'override della subnet

Quando si aggiungono sostituzioni di subnet a un profilo di gestione traffico, gestione traffico verificherà prima di tutto se è presente una subnet sostitutiva per l'indirizzo IP dell'utente finale. Se ne viene trovato uno, la query DNS dell'utente viene indirizzata all'endpoint corrispondente.  Se non viene trovato alcun mapping, gestione traffico eseguirà il fallback al metodo di routing originale del profilo. 

Gli intervalli di indirizzi IP possono essere specificati come intervalli CIDR (ad esempio, 1.2.3.0/24) o come intervalli di indirizzi (ad esempio, 1.2.3.4-5.6.7.8). Gli intervalli IP associati a ogni endpoint devono essere univoci per tale endpoint. Qualsiasi sovrapposizione degli intervalli IP tra endpoint diversi causerà il rifiuto del profilo da parte di gestione traffico.

Sono disponibili due tipi di profili di routing che supportano le sostituzioni delle subnet:

* **Geografico** : se Gestione traffico trova una sostituzione della subnet per l'indirizzo IP della query DNS, la query verrà indirizzata all'endpoint indipendentemente dall'integrità dell'endpoint.
* **Prestazioni** : se Gestione traffico trova una sostituzione della subnet per l'indirizzo IP della query DNS, eseguirà il routing del traffico all'endpoint solo se è integro.  Gestione traffico eseguirà il fallback all'euristica di routing delle prestazioni se l'endpoint di override della subnet non è integro.

## <a name="create-a-traffic-manager-subnet-override"></a>Creare un'override della subnet di gestione traffico

Per creare una sostituzione della subnet di gestione traffico, è possibile usare l'interfaccia della riga di comando di Azure per aggiungere le subnet per la sostituzione all'endpoint di gestione traffico.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Aggiornare l'endpoint di gestione traffico con l'override della subnet.
Usare l'interfaccia della riga di comando di Azure per aggiornare l'endpoint con [AZ Network Traffic-Manager endpoint Update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update).

```azurecli-interactive
### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints
```

È possibile rimuovere gli intervalli di indirizzi IP eseguendo il comando [AZ Network Traffic-Manager endpoint Update](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-update) con l'opzione **--Remove** .

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [metodi di routing](traffic-manager-routing-methods.md)di Gestione traffico.

Informazioni sul [metodo di routing del traffico della subnet](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)