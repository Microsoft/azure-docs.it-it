---
title: Diagnosticare gli errori con il servizio connesso di Azure AD (Visual Studio)
description: Il servizio connesso di Active Directory ha rilevato un tipo di autenticazione incompatibile.
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 7b437e3117540719c8c0adc5701ac1a5e934340b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88114473"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnosi degli errori con il servizio connesso di Azure Active Directory

Durante il rilevamento del codice di autenticazione precedente, il servizio connesso di Azure Active Directory ha rilevato un tipo di autenticazione non compatibile.

Per rilevare correttamente il precedente codice di autenticazione in un progetto, è necessario che il progetto sia ricompilato. Se si verifica questo errore e non si ha il precedente codice di autenticazione del progetto, ricompilare e riprovare.

## <a name="project-types"></a>Tipi di progetto

Il servizio connesso verifica il tipo di progetto in corso di sviluppo, in modo da potervi inserire la logica di autenticazione corretta. Se nel progetto è presente un controller che deriva da `ApiController`, il progetto verrà considerato come un progetto WebAPI. Se nel progetto sono presenti solo controller che derivano da `MVC.Controller`, il progetto verrà considerato come un progetto MVC. Il servizio connesso non supporta altri tipi di progetto.

## <a name="compatible-authentication-code"></a>Codice di autenticazione compatibile

Il servizio connesso cerca inoltre le impostazioni di autenticazione configurate in precedenza o compatibili. Se sono presenti tutte le impostazioni, viene considerato come caso rientrante. Il servizio connesso verrà aperto e visualizzerà le impostazioni.  Se sono presenti solo alcune impostazioni, verrà considerato come caso di errore.

In un progetto MVC il servizio connesso cerca le impostazioni seguenti che derivano da usi precedenti del servizio:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:AADInstance" value="" />
<add key="ida:PostLogoutRedirectUri" value="" />
```

Il servizio connesso cerca inoltre le impostazioni seguenti di un progetto API Web che derivano da usi precedenti del servizio:

```xml
<add key="ida:ClientId" value="" />
<add key="ida:Tenant" value="" />
<add key="ida:Audience" value="" />
```

## <a name="incompatible-authentication-code"></a>Codice di autenticazione incompatibile

Il servizio connesso prova infine a rilevare le versioni del codice di autenticazione configurate con le versioni precedenti di Visual Studio. Se si riceve questo errore, significa che il progetto contiene un tipo di autenticazione non compatibile. Il servizio connesso rileva i tipi seguenti di autenticazione delle versioni precedenti di Visual Studio:

* Autenticazione di Windows
* Account utente singoli
* Account dell'organizzazione

Per individuare Autenticazione di Windows in un progetto MVC, il servizio connesso cerca l'elemento `authentication` nel file `web.config`.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Per individuare Autenticazione di Windows in un progetto WebAPI, il servizio connesso cerca l'elemento `IISExpressWindowsAuthentication` nel file `.csproj` del progetto:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Per individuare l'autenticazione per singoli account utente, il servizio connesso cerca l'elemento package nel file `packages.config`.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Per individuare una precedente forma di autenticazione di tipo account aziendale, il servizio connesso cerca il seguente elemento nel file `web.config`:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Per cambiare il tipo di autenticazione, rimuovere il tipo non compatibile e provare ad aggiungere di nuovo il servizio connesso.

Per altre informazioni, vedere [Scenari di autenticazione per Azure AD](./authentication-vs-authorization.md).