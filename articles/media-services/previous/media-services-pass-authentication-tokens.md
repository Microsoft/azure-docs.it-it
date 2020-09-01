---
title: Passaggio di token di autenticazione a Servizi multimediale di Azure | Microsoft Docs
description: Informazioni su come inviare i token di autenticazione dal client al servizio di distribuzione delle chiavi di Servizi multimediali di Azure
services: media-services
keywords: protezione del contenuto, Digital Rights Management, autenticazione del token
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7c3b35d9-1269-4c83-8c91-490ae65b0817
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 498e6d9e38468b126dbe10e0d0f59f54f1816ff0
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89261040"
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

- Tramite una playlist HTTP Live Streaming (HLS) alternativa. Se è necessario configurare l'autenticazione del token per AES + HLS la riproduzione su iOS/Safari, non esiste un modo per l'invio diretto nel token. Per ulteriori informazioni, vedere il [post di blog](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) su come alternare la playlist per abilitare questo scenario.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
