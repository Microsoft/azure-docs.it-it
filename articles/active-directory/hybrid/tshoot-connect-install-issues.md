---
title: Risolvere i problemi di installazione di Azure AD Connect | Microsoft Docs
description: In questo argomento viene descritta la procedura per risolvere i problemi di installazione di Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75ad29c5b4a76de5317991995f132c6cb53bbe3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70211836"
---
# <a name="troubleshoot-azure-ad-connect-install-issues"></a>Risoluzione dei problemi: installazione di Azure AD Connect

## <a name="recommended-steps"></a>**Procedure consigliate**
Individuare il [tipo di installazione di Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation) adatto alle proprie esigenze. Se si soddisfano i criteri per l'installazione rapida, è consigliabile procedere con questo tipo di installazione. L'installazione rapida offre opzioni minime per completare l'installazione, pertanto è meno probabile che si verifichino problemi. 

Se tuttavia non si soddisfano i criteri per l'installazione rapida ed è necessario eseguire l'installazione personalizzata, di seguito vengono fornite alcune procedure consigliate che è possibile seguire per evitare problemi comuni. Per maggiore semplicità, vengono citate di seguito solo opzioni selettive:

* Assicurarsi di essere un amministratore nel computer in cui si installa AAD Connect. Accedere al computer con le credenziali di amministratore.

* Accettare tutte le opzioni predefinite nella pagina seguente, ad eccezione di "Usa un'istanza di SQL Server esistente", se si vuole usare l'istanza di SQL Server esistente. Ecco [altri dettagli](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom) su come usare opzioni di installazione personalizzate. 

    ![Usare un'istanza di SQL Server esistente](media/tshoot-connect-install-issues/tshoot-connect-install-issues/useexistingsqlserver.png)

* Nella pagina seguente selezionare l'opzione "Crea un nuovo account AD" per evitare problemi di autorizzazione con l'account esistente.

    ![Account della foresta AD](media/tshoot-connect-install-issues/tshoot-connect-install-issues/createnewaccount.png)

### <a name="common-issues"></a>**Problemi comuni**

* [Problemi di connettività con Active Directory locali](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-adconnectivitytools).

* [Problemi di connettività con Azure Active Directory online](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-connectivity).

* [Problemi di autorizzazione con Active Directory locali](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account).

## <a name="recommended-documents"></a>**Documenti consigliati**
* [Prerequisiti di Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-prerequisites)
* [Selezionare il tipo di installazione da usare per Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-select-installation)
* [Introduzione alle impostazioni rapide per Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-express)
* [Installazione personalizzata di Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-custom)
* [Azure AD Connect: eseguire l'aggiornamento da una versione precedente alla versione più recente](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version)
* [Azure AD Connect: che cos'è il server di staging?](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-topologies#staging-server)
* [Informazioni sul modulo ADConnectivityTool di PowerShell](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-adconnectivitytools)

## <a name="next-steps"></a>Passaggi successivi
- [Azure ad Connect sincronizzazione](how-to-connect-sync-whatis.md).
- [Informazioni sull'identità ibrida](whatis-hybrid-identity.md)





