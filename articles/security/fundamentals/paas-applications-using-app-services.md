---
title: Protezione di PaaS Web & applicazioni per dispositivi mobili
titleSuffix: Azure App Service
description: 'Informazioni sulle procedure consigliate della sicurezza di Servizio app di Azure per la protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: e583bb1b9cad79ee3e48e838d81d6a504c44f72c
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408091"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Procedure consigliate per la protezione delle applicazioni Web e per dispositivi mobili in PaaS mediante Servizio app di Azure

In questo articolo viene illustrato un insieme di procedure consigliate di [Servizio App di Azure](../../app-service/overview.md) per la protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili. Le procedure consigliate si basano sull'esperienza di tecnici e clienti con Azure.

Servizio app di Azure è una soluzione PaaS (piattaforma distribuita come servizio) che consente di creare applicazioni Web e per dispositivi mobili per qualsiasi piattaforma o dispositivo e di connettersi ai dati ovunque, nel cloud o in locale. Il servizio app include le funzionalità Web e per dispositivi mobili prima fornite separatamente come Siti Web di Azure e Servizi mobili di Azure. Include anche nuove funzionalità per l'automazione dei processi aziendali e l'hosting di API cloud. Il servizio app è un singolo servizio integrato che offre un set completo di funzionalità per scenari Web, per dispositivi mobili e di integrazione.

## <a name="authenticate-through-azure-active-directory-ad"></a>Eseguire l'autenticazione tramite Azure Active Directory (AD)
Il servizio app fornisce un servizio OAuth 2.0 per il provider di identità. OAuth 2.0 è incentrato sulla semplicità di sviluppo client fornendo i flussi di autorizzazione specifici per le applicazioni Web, applicazioni desktop e telefoni cellulari. Azure AD usa OAuth 2.0 per consentire all'utente di autorizzare l'accesso alle applicazioni per dispositivi mobili e alle applicazioni Web. Per altre informazioni, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Limitare l'accesso in base al ruolo
La limitazione degli accessi è fondamentale per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati. Usare il controllo degli accessi in base al ruolo per assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito, ad esempio la necessità di conoscere e privilegiare i principi di sicurezza. Per altre informazioni su come concedere agli utenti l'accesso alle applicazioni, vedere [che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Proteggere le chiavi.
Non importa quanto è valida la sicurezza impostata se si perdono le chiavi di sottoscrizione. L'insieme di credenziali delle chiavi di Azure consente di proteggere le chiavi e i segreti di crittografia usati da servizi e applicazioni cloud. Con Key Vault è possibile crittografare chiavi e segreti (ad esempio, chiavi di autenticazione, chiavi dell'account di archiviazione, chiavi di crittografia dati, file PFX e password) usando chiavi protette da moduli di protezione hardware (HSM). Per una maggiore sicurezza, è possibile importare o generare le chiavi in moduli di protezione hardware. È anche possibile utilizzare Azure Key Vault per gestire i certificati TLS con il rinnovo automatico. Per altre informazioni, vedere [Che cos'è Azure Key Vault?](../../key-vault/general/overview.md)

## <a name="restrict-incoming-source-ip-addresses"></a>Limitare gli indirizzi IP di origine in ingresso
[Ambiente del servizio app](../../app-service/environment/intro.md) ha una funzionalità di integrazione di rete virtuale che consente di limitare gli indirizzi IP di origine in ingresso tramite gruppi di sicurezza di rete (NSG). Se non si ha familiarità con le reti virtuali di Azure (VNET), si tratta di una funzionalità che consente di posizionare molte delle risorse di Azure in una rete instradabile non Internet di cui si controlla l'accesso. Per altre informazioni, vedere [integrare l'app con una rete virtuale di Azure](../../app-service/web-sites-integrate-with-vnet.md).

Per il servizio app in Windows, è anche possibile limitare dinamicamente gli indirizzi IP configurando il web.config. Per ulteriori informazioni, vedere [sicurezza dinamica degli indirizzi IP](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Passaggi successivi
Questo articolo descrive una serie di procedure consigliate per la protezione delle applicazioni Web PaaS e delle applicazioni per dispositivi mobili mediante il servizio app. Per ulteriori informazioni sulla protezione delle distribuzioni PaaS, vedere:

- [Protezione delle distribuzioni PaaS](paas-deployments.md)
- [Protezione di database PaaS in Azure](paas-applications-using-sql.md)