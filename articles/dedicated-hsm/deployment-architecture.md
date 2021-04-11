---
title: Architettura di distribuzione - HSM dedicato di Azure | Microsoft Docs
description: Considerazioni sulla progettazione di base quando si usa il modulo HSM dedicato di Azure come parte di un'architettura di applicazione
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: c454b2e4df7a9ce5fadd33386e5bb413b503c6e4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608426"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Architettura di distribuzione del servizio HSM dedicato di Azure

HSM dedicato di Azure offre l'archiviazione di chiavi crittografiche in Azure. Soddisfa requisiti rigorosi di sicurezza. L'uso di HSM dedicato di Azure dedicato sarà utile ai clienti che:

* Deve soddisfare la certificazione [FIPS 140-2 Level-3](https://csrc.nist.gov/publications/detail/fips/140/2/final)
* Devono avere accesso esclusivo al modulo di protezione hardware
* Devono avere il controllo completo sui propri dispositivi

I moduli di protezione hardware vengono distribuiti nei data center di Microsoft e il provisioning può essere facilmente effettuato come coppia di dispositivi, come base per una soluzione a disponibilità elevata. Possono inoltre essere distribuiti in più aree, per una soluzione resiliente alle emergenze. Le aree con modulo di protezione hardware dedicato attualmente disponibile possono essere controllate usando la [pagina prodotti in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=azure-dedicated-hsm). 

* Stati Uniti orientali
* Stati Uniti orientali 2
* Stati Uniti occidentali
* Stati Uniti occidentali 2
* Stati Uniti centro-meridionali
* Asia sud-orientale
* Asia orientale
* India centrale
* India meridionale
* Giappone orientale
* Giappone occidentale
* Europa settentrionale
* Europa occidentale
* Regno Unito meridionale
* Regno Unito occidentale
* Canada centrale
* Canada orientale
* Australia orientale
* Australia sud-orientale
* Svizzera settentrionale
* Svizzera occidentale
* US Gov Virginia
* US Gov Texas

In ognuna di queste aree, i rack di moduli di protezione hardware sono distribuiti all'interno di due data center indipendenti o in almeno due zone di disponibilità indipendenti. L'Asia sud-orientale ha tre zone di disponibilità, l'area Stati Uniti orientali 2 ne ha tre. Sono presenti un totale di venti tre aree in Europa, Asia e America del Nord che offrono il servizio HSM dedicato. Per altre informazioni sulle aree di Azure, vedere le [informazioni ufficiali sulle aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).
Alcuni fattori di progettazione per qualsiasi soluzione basata su HSM dedicato sono posizione/latenza, disponibilità elevata e supporto per altre applicazioni distribuite.

## <a name="device-location"></a>Posizione dispositivo

La posizione ottimale per un dispositivo HSM è l'area più vicina alle applicazioni che eseguono le operazioni di crittografia. La latenza prevista all'interno dell'area è di meno di 10 millisecondi. La latenza tra più aree può essere da 5 a 10 volte superiore.

## <a name="high-availability"></a>Disponibilità elevata

Per ottenere la disponibilità elevata, un cliente deve usare due dispositivi HSM in un'area configurata usando il software Thales come coppia di disponibilità elevata. Questo tipo di distribuzione assicura la disponibilità delle chiavi se in un singolo dispositivo si verifica un problema che impedisce l'elaborazione di operazioni relative alle chiavi. Riduce inoltre notevolmente il rischio quando si eseguono interventi di manutenzione in garanzia, come la sostituzione dell'alimentatore. È importante che la progettazione tenga conto di qualsiasi tipo di errore a livello di area. Possono verificarsi errori a livello di area in caso di calamità naturali, come terremoti, uragani o inondazioni. Gli eventi di questo tipo devono essere attenuati effettuando il provisioning di dispositivi HSM in un'altra area. I dispositivi distribuiti in un'altra area possono essere combinati tramite la configurazione software di Thales. Ciò significa che la distribuzione minima per una soluzione a disponibilità elevata e resiliente alle emergenze è composta da quattro dispositivi HSM in due aree. È possibile usare la ridondanza locale e la ridondanza tra aree come baseline a cui aggiungere altre distribuzioni di dispositivi HSM per supportare la latenza, la capacità o per soddisfare altri requisiti specifici dell'applicazione.

## <a name="distributed-application-support"></a>Supporto di applicazioni distribuite

I dispositivi di tipo HSM dedicato vengono in genere distribuiti a supporto delle applicazioni che devono eseguire operazioni di archiviazione e recupero chiavi. Hanno 10 partizioni, per il supporto di applicazioni indipendenti. La posizione del dispositivo deve essere basata su una visione olistica di tutte le applicazioni che devono usare il servizio.

## <a name="next-steps"></a>Passaggi successivi

Una volta determinata l'architettura di distribuzione, la maggior parte delle attività di configurazione per implementare tale architettura verrà fornita da Thales. Questo include la configurazione del dispositivo e gli scenari di integrazione di applicazioni. Per ulteriori informazioni, utilizzare il portale di [supporto clienti di Thales](https://supportportal.thalesgroup.com/csm) e scaricare le guide all'amministrazione e alla configurazione. Il sito per i partner Microsoft offre un'ampia gamma di guide all'integrazione.
Prima di eseguire il provisioning di dispositivi o di iniziare con la progettazione o distribuzione di applicazioni, è consigliabile aver ben compreso tutti i concetti chiave relativi al servizio, ad esempio disponibilità elevata e sicurezza.
Altri argomenti sui concetti:

* [Disponibilità elevata](high-availability.md)
* [Sicurezza fisica](physical-security.md)
* [Rete](networking.md)
* [Supporto](supportability.md)
* [Monitoring](monitoring.md)
