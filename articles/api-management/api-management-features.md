---
title: Confronto basato sulle funzionalità dei livelli di Gestione API di Azure | Microsoft Docs
description: Confrontare API Management livelli in base alle funzionalità offerte. Vedere una tabella che riepiloga le funzionalità principali disponibili in ogni piano tariffario.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: f111729d7d7707ed4f40ce8f89ce76975fb47400
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536443"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Confronto basato sulle funzionalità dei livelli di Gestione API di Azure

Ogni [piano tariffario](https://aka.ms/apimpricing) di Gestione API offre un set diverso di funzionalità e di[ capacità](api-management-capacity.md) per unità. La tabella seguente riepiloga le funzionalità chiave disponibili in ciascuno dei livelli. Alcune funzionalità potrebbero funzionare diversamente o avere capacità diverse a seconda del livello. In questi casi le differenze vengono indicate negli articoli della documentazione, i quali descrivono le singole funzionalità.

> [!IMPORTANT]
> Si noti che il livello Developer è per casi d'uso e valutazioni non di produzione. Non offre un contratto di servizio.

| Funzionalità                                                                                      | Consumo | Sviluppatore | Basic | Standard | Premium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Integrazione di Azure AD<sup>1</sup>                                                             | No          | Sì       | No    | Sì      | Sì     |
| Supporto della Rete virtuale di Microsoft Azure (VNet)                                                               | No          | Sì       | No    | No       | Sì     |
| Distribuzione in più aree                                                                      | No          | No        | No    | No       | Sì     |
| Zone di disponibilità                                                                           | No          | No        | No    | No       | Sì     |
| Più nomi di dominio personalizzati                                                                 | No          | Sì        | No    | No       | Sì     |
| Portale per sviluppatori<sup>2</sup>                                                                 | No          | Sì       | Sì   | Sì      | Sì     |
| Cache incorporata                                                                               | No          | Sì       | Sì   | Sì      | Sì     |
| Analisi incorporata                                                                           | No          | Sì       | Sì   | Sì      | Sì     |
| [Gateway self-hosted](self-hosted-gateway-overview.md)<sup>3</sup>                           | No          | Sì       | No    | No       | Sì     |
| [Impostazioni di TLS](api-management-howto-manage-protocols-ciphers.md)                             | Sì         | Sì       | Sì   | Sì      | Sì     |
| [Cache esterna](./api-management-howto-cache-external.md)                                                    | Sì         | Sì       | Sì   | Sì      | Sì     |
| [Autenticazione con certificato client](api-management-howto-mutual-certificates-for-clients.md) | Sì         | Sì       | Sì   | Sì      | Sì     |
| [Backup e ripristino](api-management-howto-disaster-recovery-backup-restore.md)               | No          | Sì       | Sì   | Sì      | Sì     |
| [Gestione tramite Git](api-management-configuration-repository-git.md)                        | No          | Sì       | Sì   | Sì      | Sì     |
| API di gestione diretta                                                                        | No          | Sì       | Sì   | Sì      | Sì     |
| Metriche e log di Monitoraggio di Azure                                                               | No          | Sì       | Sì   | Sì      | Sì     |
| IP statico                                                                                    | No          | Sì       | Sì   | Sì      | Sì     |

<sup>1</sup> Abilita l'uso di Azure AD (e Azure AD B2C) come provider di identità per l'accesso utente nel portale per sviluppatori.<br/>
<sup>2</sup> Sono incluse le funzionalità correlate, ad esempio utenti, gruppi, problemi, modelli di posta elettronica e applicazioni, nonché notifiche.<br/>
<sup>3</sup> Nel livello Developer i gateway self-hosted sono limitati a un singolo nodo del gateway.<br/>