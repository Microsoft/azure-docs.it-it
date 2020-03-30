---
title: Controllo delle versioni dell'SDK di client e server
description: Elenco di SDK client e compatibilità con le versioni sdk del server per Servizi mobili e app per dispositivi mobili di Azure.List of client SDKs and compatibility with server SDK versions for Mobile Services and Azure Mobile Apps.
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: a9ba442c00ec2498139ee34a1ff7497c98f17ede
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80293476"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Controllo delle versioni client e server in App per dispositivi mobili e Servizi mobili

La versione più recente di Servizi mobili di Azure è la funzionalità **App per dispositivi mobili** del Servizio app di Azure.

Gli SDK del client e del server di App per dispositivi mobili in origine si basano su quelle in Servizi mobili, ma *non* sono compatibili tra loro.
È infatti necessario usare un SDK del client di *App per dispositivi mobili* con un SDK del server di *App per dispositivi mobili* e lo stesso vale per *Servizi mobili*. Questo contratto viene applicato tramite un valore di intestazione speciale utilizzato dagli SDK del client e del server, `ZUMO-API-VERSION`.

Nota: ogni volta che in questo documento si fa riferimento a un back-end di *Servizi mobili* , esso non deve necessariamente essere ospitato su Servizi mobili. È ora possibile eseguire la migrazione di un servizio mobile per l'esecuzione nel servizio app senza modifiche al codice, ma il servizio continuerà a usare le versioni di *Mobile Services* SDK.

## <a name="header-specification"></a>Specifica di intestazione
La chiave `ZUMO-API-VERSION` può essere specificata nell'intestazione HTTP o nella stringa di query. Il valore è una stringa di versione nel formato **x.y.z**.

Ad esempio:

`GET https://service.azurewebsites.net/tables/TodoItem`

HEADERS: ZUMO-API-VERSION: 2.0.0

`POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0`

## <a name="opting-out-of-version-checking"></a>Esclusione del controllo della versione
È possibile rifiutare esplicitamente il controllo della versione impostando un valore **true** per l'impostazione dell'app **MS_SkipVersionCheck**. Specificarlo nel file web.config o nella sezione Impostazioni applicazione del portale di Azure.

> [!NOTE]
> Esistono una serie di modifiche di comportamento tra Servizi mobili e App per dispositivi mobili, in particolare per quanto riguarda la sincronizzazione offline, l’autenticazione e le notifiche push. È consigliabile rifiutare esplicitamente solo il controllo della versione dopo aver completato un test per assicurarsi che queste modifiche del comportamento non interferiscano con le funzionalità dell'applicazione.

## <a name="azure-mobile-apps-client-and-server"></a><a name="2.0.0"></a>Client e server di App per dispositivi mobili di Azure
### <a name="mobile-apps-client-sdks"></a><a name="MobileAppsClients"></a>SDK client *per le app* per dispositivi mobili
Il controllo della versione è stata introdotta a partire dalle seguenti versioni dell’SDK del client per **App per dispositivi mobili di Azure**:

| Piattaforma client | Versione | Valore dell'intestazione della versione |
| --- | --- | --- |
| Client gestito (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>SDK del server di *App* per dispositivi mobili
Il controllo della versione è incluso nelle seguenti versioni dell’SDK del server:

| Piattaforma server | SDK | Intestazione della versione accettata |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Comportamento dei back-end di app per dispositivi mobili
| ZUMO-API-VERSION | Valore di MS_SkipVersionCheck | Risposta |
| --- | --- | --- |
| x.y.z o Null |True |200 - OK |
| Null |False/Non specificato |400 - Richiesta non valida |
| 1.x.y |False/Non specificato |400 - Richiesta non valida |
| 2.0.0-2.x.y |False/Non specificato |200 - OK |
| 3.0.0-3.x.y |False/Non specificato |400 - Richiesta non valida |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
