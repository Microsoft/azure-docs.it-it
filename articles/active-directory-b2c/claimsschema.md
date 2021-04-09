---
title: ClaimsSchema - Azure Active Directory B2C | Microsoft Docs
description: Specificare l'elemento ClaimsSchema di criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ff43408cfa6d95dbd5a235a950269c47d57a416
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654031"
---
# <a name="claimsschema"></a>ClaimsSchema

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

L'elemento **ClaimsSchema** definisce i tipi di attestazione a cui è possibile fare riferimento nei criteri. Lo schema di attestazioni è il posto in cui si dichiarano le attestazioni. Un'attestazione può essere un nome, un cognome, un nome visualizzato, un numero di telefono e altro ancora. L'elemento ClaimsSchema contiene l'elenco degli elementi **ClaimType**. Un elemento **ClaimType** contiene l'attributo **Id**, che è il nome dell'attestazione.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="Id">
      <DisplayName>Surname</DisplayName>
      <DataType>string</DataType>
      <DefaultPartnerClaimTypes>
        <Protocol Name="OAuth2" PartnerClaimType="family_name" />
        <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
        <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
      </DefaultPartnerClaimTypes>
      <UserHelpText>Your surname (also known as family name or last name).</UserHelpText>
      <UserInputType>TextBox</UserInputType>
```

## <a name="claimtype"></a>ClaimType

L'elemento **ClaimType** contiene l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Id | Sì | Identificatore usato per il tipo di attestazione. Altri elementi possono usare questo identificatore nei criteri. |

L'elemento **ClaimType** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | Titolo che viene visualizzato agli utenti nelle varie schermate. Il valore può essere [localizzato](localization.md). |
| DataType | 1:1 | Tipo di attestazione. |
| DefaultPartnerClaimTypes | 0:1 | Tipi di attestazione predefiniti di partner da usare per un protocollo specificato. Il valore può essere sovrascritto nell'elemento **PartnerClaimType** specificato nell'elemento **InputClaim** o **OutputClaim**. Usare questo elemento per specificare il nome predefinito per un protocollo.  |
| Mask | 0:1 | Stringa di caratteri di mascheramento facoltativa che può essere applicata durante la visualizzazione dell'attestazione. Il numero di telefono 324-232-4343 ad esempio può essere mascherato come XXX-XXX-4343. |
| UserHelpText | 0:1 | Descrizione del tipo di attestazione che può essere utile agli utenti per comprenderne lo scopo. Il valore può essere [localizzato](localization.md). |
| UserInputType | 0:1 | Tipo di controllo di input che deve essere disponibile all'utente per l'immissione manuale dei dati di attestazione per il tipo di attestazione. Vedere i tipi di input utente definiti più avanti in questa pagina. |
| AdminHelpText | 0:1 | Descrizione del tipo di attestazione che può essere utile per gli amministratori per comprenderne lo scopo. |
| Restrizione | 0:1 | Restrizioni ai valori per questa attestazione, ad esempio un'espressione regolare (Regex) o un elenco di valori accettabili. Il valore può essere [localizzato](localization.md). |
PredicateValidationReference| 0:1 | Riferimento a un elemento **PredicateValidationsInput**. Gli elementi **PredicateValidationReference** consentono di eseguire un processo di convalida per garantire l'inserimento solo di dati formattati adeguatamente. Per altre informazioni, vedere [Predicates](predicates.md). |



### <a name="datatype"></a>DataType

L'elemento **DataType** supporta i valori seguenti:

| Tipo | Descrizione |
| ------- | ----------- |
|boolean|Rappresenta un valore booleano (`true` o `false`).|
|Data| Rappresenta un istante di tempo, in genere espresso come data di un giorno. Il valore della data segue la convenzione ISO 8601.|
|dateTime|Rappresenta un istante di tempo, in genere espresso come data e ora del giorno. Il valore della data segue la convenzione ISO 8601.|
|duration|Rappresenta un intervallo di tempo in anni, mesi, giorni, ore, minuti e secondi. Il formato di è `PnYnMnDTnHnMnS` , dove `P` indica positivo o `N` per un valore negativo. `nY` numero di anni seguito da un valore letterale `Y` . `nMo` numero di mesi seguito da un valore letterale `Mo` . `nD` numero di giorni seguito da un valore letterale `D` . Esempi: `P21Y` rappresenta 21 anni. `P1Y2Mo` rappresenta un anno e due mesi. `P1Y2Mo5D` rappresenta un anno, due mesi e cinque giorni.  `P1Y2M5DT8H5M620S` rappresenta un anno, due mesi, cinque giorni, otto ore, cinque minuti e venti secondi.  |
|phoneNumber|Rappresenta un numero di telefono. |
|INT| Rappresenta il numero compreso tra-2.147.483.648 e 2.147.483.647|
|long| Rappresenta il numero compreso tra-9.223.372.036.854.775.808 e 9.223.372.036.854.775.807 |
|string| Rappresenta il testo come sequenza di unità di codice UTF-16.|
|stringCollection|Rappresenta una raccolta di oggetti `string`.|
|userIdentity| Rappresenta un'identità utente.|
|userIdentityCollection|Rappresenta una raccolta di oggetti `userIdentity`.|

### <a name="defaultpartnerclaimtypes"></a>DefaultPartnerClaimTypes

L'elemento **DefaultPartnerClaimTypes** può contenere l'elemento seguente:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Protocollo | 1:n | Elenco di protocolli con il relativo nome del tipo di attestazione di partner predefinito. |

L'elemento **Protocollo** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Nome | Sì | Nome di un protocollo valido supportato da Azure AD B2C. I valori possibili sono: OAuth1, OAuth2, SAML2, OpenIdConnect. |
| PartnerClaimType | Sì | Nome del tipo di attestazione da usare. |

Nell'esempio seguente, quando il framework dell'esperienza di gestione delle identità interagisce con un provider di identità SAML2 o un'applicazione basata su attestazioni, l'attestazione **surname** (cognome) viene mappata a `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`. Quando il framework interagisce con OpenIdConnect e OAuth2, l'attestazione viene mappata a `family_name`.

```xml
<ClaimType Id="surname">
  <DisplayName>Surname</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
    <Protocol Name="OAuth2" PartnerClaimType="family_name" />
    <Protocol Name="OpenIdConnect" PartnerClaimType="family_name" />
    <Protocol Name="SAML2" PartnerClaimType="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" />
  </DefaultPartnerClaimTypes>
</ClaimType>
```

Il token JWT emesso da Azure AD B2C crea quindi `family_name` invece del nome di ClaimType **surname** (cognome).

```json
{
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  "auth_time": 1535013501,
  "given_name": "David",
  "family_name": "Williams",
  "name": "David Williams",
}
```

### <a name="mask"></a>Mask

L'elemento **Mask** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| `Type` | Sì | Tipo di maschera dell'attestazione. I valori possibili sono: `Simple` o `Regex`. Il valore `Simple` indica che viene applicata una semplice maschera di testo alla porzione iniziale di un'attestazione di tipo stringa. Il valore `Regex` indica che viene applicata un'espressione regolare all'intera attestazione di tipo stringa.  Se viene specificato il valore `Regex`, è necessario definire anche un attributo facoltativo insieme all'espressione regolare da usare. |
| `Regex` | No | Se **`Type`** è impostato su `Regex` , specificare l'espressione regolare da usare.

L'esempio seguente configura un'attestazione **PhoneNumber** con la maschera `Simple`:

```xml
<ClaimType Id="PhoneNumber">
  <DisplayName>Phone Number</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Simple">XXX-XXX-</Mask>
  <UserHelpText>Your telephone number.</UserHelpText>
</ClaimType>
```

Il framework dell'esperienza di gestione delle identità esegue il rendering del numero di telefono e al contempo nasconde le prime sei cifre:

![Attestazione del numero di telefono visualizzata nel browser con le prime sei cifre mascherate da XS](./media/claimsschema/mask.png)

L'esempio seguente configura un'attestazione **AlternateEmail** con la maschera `Regex`:

```xml
<ClaimType Id="AlternateEmail">
  <DisplayName>Please verify the secondary email linked to your account</DisplayName>
  <DataType>string</DataType>
  <Mask Type="Regex" Regex="(?&lt;=.).(?=.*@)">*</Mask>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```

Il framework dell'esperienza di gestione delle identità esegue il rendering solo della prima lettera dell'indirizzo e-mail e del nome del dominio di posta elettronica:

![Attestazione posta elettronica visualizzata nel browser con caratteri mascherati da asterischi](./media/claimsschema/mask-regex.png)


### <a name="restriction"></a>Restrizione

L'elemento **Restriction** può contenere l'attributo seguente:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| MergeBehavior | No | Metodo usato per unire i valori di enumerazione con un elemento ClaimType nei criteri padre avente lo stesso identificatore. Usare questo attributo quando si sovrascrive un'attestazione specificata nei criteri di base. I valori possibili sono: `Append`, `Prepend` o `ReplaceAll`. Il valore `Append` è una raccolta di dati che è consigliabile accodare alla fine della raccolta specificata nei criteri padre. Il valore `Prepend` è una raccolta di dati che è consigliabile aggiungere all'inizio della raccolta specificata nei criteri padre. Il `ReplaceAll` valore è una raccolta di dati specificati nei criteri padre che è consigliabile ignorare. |

L'elemento **Restriction** contiene gli elementi seguenti:

| Elemento | Occorrenze | Descrizione |
| ------- | ----------- | ----------- |
| Enumerazione | 1:n | Opzioni disponibili nell'interfaccia utente che l'utente può selezionare per un'attestazione, ad esempio un valore in un menu a discesa. |
| Modello | 1:1 | Espressione regolare da usare. |

#### <a name="enumeration"></a>Enumerazione

L'elemento **Enumeration** definisce le opzioni disponibili che l'utente deve selezionare per un'attestazione nell'interfaccia utente, ad esempio un valore in un oggetto `CheckboxMultiSelect` , `DropdownSingleSelect` o `RadioSingleSelect` . In alternativa, è possibile definire e localizzare le opzioni disponibili con l'elemento [LocalizedCollections](localization.md#localizedcollections) . Per cercare un elemento da una raccolta di **enumerazioni** di attestazioni, usare la trasformazione delle attestazioni [GetMappedValueFromLocalizedCollection](string-transformations.md#getmappedvaluefromlocalizedcollection) .

L'elemento **Enumeration** contiene gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| Testo | Sì | Stringa di visualizzazione che viene mostrata all'utente nell'interfaccia utente per questa opzione. |
|Valore | Sì | Valore di attestazione che viene associato alla selezione di questa opzione. |
| SelectByDefault | No | Indica se questa opzione deve essere selezionata o meno per impostazione predefinita nell'interfaccia utente. I valori possibili sono: True o False. |

L'esempio seguente configura un'attestazione di elenco a discesa di **città** con il valore predefinito impostato su `New York`:

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

Elenco a discesa delle città con valore predefinito impostato su New York:

![Controllo DropDown sottoposto a rendering nel browser e visualizzazione del valore predefinito](./media/claimsschema/dropdownsingleselect.png)

### <a name="pattern"></a>Modello

L'elemento **Pattern** può contenere gli attributi seguenti:

| Attributo | Obbligatoria | Descrizione |
| --------- | -------- | ----------- |
| RegularExpression | Sì | Espressione regolare a cui le attestazioni di questo tipo devono corrispondere per poter essere valide. |
| HelpText | No | Messaggio di errore per gli utenti se il controllo dell'espressione regolare ha esito negativo. |

L'esempio seguente configura un'attestazione **messaggio e-mail** con la convalida di input dell'espressione regolare e con il testo della Guida:

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <DefaultPartnerClaimTypes>
  <Protocol Name="OpenIdConnect" PartnerClaimType="email" />
  </DefaultPartnerClaimTypes>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

Il framework dell'esperienza di gestione delle identità esegue il rendering dell'attestazione indirizzo e-mail con la convalida di input del formato dell'indirizzo di posta elettronica:

![Casella di testo che mostra il messaggio di errore attivato dalla restrizione Regex](./media/claimsschema/pattern.png)

### <a name="userinputtype"></a>UserInputType

Azure AD B2C supporta molti tipi di input utente, ad esempio casella di testo, password ed elenco a discesa, che possono essere usati durante l'immissione dei dati di attestazione per il tipo di attestazione. È necessario specificare **tipo** quando si raccolgono informazioni dall'utente utilizzando un [profilo tecnico autocertificato](self-asserted-technical-profile.md) e i [controlli di visualizzazione](display-controls.md).

Tipi di input utente disponibili nell'elemento **tipo** :

| UserInputType | ClaimType supportati | Descrizione |
| --------- | -------- | ----------- |
|CheckboxMultiSelect| `string` |Casella di riepilogo a discesa MultiSelect. Il valore dell'attestazione è rappresentato in una stringa delimitatore da virgole dei valori selezionati. |
|DateTimeDropdown | `date`, `dateTime` |Elenchi a discesa per selezionare un giorno, un mese e un anno. |
|DropdownSingleSelect |`string` |Casella di riepilogo a discesa selezione singola. Il valore dell'attestazione è il valore selezionato.|
|EmailBox | `string` |Campo di input della posta elettronica. |
|Paragraph | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`|Campo che mostra solo il testo in un tag di paragrafo. |
|Password | `string` |Casella di testo password.|
|RadioSingleSelect |`string` | Raccolta di pulsanti di opzione. Il valore dell'attestazione è il valore selezionato.|
|Readonly | `boolean`, `date`, `dateTime`, `duration`, `int`, `long`, `string`| Casella di testo di sola lettura. |
|TextBox |`boolean`, `int`, `string` |Casella di testo a riga singola. |


#### <a name="textbox"></a>TextBox

Il tipo di input utente **TextBox** viene usato per visualizzare una casella di testo a riga singola.

![TextBox che mostra le proprietà specificate nel tipo di attestazione](./media/claimsschema/textbox.png)

```xml
<ClaimType Id="displayName">
  <DisplayName>Display Name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your display name.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="emailbox"></a>EmailBox

Il tipo di input utente **EmailBox** viene usato per visualizzare un campo di input di base per l'e-mail.

![EmailBox che mostra le proprietà specificate nel tipo di attestazione](./media/claimsschema/emailbox.png)

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>EmailBox</UserInputType>
  <Restriction>
    <Pattern RegularExpression="^[a-zA-Z0-9.+!#$%&amp;'+^_`{}~-]+(?:\.[a-zA-Z0-9!#$%&amp;'+^_`{}~-]+)*@(?:[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?\.)+[a-zA-Z0-9](?:[a-zA-Z0-9-]*[a-zA-Z0-9])?$" HelpText="Please enter a valid email address." />
  </Restriction>
</ClaimType>
```

#### <a name="password"></a>Password

Il tipo di input utente **Password** viene usato per registrare una password immessa dall'utente.

![Utilizzo del tipo di attestazione con password](./media/claimsschema/password.png)

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
</ClaimType>
```

#### <a name="datetimedropdown"></a>DateTimeDropdown

Il tipo di input utente **DateTimeDropdown** viene usato per visualizzare un set di menu a discesa da cui selezionare un giorno, un mese e un anno. È possibile usare gli elementi Predicates e PredicateValidations per controllare i valori di data minimo e massimo. Per altre informazioni, vedere la sezione **Configurare un intervallo di date** in [Predicates e PredicateValidations](predicates.md).

![Utilizzo del tipo di attestazione con menu a discesa data e ora (datetimedropdown)](./media/claimsschema/datetimedropdown.png)

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date Of Birth</DisplayName>
  <DataType>date</DataType>
  <UserHelpText>The date on which you were born.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

Il tipo di input utente **RadioSingleSelect** viene usato per visualizzare una raccolta di pulsanti radio da cui l'utente può selezionare un'opzione.

![Utilizzo del tipo di attestazione con pulsanti radio a selezione singola (radiodsingleselect)](./media/claimsschema/radiosingleselect.png)

```xml
<ClaimType Id="color">
  <DisplayName>Preferred color</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Blue" Value="Blue" SelectByDefault="false" />
    <Enumeration Text="Green " Value="Green" SelectByDefault="false" />
    <Enumeration Text="Orange" Value="Orange" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

Il tipo di input utente **DropdownSingleSelect** viene usato per visualizzare una casella a discesa da cui l'utente può selezionare un'opzione.

![Utilizzo del tipo di attestazione con casella a discesa a selezione singola (dropdownsingleselect)](./media/claimsschema/dropdownsingleselect.png)

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="New York" Value="new-york" SelectByDefault="true" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

Il tipo di input utente **CheckboxMultiSelect** viene usato per visualizzare una raccolta di caselle di controllo da cui l'utente può selezionare più opzioni.

![Utilizzo del tipo di attestazione con casella di controllo a più selezioni (checkboxmultiselect)](./media/claimsschema/checkboxmultiselect.png)

```xml
<ClaimType Id="languages">
  <DisplayName>Languages you speak</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="English" Value="English" SelectByDefault="true" />
    <Enumeration Text="France " Value="France" SelectByDefault="false" />
    <Enumeration Text="Spanish" Value="Spanish" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="readonly"></a>Readonly

Il tipo di input utente **Readonly** viene usato per visualizzare un campo di sola lettura in cui visualizzare l'attestazione e il valore.

![Utilizzo del tipo di attestazione con sola lettura (readonly)](./media/claimsschema/readonly.png)

```xml
<ClaimType Id="membershipNumber">
  <DisplayName>Membership number</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your membership number (read only)</UserHelpText>
  <UserInputType>Readonly</UserInputType>
</ClaimType>
```


#### <a name="paragraph"></a>Paragraph

Il tipo di input utente **Paragraph** viene usato per visualizzare un campo che mostra solo testo in un tag di paragrafo,  ad esempio &lt;p&gt;testo&lt;/p&gt;. Un  tipo di input utente `OutputClaim` di paragrafo del profilo tecnico autocertificato, deve impostare l' `Required` attributo `false` (impostazione predefinita).

![Utilizzo del tipo di attestazione con paragrafo (paragraph)](./media/claimsschema/paragraph.png)

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
  <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
