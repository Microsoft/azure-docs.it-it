---
title: "Azure AD Connect: Configurare le autorizzazioni dell'account del connettore di AD DS | Microsoft Docs"
description: Questo documento illustra in dettaglio come configurare l'account del connettore di AD DS con il nuovo modulo di PowerShell ADSyncConfig
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeb80c3a94e63a886e4a16c0b8fa445b2a8a34e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72515810"
---
# <a name="azure-ad-connectconfigure-ad-ds-connector-account-permissions"></a>Azure AD Connect: Configurare le autorizzazioni dell'account del connettore di AD DS 

Il modulo di PowerShell denominato [ADSyncConfig.psm1](reference-connect-adsyncconfig.md) è stato introdotto con la build 1.1.880.0 (rilasciata nel mese di agosto 2018) che include una raccolta di cmdlet che consentono di configurare le autorizzazioni di Active Directory corrette per la distribuzione di Azure AD Connect. 

## <a name="overview"></a>Panoramica 
I cmdlet di PowerShell seguenti possono essere usati per configurare le autorizzazioni di Active Directory dell'account del connettore di Active Directory Domain Services, per ogni funzionalità che si intende selezionare in Azure AD Connect. Per evitare problemi, è consigliabile preparare le autorizzazioni di Active Directory in anticipo ogni volta che si vuole installare Azure AD Connect usando un account di dominio personalizzato per la connessione alla foresta. Questo modulo ADSyncConfig può essere usato anche per configurare le autorizzazioni dopo la distribuzione di Azure AD Connect.

![panoramica dell'account ds dell'annuncio](media/how-to-connect-configure-ad-ds-connector-account/configure1.png)

Per l'installazione di Azure AD Connect Express, viene creato un account generato automaticamente (MSOL_nnnnnnnnnn) in Active Directory con tutte le autorizzazioni necessarie, quindi non occorre usare questo modulo ADSyncConfig a meno che non si sia bloccata l'ereditarietà delle autorizzazioni per le unità organizzative o per oggetti specifici di Active Directory che si vuole sincronizzare con Azure AD. 
 
### <a name="permissions-summary"></a>Riepilogo delle autorizzazioni 
La tabella seguente offre un riepilogo delle autorizzazioni necessarie per gli oggetti di Active Directory: 

| Funzionalità | Autorizzazioni |
| --- | --- |
| funzionalità ms-DS-ConsistencyGuid |Autorizzazioni di lettura e scrittura per l'attributo ms-DS-ConsistencyGuid documentato in Concetti di [progettazione - Utilizzo di ms-DS-ConsistencyGuid come sourceAnchor](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor). | 
| Sincronizzazione dell'hash delle password |<li>Replica modifiche directory</li>  <li>Replica modifiche directory - Tutto |
| Distribuzione ibrida di Exchange |Autorizzazioni di lettura e scrittura per gli attributi documentati nel [writeback ibrido](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) di Exchange per utenti, gruppi e contatti. |
| Cartelle pubbliche della posta di Exchange |Autorizzazioni di lettura per gli attributi documentati in [Cartelle pubbliche della posta di Exchange](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) per le cartelle pubbliche. | 
| writeback delle password |Autorizzazioni di lettura e scrittura per gli attributi documentati in [Introduzione alla gestione delle password](../authentication/howto-sspr-writeback.md) per gli utenti. |
| Writeback dispositivi |Autorizzazioni di lettura e scrittura per gli oggetti dispositivo e i contenitori documentati nel [writeback del dispositivo](how-to-connect-device-writeback.md). |
| Writeback dei gruppi |Lettura, creazione, aggiornamento ed eliminazione di oggetti di gruppo per i **gruppi di Office 365** sincronizzati.  Per altre informazioni, vedere [Writeback dei gruppi](how-to-connect-preview.md#group-writeback).|

## <a name="using-the-adsyncconfig-powershell-module"></a>Uso del modulo di PowerShell ADSyncConfig 
Il modulo ADSyncConfig richiede [Strumenti di amministrazione remota del server per AD DS](https://docs.microsoft.com/windows-server/remote/remote-server-administration-tools) perché dipende dal modulo e dagli strumenti di PowerShell di AD DS. Per installare gli Strumenti di amministrazione remota del server per AD DS, aprire una finestra di Windows PowerShell con 'Esegui come amministratore' ed eseguire: 

``` powershell
Install-WindowsFeature RSAT-AD-Tools 
```
![Configurare](media/how-to-connect-configure-ad-ds-connector-account/configure2.png)

>[!NOTE]
>È anche possibile copiare il file **C:\Programmi\Microsoft Azure Active Directory Connect\AdSyncConfig\ADSyncConfig.psm1** in un controller di dominio in cui sono già installati gli Strumenti di amministrazione remota del server per AD DS e usare questo modulo di PowerShell da tale posizione.

Per iniziare a usare ADSyncConfig, è necessario caricare il modulo in una finestra di Windows PowerShell: 

``` powershell
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1" 
```

Per controllare tutti i cmdlet inclusi in questo modulo è possibile digitare:  

``` powershell
Get-Command -Module AdSyncConfig  
```

![Controllo](media/how-to-connect-configure-ad-ds-connector-account/configure3.png)

Ogni cmdlet ha gli stessi parametri per l'input dell'account del connettore AD DS e un'opzione AdminSDHolder. Per specificare l'account del connettore di AD DS, è possibile specificare il nome e il dominio dell'account oppure solo il nome distinto,

ad esempio:

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <ADAccountName> -ADConnectorAccountDomain <ADDomainName>
```

oppure

```powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <ADAccountDN>
```

Assicurarsi di `<ADAccountName>` `<ADDomainName>` sostituire `<ADAccountDN>` e con i valori appropriati per l'ambiente.

Nel caso in cui non si vogliano modificare le autorizzazioni per il contenitore AdminSDHolder, usare l'opzione `-SkipAdminSdHolders`. 

Per impostazione predefinita, tutti i cmdlet per impostare le autorizzazioni proveranno a impostare le autorizzazioni di AD DS per la radice di ogni dominio nella foresta. Questo significa che l'utente che esegue la sessione di PowerShell richiede diritti di amministratore di dominio per ogni dominio nella foresta.  Tenendo conto di questo requisito, è consigliabile usare un amministratore dell'organizzazione dalla radice della foresta. Se la distribuzione di Azure AD Connect ha più connettori di AD DS, sarà necessario eseguire lo stesso cmdlet in ogni foresta che abbia un connettore di AD DS. 

È anche possibile impostare le autorizzazioni per un'unità organizzativa o un oggetto di AD DS specifici usando il parametro `-ADobjectDN` seguito dal nome distinto dell'oggetto di destinazione in cui si vogliono impostare le autorizzazioni. Quando si usa un ADobjectDN di destinazione, il cmdlet imposterà le autorizzazioni solo per l'oggetto e non nel dominio radice o nel contenitore AdminSDHolder. Questo parametro può essere utile in presenza di unità organizzative o oggetti di AD DS particolari con l'ereditarietà delle autorizzazioni disabilitata (vedere Individuare gli oggetti di AD DS con l'ereditarietà delle autorizzazioni disabilitata). 

Le eccezioni per questi parametri comuni sono il cmdlet `Set-ADSyncRestrictedPermissions` usato per impostare le autorizzazioni per l'account del connettore di AD DS stesso e il cmdlet `Set-ADSyncPasswordHashSyncPermissions`, dato che le autorizzazioni necessarie per la sincronizzazione degli hash delle password vengono impostate solo in corrispondenza della radice del dominio, per cui questo cmdlet non include i parametri `-ObjectDN` o `-SkipAdminSdHolders`.

### <a name="determine-your-ad-ds-connector-account"></a>Determinare l'account del connettore di AD DS 
Se Azure AD Connect è già installato e si vuole verificare qual è l'account del connettore di AD DS attualmente usato da Azure AD Connect, è possibile eseguire il cmdlet: 

``` powershell
Get-ADSyncADConnectorAccount 
```
### <a name="locate-ad-ds-objects-with-permission-inheritance-disabled"></a>Individuare gli oggetti di AD DS con l'ereditarietà delle autorizzazioni disabilitata 
Se si vuole verificare se sono presenti oggetti AD DS con l'ereditarietà delle autorizzazioni disabilitata, è possibile eseguire: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' 
```
Per impostazione predefinita, questo cmdlet cerca solo le unità organizzative con ereditarietà disabilitata, ma è possibile specificare altre classi di oggetti AD DS nel parametro `-ObjectClass` oppure usare '*' per tutte le classi di oggetto, come segue: 

``` powershell
Get-ADSyncObjectsWithInheritanceDisabled -SearchBase '<DistinguishedName>' -ObjectClass * 
```
 
### <a name="view-ad-ds-permissions-of-an-object"></a>Visualizzare le autorizzazioni di AD DS di un oggetto 
È possibile utilizzare il cmdlet seguente per visualizzare l'elenco delle autorizzazioni attualmente impostate su un oggetto Active Directory fornendo il relativo DistinguishedName: 

``` powershell
Show-ADSyncADObjectPermissions -ADobjectDN '<DistinguishedName>' 
```

## <a name="configure-ad-ds-connector-account-permissions"></a>Configurare le autorizzazioni dell'account del connettore di AD DS 
 
### <a name="configure-basic-read-only-permissions"></a>Configurare le autorizzazioni di sola lettura di base 
Per impostare le autorizzazioni di sola lettura di base per l'account del connettore di AD DS quando non si usa alcuna funzionalità di Azure AD Connect, eseguire: 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


oppure 

``` powershell
Set-ADSyncBasicReadPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```


Questo cmdlet imposterà le autorizzazioni seguenti: 
 

|Type |Nome |Accesso |Si applica a| 
|-----|-----|-----|-----|
|Allow |Account del connettore di AD DS |Leggi tutte le proprietà |Oggetti dispositivo discendenti| 
|Allow |Account del connettore di AD DS|Leggi tutte le proprietà |Oggetti InetOrgPerson discendenti| 
|Allow |Account del connettore di AD DS |Leggi tutte le proprietà |Oggetti Computer discendenti| 
|Allow |Account del connettore di AD DS |Leggi tutte le proprietà |Oggetti foreignSecurityPrincipal discendenti| 
|Allow |Account del connettore di AD DS |Leggi tutte le proprietà |Oggetti Group discendenti| 
|Allow |Account del connettore di AD DS |Leggi tutte le proprietà |Oggetti User discendenti| 
|Allow |Account del connettore di AD DS |Leggi tutte le proprietà |Oggetti Contact discendenti| 

 
### <a name="configure-ms-ds-consistency-guid-permissions"></a>Configurare le autorizzazioni MS-DS-Consistency-Guid 
Per impostare le autorizzazioni per l'account del connettore di AD DS quando si usa l'attributo ms-Ds-Consistency-Guid come ancoraggio di origine (ovvero l'opzione "Consenti ad Azure di gestire automaticamente l'ancoraggio di origine"), eseguire: 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```

oppure 

``` powershell
Set-ADSyncMsDsConsistencyGuidPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Questo cmdlet imposterà le autorizzazioni seguenti: 

|Type |Nome |Accesso |Si applica a|
|-----|-----|-----|-----| 
|Allow|Account del connettore di AD DS|Lettura/scrittura proprietà|Oggetti User discendenti|

### <a name="permissions-for-password-hash-synchronization"></a>Autorizzazioni per la sincronizzazione degli hash delle password 
Per impostare le autorizzazioni per l'account del connettore di AD DS quando si usa la sincronizzazione degli hash delle password, eseguire: 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [<CommonParameters>] 
```


oppure 

``` powershell
Set-ADSyncPasswordHashSyncPermissions -ADConnectorAccountDN <String> [<CommonParameters>] 
```

Questo cmdlet imposterà le autorizzazioni seguenti: 

|Type |Nome |Accesso |Si applica a|
|-----|-----|-----|-----| 
|Allow |Account del connettore di AD DS |Replica modifiche directory |Solo questo oggetto (radice dominio)| 
|Allow |Account del connettore di AD DS |Replica di tutte le modifiche directory |Solo questo oggetto (radice dominio)| 
  
### <a name="permissions-for-password-writeback"></a>Autorizzazioni per il writeback delle password 
Per impostare le autorizzazioni per l'account del connettore di AD DS quando si usa il writeback delle password, eseguire: 

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


oppure

``` powershell
Set-ADSyncPasswordWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Questo cmdlet imposterà le autorizzazioni seguenti: 

|Type |Nome |Accesso |Si applica a|
|-----|-----|-----|-----| 
|Allow |Account del connettore di AD DS |Reimpostare la password |Oggetti User discendenti| 
|Allow |Account del connettore di AD DS |Scrittura proprietà lockoutTime |Oggetti User discendenti| 
|Allow |Account del connettore di AD DS |Scrittura proprietà pwdLastSet |Oggetti User discendenti| 

### <a name="permissions-for-group-writeback"></a>Autorizzazioni per il writeback dei gruppi 
Per impostare le autorizzazioni per l'account del connettore di AD DS quando si usa il writeback dei gruppi, eseguire: 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```
oppure 

``` powershell
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>]
```
 
Questo cmdlet imposterà le autorizzazioni seguenti: 

|Type |Nome |Accesso |Si applica a|
|-----|-----|-----|-----| 
|Allow |Account del connettore di AD DS |Lettura/scrittura generica |Tutti gli attributi del gruppo di tipi di oggetto e degli oggetti secondari| 
|Allow |Account del connettore di AD DS |Crea/Elimina oggetti figli |Tutti gli attributi del gruppo di tipi di oggetto e degli oggetti secondari| 
|Allow |Account del connettore di AD DS |Elimina/Elimina oggetti albero|Tutti gli attributi del gruppo di tipi di oggetto e degli oggetti secondari|

### <a name="permissions-for-exchange-hybrid-deployment"></a>Autorizzazioni per una distribuzione ibrida di Exchange 
Per impostare le autorizzazioni per l'account del connettore di AD DS quando si usa una distribuzione ibrida di Exchange, eseguire: 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


oppure 

``` powershell
Set-ADSyncExchangeHybridPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```

Questo cmdlet imposterà le autorizzazioni seguenti:  
 

|Type |Nome |Accesso |Si applica a|
|-----|-----|-----|-----| 
|Allow |Account del connettore di AD DS |Lettura/scrittura di tutte le proprietà |Oggetti User discendenti| 
|Allow |Account del connettore di AD DS |Lettura/scrittura di tutte le proprietà |Oggetti InetOrgPerson discendenti| 
|Allow |Account del connettore di AD DS |Lettura/scrittura di tutte le proprietà |Oggetti Group discendenti| 
|Allow |Account del connettore di AD DS |Lettura/scrittura di tutte le proprietà |Oggetti Contact discendenti| 

### <a name="permissions-for-exchange-mail-public-folders-preview"></a>Autorizzazioni per le cartelle pubbliche della posta di Exchange (anteprima) 
Per impostare le autorizzazioni per l'account del connettore di AD DS quando si usa la funzionalità Cartelle pubbliche della posta di Exchange, eseguire: 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountName <String> -ADConnectorAccountDomain <String> [-SkipAdminSdHolders] [<CommonParameters>] 
```


oppure 

``` powershell
Set-ADSyncExchangeMailPublicFolderPermissions -ADConnectorAccountDN <String> [-ADobjectDN <String>] [<CommonParameters>] 
```
Questo cmdlet imposterà le autorizzazioni seguenti: 

|Type |Nome |Accesso |Si applica a|
|-----|-----|-----|-----| 
|Allow |Account del connettore di AD DS |Leggi tutte le proprietà |Oggetti PublicFolder discendenti| 

### <a name="restrict-permissions-on-the-ad-ds-connector-account"></a>Limitare le autorizzazioni per l'account del connettore di AD DS 
Questo script di PowerShell limiterà le autorizzazioni per l'account del connettore di AD DS specificato come parametro. La limitazione delle autorizzazioni prevede i passaggi seguenti: 

- Disabilitare l'ereditarietà nell'oggetto specificato 
- Rimuovere tutte le voci ACE per l'oggetto specifico, ad eccezione delle voci ACE specifiche per SELF, perché si vogliono mantenere intatte le autorizzazioni predefinite per SELF. 
 
  Il parametro -ADConnectorAccountDN è l'account di AD per cui devono essere limitate le autorizzazioni. Si tratta in genere dell'account di dominio MSOL_nnnnnnnnnnnn configurato nel connettore di AD DS (vedere Determinare l'account del connettore di AD DS). Il parametro -Credential è necessario per specificare l'account amministratore con i privilegi necessari per limitare le autorizzazioni di Active Directory per l'oggetto di AD di destinazione. Si tratta in genere dell'amministratore di dominio o dell'organizzazione.  

``` powershell
Set-ADSyncRestrictedPermissions [-ADConnectorAccountDN] <String> [-Credential] <PSCredential> [-DisableCredentialValidation] [-WhatIf] [-Confirm] [<CommonParameters>] 
```
 
Ad esempio: 

``` powershell
$credential = Get-Credential 
Set-ADSyncRestrictedPermissions -ADConnectorAccountDN'CN=ADConnectorAccount,CN=Users,DC=Contoso,DC=com' -Credential $credential  
```

Questo cmdlet imposterà le autorizzazioni seguenti: 

|Type |Nome |Accesso |Si applica a|
|-----|-----|-----|-----| 
|Allow |SYSTEM |Controllo completo |Questo oggetto 
|Allow |Enterprise Admins |Controllo completo |Questo oggetto 
|Allow |Domain Admins |Controllo completo |Questo oggetto 
|Allow |Administrators |Controllo completo |Questo oggetto 
|Allow |Controller di dominio organizzazione |Contenuto elenco |Questo oggetto 
|Allow |Controller di dominio organizzazione |Leggi tutte le proprietà |Questo oggetto 
|Allow |Controller di dominio organizzazione |Autorizzazioni di lettura |Questo oggetto 
|Allow |Utenti autenticati |Contenuto elenco |Questo oggetto 
|Allow |Utenti autenticati |Leggi tutte le proprietà |Questo oggetto 
|Allow |Utenti autenticati |Autorizzazioni di lettura |Questo oggetto 

## <a name="next-steps"></a>Passaggi successivi
- [Azure AD Connect: account e autorizzazioni](reference-connect-accounts-permissions.md)
- [Installazione rapida](how-to-connect-install-express.md)
- [Installazione personalizzata](how-to-connect-install-custom.md)
- [Informazioni di riferimento su ADSyncConfig](reference-connect-adsyncconfig.md)

