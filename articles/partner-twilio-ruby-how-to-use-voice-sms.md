---
title: Come usare Twilio per le funzionalità voce e SMS (Ruby) | Microsoft Docs
description: Informazioni su come effettuare una chiamata telefonica e inviare un SMS con il servizio API Twilio API in Azure. Gli esempi di codice sono scritti in Ruby.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 4822e6feb29f5a17c653a60937b895ec584e0ee4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "69637208"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Come usare Twilio per le funzionalità voce ed SMS in Ruby
In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per ulteriori informazioni su Twilio e sull'utilizzo di Voice and SMS nelle applicazioni, vedere la sezione [passaggi successivi](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Informazioni su Twilio
Twilio è un'API per servizi Web di telefonia che consente di usare le competenze e i linguaggi Web esistenti per sviluppare applicazioni SMS e vocali. Twilio è un servizio di terze parti. Non si tratta di una funzionalità di Azure, né di un prodotto Microsoft.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche. **Twilio SMS** consente alle applicazioni di inviare e ricevere SMS. **Twilio Client** consente alle applicazioni di abilitare le comunicazioni vocali utilizzando le connessioni Internet esistenti, comprese le connessioni mobili.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Prezzi e offerte speciali di Twilio
Per altre informazioni, vedere la pagina [Twilio Pricing][twilio_pricing] (Prezzi di Twilio). Per i clienti di Azure è disponibile un'[offerta speciale][special_offer]: un credito gratuito per 1000 SMS o 1000 minuti di connessioni in entrata. Per iscriversi a questa offerta o ottenere altre informazioni, visitare il [https://ahoy.twilio.com/azure][special_offer] .  

## <a name="concepts"></a><a id="Concepts"></a>Concetti
L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere [Twilio API Libraries][twilio_libraries] (Librerie dell'API Twilio).

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML è un set di istruzioni basate su XML che indicano a Twilio come elaborare una chiamata o un SMS.

Ad esempio, il codice TwiML seguente effettua la sintesi vocale del testo **Hello World** .

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Tutti i documenti TwiML dispongono di un elemento radice `<Response>`, da cui è possibile utilizzare i verbi Twilio per definire il comportamento dell'applicazione.

### <a name="twiml-verbs"></a><a id="Verbs"></a>Verbi TwiML
I verbi Twilio sono tag XML che indicano a Twilio le **azioni da eseguire**. Il verbo ** &lt; Say &gt; ** , ad esempio, indica a Twilio di recapitare in modo udibile un messaggio in una chiamata. 

Di seguito è riportato un elenco dei verbi Twilio.

* ** &lt; Dial &gt; **: connette il chiamante a un altro telefono.
* ** &lt; Raccolta &gt; **: raccoglie le cifre numeriche immesse sul tastierino telefonico.
* ** &lt; Hangup &gt; **: termina una chiamata.
* ** &lt; Play &gt; **: riproduce un file audio.
* ** &lt; Pause &gt; **: attende in modo invisibile all'utente un numero di secondi specificato.
* ** &lt; Record &gt; **: registra la voce del chiamante e restituisce un URL di un file che contiene la registrazione.
* ** &lt; Reindirizzamento &gt; **: trasferisce il controllo di una chiamata o di un SMS a TwiML in un URL diverso.
* ** &lt; Rifiuta &gt; **: rifiuta una chiamata in ingresso al numero Twilio senza fatturazione
* ** &lt; Say &gt; **: converte il testo in sintesi vocale eseguita in una chiamata.
* ** &lt; SMS &gt; **: Invia un messaggio SMS.

Per altre informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML][twiml]. Per altre informazioni sull'API Twilio, vedere [Twilio API][twilio_api] (API Twilio).

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Creare un account Twilio
Se si desidera creare un account Twilio, iscriversi nella pagina [Try Twilio][try_twilio] (Provare Twilio). È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a Twilio si riceve un numero di telefono gratuito per l'applicazione. Si ricevono inoltre il SID dell'account e un token di autorizzazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. Il SID account e il token di autorizzazione sono visualizzabili nella [pagina dell'account Twilio][twilio_account], rispettivamente nei campi **ACCOUNT SID** (SID ACCOUNT) e **AUTH TOKEN** (TOKEN AUTORIZZAZIONE).

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Verificare i numeri di telefono
Oltre al numero assegnato da Twilio, è possibile verificare altri numeri di cui si abbia il controllo, ad esempio quello del cellulare o quello dell'abitazione, per usarli nelle applicazioni. 

Per informazioni su come verificare un numero di telefono, vedere [Manage Numbers][verify_phone] (Gestire i numeri).

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Creare un'applicazione Ruby
Un'applicazione Ruby che usa il servizio Twilio e viene eseguita in Azure non è diversa da qualsiasi altra applicazione Ruby che usa il servizio Twilio. Anche se i servizi Twilio sono di tipo RESTful e possono essere chiamati da Ruby in molti modi, in questo articolo verrà illustrato solo come usare i servizi Twilio con la [libreria helper Twilio per Ruby][twilio_ruby].

Prima di tutto, [configurare una nuova VM Linux in Azure][azure_vm_setup] che funga da host per la nuova applicazione Web Ruby. Ignorare i passaggi relativi alla creazione di un'app Rails e configurare semplicemente la VM. Assicurarsi di creare un endpoint con porta esterna 80 e porta interna 5000.

Negli esempi che seguono verrà usato [Sinatra][sinatra], un framework Web molto semplice per Ruby. È comunque possibile utilizzare la libreria helper Twilio per Ruby con qualsiasi altro web framework, compreso Ruby on Rails.

Connettersi tramite SSH alla nuova macchina virtuale e creare una directory per la nuova app. All'interno di tale directory creare un file denominato Gemfile e copiare al suo interno il codice seguente:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Nella riga di comando eseguire `bundle install`. Le dipendenze precedenti verranno installate. Quindi, creare un file denominato `web.rb`. in cui risiederà il codice per l'app Web. Copiare nel file il codice seguente:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

A questo punto dovrebbe essere possibile eseguire il comando `ruby web.rb -p 5000`. Verrà avviato un server Web di dimensioni ridotte sulla porta 5000. Dovrebbe essere possibile passare all'app nel browser visitando l'URL configurato per la macchina virtuale di Azure. Non appena è possibile raggiungere l'app Web nel browser, è possibile iniziare a creare un'app Twilio.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Configurare l'applicazione per utilizzare Twilio
È possibile configurare l'app Web per utilizzare la libreria Twilio aggiornando il `Gemfile` includendo la seguente riga:

    gem 'twilio-ruby'

Nella riga di comando, eseguire `bundle install`. A questo punto, aprire `web.rb` e includere la riga nella parte superiore:

    require 'twilio-ruby'

È ora possibile usare la libreria helper Twilio per Ruby nell'app Web.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Procedura: Effettuare una chiamata in uscita
Di seguito è illustrato come effettuare una chiamata in uscita. I principali concetti illustrati includono l'utilizzo della libreria helper Twilio per Ruby per effettuare chiamate API REST e per il rendering di TwiML. Sostituire i valori per i numeri di telefono **From** e **To** e assicurarsi di verificare il numero di telefono in **From** per l'account Twilio prima di eseguire il codice.

Aggiungere a `web.md`la funzione seguente:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";

    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);

      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

Aprendo `http://yourdomain.cloudapp.net/make_call` in un browser verrà attivata la chiamata all'API Twilio per l'esecuzione della chiamata telefonica. I primi due parametri in `client.account.calls.create` sono facilmente comprensibili: il numero da cui proviene la chiamata è `from` e il numero a cui è diretta è `to`. 

Il terzo parametro (`url`) è l'URL utilizzato da Twilio per richiedere istruzioni sulle operazioni da eseguire dopo la connessione della chiamata. In questo caso viene configurato un URL (`http://yourdomain.cloudapp.net`) che restituisce un documento TwiML molto semplice e usa il verbo `<Say>` per effettuare la sintesi vocale del testo e pronunciare la frase "Hello Monkey" per il destinatario della chiamata.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Procedura: Ricevere un messaggio SMS
Nell'esempio precedente è stata avviata una chiamata telefonica **in uscita** . Questa volta verrà utilizzato il numero ricevuto da Twilio dopo l'iscrizione per elaborare un SMS **in arrivo** .

Prima di tutto, accedere al [dashboard Twilio][twilio_account]. Fare clic su "Numbers" sulla barra di spostamento superiore e quindi fare clic sul numero ricevuto da Twilio. Verranno visualizzati due URL che è possibile configurare, uno per le richieste vocali e uno per le richieste SMS. Si tratta degli URL chiamati da Twilio ogni volta che viene inviato un SMS o viene effettuata una chiamata telefonica verso il proprio numero. Gli URL sono noti anche come "hook Web".

Per elaborare gli SMS in arrivo è necessario aggiornare l'URL in `http://yourdomain.cloudapp.net/sms_url`. Effettuare la modifica e fare clic su Save Changes nella parte inferiore della pagina. Tornare a `web.rb` e programmare l'applicazione per la gestione dell'operazione seguente:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Dopo aver apportato la modifica, riavviare l'app Web. Prendere il telefono e inviare un SMS al proprio numero Twilio. Si dovrebbe ricevere un SMS di risposta con il testo "Hey, thanks for the ping! Twilio and Azure rock!".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Procedura: Utilizzare servizi Twilio aggiuntivi
Oltre agli esempi illustrati in questa pagina, Twilio offre API basate su Web che è possibile utilizzare per sfruttare altre funzionalità di Twilio dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API Twilio][twilio_api_documentation].

### <a name="next-steps"></a><a id="NextSteps"></a>Passaggi successivi
Dopo aver appreso le nozioni di base sul servizio Twilio, utilizzare i collegamenti seguenti per ulteriori informazioni:

* [Twilio Security Guidelines][twilio_security_guidelines] (Linee guida sulla sicurezza di Twilio)
* [Twilio HowTos and Example Code (Procedure e codice di esempio di Twilio)][twilio_howtos]
* [Twilio Quickstart Tutorials][twilio_quickstarts] (Esercitazioni con guide rapide su Twilio) 
* [Twilio on GitHub][twilio_on_github] (Twilio su GitHub)
* [Talk to Twilio Support][twilio_support] (Contattare il supporto di Twilio)

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
