---
title: 'Scenario: routing ai servizi condivisi reti virtuali'
titleSuffix: Azure Virtual WAN
description: 'Scenari per il routing: configurare route per accedere a un servizio condiviso VNet con un carico di lavoro a cui si vuole accedere ogni VNet e ramo.'
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e0d05d2cb960e760809ab35a8f9e4ca04acf250
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442962"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scenario: routing ai servizi condivisi reti virtuali

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In questo scenario, l'obiettivo è quello di configurare le route per accedere a un **servizio condiviso** VNet con i carichi di lavoro a cui si vuole accedere ogni VNet e ramo (VPN/er/P2S). Esempi di questi carichi di lavoro condivisi possono includere macchine virtuali con servizi come controller di dominio o condivisioni file o servizi di Azure esposti internamente tramite [endpoint privati di Azure](../private-link/private-endpoint-overview.md).

Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Progettazione

È possibile usare una matrice di connettività per riepilogare i requisiti di questo scenario:

**Matrice di connettività**

| Da             | Con:   |*Reti virtuali isolato*|*VNet condiviso*|*Rami*|
|---|---|---|---|---|
|**Reti virtuali isolato**| ->|        | Connessione diretta | Connessione diretta |
|**Reti virtuali condiviso**  |->| Connessione diretta | Connessione diretta | Connessione diretta |
|**Rami**      |->| Connessione diretta | Connessione diretta | Connessione diretta |

Ognuna delle celle della tabella precedente descrive se una connessione WAN virtuale (il lato "da" del flusso, le intestazioni di riga) comunica con una destinazione (il lato "a" del flusso, le intestazioni di colonna in corsivo). In questo scenario non sono presenti firewall o appliance virtuali di rete, quindi la comunicazione scorre direttamente sulla rete WAN virtuale, quindi la parola "Direct" nella tabella.

Analogamente allo [scenario VNet isolato](scenario-isolate-vnets.md), questa matrice di connettività fornisce due modelli di riga diversi, che vengono convertiti in due tabelle di route (i servizi condivisi reti virtuali e i rami hanno gli stessi requisiti di connettività). Per la rete WAN virtuale è già presente una tabella di route predefinita, quindi è necessaria un'altra tabella di route personalizzata, che verrà chiamata in questo esempio **RT_SHARED** .

Reti virtuali verrà associato alla tabella di route **RT_SHARED** . Poiché necessitano di connettività ai rami e al servizio condiviso reti virtuali, i rami e i VNet del servizio condiviso dovranno essere propagati a **RT_SHARED** . in caso contrario, reti virtuali non apprenderà il ramo e i prefissi VNet condivisi. Poiché i rami sono sempre associati alla tabella di route predefinita e i requisiti di connettività sono gli stessi per i servizi condivisi reti virtuali, si associa anche il reti virtuali del servizio condiviso alla tabella di route predefinita.

Di conseguenza, si tratta della progettazione finale:

* Reti virtuali isolate:
  * Tabella di route associata: **RT_SHARED**
  * Propagazione alle tabelle di route: **predefinita**
* Reti virtuali dei servizi condivisi:
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **RT_SHARED** e **default**
* Rami
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **RT_SHARED** e **default**

> [!NOTE]
> Se la rete WAN virtuale viene distribuita in più aree, è necessario creare la tabella di route **RT_SHARED** in ogni hub e le route da ogni VNet di servizi condivisi e la connessione del ramo devono essere propagate alle tabelle di route in ogni hub virtuale usando le etichette di propagazione.

Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Flusso di lavoro

Per configurare lo scenario, prendere in considerazione i passaggi seguenti:

1. Identificare il VNet dei **servizi condivisi** .
2. Creare una tabella di route personalizzata. Nell'esempio viene fatto riferimento alla tabella di route come **RT_SHARED**. Per i passaggi necessari per creare una tabella di route, vedere [How to Configure Virtual Hub routing](how-to-virtual-hub-routing.md). Usare i valori seguenti come linee guida:

   * **Associazione**
     * Per **reti virtuali ad *eccezione* dei servizi condivisi VNet**, selezionare il reti virtuali da isolare. Questo implica che tutti questi reti virtuali (ad eccezione dei servizi condivisi VNet) saranno in grado di raggiungere la destinazione in base alle route di RT_SHARED tabella di route.

   * **Propagazione**
      * Per i **rami**, è possibile propagare le route a questa tabella di route, oltre a qualsiasi altra tabella di route già selezionata. A causa di questo passaggio, la tabella di route RT_SHARED apprenderà le route da tutte le connessioni ramo (VPN/ER/VPN utente).
      * Per **reti virtuali** selezionare il **VNet dei servizi condivisi**. A causa di questo passaggio, RT_SHARED tabella di route apprenderà le route dalla connessione VNet dei servizi condivisi.

Questa operazione comporterà la configurazione del routing mostrata nella figura seguente:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Diagramma per i servizi condivisi VNet." lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>Passaggi successivi

* Per configurare usando un modello ARM, vedere [Guida introduttiva: indirizzare i servizi condivisi reti virtuali usando un modello ARM](quickstart-route-shared-services-vnet-template.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
