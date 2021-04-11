---
title: Criteri di chiave simmetrica in servizi multimediali-Azure
description: Questo articolo illustra le caratteristiche dei criteri di chiave simmetrica e descrive come vengono usati da Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 6e60292c817ccad8eb1dd6cb3c33b944ab1c18a0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277686"
---
# <a name="content-key-policies"></a>Criteri di chiave simmetrica

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Con Servizi multimediali è possibile distribuire contenuti live e on demand crittografati dinamicamente con AES-128 (Advanced Encryption Standard) o con uno dei principali sistemi DRM (Digital Rights Management): Microsoft PlayReady, Google Widevine e Apple FairPlay. Servizi multimediali offre anche un servizio per la distribuzione di chiavi AES e licenze DRM (PlayReady, Widevine e FairPlay) ai client autorizzati. 

Per specificare le opzioni di crittografia nel flusso, è necessario creare un [criterio di flusso](stream-streaming-policy-concept.md) e associarlo al [localizzatore di streaming](stream-streaming-locators-concept.md). I criteri della [chiave](/rest/api/media/contentkeypolicies) simmetrica vengono creati per configurare il modo in cui la chiave simmetrica (che fornisce l'accesso sicuro agli [Asset](assets-concept.md)) viene fornita ai client finali. È necessario impostare i requisiti (restrizioni) per i criteri della chiave simmetrica che devono essere soddisfatti per poter recapitare le chiavi con la configurazione specificata ai client. I criteri chiave simmetrica non sono necessari per lo streaming non crittografato o il download. 

In genere, i criteri della chiave simmetrica vengono associati al [localizzatore di streaming](stream-streaming-locators-concept.md). In alternativa, è possibile specificare i criteri della chiave simmetrica in un [criterio di flusso](stream-streaming-policy-concept.md) , quando si creano criteri di flusso personalizzati per gli scenari avanzati. 

## <a name="best-practices-and-considerations"></a>Procedure consigliate e considerazioni

> [!IMPORTANT]
> Esaminare i consigli seguenti.

* È consigliabile progettare un set limitato di criteri per l'account del servizio multimediale e riutilizzarli per i localizzatori di streaming ogni volta che sono necessarie le stesse opzioni. Per altre informazioni, vedere [quote e limiti](limits-quotas-constraints-reference.md).
* I criteri chiave simmetrica sono aggiornabili. Possono essere necessari fino a 15 minuti per aggiornare le cache di distribuzione delle chiavi e selezionare i criteri aggiornati. 

   Se si aggiornano i criteri, si sta sovrascrivendo la cache della rete CDN esistente, che potrebbe causare un problema di riproduzione per i clienti che usano contenuto memorizzato nella cache.  
* Si consiglia di non creare nuovi criteri della chiave simmetrica per ogni asset. I principali vantaggi della condivisione degli stessi criteri della chiave simmetrica tra gli asset che richiedono le stesse opzioni dei criteri sono:
   
   * È più semplice gestire un numero ridotto di criteri.
   * Se è necessario aggiornare i criteri della chiave simmetrica, le modifiche vengono applicate immediatamente a tutte le nuove richieste di licenze.
* Se è necessario creare un nuovo criterio, è necessario creare un nuovo localizzatore di streaming per l'asset.
* È consigliabile consentire a servizi multimediali di generare automaticamente la chiave simmetrica. 

   In genere, si usa una chiave di lunga durata e si verifica l'esistenza dei criteri della chiave simmetrica con [Get](/rest/api/media/contentkeypolicies/get). Per recuperare la chiave, è necessario chiamare un metodo di azione separato per ottenere i segreti o le credenziali. Vedere l'esempio riportato di seguito.

## <a name="example"></a>Esempio

Per ottenere la chiave, usare `GetPolicyPropertiesWithSecretsAsync` , come illustrato nell'esempio [ottenere una chiave di firma da un criterio esistente](drm-get-content-key-policy-dotnet-how-to.md#get-contentkeypolicy-with-secrets) .

## <a name="filtering-ordering-paging"></a>Filtro, ordinamento, paging

Vedere [Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali](filter-order-page-entitites-how-to.md).

## <a name="additional-notes"></a>Note aggiuntive

* Le proprietà dei criteri della chiave simmetrica di `Datetime` tipo sono sempre in formato UTC.
* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="next-steps"></a>Passaggi successivi

* [Usare la crittografia dinamica AES-128 e il servizio di distribuzione delle chiavi](drm-playready-license-template-concept.md)
* [Usare la crittografia dinamica DRM e il servizio di distribuzione delle licenze](drm-protect-with-drm-tutorial.md)
* [Codice di esempio di streaming e crittografia chiave Clear AES Basic](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)
