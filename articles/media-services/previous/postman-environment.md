---
title: Importare l'ambiente Postman per le chiamate REST di Servizi multimediali di Azure
description: In questo argomento viene fornita una definizione dell'ambiente Postman per le chiamate REST di Servizi multimediali di Microsoft Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 2ccc765a3fb0063d92382edcec260808183f27a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89259252"
---
# <a name="import-the-postman-environment"></a>Importare l'ambiente Postman

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

Questo articolo contiene una definizione delle variabili di ambiente **Postman** usate nella [raccolta Postman](postman-collection.md) contenente richieste HTTP raggruppate che chiamano le API REST di Servizi multimediali. I file di ambiente e raccolta vengono usati dall'esercitazione [Configurare Postman per le chiamate API REST di Servizi multimediali](media-rest-apis-with-postman.md).

> [!NOTE]
> Valore di `AzureADSTSEndpoint `  =  `https://login.microsoftonline.com/{{TenantId}}/oauth2/token` . Per ottenere l'ID tenant, è possibile passare il puntatore del mouse sul nome utente nel portale (nell'angolo in alto a destra) e si troverà nella "directory: Microsoft ({{TENANTID}}).

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
