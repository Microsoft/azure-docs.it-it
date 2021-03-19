---
title: Soluzione Analisi DNS in Monitoraggio di Azure | Microsoft Docs
description: Configurare e usare la soluzione Analisi DNS in Monitoraggio di Azure per raccogliere informazioni dettagliate nell'infrastruttura DNS relative a sicurezza, prestazioni e operazioni.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: f01a1204aefbcaabe0ddac254b24bc014c3d5a64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654527"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Raccogliere informazioni dettagliate sull'infrastruttura DNS con la soluzione DNS Analytics (anteprima)

![Simbolo di Analisi DNS](./media/dns-analytics/dns-analytics-symbol.png)

Questo articolo descrive come configurare e usare la soluzione Analisi DNS di Azure in Monitoraggio di Azure per raccogliere informazioni dettagliate in DNS relative a sicurezza, prestazioni e operazioni.

DNS Analytics consente di:

- Identificare i client che tentano di risolvere nomi di dominio dannosi.
- Identificare i record di risorse non aggiornati.
- Identificare i nomi di dominio sottoposti frequentemente a query e i client DNS loquaci.
- Visualizzare il carico di richieste nei server DNS.
- Visualizzare gli errori di registrazione di DNS dinamici.

La soluzione raccoglie, analizza e mette in relazione i log di controllo e analisi DNS di Windows e altri dati correlati dei server DNS.

## <a name="connected-sources"></a>Origini connesse

La tabella seguente descrive le origini connesse che sono supportate da questa soluzione:

| **Origine connessa** | **Supporto** | **Descrizione** |
| --- | --- | --- |
| [Agenti di Windows](../agents/agent-windows.md) | Sì | La soluzione raccoglie le informazioni DNS dagli agenti Windows. |
| [Agenti Linux](../vm/quick-collect-linux-computer.md) | No | La soluzione non raccoglie le informazioni DNS dagli agenti Linux diretti. |
| [Gruppo di gestione di System Center Operations Manager](../agents/om-agents.md) | Sì | La soluzione raccoglie le informazioni DNS dagli agenti di un gruppo di gestione di Operations Manager connesso. Non è necessaria una connessione diretta dall'agente Operations Manager a Monitoraggio di Azure. I dati vengono inoltrati dal gruppo di gestione all'area di lavoro Log Analytics. |
| [Account di archiviazione di Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) | No | La soluzione non usa le risorse di archiviazione di Azure. |

### <a name="data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati

La soluzione raccoglie i dati relativi all'inventario e agli eventi DNS dai server DNS in cui è installato un agente Log Analytics. Questi dati vengono quindi caricati in Monitoraggio di Azure e visualizzati nel dashboard della soluzione. I dati relativi all'inventario, come il numero di server, zone e record di risorse DNS, vengono raccolti eseguendo i cmdlet di PowerShell per DNS. I dati vengono aggiornati ogni due giorni. I dati relativi agli eventi vengono raccolti quasi in tempo reale dai [log di analisi e di controllo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) offerti dalle funzionalità avanzate di registrazione e diagnostica DNS di Windows Server 2012 R2.

## <a name="configuration"></a>Configurazione

Per configurare la soluzione, usare le informazioni seguenti:

- È necessario un agente [Windows](../agents/agent-windows.md) o [Operations Manager](../agents/om-agents.md) in ogni server DNS che si vuole monitorare.
- È possibile aggiungere la soluzione Analisi DNS nell'area di lavoro Log Analytics da [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace). È anche possibile seguire la procedura descritta in [Aggiungere soluzioni di Monitoraggio di Azure dalla raccolta di soluzioni](solutions.md).

La soluzione avvia la raccolta dati senza che siano necessarie operazioni di configurazione aggiuntive. È tuttavia possibile usare la configurazione seguente per personalizzare la raccolta.

### <a name="configure-the-solution"></a>Configurare la soluzione

Dall'area di lavoro Log Analytics della portale di Azure selezionare **Riepilogo area di lavoro** , quindi fare clic sul riquadro **analisi DNS** . Nel dashboard della soluzione fare clic su **Configurazione** per aprire la pagina Configurazione di DNS Analytics. È possibile apportare due tipi di modifiche di configurazione.

- **Nomi di dominio Allowlisted**. La soluzione non elabora tutte le query di ricerca, Mantiene un oggetto Allow dei suffissi del nome di dominio. Le query di ricerca che si risolvono nei nomi di dominio che corrispondono ai suffissi del nome di dominio in questo oggetto allow non vengono elaborate dalla soluzione. La mancata elaborazione dei nomi di dominio allowlisted consente di ottimizzare i dati inviati a monitoraggio di Azure. L'oggetto allow predefinito include i nomi di dominio pubblici più diffusi, ad esempio www.google.com e www.facebook.com. L'intero elenco predefinito può essere visualizzato tramite lo scorrimento.

  È possibile modificare l'elenco per aggiungere qualsiasi suffisso di nome di dominio per cui non si vogliono visualizzare informazioni dettagliate relative alla ricerca. È anche possibile rimuovere qualsiasi suffisso di nome di dominio per cui non si vogliono visualizzare informazioni dettagliate relative alla ricerca.

- **Soglia client loquaci**. I client DNS che superano la soglia relativa al numero di richieste di ricerca vengono evidenziati nel pannello **Client DNS**. La soglia predefinita è 1000. È possibile modificare la soglia.

    ![Nomi di dominio Allowlisted](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Management Pack

Se per la connessione all'area di lavoro Log Analytics si usa Microsoft Monitoring Agent, viene installato il Management Pack seguente:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)

Se il gruppo di gestione di Operations Manager è connesso all'area di lavoro Log Analytics, quando si aggiunge questa soluzione in Operations Manager vengono installati i Management Pack seguenti. Per i Management Pack seguenti non è necessaria alcuna configurazione o manutenzione:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

Per maggiori informazioni sulla modalità di aggiornamento dei Management Pack, vedere [Connettere Operations Manager a Log Analytics](../agents/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>Usare la soluzione DNS Analytics

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


Il riquadro DNS include il numero di server DNS in cui vengono raccolti i dati. e il numero di richieste effettuato dai client per risolvere domini dannosi nelle ultime 24 ore. Quando si fa clic sul riquadro, viene aperto il dashboard della soluzione.

![Riquadro DNS Analytics](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>Dashboard della soluzione

Il dashboard della soluzione visualizza informazioni di riepilogo relative alle varie funzionalità e include anche collegamenti alla visualizzazione dettagliata per l'analisi e la diagnosi per scopi legali. Per impostazione predefinita, vengono visualizzati i dati per gli ultimi sette giorni. È possibile modificare l'intervallo di tempo con il **controllo di selezione di data e ora**, come nell'immagine seguente:

![Controllo di selezione di data e ora](./media/dns-analytics/dns-time.png)

Il dashboard visualizza i pannelli descritti di seguito.

**Sicurezza DNS**. Segnala i client DNS che tentano di comunicare con domini dannosi. Usando i feed di intelligence per le minacce di Microsoft, DNS Analytics può rilevare gli indirizzi IP client che tentano di accedere ai domini dannosi. In molti casi, i dispositivi infetti da malware "stabiliscono la connessione" con il "centro di comando e controllo" del dominio dannoso risolvendo il nome di dominio del malware.

![Pannello Sicurezza DNS](./media/dns-analytics/dns-security-blade.png)

Facendo clic su un indirizzo IP client nell'elenco si apre Ricerca log con i dettagli della ricerca della rispettiva query. Nell'esempio seguente Analisi DNS rilevato che la comunicazione è stata eseguita con un [IRCBot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621):

![Ricerca log: risultati con IRCbot](./media/dns-analytics/ircbot.png)

Le informazioni consentono di identificare quanto segue:

- IP client che ha avviato la comunicazione.
- Nome di dominio che viene risolto nell'indirizzo IP dannoso.
- Indirizzi IP in cui viene risolto il nome di dominio.
- Indirizzo IP dannoso.
- Gravità del problema.
- Motivo per Blocklisting l'indirizzo IP dannoso.
- Data e ora di rilevamento.

**Domini sottoposti a query**. Contiene i nomi di dominio su cui i client DNS eseguono più frequentemente query nell'ambiente. È possibile visualizzare l'elenco di tutti i nomi di dominio sottoposti a query ed eseguire il drill-down nei dettagli delle richieste di ricerca per un nome di dominio specifico in Ricerca log.

![Pannello Domini sottoposti a query](./media/dns-analytics/domains-queried-blade.png)

**Client DNS**. Segnala i client che *violano la soglia* relativa al numero di query nel periodo di tempo selezionato. È possibile visualizzare l'elenco di tutti i client DNS e i dettagli delle query che hanno eseguito in Ricerca log.

![Pannello Client DNS](./media/dns-analytics/dns-clients-blade.png)

**Registrazioni di DNS dinamici**. Segnala gli errori di registrazione dei nomi. Vengono evidenziati tutti gli errori di registrazione relativi a [record di risorse](https://en.wikipedia.org/wiki/List_of_DNS_record_types) indirizzo (tipo A e AAAA), con gli indirizzi IP client che hanno effettuato le richieste di registrazione. È quindi possibile usare queste informazioni per trovare la causa radice dell'errore di registrazione seguendo questa procedura:

1. Trovare la zona rilevante per il nome che il client sta tentando di aggiornare.

1. Usare la soluzione per controllare le informazioni di inventario di tale zona.

1. Verificare che per la zona sia abilitato l'aggiornamento dinamico.

1. Controllare se la zona è configurata o meno per l'aggiornamento dinamico sicuro.

    ![Pannello Registrazioni di DNS dinamici](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Richieste di registrazione del nome**. Il riquadro superiore visualizza la tendenza del numero di richieste di aggiornamento dinamico di DNS riuscite e non riuscite. Il riquadro inferiore contiene l'elenco dei primi 10 client che hanno inviato richieste di aggiornamento di DNS non riuscite ai server DNS, ordinato in base al numero di errori.

![Pannello Richieste di registrazione del nome](./media/dns-analytics/name-reg-req-blade.png)

**Query DDI Analytics di esempio**. Contiene un elenco delle query di ricerca più comuni che recuperano direttamente dati di analisi non elaborati.


![Query di esempio](./media/dns-analytics/queries.png)

È possibile usare queste query come punto di partenza per creare le proprie query per l'elaborazione di report personalizzati. Le query si collegano alla pagina di Ricerca log di DNS Analytics in cui vengono visualizzati i risultati:

- **Elenco di server DNS**. Visualizza un elenco di tutti i server DNS con i nomi di dominio completo, i nomi di dominio, i nomi foresta e gli IP server associati.
- **Elenco di zone DNS**. Visualizza un elenco di tutte le zone DNS con il nome della zona, lo stato di aggiornamento dinamico, i server dei nomi e lo stato della firma DNSSEC associati.
- **Record di risorse inutilizzati**. Visualizza un elenco di tutti i record di risorse non usati o non aggiornati. L'elenco contiene il nome e il tipo dei record di risorse, il server DNS associato, la data e l'ora di generazione dei record e il nome della zona. È possibile usare questo elenco per identificare i record di risorse DNS non più in uso e quindi intervenire sulla base di tali informazioni per rimuovere tali voci dai server DNS.
- **Carico query server DNS**. Visualizza informazioni che consentono di ottenere una prospettiva del carico DNS sui server DNS. Queste informazioni consentono di pianificare la capacità per i server. È possibile passare alla scheda **Metrica** per ottenere una visualizzazione grafica, che consente di comprendere la distribuzione del carico DNS nei server DNS e mostra le tendenze nella frequenza di query DNS per ogni server.

    ![Ricerca log: risultati relativi alle query nei server DNS](./media/dns-analytics/dns-servers-query-load.png)

- **Carico query zone DNS**. Visualizza le statistiche al secondo delle query di tutte le zone DNS nei server DNS gestiti dalla soluzione. Fare clic sulla scheda **Metrica** per passare dalla visualizzazione di record dettagliati a una visualizzazione grafica dei risultati.
- **Eventi di configurazione**. Visualizza tutti gli eventi di modifica configurazione DNS e i messaggi associati. È quindi possibile filtrare questi eventi in base a data e ora dell'evento, ID evento, server DNS o categoria di attività. I dati consentono di controllare le modifiche apportate a specifici server DNS in date e ore specifiche.
- **Log analitico DNS**. Visualizza tutti gli eventi analitici in tutti i server DNS gestiti dalla soluzione. È quindi possibile filtrare questi eventi in base a data e ora dell'evento, ID evento, server DNS, IP client che ha eseguito la query di ricerca e categoria di attività del tipo di query. Gli eventi di analisi dei server DNS consentono il rilevamento delle attività nel server DNS. Viene registrato un evento di analisi ogni volta che il server invia o riceve informazioni DNS.

### <a name="search-by-using-dns-analytics-log-search"></a>Eseguire una ricerca usando la Ricerca log di DNS Analytics

Nella pagina Ricerca log è possibile creare una query. È possibile filtrare i risultati usando i controlli facet. È anche possibile creare query avanzate per trasformare, filtrare e creare report sui risultati. Per iniziare, usare le query seguenti:

1. Nella **casella della query di ricerca** digitare `DnsEvents` per visualizzare tutti gli eventi DNS generati dai server DNS gestiti dalla soluzione. Nei risultati sono elencati i dati di log per tutti gli eventi relativi a query di ricerca, registrazioni dinamiche e modifiche di configurazione.

    ![Ricerca log: eventi DNS](./media/dns-analytics/log-search-dnsevents.png)  

    a. Per visualizzare i dati di log per query di ricerca, selezionare **LookUpQuery** come filtro **sottotipo** nel controllo facet sul lato sinistro. Viene visualizzata una tabella contenente gli eventi relativi a query di ricerca per il periodo di tempo selezionato.

    b. Per visualizzare i dati di log per registrazioni dinamiche, selezionare **DynamicRegistration** come filtro **sottotipo** nel controllo facet sul lato sinistro. Viene visualizzata una tabella contenente tutti gli eventi relativi a registrazioni dinamiche per il periodo di tempo selezionato.

    c. Per visualizzare i dati di log per modifiche di configurazione, selezionare **ConfigurationChange** come filtro **sottotipo** nel controllo facet sul lato sinistro. Viene visualizzata una tabella contenente tutti gli eventi relativi a modifiche di configurazione per il periodo di tempo selezionato.

1. Nella **casella della query di ricerca** digitare `DnsInventory` per visualizzare tutti i dati relativi all'inventario DNS per i server DNS gestiti dalla soluzione. Nei risultati vengono elencati i dati di log relativi ai server DNS, alle zone DNS e ai record di risorse.

    ![Ricerca log: inventario DNS](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Risoluzione dei problemi

Passaggi comuni per la risoluzione dei problemi:

1. Dati delle ricerche DNS mancanti: per risolvere il problema, provare a reimpostare la configurazione o a caricare la pagina di configurazione una sola volta nel portale. Per la reimpostazione, è sufficiente modificare un'impostazione in un altro valore, quindi riportarla nel valore originale e salvare la configurazione.

## <a name="suggestions"></a>Suggerimenti

Per fornire commenti e suggerimenti, visitare la [pagina log Analytics UserVoice](https://aka.ms/dnsanalyticsuservoice) per pubblicare idee per il funzionamento di analisi DNS funzionalità. 

## <a name="next-steps"></a>Passaggi successivi

[Eseguire query di log](../logs/log-query-overview.md) per visualizzare record di log dettagliati per DNS.
