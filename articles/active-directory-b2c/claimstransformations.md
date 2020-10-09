---
title: ClaimsTransformations - Azure Active Directory B2C | Microsoft Docs
description: Definizione dell'elemento ClaimsTransformations nello schema del framework dell'esperienza di gestione delle identità di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d9bd78a80ac52d2a790537bf47e33807720349
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202960"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L'elemento **ClaimsTransformations** contiene un elenco di funzioni di trasformazione delle attestazioni che può essere usato nei percorsi utente come parte di [criteri personalizzati](custom-policy-overview.md). Una trasformazione di attestazioni converte una determinata attestazione in un'altra. Nella trasformazione delle attestazioni viene specificare il metodo di trasformazione, ad esempio l'aggiunta di un elemento a una raccolta di stringhe o la modifica delle maiuscole/minuscole di una stringa.

Per includere l'elenco di funzioni di trasformazione delle attestazioni che possono essere usate nei percorsi di utente, un elemento XML ClaimsTransformations deve essere dichiarato nella sezione BuildingBlocks dei criteri.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

L'elemento **ClaimsTransformation** contiene gli attributi seguenti:

| Attributo |Obbligatoria | Descrizione |
| --------- |-------- | ----------- |
| ID |Sì | Identificatore usato per identificare in modo univoco la trasformazione delle attestazioni. Viene fatto riferimento all'identificatore da altri elementi XML nei criteri. |
| TransformationMethod | Sì | Il metodo di trasformazione da usare nella trasformazione delle attestazioni. Ogni trasformazione delle attestazioni ha i propri valori. Vedere i [riferimenti delle trasformazioni di attestazioni](#claims-transformations-reference) per un elenco completo dei valori disponibili. |

## <a name="claimstransformation"></a>ClaimsTransformation

L'elemento **ClaimsTransformation** contiene gli elementi seguenti:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Elemento | Occorrenze | Descrizione |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | Un elenco degli elementi **InputClaim** che specificano i tipi di attestazione che vengono accettati come input per la trasformazione delle attestazioni. Ognuno di questi elementi contiene un riferimento a un ClaimType già definito nella sezione ClaimsSchema nei criteri. |
| InputParameters | 0:1 | Un elenco di elementi **InputParameter** forniti come input per la trasformazione delle attestazioni.
| OutputClaims | 0:1 | Un elenco di elementi **OutputClaim** che specificano i tipi di attestazione che vengono generati dopo aver richiamato ClaimsTransformation. Ognuno di questi elementi contiene un riferimento a un ClaimType già definito nella sezione ClaimsSchema. |

### <a name="inputclaims"></a>InputClaims

L'elemento **InputClaims** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Un tipo di attestazione di input previsto. |

#### <a name="inputclaim"></a>InputClaim

L'elemento **InputClaim** contiene gli attributi seguenti:

| Attributo |Obbligatoria | Descrizione |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Sì | Un riferimento a un ClaimType già definito nella sezione ClaimsSchema nei criteri. |
| TransformationClaimType |Sì | Un identificatore per fare riferimento a un tipo di attestazione di trasformazione. Ogni trasformazione delle attestazioni ha i propri valori. Vedere i [riferimenti delle trasformazioni di attestazioni](#claims-transformations-reference) per un elenco completo dei valori disponibili. |

### <a name="inputparameters"></a>InputParameters

L'elemento **InputParameters** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | Un tipo di parametro di input previsto. |

#### <a name="inputparameter"></a>InputParameter

| Attributo | Obbligatoria |Descrizione |
| --------- | ----------- |----------- |
| ID | Sì | Un identificatore che rappresenta un riferimento a un parametro del metodo di trasformazione delle attestazioni. Ogni metodo di trasformazione delle attestazioni ha i propri valori. Vedere la tabella delle trasformazioni delle attestazioni per un elenco completo dei valori disponibili. |
| DataType | Sì | Il tipo di dati del parametro, ad esempio String, Boolean, Int o DateTime in base all'enumerazione DataType nello schema XML dei criteri personalizzati. Questo tipo viene usato per eseguire operazioni aritmetiche in modo corretto. Ogni trasformazione delle attestazioni ha i propri valori. Vedere i [riferimenti delle trasformazioni di attestazioni](#claims-transformations-reference) per un elenco completo dei valori disponibili. |
| Valore | Sì | Un valore che viene passato letteralmente alla trasformazione. Alcuni valori sono arbitrari, altri vengono selezionati dal metodo di trasformazione delle attestazioni. |

### <a name="outputclaims"></a>OutputClaims

L'elemento **OutputClaims** contiene l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | Un tipo di attestazione di output previsto. |

#### <a name="outputclaim"></a>OutputClaim

L'elemento **OutputClaim** contiene gli attributi seguenti:

| Attributo |Obbligatoria | Descrizione |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Sì | Un riferimento a un ClaimType già definito nella sezione ClaimsSchema nei criteri.
| TransformationClaimType | Sì | Un identificatore per fare riferimento a un tipo di attestazione di trasformazione. Ogni trasformazione delle attestazioni ha i propri valori. Vedere i [riferimenti delle trasformazioni di attestazioni](#claims-transformations-reference) per un elenco completo dei valori disponibili. |

Se l'attestazione di input e l'attestazione di output sono dello stesso tipo (stringa o booleano), è possibile usare la stessa attestazione di input come attestazione di output. In questo caso, la trasformazione delle attestazioni modifica l'attestazione di input con il valore di output.

## <a name="example"></a>Esempio

Ad esempio, è possibile archiviare l'ultima versione delle condizioni d'uso accettare dall'utente. Quando si aggiornano le condizioni d'uso, è possibile chiedere all'utente di accettare la nuova versione. Nell'esempio seguente la trasformazione delle attestazioni **HasTOSVersionChanged** confronta il valore dell'attestazione **TOSVersion** con il valore dell'attestazione **LastTOSAcceptedVersion** e quindi restituisce l'attestazione booleana **TOSVersionChanged**.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Riferimento delle trasformazioni delle attestazioni

Per esempi di trasformazioni delle attestazioni, vedere le pagine seguenti:

- [Boolean](boolean-transformations.md)
- [Data](date-transformations.md)
- [Integer](integer-transformations.md)
- [JSON](json-transformations.md)
- [Numero di telefono](phone-number-claims-transformations.md)
- [Generale](general-transformations.md)
- [Account di social networking](social-transformations.md)
- [Stringa](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

