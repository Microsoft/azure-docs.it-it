---
title: Evitare ricaricamenti di pagina (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come evitare il ricaricamento delle pagine quando si acquisisce e si rinnovano i token in modo invisibile all'utente usando Microsoft Authentication Library per JavaScript (MSAL. js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084949"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Evitare il ricaricamento delle pagine quando si acquisisce e si rinnovano i token in modo invisibile all'utente con MSAL. js
Microsoft Authentication Library per JavaScript (MSAL. js) USA gli `iframe` elementi nascosti per acquisire e rinnovare i token in modo invisibile all'utente in background. Azure AD restituisce il token al redirect_uri registrato specificato nella richiesta di token (per impostazione predefinita, si tratta della pagina radice dell'app). Poiché la risposta è 302, viene restituito il codice HTML corrispondente al `redirect_uri` caricamento in. `iframe` In genere, l' `redirect_uri` app è la pagina radice e ne comporta il ricaricamento.

In altri casi, se la navigazione alla pagina radice dell'app richiede l'autenticazione, potrebbe causare errori o `iframe` `X-Frame-Options: deny` elementi annidati.

Poiché MSAL. js non è in grado di eliminare il 302 emesso dal Azure AD ed è necessario per elaborare il token restituito, `redirect_uri` non può impedire che il `iframe`venga caricato in.

Per evitare di ricaricare l'intera app o altri errori causati da questo problema, seguire queste soluzioni alternative.

## <a name="specify-different-html-for-the-iframe"></a>Specificare codice HTML diverso per l'iframe

Impostare la `redirect_uri` proprietà in config su una pagina semplice che non richiede l'autenticazione. È necessario assicurarsi che corrisponda al portale di Azure `redirect_uri` registrato in. Questa operazione non influirà sull'esperienza di accesso dell'utente, perché MSAL salva la pagina iniziale quando l'utente inizia il processo di accesso e reindirizza al percorso esatto dopo il completamento dell'accesso.

## <a name="initialization-in-your-main-app-file"></a>Inizializzazione nel file dell'app principale

Se l'app è strutturata in modo che sia presente un file JavaScript centrale che definisce l'inizializzazione dell'app, il routing e altri elementi, è possibile caricare in modo `iframe` condizionale i moduli dell'app a seconda che l'app venga caricata o meno. Ad esempio:

In AngularJS: app. js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

In angolare: app. Module. TS

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla [creazione di un'applicazione a singola pagina (Spa)](scenario-spa-overview.md) con MSAL. js.