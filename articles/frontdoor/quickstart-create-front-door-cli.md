---
title: 'Avvio rapido: Configurare la disponibilità elevata con Frontdoor di Azure - Interfaccia della riga di comando di Azure'
description: Questo argomento di avvio rapido illustra come usare Frontdoor di Azure per creare un'applicazione Web globale a disponibilità elevata e prestazioni elevate con l'interfaccia della riga di comando di Azure.
services: front-door
author: duongau
manager: KumudD
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/19/2021
ms.author: duau
ms.openlocfilehash: 3567d5af31b0c7bc2443e3d02426a5bb7aba06f7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862004"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Avvio rapido: Creare un'istanza di Frontdoor per un'applicazione Web globale a disponibilità elevata con l'interfaccia della riga di comando di Azure

Iniziare a usare Frontdoor di Azure con l'interfaccia della riga di comando di Azure per creare un'applicazione Web globale a disponibilità elevata e prestazioni elevate.

Frontdoor indirizza il traffico Web a risorse specifiche in un pool back-end. Definire il dominio front-end, aggiungere le risorse a un pool back-end e creare una regola di gestione. Questo articolo usa una semplice configurazione di un unico pool back-end con due risorse dell'app Web e un'unica regola di gestione che usa il percorso predefinito corrispondente a "/*".

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="Diagramma dell'ambiente di distribuzione front-door con l'interfaccia della riga di comando di Azure." border="false":::

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Interfaccia della riga di comando di Azure installata in locale o Azure Cloud Shell
- Assicurarsi che l'estensione Frontdoor sia stata aggiunta all'interfaccia della riga di comando di Azure

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento di avvio rapido è necessaria l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

In Azure, si allocano le risorse correlate a un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo.

Per questo argomento di avvio rapido sono necessari due gruppi di risorse. Uno negli *Stati Uniti centrali* e il secondo negli *Stati Uniti centro-meridionali*.

Come prima cosa creare con [az group create](/cli/azure/group#az_group_create) un gruppo di risorse:

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDEast \
    --location eastus
```

## <a name="create-two-instances-of-a-web-app"></a>Creare due istanze di un'app Web

Per questo argomento di avvio rapido, è necessario creare due istanze di un'applicazione Web eseguite in aree di Azure diverse. Entrambe le istanze dell'applicazione Web vengono eseguite in modalità attiva/attiva, in modo che entrambe possano gestire il traffico.

Se non si dispone già di un'app Web, usare lo script seguente per configurare due app Web di esempio.

### <a name="create-app-service-plans"></a>Creare piani di servizio app

Prima di poter creare le app Web, sono necessari due piani di servizio app, uno *negli* Stati Uniti centrali e il secondo negli Stati *Uniti orientali.*

Creare i piani di servizio app con [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true):

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanEastUS \
--resource-group myRGFDEast
```

### <a name="create-web-apps"></a>Creare app Web

L'esecuzione dei comandi seguenti creerà un'app Web in ognuno dei piani di servizio app del passaggio precedente. I nomi delle app Web devono essere univoci a livello globale.

Creare un'app Web con [az webapp create](/cli/azure/webapp#az_webapp_create&preserve-view=true):

```azurecli-interactive
az webapp create \
--name WebAppContoso-1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso-2 \
--resource-group myRGFDEast \
--plan myAppServicePlanEastUS
```

Prendere nota del nome host predefinito di ogni app Web, in modo da poter definire gli indirizzi back-end quando si distribuirà l'istanza di Frontdoor nel passaggio successivo.

## <a name="create-the-front-door"></a>Creare l'istanza di Frontdoor

Creare un'istanza di Frontdoor di base con le impostazioni predefinite di bilanciamento del carico, il probe di integrità e le regole di gestione eseguendo questa procedura:

Creare un'istanza di Frontdoor con [az network front-door create](/cli/azure/network/front-door#az_network_front_door_create&preserve-view=true):

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso-1.azurewebsites.net webappcontoso-2.azurewebsites.net 
```

**--resource-group:** specificare un gruppo di risorse in cui si vuole distribuire l'istanza di Frontdoor.

**--name:** specificare un nome univoco a livello globale per l'istanza di Frontdoor di Azure. 

**--accepted-protocols:** i valori possibili sono **http** e **https**. Se si vuole usare entrambi i valori, specificarli entrambi separati da uno spazio.

**--backend-address:** definire qui il nome host di entrambe le app Web separate da uno spazio.

Al termine della distribuzione, prendere nota del nome host nella sezione *frontEndpoints*.

## <a name="test-the-front-door"></a>Testare l'istanza di Frontdoor

Aprire un Web browser e immettere il nome host ottenuto dai comandi. L'istanza di Frontdoor indirizza la richiesta a una delle risorse back-end.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Pagina di test dell'istanza di Frontdoor":::

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create con l'istanza di Frontdoor non sono più necessarie, eliminare entrambi i gruppi di risorse. L'eliminazione del gruppo di risorse comporta anche l'eliminazione dell'istanza di Frontdoor e di tutte le risorse correlate. 

Per eliminare il gruppo di risorse, usare [az group delete](/cli/azure/group#az_group_delete&preserve-view=true):

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDEast
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati questi componenti:
* Frontdoor
* Due app Web

Per informazioni su come aggiungere un dominio personalizzato all'istanza di Frontdoor, proseguire con le esercitazioni su Frontdoor.

> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](front-door-custom-domain.md)
