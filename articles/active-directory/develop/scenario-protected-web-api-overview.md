---
title: API Web protetta-Panoramica
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'API Web protetta (panoramica).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537203"
---
# <a name="scenario-protected-web-api"></a>Scenario: API Web protetta

In questo scenario viene illustrato come esporre un'API Web. Si apprenderà anche come proteggere l'API Web in modo che solo gli utenti autenticati possano accedervi.

Per usare l'API Web, è necessario abilitare gli utenti autenticati con account aziendali o dell'Istituto di istruzione o abilitare account personali Microsoft.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Specifiche

Di seguito sono riportate informazioni specifiche che è necessario tenere presente per proteggere le API Web:

- La registrazione dell'app deve esporre almeno un ambito. La versione del token accettata dall'API Web dipende dai destinatari dell'accesso.
- La configurazione del codice per l'API Web deve convalidare il token usato quando viene chiamata l'API Web.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Registrazione dell'app](scenario-protected-web-api-app-registration.md)
