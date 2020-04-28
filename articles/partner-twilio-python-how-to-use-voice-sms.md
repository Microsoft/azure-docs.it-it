---
title: Come usare Twilio per le funzionalità voce ed SMS (Python) | Microsoft Docs
description: Informazioni su come effettuare una chiamata telefonica e inviare un SMS con il servizio API Twilio API in Azure. Gli esempi di codice sono scritti in Python.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.openlocfilehash: edbc9eef6b5f0af2e70152b66228cdf09ef31110
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "72242193"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Come usare Twilio per le funzionalità voce ed SMS in Python
In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio API Twilio in Azure. Gli scenari presentati includono la composizione di una chiamata telefonica e l'invio di un messaggio SMS (Short Message Service). Per ulteriori informazioni su Twilio e sull'utilizzo di Voice and SMS nelle applicazioni, vedere la sezione [passaggi successivi](#NextSteps) .

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Informazioni su Twilio
Twilio è una tecnologia all'avanguardia per le comunicazioni aziendali che consente agli sviluppatori di incorporare funzionalità voce, VoIP e di messaggistica nelle applicazioni. Consente di virtualizzare tutta l'infrastruttura necessaria in un ambiente globale basato su cloud, esponendolo attraverso la piattaforma API per le comunicazioni Twilio. Le applicazioni sono scalabili e facili da compilare. Offre flessibilità, grazie a un modello di prezzi con pagamento al consumo, e l'affidabilità del cloud.

**Twilio Voice** consente alle applicazioni di effettuare e ricevere chiamate telefoniche.
**Twilio SMS** consente alle applicazioni di inviare e ricevere messaggi di testo.
**Twilio Client** consente di effettuare chiamate VoIP da qualsiasi telefono, tablet o browser e supporta WebRTC.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Prezzi e offerte speciali di Twilio
I clienti di Azure ricevono un'[offerta speciale][special_offer]: $ 10 di credito Twilio quando si esegue l'aggiornamento dell'account Twilio. Il credito Twilio può essere applicato a qualsiasi utilizzo di Twilio ($ 10 di credito equivalgono all'invio di 1.000 SMS o a 1.000 minuti voce per le chiamate in entrata, a seconda della località del numero di telefono, del messaggio o della destinazione della chiamata). Riscattare il [credito Twilio][special_offer] e iniziare a usare il servizio.

Twilio è un servizio con pagamento in base al consumo. Non prevede spese iniziali ed è possibile chiudere l'account in qualsiasi momento. Per altre informazioni, vedere la pagina [Twilio Pricing][twilio_pricing] (Prezzi di Twilio).

## <a name="concepts"></a><a id="Concepts"></a>Concetti
L'API Twilio è un'API RESTful che fornisce funzionalità voce ed SMS per le applicazioni. Le librerie client sono disponibili in più lingue. Per un elenco, vedere [Twilio API Libraries][twilio_libraries] (Librerie dell'API Twilio).

I concetti principali dell'API Twilio sono costituiti dai verbi Twilio e dal linguaggio di markup Twilio (Twilio Markup Language, TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Verbi Twilio
L'API usa i verbi Twilio; il ** &lt;verbo Say&gt; ** , ad esempio, indica a Twilio di recapitare in modo udibile un messaggio in una chiamata.

Di seguito è riportato un elenco dei verbi Twilio. Per altre informazioni su altri verbi e funzionalità, vedere la [documentazione relativa al linguaggio di markup Twilio][twiml].

* Dial: connette il chiamante a un altro telefono. ** &lt;&gt;**
* Raccolta: raccoglie le cifre numeriche immesse sul tastierino telefonico. ** &lt;&gt;**
* Hangup: termina una chiamata. ** &lt;&gt;**
* Pause: attende in modo invisibile all'utente un numero di secondi specificato. ** &lt;&gt;**
* Play: riproduce un file audio. ** &lt;&gt;**
* Queue: aggiungere a una coda di chiamanti. ** &lt;&gt;**
* Record: registra la voce del chiamante e restituisce un URL di un file che contiene la registrazione. ** &lt;&gt;**
* Reindirizzamento: trasferisce il controllo di una chiamata o di un SMS a TwiML in un URL diverso. ** &lt;&gt;**
* Reject: rifiuta una chiamata in ingresso al numero Twilio senza fatturazione. ** &lt;&gt;**
* Say: converte il testo in sintesi vocale eseguita in una chiamata. ** &lt;&gt;**
* SMS: Invia un messaggio SMS. ** &lt;&gt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML è un set di istruzioni basate su XML e sui verbi Twilio che indicano a Twilio come elaborare una chiamata o un SMS.

Ad esempio, il codice TwiML seguente effettua la sintesi vocale del testo **Hello World** .

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Quando l'applicazione chiama l'API Twilio, uno dei parametri dell'API è l'URL che restituisce la risposta TwiML. Ai fini dello sviluppo, è possibile utilizzare gli URL offerti da Twilio per fornire le risposte TwiML utilizzate dalle applicazioni. È inoltre possibile ospitare gli URL per generare le risposte TwiML. In alternativa, usare l'oggetto `TwiMLResponse`.

Per altre informazioni sui verbi Twilio, i relativi attributi e il codice TwiML, vedere [TwiML][twiml]. Per altre informazioni sull'API Twilio, vedere [Twilio API][twilio_api] (API Twilio).

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Creare un account Twilio
Se si desidera creare un account Twilio, iscriversi nella pagina relativa alla [registrazione gratuita di Twilio][try_twilio]. È possibile iniziare con un account gratuito ed eseguire l'aggiornamento in un secondo momento.

Quando si effettua l'iscrizione a un account Twilio, si riceveranno il SID account e un token di autenticazione. Entrambe queste informazioni sono necessarie per effettuare chiamate all'API Twilio. Per prevenire accessi autorizzati all'account, conservare il token di autenticazione in un luogo sicuro. Il SID dell'account e il token di autorizzazione sono visualizzabili nella [console di Twilio][twilio_console], rispettivamente nei campi **ACCOUNT SID** (SID ACCOUNT) e **AUTH TOKEN** (TOKEN AUTORIZZAZIONE).

## <a name="create-a-python-application"></a><a id="create_app"></a>Creare un'applicazione Python
Un'applicazione Python che usa il servizio Twilio e viene eseguita in Azure non è diversa da qualsiasi altra applicazione Python che usa il servizio Twilio. Benché i servizi Twilio siano basati su REST e possano essere chiamati da Python in diversi modi, questo articolo illustra solo come usare i servizi Twilio con la [libreria Twilio per Python da GitHub][twilio_python]. Per ulteriori informazioni sull'utilizzo della libreria Twilio per Python, vedere [https://www.twilio.com/docs/libraries/python][twilio_lib_docs].

In primo luogo, [configurare una nuova macchina virtuale Linux in Azure][azure_vm_setup] che funga da host per la nuova applicazione Web Python. Quando la macchina virtuale è in esecuzione, è necessario esporla su una porta pubblica, come descritto di seguito.

### <a name="add-an-incoming-rule"></a>Aggiungere una regola in ingresso
  1. Passare alla pagina [Gruppo di sicurezza di rete][azure_nsg].
  2. Selezionare il gruppo di sicurezza di rete corrispondente alla macchina virtuale.
  3. Aggiungere una **Regola in uscita** per la **porta 80**. Verificare di consentire l'ingresso da qualsiasi indirizzo.

### <a name="set-the-dns-name-label"></a>Impostare l'etichetta del nome DNS
  1. Passare alla pagina [Indirizzi IP pubblici][azure_ips].
  2. Selezionare l'indirizzo IP pubblico corrispondente alla macchina virtuale.
  3. Impostare **Etichetta del nome DNS** nella sezione **Configurazione**. Nel caso di questo esempio, verrà visualizzata una dicitura simile a *etichetta-dominio*.centralus.cloudapp.azure.com.

Quando si è in grado di connettersi alla macchina virtuale tramite SSH, è possibile installare il framework Web scelto. In Python i due framework più noti sono [Flask](http://flask.pocoo.org/) e [Django](https://www.djangoproject.com). Per installarne uno è sufficiente eseguire il comando `pip install`.

Tenere presente che la macchina virtuale è stata configurata per consentire il traffico solo sulla porta 80. Di conseguenza, verificare di configurare l'applicazione per l'uso di questa porta.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Configurare l'applicazione per l'uso delle librerie Twilio
È possibile configurare l'applicazione per l'uso della libreria Twilio per Python in due modi:

* Installare la libreria Twilio per Python come pacchetto Pip. È possibile eseguire l'installazione tramite i comandi seguenti:
   
        $ pip install twilio

    -OPPURE-

* Scaricare la libreria Twilio per Python da GitHub ([https://github.com/twilio/twilio-python][twilio_python]) e installarla come segue:

        $ python setup.py install

Dopo avere installato la libreria Twilio per Python, è possibile usare il comando `import` per importarla nei file Python:

        import twilio

Per altre informazioni, vedere [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md).

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Procedura: Effettuare una chiamata in uscita
Di seguito è illustrato come effettuare una chiamata in uscita. Questo codice utilizza inoltre un sito fornito da Twilio per restituire la risposta TwiML (Twilio Markup Language). Sostituire i valori di **from_number** e **to_number** relativi ai numeri di telefono e, prima di eseguire il codice, verificare il numero di telefono specificato in **from_number** per l'account Twilio.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "https://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Come indicato in precedenza, questo codice utilizza un sito fornito da Twilio per restituire la risposta TwiML. Per fornire la risposta TwiML è inoltre possibile usare il proprio sito. Per altre informazioni, vedere [Come fornire risposte TwiML dal proprio sito Web](#howto_provide_twiml_responses).

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Procedura: Inviare un messaggio SMS
La schermata seguente mostra come inviare un messaggio SMS tramite la classe `TwilioRestClient`. Il numero riportato in **from_number** per l'invio di messaggi SMS con gli account di valutazione è fornito da Twilio. Il numero riportato in **to_number** deve essere verificato per l'account Twilio prima di eseguire il codice.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>Procedura: Fornire risposte TwiML dal proprio sito Web
Quando l'applicazione avvia una chiamata all'API Twilio, Twilio invia la richiesta a un URL che deve restituire una risposta TwiML. Nell'esempio precedente viene usato l'URL [https://twimlets.com/message][twimlet_message_url]fornito da Twilio. Poiché TwiML è stato progettato per essere usato da Twilio, è possibile visualizzarlo nel browser. Ad esempio, fare [https://twimlets.com/message][twimlet_message_url] clic per visualizzare un `<Response>` elemento vuoto; come altro esempio, fare [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] clic per visualizzare `<Response>` un elemento che contiene `<Say>` un elemento.

Anziché utilizzare l'URL fornito da Twilio, è possibile creare un sito personalizzato che restituisce risposte HTTP. È possibile creare il sito in qualsiasi linguaggio che restituisca risposte XML. In questo argomento si presuppone che per la creazione di TwiML venga usato Python.

Negli esempi seguenti viene generata una risposta TwiML **Hello World** nella chiamata.

Con Flask:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Con Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Come si evince dal codice riportato sopra, la risposta TwiML è semplicemente un documento XML. La libreria Twilio per Python contiene le classi che consentono di generare automaticamente le istruzioni TwiML. Nell'esempio seguente viene generata la stessa risposta descritta sopra, ma usando il modulo `twiml` nella libreria Twilio per Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Per ulteriori informazioni su TwiML, vedere [https://www.twilio.com/docs/api/twiml][twiml_reference].

Dopo aver configurato l'applicazione Python in modo da fornire risposte TwiML, usare l'URL di tale applicazione come URL trasmesso nel metodo `client.calls.create`. Se, ad esempio, si dispone di un'applicazione Web denominata **MyTwiML** distribuita in un servizio ospitato in Azure, è possibile usare il relativo URL come webhook, come mostrato nell'esempio seguente:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Procedura: Utilizzare servizi Twilio aggiuntivi
Oltre agli esempi illustrati in questa pagina, Twilio offre API basate su Web che è possibile utilizzare per sfruttare altre funzionalità di Twilio dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API Twilio][twilio_api].

## <a name="next-steps"></a><a id="NextSteps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base del servizio Twilio, usare i collegamenti seguenti per altre informazioni:

* [Twilio Security Guidelines][twilio_security_guidelines] (Linee guida sulla sicurezza di Twilio)
* [Twilio HowTo Guides and Example Code][twilio_howtos] (Procedure e codice di esempio su Twilio)
* [Twilio Quickstart Tutorials][twilio_quickstarts] (Esercitazioni con guide rapide su Twilio)
* [Twilio on GitHub][twilio_on_github] (Twilio su GitHub)
* [Talk to Twilio Support][twilio_support] (Contattare il supporto di Twilio)

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
