---
title: Configurare la disponibilità elevata-iperscala (CITUS)-database di Azure per PostgreSQL
description: Come abilitare o disabilitare la disponibilità elevata
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: 0c7702c8832e22d889a5d785dad845430bfb7d17
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116880"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Configurare la disponibilità elevata di CITUS (iperscale)

Database di Azure per PostgreSQL-iperscalabilità (CITUS) fornisce disponibilità elevata (HA) per evitare tempi di inattività del database. Con la disponibilità elevata abilitata, ogni nodo in un gruppo di server riceverà un standby. Se il nodo originale diventa non integro, il relativo standby verrà promosso per sostituirlo.

> [!IMPORTANT]
> Poiché HA raddoppiato il numero di server nel gruppo, il costo sarà anche doppio.

È possibile abilitare la disponibilità elevata durante la creazione del gruppo di server o successivamente nella scheda **Configura** del gruppo di server nel portale di Azure. L'interfaccia utente è simile in entrambi i casi. Trascinare il dispositivo di scorrimento per la **disponibilità elevata** su Sì:

![dispositivo di scorrimento a disponibilità elevata](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Fare clic sul pulsante **Salva** per applicare la selezione. L'abilitazione di disponibilità elevata può richiedere del tempo quando il gruppo di server effettua il provisioning dei dati in standby e li trasmette.

Nella scheda **Panoramica** per il gruppo di server vengono elencati tutti i nodi e i relativi standby, insieme a una colonna a **disponibilità elevata** che indica se la disponibilità elevata è stata abilitata per ogni nodo.

![Panoramica della colonna a disponibilità elevata in gruppo di server](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [disponibilità elevata](concepts-hyperscale-high-availability.md).
