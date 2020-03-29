---
title: Tradurre in ambienti protetti da firewall - API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: L'API Traduzione testo di Servizi cognitivi di Azure può essere convertita tramite firewall usando il nome di dominio o il filtro IP.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cd7904fedd3ab3f64315cb6f98d99b8fd12254f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837394"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Come tradurre in ambienti protetti da firewall IP con l'API Traduzione testuale

L'API Traduzione testuale consente di tradurre in ambienti protetti da firewall usando il filtro basato su nome di dominio o IP. Il metodo preferito è il filtro basato su nome di dominio. **Non è consigliabile** eseguire Microsoft Translator in un ambiente protetto da firewall con filtro basato su IP. Il programma di installazione è soggetto a errori in futuro senza preavviso.

## <a name="translator-ip-addresses"></a>Indirizzi IP di Translator
Gli indirizzi IP per api.cognitive.microsofttranslator.com - Microsoft Translator Text API a partire dal 21 agosto 2019:

* **Asia Pacifico:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Nord America:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Tradurre in ambienti protetti da firewall IP nella chiamata API di Translator](reference/v3-0-translate.md)
