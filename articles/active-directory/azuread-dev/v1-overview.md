---
title: Panoramica di Azure Active Directory per sviluppatori (v1.0)
description: Questo articolo offre una panoramica dell'accesso degli account Microsoft aziendali e dell'istituto di istruzione tramite la piattaforma e l'endpoint v1.0 di Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: overview
ms.workload: identity
ms.date: 10/24/2018
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 8cb426239fe9f3dea207df06b6f2bafbdb54058a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88117669"
---
# <a name="azure-active-directory-for-developers-v10-overview"></a>Panoramica di Azure Active Directory per sviluppatori (v1.0)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) è un servizio cloud di gestione delle identità che consente agli sviluppatori di creare app che permettono l'accesso sicuro agli utenti con un account Microsoft aziendale o dell'istituto di istruzione. Azure AD supporta gli sviluppatori che creano app line-of-business (LOB) a singolo tenant e gli sviluppatori che vogliono sviluppare app multi-tenant. Oltre all'accesso di base, Azure AD consente alle app di chiamare anche API Microsoft come [Microsoft Graph](/graph/overview) e API personalizzate create sulla piattaforma di Azure AD. Questa documentazione illustra come aggiungere il supporto di Azure AD all'applicazione tramite protocolli standard di settore, come OAuth2.0 e OpenID Connect.

> [!NOTE]
> La maggior parte dei contenuti di questa pagina è incentrata sulla piattaforma e sull'endpoint v1.0, in cui sono supportati solo account Microsoft aziendali o dell'istituto di istruzione. Se si vuole consentire l'accesso ad account Microsoft consumer o personali, vedere le informazioni su [piattaforma ed endpoint v2.0](../develop/v2-overview.md). L'endpoint v2.0 offre un'esperienza di sviluppo unificata per le app in cui si vuole consentire l'accesso a tutte le identità Microsoft.

- [Nozioni di base sull'autenticazione](v1-authentication-scenarios.md) Introduzione all'autenticazione con Azure AD.
- [Tipi di applicazioni](app-types.md) Panoramica degli scenari di autenticazione supportati da Azure AD.

## <a name="get-started"></a>Introduzione

Le esercitazioni e le guide introduttive per la versione 1.0 illustrano in modo dettagliato come creare un'app sulla piattaforma preferita tramite Azure AD Authentication Library (ADAL) SDK. Per iniziare, vedere le **guide introduttive per la versione 1.0** e le **esercitazioni pe la versione 1.0** in [Microsoft Identity Platform (Azure Active Directory per gli sviluppatori)](index.yml).

## <a name="how-to-guides"></a>Guide pratiche

Vedere le **guide alle procedure per la versione 1.0** per informazioni e procedure dettagliate relative alle attività più comuni in Azure AD.

## <a name="reference-topics"></a>Argomenti di riferimento

Gli articoli seguenti forniscono informazioni dettagliate su API, messaggi di protocollo e termini usati in Azure AD.

- [Librerie di autenticazione (ADAL)](active-directory-authentication-libraries.md) Panoramica delle librerie e degli SDK forniti da Azure AD.
- [Esempi di codice](sample-v1-code.md) Elenco di tutti gli esempi di codice di Azure AD.
- [Glossario](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) Terminologia e definizioni dei termini usati nella documentazione.

## <a name="videos"></a>Video

Vedere i [video sulla piattaforma per sviluppatori Azure Active Directory](videos.md) per informazioni sulla migrazione nella nuova piattaforma Microsoft Identity Platform.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]