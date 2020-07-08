---
title: Configurare l'iscrizione e l'accesso con un account GitHub
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account GitHub alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba2441ae48c99d63ae637d2b80069058a04c5ef9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388188"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account GitHub tramite Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Creare un'applicazione OAuth per GitHub

Per usare un account GitHub come [provider di identità](authorization-code-flow.md) in Azure Active Directory B2C (Azure ad B2C), è necessario creare un'applicazione nel tenant che la rappresenta. Se non si ha già un account GitHub, è possibile iscriversi all'indirizzo [https://www.github.com/](https://www.github.com/) .

1. Accedere al sito Web [GitHub Developer](https://github.com/settings/developers) con le credenziali di GitHub.
1. Selezionare **OAuth Apps** (App OAuth) e **New OAuth App** (Nuova app OAuth).
1. Immettere **Application name** (Nome applicazione) e **Homepage URL** (URL della home page) personale.
1. Immettere il valore `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` in **Authorization callback URL** (URL callback di autorizzazione). Sostituire `your-tenant-name` con il nome del tenant di Azure AD B2C. Usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C.
1. Fare clic su **Registra applicazione**.
1. Copiare i valori di **ID client** e **segreto client**. Entrambi sono necessari per aggiungere il provider di identità nel tenant.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configurare un account GitHub come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**, quindi **GitHub (anteprima)**.
1. Immettere un **Nome**. Ad esempio, *GitHub*.
1. Per **ID client**, immettere l'ID client dell'applicazione GitHub creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.
