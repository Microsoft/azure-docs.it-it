---
title: Controlli di sicurezza per il relay di Azure
description: Questo articolo fornisce un elenco di controllo dei controlli di sicurezza predefiniti per la valutazione del relay di Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 5d55026bfb6e3d6fe955a540b7596a85707398d6
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133345"
---
# <a name="security-controls-for-azure-relay"></a>Controlli di sicurezza per il relay di Azure

Questo articolo descrive i controlli di sicurezza incorporati nel servizio di inoltro di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Supporto per endpoint privati| No |  |   |
| Isolamento rete e supporto del firewall| No |  |   |
| Supporto del tunneling forzato| N/D | L'inoltro è il tunnel TLS  |   |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | |   |
| Piano di gestione e controllo - Registrazione e controllo| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml). |   |
| Piano dati - Registrazione e controllo| Sì | Connessione riuscita/non riuscita ed errori e registrato.  |   |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Authentication| Sì | Tramite SAS. | [Autenticazione e autorizzazione di Inoltro di Azure](relay-authentication-and-authorization.md) |
| Autorizzazione|  Sì | Tramite SAS. | [Autenticazione e autorizzazione di Inoltro di Azure](relay-authentication-and-authorization.md) |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft |  N/D | L'inoltro è un socket Web e non rende persistenti i dati. |   |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | No | USA solo certificati TLS Microsoft.  |   |
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D | |   |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Il servizio richiede TLS. |   |
| Chiamate API crittografate| Sì | HTTPS. |


## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml).|   |

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).