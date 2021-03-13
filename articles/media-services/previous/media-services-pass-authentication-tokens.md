---
title: Passaggio di token di autenticazione a Servizi multimediale di Azure | Microsoft Docs
description: Informazioni su come inviare i token di autenticazione dal client al servizio di distribuzione delle chiavi di Servizi multimediali di Azure
services: media-services
keywords: protezione del contenuto, Digital Rights Management, autenticazione del token
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: ba4daec8ef5f2cc1da3a211113e7236dfd22803d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008243"
---
# <a name="learn-how-clients-pass-tokens-to-the-azure-media-services-key-delivery-service"></a>Informazioni sul passaggio dei token al servizio di distribuzione delle chiavi di Servizi multimediali di Azure da parte dei client

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

I clienti chiedono spesso informazioni su come passare i token da un lettore al servizio di distribuzione delle chiavi di Servizi multimediali di Azure per la verifica, in modo che il lettore possa ottenere la chiave. Servizi multimediali supporta i formati SWT (Simple Web Token, token Web semplice) e JWT (JSON Web Token, token JSON Web). L'autenticazione di token può essere applicata a qualsiasi tipo di chiave, indipendentemente dal fatto che si esegua la crittografia comune o la crittografia envelope AES (Advanced Encryption Standard).

 Il token può essere passato con il lettore nei modi seguenti, a seconda del lettore e della piattaforma di destinazione:

- Tramite l'intestazione dell'autorizzazione HTTP.
    > [!NOTE]
    > Per le specifiche di OAuth 2.0 è previsto il prefisso "Bearer". Un lettore di esempio con la configurazione del token è ospitato nella [pagina di prova](https://ampdemo.azureedge.net/) di Azure Media Player. Scegliere **AES (token JWT)** o **AES (token SWT)** per impostare l'origine video. Il token viene passato tramite l'intestazione dell'autorizzazione.

- Tramite l'aggiunta di un parametro query URL con "token = tokenvalue".  
    > [!NOTE]
    > Non è previsto il prefisso "Bearer". Poiché il token viene inviato tramite un URL, è necessario blindare la stringa del token. Di seguito è riportato un codice di esempio C# che illustra come eseguire questa operazione:

    ```csharp
    string armoredAuthToken = System.Web.HttpUtility.UrlEncode(authToken);
    string uriWithTokenParameter = string.Format("{0}&token={1}", keyDeliveryServiceUri.AbsoluteUri, armoredAuthToken);
    Uri keyDeliveryUrlWithTokenParameter = new Uri(uriWithTokenParameter);
    ```

- Tramite il campo CustomData.
Questa opzione è utilizzata esclusivamente per l'acquisizione della licenza PlayReady tramite il campo CustomData della richiesta di acquisizione di licenze PlayReady. In questo caso, il token deve essere all'interno del documento XML, come descritto di seguito:

    ```xml
    <?xml version="1.0"?>
    <CustomData xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyCustomData/v1"> 
        <Token></Token> 
    </CustomData>
    ```
    Inserire il token di autenticazione nell'elemento Token.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
