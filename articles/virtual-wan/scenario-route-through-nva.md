---
title: "Scenario: instradare il traffico attraverso un'appliance virtuale di rete"
titleSuffix: Azure Virtual WAN
description: Indirizzare il traffico attraverso un'appliance virtuale di rete
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 24671a34214864e253d96c356dc8b2853bf6d560
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100519797"
---
# <a name="scenario-route-traffic-through-an-nva"></a>Scenario: Indirizzare il traffico attraverso un'appliance virtuale di rete

Quando si usa il routing dell'hub virtuale WAN virtuale, esistono molti scenari disponibili. In questo scenario di appliance virtuale di rete, l'obiettivo è instradare il traffico attraverso un'appliance virtuale di rete (appliance virtuale di rete) per il ramo a VNet e VNet al ramo. Per informazioni sul routing degli hub virtuali, vedere [About Virtual Hub routing](about-virtual-hub-routing.md).

> [!NOTE]
> Se è già stata configurata una configurazione con route precedenti alle nuove funzionalità [come configurare il routing dell'hub virtuale](how-to-virtual-hub-routing.md) che diventa disponibile, seguire questa procedura in queste versioni degli articoli:
>* [Articolo portale di Azure](virtual-wan-route-table-nva-portal.md)
>* [Articolo di PowerShell](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>Progettazione

In questo scenario verrà usata la convenzione di denominazione:

* "NVA reti virtuali" per le reti virtuali in cui gli utenti hanno distribuito un'appliance virtuale di rete e hanno connesso altre reti virtuali come spoke (VNet 2 e VNet 4 nella **Figura 2** più avanti nell'articolo).
* "NVA spokes" per le reti virtuali connesse a un appliance virtuale di rete VNet (VNet 5, VNet 6, VNet 7 e VNet 8 nella **Figura 2** più avanti nell'articolo).
* "Non-Appliance reti virtuali" per le reti virtuali connesse alla rete WAN virtuale che non dispongono di un appliance virtuale di rete o di altri reti virtuali con peering (VNet 1 e VNet 3 nella **Figura 2** più avanti nell'articolo).
* "Hub" per hub WAN virtuali gestiti da Microsoft, in cui i reti virtuali di rete virtuale virtuale sono connessi a. L'appliance virtuale di rete spoke reti virtuali non deve essere connessa a hub WAN virtuali, solo a appliance virtuale di rete virtuale reti virtuali.

La seguente matrice di connettività, riepiloga i flussi supportati in questo scenario:

**Matrice di connettività**

| Da             | Con:|   *Spoke di NVA*|*Reti virtuali di appliance virtuale di dispositivo*|*Reti virtuali non appliance virtuale di dispositivo*|*Rami*|
|---|---|---|---|---|---|
| **Spoke di NVA**   | &#8594; | Su appliance virtuale di VNet | Peering | Su appliance virtuale di VNet | Su appliance virtuale di VNet |
| **Reti virtuali di appliance virtuale di dispositivo**    | &#8594; | Peering | Connessione diretta | Connessione diretta | Connessione diretta |
| **Reti virtuali non appliance virtuale di dispositivo**| &#8594; | Su appliance virtuale di VNet | Connessione diretta | Connessione diretta | Connessione diretta |
| **Rami**     | &#8594; | Su appliance virtuale di VNet | Connessione diretta | Connessione diretta | Connessione diretta |

Ognuna delle celle nella matrice di connettività descrive il modo in cui un VNet o un ramo (il lato "da" del flusso, le intestazioni di riga nella tabella) comunica con un VNet o un ramo di destinazione (il lato "a" del flusso, le intestazioni di colonna in corsivo nella tabella). "Direct" significa che la connettività viene fornita in modo nativo dalla rete WAN virtuale, ovvero "peering" significa che la connettività viene fornita da una route di User-Defined in VNet, "over appliance virtuale di rete virtuale" significa che la connettività attraversa l'appliance virtuale di rete distribuita nell'appliance virtuale di rete (NVA). Considerare quanto segue:

* I spoke di appliance virtuale di rete non sono gestiti da WAN virtuale. Di conseguenza, i meccanismi con i quali comunicheranno con altri reti virtuali o rami verranno gestiti dall'utente. La connettività ai VNet dell'appliance virtuale di rete viene fornita da un peering VNet e una route predefinita a 0.0.0.0/0 che punta all'appliance virtuale di rete come hop successivo dovrebbe coprire la connettività a Internet, ad altri spoke e ai Branch
* L'appliance virtuale di reti virtuali saprà conoscere i propri spoke di appliance virtuale di dispositivo, ma non i spoke di appliance virtuale di appliance connessi ad altri reti virtuali di appliance Ad esempio, nella figura 2, più avanti in questo articolo, VNet 2 sa di VNet 5 e VNet 6, ma non di altri spoke, ad esempio VNet 7 e VNet 8. Per inserire i prefissi di altri spoke in appliance virtuale di reti virtuali, è necessaria una route statica
* Analogamente, i rami e i reti virtuali non di appliance virtuale di rete non sono in grado di rilevare i problemi dell'appliance virtuale di rete, perché i spoke di NVA non sono connessi agli hub Di conseguenza, le route statiche saranno necessarie anche qui.

Tenendo presente che i spoke di appliance virtuale di rete non sono gestiti da WAN virtuale, tutte le altre righe mostrano lo stesso modello di connettività. Di conseguenza, una singola tabella di route, ovvero quella predefinita, eseguirà le operazioni seguenti:

* Reti virtuali (reti virtuali non hub e reti virtuali dell'hub utente):
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **predefinita**
* Rami
  * Tabella di route associata: **predefinita**
  * Propagazione alle tabelle di route: **predefinita**

Tuttavia, in questo scenario è necessario considerare le route statiche da configurare. Ogni route statica avrà due componenti, una parte nell'hub WAN virtuale che comunica ai componenti WAN virtuali la connessione da usare per ogni spoke e un'altra nella connessione specifica che punta all'indirizzo IP concreto assegnato all'appliance virtuale di rete (o a un servizio di bilanciamento del carico davanti a più appliance virtuali), come illustrato **nella figura 1** :

**Figura 1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="Figura 1":::

In questo modo, le route statiche necessarie nella tabella predefinita per inviare il traffico ai spoke di appliance virtuale di dispositivo dietro la VNet dell'appliance virtuale di dispositivo sono le seguenti:

| Descrizione | Tabella di route | Route statica              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | Predefinito     | 10.2.0.0/16-> eastusconn |
| VNet 4       | Predefinito     | 10.4.0.0/16-> weconn     |

A questo punto, la rete WAN virtuale sa a quale connessione inviare i pacchetti, ma la connessione deve sapere cosa fare quando ricevono i pacchetti: qui vengono usate le tabelle della route di connessione. Qui verranno usati i prefissi più brevi (/24 anziché più a lungo/16) per assicurarsi che queste route abbiano la preferenza sulle route importate dall'appliance virtuale di reti virtuali (VNet 2 e VNet 4):

| Descrizione | Connessione | Route statica            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24-> 10.2.0.5 |
| VNet 6       | eastusconn | 10.2.2.0/24-> 10.2.0.5 |
| VNet 7       | weconn     | 10.4.1.0/24-> 10.4.0.5 |
| VNet 8       | weconn     | 10.4.2.0/24-> 10.4.0.5 |

A questo punto, i rami reti virtuali, non reti virtuali e i rami sanno come raggiungere tutti i spoke di appliance virtuale di dispositivo. Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architettura

Nella **Figura 2** sono presenti due hub: **HUB1** e **HUB2**.

* **HUB1** e **HUB2** sono direttamente connessi a appliance virtuale di reti virtuali **VNet 2** e **VNet 4**.

* Il peering di **VNet 5** e **VNet 6** è **VNet 2**.

* **VNet 7** e **VNet 8** sono peering con **VNet 4**.

* **Reti virtuali 5, 6, 7, 8** sono spoke indiretti, non direttamente connessi a un hub virtuale.

**Figura 2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="Figura 2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Flusso di lavoro dello scenario

Per configurare il routing tramite appliance virtuale di sistema, ecco i passaggi da considerare:

1. Identificare la connessione VNet per l'appliance virtuale di dispositivo. Nella **Figura 2** sono **VNet 2 Connection (Eastusconn)** e **VNet 4 Connection (weconn)**.

   Verificare la configurazione di UdR:
   * Da VNet 5 e VNet da 6 a VNet 2 appliance virtuale di dispositivo virtuale
   * Da VNet 7 e VNet 8 a VNet 4 appliance virtuale di dispositivo virtuale 
   
   Non è necessario connettere direttamente reti virtuali 5, 6, 7 e 8 agli hub virtuali. Assicurarsi che gruppi in reti virtuali 5, 6, 7, 8 consentano il traffico per Branch (VPN/ER/P2S) o reti virtuali connesso al reti virtuali remoto. Ad esempio, reti virtuali 5, 6 deve garantire che gruppi consenta il traffico per i prefissi degli indirizzi locali e reti virtuali 7, 8 connessi all'Hub remoto 2.

La rete WAN virtuale non supporta uno scenario in cui reti virtuali 5, 6 si connettono all'hub virtuale e comunicano tramite l'IP di VNet 2. quindi, la necessità di connettere reti virtuali 5, 6 a VNet2 e in modo analogo VNet 7, 8 a VNet 4.

2. Aggiungere una voce di route statica aggregata per reti virtuali 2, 5, 6 alla tabella di route predefinita dell'hub 1.

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="Esempio":::

3. Configurare una route statica per reti virtuali 5, 6 nella connessione di rete virtuale di VNet 2. Per configurare la configurazione del routing per una connessione di rete virtuale, vedere [routing dell'hub virtuale](how-to-virtual-hub-routing.md#routing-configuration).

4. Aggiungere una voce di route statica aggregata per reti virtuali 4, 7, 8 alla tabella di route predefinita dell'hub 1.

5. Ripetere i passaggi 2, 3 e 4 per la tabella di route predefinita dell'hub 2.

Questo comporterà la modifica della configurazione del routing, come illustrato nella **Figura 3** riportata di seguito.

**Figura 3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="Figura 3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).
* Per altre informazioni sul routing degli hub virtuali, vedere [informazioni sul routing di hub virtuali](about-virtual-hub-routing.md).
