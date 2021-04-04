---
title: Suggerimenti per la sicurezza
description: Implementare le raccomandazioni sulla sicurezza per soddisfare gli obblighi di sicurezza come indicato nel modello di responsabilità condivisa. Migliorare la sicurezza dell'app.
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 09dbe0fbefb8b90b4c4e1ddef57abf3b13856183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148089"
---
# <a name="security-recommendations-for-app-service"></a>Raccomandazioni sulla sicurezza per il servizio app

Questo articolo contiene raccomandazioni sulla sicurezza per il servizio app Azure. L'implementazione di questi consigli consente di soddisfare gli obblighi di sicurezza come descritto nel modello di responsabilità condivisa e di migliorare la sicurezza complessiva per le soluzioni di app Web. Per altre informazioni sulle funzionalità offerte da Microsoft per soddisfare le responsabilità del provider di servizi, vedere [sicurezza dell'infrastruttura di Azure](../security/fundamentals/infrastructure.md).

## <a name="general"></a>Generale

| Recommendation | Commenti |
|-|-|----|
| Aggiornamenti | Usare le versioni più recenti delle piattaforme supportate, i linguaggi di programmazione, i protocolli e i Framework. |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Recommendation | Commenti |
|-|----|
| Disabilitare l'accesso anonimo | A meno che non sia necessario supportare richieste anonime, disabilitare l'accesso anonimo. Per ulteriori informazioni sulle opzioni di autenticazione del servizio app Azure, vedere [autenticazione e autorizzazione nel servizio app Azure](overview-authentication-authorization.md).|
| Richiedi autenticazione | Quando possibile, usare il modulo di autenticazione del servizio app anziché scrivere codice per gestire l'autenticazione e l'autorizzazione. Vedere [autenticazione e autorizzazione nel servizio app Azure](overview-authentication-authorization.md). |
| Proteggere le risorse back-end con l'accesso autenticato | È possibile usare l'identità dell'utente o usare un'identità dell'applicazione per eseguire l'autenticazione a una risorsa back-end. Quando si sceglie di usare un'identità dell'applicazione, usare un' [identità gestita](overview-managed-identity.md).
| Richiedi autenticazione del certificato client | L'autenticazione del certificato client migliora la sicurezza consentendo solo connessioni da client che possono eseguire l'autenticazione tramite certificati forniti dall'utente. |

## <a name="data-protection"></a>Protezione dei dati

| Recommendation | Commenti |
|-|-|
| Reindirizza HTTP a HTTPs | Per impostazione predefinita, i client possono connettersi alle app Web usando sia HTTP che HTTPS. Si consiglia di reindirizzare HTTP a HTTPs perché HTTPS usa il protocollo SSL/TLS per fornire una connessione protetta, crittografata e autenticata. |
| Crittografare la comunicazione con le risorse di Azure | Quando l'app si connette alle risorse di Azure, ad esempio il [database SQL](https://azure.microsoft.com/services/sql-database/) o [archiviazione di Azure](../storage/index.yml), la connessione rimane in Azure. Poiché la connessione passa attraverso la rete condivisa in Azure, è sempre necessario crittografare tutte le comunicazioni. |
| Richiedi la versione più recente di TLS possibile | Dal 2018 le nuove app di servizio app Azure usano TLS 1,2. Le versioni più recenti di TLS includono miglioramenti per la sicurezza rispetto alle versioni precedenti del protocollo. |
| USA FTPS | Il servizio app supporta FTP e FTPS per la distribuzione dei file. Quando possibile, utilizzare FTPS invece di FTP. Quando uno o entrambi questi protocolli non sono in uso, è necessario [disabilitarli](deploy-ftp.md#enforce-ftps). |
| Proteggere i dati dell'applicazione | Non archiviare i segreti dell'applicazione, ad esempio le credenziali del database, i token API o le chiavi private nel codice o nei file di configurazione. L'approccio comunemente accettato consiste nell'accedere ai segreti come [variabili di ambiente](https://wikipedia.org/wiki/Environment_variable) usando il modello standard nel linguaggio preferito. Nel servizio app Azure è possibile definire le variabili di ambiente tramite [le impostazioni dell'app e le](./configure-common.md) stringhe di [connessione](./configure-common.md). Le impostazioni dell'app e le stringhe di connessione vengono archiviate crittografate in Azure. Le impostazioni dell'app vengono decrittografate solo prima di essere inserite nella memoria del processo dell'app all'avvio dell'app. Le chiavi di crittografia vengono sottoposte a rotazione regolarmente. In alternativa, è possibile integrare l'app di servizio app Azure con [Azure Key Vault](../key-vault/index.yml) per la gestione avanzata dei segreti. [Accedendo all'insieme di credenziali delle chiavi con un'identità gestita](../key-vault/general/tutorial-net-create-vault-azure-web-app.md), l'app del Servizio app può accedere in modo sicuro ai segreti necessari. |

## <a name="networking"></a>Rete

| Recommendation | Commenti |
|-|-|
| USA restrizioni IP statiche | App Azure servizio in Windows consente di definire un elenco di indirizzi IP autorizzati ad accedere all'app. Questo elenco può includere singoli indirizzi IP o un intervallo di indirizzi IP definito da una subnet mask. Per altre informazioni, vedere [Restrizioni IP statico del Servizio app di Azure](app-service-ip-restrictions.md).  |
| Usa il piano tariffario isolato | Ad eccezione del piano tariffario isolato, tutti i livelli eseguono le app nell'infrastruttura di rete condivisa nel servizio app Azure. Il livello isolato ti offre l'isolamento completo della rete eseguendo le tue app all'interno di un [ambiente del servizio app](environment/intro.md)dedicato. Un ambiente del servizio app viene eseguito nell'istanza di [rete virtuale di Azure](../virtual-network/index.yml) dell'utente.|
| Usare connessioni sicure durante l'accesso alle risorse locali | È possibile usare le [connessioni ibride](app-service-hybrid-connections.md), l' [integrazione della rete virtuale](web-sites-integrate-with-vnet.md)o [l'ambiente del servizio app](environment/intro.md) per connettersi alle risorse locali. |
| Limita l'esposizione al traffico di rete in ingresso | I gruppi di sicurezza di rete consentono di limitare l'accesso alla rete e di controllare il numero di endpoint esposti. Per ulteriori informazioni, vedere [come controllare il traffico in ingresso in un ambiente del servizio app](environment/app-service-app-service-environment-control-inbound-traffic.md). |

## <a name="monitoring"></a>Monitoraggio

| Recommendation | Commenti |
|-|-|
|Usare il livello standard del Centro sicurezza di Azure | Il [Centro sicurezza di Azure](../security-center/defender-for-app-service-introduction.md) è integrato in modo nativo con app Azure servizio. Può eseguire valutazioni e fornire consigli sulla sicurezza. |

## <a name="next-steps"></a>Passaggi successivi

Verificare con il provider di applicazioni l'eventuale necessità di soddisfare requisiti di sicurezza aggiuntivi. Per altre informazioni sullo sviluppo di applicazioni sicure, vedere [Documentazione sullo sviluppo sicuro](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/).