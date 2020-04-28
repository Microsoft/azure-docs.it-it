---
title: Controlli di sicurezza
description: Trovare un elenco di controllo dei controlli di sicurezza per la valutazione del servizio app Azure per la propria organizzazione.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74671442"
---
# <a name="security-controls-for-azure-app-service"></a>Controlli di sicurezza per il servizio app Azure

Questo articolo descrive i controlli di sicurezza incorporati nel servizio app Azure.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto per endpoint di servizio| Sì | Disponibile per il servizio app.| [Restrizioni di accesso al servizio app Azure](app-service-ip-restrictions.md)
| Supporto di VNet Injection| Sì | Gli ambienti del servizio app sono implementazioni private del servizio App dedicato a un singolo cliente inserito nella rete virtuale di un cliente. | [Introduzione agli ambienti del servizio app](environment/intro.md)
| Isolamento rete e supporto del firewall| Sì | Per la variante pubblica multi-tenant del servizio app, i clienti possono configurare gli ACL di rete (restrizioni IP) per bloccare il traffico in ingresso consentito.  Gli ambienti del servizio app vengono distribuiti direttamente nelle reti virtuali e possono quindi essere protetti con gruppi. | [Restrizioni di accesso al servizio app Azure](app-service-ip-restrictions.md)
| Supporto del tunneling forzato| Sì | Gli ambienti del servizio app possono essere distribuiti nella rete virtuale di un cliente in cui è configurato il tunneling forzato. | [Configurare Ambiente del servizio app con il tunneling forzato](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Il servizio app si integra con Application Insights per le lingue che supportano Application Insights (Full .NET Framework, .NET Core, Java e node. JS).  Vedere [monitorare le prestazioni del servizio app Azure](../azure-monitor/app/azure-web-apps.md). Il servizio app invia anche le metriche dell'applicazione in monitoraggio di Azure. | [Monitorare le app nel servizio app Azure](web-sites-monitor.md)
| Registrazione e controllo del piano di gestione e controllo| Sì | Tutte le operazioni di gestione eseguite sugli oggetti del servizio app si verificano tramite [Azure Resource Manager](../azure-resource-manager/index.yml). I log cronologici di queste operazioni sono disponibili sia nel portale che tramite l'interfaccia della riga di comando. | [Operazioni del provider di risorse Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb), [AZ monitor Activity-log](/cli/azure/monitor/activity-log)
| Registrazione e controllo del piano dati | No | Il piano dati per il servizio app è una condivisione file remota che contiene il contenuto del sito Web distribuito del cliente.  Nessun controllo della condivisione file remota. |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note |  Documentazione
|---|---|--|
| Autenticazione| Sì | I clienti possono compilare applicazioni nel servizio app che si integrano automaticamente con [Azure Active Directory (Azure ad)](../active-directory/index.yml) e altri provider di identità compatibili con OAuth per l'accesso di gestione alle risorse del servizio app. tutti gli accessi sono controllati da una combinazione di Azure ad entità autenticata e di Azure Resource Manager ruoli RBAC. | [Autenticazione e autorizzazione nel servizio app di Azure](overview-authentication-authorization.md)
| Autorizzazione| Sì | Per la gestione dell'accesso alle risorse del servizio app, tutti gli accessi sono controllati da una combinazione di Azure AD entità autenticata e Azure Resource Manager ruoli RBAC.  | [Autenticazione e autorizzazione nel servizio app di Azure](overview-authentication-authorization.md)

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | Sì | Il contenuto del file di sito Web viene archiviato in archiviazione di Azure, che crittografa automaticamente il contenuto inattivo. <br><br>I segreti forniti dal cliente vengono crittografati a riposo. I segreti vengono crittografati a riposo durante l'archiviazione nei database di configurazione del servizio app.<br><br>I dischi collegati localmente possono essere facoltativamente usati come archiviazione temporanea da siti Web (D:\Local e% TMP%). I dischi collegati localmente non sono crittografati. | [Crittografia di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | Sì | I clienti possono scegliere di archiviare i segreti dell'applicazione in Key Vault e recuperarli in fase di esecuzione. | [Usare i riferimenti a Key Vault per Servizio app e Funzioni di Azure (anteprima)](app-service-key-vault-references.md)
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | I clienti possono configurare i siti Web in modo da richiedere e usare HTTPS per il traffico in ingresso.  | [Come creare solo un servizio di app Azure HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (post di Blog)
| Chiamate API crittografate| Sì | Le chiamate di gestione per configurare il servizio app si verificano tramite [Azure Resource Manager](../azure-resource-manager/index.yml) chiamate su HTTPS. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note | Documentazione
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Per le operazioni di gestione, lo stato di una configurazione del servizio app può essere esportato come modello di Azure Resource Manager e con controllo delle versioni nel tempo. Per le operazioni di runtime, i clienti possono gestire più versioni live di un'applicazione usando la funzionalità slot di distribuzione del servizio app. | 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
