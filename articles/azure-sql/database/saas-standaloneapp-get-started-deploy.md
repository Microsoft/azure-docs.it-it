---
title: Esercitazione su SaaS a singolo tenant
description: Distribuire ed esplorare un'applicazione SaaS autonoma a singolo tenant che usa il database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/07/2018
ms.openlocfilehash: 896f4edb1e94c1eca06b046382727a5042375e3b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793280"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Distribuire ed esplorare un'applicazione autonoma a singolo tenant che usa il database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In questa esercitazione, si distribuisce ed esplora l'applicazione di esempio SaaS Wingtip Tickets sviluppata utilizzando il modello di applicazione autonoma o app per tenant.  L'applicazione è progettata per dimostrare le funzionalità del database SQL di Azure che semplificano l'abilitazione degli scenari SaaS multi-tenant.

L'applicazione autonoma o un modello di applicazione per tenant consente di distribuire un'istanza di applicazione per ogni tenant.  Ogni applicazione è configurata per un tenant specifico e distribuita in un gruppo di risorse di Azure separato. È possibile effettuare il provisioning di più istanze dell'applicazione per offrire una soluzione multi-tenant. Questo modello è più adatto per piccoli numeri di tenant, in cui l'isolamento è una priorità. Azure offre programmi partner che consentono di distribuire risorse nella sottoscrizione del tenant e di gestirle tramite un provider di servizi per conto del tenant. 

In questa esercitazione, si distribuiscono tre applicazioni autonome per tre tenant nella propria sottoscrizione di Azure.  Si ottiene l'accesso completo per l'esplorazione e l'uso dei singoli componenti dell'applicazione.

Il codice sorgente e gli script di gestione dell'applicazione sono disponibili nel repository [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) di GitHub. L'applicazione è stata creata con Visual Studio 2015 e non è possibile aprirla e compilarla in Visual Studio 2019 senza eseguire l'aggiornamento.


In questa esercitazione si apprenderà:

> [!div class="checklist"]
> * Come distribuire l'applicazione SaaS autonoma Wingtip Tickets
> * Dove ottenere gli script di gestione e il codice sorgente dell'applicazione
> * Informazioni su server e database che costituiscono l'app

Verranno rese disponibili esercitazioni aggiuntive che consentiranno di esplorare diversi scenari di gestione basati su questo modello di applicazione.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Distribuire l'applicazione SaaS autonoma Wingtip Tickets

Distribuire l'app per i tre tenant specificati:

1. Fare clic su ogni pulsante **Distribuisci in Azure** blu per aprire il modello di distribuzione nel [portale di Azure](https://portal.azure.com). Ogni modello richiede due valori di parametro, un nome per un nuovo gruppo di risorse e un nome utente che distingue questa distribuzione da altre distribuzioni dell'app. Il passaggio seguente fornisce dettagli per l'impostazione di questi valori.

   **Contoso Concert Hall**   
   [![Immagine che mostra il pulsante "Distribuisci in Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-contoso)

   **Dogwood Dojo**   
   [![Immagine che mostra il pulsante "Distribuisci in Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-dogwood)

   **Fabrikam Jazz Club**   
   [![Immagine che mostra il pulsante "Distribuisci in Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-fabrikam)
 
2. Immettere i valori dei parametri necessari per ogni distribuzione.

    > [!IMPORTANT]
    > Alcune impostazioni di autenticazione e per i firewall server sono intenzionalmente non protette a scopo dimostrativo. **Creare un nuovo gruppo di risorse** per ogni distribuzione di applicazione.  Non usare un gruppo di risorse esistente. Non utilizzare l'applicazione o le risorse che crea per la produzione. Eliminare tutti i gruppi di risorse quando non è più necessario usare le applicazioni, per interrompere la fatturazione correlata.

    Nei nomi delle risorse è preferibile usare solo lettere minuscole, numeri e trattini.
    * Per **Gruppo di risorse** selezionare Crea nuovo e quindi specificare un valore in minuscolo in Nome. **wingtip-sa-\<venueName\>-\<user\>** è il modello consigliato.  Per \<venueName\>, sostituire il nome del locale senza spazi. Per \<user\>, sostituire il valore dell'utente indicato qui sotto.  Utilizzando questo modello, i possibili nomi dei gruppi di risorse sono *wingtip-sa-contosoconcerthall-af1* , *wingtip-sa-dogwooddojo-af1* , *wingtip-sa-fabrikamjazzclub-af1* .
    * Selezionare un **percorso** nell'elenco a discesa.

    * Per **User** è consigliabile un valore breve, ad esempio le iniziali seguite da una cifra, ad esempio *af1* .


3. **Distribuire l'applicazione** .

    * Fare clic per accettare i termini e le condizioni.
    * Fare clic su **Acquista** .

4. Monitorare lo stato delle tre distribuzioni facendo clic su **Notifiche** (l'icona a forma di campanella a destra della casella di ricerca). La distribuzione dell'app richiede circa cinque minuti.


## <a name="run-the-applications"></a>Eseguire le applicazioni

L'app illustra le sedi che ospitano gli eventi.  Le sedi sono i tenant dell'applicazione. Ogni sede è associata a un sito Web personalizzato in cui sono elencati i relativi eventi e dove è possibile la vendita dei biglietti. I tipi di sede includono sale concerto, jazz club e club sportivi. Nell'esempio, il tipo di sede determina la fotografia di sfondo visualizzata nel sito web della struttura.   Nel modello di applicazione autonoma, ogni sede ottiene un'istanza di applicazione separata associata a un proprio database SQL di Azure autonomo.

1. Aprire la pagina degli eventi per ognuno dei tre tenant in schede separate del browser:

   - http://events.contosoconcerthall.&lt;utente&gt;.trafficmanager.net
   - http://events.dogwooddojo.&lt;utente&gt;.trafficmanager.net
   - http://events.fabrikamjazzclub.&lt;utente&gt;.trafficmanager.net

     In ogni URL sostituire &lt;user&gt; con il valore relativo all'utente della distribuzione specifica.

   ![Events](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Per controllare la distribuzione delle richieste in ingresso, l'app usa [*Gestione traffico di Microsoft Azure*](../../traffic-manager/traffic-manager-overview.md). Ogni istanza dell'app specifica del tenant include il nome del tenant come parte del nome di dominio nell'URL. Gli URL di tutti i tenant includono valori di **User** specifici. Gli URL hanno il formato seguente:
- http://events.&lt;nomeluogo&gt;.&lt;utente&gt;.trafficmanager.net

La **posizione** del database di ogni tenant è inclusa nelle impostazioni dell'app distribuita corrispondente.

In un ambiente di produzione si crea in genere un record CNAME DNS per [*configurare un dominio Internet aziendale in modo che faccia riferimento*](../../traffic-manager/traffic-manager-point-internet-domain.md) all'URL del profilo di Gestione traffico.


## <a name="explore-the-servers-and-tenant-databases"></a>Esplorare i server e i database tenant

È possibile esaminare alcune risorse distribuite:

1. Nel [portale di Azure](https://portal.azure.com) passare all'elenco dei gruppi di risorse.
2. Dovrebbero essere visualizzati i tre gruppi di risorse del tenant.
3. Aprire il gruppo di risorse **wingtip-sa-fabrikam-&lt;user&gt;** , che contiene le risorse per la distribuzione di Fabrikam Jazz Club.  Il server **fabrikamjazzclub-&lt;user&gt;** contiene il database **fabrikamjazzclub** .

Ogni database del tenant è un database *autonomo* con 50 DTU.

## <a name="additional-resources"></a>Risorse aggiuntive

<!--
* Additional [tutorials that build on the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](elastic-pool-overview.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](./elastic-jobs-overview.md)
-->

- Per informazioni sulle applicazioni SaaS multi-tenant, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Eliminare gruppi di risorse per interrompere la fatturazione ##

Quando si finisce di utilizzare l'esempio, è importante eliminare tutti i gruppi di risorse creati per interrompere la fatturazione correlata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]
> * Come distribuire l'applicazione SaaS autonoma Wingtip Tickets
> * Informazioni su server e database che costituiscono l'app
> * Come eliminare le risorse di esempio per interrompere la fatturazione correlata

Successivamente, eseguire l'esercitazione su [provisioning e catalogazione](saas-standaloneapp-provision-and-catalog.md) in cui viene illustrato l'uso di un catalogo di tenant che consente una serie di scenari tra tenant, ad esempio l'analisi dei tenant e la gestione degli schemi.
