---
title: Creare un'identità dell'app di Azure (PowerShell) | Azure
titleSuffix: Microsoft identity platform
description: Descrive come usare Azure PowerShell per creare un'applicazione Azure Active Directory e un'entità servizio e concedere l'accesso alle risorse tramite il controllo degli accessi in base al ruolo. Illustra come autenticare l'applicazione con un certificato.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.openlocfilehash: 423ec19d249d183f8888bf9e1eb837e2c860b1ed
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117142"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Procedure: Usare Azure PowerShell per creare un'entità servizio con un certificato

Quando si ha un'app o uno script che deve accedere alle risorse, è possibile configurare un'identità per l'app ed eseguirne l'autenticazione con credenziali specifiche. Questa identità è nota come entità servizio. Questo approccio consente di:

* Assegnare all'identità dell'app autorizzazioni diverse rispetto a quelle dell'utente. Tali autorizzazioni sono in genere limitate alle specifiche operazioni che devono essere eseguite dall'app.
* Usare un certificato per l'autenticazione in caso di esecuzione di uno script automatico.

> [!IMPORTANT]
> Anziché creare un'entità servizio, considerare l'uso delle identità gestite per le risorse di Azure per l'identità dell'applicazione. Se il codice viene eseguito in un servizio che supporta le identità gestite e accede alle risorse che supportano l'autenticazione di Azure AD, le identità gestite rappresentano un'opzione migliore. Per altre informazioni sulle identità gestite per le risorse di Azure, inclusi i servizi attualmente supportati, vedere [Informazioni sulle identità gestite per le risorse di Azure](../managed-identities-azure-resources/overview.md).

Questo articolo illustra come creare un'entità servizio che esegue l'autenticazione con un certificato. Per configurare un'entità servizio con password, vedere come [creare un'entità servizio di Azure con Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Per questo articolo è necessaria la [versione più recente](/powershell/azure/install-az-ps) di PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per completare questo articolo, è necessario avere autorizzazioni sufficienti sia nell'istanza di Azure Active Directory che nella sottoscrizione di Azure. In particolare, è necessario poter creare un'app in Azure Active Directory e assegnare l'entità servizio a un ruolo.

Il modo più semplice per verificare se l'account dispone delle autorizzazioni appropriate è tramite il portale. Vedere [Controllare le autorizzazioni necessarie](howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="assign-the-application-to-a-role"></a>Assegnare l'applicazione a un ruolo
Per accedere alle risorse della propria sottoscrizione è necessario assegnare l'applicazione a un ruolo. Decidere quale ruolo offre le autorizzazioni appropriate per l'applicazione. Per informazioni sui ruoli disponibili, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../../role-based-access-control/built-in-roles.md).

È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate a livelli inferiori dell'ambito. Ad esempio, l'aggiunta di un'applicazione al ruolo *lettore* per un gruppo di risorse significa che può leggere il gruppo di risorse e tutte le risorse in esso contenute. Per consentire all'applicazione di eseguire azioni come il riavvio, l'avvio e l'arresto delle istanze, selezionare il ruolo *collaboratore* .

## <a name="create-service-principal-with-self-signed-certificate"></a>Creare un'entità servizio con certificato autofirmato

L'esempio seguente illustra uno scenario semplice. USA [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) per creare un'entità servizio con un certificato autofirmato e USA [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) per assegnare il ruolo [lettore](../../role-based-access-control/built-in-roles.md#reader) all'entità servizio. L'ambito di assegnazione del ruolo corrisponde alla sottoscrizione di Azure selezionata. Per selezionare un'altra sottoscrizione, usare [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

> [!NOTE]
> Il cmdlet New-SelfSignedCertificate e il modulo PKI non sono attualmente supportati in PowerShell core. 

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $sp.ApplicationId
```

L'esempio viene sospeso per 20 secondi per consentire la propagazione della nuova entità servizio in Azure Active Directory. Se la durata dell'attesa dello script non è sufficiente, verrà visualizzato un errore simile al seguente: "L'entità di sicurezza {ID} non esiste nella directory {DIR-ID}". Per correggere l'errore, attendere qualche istante quindi eseguire di nuovo il comando **New-AzRoleAssignment**.

È possibile definire l'ambito di assegnazione di ruolo a un gruppo di risorse specifico tramite il parametro **ResourceGroupName**. È possibile definire l'ambito per una risorsa specifica usando anche i parametri **ResourceType** e **ResourceName**. 

Se **non si dispone di Windows 10 o Windows Server 2016**, è necessario scaricare il [generatore di certificati autofirmati](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) da Microsoft Script Center. Estrarre i contenuti e importare il cmdlet necessario.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Nello script sostituire le due righe seguenti per generare il certificato.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornire il certificato tramite uno script di PowerShell automatizzato

Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Azure Active Directory.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Creare un'entità servizio con certificato dell'autorità di certificazione

L'esempio seguente usa un certificato emesso da un'autorità di certificazione per creare un'entità servizio. L'ambito dell'assegnazione corrisponde alla sottoscrizione di Azure specificata. Aggiunge l'entità servizio al ruolo [Reader](../../role-based-access-control/built-in-roles.md#reader) . Se si verifica un errore durante l'assegnazione del ruolo, l'assegnazione viene ritentata.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornire il certificato tramite uno script di PowerShell automatizzato
Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Poiché l'ID applicazione e l'ID tenant non sono sensibili, è possibile incorporarli direttamente nello script. Se è necessario recuperare l'ID tenant, usare:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Se è necessario recuperare l'ID applicazione, usare:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Modificare le credenziali

Per modificare le credenziali per un'app AD, a causa di una violazione della sicurezza o della scadenza delle credenziali, usare i cmdlet [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) e [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential).

Per rimuovere tutte le credenziali per un'applicazione, usare:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Per aggiungere un valore del certificato, creare un certificato autofirmato come illustrato in questo articolo. Successivamente, usare:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Debug

Durante la creazione di un'entità servizio, è possibile riscontrare gli errori seguenti:

* **"Authentication_Unauthorized"** o **"Nessuna sottoscrizione trovata nel contesto".** - Questo errore viene visualizzato quando l'account non ha le [autorizzazioni necessarie](#required-permissions) in Azure Active Directory per registrare un'app. Questo errore viene in genere visualizzato quando solo gli utenti amministratori del Azure Active Directory possono registrare le app e l'account non è un amministratore. Richiedere all'amministratore di assegnare l'utente a un ruolo di amministratore o di consentire agli utenti di registrare le app.

* L'account **"non è autorizzato a eseguire l'azione 'Microsoft.Authorization/roleAssignments/write' nell'ambito '/subscriptions/{guid}'."** - Questo errore viene visualizzato quando l'account non dispone di autorizzazioni sufficienti per assegnare un ruolo a un'identità. Chiedere all'amministratore della sottoscrizione di essere aggiunti al ruolo Amministratore accessi utente.

## <a name="next-steps"></a>Passaggi successivi

* Per configurare un'entità servizio con password, vedere come [creare un'entità servizio di Azure con Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](app-objects-and-service-principals.md).
* Per altre informazioni sull'autenticazione di Azure Active Directory, vedere [Scenari di autenticazione per Azure AD](./authentication-vs-authorization.md).