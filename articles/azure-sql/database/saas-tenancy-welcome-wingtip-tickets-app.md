---
title: Benvenuti nell'app wingtips
description: Informazioni sui modelli di tenancy database e sull'applicazione SaaS Wingtips di esempio per il database SQL di Azure nell'ambiente cloud.
keywords: esercitazione database SQL
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: a2969ce6ceda0d1b71ec991b32f5b10acf9bfa12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780377"
---
# <a name="the-wingtip-tickets-saas-application"></a>L'applicazione SaaS Wingtip Tickets
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

La stessa applicazione SaaS *Wingtip Tickets* viene implementata in ciascuno dei tre campioni. L'app è un semplice elenco di eventi e app SaaS per ticket destinate a piccole sedi, teatri, clubs e così via. Ogni sede è un tenant dell'app e ha i propri dati: dettagli della sede, elenchi di eventi, clienti, ordini di ticket e così via.  L'app, insieme agli script di gestione e alle esercitazioni, illustra uno scenario SaaS end-to-end. Sono inclusi i tenant di provisioning, il monitoraggio e la gestione delle prestazioni, la gestione dello schema e il reporting e analisi su più tenant.

## <a name="three-saas-application-and-tenancy-patterns"></a>Tre modelli di applicazione SaaS e tenancy

Sono disponibili tre versioni dell'app. Ognuna esplora un diverso modello di tenancy del database nel database SQL di Azure.  Il primo usa un'applicazione autonoma per ogni tenant con il proprio database. Il secondo usa un'app multi-tenant con un database per ogni tenant. Il terzo esempio usa un'app multi-tenant con database multi-tenant partizionati.

![Tre modelli di tenant][image-three-tenancy-patterns]

 Ogni esempio include il codice applicazione, gli script di gestione ed esercitazioni che esplorano una gamma di modelli di progettazione e gestione.  Ogni esempio si distribuisce in meno di cinque minuti.  Tutti e tre possono essere distribuiti fianco a fianco in modo da poter vedere le differenze di gestione e progettazione.

## <a name="standalone-application-per-tenant-pattern"></a>Modello di applicazione autonoma per ogni tenant

Il modello di app autonoma per ogni tenant usa un'applicazione a tenant singolo con un database per ogni tenant. L'app di ciascun tenant, incluso il relativo database, viene distribuita in un gruppo di risorse di Azure separato. Il gruppo di risorse può essere distribuito nella sottoscrizione del provider di servizi o in quella del tenant ed essere gestito dal provider per conto del tenant. Il modello di applicazione autonoma per ogni tenant fornisce il massimo isolamento dei tenant, ma è in genere il più costoso perché non esiste alcuna possibilità di condividere le risorse tra più tenant.  Questo modello è particolarmente adatto ad applicazioni che potrebbero essere più complesse e che vengono distribuite in un numero inferiore di tenant.  Con le distribuzioni autonome, l'app può essere più facilmente personalizzata per ogni tenant rispetto ad altri modelli.  

Vedere le [esercitazioni][docs-tutorials-for-wingtip-sa] e il codice su GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Modello con un database per ogni tenant

Il modello con un database per ogni tenant è utile per i provider di servizi che si occupano di isolamento dei tenant e vogliono eseguire un servizio centralizzato che consente un utilizzo efficiente delle risorse condivise. Viene creato un database per ogni locale di ritrovo, o tenant, e tutti i database sono gestiti centralmente. I database possono essere ospitati in pool elastici per offrire una gestione delle prestazioni economica e semplice, che sfrutta i modelli di carico di lavoro imprevisto dei tenant. Un database di catalogo contiene il mapping tra i tenant e i relativi database. Questo mapping viene gestito mediante le funzionalità di gestione mappe partizioni della [libreria client dei database elastici](elastic-database-client-library.md), il che offre una gestione efficiente delle connessioni all'applicazione.

Vedere le [esercitazioni][docs-tutorials-for-wingtip-dpt] e il codice in GitHub [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Modello con database multi-tenant partizionati

I database multi-tenant sono efficaci per i provider di servizi che cercano un costo per tenant inferiore e accettano un minore isolamento dei tenant. Questo modello consente di inserire un numero elevato di tenant in un unico database, riducendo il costo per tenant. Partizionando i tenant tra più database diventa possibile una scalabilità quasi infinita. Anche in questo caso un database di catalogo esegue il mapping dei tenant ai database.  

Questo modello consente anche un modello *ibrido* in cui è possibile ottimizzare i costi con più tenant in un database o ottimizzare l'isolamento con un singolo tenant nel proprio database. La scelta può essere effettuata tenant per tenant, quando viene eseguito il provisioning del tenant o in seguito, senza alcun impatto sull'applicazione.  Questo modello può essere usato in modo efficace quando è necessario trattare in modo diverso i gruppi di tenant. Ad esempio, i tenant a basso costo possono essere assegnati a database condivisi, mentre i tenant premium possono essere assegnati ai propri database. 

Vedere le [esercitazioni][docs-tutorials-for-wingtip-mt] e il codice su GitHub [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Passaggi successivi

#### <a name="conceptual-descriptions"></a>Descrizioni concettuali

- Una spiegazione più dettagliata dei modelli di tenancy dell'applicazione è disponibile nell'articolo [Criteri di tenancy di database delle applicazioni SaaS multi-tenant][saas-tenancy-app-design-patterns-md]

#### <a name="tutorials-and-code"></a>Esercitazioni e codice

- Applicazione autonoma per ogni tenant:
    - [Esercitazioni per l'app autonoma][docs-tutorials-for-wingtip-sa].
    - [Codice per l'app autonoma, in GitHub][github-code-for-wingtip-sa].

- Un database per ogni tenant:
    - [Esercitazioni per un database per ogni tenant][docs-tutorials-for-wingtip-dpt].
    - [Codice per un database per ogni tenant, in GitHub][github-code-for-wingtip-dpt].

- Multi-tenant partizionati:
    - [Esercitazioni per multi-tenant partizionati][docs-tutorials-for-wingtip-mt].
    - [Codice per multi-tenant partizionati, in GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Tre modelli di tenancy."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: ./saas-standaloneapp-get-started-deploy.md
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: ./saas-dbpertenant-wingtip-app-overview.md
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: ./saas-multitenantdb-get-started-deploy.md
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb