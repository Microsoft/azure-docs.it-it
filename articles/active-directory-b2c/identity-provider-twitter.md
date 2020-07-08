---
title: Configurare l'iscrizione e l'accesso con un account Twitter
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account Twitter alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e07be01a0fb6d74b4dcef5cbc6ec129f95fd2e7d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387933"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Twitter tramite Azure Active Directory B2C

## <a name="create-an-application"></a>Creare un'applicazione

Per usare Twitter come provider di identità in Azure AD B2C, è necessario creare un'applicazione Twitter. Se non si ha già un account Twitter, è possibile iscriversi all'indirizzo [https://twitter.com/signup](https://twitter.com/signup) .

1. Accedere al [sito Web di sviluppatori Twitter](https://developer.twitter.com/en/apps) con le credenziali dell'account Twitter.
1. Selezionare **Crea un'app**.
1. Immettere un ** Nome app** e una **Descrizione applicazione**.
1. In **URL del sito Web**, immettere `https://your-tenant.b2clogin.com`. Sostituire `your-tenant` con il nome del tenant. Ad esempio: `https://contosob2c.b2clogin.com`.
1. Per **URL callback** immettere `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Sostituire `your-tenant` con il nome del nome tenant e `your-user-flow-Id` con l'identificatore del flusso utente. Ad esempio: `b2c_1A_signup_signin_twitter`. È necessario usare tutte le lettere minuscole quando si immette il nome del tenant e l'ID del flusso utente anche se sono definiti con lettere maiuscole in Azure AD B2C.
1. Nella parte inferiore della pagina leggere e accettare le condizioni, quindi fare clic su **Crea**.
1. Nella pagina **Dettagli app**, selezionare **Modifica > Modifica dettagli**, selezionare la casella **Enable Sign in with Twitter** (Consenti accesso con Twitter) e quindi selezionare **Salva**.
1. Selezionare **Keys and tokens** (Chiavi e token) e registrare i valori **Chiave API Consumer** e **Consumer API secret key** (Chiave segreto API Consumer) da usare in un secondo momento.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurare Twitter come provider di identità nel tenant,

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**e quindi selezionare **Twitter**.
1. Immettere un **Nome**. Ad esempio *Twitter*.
1. Per **ID client**, immettere la chiave API del consumer dell'applicazione Twitter creata in precedenza.
1. Per il **segreto client**, immettere la chiave privata dell'API utente registrata.
1. Selezionare **Salva**.
