---
title: Autenticazione basata su certificati-Azure Active Directory
description: Informazioni su come configurare l'autenticazione basata su certificati nel proprio ambiente
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 6db7037cbcad335db77784ecfa624f08e88b1e83
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744432"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Introduzione all'autenticazione basata su certificati di Azure Active Directory

L'autenticazione basata su certificati consente di essere autenticati da Azure Active Directory con un certificato client su un dispositivo Windows, Android o iOS quando si connette il proprio account Exchange online a:

- Applicazioni per dispositivi mobili Microsoft, come Microsoft Outlook e Microsoft Word
- Client Exchange ActiveSync (EAS)

La configurazione di questa funzionalità elimina la necessità di immettere una combinazione di nome utente e password in determinate applicazioni di posta e applicazioni Microsoft Office sul dispositivo mobile.

In questo argomento:

- Viene descritto come configurare e usare l'autenticazione basata su certificati per gli utenti dei tenant nei piani Office 365 Enterprise, Business, Education e US Government. Questa funzionalità è disponibile in anteprima nei piani Office 365 China, US Government Defense e US Government Federal.
- Si presuppone che siano già configurati un'[infrastruttura a chiave pubblica (PKI)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831740(v=ws.11)) e [AD FS](../hybrid/how-to-connect-fed-whatis.md).

## <a name="requirements"></a>Requisiti

Per configurare l'autenticazione basata su certificati, devono essere soddisfatte le istruzioni seguenti:

- L'autenticazione basata su certificati è supportata solo per gli ambienti federati per le applicazioni browser, i client nativi che usano le librerie di autenticazione moderna (ADAL) o MSAL. L'unica eccezione è Exchange Active Sync per Exchange Online (EXO) che può essere usato per gli account federati e per quelli gestiti.
- L'autorità di certificazione radice e tutte le autorità di certificazione intermedie devono essere configurate in Azure Active Directory.
- Ogni autorità di certificazione deve avere un elenco di revoche di certificati (Certificate Revocation List o CRL) a cui si possa fare riferimento tramite un URL Internet.
- È necessario che in Azure Active Directory sia configurata almeno un'autorità di certificazione. I passaggi correlati sono descritti nella sezione [Configure the certificate authorities](#step-2-configure-the-certificate-authorities) (Configurare le autorità di certificazione).
- Per i client Exchange ActiveSync, il certificato client deve avere l'indirizzo di posta elettronica instradabile dell'utente in Exchange Online nel nome dell'entità o nel valore del nome RFC822 del campo nome alternativo oggetto. Azure Active Directory esegue il mapping del valore RFC822 all'attributo dell'indirizzo Proxy nella directory.
- Il dispositivo client deve avere accesso ad almeno un'autorità di certificazione che emetta i certificati client.
- È necessario che al client sia stato rilasciato un certificato client per l'autenticazione.

>[!IMPORTANT]
>Le dimensioni massime di un CRL per Azure Active Directory per il download e la memorizzazione nella cache sono 20MB e il tempo necessario per scaricare il CRL non deve superare i 10 secondi.  Se Azure Active Directory non è in grado di scaricare un CRL, le autenticazioni basate su certificati che usano i certificati emessi dalla CA corrispondente avranno esito negativo. Le procedure consigliate per garantire che i file CRL rientrino nei vincoli di dimensione sono la conservazione delle durate dei certificati entro limiti ragionevoli e la pulizia dei certificati scaduti.

## <a name="step-1-select-your-device-platform"></a>Passaggio 1: Selezionare la piattaforma del dispositivo

Il primo passaggio per quanto riguarda la piattaforma del dispositivo è verificare i punti seguenti:

- Supporto delle applicazioni Office per dispositivi mobili
- Requisiti specifici di implementazione

Le informazioni correlate sono disponibili per le piattaforme dei dispositivi seguenti:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Passaggio 2: Configurare le autorità di certificazione

Per configurare le proprie autorità di certificazione in Azure Active Directory, caricare gli elementi seguenti per ogni autorità:

* La parte pubblica del certificato, nel formato *.cer*
* Gli URL Internet in cui si trovano gli elenchi di revoche di certificati (Certificate Revocation List o CRL)

Lo schema per un'autorità di certificazione ha un aspetto simile al seguente:

```csharp
    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }
```

Per la configurazione, è possibile usare [Azure Active Directory PowerShell versione 2](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0):

1. Avviare Windows PowerShell con privilegi amministrativi.
2. Installare il modulo Azure AD [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) o versione successiva.

```powershell
    Install-Module -Name AzureAD –RequiredVersion 2.0.0.33
```

Il primo passaggio di configurazione consiste nello stabilire una connessione con il tenant. Non appena viene stabilita la connessione al tenant è possibile rivedere, aggiungere, eliminare e modificare le autorità di certificazione attendibili definite nella directory.

### <a name="connect"></a>Connessione

Per stabilire una connessione con il tenant, usare il cmdlet [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0):

```azurepowershell
    Connect-AzureAD
```

### <a name="retrieve"></a>Recupero

Per recuperare le autorità di certificazione attendibili definite nella directory, usare il cmdlet [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0).

```azurepowershell
    Get-AzureADTrustedCertificateAuthority
```

### <a name="add"></a>Add

Per creare un'autorità di certificazione attendibile, usare il cmdlet [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) e l'attributo **crlDistributionPoint** per un valore corretto:

```azurepowershell
    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca
```

### <a name="remove"></a>Rimuovi

Per rimuovere un'autorità di certificazione attendibile, usare il cmdlet [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0):

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]
```

### <a name="modify"></a>Modifica

Per modificare un'autorità di certificazione attendibile, usare il cmdlet [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0):

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]
```

## <a name="step-3-configure-revocation"></a>Passaggio 3: Configurare la revoca

Per revocare un certificato client, Azure Active Directory recupera l'elenco di revoche di certificati (Certificate Revocation List o CRL) dagli URL caricati come parte delle informazioni sull'autorità di certificazione e li memorizza nella cache. L'ultimo timestamp di pubblicazione, ovvero la proprietà **Effective Date** (Data di validità), in CRL viene usato per assicurare la validità di CRL. Il CRL viene referenziato periodicamente per revocare l'accesso ai certificati che fanno parte dell'elenco.

Se è necessaria una revoca più immediata (ad esempio in caso di smarrimento del dispositivo da parte di un utente), il token di autorizzazione dell'utente può essere annullato. Per annullare il token di autorizzazione, impostare il campo **StsRefreshTokenValidFrom** per questo particolare utente usando Windows PowerShell. È necessario aggiornare il campo **StsRefreshTokenValidFrom** per ogni utente a cui revocare l'accesso.

Per fare in modo che la revoca persista, è necessario impostare la proprietà **Effective Date** (Data di validità) di CRL su una data successiva al valore impostato da **StsRefreshTokenValidFrom** e assicurarsi che il certificato in questione sia in CRL.

I passaggi seguenti illustrano il processo per aggiornare e annullare il token di autorizzazione impostando il campo **StsRefreshTokenValidFrom** .

**Per configurare la revoca:**

1. Connettersi con credenziali amministrative al servizio MSOL:

```powershell
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
```

2. Recuperare il valore StsRefreshTokensValidFrom corrente per un utente:

```powershell
        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom
```

3. Configurare un nuovo valore StsRefreshTokensValidFrom per l'utente uguale al timestamp corrente:

```powershell
        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")
```

La data impostata deve essere futura. Se la data non è futura, la proprietà **StsRefreshTokensValidFrom** non viene impostata. Se la data è futura, la proprietà **StsRefreshTokensValidFrom** viene impostata sull'ora corrente, non sulla data indicata dal comando Set-MsolUser.

## <a name="step-4-test-your-configuration"></a>Passaggio 4: Testare la configurazione

### <a name="testing-your-certificate"></a>Test del certificato

Come primo test di configurazione è consigliabile accedere a [Outlook Web Access](https://outlook.office365.com) o a [SharePoint Online](https://microsoft.sharepoint.com) usando il **browser sul dispositivo**.

Se l'accesso ha esito positivo significa che:

- È stato eseguito il provisioning del certificato utente al dispositivo di test
- AD FS è configurato correttamente

### <a name="testing-office-mobile-applications"></a>Test delle applicazioni Office per dispositivi mobili

**Per testare l'autenticazione basata su certificati sulla propria applicazione Office per dispositivi mobili:**

1. Nel dispositivo di test installare un'applicazione Office per dispositivi mobili (ad esempio, OneDrive).
3. Avviare l'applicazione.
4. Immettere il nome utente, quindi selezionare il certificato utente da usare.

È necessario aver eseguito l'accesso.

### <a name="testing-exchange-activesync-client-applications"></a>Test delle applicazioni client Exchange ActiveSync

Per accedere a Exchange ActiveSync (EAS) tramite l'autenticazione basata su certificati, l'applicazione deve avere a disposizione un profilo EAS contenente il certificato client.

Il profilo EAS deve contenere le informazioni seguenti:

- Il certificato utente da usare per l'autenticazione

- L'endpoint EAS (ad esempio outlook.office365.com)

Un profilo EAS può essere configurato e aggiunto al dispositivo tramite l'uso di software MDM, come Intune, o inserendo manualmente il certificato nel profilo EAS sul dispositivo.

### <a name="testing-eas-client-applications-on-android"></a>Test delle applicazioni client EAS in Android

**Per testare l'autenticazione basata su certificati:**

1. Configurare un profilo EAS nell'applicazione che soddisfi i requisiti riportati nella sezione precedente.
2. Aprire l'applicazione e verificare che venga eseguita la sincronizzazione dell'email.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sull'autenticazione basata su certificati nei dispositivi Android.](active-directory-certificate-based-authentication-android.md)

[Altre informazioni sull'autenticazione basata su certificati nei dispositivi iOS.](active-directory-certificate-based-authentication-ios.md)