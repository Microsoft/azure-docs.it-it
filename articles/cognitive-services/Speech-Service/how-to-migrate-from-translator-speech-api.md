---
title: Eseguire la migrazione dalla API Traduzione vocale al servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come eseguire la migrazione delle applicazioni dal API Traduzione vocale al servizio di riconoscimento vocale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: aahi
ms.openlocfilehash: 2fb03721baa80e77a5fd387600a272e6b1cfc7d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "95013649"
---
# <a name="migrate-from-the-translator-speech-api-to-the-speech-service"></a>Eseguire la migrazione dalla API Traduzione vocale al servizio riconoscimento vocale

Usare questo articolo per eseguire la migrazione delle applicazioni da Microsoft API Traduzione vocale al [servizio di riconoscimento vocale](index.yml). Questa guida descrive le differenze tra il servizio di API Traduzione vocale e riconoscimento vocale e suggerisce strategie per la migrazione delle applicazioni.

> [!NOTE]
> La chiave di sottoscrizione API Traduzione vocale non verrà accettata dal servizio di riconoscimento vocale. È necessario creare una nuova sottoscrizione del servizio di riconoscimento vocale.

## <a name="comparison-of-features"></a>Confronto delle funzionalità

| Funzionalità                                           | API Traduzione vocale                                  | Servizio Voce | Dettagli                                                                                                                                                                                                                                                                            |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Traduzione in testo                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Traduzione in voce                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Endpoint globale                                   | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Il servizio riconoscimento vocale non offre un endpoint globale. Un endpoint globale può indirizzare automaticamente il traffico all'endpoint più vicino a livello di area, riducendo la latenza dell'applicazione.                                                    |
| Endpoint a livello di area                                | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Limite di tempo della connessione                             | 90 minuti                                               | Senza limiti con l'SDK. 10 minuti con una connessione WebSocket.                                                                                                                                                                                                                                                                                   |
| Chiave di autenticazione nell'intestazione                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Più lingue tradotte in una singola richiesta | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| SDK disponibili                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Vedere la [documentazione del servizio vocale](index.yml) per gli SDK disponibili.                                                                                                                                                    |
| Connessioni WebSocket                            | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| API di lingue                                     | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Il servizio riconoscimento vocale supporta la stessa gamma di linguaggi descritti nell'articolo di riferimento per le [Lingue di traduzione]() . |
| Marcatore e filtro per contenuto volgare                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| .WAV/PCM come input                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Altri tipi di file come input                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Risultati parziali                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |
| Informazioni di temporizzazione                                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                 |
| ID correlazione                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |
| Modelli conversione voce/testo personalizzati                              | :heavy_minus_sign:                                              | :heavy_check_mark:                 | Il servizio di riconoscimento vocale offre modelli di riconoscimento vocale personalizzati che consentono di personalizzare il riconoscimento vocale per il vocabolario univoco dell'organizzazione.                                                                                                                                           |
| Modelli di traduzione personalizzati                         | :heavy_minus_sign:                                              | :heavy_check_mark:                 | La sottoscrizione all'API Traduzione testuale Microsoft consente di usare il [traduttore personalizzato](https://www.microsoft.com/translator/business/customization/) per usare i propri dati per traduzioni più accurate.                                                 |

## <a name="migration-strategies"></a>Strategie di migrazione

Se l'utente o l'organizzazione dispone di applicazioni in fase di sviluppo o produzione che usano la API Traduzione vocale, è necessario aggiornarle per usare il servizio di riconoscimento vocale. Vedere la documentazione del [servizio vocale](index.yml) per SDK, esempi di codice ed esercitazioni disponibili. Quando si esegue la migrazione, tenere presente quanto segue:

* Il servizio riconoscimento vocale non offre un endpoint globale. Determinare se l'applicazione funziona in modo efficiente quando usa un singolo endpoint a livello di area per tutto il traffico. In caso contrario, usare la georilevazione per determinare l'endpoint più efficiente.

* Se l'applicazione usa connessioni di lunga durata e non può usare gli SDK disponibili, è possibile usare una connessione WebSocket. Gestire il limite di timeout di 10 minuti eseguendo la riconnessione nei momenti appropriati.

* Se l'applicazione usa il servizio di conversione e API Traduzione vocale per abilitare modelli di traduzione personalizzati, è possibile aggiungere ID di categoria direttamente usando il servizio di riconoscimento vocale.

* Diversamente dalla API Traduzione vocale, il servizio riconoscimento vocale può completare le traduzioni in più lingue in un'unica richiesta.

## <a name="next-steps"></a>Passaggi successivi

* [Prova gratuitamente il servizio vocale](overview.md#try-the-speech-service-for-free)
* [Avvio rapido: Riconoscimento vocale in un'applicazione piattaforma UWP con Speech SDK](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Vedi anche

* [Informazioni sul servizio di riconoscimento vocale](overview.md)
* [Documentazione per il servizio riconoscimento vocale e l'SDK vocale](./speech-devices-sdk-quickstart.md?pivots=platform-android)