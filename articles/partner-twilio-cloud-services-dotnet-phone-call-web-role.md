---
title: Come effettuare una chiamata telefonica da Twilio (.NET) | Microsoft Docs
description: Informazioni su come effettuare una telefonata con il servizio API Twilio in Azure. Esempi di codice scritti in .NET.
services: ''
documentationcenter: .net
author: mimckitt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: mimckitt
ms.custom: devx-track-dotnet
ms.openlocfilehash: 22e0a19c0e2f5b29b35ebf43a3f250959c9b636d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95521122"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Come effettuare una chiamata tramite Twilio in un ruolo Web in Azure
In questa guida viene illustrato come usare Twilio per effettuare una chiamata da una pagina Web ospitata in Azure. L'applicazione risultante chiede all'utente di eseguire una chiamata con il numero e il messaggio specificati, come illustrato nella schermata seguente.

![Modulo di chiamata di Azure con Twilio e ASP.NET][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Prerequisiti
Per usare il codice in questo argomento è necessario eseguire le operazioni seguenti:

1. Ottenere un account Twilio e un token di autenticazione dalla [console di Twilio][twilio_console]. Per iniziare a usare Twilio, iscriversi a [https://www.twilio.com/try-twilio][try_twilio] . È possibile valutare i prezzi all'indirizzo [https://www.twilio.com/pricing][twilio_pricing] . Per informazioni sull'API fornita da Twilio, vedere [https://www.twilio.com/voice/api][twilio_api] .
2. Aggiungere la *libreria .NET di Twilio* al ruolo Web. Vedere **per aggiungere le librerie Twilio al progetto di ruolo Web**, più avanti in questo argomento.

È necessario conoscere le modalità di creazione di un [ruolo Web di base in Azure][azure_webroles_get_started].

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>Procedura: Creare un modulo Web per effettuare una chiamata
<a id="use_nuget"></a>Per aggiungere le librerie Twilio al progetto di ruolo Web:

1. Aprire la soluzione in Visual Studio.
2. Fare clic con il pulsante destro del mouse su **Riferimenti**.
3. Fare clic su **Gestisci pacchetti NuGet**.
4. Fare clic su **Online**.
5. Nella casella di ricerca online digitare *twilio*.
6. Fare clic su **Install** sul pacchetto Twilio.

Nel codice seguente viene illustrato come creare un modulo Web per recuperare i dati utente per l'esecuzione di una chiamata. In questo esempio viene creato un ruolo Web ASP.NET denominato **TwilioCloud** .

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Procedura: Creare il codice per effettuare la chiamata
Il codice seguente, chiamato quando l'utente completa il modulo, crea il messaggio di chiamata e genera la chiamata. In questo esempio, il codice viene eseguito sul gestore dell'evento onclick del pulsante del modulo. Nel codice seguente sostituire i valori segnaposto assegnati a `accountSID` e `authToken` con il proprio account e il token di autenticazione Twilio.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

La chiamata viene effettuata e vengono visualizzati l'endpoint Twilio, la versione dell'API e lo stato della chiamata. Nella schermata seguente viene illustrato l'output di un'esecuzione di esempio.

![Risposta a chiamata di Azure tramite Twilio e ASP.NET][twilio_dotnet_basic_form_output]

Altre informazioni su TwiML sono disponibili in [https://www.twilio.com/docs/api/twiml][twiml] . Altre informazioni su &lt; Say &gt; e altri verbi Twilio sono reperibili in [https://www.twilio.com/docs/api/twiml/say][twilio_say] .

## <a name="next-steps"></a><a id="nextsteps"></a>Passaggi successivi
Questo codice ha lo scopo di illustrare le funzionalità di base dell'utilizzo di Twilio in un ruolo Web ASP.NET in Azure. Prima di eseguire la distribuzione in Azure in produzione, può essere necessario aggiungere ulteriori funzionalità per la gestione degli errori o per altri scopi. Ad esempio:

* Anziché usare un modulo Web, è possibile usare l'archivio BLOB di Azure o un'istanza di database SQL di Azure per l'archiviazione di numeri di telefono e testo delle chiamate. Per informazioni sull'uso dei BLOB in Azure, vedere [Come usare il servizio di archiviazione BLOB di Azure in .NET][howto_blob_storage_dotnet]. Per informazioni sull'uso del database SQL, vedere [Come usare il database SQL di Azure in applicazioni .NET][howto_sql_azure_dotnet].
* È possibile usare `RoleEnvironment.getConfigurationSettings` per recuperare l'ID e il token di autenticazione dell'account Twilio dalle impostazioni di configurazione della distribuzione, anziché impostare i valori hardcoded nel modulo. Per informazioni sulla classe `RoleEnvironment`, vedere [Microsoft.WindowsAzure.ServiceRuntime Namespace][azure_runtime_ref_dotnet].
* Leggere le linee guida sulla sicurezza di Twilio all'indirizzo [https://www.twilio.com/docs/security][twilio_docs_security] .
* Altre informazioni su Twilio sono disponibili all'indirizzo [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a><a name="seealso"></a>Vedere anche
* [Come usare Twilio per le funzionalità voce ed SMS da Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: /previous-versions/azure/reference/ee741722(v=azure.100)
[azure_webroles_get_started]: ./cloud-services/cloud-services-dotnet-get-started.md