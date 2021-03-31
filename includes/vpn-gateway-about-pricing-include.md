---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/08/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8f8d366961049deb3eda193718ccb553aac930e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "67666361"
---
Si pagano due cose: i costi di calcolo orari per il gateway di rete virtuale e il trasferimento dei dati in uscita dal gateway di rete virtuale. Le informazioni sui prezzi sono disponibili nella pagina [Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway) . Per informazioni sui prezzi degli SKU di gateway legacy, vedere la [pagina dei prezzi di ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) e scorrere fino alla sezione **Gateway di rete virtuale**.

**Costi di calcolo per il gateway di rete virtuale**<br>Ogni gateway di rete virtuale ha un costo di calcolo orario. Il prezzo dipende dallo SKU del gateway specificato quando si crea un gateway di rete virtuale. Il costo è relativo al gateway in sé e va aggiunto al trasferimento dei dati che passano dal gateway. Il costo delle configurazioni attiva/attiva e attiva-passiva è equivalente.

**Costi di trasferimento dati**<br>I costi per il trasferimento dei dati vengono calcolati in base al traffico in uscita dal gateway di rete virtuale di origine.

* Se si invia il traffico al dispositivo VPN locale, i costi verranno addebitati insieme alla tariffa per il trasferimento dei dati in uscita da Internet.
* Se si invia il traffico tra reti virtuali in aree diverse, i prezzi dipendono dall'area.
* Se si invia il traffico solo tra reti virtuali nella stessa area, non sono previsti costi per i dati. Il traffico tra reti virtuali nella stessa area è gratuito.
