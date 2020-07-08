---
title: Configurare l'iscrizione e l'accesso con un account LinkedIn
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account LinkedIn alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2f1099a3564a5891a69429d78bda8177094538e4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388018"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account LinkedIn tramite Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Creare un'applicazione su LinkedIn

Per usare un account LinkedIn come [provider di identità](authorization-code-flow.md) in Azure Active Directory B2C (Azure ad B2C), è necessario creare un'applicazione nel tenant che la rappresenta. Se non si dispone già di un account LinkedIn, è possibile iscriversi all'indirizzo [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Accedere al [sito Web di sviluppatori LinkedIn](https://www.developer.linkedin.com/) con le credenziali dell'account LinkedIn.
1. Selezionare **My Apps** (App personali) e quindi **Crea applicazione**.
1. Immettere **Nome società**, **Nome applicazione**, **Descrizione applicazione**, **Logo applicazione**, **Uso applicazione **, **URL del sito Web**, **Indirizzo di posta elettronica aziendale** e **Telefono (uff.)**.
1. Accettare il documento **LinkedIn API Terms of Use** (Condizioni d'uso dell'API LinkedIn) e fare clic su **Submit** (Invia).
1. Copiare i valori di **ID client** e **segreto client**. È possibile trovarli in **chiavi di autenticazione**. Sono necessari entrambi per configurare LinkedIn come provider di identità nel tenant. **Client Secret** è un'importante credenziale di sicurezza.
1. Immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` negli **URL di reindirizzamento autorizzati**. Sostituire `your-tenant-name` con il nome del tenant. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C. Fare clic su **Aggiungi** e quindi su **Aggiorna**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurare un account LinkedIn come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**e quindi fare clic su **LinkedIn**.
1. Immettere un **Nome**. Ad esempio, *LinkedIn*.
1. Per **ID client**, immettere l'ID client dell'applicazione LinkedIn creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.

## <a name="migration-from-v10-to-v20"></a>Migrazione dalla versione 1.0 alla versione 2.0

LinkedIn ha [aggiornato di recente le API dalla versione 1.0 alla versione 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Come parte della migrazione, Azure AD B2C è in grado di ottenere solo il nome completo dell'utente LinkedIn durante l'iscrizione. Se un indirizzo di posta elettronica è uno degli attributi raccolti durante l'iscrizione, l'utente deve immettere manualmente l'indirizzo di posta elettronica e convalidarlo.
