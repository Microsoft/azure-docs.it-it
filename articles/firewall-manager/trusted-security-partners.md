---
title: Che cosa sono i provider del partner di sicurezza di Azure Firewall Manager?
description: Informazioni sui provider di partner di sicurezza di Azure Firewall Manager
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 34da82510f96ef7bde65ceec397b048c941e3234
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563614"
---
# <a name="what-are-security-partner-providers"></a>Che cosa sono i provider del partner di sicurezza?

I *provider di partner di sicurezza* in Azure Firewall Manager consentono di usare le proprie offerte di sicurezza come servizio (secar) di terze parti per proteggere l'accesso a Internet per gli utenti.

Con una configurazione rapida, è possibile proteggere un hub con un partner di sicurezza supportato e indirizzare e filtrare il traffico Internet dalle reti virtuali (reti virtuali) o dalle sedi delle succursali all'interno di un'area. Questa operazione può essere eseguita con la gestione automatica delle route senza configurare e gestire route definite dall'utente (UDR).

Puoi distribuire Hub protetti configurati con il partner di sicurezza che preferisci in più aree di Azure per ottenere connettività e sicurezza per gli utenti in qualsiasi punto del mondo in tali aree. Grazie alla possibilità di usare l'offerta del partner di sicurezza per il traffico delle applicazioni Internet/SaaS e il firewall di Azure per il traffico privato negli hub protetti, ora puoi iniziare a creare il tuo perimetro di sicurezza in Azure, che è vicino a utenti e applicazioni distribuiti a livello globale.

I partner di sicurezza supportati sono **ZScaler**, **Check Point** (Preview) e **iboss** (Preview).

![Provider del partner di sicurezza](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Scenari principali

È possibile usare i partner di sicurezza per filtrare il traffico Internet negli scenari seguenti:

- Da rete virtuale (VNet) a Internet

   Sfruttare la protezione Internet avanzata in grado di riconoscere l'utente per i carichi di lavoro su cloud in esecuzione in Azure.

- Da ramo a Internet

   Sfrutta la tua connettività di Azure e la tua distribuzione globale per aggiungere con facilità il filtro NSaaS di terze parti per gli scenari da ramo a Internet. È possibile creare la rete di transito globale e il perimetro di sicurezza usando la rete WAN virtuale di Azure.

Sono supportati gli scenari indicati di seguito:
- VNet/ramo a Internet tramite un provider del partner di sicurezza e l'altro traffico (spoke to spoke, spoke to Branch, branch to spoke) tramite il firewall di Azure.
- VNet/ramo a Internet tramite il provider del partner di sicurezza

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Procedure consigliate per il filtraggio del traffico Internet in hub virtuali protetti

Il traffico Internet include in genere il traffico Web. Include anche il traffico destinato ad applicazioni SaaS come Office 365 (O365) e servizi PaaS pubblici di Azure, ad esempio archiviazione di Azure, Azure SQL e così via. Di seguito sono illustrate le procedure consigliate per la gestione del traffico a questi servizi:

### <a name="handling-azure-paas-traffic"></a>Gestione del traffico PaaS di Azure
 
- Usare il firewall di Azure per la protezione se il traffico è costituito principalmente da Azure PaaS e l'accesso alle risorse per le applicazioni può essere filtrato usando indirizzi IP, nomi di dominio completi, tag di servizio o tag FQDN.

- Usare una soluzione di partner di terze parti negli hub se il traffico è costituito da accesso alle applicazioni SaaS o se è necessario un filtro compatibile con l'utente (ad esempio per i carichi di lavoro dell'infrastruttura VDI) oppure sono necessarie funzionalità di filtro Internet avanzate.

![Tutti gli scenari per gestione firewall di Azure](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Gestione del traffico di Office 365 (O365)

Negli scenari di distribuzione di Branch distribuiti a livello globale, è consigliabile reindirizzare il traffico di Office 365 direttamente al ramo prima di inviare il traffico Internet rimanente nell'hub protetto di Azure.

Per Office 365, la latenza di rete e le prestazioni sono fondamentali per un'esperienza utente corretta. Per raggiungere questi obiettivi in termini di prestazioni ottimali e di esperienza utente, i clienti devono implementare Office 365 Direct e local escape prima di prendere in considerazione il routing del resto del traffico Internet tramite Azure.

I [principi di connettività di rete di office 365](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) chiamano per le connessioni di rete key Office 365 da indirizzare localmente dal ramo utente o dal dispositivo mobile e direttamente tramite Internet al punto di presenza più vicino di rete Microsoft.

Inoltre, le connessioni di Office 365 vengono crittografate per la privacy e usano protocolli proprietari efficienti per motivi di prestazioni. In questo modo è impraticabile che influisca negativamente sulle connessioni alle tradizionali soluzioni di sicurezza a livello di rete. Per questi motivi si consiglia vivamente ai clienti di inviare il traffico di Office 365 direttamente dai rami, prima di inviare il resto del traffico tramite Azure. Microsoft ha collaborato con diversi provider di soluzioni SD-WAN, che si integrano con Azure e Office 365 e semplificano la possibilità per i clienti di abilitare Office 365 Direct e Internet breakout locale. Per informazioni dettagliate, vedere [ricerca per categorie impostare i criteri O365 tramite la rete WAN virtuale?](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview)

## <a name="next-steps"></a>Passaggi successivi

[Distribuire un'offerta partner di sicurezza in un hub protetto usando gestione firewall di Azure](deploy-trusted-security-partner.md).
