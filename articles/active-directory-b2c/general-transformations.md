---
title: Esempi di trasformazione generale delle attestazioni per i criteri personalizzati
titleSuffix: Azure AD B2C
description: Esempi di trasformazione generale delle attestazioni per lo schema Framework dell'esperienza (Identity Experience Framework) del Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 52831a1907d5ca8d13b0477c909d0d0358873973
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85202221"
---
# <a name="general-claims-transformations"></a>Trasformazioni delle attestazioni generali

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce esempi per l'uso delle trasformazioni delle attestazioni generali dello schema del Framework dell'esperienza di identità in Azure Active Directory B2C (Azure AD B2C). Per altre informazioni, vedere [ClaimsTransformations](claimstransformations.md).

## <a name="copyclaim"></a>CopyClaim

Copia il valore di un'attestazione in un altro. Entrambe le attestazioni devono essere dello stesso tipo.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringa, int | Tipo di attestazione da copiare. |
| OutputClaim | outputClaim | stringa, int | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. |

Usare questa trasformazione delle attestazioni per copiare un valore da un'attestazione stringa o numerica a un'altra attestazione. Nell'esempio seguente il valore dell'attestazione externalEmail viene copiato in attestazione posta elettronica.

```xml
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
    - **attestazione**: bob@contoso.com
- Attestazioni di output:
    - **outputClaim**: bob@contoso.com

## <a name="doesclaimexist"></a>DoesClaimExist

Controlla se **inputClaim** esiste o meno e imposta **outputClaim** su true o false di conseguenza.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Qualsiasi | L'attestazione di input di cui deve essere verificata l'esistenza. |
| OutputClaim | outputClaim | boolean | ClaimType generato dopo che è stata chiamata questa ClaimsTransformation. |

Usare questa trasformazione delle attestazioni per verificare se un'attestazione esiste o contiene un valore. Il valore restituito è un valore booleano che indica se l'attestazione esiste. L'esempio controlla se l'indirizzo di posta elettronica esiste.

```xml
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **attestazione**: someone@contoso.com
- Attestazioni di output:
  - **outputClaim**: true

## <a name="hash"></a>Hash

Eseguire l'hash del testo normale specificato usando il salt e un segreto. L'algoritmo hash usato è SHA-256.

| Elemento | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | string | L'attestazione di input da crittografare |
| InputClaim | salt | string | Il parametro salt. È possibile creare un valore casuale, usando le trasformazione delle attestazioni `CreateRandomString`. |
| InputParameter | randomizerSecret | string | Punta a una chiave di **criteri** di Azure ad B2C esistente. Per creare una nuova chiave dei criteri: nel tenant di Azure AD B2C, in **Gestisci** selezionare **Framework dell'esperienza** di gestione delle identità. Selezionare **chiavi dei criteri** per visualizzare le chiavi disponibili nel tenant. Selezionare **Aggiungi**. Selezionare **Manuale** in **Opzioni**. Specificare un nome (il prefisso *B2C_1A_* potrebbe essere aggiunto automaticamente.) Nella casella di testo **Secret** immettere il segreto da usare, ad esempio 1234567890. Per **Uso chiave** selezionare **Firma**. Selezionare **Create** (Crea). |
| OutputClaim | hash | string | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. L'attestazione configurata in `plaintext` inputClaim. |

```xml
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **testo normale**: MyPass@word1
  - **salt**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Attestazioni di output:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
