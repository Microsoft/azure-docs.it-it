---
title: 'Azure AD Connect: Informazioni di riferimento per PowerShell ADConnectivityTools | Microsoft Docs'
description: Questo documento fornisce informazioni di riferimento per il modulo di PowerShell ADConnectivityTools.psm1.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d5e06d0a6f7693ca9cc69f05f6b2318d6b60b5e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89070803"
---
# <a name="azure-ad-connect--adconnectivitytools-powershell-reference"></a>Azure AD Connect: Informazioni di riferimento per PowerShell ADConnectivityTools

La documentazione seguente fornisce informazioni di riferimento per il modulo di PowerShell ADConnectivityTools.psm1 incluso in Azure AD Connect.

## <a name="confirm-dnsconnectivity"></a>Confirm-DnsConnectivity

### <a name="synopsis"></a>RIEPILOGO

Rileva i problemi di DNS locali.

### <a name="syntax"></a>SYNTAX

```
Confirm-DnsConnectivity [-Forest] <String> [-DCs] <Array> [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE

Esegue test di connettività DNS locali.
Per configurare il connettore Active Directory, l'utente deve avere sia il nome ResolutionThe per la foresta a cui sta tentando di connettersi che i controller di dominio associati a questa foresta.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>ESEMPIO 2

```powershell
Confirm-DnsConnectivity -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

Specifica il nome della foresta da testare.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Specifica i controller di dominio da testare.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Restituisce il risultato di questa diagnostica sotto forma di un oggetto PSObject.
Non necessario durante l'interazione manuale con questo strumento.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-forestexists"></a>Confirm-ForestExists

### <a name="synopsis"></a>RIEPILOGO

Determina se la foresta specificata esiste.

### <a name="syntax"></a>SYNTAX

```
Confirm-ForestExists [-Forest] <String> [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE

Esegue una query in un server DNS per gli indirizzi IP associati a una foresta.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

Specifica il nome della foresta da testare.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-functionallevel"></a>Confirm-FunctionalLevel

### <a name="synopsis"></a>RIEPILOGO

Verifica il livello funzionale della foresta AD.

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-FunctionalLevel -Forest <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-FunctionalLevel -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE

Verifica che il livello funzionale della foresta AD sia uguale o superiore a un determinato valore MinAdForestVersion (WindowsServer2003).
Potrebbero essere richiesti l'account (Dominio\Nome utente) e la password.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com"
```

#### <a name="example-2"></a>ESEMPIO 2

```powershell
Confirm-FunctionalLevel -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>ESEMPIO 3

```powershell
Confirm-FunctionalLevel -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

Foresta di destinazione.
Il valore predefinito è la foresta dell'utente attualmente connesso.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

Oggetto ForestFQDN di destinazione.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

La funzione userà le credenziali dell'utente attualmente connesso nel computer, invece di richiedere credenziali personalizzate all'utente.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-networkconnectivity"></a>Confirm-NetworkConnectivity

### <a name="synopsis"></a>RIEPILOGO

Rileva i problemi di connettività di rete locale.

### <a name="syntax"></a>SYNTAX

```
Confirm-NetworkConnectivity [-DCs] <Array> [-SkipDnsPort] [-ReturnResultAsPSObject] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE

Esegue test di connettività di rete locale.

Per i test di rete locale, AAD Connect deve essere in grado di comunicare con i controller di dominio denominati sulle porte 53 (DNS) e 88 (Kerberos) 389 (LDAP). La maggior parte delle organizzazioni esegue il servizio DNS nei relativi controller di dominio e questo è il motivo per cui questo test è attualmente integrato.
La porta 53 deve essere ignorata se è stato specificato un altro server DNS.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1

```powershell
Confirm-NetworkConnectivity -SkipDnsPort -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>ESEMPIO 2

```powershell
Confirm-NetworkConnectivity -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM" -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-dcs"></a>-DCs

Specifica i controller di dominio da testare.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-skipdnsport"></a>-SkipDnsPort

Se l'utente non usa i servizi DNS forniti dal controller di dominio di accesso/sito Active Directory, potrebbe voler ignorare il controllo della porta 53.
L'utente deve comunque essere in grado di risolvere _.ldap._tcp.\<forestfqdn\>
affinché la configurazione del connettore Active Directory abbia esito positivo.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Restituisce il risultato di questa diagnostica sotto forma di un oggetto PSObject.
Non necessario durante l'interazione manuale con questo strumento.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-targetsarereachable"></a>Confirm-TargetsAreReachable

### <a name="synopsis"></a>RIEPILOGO

Determina se una foresta specificata e i controller di dominio associati sono raggiungibili.

### <a name="syntax"></a>SYNTAX

```
Confirm-TargetsAreReachable [-Forest] <String> [-DCs] <Array> [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE

Esegue test "ping" (se un computer può raggiungere un computer di destinazione attraverso la rete e/o Internet)

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM" -DCs "MYDC1.CONTOSO.COM","MYDC2.CONTOSO.COM"
```

#### <a name="example-2"></a>ESEMPIO 2

```powershell
Confirm-TargetsAreReachable -Forest "TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

Specifica il nome della foresta da testare.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Specifica i controller di dominio da testare.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validdomains"></a>Confirm-ValidDomains

### <a name="synopsis"></a>RIEPILOGO

Verifica che i domini nel nome di dominio completo della foresta ottenuto siano raggiungibili

### <a name="syntax"></a>SYNTAX

#### <a name="samaccount"></a>SamAccount

```
Confirm-ValidDomains [-Forest <String>] [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

#### <a name="forestfqdn"></a>ForestFQDN

```
Confirm-ValidDomains -ForestFQDN <Forest> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE

Verifica che tutti i domini nel nome di dominio completo della foresta ottenuto siano raggiungibili tentando di recuperare DomainGuid e DomainDN.
Potrebbero essere richiesti l'account (Dominio\Nome utente) e la password.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -Verbose
```

#### <a name="example-2"></a>ESEMPIO 2

```powershell
Confirm-ValidDomains -Forest "test.contoso.com" -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

#### <a name="example-3"></a>ESEMPIO 3

```powershell
Confirm-ValidDomains -ForestFQDN $ForestFQDN -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

Foresta di destinazione.

```yml
Type: String
Parameter Sets: SamAccount
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-forestfqdn"></a>-ForestFQDN

Oggetto ForestFQDN di destinazione.

```yml
Type: Forest
Parameter Sets: ForestFQDN
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

La funzione userà le credenziali dell'utente attualmente connesso nel computer, invece di richiedere credenziali personalizzate all'utente.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="confirm-validenterpriseadmincredentials"></a>Confirm-ValidEnterpriseAdminCredentials

### <a name="synopsis"></a>RIEPILOGO

Verifica se un utente ha le credenziali di amministratore aziendale.

### <a name="syntax"></a>SYNTAX

```
Confirm-ValidEnterpriseAdminCredentials [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE

Esegue una ricerca per verificare se l'utente specificato ha le credenziali di amministratore aziendale.
Potrebbero essere richiesti l'account (Dominio\Nome utente) e la password.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1

```powershell
Confirm-ValidEnterpriseAdminCredentials -DomainName test.contoso.com -Verbose
```

#### <a name="example-2"></a>ESEMPIO 2

```powershell
Confirm-ValidEnterpriseAdminCredentials -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

La funzione userà le credenziali dell'utente attualmente connesso nel computer, invece di richiedere credenziali personalizzate all'utente.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-domainfqdndata"></a>Get-DomainFQDNData

### <a name="synopsis"></a>RIEPILOGO

Recupera un DomainFQDN da una combinazione di account e password.

### <a name="syntax"></a>SYNTAX

```
Get-DomainFQDNData [[-DomainFQDNDataType] <String>] [-RunWithCurrentlyLoggedInUserCredentials]
 [-ReturnExceptionOnError] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE

Tenta di ottenere un oggetto domainFQDN dalle credenziali specificate.
Se l'oggetto domainFQDN è valido, verrà restituito un DomainFQDNName o RootDomainName, a seconda della scelta dell'utente.
Potrebbero essere richiesti l'account (Dominio\Nome utente) e la password.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1

```powershell
Get-DomainFQDNData -DomainFQDNDataType DomainFQDNName -Verbose
```

#### <a name="example-2"></a>ESEMPIO 2

```powershell
Get-DomainFQDNData -DomainFQDNDataType RootDomainName -RunWithCurrentlyLoggedInUserCredentials
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-domainfqdndatatype"></a>-DomainFQDNDataType

Tipo di dati desiderato che verrà recuperato.
Attualmente limitato a "DomainFQDNName" o "RootDomainName".

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

La funzione userà le credenziali dell'utente attualmente connesso nel computer, invece di richiedere credenziali personalizzate all'utente.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnexceptiononerror"></a>-ReturnExceptionOnError

Parametro ausiliario usato dalla funzione Start-NetworkConnectivityDiagnosisTools

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="get-forestfqdn"></a>Get-ForestFQDN

### <a name="synopsis"></a>RIEPILOGO

Recupera un ForestFQDN da una combinazione di account e password.

### <a name="syntax"></a>SYNTAX

```
Get-ForestFQDN [-Forest] <String> [-RunWithCurrentlyLoggedInUserCredentials] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE

Tenta di ottenere un ForestFQDN dalle credenziali specificate.
Potrebbero essere richiesti l'account (Dominio\Nome utente) e la password.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -Verbose
```

#### <a name="example-2"></a>ESEMPIO 2

```powershell
Get-ForestFQDN -Forest CONTOSO.MICROSOFT.COM -RunWithCurrentlyLoggedInUserCredentials -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

Foresta di destinazione. Il valore predefinito è il dominio dell'utente attualmente connesso.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-runwithcurrentlyloggedinusercredentials"></a>-RunWithCurrentlyLoggedInUserCredentials

La funzione userà le credenziali dell'utente attualmente connesso nel computer, invece di richiedere credenziali personalizzate all'utente.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

### <a name="synopsis"></a>RIEPILOGO

Funzione principale.

### <a name="syntax"></a>SYNTAX

```
Start-ConnectivityValidation [-Forest] <String> [-AutoCreateConnectorAccount] <Boolean> [[-UserName] <String>]
 [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE

Esegue tutti i meccanismi disponibili che verificano la validità delle credenziali di AD.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1

```powershell
Start-ConnectivityValidation -Forest "test.contoso.com" -AutoCreateConnectorAccount $True -Verbose
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

Foresta di destinazione.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-autocreateconnectoraccount"></a>-AutoCreateConnectorAccount

Per le installazioni personalizzate: flag che è $True se l'utente sceglie "Crea un nuovo account AD" nella finestra Account della foresta AD della procedura guidata di AAD Connect.
$False se l'utente sceglie "Usare l'account AD esistente".
Per installazioni rapide: il valore di questa variabile deve essere $True per le installazioni rapide.

```yml
Type: Boolean
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-username"></a>-UserName

Parametro che precompila il campo del nome utente quando vengono richieste le credenziali dell'utente.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).

## <a name="start-networkconnectivitydiagnosistools"></a>Start-NetworkConnectivityDiagnosisTools

### <a name="synopsis"></a>RIEPILOGO

Funzione principale per i test di connettività di rete.

### <a name="syntax"></a>SYNTAX

```
Start-NetworkConnectivityDiagnosisTools [[-Forest] <String>] [-Credentials] <PSCredential>
 [[-LogFileLocation] <String>] [[-DCs] <Array>] [-DisplayInformativeMessage] [-ReturnResultAsPSObject]
 [-ValidCredentials] [<CommonParameters>]
```

### <a name="description"></a>DESCRIZIONE

Esegue test di connettività di rete locale.

### <a name="examples"></a>ESEMPI

#### <a name="example-1"></a>ESEMPIO 1

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM"
```

#### <a name="example-2"></a>ESEMPIO 2

```powershell
Start-NetworkConnectivityDiagnosisTools -Forest "TEST.CONTOSO.COM" -DCs "DC1.TEST.CONTOSO.COM", "DC2.TEST.CONTOSO.COM"
```

### <a name="parameters"></a>PARAMETERS

#### <a name="-forest"></a>-Forest

Specifica il nome della foresta da testare.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-credentials"></a>-Credentials

Nome utente e password dell'utente che esegue il test.
Richiede lo stesso livello di autorizzazioni richiesto per eseguire la procedura guidata di Azure AD Connect.

```yml
Type: PSCredential
Parameter Sets: (All)
Aliases:

Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-logfilelocation"></a>-LogFileLocation

Specifica il percorso di un file di log che conterrà l'output di questa funzione.

```yml
Type: String
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-dcs"></a>-DCs

Specifica i controller di dominio da testare.

```yml
Type: Array
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-displayinformativemessage"></a>-DisplayInformativeMessage

Flag che consente di visualizzare un messaggio sullo scopo di questa funzione.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-returnresultaspsobject"></a>-ReturnResultAsPSObject

Restituisce il risultato di questa diagnostica sotto forma di un oggetto PSObject.
Non è necessario specificarlo durante l'interazione manuale con questo strumento.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="-validcredentials"></a>-ValidCredentials

Indica se le credenziali digitate dall'utente sono valide.
Non è necessario specificarlo durante l'interazione manuale con questo strumento.

```yml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Questo cmdlet supporta i parametri comuni seguenti: -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction e -WarningVariable.
Per altre informazioni, vedere about_CommonParameters (https://go.microsoft.com/fwlink/?LinkID=113216).
