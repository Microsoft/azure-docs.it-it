---
title: Elenco di compatibilità di federazione di Azure AD
description: Questa pagina include provider di identità non Microsoft che possono essere usati per implementare l'accesso Single Sign-On.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3ca659535c225ca6736fe7bba8aa5b0120c684c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359824"
---
# <a name="azure-ad-federation-compatibility-list"></a>Elenco di compatibilità di federazione di Azure AD
Azure Active Directory offre la sicurezza avanzata dell'accesso alle applicazioni e Single Sign-On per Office 365 e altri Microsoft Online Services per implementazioni ibride o solo cloud senza richiedere soluzioni di terze parti. Analogamente alla maggior parte dei Microsoft Online Services, Office 365 è integrato con Azure Active Directory per i servizi di directory, l'autenticazione e l'autorizzazione. Azure Active Directory offre anche l'accesso Single Sign-On a migliaia di applicazioni SaaS e di applicazioni Web locali. Per le applicazioni SaaS supportate, vedere la [raccolta di applicazioni](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) Azure Active Directory. 

## <a name="idp-validation"></a>Convalida IDP
Se l'organizzazione usa una soluzione di federazione di terze parti, è possibile configurare Single Sign-On per gli utenti di Active Directory locale con Microsoft Online Services, ad esempio Office 365, purché la soluzione di federazione di terze parti sia compatibile con Azure Active Directory.  Per informazioni sulla compatibilità, contattare il provider di identità.  Per visualizzare un elenco di provider di identità già testati da Microsoft per verificarne la compatibilità con Azure AD, fare clic [qui](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Microsoft non fornisce più test di convalida ai provider di identità indipendenti per la compatibilità con Azure Active Directory. Per testare il prodotto per verificarne l'interoperabilità, vedere queste [linee guida](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Passaggi successivi

- [Integrare le directory locali con Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect e federazione](how-to-connect-fed-whatis.md)
