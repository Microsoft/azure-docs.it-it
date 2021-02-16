---
title: Soluzione Monitoraggio prestazioni rete in Azure | Microsoft Docs
description: Monitoraggio prestazioni rete di Azure consente di monitorare le prestazioni delle reti, quasi in tempo reale, per rilevare e trovare i colli di bottiglia delle prestazioni di rete.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 4ae0a85c8608ff96777bc6b952554f1ccd917306
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530606"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Soluzione Monitoraggio prestazioni rete in Azure

![Simbolo di Monitoraggio prestazioni rete](./media/network-performance-monitor/npm-symbol.png)

> [!IMPORTANT]
> A partire dall'1 luglio 2021, non sarà possibile aggiungere nuovi test in un'area di lavoro esistente o abilitare una nuova area di lavoro in Monitoraggio prestazioni rete. È possibile continuare a usare i test creati prima dell'1 luglio 2021. Per ridurre al minimo le rotture dei servizi nei carichi di lavoro correnti, [migrare i test da monitoraggio prestazioni rete al nuovo monitoraggio della connessione](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) in Azure Network Watcher prima del 29 febbraio 2024.

Monitoraggio prestazioni rete è una soluzione di monitoraggio reti ibrida basata sul cloud che consente di monitorare le prestazioni della rete tra vari punti dell'infrastruttura di rete. Consente anche di monitorare la connettività di rete al servizio e agli endpoint dell'applicazione e di monitorare le prestazioni di Microsoft Azure ExpressRoute. 

Monitoraggio prestazioni rete rileva i problemi di rete come i buchi neri del traffico, gli errori di routing e i problemi che i metodi di monitoraggio delle reti tradizionali non sono in grado di rilevare. La soluzione genera avvisi e invia notifiche quando viene superata una soglia per un collegamento di rete. Garantisce anche una tempestiva individuazione dei problemi legati alle prestazioni di rete e localizza l'origine del problema in un dispositivo o segmento di rete specifico. 

Monitoraggio prestazioni rete offre tre ampie funzionalità: 

* [Monitoraggio delle prestazioni](network-performance-monitor-performance-monitor.md): è possibile monitorare la connettività di rete tra distribuzioni cloud e percorsi locali, più data center, succursali e applicazioni o microservizi multilivello mission-critical. Con Monitoraggio delle prestazioni, è possibile rilevare i problemi di rete prima che gli utenti se ne lamentino.

* [Monitoraggio connettività servizio](network-performance-monitor-service-connectivity.md): è possibile monitorare la connettività dagli utenti ai servizi più importanti, determinare il tipo di infrastruttura nel percorso e i punti della rete in cui si verificano i colli di bottiglia. Consente di essere avvisati delle interruzioni di servizio prima degli utenti e fornisce la posizione esatta dei problemi lungo il percorso di rete. 

    Questa funzionalità consente di eseguire test basati su HTTP, HTTPS, TCP e ICMP per monitorare la disponibilità e il tempo di risposta del servizio quasi in tempo reale o su base cronologica. È anche possibile monitorare il contributo della rete a una perdita di pacchetti e alla latenza. Con una mappa della topologia di rete, è possibile isolare i rallentamenti della rete. È possibile identificare le aree problematiche lungo il percorso di rete dal nodo al servizio, con dati di latenza per ogni hop. Con i test predefiniti, è possibile monitorare la connettività di rete per Microsoft 365 e Dynamics CRM senza alcuna preconfigurazione. Questa funzionalità consente di monitorare la connettività di rete a qualsiasi endpoint idoneo per TCP, ad esempio siti Web, applicazioni SaaS e PaaS e database SQL.

* [Monitoraggio di ExpressRoute](network-performance-monitor-expressroute.md): consente di monitorare la connettività end-to-end e le prestazioni tra succursali e Azure, tramite Azure ExpressRoute.  

Ulteriori informazioni sulle diverse funzionalità supportate da [Monitoraggio prestazioni rete](../../networking/network-monitoring-overview.md) sono disponibili online.
 
## <a name="supported-regions"></a>Aree supportate
NPM consente di monitorare la connettività tra reti e applicazioni in qualsiasi parte del mondo, da un'area di lavoro ospitata in una delle seguenti aree:
* Europa settentrionale
* Europa occidentale
* Svizzera settentrionale
* Francia centrale
* Sudafrica settentrionale
* Canada centrale
* Stati Uniti occidentali
* Stati Uniti centro-occidentali
* Stati Uniti centro-settentrionali
* Stati Uniti centro-meridionali
* Stati Uniti centrali
* Stati Uniti orientali
* Stati Uniti orientali 2
* Stati Uniti occidentali 2
* Giappone orientale
* Asia sud-orientale
* Australia sud-orientale
* Australia centrale
* Australia orientale
* Regno Unito meridionale
* Asia orientale
* Corea centrale
* India centrale
* Virginia e US Gov
* Arizona del governo degli Stati Uniti
* Cina orientale 2


L'elenco delle aree supportate per Monitoraggio di ExpressRoute è disponibile nella [documentazione](../../expressroute/how-to-npm.md?utm_swu=8117).


## <a name="set-up-and-configure"></a>Installare e configurare

> [!NOTE]
> È anche possibile che l'agente di Log Analytics sia denominato Microsoft Monitoring Agent (MMA) o agente di OMS per Linux.

### <a name="install-and-configure-agents"></a>Installare e configurare agenti 

Usare i processi di base per installare gli agenti in [connettere i computer Windows a monitoraggio di Azure](../platform/agent-windows.md), [connettere i computer Linux a monitoraggio di Azure (anteprima)](../../virtual-machines/extensions/oms-linux.md) e [connettersi Operations Manager a monitoraggio di Azure](../platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Dove installare gli agenti 

* **Performance Monitor:** installare gli agenti di Log Analytics in almeno un nodo connesso a ogni subnet da cui si vuole monitorare la connettività di rete ad altre subnet.

    Per monitorare un collegamento di rete, installare agenti in entrambi gli endpoint del collegamento. Se non si conosce la topologia della rete, installare gli agenti nei server con carichi di lavoro critici tra cui si desidera monitorare le prestazioni di rete. Ad esempio, se si desidera monitorare la connessione di rete tra un server Web e un server che esegue SQL, installare un agente in entrambi i server. Gli agenti monitorano la connettività di rete (collegamenti) tra host, non gli host stessi. 

* **Monitoraggio connettività servizio:** installare un agente di Log Analytics in ogni nodo da cui si vuole monitorare la connettività di rete all'endpoint di servizio. Un esempio è se si vuole monitorare la connettività di rete per Microsoft 365 dai siti di Office con etichetta O1, O2 e O3. Installare l'agente di Log Analytics in almeno un nodo ciascuno in O1, O2 e O3. 

* **Monitoraggio di ExpressRoute**: installare almeno un agente di Log Analytics nella rete virtuale di Azure. Installare anche almeno un agente nella subnet locale, connessa attraverso il peering privato di ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Configurare gli agenti di Log Analytics per il monitoraggio 

Monitoraggio prestazioni rete usa le transazioni sintetiche per monitorare le prestazioni di rete tra gli agenti di origine e di destinazione. È possibile scegliere tra TCP e ICMP come protocollo di monitoraggio per le funzionalità Monitoraggio prestazioni e Monitoraggio connettività servizio. Per Monitoraggio di ExpressRoute è disponibile solo il protocollo di monitoraggio TCP. Assicurarsi che il firewall consenta la comunicazione tra gli agenti di Log Analytics usati per il monitoraggio sul protocollo scelto. 

* **Protocollo TCP:** se si è scelto TCP come protocollo di monitoraggio, aprire la porta del firewall sugli agenti usati per le funzionalità Monitoraggio prestazioni rete e Monitoraggio di ExpressRoute per assicurarsi che gli agenti possano connettersi tra loro. Per i computer Windows, per aprire la porta, eseguire lo script [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell senza parametri in una finestra di PowerShell con privilegi amministrativi.
Per i computer Linux, l'uso di portNumbers deve essere modificato manualmente. 
* Passare a percorso:/var/opt/Microsoft/omsagent/npm_state. 
* Apri file: npmdregistry
* Modificare il valore per il numero di porta ```“PortNumber:<port of your choice>”```

 Si noti che i numeri di porta usati devono essere uguali in tutti gli agenti usati in un'area di lavoro. 

Lo script crea le chiavi del Registro di sistema richieste dalla soluzione. Crea anche le regole di Windows Firewall per consentire agli agenti di creare connessioni TCP tra loro. Le chiavi del Registro di sistema create dallo script specificano se registrare i log di debug e il percorso del file dei log. Lo script definisce anche la porta TCP dell'agente usata per la comunicazione. I valori per queste chiavi vengono impostati automaticamente dallo script. Non modificare manualmente queste chiavi. La porta aperta per impostazione predefinita è 8084. È possibile usare una porta personalizzata fornendo il parametro portNumber allo script. Usare la stessa porta in tutti i computer in cui viene eseguito lo script. 

   >[!NOTE]
   > Lo script configura Windows Firewall solo in locale. Se si dispone di un firewall di rete, assicurarsi che consenta il traffico destinato alla porta TCP usata da Monitoraggio prestazioni rete.

   >[!NOTE]
   > Non è necessario eseguire lo script di [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell per il monitoraggio della connettività del servizio.

    

* **Protocollo ICMP**: se si è scelto ICMP come protocollo di monitoraggio, abilitare le seguenti regole del firewall per usare ICMP in modo affidabile:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Configurare la soluzione 

1. Aggiungere la soluzione Monitoraggio prestazioni rete nell'area di lavoro dall'[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/solarwinds.solarwinds-orion-network-performance-monitor?tab=Overview). È anche possibile usare la procedura descritta in [aggiungere soluzioni di monitoraggio di Azure dalla raccolta di soluzioni](./solutions.md). 
2. Aprire l'area di lavoro Log Analytics e selezionare il riquadro **Panoramica**. 
3. Selezionare il riquadro **monitoraggio prestazioni rete** con la *soluzione Message richiede una configurazione aggiuntiva*.

   ![Riquadro Monitoraggio delle prestazioni di rete](media/network-performance-monitor/npm-config.png)

4. Nella pagina **Installazione** viene visualizzata l'opzione per installare gli agenti di Log Analytics e configurare gli agenti per il monitoraggio nella visualizzazione **Impostazioni comuni**. Come descritto in precedenza, se sono stati installati e configurati agenti di Log Analytics, selezionare la visualizzazione **Installazione** per configurare la funzionalità che si desidera usare. 

   **Monitoraggio prestazioni**: scegliere il protocollo da usare per le transazioni sintetiche nella regola di Monitoraggio prestazioni **predefinita** e selezionare **Salva e continua**. La selezione di questo protocollo viene mantenuta solo per la regola predefinita generata dal sistema. È necessario scegliere il protocollo ogni volta che si crea in modo esplicito una regola di Monitoraggio prestazioni. È sempre possibile passare alle impostazioni della regola **predefinita** nella scheda **Monitoraggio prestazioni** (visualizzata dopo avere completato la configurazione del giorno 0) e modificare il protocollo in seguito. Se non si è interessati alla funzionalità Monitoraggio prestazioni, è possibile disabilitare la regola predefinita dalle impostazioni della regola **predefinita** nella scheda **Monitoraggio prestazioni**.

   ![Visualizzazione Monitoraggio prestazioni](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitoraggio della connettività del servizio**: la funzionalità fornisce test preconfigurati predefiniti per monitorare la connettività di rete a Microsoft 365 e Dynamics 365 dagli agenti. Scegliere i servizi Microsoft 365 e Dynamics 365 che si desidera monitorare selezionando le caselle di controllo accanto a essi. Per scegliere gli agenti da cui eseguire il monitoraggio, selezionare **Aggiungi agenti**. Se non si desidera utilizzare questa funzionalità o si desidera configurarla in un secondo momento, non scegliere nulla e selezionare **Salva e continua**.

   ![Visualizzazione Monitoraggio connettività del servizio](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **Monitoraggio di ExpressRoute**: selezionare **Individua adesso** per individuare tutti i peering privati di ExpressRoute connessi alle reti virtuali nella sottoscrizione di Azure collegata a questa area di lavoro Log Analytics. 

   ![Visualizzazione Monitoraggio di ExpressRoute](media/network-performance-monitor/npm-express-route.png)

   Al termine dell'individuazione, i circuiti e i peering individuati vengono elencati in una tabella. 

   ![Pagina di configurazione di Monitoraggio prestazioni rete](media/network-performance-monitor/npm-private-peerings.png)
    
Il monitoraggio di questi circuiti e peering è inizialmente in stato disabilitato. Selezionare tutte le risorse che si desidera monitorare e configurare il monitoraggio per tali risorse dalla visualizzazione dettagli a destra. Selezionare **Save (Salva** ) per salvare la configurazione. Per altre informazioni, vedere l'articolo "Configurare Monitoraggio di ExpressRoute". 

Dopo aver completato la configurazione, il popolamento dei dati richiede da 30 minuti a un'ora. Mentre la soluzione aggrega i dati dalla rete, nel riquadro **Panoramica** di Monitoraggio prestazioni rete viene visualizzato il messaggio *La soluzione richiede una configurazione aggiuntiva*. Al termine della raccolta e dell'indicizzazione dei dati, il riquadro **Panoramica** cambia e mostra un riepilogo dell'integrità della rete. È quindi possibile modificare il monitoraggio dei nodi in cui sono installati gli agenti di Log Analytics, nonché delle subnet individuate dall'ambiente.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Modificare le impostazioni di monitoraggio per le subnet e i nodi 

Tutte le subnet con almeno un agente installato sono elencate nella scheda **subnet** della pagina di configurazione. 


Per abilitare o disabilitare il monitoraggio di subnet specifiche:

1. Selezionare o deselezionare la casella di controllo accanto all'**ID subnet**. Assicurarsi quindi che la casella **Usa per il monitoraggio** sia selezionata o deselezionata a seconda delle esigenze. È possibile selezionare o deselezionare più subnet. Se disabilitate, le subnet non vengono monitorate e gli agenti vengono aggiornati in modo da interrompere l'invio di ping ad altri agenti. 
2. Scegliere i nodi che si desidera monitorare in una subnet specifica. Selezionare la subnet dall'elenco e spostare i nodi necessari tra gli elenchi che contengono nodi non monitorati e monitorati. È possibile aggiungere una descrizione personalizzata alla subnet.
3. Selezionare **Save (Salva** ) per salvare la configurazione. 

#### <a name="choose-nodes-to-monitor"></a>Selezionare i nodi da monitorare

Tutti i nodi su cui è installato un agente sono elencati nella scheda **Nodi**. 

1. Selezionare o deselezionare i nodi per cui si desidera abilitare o interrompere il monitoraggio. 
2. Selezionare o deselezionare **Usa per il monitoraggio**, secondo le esigenze. 
3. Selezionare **Salva**. 


Configurare le funzionalità desiderate:

- [Monitoraggio delle prestazioni](network-performance-monitor-performance-monitor.md#configuration)
- [Monitoraggio connettività servizio](network-performance-monitor-performance-monitor.md#configuration)
- [Monitoraggio di ExpressRoute](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati
Per raccogliere informazioni su perdita e latenza, Monitoraggio prestazioni rete usa pacchetti di handshaking TCP SYN-SYNACK-ACK se si sceglie TCP come protocollo. Monitoraggio prestazioni rete usa ICMP ECHO ICMP ECHO REPLY se si sceglie ICMP come protocollo. Per ottenere informazioni sulla topologia si usa anche il tracciamento route.

Nella tabella seguente vengono illustrati i metodi di raccolta dei dati e altri dettagli sulla modalità di raccolta dei dati per il monitoraggio delle prestazioni di rete.

| Piattaforma | Agente diretto | Agente System Center Operations Manager | Archiviazione di Azure | È necessario Operations Manager? | Dati dell'agente Operations Manager inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Handshake TCP/messaggi ICMP ECHO ogni 5 secondi, dati inviati ogni 3 minuti |
 

 
La soluzione usa le transazioni sintetiche per valutare l'integrità della rete. Gli agenti di Log Analytics installati in vari punti della rete si scambiano pacchetti TCP o ICMP Echo. Il fatto che gli agenti usino pacchetti TCP o ICMP Echo dipende dal protocollo selezionato per il monitoraggio. Durante il processo, gli agenti vengono informati del tempo di round trip e di eventuali perdite di pacchetti. Periodicamente, ogni agente esegue anche un comando di tracciamento delle route agli altri agenti per trovare tutte le varie route nella rete che devono essere verificate. Usando questi dati, gli agenti possono dedurre i valori relativi a latenza di rete e perdita di pacchetti. I test vengono ripetuti ogni cinque secondi. I dati vengono aggregati per circa tre minuti dagli agenti prima che vengano caricati nell'area di lavoro Log Analytics di monitoraggio di Azure.



>[!NOTE]
> Anche se gli agenti comunicano spesso tra loro, non generano un traffico di rete intenso durante l'esecuzione dei test. Gli agenti si basano solo sui pacchetti di handshaking TCP SYN-SYNACK-ACK per determinare la perdita e la latenza. Non vengono scambiati pacchetti di dati. Durante questo processo, gli agenti comunicano tra loro solo quando necessario. La topologia di comunicazione dell'agente è ottimizzata per ridurre il traffico di rete.

## <a name="use-the-solution"></a>Usare la soluzione 

### <a name="network-performance-monitor-overview-tile"></a>Riquadro Panoramica di Monitoraggio prestazioni rete 

Dopo aver abilitato la soluzione Monitoraggio prestazioni rete, il riquadro della soluzione nella pagina **Panoramica** offre una rapida panoramica dell'integrità della rete. 

 ![Riquadro Panoramica di Monitoraggio prestazioni rete](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Dashboard di monitoraggio delle prestazioni di rete 

* **Principali eventi di integrità della rete**: questa pagina fornisce un elenco dei più recenti avvisi ed eventi di integrità nel sistema e specifica da quanto tempo è attivo l'evento. Un evento o avviso di integrità viene generato quando il valore della metrica scelta (perdita, latenza, tempo di risposta o utilizzo della larghezza di banda) per la regola di monitoraggio supera la soglia. 

* **Monitoraggio di ExpressRoute**: questa pagina fornisce riepiloghi sull'integrità per le diverse connessioni di peering ExpressRoute monitorate dalla soluzione. Il riquadro **Topologia** mostra il numero di percorsi di rete attraverso i circuiti di ExpressRoute monitorati nella rete. Selezionare questo riquadro per andare alla visualizzazione **Topologia**.

* **Monitoraggio connettività servizio**: questa pagina fornisce riepiloghi sull'integrità per i diversi test creati. Il riquadro **Topologia** mostra il numero di endpoint monitorati. Selezionare questo riquadro per andare alla visualizzazione **Topologia**.

* **Monitoraggio prestazioni**: questa pagina fornisce riepiloghi sull'integrità per i collegamenti **Rete** e **Subnet** monitorati dalla soluzione. Il riquadro **Topologia** mostra il numero di percorsi di rete monitorati nella rete. Selezionare questo riquadro per andare alla visualizzazione **Topologia**. 

* **Query comuni**: questa pagina contiene una serie di query di ricerca che recuperano direttamente i dati di monitoraggio di rete non elaborati. È possibile usare queste query come punto di partenza per creare proprie query per report personalizzati. 

   ![Dashboard di monitoraggio delle prestazioni di rete](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Drill-down in profondità 

È possibile scegliere diversi collegamenti nel dashboard della soluzione per eseguire il drill-down più approfondito in qualsiasi area di interesse. Ad esempio, quando viene visualizzato un avviso o un collegamento di rete non integro sul dashboard, selezionarlo per indagare più a fondo. Una pagina elenca tutti i collegamenti a subnet per un collegamento di rete specifico. È possibile visualizzare la perdita, la latenza e lo stato di integrità di ciascun collegamento a subnet. È possibile trovare rapidamente i collegamenti a subnet che causano problemi. Selezionare **Visualizza collegamenti nodo** per visualizzare tutti i collegamenti del nodo per il collegamento di subnet non integro. È possibile vedere i singoli collegamenti tra nodi e trovare i collegamenti del nodo non integri. 

Selezionare **Visualizza topologia** per visualizzare la topologia hop-by-hop delle route tra i nodi di origine e di destinazione. Le route non integre vengono visualizzate in rosso. È possibile visualizzare la latenza generata da ogni hop per consentire di associare rapidamente il problema a una parte specifica della rete.

 

### <a name="network-state-recorder-control"></a>Controllo dell'utilità di registrazione dello stato di rete

Ogni visualizzazione mostra uno snapshot dell'integrità della rete in un determinato momento. Per impostazione predefinita, viene visualizzato lo stato più recente. La barra nella parte superiore della pagina mostra il momento per cui viene visualizzato lo stato. Per visualizzare uno snapshot dell'integrità della rete in un momento precedente, selezionare **Azioni**. È anche possibile abilitare o disabilitare l'aggiornamento automatico per qualsiasi pagina mentre si visualizza lo stato più recente. 

 ![Utilità di registrazione dello stato di rete](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Grafici delle tendenze 

A ogni livello del drill-down, è possibile vedere la tendenza della metrica applicabile. Può trattarsi di perdita, latenza, tempo di risposta o utilizzo della larghezza di banda. Per modificare l'intervallo di tempo per la tendenza, usare il controllo del tempo nella parte superiore del grafico. 

I grafici delle tendenze mostrano una prospettiva cronologica delle prestazioni di una metrica delle prestazioni. Alcuni problemi di rete sono di natura temporanea e sarebbero difficili da rilevare solo esaminando lo stato corrente della rete. I problemi possono emergere rapidamente e sparire prima che qualcuno li noti per poi riapparire in un secondo momento. Questi problemi temporanei possono rivelarsi particolarmente complessi per gli amministratori dell'applicazione. I problemi spesso emergono come inspiegabili aumenti nel tempo di risposta dell'applicazione, anche quando tutti i componenti dell'applicazione sembrano funzionare regolarmente. 

È possibile rilevare con facilità questi tipi di problemi facendo riferimento al grafico delle tendenze. Il problema viene visualizzato come un improvviso picco di latenza di rete o una perdita di pacchetti. Per analizzare il problema, utilizzare il controllo di registrazione dello stato di rete per visualizzare lo snapshot della rete e la topologia del momento in cui il problema si è verificato.

 
![Grafici delle tendenze](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mappa della topologia 

Monitoraggio prestazioni rete mostra la topologia hop per hop delle route tra l'endpoint di origine e quello di destinazione in una mappa della topologia interattiva. Per visualizzare la mappa della topologia, selezionare il riquadro **Topologia** nel dashboard della soluzione. È anche possibile selezionare il collegamento **Visualizza topologia** nelle pagine di drill-down. 

La mappa della topologia visualizza la quantità di route presenti tra l'origine e la destinazione e i percorsi intrapresi dai pacchetti di dati. È visibile anche la latenza generata da ogni hop di rete. Tutti i percorsi per cui la latenza totale dei percorsi è superiore alla soglia (impostata nella regola di monitoraggio corrispondente) vengono visualizzati in rosso. 

Quando si seleziona un nodo o si passa il mouse sulla mappa della topologia, vengono visualizzate le proprietà del nodo, ad esempio FQDN e indirizzo IP. Selezionare un hop per visualizzare il relativo indirizzo IP. È possibile identificare l'hop di rete in cui si è verificato il problema osservando la latenza generata dall'hop. Per filtrare route specifiche, usare i filtri nel riquadro azioni comprimibile. Per semplificare le topologie di rete, nascondere gli hop intermedi usando il dispositivo di scorrimento nel riquadro azioni. È possibile ingrandire o ridurre la mappa della topologia usando la rotellina del mouse. 

La topologia mostrata sulla mappa è di livello 3 e non contiene connessioni e dispositivi di livello 2. 

 
![Mappa della topologia](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Query di log in Monitoraggio di Azure

Tutti i dati esposti graficamente attraverso il dashboard Monitoraggio prestazioni rete e le pagine di drill-down sono disponibili anche in modalità nativa nelle [query di log](../log-query/log-query-overview.md). È possibile eseguire l'analisi interattiva dei dati nel repository e correlare i dati da origini diverse. È anche possibile creare visualizzazioni e avvisi personalizzati ed esportare i dati in Excel, Power BI o un collegamento condivisibile. Nell'area **query comuni** del dashboard sono disponibili alcune query utili che è possibile utilizzare come punto di partenza per creare query e report personalizzati. 

## <a name="alerts"></a>Avvisi

Il Monitoraggio delle prestazioni di rete usa le funzionalità di avviso di [Monitoraggio di Azure](../platform/alerts-overview.md).

Ciò significa che tutte le notifiche vengono gestite mediante [gruppi di azioni](../platform/action-groups.md).  

Per gli utenti di NPM che creano un avviso tramite Log Analytics: 
1. Viene visualizzato un collegamento che consente di reindirizzare a portale di Azure. Fare clic per accedere al portale.
2. Fare clic sul riquadro della soluzione di Monitoraggio delle prestazioni di rete. 
3. Passare a Configura.  
4. Selezionare il test desiderato per creare un avviso e seguire i passaggi indicati di seguito.

Se si è un utente NPM che crea un avviso tramite portale di Azure:  
1. È possibile scegliere di immettere direttamente l'indirizzo di posta elettronica o creare avvisi tramite gruppi di azioni.
2. Se si sceglie di immettere l'indirizzo di posta elettronica direttamente, viene creato un gruppo di azione con il nome **NPM email ActionGroup** e l'ID di posta elettronica viene aggiunto al gruppo di azioni.
3. Se si sceglie di usare gruppi di azioni, è necessario selezionare un gruppo di azioni creato in precedenza. È possibile imparare a creare un gruppo di azioni [qui.](../platform/action-groups.md#create-an-action-group-by-using-the-azure-portal) 
4. Una volta creato correttamente l'avviso è possibile usare il collegamento Gestisci avvisi per gestire gli avvisi. 

Ogni volta che si crea un avviso, NPM crea una regola di avviso log basata su query in Monitoraggio di Azure. Questa query viene attivata ogni 5 minuti per impostazione predefinita. Monitoraggio di Azure non prevede addebiti per le prime 250 regole di avviso log create. Superato questo limite, ogni regola di avviso viene addebitata in base ai [prezzi per gli avvisi riportati nella pagina dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).
Le notifiche vengono addebitate separatamente in base ai [prezzi per le notifiche riportati nella pagina dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Prezzi

Le informazioni sui prezzi sono disponibili [online](network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Fornire il feedback 

* **UserVoice**: è possibile pubblicare le proprie idee sulle funzionalità di Monitoraggio prestazioni rete che si ritiene possano essere migliorate. Visitare la [pagina UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Unisciti alla coorte:** Siamo sempre interessati a far partecipare i nuovi clienti alla coorte. In questo contesto si avrà accesso in anteprima alle nuove funzionalità e si avrà l'opportunità di contribuire a migliorare Monitoraggio prestazioni rete. Se si è interessati a partecipare, compilare questo [sondaggio rapido](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Passaggi successivi 
Per altre informazioni, vedere [Monitoraggio prestazioni rete](network-performance-monitor-performance-monitor.md), [Monitoraggio connettività servizio](network-performance-monitor-performance-monitor.md) e [Monitoraggio di ExpressRoute](network-performance-monitor-expressroute.md). 
