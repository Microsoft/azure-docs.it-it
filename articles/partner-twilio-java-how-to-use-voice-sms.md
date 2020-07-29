---
title: Come usare Twilio per le funzionalità voce e SMS (Java) | Microsoft Docs
description: Informazioni su come effettuare una chiamata telefonica e inviare un SMS con il servizio API Twilio API in Azure. Gli esempi di codice sono scritti in Java.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.custom: devx-track-java
ms.openlocfilehash: f2f30230418637b53826bd314e395e760db7087f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87306015"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Come usare Twilio per le funzionalità voce ed SMS in Java
In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per ulteriori informazioni su Twilio e sull'utilizzo di Voice and SMS nelle applicazioni, vedere la sezione [passaggi successivi](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Informazioni su Twilio
Twilio è un'API per servizi Web di telefonia che consente di usare le competenze e i linguaggi Web esistenti per sviluppare applicazioni SMS e vocali. Twilio è un servizio di terze parti. Non si tratta di una funzionalità di Azure, né di un prodotto Microsoft.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente alle applicazioni di abilitare le comunicazioni vocali utilizzando le connessioni Internet esistenti, comprese le connessioni mobili.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Prezzi e offerte speciali di Twilio
Per altre informazioni, vedere la pagina [Twilio Pricing][twilio_pricing] (Prezzi di Twilio). Per i clienti di Azure è disponibile un'[offerta speciale][special_offer]: un credito gratuito per 1000 SMS o 1000 minuti di connessioni in entrata. Per iscriversi a questa offerta o ottenere altre informazioni, visitare il [https://ahoy.twilio.com/azure][special_offer] .

## <a name="concepts"></a><a id="Concepts"></a>Concetti
L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere [Twilio API Libraries][twilio_libraries] (Librerie dell'API Twilio).

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Verbi Twilio
L'API usa i verbi Twilio; il verbo ** &lt; Say &gt; ** , ad esempio, indica a Twilio di recapitare in modo udibile un messaggio in una chiamata.

Di seguito è riportato un elenco dei verbi Twilio.

* ** &lt; Dial &gt; **: connette il chiamante a un altro telefono.
* ** &lt; Raccolta &gt; **: raccoglie le cifre numeriche immesse sul tastierino telefonico.
* ** &lt; Hangup &gt; **: termina una chiamata.
* ** &lt; Play &gt; **: riproduce un file audio.
* ** &lt; Queue &gt; **: aggiungere a una coda di chiamanti.
* ** &lt; Pause &gt; **: attende in modo invisibile all'utente un numero di secondi specificato.
* ** &lt; Record &gt; **: registra la voce del chiamante e restituisce un URL di un file che contiene la registrazione.
* ** &lt; Reindirizzamento &gt; **: trasferisce il controllo di una chiamata o di un SMS a TwiML in un URL diverso.
* ** &lt; Reject &gt; **: rifiuta una chiamata in ingresso al numero Twilio senza fatturazione.
* ** &lt; Say &gt; **: converte il testo in sintesi vocale eseguita in una chiamata.
* ** &lt; SMS &gt; **: Invia un messaggio SMS.

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML è un set di istruzioni basate su XML e sui verbi Twilio che indicano a Twilio come elaborare una chiamata o un SMS.

Ad esempio, il codice TwiML seguente effettua la sintesi vocale del testo **Hello World!** .

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Quando l'applicazione chiama l'API Twilio, uno dei parametri dell'API è l'URL che restituisce la risposta TwiML. Ai fini dello sviluppo, è possibile utilizzare gli URL offerti da Twilio per fornire le risposte TwiML utilizzate dalle applicazioni. È inoltre possibile ospitare gli URL per produrre le risposte TwiML oppure utilizzare l'oggetto **TwiMLResponse** .

Per altre informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML][twiml]. Per altre informazioni sull'API Twilio, vedere [Twilio API][twilio_api] (API Twilio).

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Creare un account Twilio
Se si desidera creare un account Twilio, iscriversi nella pagina [Try Twilio][try_twilio] (Provare Twilio). È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a un account Twilio, si riceverà un ID account e un token di autenticazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. L'ID account e il token di autorizzazione sono visualizzabili nella [console di Twilio][twilio_console], rispettivamente nei campi **ACCOUNT SID** (SID ACCOUNT) e **AUTH TOKEN** (TOKEN AUTORIZZAZIONE).

## <a name="create-a-java-application"></a><a id="create_app"></a>Creare un'applicazione Java
1. Ottenere il file JAR di Twilio e aggiungerlo al percorso di compilazione Java e all'assembly di distribuzione del file WAR. In [https://github.com/twilio/twilio-java][twilio_java] è possibile scaricare le origini di GitHub e creare un file jar personalizzato oppure scaricare un file jar predefinito (con o senza dipendenze).
2. Verificare che l'archivio chiavi **cacerts** del JDK contenga il certificato Equifax Secure Certificate Authority con ID digitale MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (il numero di serie è 35:DE:F4:CF e l'ID digitale SHA1 è D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Si tratta del certificato dell'autorità di certificazione (CA) per il [https://api.twilio.com][twilio_api_service] servizio, che viene chiamato quando si usano le API Twilio. Per informazioni su come verificare che l'archivio chiavi **cacerts** del JDK contenga il certificato CA corretto, vedere [Aggiunta di un certificato all'archivio certificati CA Java][add_ca_cert].

Per istruzioni dettagliate sull'uso della libreria client Twilio per Java, vedere [Come effettuare una chiamata tramite Twilio in un'applicazione Java in Azure][howto_phonecall_java].

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Configurare l'applicazione per l'uso delle librerie Twilio
All'interno del codice è possibile aggiungere istruzioni **import** nella parte superiore dei file di origine per i pacchetti o le classi Twilio da utilizzare nell'applicazione.

Per i file di origine Java:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Per i file di origine JSP (Java Server Page):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Le istruzioni **import** possono variare in base ai pacchetti o alle classi Twilio che si desidera utilizzare.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Procedura: Effettuare una chiamata in uscita
Di seguito è illustrato come effettuare una chiamata in uscita usando la classe **Call**. Questo codice utilizza inoltre un sito fornito da Twilio per restituire la risposta TwiML (Twilio Markup Language). Sostituire i valori per i numeri di telefono **da** e **a** e assicurarsi di **verificare il numero di telefono** per l'account Twilio prima di eseguire il codice.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Per altre informazioni sui parametri passati al metodo **Call.creator**, vedere [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Come indicato in precedenza, questo codice utilizza un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML è inoltre possibile usare il proprio sito. Per altre informazioni, vedere [Procedura per fornire risposte TwiML in un'applicazione Java in Azure](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Procedura: Inviare un messaggio SMS
La schermata seguente mostra come inviare un messaggio SMS usando la classe **Message**. Il numero riportato in **from_number**, **4155992671**, è fornito da Twilio per l'invio di messaggi SMS con gli account di valutazione. Il numero riportato in **to_number** deve essere verificato per l'account Twilio prima dell'esecuzione del codice.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Per altre informazioni sui parametri passati al metodo **Message.creator**, vedere [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Procedura: Fornire risposte TwiML dal proprio sito Web
Quando l'applicazione avvia una chiamata all'API Twilio, ad esempio tramite il metodo **CallCreator.create**, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. Nell'esempio precedente viene usato l'URL fornito da Twilio [https://twimlets.com/message][twimlet_message_url] . Poiché TwiML è progettato per essere usato da servizi Web, è possibile visualizzare il codice TwiML nel browser. Ad esempio, fare clic per [https://twimlets.com/message][twimlet_message_url] visualizzare un elemento ** &lt; Response &gt; ** vuoto. come altro esempio, fare clic [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] per visualizzare un elemento ** &lt; &gt; Response** contenente un elemento ** &lt; Say &gt; ** .

Anziché utilizzare l'URL fornito da Twilio, è possibile creare un sito Web personalizzato che restituisce risposte HTTP. È possibile creare il sito in qualsiasi linguaggio che restituisca risposte XML. In questo argomento si presuppone che l'URL sarà ospitato in una pagina JSP.

La pagina JSP seguente genera una risposta TwiML **Hello World!** nella chiamata.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

La pagina JSP seguente crea una risposta TwiML che pronuncia del testo, contiene diverse pause e pronuncia informazioni sulla versione dell'API Twilio e sul nome del ruolo di Azure.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

Il parametro **ApiVersion** è disponibile nelle richieste vocali Twilio, non nelle richieste SMS. Per visualizzare i parametri disponibili per le richieste vocali e SMS Twilio, vedere rispettivamente <https://www.twilio.com/docs/api/twiml/twilio_request> e <https://www.twilio.com/docs/api/twiml/sms/twilio_request>. La variabile di ambiente **RoleName** è disponibile come parte di una distribuzione Azure. Se si vuole aggiungere variabili di ambiente personalizzate in modo che possano essere recuperate da **System.getenv**, vedere la sezione relativa alle variabili di ambiente in [Impostazioni varie di configurazione dei ruoli][misc_role_config_settings].

Dopo aver configurato la pagina JSP in modo che vengano fornite risposte TwiML, usare l'URL della pagina JSP come URL passato nel metodo **Call.creator**. Se, ad esempio, si dispone di un'applicazione Web denominata MyTwiML distribuita in un servizio ospitato in Azure e il nome della pagina JSP è mytwiml.jsp, è possibile passare l'URL a **Call.creator** come illustrato nell'esempio di codice seguente:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Un'altra possibilità per rispondere con TwiML consiste nell'usare la classe **VoiceResponse**, disponibile nel pacchetto **com.twilio.twiml**.

Per altre informazioni sull'uso di Twilio in Azure con Java, vedere [Come effettuare una chiamata tramite Twilio in un'applicazione Java in Azure][howto_phonecall_java].

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Procedura: Utilizzare servizi Twilio aggiuntivi
Oltre agli esempi illustrati in questa pagina, Twilio offre API basate su Web che è possibile utilizzare per sfruttare altre funzionalità di Twilio dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API Twilio][twilio_api_documentation].

## <a name="next-steps"></a><a id="NextSteps"></a>Passaggi successivi
Dopo aver appreso le nozioni di base sul servizio Twilio, utilizzare i collegamenti seguenti per ulteriori informazioni:

* [Twilio Security Guidelines][twilio_security_guidelines] (Linee guida sulla sicurezza di Twilio)
* [Twilio HowTo e codice di esempio][twilio_howtos]
* [Twilio Quickstart Tutorials][twilio_quickstarts] (Esercitazioni con guide rapide su Twilio)
* [Twilio on GitHub][twilio_on_github] (Twilio su GitHub)
* [Talk to Twilio Support][twilio_support] (Contattare il supporto di Twilio)

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
