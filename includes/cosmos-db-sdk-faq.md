---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179898"
---
**1. In che modo i clienti verranno avvisati circa l'SDK in fase di ritiro?**

Microsoft invierà una notifica anticipata 12 mesi prima fine del supporto dell'SDK in fase di ritiro per agevolare la transizione a un SDK supportato. I clienti verranno anche informati tramite vari canali di comunicazione, ad esempio il portale di gestione di Azure, il centro sviluppatori e comunicazioni dirette indirizzate agli amministratori dei servizi indicati.

**2. I clienti possono creare applicazioni utilizzando un SDK di Azure Cosmos DB ritirato "to-be" durante il periodo di 12 mesi?** 

Sì, durante il periodo di tolleranza di 12 mesi ai clienti sarà garantito l'accesso completo per creare, distribuire e modificare applicazioni tramite la versione dell'SDK di Azure Cosmos DB "in fase di ritiro". Durante il periodo di tolleranza di 12 mesi, i clienti sono invitati a eseguire la migrazione a una versione supportata più recente dell'SDK di Azure Cosmos DB.

**3. I clienti possono creare e modificare le applicazioni usando un SDK Cosmos DB di Azure ritirato dopo il periodo di notifica di 12 mesi?**

Dopo il periodo di notifica di 12 mesi, l'SDK verrà ritirato. La piattaforma Azure Cosmos DB non consentirà alcun tentativo di accesso ad Azure Cosmos DB da applicazioni che usano una versione di SDK ritirata. Inoltre, Microsoft non fornirà più il supporto tecnico per la versione di SDK ritirata.

**4. Cosa succede alle applicazioni in esecuzione del cliente che usano la versione azure Cosmos DB SDK non supportata?**

Qualsiasi tentativo di connessione al servizio Azure Cosmos DB con una versione di SDK ritirata verrà rifiutato. 

**5. Le nuove caratteristiche e funzionalità verranno applicate a tutti gli SDK non ritirati?**

Le nuove funzioni e funzionalità verranno aggiunte solo alle nuove versioni. Se si usa una versione di SDK precedente non ritirata, le richieste inviate ad Azure Cosmos DB continueranno a funzionare come in precedenza, ma non sarà possibile accedere alle nuove funzionalità.  

**6. Cosa devo fare se non posso aggiornare la mia domanda prima di una data limite?**

Si consiglia di effettuare l'aggiornamento alla versione di SDK più recente quanto prima. Una volta che un SDK è stato contrassegnato per il ritiro, l'utente ha a disposizione 12 mesi per aggiornare l'applicazione. Se, per qualunque motivo, non è possibile completare l'aggiornamento dell'applicazione entro questo periodo di tempo, contattare il [team di Cosmos DB](mailto:askcosmosdb@microsoft.com) e richiedere assistenza prima della data prevista per il ritiro.

