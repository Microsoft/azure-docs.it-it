---
title: ClaimsProviders - Azure Active Directory B2C | Microsoft Docs
description: Specificare l'elemento ClaimsProvider dei criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 846394266b981c14788148be465912b14bc1fb3e
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447912"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Una richiesta di attestazione è un'interfaccia per comunicare con tipi diversi di entità tramite i [profili tecnici](technicalprofiles.md). Ogni provider di attestazioni deve disporre di uno o più profili tecnici che determinano gli endpoint e i protocolli necessari per comunicare con il provider stesso. Un provider di attestazioni può avere più profili tecnici. Potrebbero essere definiti più profili tecnici, ad esempio, perché il provider di attestazioni supporta più protocolli o vari endpoint con funzionalità diverse oppure rilascia attestazioni diverse per livelli di controllo diversi. Potrebbe essere accettabile rilasciare attestazioni sensibili in un percorso utente, ma non in un altro.

Un percorso utente combina la chiamata ai profili tecnici tramite i passaggi dell'orchestrazione per definire la logica di business. 

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

**ClaimsProviders** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | Provider di attestazioni accreditato che può essere sfruttato in vari percorsi utente. |

## <a name="claimsprovider"></a>ClaimsProvider

**ClaimsProvider** contiene gli elementi figlio seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ---------- | ----------- |
| Dominio | 0:1 | Stringa contenente il nome di dominio per il provider di attestazioni. Se il provider di attestazioni include il profilo tecnico Facebook, ad esempio, il nome di dominio è Facebook.com. Questo nome di dominio viene usato per tutti i profili tecnici definiti nel provider di attestazioni a meno che il profilo tecnico non ne esegua l'override. Può essere fatto riferimento al nome di dominio anche in **domain_hint**. Per altre informazioni, vedere la sezione **Reindirizzare l'accesso a un provider di social networking** in [Configurare l'accesso diretto tramite Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | Stringa che contiene il nome del provider di attestazioni. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Set di profili tecnici supportato dal provider di attestazioni. |

**ClaimsProvider** organizza il modo in cui i profili tecnici sono correlati al provider di attestazioni. L'esempio seguente illustra il provider di attestazioni Azure Active Directory con i profili tecnici di Azure Active Directory:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

L'esempio seguente illustra il provider di attestazioni Facebook con il profilo tecnico **Facebook-OAUTH**.

```xml
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
