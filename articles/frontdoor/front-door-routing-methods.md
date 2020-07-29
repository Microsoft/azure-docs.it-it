---
title: Sportello anteriore di Azure-metodi di routing del traffico | Microsoft Docs
description: Questo articolo fornisce informazioni sui diversi metodi di routing del traffico usati da Frontdoor
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: b7dd00d28ecfe844094677e0ae19f4fd359d97d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687800"
---
# <a name="front-door-routing-methods"></a>Metodi di routing di Frontdoor

Lo sportello anteriore di Azure supporta vari metodi di routing del traffico per determinare come indirizzare il traffico HTTP/HTTPS ai vari endpoint di servizio. Per ognuna delle richieste client che raggiunge Frontdoor, viene applicato il metodo di routing configurato per garantire che le richieste vengano inoltrate all'istanza di back-end migliore. 

Esistono quattro concetti principali in relazione al routing del traffico in Frontdoor:

* **[Latenza](#latency):** il routing basato sulla latenza assicura che le richieste vengano inviate al back-end con la latenza più bassa accettabile all'interno di un intervallo di sensibilità. Sostanzialmente, le richieste utente vengono inviate al set di back-end "più vicino" rispetto alla latenza della rete.
* ** [Priorità](#priority):** È possibile assegnare le priorità ai back-end diversi quando si vuole usare un back-end del servizio primario per tutto il traffico e fornire backup nel caso in cui il back-end primario o di backup non sia disponibile.
* **[Peso](#weighted):** è possibile assegnare un peso ai diversi back-end quando si vuole distribuire il traffico in un set back-end, in modo uniforme o in base ai coefficienti di peso.
* **[Affinità di sessione](#affinity):** è possibile configurare l'affinità di sessione per gli host o i domini front-end quando le richieste successive da un utente devono essere inviate allo stesso back-end, a condizione che la sessione utente sia ancora attiva e l'istanza di back-end sia ancora segnalata come integra in base ai probe di integrità. 

Tutte le configurazioni di Frontdoor includono il monitoraggio dell'integrità back-end e il failover globale immediato automatizzato. Per altre informazioni, vedere [Front Door Backend Monitoring](front-door-health-probes.md) (Monitoraggio del back-end Frontdoor). Frontdoor può essere configurato per operare in base a un singolo metodo di routing. A seconda delle esigenze dell'applicazione, è comunque possibile usare diversi o tutti questi metodi di routing in combinazione per creare una topologia di routing ottimale.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Routing del traffico in base alla latenza più bassa

La velocità di risposta di molte applicazioni può essere migliorata distribuendo i back-end in due o più posizioni a livello globale e indirizzando il traffico alla posizione più vicina agli utenti finali. Il metodo predefinito di routing del traffico per la configurazione di Frontdoor inoltra le richieste dagli utenti finali al back-end più vicino dall'ambiente Frontdoor che ha ricevuto la richiesta. In combinazione con l'architettura Anycast di Azure front door, questo approccio garantisce che ogni utente finale ottenga le massime prestazioni personalizzate in base alla propria posizione.

Il back-end più vicino non è necessariamente il più vicino in termini di distanza geografica. Frontdoor determina invece il back-end più vicino misurando la latenza di rete. Per altre informazioni, vedere [Front Door's routing architecture](front-door-routing-architecture.md) (Architettura di routing di Frontdoor). 

Di seguito è illustrato il flusso complessivo delle decisioni:

| Back-end disponibili | Priorità | Segnale di latenza (basato sui probe di integrità) | Pesi |
|-------------| ----------- | ----------- | ----------- |
| In primo luogo, selezionare tutti i back-end che sono abilitati e restituiti come integri (200 OK) per il probe di integrità. Supponiamo che siano presenti sei back-end A, B, C, D, E e F, tra i quali C non è integro ed E è disabilitato. Pertanto, l'elenco dei back-end disponibili è A, B, D e F.  | Vengono quindi selezionati i back-end prioritari tra quelli disponibili. Supponiamo che i back-end A, B e D abbiano la priorità 1 e che il back-end F abbia la priorità 2. I back-end selezionati saranno A, B e D.| Selezionare i back-end con l'intervallo di latenza (latenza inferiore e sensibilità di latenza in ms specificata). Supponiamo che A sia a 15 ms, B a 30 ms e D a 60 ms dall'ambiente Frontdoor in cui è stata ricevuta la richiesta e che la sensibilità di latenza sia di 30 ms. In questo caso, il pool con la latenza più bassa è costituito dai back-end A e B, perché D è a oltre 30 ms dal back-end più vicino (A). | Infine, Frontdoor eseguirà il round robin del traffico tra il pool selezionato finale di back-end nel rapporto di pesi specificati. Se il back-end A ha un peso di 5 e il back-end B ha un peso di 8, il traffico verrà distribuito in un rapporto di 5:8 tra i back-end A e B. |

>[!NOTE]
> Per impostazione predefinita, la proprietà di sensibilità di latenza è impostata su 0 ms, ovvero inoltrare sempre la richiesta al back-end più veloce disponibile.


## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Routing del traffico basato sulla priorità

Un'organizzazione vuole spesso offrire la massima affidabilità per i propri servizi dotandosi di uno o più servizi di backup in caso di inattività del servizio primario. A livello di settore, questa topologia è anche denominata topologia di distribuzione attiva/standby o attiva/passiva. Il metodo di routing del traffico "Priorità" consente ai clienti di Azure di implementare facilmente questo modello di failover.

Il sistema Frontdoor predefinito contiene un elenco con uguale priorità dei back-end. Per impostazione predefinita, Frontdoor invia il traffico solo ai back-end con la massima priorità (il valore più basso per la priorità), ovvero il set primario di back-end. Se i back-end primari non sono disponibili, Frontdoor instrada il traffico al set secondario di back-end (il secondo valore più basso per la priorità). Se i back-end primari e secondari non sono disponibili, il traffico viene indirizzato al terzo e così via. La disponibilità del back-end si basa sullo stato configurato (abilitato o disabilitato) e lo stato di integrità del back-end, determinato continuamente tramite i probe di integrità.

### <a name="configuring-priority-for-backends"></a>Configurazione della priorità per i back-end

Ognuno dei back-end nel pool back-end all'interno della configurazione di Frontdoor include una proprietà denominata "Priorità", che può essere un numero compreso tra 1 e 5. Con Azure front door è possibile configurare la priorità di back-end in modo esplicito usando questa proprietà per ogni back-end. Questa proprietà accetta un valore compreso tra 1 e 5, dove i valori più bassi rappresentano una priorità più elevata. I back-end possono condividere i valori di priorità.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Metodo di routing del traffico Ponderato
Il metodo di routing del traffico "Ponderato" consente di distribuire il traffico tra tutti gli endpoint in modo uniforme o con un peso predefinito.

Nel metodo di routing del traffico "Ponderato" viene assegnato un peso a ogni back-end nella configurazione di Frontdoor del pool back-end. Ogni peso è un numero intero compreso tra 1 e 1000. Questo parametro usa un peso predefinito pari a "50".

Tra l'elenco dei back-end disponibili entro la sensibilità di latenza accettata (come specificato), il traffico viene distribuito in un meccanismo round robin nel rapporto dei pesi specificati. Se la sensibilità di latenza è impostata su 0 millisecondi, questa proprietà non avrà effetto a meno che non siano disponibili due back-end con la stessa latenza di rete. 

Il metodo "Ponderato" abilita alcuni scenari utili:

* **Aggiornamento graduale dell'applicazione**: allocare una percentuale di traffico da indirizzare a un nuovo back-end, quindi incrementare gradualmente il traffico nel tempo per portarlo al livello degli altri back-end.
* **Migrazione dell'applicazione in Azure**: creare un pool back-end con back-end di Azure ed esterni. Regolare il peso dei back-end per preferire i nuovi back-end. È possibile applicare questa configurazione gradualmente, iniziando con i nuovi back-end disabilitati, quindi assegnando loro i pesi più bassi e aumentandoli lentamente fino ai livelli di massimo traffico. È infine possibile disabilitare i back-end con preferenza inferiore e rimuoverli dal pool.  
* **Espansione del cloud per capacità aggiuntiva**: espandere rapidamente una distribuzione locale nel cloud posizionandola dietro Frontdoor. In caso di necessità di capacità aggiuntiva nel cloud, si possono aggiungere o abilitare più back-end e si può specificare la quantità di traffico da indirizzare a ogni back-end.

## <a name="session-affinity"></a><a name = "affinity"></a>Affinità di sessione
Per impostazione predefinita, senza affinità di sessione, il portello anteriore Invia le richieste provenienti dallo stesso client a backend diversi in base alla configurazione del bilanciamento del carico in particolare come le latenze per i diversi backend cambiano o se le richieste diverse provenienti dallo stesso utente vengono riportate in un altro ambiente di front-end. Tuttavia, per alcune applicazioni con stato o in determinati scenari è preferibile inoltrare le richieste successive dallo stesso utente allo stesso back-end che ha elaborato la richiesta iniziale. L'affinità di sessione basata su cookie è utile quando si vuole mantenere una sessione utente nello stesso back-end. Usando i cookie gestiti della porta anteriore, l'ingresso di Azure può indirizzare il traffico successivo da una sessione utente allo stesso back-end per l'elaborazione, purché il back-end sia integro e la sessione utente non sia scaduta. 

L'affinità di sessione può essere abilitata a livello di host front-end per tutti i domini (o i sottodomini) configurati. Una volta abilitata, Frontdoor aggiunge un cookie alla sessione dell'utente. L'affinità di sessione basata su cookie consente a Frontdoor di identificare i diversi utenti, anche se usano lo stesso indirizzo IP, permettendo una distribuzione più uniforme del traffico tra back-end differenti.

La durata del cookie corrisponde a quella della sessione utente, perché Frontdoor attualmente supporta solo i cookie di sessione. 

> [!NOTE]
> I proxy pubblici possono interferire con l'affinità di sessione, perché per stabilire una sessione è necessario che Frontdoor aggiunga un cookie di affinità di sessione nella risposta, operazione che non può essere eseguita se la risposta è memorizzabile nella cache, in quanto potrebbe compromettere i cookie di altri client che richiedono la stessa risorsa. Per proteggersi da questa situazione, l'affinità di sessione **non** verrà stabilita se il back-end invia una risposta memorizzabile nella cache quando si tenta di eseguire questa operazione. Se la sessione è già stata stabilita, non importa se la risposta dal back-end è memorizzabile nella cache.
> L'affinità di sessione verrà stabilita nelle circostanze seguenti, **a meno che** la risposta non abbia un codice di stato HTTP 304:
> - Per la risposta sono stati impostati valori specifici per l' ```Cache-Control``` intestazione che impedisce la memorizzazione nella cache, ad esempio "privato" o nessun archivio ".
> - La risposta contiene un'intestazione ```Authorization``` non scaduta.
> - La risposta contiene un codice di stato HTTP 302.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
