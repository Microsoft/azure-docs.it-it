---
title: Fornire un proxy di & HttpClient (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come fornire un client HTTP e proxy personalizzato per la connessione ad Azure AD con Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: dbf08e23b2bc1f657363f69df55763437e6c8a90
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76695051"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Fornire un client HTTP e proxy personalizzato con MSAL.NET
Quando si [inizializza un'applicazione client pubblica](msal-net-initializing-client-applications.md), è possibile usare il metodo `.WithHttpClientFactory method` per fornire un client HTTP personalizzato.  Fornendo tale client, è possibile implementare scenari avanzati, come quelli per controllare in modo specifico un proxy HTTP, personalizzare le intestazioni dell'agente utente o forzare MSAL a usare un determinato cliente HTTP (ad esempio, in API o app Web ASP.NET Core).

## <a name="initialize-with-httpclientfactory"></a>Eseguire l'inizializzazione con HttpClientFactory
L'esempio seguente illustra come creare un elemento `HttpClientFactory` e quindi inizializzare un'applicazione client pubblica con tale elemento:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient e Xamarin iOS
Quando si usa Xamarin iOS, è consigliabile creare un client HTTP (`HttpClient`) che usi in modo esplicito il gestore basato su `NSURLSession` per iOS 7 e versioni successive. MSAL.NET crea automaticamente un `HttpClient` che usa `NSURLSessionHandler` per iOS 7 e versioni successive. Per altre informazioni, leggere la [documentazione di Xamarin iOS per HttpClient](/xamarin/cross-platform/macios/http-stack).