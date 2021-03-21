---
title: includere file
description: File di inclusione
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: include
ms.date: 02/18/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 53d837883daefddd5fa3f0f543eae1d116a5e86a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101102938"
---
| Risorsa | Limite |
| --- | --- |
| Risorse di Frontdoor di Azure per sottoscrizione | 100 |
| Host front-end, inclusi i domini personalizzati per risorsa | 500 |
| Regole di gestione per risorsa | 500 |
| Pool back-end per risorsa | 50 |
| Back-end per pool back-end | 100 |
| Criteri di percorso da soddisfare per una regola di gestione | 25 |
| URL in una singola chiamata di ripulitura della cache | 100 |
| Regole web application firewall personalizzate per criterio | 100 |
| Criteri di web application firewall per sottoscrizione | 100 |
| Condizioni di corrispondenza di web application firewall per regola personalizzata | 10 |
| Intervalli di indirizzi IP di web application firewall per condizione di corrispondenza | 600 |
| Valori di corrispondenza stringa di web application firewall per condizione di corrispondenza | 10 |
| Lunghezza dei valori di corrispondenza stringa di web application firewall | 256 |
| Lunghezza dei nomi dei parametri nel corpo POST di web application firewall | 256 |
| Lunghezza dei nomi di intestazioni HTTP di web application firewall | 256 |
| Lunghezza dei nomi di cookie di web application firewall | 256 |
| Dimensioni analizzate del corpo delle richieste HTTP di web application firewall | 128 KB |
| Lunghezza del corpo delle risposte personalizzate di web application firewall | 2 KB |

### <a name="azure-front-door-standardpremium-preview-service-limits"></a>Limiti dei servizi di Azure front door standard/Premium (anteprima)

*** *Massimo **500** profili standard e Premium totali per sottoscrizione.*

| Risorsa | Limite SKU standard | Limite SKU Premium |
| --- | --- | --- |
| Numero massimo di endpoint per profilo  | 10 | 25 |
| Massimo dominio personalizzato per profilo | 100 | 200 |
| Gruppo di origine massimo per profilo | 100 | 200 |
| Numero massimo di segreti per profilo | 100 | 200 |
| Numero massimo di criteri di sicurezza per profilo | 100 | 200 |
| Set di regole massimo per profilo | 100 | 200 |
| Numero massimo di regole per set di regole | 100 | 100 |
| Numero massimo di origini per gruppo di origine | 50 | 50 |
| Numero massimo di route per endpoint | 100 | 200 |
| Condizioni di corrispondenza di web application firewall per regola personalizzata | 10 | 10 |
| Intervalli di indirizzi IP di web application firewall per condizione di corrispondenza | 600 | 600 |
| Valori di corrispondenza stringa di web application firewall per condizione di corrispondenza | 10 | 10 |
| Lunghezza dei valori di corrispondenza stringa di web application firewall | 256 | 256 |
| Lunghezza dei nomi dei parametri nel corpo POST di web application firewall | 256 | 256 |
| Lunghezza dei nomi di intestazioni HTTP di web application firewall | 256 | 256 |
| Lunghezza dei nomi di cookie di web application firewall | 256 | 256|
| Dimensioni analizzate del corpo delle richieste HTTP di web application firewall | 128 KB | 128 KB |
| Lunghezza del corpo delle risposte personalizzate di web application firewall | 2 KB | 2 KB |

### <a name="timeout-values"></a>Valori di timeout
#### <a name="client-to-front-door"></a>Da client a Frontdoor
* Frontdoor ha un timeout per connessione TCP inattiva di 61 secondi.

#### <a name="front-door-to-application-back-end"></a>Da Frontdoor a back-end applicazione
* Se la risposta è suddivisa in blocchi, quando viene ricevuto il primo blocco viene restituito un messaggio 200.
* Dopo l'inoltro della richiesta HTTP al back-end, Frontdoor attende 30 secondi il primo pacchetto inviato dal back-end. Quindi restituisce un errore 503 al client. Questo valore può essere configurato tramite il campo sendRecvTimeoutSeconds nell'API.
    * Per gli scenari di memorizzazione nella cache, questo timeout non è configurabile e quindi, se una richiesta viene memorizzata nella cache e il primo pacchetto proveniente da Frontdoor o dal back-end impiega più di 30 secondi, al client viene restituito un errore 504. 
* Dopo aver ricevuto il primo pacchetto dal back-end, Frontdoor attende 30 secondi in timeout di inattività. Quindi restituisce un errore 503 al client. Questo valore di timeout non è configurabile.
* Il timeout delle sessioni TCP da Frontdoor al back-end è di 90 secondi.

### <a name="upload-and-download-data-limit"></a>Limite del caricamento e download dei dati

|  | Con codifica CTE (Chunked Transfer Encoding) | Senza suddivisione in blocchi HTTP |
| ---- | ------- | ------- |
| **Scaricare** | Nessun limite per le dimensioni di download. | Nessun limite per le dimensioni di download. |
| **Carica** |    Nessun limite, a condizione che le dimensioni di ogni caricamento CTE siano inferiori a 2 GB. | Le dimensioni non possono essere maggiori di 2 GB. |

### <a name="other-limits"></a>Altri limiti
* Dimensioni massime URL - 8.192 byte - Specifica la lunghezza massima dell'URL non elaborato (schema + nome host + porta + percorso + stringa di query dell'URL)
* Dimensioni massime stringa di query - 4.096 byte - Specifica la lunghezza massima della stringa di query, in byte.
* Dimensioni massime intestazioni di risposte HTTP dell'URL del probe di integrità - 4.096 byte - Specifica la lunghezza massima di tutte le intestazioni delle risposte dei probe di integrità. 
