---
title: 'Azure AD Connect: Imposizione di TLS 1.2 per Azure Active Directory Connect| Microsoft Docs'
description: Informazioni su come forzare il server di Azure AD Connect a usare solo Transport Layer Security (TLS) 1,2.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c610eb8fe6caf308117fa7adfc6e2f2a35c6352
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325896"
---
# <a name="tls-12-enforcement-for-azure-ad-connect"></a>Imposizione di TLS 1.2 per Azure AD Connect

Il protocollo TLS (Transport Layer Security) versione 1.2 è un protocollo di crittografia progettato per consentire le comunicazioni sicure.  Lo scopo principale del protocollo TLS è garantire la privacy e l'integrità dei dati.  TLS è stato sottoposto a numerose iterazioni con la versione 1.2 definita nell'[RFC 5246](https://tools.ietf.org/html/rfc5246).  Azure Active Directory Connect versione 1.2.65.0 e successive ora supporta completamente l'uso esclusivo di TLS 1.2 per le comunicazioni con Azure.  Questo documento fornirà informazioni su come forzare il server Azure AD Connect a usare solo TLS 1.2.

## <a name="update-the-registry"></a>Aggiornare il Registro di sistema
Per forzare il server Azure AD Connect a usare solo TLS 1.2, è necessario aggiornare il Registro di sistema del server Windows.  Impostare le chiavi del Registro di sistema seguenti nel server Azure AD Connect.

>[!IMPORTANT]
>Dopo aver aggiornato il Registro di sistema, è necessario riavviare il server Windows per applicare le modifiche.


### <a name="enable-tls-12"></a>Abilitare TLS 1.2
- [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\ . NETFramework\v4.0.30319
  - "Systemdefaulttlsversions su" = DWORD: 00000001
  - "SchUseStrongCrypto" = DWORD: 0000001
- [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\ . NETFramework\v4.0.30319
  - "Systemdefaulttlsversions su" = DWORD: 00000001
  - "SchUseStrongCrypto"=dword:00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Server]
  - "Enabled" = DWORD: 00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Server]
  - "DisabledByDefault" = DWORD: 00000000 
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client]
  - "Enabled" = DWORD: 00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client]
  - "DisabledByDefault" = DWORD: 00000000

### <a name="powershell-script-to-enable-tls-12"></a>Script di PowerShell per abilitare TLS 1.2
È possibile usare lo script di PowerShell seguente per abilitare TLS 1.2 nel server Azure AD Connect.

```powershell
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '1' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'Enabled' -value '1' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'DisabledByDefault' -value 0 -PropertyType 'DWord' -Force | Out-Null
    
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'Enabled' -value '1' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'DisabledByDefault' -value 0 -PropertyType 'DWord' -Force | Out-Null
    Write-Host 'TLS 1.2 has been enabled.'
```

### <a name="disable-tls-12"></a>Disabilitare TLS 1.2
- [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\ . NETFramework\v4.0.30319
  - "Systemdefaulttlsversions su" = DWORD: 00000000
  - "SchUseStrongCrypto" = DWORD: 0000000
- [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\ . NETFramework\v4.0.30319
  - "Systemdefaulttlsversions su" = DWORD: 00000000
  - "SchUseStrongCrypto" = DWORD: 00000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Server]
  - "Enabled" = DWORD: 00000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Server]
  - "DisabledByDefault" = DWORD: 00000001
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client]
  - "Enabled" = DWORD: 00000000
- [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client]
  - "DisabledByDefault" = DWORD: 00000001 

### <a name="powershell-script-to-disable-tls-12"></a>Script di PowerShell per disabilitare TLS 1.2
È possibile usare lo script di PowerShell seguente per disabilitare TLS 1.2 nel server Azure AD Connect.

```powershell
    New-Item 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SystemDefaultTlsVersions' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-ItemProperty -path 'HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319' -name 'SchUseStrongCrypto' -value '0' -PropertyType 'DWord' -Force | Out-Null

    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'Enabled' -value '0' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server' -name 'DisabledByDefault' -value 1 -PropertyType 'DWord' -Force | Out-Null
    
    New-Item 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'Enabled' -value '0' -PropertyType 'DWord' -Force | Out-Null
    
    New-ItemProperty -path 'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client' -name 'DisabledByDefault' -value 1 -PropertyType 'DWord' -Force | Out-Null
    Write-Host 'TLS 1.2 has been disabled.'
```

## <a name="next-steps"></a>Passaggi successivi
* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)
