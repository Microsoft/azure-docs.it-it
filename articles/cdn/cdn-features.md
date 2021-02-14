---
title: Confronto tra funzionalità dei prodotti della rete per la distribuzione di contenuti di Azure (rete CDN)
description: Informazioni sulle funzionalità supportate da ogni prodotto della rete per la distribuzione di contenuti di Azure (rete CDN).
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3a794d8915c820abecd530911f05ac649e5e538c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379914"
---
# <a name="what-are-the-comparisons-between-azure-cdn-product-features"></a>Quali sono le differenze tra le funzionalità dei prodotti della rete CDN di Azure?

La rete CDN di Azure include quattro prodotti: 

* **Rete CDN Standard di Azure offerta da Microsoft**
* **Rete CDN Standard di Azure offerta da Akamai**
* **Rete CDN Standard di Azure offerta da Verizon**
* **Rete CDN Premium di Azure offerta da Verizon**. 

La tabella seguente confronta le funzionalità disponibili con ogni prodotto.

| **Ottimizzazioni e funzionalità per le prestazioni** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Accelerazione sito dinamico](./cdn-dynamic-site-acceleration.md)  | Offerto dal [servizio Frontdoor di Azure](../frontdoor/front-door-overview.md) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Accelerazione sito dinamico - Compressione di immagini adattiva](./cdn-dynamic-site-acceleration.md#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Accelerazione sito dinamico - Prelettura degli oggetti](./cdn-dynamic-site-acceleration.md#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Ottimizzazione distribuzione Web generale](./cdn-optimization-overview.md#general-web-delivery)  | **&#x2713;** | **&#x2713;** , selezionare questo tipo di ottimizzazione se le dimensioni medie dei file sono inferiori a 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Ottimizzazione dello streaming video](./cdn-media-streaming-optimization.md)  | tramite Distribuzione Web generale | **&#x2713;**  | tramite Distribuzione Web generale |  tramite Distribuzione Web generale |
| [Ottimizzazione di file di grandi dimensioni](./cdn-large-file-optimization.md)  | tramite Distribuzione Web generale | **&#x2713;** , selezionare questo tipo di ottimizzazione se le dimensioni medie dei file sono superiori a 10 MB   | tramite Distribuzione Web generale |  tramite Distribuzione Web generale |
| Cambia tipo di ottimizzazione | |**&#x2713;** | | |
| Porta dell'origine |Tutte le porte TCP |[Porte di origine consentite](/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Tutte le porte TCP |Tutte le porte TCP |
| [Bilanciamento del carico del server globale](../traffic-manager/traffic-manager-load-balancing-azure.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Eliminazione veloce](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** , le funzioni Elimina tutti e Wildcard purge (Eliminazione dei caratteri jolly) non sono attualmente supportate dalla rete CDN di Azure fornita da Akamai |**&#x2713;** |**&#x2713;** |
| [Precaricamento Asset](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Impostazioni cache/intestazioni (con [regole di memorizzazione nella cache](cdn-caching-rules.md))  |**&#x2713;** con il [motore regole Standard](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Motore di distribuzione di contenuti personalizzabile, basato su regole |**&#x2713;** con il [motore regole Standard](cdn-standard-rules-engine.md)  | | |**&#x2713;** con il [motore regole](./cdn-verizon-premium-rules-engine.md) |
| Impostazioni per cache/intestazione  |**&#x2713;** con il [motore regole Standard](cdn-standard-rules-engine.md) | | |**&#x2713;** con il [motore regole Premium](./cdn-verizon-premium-rules-engine.md) |
| Reindirizzamento/riscrittura URL |**&#x2713;** con il [motore regole Standard](cdn-standard-rules-engine.md)  | | |**&#x2713;** con il [motore regole Premium](./cdn-verizon-premium-rules-engine.md) |
| Regole per dispositivi mobili  |**&#x2713;** con il [motore regole Standard](cdn-standard-rules-engine.md) | | |**&#x2713;** con il [motore regole Premium](./cdn-verizon-premium-rules-engine.md) |
| [Memorizzazione nella cache della stringa di query](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 dual stack | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Supporto HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Sicurezza** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Supporto per HTTPS con endpoint della rete CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS dominio personalizzato](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** , per l'abilitazione è necessario un record CNAME diretto |**&#x2713;** |**&#x2713;** |
| [Supporto del nome di dominio personalizzato.](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtro geografico](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticazione tramite token](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Protezione DDoS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Usare un certificato personale](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Versioni di TLS supportate | TLS 1.2, TLS 1.0/1.1 - [Configurabile](/rest/api/cdn/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analisi e report** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Log di diagnostica di Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Report principali da Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Report personalizzati da Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Report HTTP avanzati](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Statistiche in tempo reale](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Prestazioni del nodo perimetrale](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Avvisi in tempo reale](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Semplicità d'uso** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Integrazione semplice con i servizi di Azure, come [Archiviazione](cdn-create-a-storage-account-with-cdn.md), [App Web](cdn-add-to-web-app.md) e [Servizi multimediali](../media-services/previous/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gestione tramite [API REST](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) oppure [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tipi MIME con compressione](./cdn-improve-performance.md)  |Solo predefinito |Configurabile |Configurabile  |Configurabile  |
| Codifiche di compressione  |gzip, brotli |gzip |gzip, deflate, bzip2, brotli  |gzip, deflate, bzip2, brotli  |

## <a name="migration"></a>Migrazione

Per informazioni sulla migrazione di un profilo di **rete CDN di Azure Standard di Verizon** a un profilo di **rete CDN di Azure Premium di Verizon**, vedere [Migrare un profilo di rete CDN di Azure da Standard Verizon a Premium Verizon](cdn-migrate.md). 

> [!NOTE]
> Anche se è previsto un percorso di aggiornamento da Standard Verizon a Premium Verizon, al momento non esiste un meccanismo di conversione tra altri prodotti.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [rete CDN di Azure](cdn-overview.md).
