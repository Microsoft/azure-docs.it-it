---
title: Gestione della configurazione per Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Procedure di mitigazione delle minacce esposte in Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 391851927d03a557483afa2656e315b28c613956
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322634"
---
# <a name="security-frame-configuration-management--mitigations"></a>Infrastruttura di sicurezza: Gestione della configurazione - Procedure di mitigazione 
| Prodotto o servizio | Articolo |
| --------------- | ------- |
| **Applicazione Web** | <ul><li>[Implementare Content Security Policy (CSP) e disabilitare il contenuto JavaScript inline](#csp-js)</li><li>[Abilitare il filtro XSS del browser](#xss-filter)</li><li>[Le applicazioni ASP.NET devono disabilitare la traccia e il debug prima della distribuzione](#trace-deploy)</li><li>[Accedere a contenuto JavaScript di terze parti solo da origini attendibili](#js-trusted)</li><li>[Assicurarsi che le pagine ASP.NET autenticate incorporino difese contro attacchi di tipo UI redress o click-jacking](#ui-defenses)</li><li>[Assicurarsi che siano consentite solo origini attendibili se CORS è abilitato nelle applicazioni Web ASP.NET](#cors-aspnet)</li><li>[Abilitare l'attributo ValidateRequest nelle pagine ASP.NET](#validate-aspnet)</li><li>[Usare le versioni più recenti ospitate in locale delle librerie JavaScript](#local-js)</li><li>[Disabilitare l'analisi MIME automatica](#mime-sniff)</li><li>[Rimuovere le intestazioni del server standard nei siti Web di Microsoft Azure per evitare la creazione di impronte digitali](#standard-finger)</li></ul> |
| **Database** | <ul><li>[Configurare Windows Firewall per l'accesso al motore di database](#firewall-db)</li></ul> |
| **API Web** | <ul><li>[Assicurarsi che siano consentite solo origini attendibili se CORS è abilitato nell'API Web ASP.NET](#cors-api)</li><li>[Crittografare le sezioni dei file di configurazione dell'API Web contenenti dati sensibili](#config-sensitive)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Assicurarsi che tutte le interfacce amministrative siano protette con credenziali sicure](#admin-strong)</li><li>[Assicurarsi che un codice sconosciuto non possa essere eseguito sui dispositivi](#unknown-exe)</li><li>[Crittografare il sistema operativo e altre partizioni del dispositivo IoT con bit-locker](#partition-iot)</li><li>[Assicurarsi che sui dispositivi siano abilitati solo servizi/funzionalità minime](#min-enable)</li></ul> |
| **Gateway IoT sul campo** | <ul><li>[Crittografare il sistema operativo e altre partizioni del gateway IoT sul campo con bit-locker](#field-bit-locker)</li><li>[Assicurarsi che le credenziali di accesso predefinite del gateway sul campo vengano modificate durante l'installazione](#default-change)</li></ul> |
| **Gateway IoT cloud** | <ul><li>[Assicurarsi che il gateway nel cloud implementi un processo per mantenere aggiornato il firmware dei dispositivi connessi](#cloud-firmware)</li></ul> |
| **Limite di Trust del computer** | <ul><li>[Assicurarsi che i dispositivi abbiano i controlli di sicurezza degli endpoint configurati in base ai criteri organizzativi](#controls-policies)</li></ul> |
| **Archiviazione di Azure** | <ul><li>[Assicurare una gestione sicura delle chiavi di accesso alle risorse di archiviazione di Azure](#secure-keys)</li><li>[Assicurarsi che siano consentite solo origini attendibili se CORS è abilitato in Archiviazione di Azure](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Abilitare la funzionalità di limitazione dei servizi di WCF](#throttling)</li><li>[Diffusione di informazioni di WCF tramite i metadati](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Implementare Content Security Policy (CSP) e disabilitare il contenuto JavaScript inline

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [An Introduction to Content Security Policy](https://www.html5rocks.com/en/tutorials/security/content-security-policy/) (Introduzione a Content Security Policy) [Content Security Policy Reference](https://content-security-policy.com/) (Informazioni di riferimento su Content Security Policy), [Security features](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/) (Funzionalità di sicurezza), [Introduction to content security policy](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy) (Introduzione a Content Security Policy), [(È possibile usare use CSP?)](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Passaggi** | <p>Content Security Policy (CSP) è un meccanismo di sicurezza avanzato, uno standard W3C, che consente ai proprietari di applicazioni Web di avere il controllo del contenuto incorporato nel sito. CSP viene aggiunto come intestazione della risposta HTTP nel server Web e viene applicato sul lato client dai browser. Si tratta di criteri basati su un elenco di elementi consentiti: un sito Web può dichiarare un set di domini attendibili da cui possono essere caricati contenuti attivi, ad esempio JavaScript.</p><p>CSP offre i seguenti vantaggi per la sicurezza:</p><ul><li>**Protezione da XSS:** Se una pagina è vulnerabile a XSS, un utente malintenzionato può sfruttarlo in 2 modi:<ul><li>Inserimento di `<script>malicious code</script>`. Questo exploit non funzionerà a causa della restrizione di base 1 di CSP</li><li>Inserimento di `<script src="http://attacker.com/maliciousCode.js"/>`. Questo exploit non funzionerà perché il dominio controllato dall'utente malintenzionato non sarà nell'elenco di domini consentiti di CSP</li></ul></li><li>**Controllo sull'esfiltrazione dei dati:** Se un contenuto dannoso in una pagina Web prova a connettersi a un sito Web esterno e a sottrarre dati, la connessione verrà interrotta da CSP. Infatti il dominio di destinazione non sarà nell'elenco elementi consentiti di CSP</li><li>**Difesa contro il click-jacking:** il click-jacking è una tecnica di attacco con la quale un antagonista può inserire in un frame un sito Web originale e forzare gli utenti a fare clic sugli elementi dell'interfaccia utente. Attualmente la difesa contro il click-jacking si basa sulla configurazione dell'intestazione della risposta X-Frame-Options. Non tutti i browser supportano questa intestazione e, con il passare del tempo, CSP diventerà uno dei modi standard per difendersi dal click-jacking</li><li>**Creazione di report sugli attacchi in tempo reale:** Se si verifica un attacco di tipo injection in un sito Web abilitato per CSP, i browser attiveranno automaticamente una notifica per un endpoint configurato sul server Web. In questo modo, CSP funge da sistema di avviso in tempo reale.</li></ul> |

### <a name="example"></a>Esempio
Criteri di esempio: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Questi criteri consentono il caricamento degli script solo dal server dell'applicazione Web e dal server Google Analytics. Gli script caricati da altri siti verranno rifiutati. Quando CSP viene abilitato in un sito Web, le funzionalità seguenti vengono automaticamente disabilitate per mitigare gli attacchi XSS. 

### <a name="example"></a>Esempio
Gli script inline non verranno eseguiti. Di seguito sono riportati esempi di script inline. 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>Esempio
Le stringhe non verranno valutate come codice. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>Abilitare il filtro XSS del browser

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [XSS Protection Filter](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) (Filtro di protezione XSS) |
| **Passaggi** | <p>La configurazione dell'intestazione della risposta X-XSS-Protection controlla il filtro di cross-site scripting del browser. Questa intestazione della risposta può avere i valori seguenti:</p><ul><li>`0:` Disabilita il filtro.</li><li>`1: Filter enabled` Se viene rilevato un attacco di tipo cross-site scripting, per arrestare l'attacco, il browser purifica la pagina.</li><li>`1: mode=block : Filter enabled`. Invece di purificare la pagina, quando viene rilevato un attacco XSS, il browser impedisce il rendering della pagina</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Il browser purificherà la pagina e segnalerà la violazione.</li></ul><p>Si tratta di una funzione di Chromium che utilizza i report sulle violazioni CSP per inviare i dettagli all'URI scelto. Le ultime 2 opzioni sono considerate valori sicuri.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>Le applicazioni ASP.NET devono disabilitare la traccia e il debug prima della distribuzione

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Panoramica sul debug di ASP.NET](https://msdn.microsoft.com/library/ms227556.aspx), [Panoramica sull'analisi di ASP.NET](https://msdn.microsoft.com/library/bb386420.aspx), [Informazioni su come: Abilitare l’analisi per un'applicazione ASP.NET](https://msdn.microsoft.com/library/0x5wc973.aspx), [Informazioni su come: Abilitare il debug per applicazioni ASP.NET](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Passaggi** | Quando l'analisi viene abilitata per la pagina, ogni browser che la richiede ottiene anche le informazioni di analisi contenenti i dati sul flusso di lavoro e sullo stato del server interno. Tali informazioni possono essere relative alla sicurezza. Quando il debug viene abilitato per la pagina, gli errori che si verificano sul server restituiscono dati di analisi dello stack completa presentati al browser. Tali dati possono esporre informazioni relative alla sicurezza sul flusso di lavoro del server. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Accedere a contenuto JavaScript di terze parti solo da origini attendibili

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | solo le origini attendibili devono fare riferimento al contenuto JavaScript di terze parti. Gli endpoint di riferimento devono sempre essere in TLS. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Assicurarsi che le pagine ASP.NET autenticate incorporino difese contro attacchi di tipo UI redress o click-jacking

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [OWASP click-jacking Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html) (Foglio informativo di OWASP sulla difesa contro il click-jacking), [IE Internals - Combating click-jacking With X-Frame-Options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) (IEInternals: lotta al click-jacking con X-Frame-Options) |
| **Passaggi** | <p>Il click-jacking, noto anche come "attacco di tipo UI redress", si verifica quando un utente malintenzionato usa più livelli trasparenti o opachi per ingannare un utente che vuole fare clic nella pagina principale e indurlo invece a fare clic su un pulsante o su un collegamento in un'altra pagina.</p><p>Questa sovrapposizione si ottiene creando una pagina dannosa con un iframe, che carica la pagina della vittima. L'utente malintenzionato assume quindi il controllo dei clic destinati alla pagina e li instrada a un'altra pagina, quasi certamente di proprietà di un'altra applicazione o dominio oppure di entrambi. Per impedire gli attacchi di tipo click-jacking, impostare le intestazioni della risposta HTTP X-Frame-Options appropriate che indicano al browser di non consentire l'inserimento in frame da altri domini</p>|

### <a name="example"></a>Esempio
L'intestazione X-FRAME-OPTIONS può essere impostata tramite file web.config IIS. Frammento di codice di Web.config che non devono mai essere inseriti in un frame: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Esempio
Codice di Web.config per siti che devono essere inseriti in un frame solo dalle pagine nello stesso dominio: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Assicurarsi che siano consentite solo origini attendibili se CORS è abilitato nelle applicazioni Web ASP.NET

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Web Form, MVC 5 |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>La sicurezza del browser impedisce a una pagina Web di creare richieste AJAX per un altro dominio. Questa restrizione è nota come criteri di corrispondenza dell'origine e impedisce a un sito dannoso di leggere dati sensibili da un altro sito. In alcuni casi può tuttavia essere necessario esporre in modo sicuro le API che gli altri siti possono utilizzare. Cross Origin Resource Sharing (CORS) è uno standard W3C che consente a un server di ridurre i criteri di corrispondenza dell'origine. Con CORS un server può consentire in modo esplicito alcune richieste multiorigine e rifiutarne altre.</p><p>CORS è più sicuro e flessibile delle tecniche precedenti, ad esempio JSONP. In sostanza, abilitando CORS sarà possibile aggiungere alcune intestazioni della risposta HTTP (Access-Control-*) all'applicazione Web in due modi.</p>|

### <a name="example"></a>Esempio
Se è disponibile l'accesso a Web.config, CORS può essere aggiunto tramite il codice seguente: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Esempio
Se non è disponibile l'accesso a Web.config, CORS può essere configurato aggiungendo il codice CSharp seguente: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Si noti che è fondamentale assicurarsi che l'elenco di origini nell'attributo "Access-Control-Allow-Origin" venga impostato su un set finito e attendibile di origini. Se la configurazione non è corretta (ad esempio, se si imposta il valore come "*"), i siti dannosi potranno attivare richieste multiorigine per l'applicazione Web senza alcuna restrizione, rendendo in questo modo l'applicazione vulnerabile agli attacchi CSRF. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>Abilitare l'attributo ValidateRequest nelle pagine ASP.NET

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Web Form, MVC 5 |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Request Validation - Preventing Script Attacks](https://www.asp.net/whitepapers/request-validation) (Convalida della richiesta: prevenzione degli attacchi basati su script) |
| **Passaggi** | <p>La convalida della richiesta, una funzionalità di ASP.NET sin dalla versione 1.1, impedisce al server di accettare contenuti che includono HTML non codificato. Questa funzionalità è progettata per impedire alcuni attacchi script injection in cui il codice script client o HTML può essere inconsapevolmente inviato a un server, archiviato e quindi presentato ad altri utenti. È tuttavia vivamente consigliabile convalidare tutti i dati di input e codificarli con HTML, se appropriato.</p><p>La convalida della richiesta viene eseguita confrontando tutti i dati di input con un elenco di valori potenzialmente pericolosi. Se viene rilevata una corrispondenza, ASP.NET genera `HttpRequestValidationException`. Per impostazione predefinita, la funzionalità di convalida della richiesta è abilitata.</p>|

### <a name="example"></a>Esempio
Questa funzionalità può essere tuttavia disabilitata a livello di pagina: 
```XML
<%@ Page validateRequest="false" %> 
```
o a livello di applicazione. 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Si noti che la funzionalità di convalida della richiesta non è supportata e non fa parte della pipeline MVC 6. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>Usare le versioni più recenti ospitate in locale delle librerie JavaScript

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>Gli sviluppatori che usano librerie JavaScript standard, ad esempio JQuery, devono usare versioni approvate delle comuni librerie JavaScript che non contengono difetti di sicurezza noti. È consigliabile usare la versione più recente delle librerie, perché contiene aggiornamenti della sicurezza per le vulnerabilità note delle versioni precedenti.</p><p>Se la versione più recente non può essere usata per motivi di compatibilità, è consigliabile usare la versione inferiore a quella minima.</p><p>Versioni minime accettabili:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Validate 1.9</li><li>JQuery Mobile 1.0.1</li><li>JQuery Cycle 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Ajax Control Toolkit 40412</li></ul></li><li>**Web Form ASP.NET e Ajax**<ul><li>Web Form ASP.NET e Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Non caricare mai librerie JavaScript da siti esterni, ad esempio reti CDN pubbliche.</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Disabilitare l'analisi MIME automatica

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Sicurezza di IE8 parte V: Protezione completa](https://docs.microsoft.com/archive/blogs/ie/ie8-security-part-v-comprehensive-protection), [Tipo MIME](https://en.wikipedia.org/wiki/Mime_type) |
| **Passaggi** | L'intestazione X-Content-Type-Options è un'intestazione HTTP che consente agli sviluppatori di specificare che il contenuto non deve essere sottoposto ad analisi MIME. Questa intestazione è progettata per mitigare gli attacchi basati sull'analisi MIME. Per ogni pagina che potrebbe includere contenuti controllabili dall'utente, è necessario usare l'intestazione HTTP X-Content-Type-Options:nosniff. Per abilitare l'intestazione necessaria a livello globale per tutte le pagine nell'applicazione, eseguire una di queste operazioni.|

### <a name="example"></a>Esempio
Aggiungere l'intestazione nel file web.config se l'applicazione è ospitata da Internet Information Services (IIS) 7 o versione successiva. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Esempio
Aggiungere l'intestazione tramite Application\_BeginRequest globale 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Esempio
Implementare un modulo HTTP personalizzato 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Esempio
È possibile abilitare l'intestazione necessaria solo per pagine specifiche aggiungendola a singole risposte: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Rimuovere le intestazioni del server standard nei siti Web di Microsoft Azure per evitare la creazione di impronte digitali

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | Tipo di ambiente: Azure |
| **Riferimenti**              | [Removing standard server headers on Windows Azure Web Sites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) (Rimozione di intestazioni del server standard nei siti Web di Microsoft Azure) |
| **Passaggi** | Intestazioni come Server, X-Powered-By e X-AspNet-Version rivelano informazioni sul server e sulle tecnologie sottostanti. È consigliabile eliminare queste intestazioni evitando così di creare impronte digitali dell'applicazione |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Configurare Windows Firewall per l'accesso al motore di database

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | SQL Azure, locale |
| **Attributes (Attributi)**              | N/D, versione SQL: V12 |
| **Riferimenti**              | [Come configurare un firewall del database SQL di Azure](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [configurare un Windows Firewall per l'accesso motore di database](https://msdn.microsoft.com/library/ms175043) |
| **Passaggi** | I sistemi firewall contribuiscono a impedire l'accesso non autorizzato alle risorse del computer. Per accedere a un'istanza del motore di database di SQL Server tramite un firewall, è necessario configurare il firewall sul computer che esegue SQL Server per consentire l'accesso. |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Assicurarsi che siano consentite solo origini attendibili se CORS è abilitato nell'API Web ASP.NET

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | MVC 5 |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Enabling Cross-Origin Requests in ASP.NET Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) (Abilitazione di richieste multiorigine nell'API Web ASP.NET 2), [API Web ASP.NET: supporto di CORS nell'API Web ASP.NET 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Passaggi** | <p>La sicurezza del browser impedisce a una pagina Web di creare richieste AJAX per un altro dominio. Questa restrizione è nota come criteri di corrispondenza dell'origine e impedisce a un sito dannoso di leggere dati sensibili da un altro sito. In alcuni casi può tuttavia essere necessario esporre in modo sicuro le API che gli altri siti possono utilizzare. Cross Origin Resource Sharing (CORS) è uno standard W3C che consente a un server di ridurre i criteri di corrispondenza dell'origine.</p><p>Con CORS un server può consentire in modo esplicito alcune richieste multiorigine e rifiutarne altre. CORS è più sicuro e flessibile delle tecniche precedenti, ad esempio JSONP.</p>|

### <a name="example"></a>Esempio
In App_Start/WebApiConfig.cs aggiungere il codice seguente al metodo WebApiConfig.Register. 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Esempio
L'attributo EnableCors può essere applicato ai metodi di azione in un controller, come segue: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Si noti che è fondamentale assicurarsi che l'elenco di origini nell'attributo EnableCors venga impostato su un set finito e attendibile di origini. Se la configurazione non è corretta (ad esempio, se si imposta il valore come "*"), i siti dannosi potranno attivare richieste multiorigine per l'API senza alcuna restrizione, rendendo in questo modo l'API vulnerabile agli attacchi CSRF. EnableCors può essere decorato a livello di controller. 

### <a name="example"></a>Esempio
Per disabilitare CORS in un determinato metodo di una classe, l'attributo DisableCors può essere usato come illustrato sotto: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | MVC 6 |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Enabling Cross-Origin Requests (CORS) in ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) (Abilitazione di richieste multiorigine (CORS) in ASP.NET Core 1.0) |
| **Passaggi** | <p>In ASP.NET Core 1.0 CORS può essere abilitato usando il middleware o MVC. Quando si usa MVC per abilitare CORS, vengono usati gli stessi servizi CORS, ma non il middleware CORS.</p>|

**Approccio 1** Abilitazione di CORS con il middleware: Per abilitare CORS per l'intera applicazione, aggiungere il middleware CORS alla pipeline di richieste usando il metodo di estensione UseCors. Si possono specificare criteri multiorigine quando si aggiunge il middleware CORS usando la classe CorsPolicyBuilder. A questo scopo è possibile procedere in due modi:

### <a name="example"></a>Esempio
Il primo consiste nel chiamare UseCors con un operatore lambda. L'operatore lambda accetta un oggetto CorsPolicyBuilder: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Esempio
Il secondo consiste nel definire uno più criteri CORS denominati e quindi nel selezionare i criteri per nome in fase di esecuzione. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Approccio 2** Abilitazione di CORS in MVC: In alternativa gli sviluppatori possono usare MVC per applicare CORS specifici per azione, per controller o a livello globale per tutti i controller.

### <a name="example"></a>Esempio
Per azione: Per specificare un criterio CORS per un'azione specifica, aggiungere l'attributo [EnableCors] all'azione. Specificare il nome del criterio. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Esempio
Per controller: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Esempio
A livello globale: 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Si noti che è fondamentale assicurarsi che l'elenco di origini nell'attributo EnableCors venga impostato su un set finito e attendibile di origini. Se la configurazione non è corretta (ad esempio, se si imposta il valore come "*"), i siti dannosi potranno attivare richieste multiorigine per l'API senza alcuna restrizione, rendendo in questo modo l'API vulnerabile agli attacchi CSRF. 

### <a name="example"></a>Esempio
Per disabilitare CORS per un controller o un'azione, usare l'attributo [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>Crittografare le sezioni dei file di configurazione dell'API Web contenenti dati sensibili

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Procedura: crittografare le sezioni di configurazione in ASP.NET 2.0 usando DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [Specifica di un provider di configurazione protetta](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Uso di Azure Key Vault per proteggere i segreti dell'applicazione](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Passaggi** | I file di configurazione, ad esempio Web.config e appsettings.json, vengono spesso usati per memorizzare informazioni sensibili, inclusi nomi utente, password, stringhe di connessione del database e chiavi di crittografia. Se non si proteggono queste informazioni, l'applicazione è vulnerabile agli utenti malintenzionati che ottengono informazioni sensibili, ad esempio nomi account utente e password, nomi dei database e nomi dei server. In base al tipo di distribuzione (Azure/locale), crittografare le sezioni sensibili dei file config usando DPAPI o servizi come Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Assicurarsi che tutte le interfacce amministrative siano protette con credenziali sicure

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Tutte le interfacce amministrative esposte dal dispositivo o dal gateway sul campo devono essere protette con credenziali sicure. Anche tutte le altre interfacce esposte, ad esempio WiFi, SSH, condivisioni file e FTP, devono essere protette con credenziali sicure. Non si devono usare le password vulnerabili predefinite. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Assicurarsi che un codice sconosciuto non possa essere eseguito sui dispositivi

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Enabling Secure Boot and bit-locker Device Encryption on Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) (Abilitazione dell'avvio protetto e della crittografia dispositivo BitLocker in Windows 10 IoT Core) |
| **Passaggi** | L'avvio protetto UEFI limita il sistema consentendo solo l'esecuzione di file binari firmati da un'autorità specificata. Questa funzionalità impedisce l'esecuzione di codice sconosciuto sulla piattaforma e il potenziale indebolimento del comportamento di sicurezza. Abilitare l'avvio protetto UEFI e limitare l'elenco di autorità di certificazione considerate attendibili per la firma del codice. Firmare tutto il codice distribuito nel dispositivo usando una delle autorità attendibili. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Crittografare il sistema operativo e altre partizioni del dispositivo IoT con bit-locker

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Windows 10 IoT Core implementa una versione lightweight della crittografia dispositivo bit-locker, che dipende fortemente dalla presenza di un modulo TPM nella piattaforma, incluso il protocollo preOS necessario in UEFI che esegue le misurazioni necessarie. Queste misurazioni preOS assicurano che in seguito il sistema operativo conservi un record definitivo di come è stato avviato. Crittografare le partizioni del sistema operativo che usano bit-locker ed eventuali partizioni aggiuntive anche nel caso in cui archivino dati sensibili. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Assicurarsi che sui dispositivi siano abilitati solo servizi/funzionalità minime

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Non abilitare né disattivare funzionalità o servizi del sistema operativo non necessari per il funzionamento della soluzione. Se, ad esempio, il dispositivo non richiede la distribuzione di un'interfaccia utente, installare Windows IoT Core in modalità headless. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Crittografare il sistema operativo e altre partizioni del gateway IoT sul campo con bit-locker

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Gateway IoT sul campo | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Windows 10 IoT Core implementa una versione lightweight della crittografia dispositivo bit-locker, che dipende fortemente dalla presenza di un modulo TPM nella piattaforma, incluso il protocollo preOS necessario in UEFI che esegue le misurazioni necessarie. Queste misurazioni preOS assicurano che in seguito il sistema operativo conservi un record definitivo di come è stato avviato. Crittografare le partizioni del sistema operativo che usano bit-locker ed eventuali partizioni aggiuntive anche nel caso in cui archivino dati sensibili. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Assicurarsi che le credenziali di accesso predefinite del gateway sul campo vengano modificate durante l'installazione

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Gateway IoT sul campo | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Assicurarsi che le credenziali di accesso predefinite del gateway sul campo vengano modificate durante l'installazione |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Assicurarsi che il gateway nel cloud implementi un processo per mantenere aggiornato il firmware dei dispositivi connessi

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Gateway IoT cloud | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | Opzione gateway: Hub IoT di Azure |
| **Riferimenti**              | [Panoramica della gestione dei dispositivi con l'hub IoT](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [How to update Device Firmware](../../iot-hub/tutorial-firmware-update.md) (Come aggiornare il firmware di un dispositivo) |
| **Passaggi** | LWM2M è un protocollo di Open Mobile Alliance per la gestione dei dispositivi IoT. Gestione dei dispositivi dell'hub IoT di Azure consente di interagire con dispositivi fisici tramite processi del dispositivo. Assicurarsi che il gateway nel cloud implementi a processo per mantenere regolarmente aggiornati il dispositivo e gli altri dati di configurazione usando Gestione dei dispositivi dell'hub IoT di Azure. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Assicurarsi che i dispositivi abbiano i controlli di sicurezza degli endpoint configurati in base ai criteri organizzativi

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Limite di trust dei computer | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Assicurarsi che i dispositivi abbiano controlli di sicurezza degli endpoint, ad esempio bit-locker per la crittografia a livello di disco, antivirus con firme aggiornate, firewall basato su host, aggiornamenti del sistema operativo, criteri di gruppo e così via, configurati in base ai criteri di sicurezza dell'organizzazione. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Assicurare una gestione sicura delle chiavi di accesso alle risorse di archiviazione di Azure

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Archiviazione di Azure | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Guida alla sicurezza di Archiviazione di Azure: gestione delle chiavi dell'account di archiviazione](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Passaggi** | <p>Archiviazione chiavi: Si consiglia di archiviare le chiavi di accesso alle risorse di archiviazione di Azure in Azure Key Vault come segreto e fare in modo che le applicazioni recuperino la chiave dall'insieme di credenziali delle chiavi. È consigliabile per i motivi seguenti:</p><ul><li>L'applicazione non avrà mai una chiave di archiviazione hardcoded in un file di configurazione, eliminando la possibilità che qualcuno possa ottenere l'accesso alle chiavi senza un'autorizzazione specifica.</li><li>L'accesso alle chiavi può essere controllato con Azure Active Directory. Il proprietario dell'account può quindi concedere l'accesso solo alle applicazioni che devono recuperare le chiavi da Azure Key Vault. Le altre applicazioni non riusciranno ad accedere alle chiavi se non vengono loro concesse autorizzazioni specifiche.</li><li>Rigenerazione delle chiavi: Si consiglia di predisporre un processo per rigenerare le chiavi di accesso alle risorse di archiviazione di Azure per motivi di sicurezza. I dettagli su perché e come pianificare la rigenerazione delle chiavi sono documentati nell'articolo di riferimento Guida alla sicurezza di Archiviazione di Azure.</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Assicurarsi che siano consentite solo origini attendibili se CORS è abilitato in Archiviazione di Azure

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Archiviazione di Azure | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Supporto di CORS per i servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Passaggi** | Archiviazione di Azure consente di abilitare CORS (Condivisione risorse tra le origini). Per ogni account di archiviazione è possibile specificare i domini che possono accedere alle risorse in tale account di archiviazione. Per impostazione predefinita, CORS è disabilitato in tutti i servizi. È possibile abilitare CORS con l'API REST o la libreria client di archiviazione per chiamare uno dei metodi che impostano i criteri del servizio. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>Abilitare la funzionalità di limitazione dei servizi di WCF

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | .NET Framework 3 |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com) |
| **Passaggi** | <p>Se non si imposta un limite all'uso delle risorse di sistema, potrebbe verificarsi un problema di esaurimento risorse e infine di Denial of Service.</p><ul><li>**SPIEGAZIONE:** Windows Communication Foundation (WCF) offre la possibilità di limitare le richieste di servizio. Se si consentono troppe richieste client, un sistema può riempirsi ed esaurire le risorse. Al contrario, consentire solo un numero ridotto di richieste per un servizio può impedire ai legittimi utenti di usare il servizio. Ogni singolo servizio deve essere ottimizzato e configurato per consentire la quantità appropriata di risorse.</li><li>**SUGGERIMENTI** Abilitare la funzionalità di limitazione del servizio di WCF e impostare limiti appropriati per l'applicazione.</li></ul>|

### <a name="example"></a>Esempio
Di seguito è riportato un esempio di configurazione con la funzionalità di limitazione abilitata:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>Diffusione di informazioni di WCF tramite i metadati

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | .NET Framework 3 |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com) |
| **Passaggi** | I metadati possono consentire agli utenti malintenzionati di ottenere informazioni sul sistema e di pianificare un attacco. I servizi WCF possono essere configurati per esporre i metadati. I metadati offrono informazioni dettagliate sui servizi e non devono essere trasmessi negli ambienti di produzione. Le proprietà `HttpGetEnabled` / `HttpsGetEnabled` della classe ServiceMetaData definiscono se un servizio esporrà i metadati. | 

### <a name="example"></a>Esempio
Il codice seguente indica a WCF di trasmettere i metadati di un servizio.
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Non trasmettere i metadati del servizio in un ambiente di produzione. Impostare le proprietà HttpGetEnabled/HttpsGetEnabled della classe ServiceMetaData su false. 

### <a name="example"></a>Esempio
Il codice seguente indica a WCF di non trasmettere i metadati di un servizio. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
