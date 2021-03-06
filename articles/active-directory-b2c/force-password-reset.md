---
title: Configurare un flusso di reimpostazione della password forzata in Azure AD B2C
titleSuffix: Azure AD B2C
description: Informazioni su come configurare un flusso di reimpostazione della password forzata in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7fac7df0978b23e535d8761b436b14e2f41e5f91
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209503"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Configurare un flusso di reimpostazione della password di forzatura in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

> [!IMPORTANT]
> Force reimpostazione password è una funzionalità di anteprima pubblica di Azure AD B2C. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Panoramica
In qualità di amministratore, è possibile [reimpostare la password di un utente](manage-users-portal.md#reset-a-users-password) nel caso in cui l'utente dimentichi la password. In alternativa, si desidera forzare la reimpostazione della password. In questo articolo si apprenderà come forzare la reimpostazione della password in questi scenari.

Quando un amministratore reimposta la password di un utente tramite la portale di Azure, il valore dell'attributo [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) è impostato su `true` . Il [percorso di accesso e iscrizione](add-sign-up-and-sign-in-policy.md) controlla il valore di questo attributo. Dopo che l'utente ha completato l'accesso, se l'attributo è impostato su `true` , l'utente deve reimpostare la password. Il valore dell'attributo viene quindi impostato su Back `false` .

![Forzare il flusso di reimpostazione della password](./media/force-password-reset/force-password-reset-flow.png)

Il flusso di reimpostazione della password è applicabile agli account locali in Azure AD B2C che usano un [indirizzo di posta elettronica](identity-provider-local.md#email-sign-in) o un [nome utente](identity-provider-local.md#username-sign-in) con una password per l'accesso.

::: zone pivot="b2c-user-flow"

### <a name="force-a-password-reset-after-90-days"></a>Forza la reimpostazione della password dopo 90 giorni

In qualità di amministratore, è possibile impostare la scadenza della password di un utente su 90 giorni, usando [MS Graph](microsoft-graph-operations.md). Dopo 90 giorni, il valore dell'attributo [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) viene impostato automaticamente su `true` . Per altre informazioni su come impostare i criteri di scadenza delle password di un utente, vedere [attributo criteri password](user-profile-attributes.md#password-policy-attribute).

Una volta impostati i criteri di scadenza della password, è necessario configurare anche il flusso di reimpostazione della password forzata, come descritto in questo articolo.  

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>Configurare i criteri

Per abilitare l'impostazione di **reimpostazione della password forzata** in un flusso utente di iscrizione o accesso:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Selezionare il flusso di iscrizione e accesso o l'utente di accesso (di tipo **consigliato**) che si desidera personalizzare.
1. Nel menu a sinistra in **Impostazioni** selezionare **Proprietà**.
1. In **complessità password** selezionare **reimpostazione password forzata**.
1. Selezionare **Salva**.

### <a name="test-the-user-flow"></a>Testare il flusso utente

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore utente o password. Per ulteriori informazioni sui ruoli disponibili, vedere [assegnazione di ruoli di amministratore in Azure Active Directory](../active-directory/roles/permissions-reference.md#all-roles).
1. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Utenti**. Cercare e selezionare l'utente che verrà usato per testare la reimpostazione della password e quindi selezionare **Reimposta password**.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Flussi utente**.
1. Selezionare un flusso utente di iscrizione o di accesso (di tipo **consigliato**) che si desidera testare.
1. Selezionare **Esegui il flusso utente**.
1. Per **Applicazione** selezionare l'applicazione Web denominata *webapp1* che è stata registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare **Esegui il flusso utente**.
1. Accedere con l'account utente per il quale si reimposta la password.
1. È ora necessario modificare la password per l'utente. Cambiare la password e selezionare **Continua**. Il token viene restituito a `https://jwt.ms` e dovrebbe essere visualizzato.

::: zone-end

::: zone pivot="b2c-custom-policy"

Questa funzionalità è attualmente disponibile solo per Flussi utente. Per la procedura di installazione, scegliere **flusso utente** sopra.

::: zone-end

## <a name="next-steps"></a>Passaggi successivi

Configurare una [reimpostazione della password self-service](add-password-reset-policy.md).
