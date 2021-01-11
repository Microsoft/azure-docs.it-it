---
title: Creare un'istanza di un'app client pubblica (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come creare un'istanza di un'applicazione client pubblica con le opzioni di configurazione utilizzando Microsoft Authentication Library per .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3e2ffebf0b414d4b59178fe04fb109530365786b
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064709"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Creare un'istanza di un'applicazione client pubblica con le opzioni di configurazione usando MSAL.NET

Questo articolo descrive come creare un'istanza di un' [applicazione client pubblica](msal-client-applications.md) usando Microsoft Authentication Library per .net (MSAL.NET).  Viene creata un'istanza dell'applicazione con le opzioni di configurazione definite in un file di impostazioni.

Prima di inizializzare un'applicazione, è prima necessario [registrarla](quickstart-register-app.md) in modo che l'app possa essere integrata con la piattaforma di identità Microsoft. Dopo la registrazione, è possibile che siano necessarie le informazioni seguenti (disponibili nella portale di Azure):

- ID client (una stringa che rappresenta un GUID)
- URL del provider di identità (denominato istanza) e destinatari dell'accesso per l'applicazione. Questi due parametri sono collettivamente noti come autorità.
- ID tenant se si sta scrivendo un'applicazione line-of-business esclusivamente per l'organizzazione (denominata anche applicazione a tenant singolo).
- Per le app Web e talvolta per le app client pubbliche (in particolare quando l'app deve usare un broker), è anche possibile impostare il redirectUri in cui il provider di identità contatterà l'applicazione con i token di sicurezza.


Nel file di configurazione di un'applicazione console .NET Core potrebbero essere presenti i *appsettings.js* seguenti:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Il codice seguente legge questo file usando .NET Configuration Framework:

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Il codice seguente consente di creare l'applicazione usando la configurazione dal file di impostazioni:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

