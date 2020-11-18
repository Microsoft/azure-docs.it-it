---
title: Regole per l'appartenenza ai gruppi popolati in modo dinamico - Azure AD | Microsoft Docs
description: Come creare regole di appartenenza per popolare automaticamente i gruppi e informazioni di riferimento sulle regole.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: overview
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a448b510662eb2c3c4c8ae5a68c2ebc2ed448dc
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647359"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Regole di appartenenza dinamica per i gruppi in Azure Active Directory

In Azure Active Directory (Azure AD) è possibile creare regole complesse basate su attributi per abilitare appartenenze dinamiche per gruppi. L'appartenenza a gruppi dinamica riduce il sovraccarico amministrativo per l'aggiunta e la rimozione di utenti. Questo articolo descrive in dettaglio le proprietà e la sintassi per creare regole di appartenenza dinamica per utenti o dispositivi. È possibile configurare una regola per l'appartenenza dinamica nei gruppi di sicurezza o nei gruppi di Microsoft 365.

Quando gli attributi di un utente o un dispositivo cambiano, il sistema valuta tutte le regole dinamiche del gruppo in una directory per verificare se la modifica attiverà aggiunte o rimozioni nel gruppo. Se un utente o un dispositivo soddisfa una regola in un gruppo, viene aggiunto come membro a tale gruppo. Se non soddisfano la regola, vengono rimossi. Non è possibile aggiungere o rimuovere un membro di un gruppo dinamico manualmente.

- Sebbene sia possibile creare un gruppo dinamico per i dispositivi o gli utenti, non è possibile creare una regola che contenga sia oggetti utente che dispositivo.
- Non è possibile creare un gruppo di dispositivi in base agli attributi dei proprietari dei dispositivi. Le regole di appartenenza dei dispositivi possono fare riferimento solo agli attributi dei dispositivi.

> [!NOTE]
> Questa funzionalità richiede una licenza Azure AD Premium P1 per ogni utente univoco membro di almeno un gruppo dinamico. Perché gli utenti siano membri dei gruppi dinamici, non è obbligatorio che vengano effettivamente assegnate loro le licenze, ma è necessario avere il numero necessario di licenze nell'organizzazione Azure AD per coprire tutti gli utenti. Se ad esempio si ha un totale di 1.000 utenti univoci in tutti i gruppi dinamici dell'organizzazione, è necessario avere almeno 1.000 licenze di Azure AD Premium P1 per soddisfare il requisito delle licenze.
> Per i dispositivi che sono membri di un gruppo di dispositivi dinamici non è necessaria alcuna licenza.

## <a name="rule-builder-in-the-azure-portal"></a>Generatore di regole nel portale di Azure

Azure AD fornisce un generatore di regole per creare e aggiornare le regole importanti più rapidamente. Il generatore di regole supporta la costruzione di un massimo di cinque espressioni. Il generatore di regole rende più semplice formare una regola con alcune espressioni semplici, ma non può essere usato per riprodurre ogni regola. Se il generatore regole non supporta la regola che si desidera creare, è possibile utilizzare la casella di testo.

Di seguito sono riportati alcuni esempi di regole o sintassi avanzate che si consiglia di generare utilizzando la casella di testo:

- Regola con più di cinque espressioni
- La regola per i dipendenti diretti
- Impostazione della [precedenza degli operatore](#operator-precedence)
- [Regole con espressioni complesse](#rules-with-complex-expressions); ad esempio `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Il generatore di regole potrebbe non essere in grado di visualizzare alcune regole costruite nella casella di testo. Può essere visualizzato un messaggio quando il generatore di regole non è in grado di visualizzare la regola. Il generatore di regole non modifica in alcun modo la sintassi, la convalida o l'elaborazione delle regole di gruppo dinamiche supportate.

Per altre istruzioni dettagliate, vedere [Creare o aggiornare un gruppo dinamico](groups-create-rule.md).

![Aggiungere la regola di appartenenza per un gruppo dinamico](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>Sintassi della regola per una singola espressione

Una singola espressione è la forma più semplice per una regola di appartenenza e include solo le tre parti indicate in precedenza. Una regola con una singola espressione è simile a quanto segue: `Property Operator Value`, dove la sintassi per la proprietà è il nome di oggetto.proprietà.

Di seguito è riportato un esempio di regola di appartenenza strutturata correttamente con una singola espressione:

```
user.department -eq "Sales"
```

Per una singola espressione le parentesi sono facoltative. La lunghezza totale del corpo della regola di appartenenza non può superare i 2048 caratteri.

## <a name="constructing-the-body-of-a-membership-rule"></a>Creazione del corpo di una regola di appartenenza

Una regola di appartenenza che popola automaticamente un gruppo con utenti o dispositivi è un'espressione binaria che restituisce un risultato true o false. Le tre parti di una regola semplice sono:

- Proprietà
- Operatore
- valore

L'ordine delle parti in un'espressione è importante per evitare gli errori di sintassi.

## <a name="supported-properties"></a>Proprietà supportate

Ci sono tre tipi di proprietà che è possibile usare per costruire una regola di appartenenza.

- Boolean
- string
- Raccolta di tipi string

Di seguito sono elencate le proprietà utente che è possibile usare per creare una singola espressione.

### <a name="properties-of-type-boolean"></a>Proprietà di tipo boolean

| Proprietà | Valori consentiti | Uso |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>Proprietà di tipo stringa

| Proprietà | Valori consentiti | Uso |
| --- | --- | --- |
| city |Qualsiasi valore di stringa o *null* |(user.city -eq "valore") |
| country |Qualsiasi valore di stringa o *null* |(user.country -eq "valore") |
| companyName | Qualsiasi valore di stringa o *null* | (user.companyName -eq "value") |
| department |Qualsiasi valore di stringa o *null* |(user.department -eq "valore") |
| displayName |Qualsiasi valore di stringa |(user.displayName -eq "valore") |
| employeeId |Qualsiasi valore di stringa |(user.employeeId -eq "valore")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |Qualsiasi valore di stringa o *null* |(user.facsimileTelephoneNumber -eq "valore") |
| givenName |Qualsiasi valore di stringa o *null* |(user.givenName -eq "valore") |
| jobTitle |Qualsiasi valore di stringa o *null* |(user.jobTitle -eq "valore") |
| mail |Qualsiasi valore di stringa o *null* (indirizzo SMTP dell'utente) |(user.mail -eq "valore") |
| mailNickName |Qualsiasi valore stringa (alias di posta dell'utente) |(user.mailNickName -eq "valore") |
| mobile |Qualsiasi valore di stringa o *null* |(user.mobile -eq "valore") |
| objectId |GUID dell'oggetto utente |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | Identificatore di sicurezza (SID) locale per gli utenti sincronizzati da un ambiente locale al cloud. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |Nessuno DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |Qualsiasi valore di stringa o *null* |(user.physicalDeliveryOfficeName -eq "valore") |
| postalCode |Qualsiasi valore di stringa o *null* |(user.postalCode -eq "valore") |
| preferredLanguage |Codice ISO 639-1 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |Qualsiasi valore di stringa o *null* |(user.sipProxyAddress -eq "valore") |
| state |Qualsiasi valore di stringa o *null* |(user.state -eq "valore") |
| streetAddress |Qualsiasi valore di stringa o *null* |(user.streetAddress -eq "valore") |
| surname |Qualsiasi valore di stringa o *null* |(user.surname -eq "valore") |
| telephoneNumber |Qualsiasi valore di stringa o *null* |(user.telephoneNumber -eq "valore") |
| usageLocation |Codice di paese/regione di due lettere |(user.usageLocation -eq "US") |
| userPrincipalName |Qualsiasi valore di stringa |(user.userPrincipalName -eq "alias@domain") |
| userType |membro guest *null* |(user.userType -eq "Membro") |

### <a name="properties-of-type-string-collection"></a>Proprietà di tipo insieme String

| Proprietà | Valori consentiti | Uso |
| --- | --- | --- |
| otherMails |Qualsiasi valore di stringa |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

Per le proprietà usate per le regole dei dispositivi, vedere [Regole per i dispositivi](#rules-for-devices).

## <a name="supported-expression-operators"></a>Operatori di espressione supportati

Nella tabella seguente sono elencati tutti gli operatori supportati e la relativa sintassi per un'espressione singola. Gli operatori possono essere usati con o senza trattino (-) come prefisso.

| Operatore | Sintassi |
| --- | --- |
| Non uguale a |-ne |
| Uguale a |-eq |
| Non inizia con |-notStartsWith |
| Starts With |-startsWith |
| Non contiene |-notContains |
| Contiene |-contains |
| Non corrispondente |-notMatch |
| Corrispondente |-match |
| In ingresso | -in |
| Non incluso | -notIn |

### <a name="using-the--in-and--notin-operators"></a>Uso degli operatori -in e -notIn

Per confrontare il valore di un attributo utente con una serie di valori diversi, è possibile usare operatori -in o -notIn. Usare i simboli di parentesi quadre "[" e "]" per iniziare e terminare l'elenco di valori.

 Nell'esempio seguente l'espressione restituisce true se il valore di user.department è uguale a uno dei valori nell'elenco:

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>Uso dell'operatore -match 
L'operatore **-match** viene usato per la corrispondenza di qualsiasi espressione regolare. Esempi:

```
user.displayName -match "Da.*"   
```
Da, Dav e David restituiscono true, mentre aDa restituisce false.

```
user.displayName -match ".*vid"
```
David restituisce true, mentre Da restituisce false.

## <a name="supported-values"></a>Valori supportati

I valori usati in un'espressione possono essere costituiti da tipi diversi, tra cui:

* Stringhe
* Valori booleani: true, false
* Numeri
* Matrici: matrice di numeri, matrice di stringhe

Quando si specifica un valore in un'espressione, è importante usare la sintassi corretta per evitare errori. Ecco alcuni suggerimenti per la sintassi:

* Le virgolette doppie sono facoltative, a meno che il valore non sia una stringa.
* Le operazioni di stringa ed espressione regolare non fanno distinzione tra maiuscole e minuscole.
* Quando un valore stringa contiene virgolette doppie, per entrambe le virgolette deve venire usato il carattere di escape \`, ad esempio user.department -eq \`"Sales\`" è la sintassi corretta quando "Sales" è il valore.
* È anche possibile eseguire controlli Null usando null come valore, ad esempio `user.department -eq null`.

### <a name="use-of-null-values"></a>Uso dei valori Null

Per specificare un valore Null in una regola, è possibile usare il valore *null*. 

* Usare -eq o -ne quando si confronta il valore *null* in un'espressione.
* Racchiudere la parola *null* tra virgolette solo se si vuole che venga interpretata come valore di stringa letterale.
* L'operatore -not non può essere usato come operatore di confronto per null. Se si usa questo operatore, viene restituito un errore indipendentemente dal fatto che si usi null o $null.

Il modo corretto per fare riferimento al valore Null è il seguente:

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>Regole con più espressioni

Una regola di appartenenza a un gruppo può essere costituita da più espressioni connesse dagli operatori logici -and, -or e -not. Gli operatori logici possono anche essere usati in combinazione. 

Di seguito sono riportati esempi di regole di appartenenza strutturate correttamente con più espressioni:

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>Precedenza degli operatori

Tutti gli operatori sono elencati di seguito in ordine decrescente di precedenza. Gli operatori sulla stessa riga hanno uguale precedenza:

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

Di seguito è riportato un esempio di precedenza degli operatori in cui due espressioni vengono valutate per l'utente:

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

Le parentesi sono necessarie solo quando la priorità non soddisfa i requisiti. Ad esempio, se si vuole che venga valutato prima department, usare le parentesi come illustrato di seguito per determinare l'ordine:

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>Regole con espressioni complesse

Una regola di appartenenza può essere costituita da espressioni complesse in cui proprietà, operatori e valori acquisiscono forme più complesse. Le espressioni sono considerate complesse in presenza di una delle condizioni seguenti:

* La proprietà è costituita da una raccolta di valori, nello specifico si parla di proprietà multivalore
* Le espressioni usano gli operatori -any e -all
* Il valore dell'espressione può essere costituito da una o più espressioni

## <a name="multi-value-properties"></a>Proprietà multivalore

Le proprietà multivalore sono raccolte di oggetti dello stesso tipo. Possono essere usate per creare regole di appartenenza tramite gli operatori logici -any e -all.

| Proprietà | Valori | Uso |
| --- | --- | --- |
| assignedPlans | Ogni oggetto della raccolta espone le proprietà di stringa seguenti: capabilityStatus, service, servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -any (\_ -contains "contoso")) |

### <a name="using-the--any-and--all-operators"></a>Uso degli operatori -any e -all

È possibile usare gli operatori -any e -all per applicare una condizione rispettivamente a uno o a tutti gli elementi della raccolta.

* -any (soddisfatto quando almeno un elemento della raccolta corrisponde alla condizione)
* -all (soddisfatto quando tutti gli elementi della raccolta corrispondono alla condizione)

#### <a name="example-1"></a>Esempio 1

assignedPlans è una proprietà multivalore che elenca tutti i piani di servizio assegnati all'utente. L'espressione seguente seleziona gli utenti che hanno il piano di servizio Exchange Online (Piano 2) (come valore GUID) con lo stato abilitato:

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

Una regola di questo tipo può essere usata per raggruppare tutti gli utenti per cui è abilitata una funzionalità di Microsoft 365 o di un altro servizio Microsoft online. È quindi possibile applicare un set di criteri al gruppo.

#### <a name="example-2"></a>Esempio 2

L'espressione seguente seleziona tutti gli utenti che hanno un piano di servizio qualsiasi associato al servizio Intune (identificato dal nome di servizio "SCO"):

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>Usando la sintassi del carattere di sottolineatura (\_)

La sintassi del carattere di sottolineatura (\_) combina le occorrenze di un valore specifico in una delle proprietà di raccolta stringa multivalore per aggiungere utenti o dispositivi a un gruppo dinamico. Viene usata con-qualsiasi - o tutti - gli operatori.

Ecco un esempio dell'uso del carattere di sottolineatura (\_) in una regola per aggiungere membri in base a user.proxyAddress (funziona allo stesso modo per user.otherMails). Questa regola aggiunge tutti gli utenti con l'indirizzo del proxy contenente "contoso" al gruppo.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>Altre proprietà e regole comuni

### <a name="create-a-direct-reports-rule"></a>Creare una regola "Dipendenti diretti"

È possibile creare un gruppo contenente tutti i dipendenti diretti di un manager. Quando in futuro i dipendenti diretti del manager cambiano, l'appartenenza al gruppo viene modificata automaticamente.

La regola per i dipendenti diretti viene costruita usando la sintassi seguente:

```
Direct Reports for "{objectID_of_manager}"
```

Ecco un esempio di regola valida, dove "62e19b97-8b3d-4d4a-a106-4ce66896a863" è il valore di objectID del manager:

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

I suggerimenti seguenti sono utili per usare la regola in modo appropriato.

- L'**ID del manager** è l'ID oggetto del manager. È indicato nella sezione **Profilo** del manager.
- Per il corretto funzionamento della regola, verificare che la proprietà **Manager** sia impostata correttamente per gli utenti inclusi nell'organizzazione. È possibile controllare il valore corrente per un utente nella sezione **Profilo** dell'utente.
- Questa regola supporta solo i dipendenti diretti del manager. In altre parole, non è possibile creare un gruppo con i dipendenti diretti del manager *e* i loro dipendenti diretti.
- Questa regola non può essere combinata con altre regole di appartenenza.

### <a name="create-an-all-users-rule"></a>Creare una regola "Tutti gli utenti"

È possibile creare un gruppo contenente tutti gli utenti di un'organizzazione usando una regola di appartenenza. Quando in futuro gli utenti vengono aggiunti o rimossi dall'organizzazione, l'appartenenza al gruppo viene modificata automaticamente.

La regola "Tutti gli utenti" viene costruita usando una singola espressione con l'operatore -ne e il valore null. Questa regola consente di aggiungere al gruppo sia gli utenti guest B2B che gli utenti membri.

```
user.objectId -ne null
```
Se si desidera che il gruppo escluda gli utenti guest e includa solo i membri dell'organizzazione, è possibile utilizzare la sintassi seguente:

```
(user.objectId -ne null) -and (user.userType -eq "Member")
```

### <a name="create-an-all-devices-rule"></a>Creare una regola "Tutti i dispositivi"

È possibile creare un gruppo contenente tutti i dispositivi di un'organizzazione usando una regola di appartenenza. Quando in futuro i dispositivi vengono aggiunti o rimossi dall'organizzazione, l'appartenenza al gruppo viene modificata automaticamente.

La regola "Tutti i dispositivi" viene costruita usando una singola espressione con l'operatore -ne e il valore null:

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>Proprietà di estensione e proprietà di estensione personalizzate

Gli attributi di estensione e le proprietà di estensione personalizzate sono supportati come proprietà delle stringhe nelle regole di appartenenza dinamica. Gli [attributi di estensione](/graph/api/resources/onpremisesextensionattributes?view=graph-rest-1.0) vengono sincronizzati dall'istanza locale di Window Server AD e hanno il formato "ExtensionAttributeX", dove X equivale a 1 - 15. Ecco un esempio di regola che usa un attributo di estensione come proprietà:

```
(user.extensionAttribute15 -eq "Marketing")
```

Le [proprietà di estensione personalizzate](../hybrid/how-to-connect-sync-feature-directory-extensions.md) vengono sincronizzate dall'istanza locale di AD di Windows Server o da un'applicazione SaaS connessa e hanno il formato `user.extension_[GUID]_[Attribute]`, dove:

* [GUID] è l'identificatore univoco in Azure AD per l'applicazione che ha creato la proprietà in Azure AD
* [Attribute] è il nome della proprietà quando è stata creata

Ecco un esempio di regola che usa una proprietà di estensione personalizzata:

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

È possibile trovare il nome della proprietà personalizzata nella directory eseguendo una query sulla proprietà dell'utente con Graph explorer e cercando il nome della proprietà. È inoltre ora possibile selezionare il collegamento **Ottieni proprietà di estensione personalizzate** nel generatore di regole di assegnazione dinamica dei gruppi utenti per immettere un ID app univoco e ricevere l'elenco completo di proprietà di estensione personalizzate da usare quando si crea una regola di appartenenza dinamica. È anche possibile aggiornare questo elenco per ottenere tutte le nuove proprietà di estensione personalizzate per l'app.

## <a name="rules-for-devices"></a>Regole per i dispositivi

È anche possibile creare una regola che consenta di selezionare gli oggetti dispositivo per l'appartenenza a un gruppo. Un gruppo non può avere come membri sia utenti che dispositivi. 

> [!NOTE]
> L'attributo **organizationalUnit** non viene più elencato e non deve essere usato. Questa stringa viene impostata da Intune in casi specifici ma non viene riconosciuta da Azure AD, quindi nessun dispositivo viene aggiunto ai gruppi in base a questo attributo.

> [!NOTE]
> systemlabels è un attributo di sola lettura che non può essere impostato con Intune.
>
> Per Windows 10, il formato corretto dell'attributo deviceOSVersion è il seguente: (device.deviceOSVersion -eq "10.0.17763"). La formattazione può essere convalidata con il cmdlet Get-MsolDevice di PowerShell.

È possibile usare gli attributi del dispositivo seguenti.

 Attributo del dispositivo  | Valori | Esempio
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | Qualsiasi valore stringa. |(device.displayName -eq "Rob iPhone")
 deviceOSType | Qualsiasi valore stringa. | (device.deviceOSType -eq "iPad") o (device.deviceOSType -eq "iPhone")<br>(device.deviceOSType -contains "AndroidEnterprise")<br>(device.deviceOSType -eq "AndroidForWork")
 deviceOSVersion | Qualsiasi valore stringa. | (device.deviceOSVersion -eq "9.1")
 deviceCategory | nome di una categoria di dispositivo valido | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | Qualsiasi valore stringa. | (device.deviceManufacturer -eq "Samsung")
 deviceModel | Qualsiasi valore stringa. | (device.deviceModel -eq "iPad Air")
 deviceOwnership | Personale, Azienda, Sconosciuto | (device.deviceOwnership -eq "Company")
 enrollmentProfileName | Nome del profilo di registrazione dispositivi di Apple (Apple Device Enrollment), nome del profilo di registrazione del dispositivo di proprietà dell'azienda Android Enterprise o nome del profilo di Windows Autopilot | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM (per i dispositivi mobili)<br>PC (per i computer gestiti dall'agente di PC Intune) | (device.managementType -eq "MDM")
 deviceId | ID dispositivo di Azure AD valido | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | ID oggetto di Azure AD valido |  (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds | qualsiasi valore stringa utilizzato da Autopilot, ad esempio tutti i dispositivi Autopilot, OrderID o PurchaseOrderID  | (device.devicePhysicalIDs -any _ -contains "[ZTDId]") (device.devicePhysicalIds -any _ -eq "[OrderID]:179887111881") (device.devicePhysicalIds -any _ -eq "[PurchaseOrderId]:76222342342")
 systemLabels | qualsiasi stringa corrispondente alla proprietà del dispositivo Intune per contrassegnare i dispositivi dell'area di lavoro moderna | (device.systemLabels -contains "M365Managed")

> [!Note]  
> Per deviceOwnership durante la creazione di gruppi dinamici per i dispositivi, è necessario impostare il valore uguale a "Company". In Intune la proprietà del dispositivo viene invece rappresentata come aziendale. Fare riferimento a [OwnerTypes](/intune/reports-ref-devices#ownertypes) per altri dettagli. 

## <a name="next-steps"></a>Passaggi successivi

Questi articoli forniscono informazioni aggiuntive sui gruppi in Azure Active Directory.

- [Vedere i gruppi esistenti](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Creare un nuovo gruppo e aggiunta di membri](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Gestire le impostazioni di un gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gestire le appartenenze di un gruppo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gestire le regole dinamiche per gli utenti in un gruppo](groups-create-rule.md)