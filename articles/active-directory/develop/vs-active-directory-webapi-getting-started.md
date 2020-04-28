---
title: Introduzione ad Azure AD nei progetti WebApi di Visual Studio
description: Come iniziare a utilizzare Azure Active Directory nei progetti WebApi dopo la connessione o la creazione ad Azure AD utilizzando i servizi relativi a Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 9a9f8aaa9e3f90e78668d2294ea9959b480e3384
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886076"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Introduzione ad Azure Active Directory (progetti WebAPI)

> [!div class="op_single_selector"]
> - [Per iniziare](vs-active-directory-webapi-getting-started.md)
> - [Cos'è successo](vs-active-directory-webapi-what-happened.md)

Questo articolo fornisce istruzioni aggiuntive da seguire dopo l'aggiunta di Active Directory a un progetto WebAPI ASP.NET tramite il comando **Progetto > Servizi connessi** di Visual Studio. Se il servizio non è ancora stato aggiunto al progetto, è possibile farlo in qualsiasi momento.

Vedere [Modifiche apportate al progetto WebAPI](vs-active-directory-webapi-what-happened.md) per informazioni sulle modifiche apportate al progetto quando si aggiunge il servizio connesso.

## <a name="requiring-authentication-to-access-controllers"></a>Richiesta di autenticazione ai controller di accesso

Tutti i controller del progetto sono dotati dell'attributo `[Authorize]`. Questo attributo richiede l'autenticazione dell'utente prima dell'accesso alle API definite dai controller. Per permettere l'accesso anonimo al controller, rimuovere l'attributo dal controller. Per configurare le autorizzazioni con un livello di granularità superiore, applicare l'attributo a ogni metodo che necessita di autorizzazione invece di applicarlo alla classe controller.

## <a name="next-steps"></a>Passaggi successivi

- [Scenari di autenticazione per Azure Active Directory](authentication-scenarios.md)
- [Aggiungere l'accesso con Microsoft a un'app Web ASP.NET](quickstart-v2-aspnet-webapp.md)
