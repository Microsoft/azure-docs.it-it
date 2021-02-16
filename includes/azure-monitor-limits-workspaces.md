---
title: File di inclusione
description: File di inclusione
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 1f461c4870b398a49387472c49d45b90929405c7
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100529667"
---
**Conservazione e volume della raccolta dati** 

| Livello | Limite giornaliero | Conservazione dei dati | Commento |
|:---|:---|:---|:---|
| Piano tariffario Per GB corrente<br>(introdotto nel mese di aprile 2018) | Nessun limite | 30 - 730 giorni | La conservazione dei dati oltre 31 giorni è disponibile con l'addebito di costi aggiuntivi. Altre informazioni sui prezzi di Monitoraggio di Azure. |
| Livelli gratuiti legacy<br>(introdotto nel mese di aprile 2016) | 500 MB | 7 giorni | Quando l'area di lavoro raggiunge il limite di 500 MB al giorno, l'inserimento dei dati si interrompe e riprende all'inizio del giorno successivo. Un giorno si basa su UTC. Si noti che i dati raccolti dal Centro sicurezza di Azure non sono inclusi in questo limite di 500 MB al giorno e continueranno a essere raccolti oltre questo limite.  |
| Livello Per GB autonomo legacy<br>(introdotto nel mese di aprile 2016) | Nessun limite | 30 - 730 giorni | La conservazione dei dati oltre 31 giorni è disponibile con l'addebito di costi aggiuntivi. Altre informazioni sui prezzi di Monitoraggio di Azure. |
| Per nodo legacy (OMS)<br>(introdotto nel mese di aprile 2016) | Nessun limite | 30 - 730 giorni | La conservazione dei dati oltre 31 giorni è disponibile con l'addebito di costi aggiuntivi. Altre informazioni sui prezzi di Monitoraggio di Azure. |
| Livello Standard legacy | Nessun limite | 30 giorni  | Non è possibile modificare la conservazione |
| Livello Premium legacy | Nessun limite | 365 giorni  | Non è possibile modificare la conservazione |

**Numero di aree di lavoro per sottoscrizione.**

| Piano tariffario    | Limite area di lavoro | Commenti
|:---|:---|:---|
| Livello gratuito  | 10 | Non è possibile aumentare questo limite. |
| Tutti gli altri livelli | Nessun limite | Il limite è costituito dal numero di risorse all'interno di un gruppo di risorse e dal numero di gruppi di risorse per ogni sottoscrizione. |

**Portale di Azure**

| Category | Limite | Commenti |
|:---|:---|:---|
| Numero massimo di record restituiti da una query su log | 10,000 | Ridurre i risultati usando l'ambito della query, l'intervallo di tempo e i filtri nella query. |


**API di raccolta dati**

| Category | Limite | Commenti |
|:---|:---|:---|
| Dimensioni massime per un singolo post | 30 MB | Dividere i volumi più grandi in più post. |
| Dimensioni massime per i valori dei campi  | 32 KB | I campi con una lunghezza superiore a 32 KB vengono troncati. |

**Cerca l'API**

| Category | Limite | Commenti |
|:---|:---|:---|
| Numero massimo di record restituiti in una singola query | 500.000 | |
| Dimensioni massime dei dati restituiti | 64 milioni di byte (~61 MiB)| |
| Tempo di esecuzione massimo query | 10 minuti | Per informazioni dettagliate, vedere [Timeout](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts).  |
| Frequenza massima richieste | 200 richieste per 30 secondi per utente di Azure AD o indirizzo IP client | Per informazioni dettagliate, vedere [Limiti di frequenza](https://dev.loganalytics.io/documentation/Using-the-API/Limits). |

**Connettore dei log di Monitoraggio di Azure**
| Category | Limite | Commenti |
|:---|:---|:---|
| Numero massimo di record | 500.000 | |
| Timeout max query | 110 secondo | |
| Grafici | | La visualizzazione nella pagina log e il connettore usano librerie di grafici diverse e alcune funzionalità non sono attualmente disponibili nel connettore. |

**Limiti generali dell'area di lavoro**

| Category | Limite | Commenti |
|:---|:---|:---|
| Numero massimo di colonne in una tabella         | 500 | |
| Numero massimo di caratteri per nome di colonna | 500 | |

**<a name="data-ingestion-volume-rate">Velocità del volume di inserimento dati</a>**

Monitoraggio di Azure è un servizio dati su larga scala che serve migliaia di clienti che inviano terabyte di dati ogni mese a un ritmo crescente. Il limite per la velocità del volume ha lo scopo di isolare i clienti di Monitoraggio di Azure da picchi di inserimento improvvisi nell'ambiente multi-tenant. Nelle aree di lavoro viene definita una soglia per la velocità del volume di inserimento predefinita di 500 MB (dati compressi), che equivale a circa **6 GB/min** di dati non compressi. Le dimensioni effettive possono variare tra i tipi di dati a seconda della lunghezza del log e del relativo rapporto di compressione. Il limite di velocità del volume si applica ai dati inseriti da risorse di Azure tramite [Impostazioni di diagnostica](../articles/azure-monitor/platform/diagnostic-settings.md). Quando viene raggiunto il limite di velocità del volume, un meccanismo di ripetizione dei tentativi prova a inserire i dati 4 volte in un periodo di 30 minuti e, se l'operazione non riesce, li rimuove. Ciò non si applica ai dati inseriti da [agenti](../articles/azure-monitor/platform/agents-overview.md) o dall'[API dell'agente di raccolta dati](../articles/azure-monitor/platform/data-collector-api.md).

Quando si inviano dati a un'area di lavoro a una velocità del volume superiore all'80% della soglia configurata nell'area di lavoro, viene inviato un evento alla tabella delle *operazioni* nell'area di lavoro ogni 6 ore fintanto che la soglia continua a essere superata. Quando la velocità del volume è superiore alla soglia, alcuni dati vengono eliminati e un evento viene inviato alla tabella delle *operazioni* nell'area di lavoro ogni 6 ore durante il periodo in cui la soglia continua a essere superata. Se il volume di inserimento continua a superare la soglia o se si prevede di raggiungerlo presto, è possibile richiedere un aumento aprendo una richiesta di supporto. 

Per creare regole di avviso in modo da ricevere notifiche proattive quando si raggiungono i limiti di inserimento dati, vedere [Monitorare l'integrità dell'area di lavoro Log Analytics in Monitoraggio di Azure](../articles/azure-monitor/platform/monitor-workspace.md).

>[!NOTE]
>A seconda di quando si è iniziato a usare Log Analytics, è possibile che si disponga dell'accesso ai piani tariffari legacy. Altre informazioni sui [piani tariffari legacy di Log Analytics](../articles/azure-monitor/platform/manage-cost-storage.md#legacy-pricing-tiers).