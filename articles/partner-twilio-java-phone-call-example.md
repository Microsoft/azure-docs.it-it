---
title: Come effettuare una chiamata telefonica da Twilio (Java) | Microsoft Docs
description: Informazioni su come effettuare una chiamata telefonica da una pagina Web usando Twilio con un'applicazione Java in Azure.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 168ec65cfd0ff4e87c33324daa353b554111c8aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838546"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Come effettuare una chiamata tramite Twilio in un'applicazione Java in Azure
Nell'esempio seguente viene illustrato come è possibile usare Twilio per effettuare una chiamata da una pagina Web ospitata in Azure. L'applicazione risultante richiederà all'utente i valori delle chiamate telefoniche, come illustrato nella schermata seguente.

![Modulo di chiamata di Azure con Twilio e Java][twilio_java]

Per usare il codice in questo argomento è necessario eseguire le operazioni seguenti:

1. Ottenere un account e un token di autenticazione Twilio. Per iniziare a utilizzare Twilio, valuta i prezzi in [https://www.twilio.com/pricing][twilio_pricing]. È possibile iscriversi [https://www.twilio.com/try-twilio][try_twilio]a . Per informazioni sull'API fornita da Twilio, vedere [https://www.twilio.com/api][twilio_api].
2. Ottenere il file JAR di Twilio. In [https://github.com/twilio/twilio-java][twilio_java_github], è possibile scaricare le origini GitHub e creare il proprio JAR, o scaricare un JAR predefinito (con o senza dipendenze).
   Il codice in questo argomento è stato scritto utilizzando il JAR precompilato TwilioJava-3.3.8-with-dependencies.
3. Aggiungere il file JAR al percorso di compilazione Java.
4. Se si usa Eclipse per creare l'applicazione Java, includere il JAR di Twilio nel file WAR per la distribuzione dell'applicazione usando l'assembly di distribuzione di Eclipse. Se non si utilizza Eclipse per creare l'applicazione Java, verificare che il JAR di Twilio sia incluso nello stesso ruolo di Azure dell'applicazione Java e che sia stato aggiunto al percorso della classe dell'applicazione.
5. Verificare che l'archivio chiavi cacerts del JDK contenga il certificato Equifax Secure Certificate Authority con ID digitale MD5 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (il numero di serie è 35:DE:F4:CF e l'ID digitale SHA1 è D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Si tratta del certificato dell'autorità [https://api.twilio.com][twilio_api_service] di certificazione (CA) per il servizio, che viene chiamato quando si utilizzano le API Twilio. Per informazioni sull'aggiunta del certificato della CA all'archivio cacert del JDK, vedere [Aggiunta di un certificato all'archivio certificati CA Java][add_ca_cert].

Se non si usa Eclipse, è anche consigliabile acquisire familiarità con le informazioni in [Creazione di un'applicazione Hello World per Azure in Eclipse][azure_java_eclipse_hello_world] o con altre tecniche per l'hosting di applicazioni Java in Azure.

## <a name="create-a-web-form-for-making-a-call"></a>Creare un modulo Web per effettuare una chiamata
Nel codice seguente viene illustrato come creare un modulo Web per recuperare i dati utente per l'esecuzione di una chiamata. Ai fini di questo esempio, è stato creato un nuovo progetto Web dinamico denominato **TwilioCloud** ed è stato aggiunto il file JSP **callform.jsp**.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>Creare il codice per l'esecuzione della chiamata
Il codice seguente, chiamato quando l'utente completa il modulo visualizzato da callform.jsp, crea il messaggio di chiamata e genera la chiamata. Ai fini di questo esempio, il file JSP è denominato **makecall.jsp** ed è stato aggiunto al progetto **TwilioCloud**. Nel codice seguente sostituire i valori segnaposto assegnati a **accountSID** e **authToken** con il proprio account e il token di autenticazione Twilio.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="https://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Oltre ad eseguire la chiamata, makecall.jsp visualizza l'endpoint Twilio, la versione dell'API e lo stato della chiamata. Un esempio è la schermata seguente:An example is the following screenshot:

![Risposta a chiamata di Azure tramite Twilio e Java][twilio_java_response]

## <a name="run-the-application"></a>Eseguire l'applicazione
Di seguito è riportata la procedura generica per eseguire l'applicazione. Per istruzioni dettagliate, vedere [Creazione di un'applicazione Hello World tramite Azure Toolkit for Eclipse][azure_java_eclipse_hello_world].

1. Esportare il file WAR TwilioCloud nella cartella **approot** di Azure. 
2. Modificare **startup.cmd** per decomprimere il file WAR TwilioCloud.
3. Compilare l'applicazione per l'emulatore di calcolo.
4. Avviare la distribuzione nell'emulatore di calcolo.
5. Aprire un browser ed eseguire `http://localhost:8080/TwilioCloud/callform.jsp`.
6. Immettere i valori nel modulo, fare clic su **Make this call**e quindi visualizzare i risultati in makecall.jsp.

Quando si è pronti a distribuire l'applicazione in Azure, ricompilare per la distribuzione nel cloud, distribuire in Azure ed eseguire http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp nel browser, sostituendo *your_hosted_name* con il proprio valore.

## <a name="next-steps"></a>Passaggi successivi
Questo codice ha lo scopo di illustrare le funzionalità di base dell'utilizzo di Twilio con Java in Azure. Prima di eseguire la distribuzione in Azure in produzione, può essere necessario aggiungere ulteriori funzionalità per la gestione degli errori o per altri scopi. Ad esempio:

* Anziché utilizzare un modulo Web, è possibile utilizzare l'archiviazione BLOB o un database SQL di Azure per l'archiviazione di numeri di telefono e testo delle chiamate. Per informazioni sull'uso dei BLOB di archiviazione di Azure in Java, vedere [Come usare il servizio di archiviazione BLOB da Java][howto_blob_storage_java]. 
* È possibile usare **RoleEnvironment.getConfigurationSettings** per recuperare l'ID e il token di autenticazione dell'account Twilio dalle impostazioni di configurazione della distribuzione, anziché impostare i valori come hardcoded in makecall.jsp. Per informazioni sulla classe **RoleEnvironment,** vedere Utilizzo della libreria di runtime del servizio di [Azure in JSP][azure_runtime_jsp].
* Il codice makecall.jsp assegna un URL fornito [https://twimlets.com/message][twimlet_message_url]da Twilio, , alla variabile **Url.** Tale URL fornisce una risposta TwiML (Twilio Markup Language) che indica a Twilio in che modo eseguire la chiamata. Ad esempio, il TwiML restituito può ** &lt;&gt; ** contenere un verbo Say che determina la pronuncia del testo al destinatario della chiamata. Anziché usare l'URL fornito da Twilio, è possibile creare un servizio personalizzato per rispondere alla richiesta di Twilio. Per altre informazioni, vedere [Come usare Twilio per le funzionalità voce ed SMS in Java][howto_twilio_voice_sms_java]. Ulteriori informazioni su TwiML [https://www.twilio.com/docs/api/twiml][twiml]sono disponibili all'indirizzo , e ulteriori informazioni su ** &lt;&gt; ** [https://www.twilio.com/docs/api/twiml/say][twilio_say]Say e altri verbi Twilio sono disponibili all'indirizzo .
* Leggere le linee guida di [https://www.twilio.com/docs/security][twilio_docs_security]sicurezza Twilio all'indirizzo .

Per ulteriori informazioni su Twilio, vedere [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Vedere anche
* [Come usare Twilio per le funzionalità voce ed SMS in Java][howto_twilio_voice_sms_java]
* [Aggiunta di un certificato all'archivio certificati CA Java][add_ca_cert]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: https://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
