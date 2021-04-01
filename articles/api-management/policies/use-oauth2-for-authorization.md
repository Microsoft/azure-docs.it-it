---
title: "Esempio di criteri di gestione API di Azure: usare OAuth2 per l'autorizzazione tra gateway e back-end"
titleSuffix: Azure API Management
description: Esempio di criteri di Gestione API di Azure che illustra come usare OAuth2 per l'autorizzazione tra il gateway e un back-end. Mostra come ottenere un token di accesso da AAD e inoltrarlo al back-end.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 0f5a10eb2ebc3b3a7c527dd718e37faf03c927bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076097"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Usare OAuth2 per l'autorizzazione tra gateway e back-end

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come usare OAuth2 per l'autorizzazione tra il gateway e un back-end. Mostra come ottenere un token di accesso da AAD e inoltrarlo al back-end. 

Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-reference.md).

Lo script seguente usa le proprietà incluse in {{property}}. Per informazioni sulle proprietà e su come usarle nei criteri di Gestione API, vedere [questo](../api-management-howto-properties.md) argomento.
 
## <a name="policy"></a>Criteri

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-reference.md)