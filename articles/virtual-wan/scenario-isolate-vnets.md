---
title: 'Scenario: Isolating reti virtuali'
titleSuffix: Azure Virtual WAN
description: Scenari per l'isolamento del routing reti virtuali
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e5e2ce17be6d8a1fa82d8a92b9b788f0bd2a37b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92424739"
---
# <a name="scenario-isolating-vnets"></a>Scenario: Isolating reti virtuali

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In questo scenario, l'obiettivo è impedire a reti virtuali di raggiungere altri. Questa operazione è nota come Isolating reti virtuali. Per informazioni sul routing degli hub virtuali, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Progettazione

In questo scenario, il carico di lavoro all'interno di un determinato VNet rimane isolato e non è in grado di comunicare con altri reti virtuali. Tuttavia, reti virtuali sono necessari per raggiungere tutti i rami (VPN, ER e VPN utente). Per determinare il numero di tabelle di route necessarie, è possibile creare una matrice di connettività. Per questo scenario l'aspetto sarà simile alla tabella seguente, in cui ogni cella indica se un'origine (riga) può comunicare con una destinazione (colonna):

| Da |   A |  *Reti virtuali* | *Rami* |
| -------------- | -------- | ---------- | ---|
| Reti virtuali     | &#8594;| Connessione diretta |   Connessione diretta    |
| Rami   | &#8594;|  Connessione diretta  |   Connessione diretta    |

Ognuna delle celle della tabella precedente descrive se una connessione WAN virtuale (il lato "da" del flusso, le intestazioni di riga) comunica con un prefisso di destinazione (il lato "a" del flusso, le intestazioni di colonna in corsivo). In questo scenario non sono presenti firewall o appliance virtuali di rete, quindi le comunicazioni passano direttamente alla rete WAN virtuale, quindi la parola "Direct" nella tabella.

Questa matrice di connettività fornisce due modelli di riga diversi, che vengono convertiti in due tabelle di route. Per la rete WAN virtuale è già presente una tabella di route predefinita, quindi è necessaria un'altra tabella di route. Per questo esempio, la tabella di route verrà denominata **RT_VNET**.

Reti virtuali verrà associato a questa **RT_VNET** tabella di route. Poiché necessitano di connettività ai Branch, i rami dovranno propagarsi a **RT_VNET** . in caso contrario, reti virtuali non apprenderà i prefissi del ramo. Poiché i rami sono sempre associati alla tabella di route predefinita, reti virtuali dovrà essere propagato alla tabella di route predefinita. Di conseguenza, si tratta della progettazione finale:

* Reti virtuali:
  * Tabella di route associata: **RT_VNET**
  * Propagazione alle tabelle di route: **predefinita**
* Rami
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **RT_VNET** e **default**

Si noti che poiché solo i rami si propagano alla tabella di route **RT_VNET**, questi saranno gli unici prefissi che reti virtuali apprenderà e non quelli di altri reti virtuali.

Per informazioni sul routing degli hub virtuali, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Flusso di lavoro

Per configurare questo scenario, prendere in considerazione i passaggi seguenti:

1. Creare una tabella di route personalizzata in ogni hub. Nell'esempio, la tabella di route è **RT_VNet**. Per creare una tabella di route, vedere [How to Configure Virtual Hub routing](how-to-virtual-hub-routing.md). Per ulteriori informazioni sulle tabelle di route, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).
2. Quando si crea la tabella di route **RT_VNet** , configurare le impostazioni seguenti:

   * **Associazione**: selezionare il reti virtuali che si vuole isolare.
   * **Propagazione**: selezionare l'opzione per i rami. le connessioni che implicano il ramo (VPN/er/P2S) propagheranno le route a questa tabella di route.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Reti virtuali isolato":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
