---
title: Controlli di sicurezza per la messaggistica del bus di servizio di Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione della messaggistica del bus di servizio di Azure
services: service-bus-messaging
ms.service: service-bus-messaging
author: spelluru
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: spelluru
ms.openlocfilehash: af119ef026b70fcb4a56b4f823d20c0e9eddddc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75903260"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Controlli di sicurezza per la messaggistica del bus di servizio di Azure

Questo articolo descrive i controlli di sicurezza incorporati nella messaggistica del bus di servizio di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Supporto per endpoint di servizio| Sì (solo livello Premium) | Gli endpoint del servizio VNet sono supportati solo per il [livello Premium del bus di servizio](service-bus-premium-messaging.md) . |  |
| Supporto di VNet Injection| No | |  |
| Isolamento rete e supporto del firewall| Sì (solo livello Premium) |  |  |
| Supporto del tunneling forzato| No |  |  |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Supportato tramite [monitoraggio e avvisi di Azure](service-bus-metrics-azure-monitor.md). |  |
| Registrazione e controllo del piano di gestione e controllo| Sì | I log delle operazioni sono disponibili.  | [Log di diagnostica del bus di servizio](service-bus-diagnostic-logs.md) |
| Registrazione e controllo del piano dati| No |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Authentication| Sì | Gestito tramite [Azure Active Directory identità del servizio gestita](service-bus-managed-service-identity.md).| [Autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md). |
| Autorizzazione| Sì | Supporta l'autorizzazione tramite il controllo [RBAC](authenticate-application.md) e il token SAS. | [Autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server: chiavi gestite da Microsoft |  Sì per la crittografia lato server per impostazione predefinita. |  |  |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | Sì. | Una chiave gestita dal cliente nell'insieme di credenziali delle chiavi di Azure può essere usata per crittografare i dati nello spazio dei nomi del bus di servizio inattivi. | [Configurare chiavi gestite dal cliente per la crittografia dei dati del bus di servizio di Azure inattivi usando il portale di Azure](configure-customer-managed-key.md)  |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |   |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Supporta il meccanismo HTTPS/TLS standard. |   |
| Chiamate API crittografate| Sì | Le chiamate API vengono eseguite tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |   |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Supporta il controllo delle versioni del provider di risorse tramite l' [API Azure Resource Manager](/rest/api/resources/).|   |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
