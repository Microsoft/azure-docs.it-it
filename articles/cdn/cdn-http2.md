---
title: Supporto HTTP/2 nella rete CDN di Azure| Microsoft Docs
description: La rete per la distribuzione di contenuti di Azure supporta HTTP/2, che offre vantaggi rispetto a HTTP/1, ad esempio il multiplexing & concorrenza, la compressione delle intestazioni e le dipendenze di flusso.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: d4ed04185da251d9042f6b54a192d0e49ef20ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778541"
---
# <a name="http2-support-in-azure-cdn"></a>Supporto HTTP/2 nella rete CDN di Azure

HTTP/2 è una revisione principale del protocollo HTTP/1.1\. Fornisce prestazioni Web più veloci, riduce il tempo di risposta e offre un'esperienza utente migliore pur mantenendo invariati i metodi, i codici di stato e la semantica di HTTP. Anche se HTTP/2 è progettato per funzionare con HTTP e HTTPS, molti browser Web client supportano solo HTTP/2 su TLS.

### <a name="http2-benefits"></a>Vantaggi di HTTP/2

I vantaggi di HTTP/2 includono:

*   **Multiplexing e concorrenza**

    Con HTTP 1.1, se si eseguono più richieste di risorse multiple sono necessarie più connessioni TCP e a ogni connessione è associato un overhead delle prestazioni. HTTP/2 consente di richiedere più risorse in un'unica connessione TCP.

*   **Compressione delle intestazioni**

    Comprimendo le intestazioni HTTP per le risorse servite, il tempo di trasferimento è ridotto in modo significativo.

*   **Dipendenze del flusso**

    Le dipendenze del flusso consentono al client di indicare al server le risorse con priorità.


## <a name="http2-browser-support"></a>Supporto browser HTTP/2

Tutti i browser principali hanno implementato il supporto HTTP/2 le versioni correnti. Per i browser non supportati viene eseguito il fallback automatico a HTTP/1.1.

|Browser|Versione minima|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Abilitazione del supporto HTTP/2 nella rete CDN di Azure

Il supporto HTTP/2 è attualmente attivo per tutti i profili di rete CDN di Azure. Non è necessaria alcuna azione da parte dei clienti.

## <a name="next-steps"></a>Passaggi successivi

Per visualizzare i vantaggi di HTTP/2 in azione, vedere [questa demo di Akamai](https://http2.akamai.com/demo).

Per altre informazioni su HTTP/2, vedere le risorse seguenti:

*   [Home page delle specifiche HTTP/2](https://http2.github.io/)
*   [Domande frequenti su HTTP/2](https://http2.github.io/faq/)
*   [Informazioni su HTTP/2 di Akamai](https://http2.akamai.com/)

Per altre informazioni sulle funzionalità disponibili della rete CDN di Azure, vedere [Panoramica della rete CDN di Azure](./cdn-overview.md).