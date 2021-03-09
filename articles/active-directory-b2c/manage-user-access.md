---
title: Gestire l'accesso utente in Azure Active Directory B2C | Microsoft Docs
description: Informazioni su come identificare i minori, raccogliere dati su paese/area geografica e data di nascita e ottenere l'accettazione delle condizioni di uso nell'applicazione con Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0ee26e7fe74d87f7b20f9a28b049b8043b376273
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518058"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Gestire l'accesso utente in Azure Active Directory B2C

Questo articolo spiega come gestire l'accesso utente alle applicazioni usando Azure Active Directory B2C (Azure AD B2C). La gestione dell'accesso nell'applicazione include le operazioni seguenti:

- Identificazione dei minorenni e controllo dell'accesso utente per l'applicazione.
- Richiesta del consenso dei genitori per l'uso delle applicazioni da parte dei minorenni.
- Raccolta dei dati su paese/area geografica e data di nascita.
- Acquisizione del contratto per le condizioni per l'utilizzo e controllo dell'accesso.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Controllare l'accesso dei minorenni

Le applicazioni e le organizzazioni possono decidere di impedire ai minorenni l'uso di applicazioni e servizi non destinati a questi destinatari. In alternativa, le applicazioni e le organizzazioni possono decidere di accettare i minorenni e successivamente di gestire il consenso dei genitori e offrire esperienze consentite ai minorenni, come indicato dalle regole business e permesso dalle normative.

Se un utente viene identificato come minore, il flusso utente in Azure AD B2C può essere impostato su una di queste tre opzioni:

- **Inviare un id_token JWT firmato all'applicazione**: L'utente è registrato nella directory e viene restituito un token all'applicazione. L'applicazione quindi procede applicando le regole business. Ad esempio, l'applicazione può continuare con un processo di consenso dei genitori. Per usare questo metodo, scegliere di ricevere le attestazioni **ageGroup** e **consentProvidedForMinor** dall'applicazione.

- **Inviare un token JSON non firmato all'applicazione**: Azure AD B2C comunica all'applicazione che l'utente è minorenne e invia lo stato del consenso dei genitori dell'utente. L'applicazione quindi procede applicando le regole business. Se un token JSON non completa l'autenticazione in modo corretto, l'applicazione deve elaborare l'utente non autenticato in base alle attestazioni incluse nel token JSON, che possono includere **name**, **email**, **ageGroup**, and **consentProvidedForMinor**.

- **Bloccare l'utente**: Se un utente è minorenne e non è stato fornito il consenso dei genitori, Azure AD B2C può notificare all'utente che è stato bloccato. Non verrà emesso alcun token, l'accesso è bloccato e durante un processo di registrazione non verrà creato alcun account utente. Per implementare la notifica, presentare una pagina di contenuto HTML/CSS adatta per informare l'utente e visualizzare le opzioni appropriate. Per le nuove registrazioni non sono necessarie altre azioni da parte dell'applicazione.

## <a name="get-parental-consent"></a>Ottenere il consenso dei genitori

A seconda delle normative che l'applicazione deve rispettare, potrebbe essere necessario che il consenso dei genitori venga concesso da un utente verificato come adulto. Azure AD B2C non offre un'esperienza per verificare l'età degli individui né per consentire a un adulto verificato di concedere il consenso dei genitori a un minorenne. Questa esperienza deve essere offerta dall'applicazione o da un altro provider di servizi.

Di seguito è riportato un esempio di un flusso utente per l'acquisizione del consenso dei genitori:

1. Un'operazione dell'[API Microsoft Graph](/graph/use-the-api) identifica l'utente come minorenne e restituisce i dati dell'utente all'applicazione sotto forma di token JSON non firmato.

2. L'applicazione elabora il token JSON e visualizza una schermata per comunicare al minore che è necessario il consenso dei genitori e richiedere il consenso di un genitore online.

3. Azure AD B2C visualizza un percorso di accesso a cui l'utente può accedere normalmente e genera un token per l'applicazione impostato in modo da includere **legalAgeGroupClassification = "minorWithParentalConsent"** . L'applicazione raccoglie l'indirizzo di posta elettronica del genitore e verifica che il genitore sia un adulto. Per eseguire questa operazione, usa un'origine attendibile, ad esempio una carta di identità, patente o carta di credito. Se la verifica ha esito positivo, l'applicazione richiede al minore di eseguire l'accesso usando il flusso utente di Azure AD B2C. Se il consenso viene negato, ovvero se **legalAgeGroupClassification = "minorWithoutParentalConsent"** , Azure AD B2C restituisce un token JSON (non un account di accesso) all'applicazione per riavviare il processo di concessione del consenso. Se necessario, è possibile personalizzare il flusso utente in modo tale che un minorenne o un adulto possa ottenere nuovamente l'accesso all'account del minorenne inviando un codice di registrazione ufficiale all'indirizzo di posta elettronica del minorenne o a quello dell'adulto.

4. L'applicazione offre un'opzione al minorenne per revocare il consenso.

5. Se il minorenne o l'adulto revoca il consenso, l'API Microsoft Graph consente di impostare **consentProvidedForMinor** su **denied**. In alternativa, l'applicazione può scegliere di eliminare un minorenne per cui il consenso è stato revocato. È anche possibile personalizzare il flusso utente in modo che il minorenne autenticato (o il genitore che ne usa l'account) possa revocare il consenso. Azure AD B2C registra **consentProvidedForMinor** come **denied**.

Per altre informazioni su **legalAgeGroupClassification**, **consentProvidedForMinor** e **ageGroup**, vedere i [tipi di risorsa utente](/graph/api/resources/user). Per altre informazioni sugli attributi personalizzati, vedere [Usare attributi personalizzati per raccogliere informazioni sugli utenti](user-flow-custom-attributes.md). Quando si indirizzano attributi estesi con l'API Microsoft Graph, usare la versione estesa dell'attributo, ad esempio *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Raccogliere dati sul paese o sull'area geografica e sulla data di nascita

Le applicazioni possono basarsi su Azure AD B2C per raccogliere dati sul paese o sull'area geografica e sulla data di nascita da tutti gli utenti durante la registrazione. Se tali informazioni non esistono ancora, l'applicazione può richiederle all'utente durante la fase di autenticazione (accesso) successiva. Gli utenti non possono procedere senza indicare le informazioni sul paese o sull'area geografica e sulla data di nascita. Azure AD B2C usa le informazioni per determinare se l'utente è considerato un minorenne secondo gli standard normativi del paese o dell'area geografica.

Un flusso utente personalizzato consente di raccogliere le informazioni sul paese o sull'area geografica e sulla data di nascita e di usare la trasformazione delle attestazioni di Azure AD B2C per determinare l'elemento **ageGroup** e rendere persistente il risultato (o direttamente le informazioni sul paese o sull'area geografica e sulla data di nascita) nella directory.

I passaggi seguenti mostrano la logica che viene usata per calcolare **ageGroup** a partire dalla data di nascita dell'utente:

1. Trovare il paese o l'area geografica in base al relativo codice nell'elenco. Se il paese o l'area geografica non è presente, tornare a **Default**.

2. Se il nodo **MinorConsent** è presente nell'elemento relativo al paese o all'area geografica:

    a. Calcolare la data in cui l'utente deve essere nato per essere considerato un adulto. Ad esempio, se la data corrente è 14 marzo 2015 e **MinorConsent** è 18, la data di nascita non deve essere successiva al 14 marzo 2000.

    b. Confrontare la data di nascita minima con la data di nascita effettiva. Se la data di nascita minima precede la data di nascita dell'utente, il calcolo restituisce **Minor** come calcolo della fascia d'età.

3. Se il nodo **MinorNoConsentRequired** è presente nell'elemento relativo al paese o all'area geografica, ripetere i passaggi 2a e 2b usando il valore di **MinorNoConsentRequired**. Il risultato di 2b restituisce **MinorNoConsentRequired** se la data di nascita minima precede la data di nascita dell'utente.

4. Se nessuno dei due calcoli restituisce true, il calcolo restituisce **Adult**.

Se un'applicazione ha raccolto in modo affidabile i dati sul paese o sull'area geografica e sulla data di nascita usando altri metodi, l'applicazione può usare l'API Graph per aggiornare il record utente con queste informazioni. Ad esempio:

- Se è noto che l'utente è un adulto, aggiornare l'attributo directory **ageGroup** con il valore **Adult**.
- Se è noto che l'utente è minorenne, aggiornare l'attributo directory **ageGroup** con il valore **Minor** e impostare **consentProvidedForMinor** in base alle esigenze.

## <a name="minor-calculation-rules"></a>Regole di calcolo secondarie

Il controllo dell'accesso in base all'età prevede due valori: l'età in cui un utente non è più considerato un minore e l'età in cui un minore deve avere il consenso dei genitori. La tabella seguente elenca le regole per l'età che vengono usate per identificare un minore e un minore che necessita del consenso.

| Paese/Area geografica | Nome paese/area geografica | Minore età per cui richiedere il consenso | Minore età |
| -------------- | ------------------- | ----------------- | --------- |
| Predefinito | nessuno | nessuno | 18 |
| AE | Emirati Arabi Uniti | nessuno | 21 |
| AT | Austria | 14 | 18 |
| BE | Belgio | 14 | 18 |
| BG | Bulgaria | 16 | 18 |
| BH | Bahrain | nessuno | 21 |
| CM | Camerun | nessuno | 21 |
| CY | Cipro | 16 | 18 |
| CZ | Repubblica ceca | 16 | 18 |
| DE | Germania | 16 | 18 |
| DK | Danimarca | 16 | 18 |
| EE | Estonia | 16 | 18 |
| EG | Egitto | nessuno | 21 |
| ES | Spagna | 13 | 18 |
| VF | Francia | 16 | 18 |
| GB | Regno Unito | 13 | 18 |
| GR | Grecia | 16 | 18 |
| HR | Croazia | 16 | 18 |
| HU | Ungheria | 16 | 18 |
| IE | Irlanda | 13 | 18 |
| IT | Italia | 16 | 18 |
| KR | Repubblica di Corea | 14 | 18 |
| LT | Lituania | 16 | 18 |
| LU | Lussemburgo | 16 | 18 |
| LV | Lettonia | 16 | 18 |
| MT | Malta | 16 | 18 |
| ND | Namibia | nessuno | 21 |
| NL | Paesi Bassi | 16 | 18 |
| PL | Polonia | 13 | 18 |
| PT | Portogallo | 16 | 18 |
| RO | Romania | 16 | 18 |
| SE | Svezia | 13 | 18 |
| SG | Singapore | nessuno | 21 |
| SI | Slovenia | 16 | 18 |
| SK | Slovacchia | 16 | 18 |
| TD | Ciad | nessuno | 21 |
| TH | Thailandia | nessuno | 20 |
| TW | Taiwan | nessuno | 20 |
| Stati Uniti | Stati Uniti | 13 | 18 |



## <a name="capture-terms-of-use-agreement"></a>Acquisire il contratto per le condizioni per l'utilizzo

Quando si sviluppa l'applicazione, in genere si acquisisce l'accettazione delle condizioni per l'uso da parte dell'utente nelle applicazioni, senza alcuna partecipazione, o con una partecipazione ridotta, dalla directory dell'utente. È tuttavia possibile usare un flusso utente di Azure AD B2C per acquisire l'accettazione delle condizioni per l'utilizzo da parte dell'utente, limitare l'accesso a meno che non venga concessa l'accettazione e imporre l'accettazione delle modifiche future alle condizioni per l'utilizzo in base alla data dell'ultima accettazione e a quella dell'ultima versione delle condizioni per l'utilizzo.

Le **Condizioni per l'utilizzo** possono anche includere la dicitura per acconsentire a condividere i dati con terze parti. In base alle normative locali e alle regole di business, è possibile raccogliere l'accettazione di un utente di entrambe le condizioni combinate oppure consentire all'utente di accettare una condizione e non l'altra.

I passaggi seguenti descrivono come gestire le condizioni per l'utilizzo:

1. Registrare l'accettazione delle condizioni per l'utilizzo e la data di accettazione usando l'API Graph e gli attributi estesi. Questa operazione può essere eseguita usando flussi utente predefiniti e personalizzati. Si consiglia di creare e usare gli attributi **extension_termsOfUseConsentDateTime** e **extension_termsOfUseConsentVersion**.

2. Creare una casella di controllo obbligatoria con etichetta "Accettare le Condizioni per l'utilizzo" e registrare il risultato durante l'iscrizione. Questa operazione può essere eseguita usando flussi utente predefiniti e personalizzati.

3. Azure AD B2C archivia il contratto delle condizioni per l'utilizzo e l'accettazione dell'utente. È possibile usare l'API Graph per eseguire una query sullo stato di qualsiasi utente leggendo l'attributo di estensione usato per registrare la risposta, ad esempio **termsOfUseTestUpdateDateTime**. Questa operazione può essere eseguita usando flussi utente predefiniti e personalizzati.

4. Richiedere l'accettazione delle condizioni per l'utilizzo aggiornate confrontando la data di accettazione a quella dell'ultima versione delle condizioni per l'utilizzo. È possibile confrontare le date solo usando un flusso utente personalizzato. Usare l'attributo esteso **extension_termsOfUseConsentDateTime** e confrontare il valore con l'attestazione di **termsOfUseTextUpdateDateTime**. Se l'accettazione è datata, forzare una nuova accettazione visualizzando una schermata autocertificata. In caso contrario, bloccare l'accesso usando la logica dei criteri.

5. Richiedere l'accettazione delle condizioni per l'utilizzo aggiornate confrontando il numero di versione dell'accettazione con quello della versione accettata più recente. È possibile confrontare i numeri di versione solo usando un flusso utente personalizzato. Usare l'attributo esteso **extension_termsOfUseConsentDateTime** e confrontare il valore con l'attestazione di **extension_termsOfUseConsentVersion**. Se l'accettazione è datata, forzare una nuova accettazione visualizzando una schermata autocertificata. In caso contrario, bloccare l'accesso usando la logica dei criteri.

È possibile acquisire l'accettazione delle condizioni per l'utilizzo negli scenari seguenti:

- Un nuovo esegue l'iscrizione. Le condizioni per l'utilizzo vengono visualizzate e il risultato dell'accettazione viene archiviato.
- Un utente accede e in precedenza ha già accettato le condizioni per l'utilizzo attive o più recenti. Le condizioni per l'utilizzo non vengono visualizzate.
- Un utente accede e in precedenza non ha accettato le condizioni per l'utilizzo attive o più recenti. Le condizioni per l'utilizzo vengono visualizzate e il risultato dell'accettazione viene archiviato.
- Un utente esegue l'accesso e ha già accettato una versione precedente delle condizioni per l'utilizzo, che ora vengono aggiornate alla versione più recente. Le condizioni per l'utilizzo vengono visualizzate e il risultato dell'accettazione viene archiviato.

La figura seguente illustra il flusso utente consigliato:

![Diagramma di flusso che mostra il flusso utente di accettazione consigliato](./media/manage-user-access/user-flow.png)

Di seguito è riportato un esempio di un consenso basato sulla data per l'utilizzo in un'attestazione. Se l' `extension_termsOfUseConsentDateTime` attestazione è precedente a `2025-01-15T00:00:00` , forzare una nuova accettazione controllando l' `termsOfUseConsentRequired` attestazione booleana e visualizzando una schermata autocertificata. 

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2025-01-15T00:00:00" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Di seguito è riportato un esempio di un consenso basato sulla versione per l'utilizzo in un'attestazione. Se l' `extension_termsOfUseConsentVersion` attestazione non è uguale a `V1` , forzare una nuova accettazione controllando l' `termsOfUseConsentRequired` attestazione booleana e visualizzando una schermata autocertificata.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Passaggi successivi

- [Abilita il controllo Age in Azure ad B2C](age-gating.md).
- Per informazioni su come eliminare ed esportare i dati utente, vedere [Gestire i dati utente](manage-user-data.md).
- Per un criterio personalizzato di esempio che implementa una richiesta di condizioni per l'utilizzo, vedere [Criterio personalizzato IEF B2C - Iscriversi e accedere con la richiesta di condizioni per l'utilizzo](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou).