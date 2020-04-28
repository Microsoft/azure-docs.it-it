---
title: Trasformazioni di attestazioni numero di telefono nei criteri personalizzati
titleSuffix: Azure AD B2C
description: Riferimento ai criteri personalizzati per le trasformazioni delle attestazioni con numero di telefono in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd26b2b475e293a1fda1b007289ba7c3eef35136
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183933"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Definire le trasformazioni delle attestazioni del numero di telefono in Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Questo articolo fornisce informazioni di riferimento ed esempi per l'uso delle trasformazioni delle attestazioni del numero di telefono dello schema del Framework dell'esperienza di identità in Azure Active Directory B2C (Azure AD B2C). Per ulteriori informazioni sulle trasformazioni delle attestazioni in generale, vedere [ClaimsTransformations](claimstransformations.md).

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

Converte un `phoneNumber` tipo di dati in `string` un tipo di dati.

| Item | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  Il ClaimType da convertire in una stringa. |
| OutputClaim | phoneNumberString | stringa | Elemento ClaimType generato dopo che è stata richiamata questa trasformazione di attestazioni. |

In questo esempio, l'attestazione cellPhoneNumber con un tipo di `phoneNumber` valore viene convertita in un'attestazione cellulare con un `string`tipo di valore.

```XML
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Esempio

- Attestazioni di input:
  - **PhoneNumber**: + 11234567890 (PhoneNumber)
- Attestazioni di output:
  - **phoneNumberString**: + 11234567890 (stringa)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Questa trasformazione attestazione convalida il formato del numero di telefono. Se il formato è valido, modificarlo in un formato standard usato da Azure AD B2C. Se il numero di telefono specificato non è in un formato valido, viene restituito un messaggio di errore.

| Item | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | stringa |  Attestazione di stringa per il numero di telefono. Il numero di telefono deve essere in formato internazionale, completo con una "+" e un codice paese iniziali. Se viene fornita `country` l'attestazione di input, il numero di telefono è in formato locale (senza il prefisso del paese). |
| InputClaim | country | stringa | Opzionale Attestazione di stringa per il codice paese del numero di telefono in formato ISO3166 (codice paese ISO-3166 a due lettere). |
| OutputClaim | outputClaim | phoneNumber | Risultato della trasformazione delle attestazioni. |

La trasformazione delle attestazioni **ConvertStringToPhoneNumberClaim** viene sempre eseguita da un [profilo tecnico di convalida](validation-technical-profile.md) chiamato da un [profilo tecnico autocertificato](self-asserted-technical-profile.md) o da un [controllo di visualizzazione](display-controls.md). I metadati del profilo tecnico autocertificato **UserMessageIfClaimsTransformationInvalidPhoneNumber** controllano il messaggio di errore visualizzato all'utente.

![Diagramma del percorso di esecuzione del messaggio di errore](./media/phone-authentication/assert-execution.png)

È possibile utilizzare questa trasformazione delle attestazioni per assicurarsi che l'attestazione stringa fornita sia un numero di telefono valido. In caso contrario, viene generato un messaggio di errore. L'esempio seguente verifica che il valore di **phoneString** ClaimType sia effettivamente un numero di telefono valido e quindi restituisce il numero di telefono nel formato Azure ad B2C standard. In caso contrario, viene generato un messaggio di errore.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Il profilo tecnico autocertificato che chiama il profilo tecnico di convalida che contiene questa trasformazione delle attestazioni può definire il messaggio di errore.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Esempio 1

- Attestazioni di input:
  - **phoneNumberString**: 033 456-7890
  - **paese**: dk
- Attestazioni di output:
  - **outputClaim**: + 450334567890

### <a name="example-2"></a>Esempio 2

- Attestazioni di input:
  - **phoneNumberString**: + 1 (123) 456-7890
- Attestazioni di output:
  - **outputClaim**: + 11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Questa operazione estrae il codice paese e il numero nazionale dall'attestazione di input e, facoltativamente, genera un'eccezione se il numero di telefono specificato non è valido.

| Item | TransformationClaimType | Tipo di dati | Note |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | stringa | Attestazione di stringa del numero di telefono. Il numero di telefono deve essere in formato internazionale, completo con una "+" e un codice paese iniziali. |
| InputParameter | throwExceptionOnFailure | boolean | Opzionale Parametro che indica se viene generata un'eccezione quando il numero di telefono non è valido. Il valore predefinito è False. |
| InputParameter | countryCodeType | stringa | Opzionale Parametro che indica il tipo di codice paese nell'attestazione di output. I valori disponibili sono **CallingCode** (il codice chiamante internazionale per un paese, ad esempio + 1) o **ISO3166** (il codice paese ISO-3166 a due lettere). |
| OutputClaim | nationalNumber | stringa | Attestazione di stringa per il numero nazionale del numero di telefono. |
| OutputClaim | countryCode | stringa | Attestazione di stringa per il codice paese del numero di telefono. |


Se la trasformazione delle attestazioni **GetNationalNumberAndCountryCodeFromPhoneNumberString** viene eseguita da un [profilo tecnico di convalida](validation-technical-profile.md) chiamato da un [profilo tecnico autocertificato](self-asserted-technical-profile.md) o da un' [azione di controllo di visualizzazione](display-controls.md#display-control-actions), i metadati del profilo tecnico autocertificato **UserMessageIfPhoneNumberParseFailure** controllano il messaggio di errore visualizzato all'utente.

![Diagramma del percorso di esecuzione del messaggio di errore](./media/phone-authentication/assert-execution.png)

È possibile utilizzare questa trasformazione delle attestazioni per suddividere un numero di telefono completo nel codice paese e il numero nazionale. Se il numero di telefono specificato non è valido, è possibile scegliere di generare un messaggio di errore.

Nell'esempio seguente si tenta di suddividere il numero di telefono in un numero nazionale e in un codice paese. Se il numero di telefono è valido, il numero di telefono verrà sostituito dal numero nazionale. Se il numero di telefono non è valido, non verrà generata un'eccezione e il numero di telefono avrà ancora un valore originale.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Il profilo tecnico autocertificato che chiama il profilo tecnico di convalida che contiene questa trasformazione delle attestazioni può definire il messaggio di errore.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Esempio 1

- Attestazioni di input:
  - **PhoneNumber**: + 49 (123) 456-7890
- Parametri di input:
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: ISO3166
- Attestazioni di output:
  - **nationalNumber**: 1234567890
  - **CountryCode**: de

### <a name="example-2"></a>Esempio 2

- Attestazioni di input:
  - **PhoneNumber**: + 49 (123) 456-7890
- Parametri di input
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: CallingCode
- Attestazioni di output:
  - **nationalNumber**: 1234567890
  - **CountryCode**: + 49
