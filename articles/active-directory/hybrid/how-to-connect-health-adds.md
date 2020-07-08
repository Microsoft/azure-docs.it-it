---
title: Uso di Azure AD Connect Health con Active Directory Domain Services | Documentazione Microsoft
description: Questa è la pagina di Azure AD Connect Health che descrive come monitorare Servizi di dominio Active Directory.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 19e3cf15-f150-46a3-a10c-2990702cd700
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc7e8e3219cc2b11ed123889973c375edd22664d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359399"
---
# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Uso di Azure AD Connect Health con Servizi di dominio Active Directory
La documentazione seguente è specifica per il monitoraggio di Servizi di dominio Active Directory con Azure AD Connect Health. Versioni supportate di AD DS: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016.

Per altre informazioni sul monitoraggio di AD FS con Azure AD Connect Health, vedere [Uso di Azure AD Connect Health con AD FS](how-to-connect-health-adfs.md). Per informazioni sul monitoraggio di Azure Active Directory Connect (Sincronizzazione) con Azure AD Connect Health, vedere [Uso di Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md).

![Azure AD Connect Health per Servizi di dominio Active Directory](./media/how-to-connect-health-adds/domainservicesnapshot.PNG)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Avvisi di Azure AD Connect Health per Servizi di dominio Active Directory
La sezione Avvisi di Azure AD Connect Health per AD DS offre un elenco di avvisi attivi e risolti relativi ai controller di dominio. Se si seleziona un avviso attivo o risolto, viene visualizzato un nuovo pannello con altre informazioni, oltre ai passaggi per la risoluzione e a collegamenti alla documentazione di supporto. Ogni tipo di avviso può avere una o più istanze, che corrispondono a ogni controller di dominio interessato da quel particolare avviso. Nella parte inferiore del pannello degli avvisi è possibile fare doppio clic su un controller di dominio interessato per aprire un pannello aggiuntivo, che include altri dettagli sull'istanza dell'avviso.

In questo pannello è possibile abilitare le notifiche di posta elettronica per gli avvisi e modificare l'intervallo temporale visualizzato. Espandendo l'intervallo di tempo è possibile visualizzare gli avvisi risolti precedenti.

![Errore del servizio di sincronizzazione Azure AD Connect](./media/how-to-connect-health-adds/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>Dashboard dei controller di dominio
Questo dashboard offre una visualizzazione topologica dell'ambiente, oltre a metriche operative chiave e allo stato di integrità di ogni controller di dominio monitorato. Le metriche presentate consentono di identificare rapidamente eventuali controller di dominio che potrebbero richiedere altre indagini. Per impostazione predefinita, viene visualizzato solo un sottoinsieme di colonne. È tuttavia possibile visualizzare l'intero set di colonne disponibili facendo doppio clic sul comando corrispondente. Selezionando le colonne di maggiore interesse, il dashboard diventa una postazione centralizzata per la semplice visualizzazione dell'integrità dell'ambiente di AD DS.

![Controller di dominio](./media/how-to-connect-health-adds/aadconnect-health-adds-domainsandsites-dashboard.png)

I controller di dominio possono essere raggruppati in base al rispettivo dominio o sito. Ciò è utile per comprendere la topologia dell'ambiente. Se infine si fa doppio clic sull'intestazione del pannello, il dashboard viene ingrandito per usare tutto lo spazio disponibile sullo schermo. La visualizzazione più grande è utile quando vengono mostrate più colonne.

## <a name="replication-status-dashboard"></a>Dashboard Stato replica
Questo dashboard offre una visualizzazione dello stato e della topologia della replica dei controller di dominio monitorati. Viene indicato lo stato dell'ultimo tentativo di replica, insieme a documentazione utile per eventuali errori rilevati. È possibile fare doppio clic su un controller di dominio con un errore per aprire un nuovo pannello che include informazioni quali i dettagli sull'errore, le procedure consigliate per la risoluzione e i collegamenti alla documentazione per la risoluzione dei problemi.

![Stato replica](./media/how-to-connect-health-adds/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Monitoraggio
Questa funzionalità offre tendenze grafiche dei diversi contatori delle prestazioni, con dati costantemente raccolti da ogni controller di dominio monitorato. Le prestazioni di un controller di dominio possono essere facilmente confrontate con quelle di tutti gli altri controller di dominio monitorati nella foresta. È inoltre possibile visualizzare più contatori delle prestazioni affiancati; ciò è utile durante la risoluzione dei problemi nell'ambiente.

![Monitoraggio](./media/how-to-connect-health-adds/aadconnect-health-adds-monitoring.png)

Per impostazione predefinita sono selezionati quattro contatori delle prestazioni. È tuttavia possibile includerne altri facendo clic sul comando del filtro e selezionando o deselezionando qualsiasi contatore delle prestazioni. È anche possibile fare doppio clic su un grafico relativo ai contatori delle prestazioni per aprire un nuovo pannello, che include punti dati corrispondenti a ogni controller di dominio monitorato.

## <a name="related-links"></a>Collegamenti correlati
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Installazione dell'agente di Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operazioni di Azure AD Connect Health](how-to-connect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](how-to-connect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md)
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
* [Cronologia delle versioni di Azure AD Connect Health](reference-connect-health-version-history.md)

