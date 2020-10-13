---
title: Funzionamento della sincronizzazione in Azure AD Domain Services | Microsoft Docs
description: Informazioni sul funzionamento del processo di sincronizzazione per oggetti e credenziali da un tenant Azure AD o un ambiente Active Directory Domain Services locale a un dominio gestito da Azure Active Directory Domain Services.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: joflore
ms.openlocfilehash: 683a6c9f31947355a5415a5b8b57b621f717af91
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967665"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-active-directory-domain-services-managed-domain"></a>Modalità di sincronizzazione di oggetti e credenziali in un Azure Active Directory Domain Services dominio gestito

Gli oggetti e le credenziali in un dominio gestito di Azure Active Directory Domain Services (Azure AD DS) possono essere creati localmente all'interno del dominio o sincronizzati da un tenant Azure Active Directory (Azure AD). Quando si distribuisce per la prima volta Azure AD DS, viene configurata e avviata una sincronizzazione automatica unidirezionale per replicare gli oggetti da Azure AD. Questa sincronizzazione unidirezionale continua a essere eseguita in background per Mantenete aggiornato il dominio gestito di Azure AD DS con eventuali modifiche da Azure AD. Non viene eseguita alcuna sincronizzazione da Azure AD DS a Azure AD.

In un ambiente ibrido, gli oggetti e le credenziali di un dominio di servizi di dominio Active Directory locale possono essere sincronizzati con Azure AD usando Azure AD Connect. Una volta che gli oggetti sono stati sincronizzati correttamente con Azure AD, la sincronizzazione automatica in background rende disponibili tali oggetti e credenziali per le applicazioni che usano il dominio gestito.

Il diagramma seguente illustra il funzionamento della sincronizzazione tra Azure AD DS, Azure AD e un ambiente di servizi di dominio Active Directory locale facoltativo:

![Panoramica della sincronizzazione per un dominio gestito Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Sincronizzazione da Azure AD a Azure AD DS

Gli account utente, le appartenenze ai gruppi e gli hash delle credenziali sono sincronizzati in un modo da Azure AD a Azure AD DS. Questo processo di sincronizzazione è automatico, Non è necessario configurare, monitorare o gestire questo processo di sincronizzazione. La sincronizzazione iniziale può richiedere alcune ore a un paio di giorni, a seconda del numero di oggetti nella directory Azure AD. Al termine della sincronizzazione iniziale, le modifiche apportate in Azure AD, ad esempio le modifiche alla password o agli attributi, vengono sincronizzate automaticamente con Azure AD DS.

Quando un utente viene creato in Azure AD, non viene sincronizzato con Azure AD DS fino a quando non viene modificata la password in Azure AD. Con questo processo di modifica delle password, in Azure AD vengono generati e archiviati gli hash delle password per l'autenticazione Kerberos e NTLM. Per autenticare un utente in Azure AD DS sono necessari gli hash delle password.

Il processo di sincronizzazione è unidirezionale/unidirezionale per progettazione. Non è possibile eseguire la sincronizzazione inversa delle modifiche da Azure AD DS a Azure AD. Un dominio gestito è in gran parte di sola lettura, tranne che per le unità organizzative personalizzate che è possibile creare. Non è possibile modificare gli attributi utente, le password utente o le appartenenze ai gruppi all'interno di un dominio gestito.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Sincronizzazione degli attributi e mapping a Azure AD DS

La tabella seguente elenca alcuni attributi comuni e il modo in cui vengono sincronizzati con Azure AD DS.

| Attributo in Azure AD DS | Source (Sorgente) | Note |
|:--- |:--- |:--- |
| UPN | Attributo *UPN* dell'utente in Azure ad tenant | L'attributo UPN del tenant Azure AD viene sincronizzato così com'è per Azure AD DS. Il modo più affidabile per accedere a un dominio gestito consiste nell'usare l'UPN. |
| SAMAccountName | Attributo *mailNickname* dell'utente in Azure ad tenant o generato automaticamente | L'attributo *sAMAccountName* viene originato dall'attributo *mailNickname* nel tenant Azure ad. Se più account utente hanno lo stesso attributo *mailNickname* , il *sAMAccountName* viene generato automaticamente. Se il *mailNickname* o il prefisso *UPN* dell'utente è più lungo di 20 caratteri, *sAMAccountName* viene generato automaticamente per soddisfare il limite di 20 caratteri per gli attributi *sAMAccountName* . |
| Password | Password dell'utente dal tenant Azure AD | Gli hash delle password legacy necessari per l'autenticazione NTLM o Kerberos sono sincronizzati dal tenant Azure AD. Se il tenant di Azure AD è configurato per la sincronizzazione ibrida con Azure AD Connect, questi hash delle password vengono originati dall'ambiente Servizi di dominio Active Directory locale. |
| SID utente/gruppo primario | Generato automaticamente | Il SID primario per gli account utente/di gruppo viene generato automaticamente in Azure AD DS. Questo attributo non corrisponde al SID utente/gruppo primario dell'oggetto in un ambiente di servizi di dominio Active Directory locale. Questa mancata corrispondenza è dovuta al fatto che il dominio gestito ha un diverso spazio dei nomi SID rispetto al dominio di servizi di dominio Active Directory locale. |
| Cronologia SID per utenti e gruppi | SID utente/gruppo primario locale | L'attributo *sIDHistory* per utenti e gruppi in Azure AD DS è impostato in modo da corrispondere al SID di gruppo o utente primario corrispondente in un ambiente di servizi di dominio Active Directory locale. Questa funzionalità consente di eseguire in modo Lift-and-Shift le applicazioni locali per Azure AD DS più semplice, perché non è necessario rieseguire l'ACL delle risorse. |

> [!TIP]
> **Accedere al dominio gestito usando il formato UPN** L'attributo *sAMAccountName* , ad esempio `AADDSCONTOSO\driley` , può essere generato automaticamente per alcuni account utente in un dominio gestito. Il *sAMAccountName* generato automaticamente dagli utenti può variare dal prefisso UPN, quindi non è sempre un modo affidabile per accedere.
>
> Ad esempio, se più utenti hanno lo stesso attributo *mailNickname* o se gli utenti hanno prefissi UPN eccessivamente lunghi, il *sAMAccountName* per questi utenti potrebbe essere generato automaticamente. Usare il formato UPN, ad esempio `driley@aaddscontoso.com` , per accedere in modo affidabile a un dominio gestito.

### <a name="attribute-mapping-for-user-accounts"></a>Mapping degli attributi per gli account utente

Nella tabella seguente viene illustrato il modo in cui gli attributi specifici per gli oggetti utente in Azure AD vengono sincronizzati con gli attributi corrispondenti in Azure AD DS.

| Attributo utente in Azure AD | Attributo utente in Azure AD DS |
|:--- |:--- |
| accountEnabled |userAccountControl (imposta o cancella il bit ACCOUNT_DISABLED) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| employeedId |employeeId |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (a volte può essere generato automaticamente) |
| manager |manager |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (imposta o cancella il bit DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| proxyAddresses | proxyAddresses |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Mapping degli attributi per i gruppi

Nella tabella seguente viene illustrato il modo in cui gli attributi specifici per gli oggetti gruppo in Azure AD vengono sincronizzati con gli attributi corrispondenti in Azure AD DS.

| Attributo Group in Azure AD | Attributo Group in Azure AD DS |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (a volte può essere generato automaticamente) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| proxyAddresses | proxyAddresses |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>Sincronizzazione da servizi di dominio Active Directory locali a Azure AD e Azure AD DS

Azure AD Connect viene usato per sincronizzare gli account utente, le appartenenze ai gruppi e gli hash delle credenziali da un ambiente Active Directory Domain Services locale a Azure AD. Gli attributi degli account utente, ad esempio UPN e l'ID di sicurezza locale (SID), sono sincronizzati. Per accedere con Azure AD DS, gli hash delle password legacy necessari per l'autenticazione NTLM e Kerberos sono sincronizzati anche con Azure AD.

> [!IMPORTANT]
> Azure AD Connect deve essere installato e configurato solo per la sincronizzazione con gli ambienti AD DS locali. Non è supportata l'installazione di Azure AD Connect in un dominio gestito per sincronizzare gli oggetti con Azure AD.

Se si configura il writeback, le modifiche apportate da Azure AD vengono sincronizzate di nuovo nell'ambiente di servizi di dominio Active Directory locale. Se, ad esempio, un utente modifica la password utilizzando Azure AD la gestione delle password self-service, la password viene aggiornata nuovamente nell'ambiente di servizi di dominio Active Directory locale.

> [!NOTE]
> Usare sempre la versione più recente di Azure AD Connect per ottenere le correzioni per tutti i bug noti.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronizzazione da un ambiente locale a più foreste

Molte organizzazioni dispongono di un ambiente Active Directory Domain Services piuttosto complesso che include più insiemi di strutture. Azure AD Connect supporta la sincronizzazione di utenti, gruppi e hash delle credenziali da ambienti a più foreste per Azure AD.

Azure AD dispone di uno spazio dei nomi molto più semplice e Flat. Per consentire agli utenti di accedere in modo affidabile ad applicazioni protette da Azure AD, risolvere i conflitti UPN tra gli account utente nelle varie foreste. I domini gestiti usano una struttura di unità organizzativa Flat, simile a Azure AD. Tutti gli account utente e i gruppi vengono archiviati nel contenitore *degli utenti di aaddc Computers* , anche se sono sincronizzati da domini o foreste locali diversi, anche se è stata configurata una struttura organizzativa gerarchica locale. Il dominio gestito rende Flat le strutture OU gerarchiche.

Come descritto in precedenza, non viene eseguita alcuna sincronizzazione tra Azure AD DS e Azure AD. È possibile [creare un'unità organizzativa (OU) personalizzata](create-ou.md) in Azure AD DS e quindi utenti, gruppi o account del servizio all'interno di tali unità organizzative personalizzate. Nessuno degli oggetti creati nelle unità organizzative personalizzate viene sincronizzato nuovamente con Azure AD. Questi oggetti sono disponibili solo all'interno del dominio gestito e non sono visibili con Azure AD cmdlet di PowerShell, Microsoft Graph API o usando l'interfaccia utente di gestione di Azure AD.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Cosa non viene sincronizzato con Azure AD DS

Gli oggetti o gli attributi seguenti non vengono sincronizzati da un ambiente Active Directory Domain Services locale per Azure AD o Azure AD DS:

* **Attributi esclusi:** È possibile scegliere di escludere determinati attributi dalla sincronizzazione per Azure AD da un ambiente Active Directory Domain Services locale usando Azure AD Connect. Questi attributi esclusi non sono quindi disponibili in Azure AD DS.
* **Criteri di gruppo:** I criteri di gruppo configurati in un ambiente di servizi di dominio Active Directory locale non vengono sincronizzati con Azure AD DS.
* **Cartella SYSVOL:** Il contenuto della cartella *SYSVOL* in un ambiente di servizi di dominio Active Directory locale non è sincronizzato con Azure AD DS.
* **Oggetti computer:** Gli oggetti computer per i computer aggiunti a un ambiente Servizi di dominio Active Directory locale non vengono sincronizzati con Azure AD DS. Questi computer non hanno una relazione di trust con il dominio gestito e appartengono solo all'ambiente di servizi di dominio Active Directory locale. In Azure AD DS vengono visualizzati solo gli oggetti computer per i computer che hanno aggiunto al dominio gestito in modo esplicito.
* **Attributi sIDHistory per utenti e gruppi:** L'utente primario e i SID del gruppo primario da un ambiente di servizi di dominio Active Directory locale vengono sincronizzati con Azure AD DS. Tuttavia, gli attributi *sIDHistory* esistenti per utenti e gruppi non vengono sincronizzati dall'ambiente Servizi di dominio Active Directory locale a Azure AD DS.
* **Strutture di unità organizzative (OU):** Le unità organizzative definite in un ambiente di servizi di dominio Active Directory locale non vengono sincronizzate con Azure AD DS. Sono disponibili due unità organizzative predefinite in Azure AD DS, una per gli utenti e una per i computer. Il dominio gestito ha una struttura di unità organizzativa flat. È possibile scegliere di [creare un'unità organizzativa personalizzata nel dominio gestito](create-ou.md).

## <a name="password-hash-synchronization-and-security-considerations"></a>Considerazioni sulla sicurezza e la sincronizzazione dell'hash delle password

Quando si Abilita Azure AD DS, sono necessari gli hash delle password legacy per l'autenticazione NTLM + Kerberos. Azure AD non archivia le password di testo non crittografato, pertanto non è possibile generare automaticamente questi hash per gli account utente esistenti. Una volta generati e archiviati, gli hash delle password compatibili con NTLM e Kerberos vengono sempre archiviati in modo crittografato in Azure AD.

Le chiavi di crittografia sono univoche per ogni tenant Azure AD. Questi hash sono crittografati in modo che solo Azure AD DS abbia accesso alle chiavi di decrittografia. Nessun altro servizio o componente in Azure AD ha accesso alle chiavi di decrittografia.

Gli hash delle password legacy vengono quindi sincronizzati da Azure AD nei controller di dominio per un dominio gestito. I dischi per i controller di dominio gestiti in Azure AD DS vengono crittografati a riposo. Questi hash delle password vengono archiviati e protetti in questi controller di dominio in modo analogo alla modalità di archiviazione e protezione delle password in un ambiente di servizi di dominio Active Directory locale.

Per gli ambienti di Azure AD solo cloud, [gli utenti devono reimpostare o modificare la password](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) affinché gli hash delle password richiesti vengano generati e archiviati in Azure ad. Per qualsiasi account utente del cloud creato in Azure AD dopo l'abilitazione di Azure AD Domain Services, gli hash delle password sono generati e archiviati in formati compatibili con NTLM e Kerberos. Tutti gli account utente cloud devono modificare la password prima di essere sincronizzati con Azure AD DS.

Per gli account utente ibridi sincronizzati dall'ambiente Servizi di dominio Active Directory locale usando Azure AD Connect, è necessario [configurare Azure ad Connect per sincronizzare gli hash delle password nei formati compatibili con NTLM e Kerberos](tutorial-configure-password-hash-sync.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle specifiche della sincronizzazione delle password, vedere funzionamento della [sincronizzazione dell'hash delle password con Azure ad Connect](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context).

Per iniziare a usare Azure AD DS, [creare un dominio gestito](tutorial-create-instance.md).
