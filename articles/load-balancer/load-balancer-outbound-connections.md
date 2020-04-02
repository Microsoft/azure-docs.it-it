---
title: Connessioni in uscita in Azure
titleSuffix: Azure Load Balancer
description: In questo articolo viene spiegato come Azure consente alle macchine virtuali di comunicare con i servizi Internet pubblici.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 55fa14f367dbf24e951fde8e9075a34499a510b1
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547084"
---
# <a name="outbound-connections-in-azure"></a>Connessioni in uscita in Azure

Azure offre connettività in uscita per le distribuzioni dei clienti tramite vari meccanismi diversi. Questo articolo descrive che cosa sono gli scenari, quando si applicano, come funzionano e come gestirli.

>[!NOTE] 
>Questo articolo descrive solo le distribuzioni di Resource Manager. Vedere [Connessioni in uscita (versione classica)](load-balancer-outbound-connections-classic.md) per tutti gli scenari di distribuzione classica in Azure.

Una distribuzione in Azure può comunicare con endpoint all'esterno di Azure nello spazio indirizzi IP pubblici. Quando un'istanza avvia un flusso in uscita verso una destinazione nello spazio indirizzi IP pubblici, Azure esegue in modo dinamico il mapping dell'indirizzo IP privato a un indirizzo IP pubblico. Dopo aver creato questo mapping, il traffico di ritorno per questo flusso con origine in uscita può raggiungere l'indirizzo IP privato da cui ha avuto origine il flusso.

Azure usa SNAT (Source Network Address Translation) per eseguire questa funzione. Quando più indirizzi IP privati si mascherano dietro un singolo indirizzo IP pubblico, Azure usa [PAT (Port Address Translation)](#pat) per il mascheramento degli indirizzi IP privati. Per PAT vengono usate porte temporanee [preallocate](#preallocatedports) in base alle dimensioni del pool.

Sono presenti più [scenari in uscita](#scenarios). È possibile combinare questi scenari in base alle esigenze. Esaminarli con attenzione per comprenderne le funzionalità, i vincoli e gli schemi, in riferimento a modelli di distribuzione e scenari di applicazione specifici. Vedere le linee guida per la [gestione di questi scenari](#snatexhaust).

>[!IMPORTANT] 
>Load Balancer Standard e l'indirizzo IP pubblico standard introducono nuove funzionalità e comportamenti differenti per la connettività in uscita.  Non corrispondono agli SKU di base.  Per disporre di connettività in uscita quando si utilizzano SKU standard, è necessario definire questa opzione sia per gli indirizzi IP pubblici standard sia per un'istanza di Load Balancer Standard pubblica.  Ciò include la creazione di connettività in uscita quando si utilizza un servizio di bilanciamento del carico Standard interno.  È consigliabile usare sempre le regole in uscita in un'istanza di Load Balancer Standard pubblica.  Lo [scenario 3](#defaultsnat) non è disponibile con gli SKU standard.  Ciò significa che quando viene usata un'istanza di Load Balancer Standard interna, è necessario provvedere a creare una connettività in uscita per le macchine virtuali nel pool di back-end se è richiesta la connettività in uscita.  Nel contesto di connettività in uscita, una singola macchina virtuale autonoma, tutte le macchine virtuali in un set di disponibilità e tutte le istanze in un set di scalabilità di macchine virtuali si comportano come un gruppo. Ciò significa che, se una singola macchina virtuale in un set di disponibilità è associata a uno SKU standard, tutte le istanze della macchina virtuale all'interno di questo set di disponibilità si comportano allo stesso modo, come se fossero associate allo SKU standard, anche se una singola istanza non è direttamente associata a tale SKU. Questo comportamento si osserva anche nel caso di una macchina virtuale autonoma con più schede di interfaccia di rete collegate a un servizio di bilanciamento del carico. Se una scheda di interfaccia di rete viene aggiunta come standalone, avrà lo stesso comportamento. Rivedere attentamente l'intero documento per comprendere i concetti generali, esaminare [Load Balancer Standard](load-balancer-standard-overview.md) per le differenze tra gli SKU e rivedere le [regole in uscita](load-balancer-outbound-rules-overview.md).  L'uso di regole in uscita offre all'utente un controllo dettagliato su tutti gli aspetti della connettività in uscita.

## <a name="scenario-overview"></a><a name="scenarios"></a>Panoramica dello scenario

Quando si usa [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), vengono definiti in modo esplicito il servizio Azure Load Balancer e le risorse correlate.  Attualmente, Azure offre tre diversi metodi per ottenere la connettività in uscita per le risorse di Azure Resource Manager. 

| SKU | Scenario | Metodo | Protocolli IP | Descrizione |
| --- | --- | --- | --- | --- |
| Standard, di base | [1. VM con un indirizzo IP pubblico a livello di istanza (con o senza load Balancer)1. VM with an Instance Level Public IP address (with or without Load Balancer)](#ilpip) | SNAT, il mascheramento delle porte non viene usato | TCP, UDP, ICMP, ESP | Azure usa l'indirizzo IP pubblico assegnato alla configurazione IP della scheda di interfaccia di rete dell'istanza. L'istanza ha tutte le porte temporanee disponibili. Quando si usa Load Balancer Standard, [le regole in uscita](load-balancer-outbound-rules-overview.md) non sono supportate se alla macchina virtuale viene assegnato un indirizzo IP pubblico. |
| Standard, di base | [2. Servizio di bilanciamento del carico pubblico associato a una macchina virtuale (nessun indirizzo IP pubblico nell'istanza)2. Public Load Balancer associated with a VM (no Public IP address on the instance)](#lb) | SNAT con mascheramento delle porte (PAT) tramite i front-end di Load Balancer | TCP, UDP |Azure condivide l'indirizzo IP pubblico dei front-end di Load Balancer pubblici con più IP privati. Azure usa le porte temporanee dei front-end per PAT. È consigliabile usare [le regole in uscita](load-balancer-outbound-rules-overview.md) per definire in modo esplicito la connettività in uscita. |
| Nessuna o di base | [3. VM autonoma (nessun servizio di bilanciamento del carico, nessun indirizzo IP pubblico)](#defaultsnat) | SNAT con mascheramento delle porte (PAT) | TCP, UDP | Azure designa automaticamente un indirizzo IP pubblico per SNAT, condivide questo indirizzo IP pubblico con più indirizzi IP privati del set di disponibilità e usa le porte temporanee di questo indirizzo IP pubblico. Questo è uno scenario di fallback per gli scenari precedenti. Non è consigliato se sono necessari visibilità e controllo. |

Se non si vuole che una macchina virtuale comunichi con gli endpoint all'esterno di Azure nello spazio indirizzi IP pubblici, è possibile usare i gruppi di sicurezza di rete per bloccare l'accesso in base alle specifiche esigenze. La sezione [Impedire la connettività in uscita](#preventoutbound) illustra i gruppi di sicurezza di rete più in dettaglio. La progettazione, l'implementazione e la gestione di una rete virtuale senza accesso in uscita non rientrano nell'ambito di questo articolo.

### <a name="scenario-1-vm-with-public-ip-address"></a><a name="ilpip"></a>Scenario 1: VM con indirizzo IP pubblicoScenario 1: VM with Public IP address

In questo scenario, la macchina virtuale dispone di un IP pubblico assegnato. Per quanto riguarda le connessioni in uscita, non è importante se la macchina virtuale abbia un carico bilanciato o meno. Questo scenario ha la precedenza rispetto agli altri. Quando viene usato un indirizzo IP pubblico, la macchina virtuale usa l'indirizzo IP pubblico per tutti i flussi in uscita.  

Un indirizzo IP pubblico assegnato a una macchina virtuale è una relazione 1:1, anziché 1:molti, e viene implementato come NAT 1:1 senza stato.  Il mascheramento delle porte (PAT) non viene usato e tutte le porte temporanee sono disponibili per l'uso per la macchina virtuale.

Se l'applicazione avvia molti flussi in uscita e si verifica l'esaurimento delle porte SNAT, è consigliabile assegnare un [indirizzo IP pubblico per ridurre i vincoli SNAT.](#assignilpip) Leggere per intero [Gestione dell'esaurimento SNAT](#snatexhaust).

### <a name="scenario-2-load-balanced-vm-without-a-public-ip-address"></a><a name="lb"></a>Scenario 2: VM con carico bilanciato senza un indirizzo IP pubblicoScenario 2: Load-balanced VM without a Public IP address

In questo scenario la macchina virtuale fa parte di un pool di back-end di Load Balancer pubblico. Alla macchina virtuale non è assegnato un indirizzo IP pubblico. La risorsa di bilanciamento del carico deve essere configurata con una regola di bilanciamento del carico per creare un collegamento fra il front-end dell'IP pubblico e il pool back-end.

Se non si completa la configurazione della regola, il comportamento è come descritto nello scenario per la [macchina virtuale autonoma senza IP pubblico](#defaultsnat). Per la regola non è richiesta la presenza di un listener di lavoro nel pool di back-end perché il probe di integrità abbia esito positivo.

Quando la macchina virtuale con carico bilanciato crea un flusso in uscita, Azure converte l'indirizzo IP di origine privata del flusso in uscita nell'indirizzo IP pubblico del front-end pubblico di Load Balancer. Azure usa SNAT per eseguire questa funzione. Azure usa anche [PAT](#pat) per mascherare più indirizzi IP privati dietro un indirizzo IP pubblico. 

Per distinguere i singoli flussi provenienti dalla macchina virtuale vengono usate le porte temporanee del front-end dell'indirizzo IP pubblico di Load Balancer. SNAT usa dinamicamente le [porte temporanee preallocate](#preallocatedports) quando vengono creati i flussi in uscita. In questo contesto le porte temporanee usate per SNAT sono dette porte SNAT.

Le porte SNAT vengono preallocate come descritto nella sezione [Informazioni su SNAT e PAT.](#snat) Sono una risorsa limitata che può esaurirsi. L'importante è capire come vengono [usate](#pat). Per informazioni su come progettare questo consumo e come attenuarlo in base alle necessità, vedere [Gestione dell'esaurimento SNAT](#snatexhaust).

Se a [Load Balancer Basic vengono associati più indirizzi IP pubblici](load-balancer-multivip-overview.md), uno qualsiasi di questi indirizzi IP pubblici è un candidato per i flussi in uscita e ne viene selezionato uno in modo casuale.  

Per monitorare l'integrità delle connessioni in uscita con Load Balancer Basic, è possibile usare i log di Monitoraggio di [Azure per Load Balancer](load-balancer-monitor-log.md) e i log eventi di [avviso](load-balancer-monitor-log.md#alert-event-log) per monitorare i messaggi di esaurimento delle porte SNAT.

### <a name="scenario-3-standalone-vm-without-a-public-ip-address"></a><a name="defaultsnat"></a>Scenario 3: VM autonoma senza un indirizzo IP pubblicoScenario 3: Standalone VM without a Public IP address

In questo scenario, la macchina virtuale non fa parte di un pool di bilanciamento del carico pubblico (e non fa parte di un pool di bilanciamento del carico Standard interno) e non dispone di un indirizzo IP pubblico assegnato. Quando la macchina virtuale crea un flusso in uscita, Azure converte l'indirizzo IP di origine privata del flusso in uscita in un indirizzo IP di origine pubblica. L'indirizzo IP pubblico usato per questo flusso in uscita non è configurabile e non interferisce con il limite della risorsa IP pubblico della sottoscrizione. Questo indirizzo IP pubblico non appartiene all'utente e non può essere riservato. Se si ridistribuisce la macchina virtuale, il set di disponibilità o il set di scalabilità della macchina virtuale, questo indirizzo IP pubblico verrà rilasciato e verrà richiesto un nuovo indirizzo IP pubblico. Non usare questo scenario per creare un elenco di indirizzi IP consentiti. Adottare uno degli altri due scenari in cui si dichiara in modo esplicito lo scenario in uscita e l'indirizzo IP pubblico da usare per la connettività in uscita.

>[!IMPORTANT] 
>Questo scenario si applica anche quando è collegata __solo__ un'istanza di Load Balancer Basic interna. Lo scenario 3 __non è disponibile__ quando un'istanza di Load Balancer Standard interna è collegata a una macchina virtuale.  È necessario creare in modo esplicito lo [scenario 1](#ilpip) o lo [scenario 2](#lb) oltre a usare un'istanza di Load Balancer Standard interna.

Azure usa SNAT con il mascheramento delle porte ([PAT](#pat)) per eseguire questa funzione. Questo scenario è simile allo [scenario 2](#lb), ad eccezione del fatto che non esiste alcun controllo sull'indirizzo IP utilizzato. Si tratta di uno scenario di fallback per quando non esistono gli scenari 1 e 2. Questo scenario non è consigliato se si vuole avere il controllo dell'indirizzo in uscita. Se le connessioni in uscita sono una parte essenziale dell'applicazione, è consigliabile scegliere un altro scenario.

Le porte SNAT vengono preallocate come descritto nella sezione [Informazioni su SNAT e PAT](#snat).  Il numero di macchine virtuali che condividono un set di disponibilità determina il livello di preallocazione applicato.  Una macchina virtuale autonoma senza un set di disponibilità costituisce un pool di 1 ai fini della determinazione della preallocazione (1024 porte SNAT). Le porte SNAT sono una risorsa limitata che può esaurirsi. L'importante è capire come vengono [usate](#pat). Per informazioni su come progettare questo consumo e come attenuarlo in base alle necessità, vedere [Gestione dell'esaurimento SNAT](#snatexhaust).

### <a name="multiple-combined-scenarios"></a><a name="combinations"></a>Più scenari combinati

È possibile combinare gli scenari descritti nelle sezioni precedenti per ottenere un risultato particolare. Quando esistono più scenari, si applica un ordine di precedenza: lo [scenario 1](#ilpip) ha la precedenza sullo [scenario 2](#lb) e [3](#defaultsnat). Lo [scenario 2](#lb) sostituisce lo [scenario 3](#defaultsnat).

Un esempio è una distribuzione di Azure Resource Manager in cui l'applicazione dipende fortemente da connessioni in uscita a un numero limitato di destinazioni, ma riceve anche flussi in ingresso tramite un front-end di Load Balancer. In questo caso, è possibile combinare gli scenari 1 e 2 per una maggiore efficacia. Per informazioni su altri modelli, vedere [Gestione dell'esaurimento SNAT](#snatexhaust).

### <a name="multiple-frontends-for-outbound-flows"></a><a name="multife"></a> Più front-end per i flussi in uscita

#### <a name="standard-load-balancer"></a>Load Balancer Standard

Load Balancer Standard usa tutti i candidati per i flussi in uscita contemporaneamente quando sono presenti [più front-end IP (pubblici)](load-balancer-multivip-overview.md). Ogni front-end moltiplica il numero di porte SNAT preallocate disponibili se è abilitata una regola di bilanciamento del carico per le connessioni in uscita.

È possibile scegliere di non usare un indirizzo IP front-end per le connessioni in uscita con una nuova opzione della regola di bilanciamento del carico:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

In genere, il valore predefinito dell'opzione `disableOutboundSnat` è _false_. Questo significa che questa regola programma le connessioni SNAT in uscita per le macchine virtuali associate nel pool back-end della regola di bilanciamento del carico. Il valore di `disableOutboundSnat` può essere impostato su _true_, per impedire che Load Balancer usi l'indirizzo IP front-end associato per le connessioni in uscita per le macchine virtuali, nel pool back-end di questa regola di bilanciamento del carico.  È tuttavia possibile designare anche un indirizzo IP specifico per i flussi in uscita, come descritto in [Più scenari combinati](#combinations).

#### <a name="load-balancer-basic"></a>Load Balancer Basic

Load Balancer Basic sceglie un unico front-end da usare per i flussi in uscita quando esistono [più front-end IP (pubblici)](load-balancer-multivip-overview.md) candidati per i flussi in uscita. Questa selezione non è configurabile ed è consigliabile considerare casuale l'algoritmo di selezione. È possibile designare un indirizzo IP specifico per i flussi in uscita come descritto in [Più scenari combinati](#combinations).

### <a name="availability-zones"></a><a name="az"></a>Aree di disponibilità

Quando si usa [Load Balancer Standard con le zone di disponibilità](load-balancer-standard-availability-zones.md), i front-end con ridondanza della zona possono fornire connessioni SNAT in uscita con ridondanza della zona e la programmazione SNAT non risente degli errori a livello di zona.  Quando vengono usati front-end di zona, la durata delle connessioni SNAT in uscita è legata alla zona a cui appartengono.

## <a name="understanding-snat-and-pat"></a><a name="snat"></a>Informazioni su SNAT e PAT

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Mascheramento delle porte SNAT (PAT)

Quando una risorsa pubblica di Load Balancer è associata alle istanze VM, ogni origine di connessione in uscita viene riscritta. L'origine viene riscritta dallo spazio dell'indirizzo IP privato della rete virtuale all'indirizzo IP pubblico front-end del bilanciamento del carico. Nello spazio degli indirizzi IP pubblici, le 5 tuple del flusso (indirizzo IP di origine, porta di origine, protocollo di trasporto IP, indirizzo IP di destinazione, porta di destinazione) devono essere univoche.  Il mascheramento delle porte SNAT è utilizzabile con i protocolli TCP o UDP IP.

Per ottenere ciò vengono usate porte temporanee (porte SNAT) dopo la riscrittura dell'indirizzo IP di origine privato, perché più flussi hanno origine da un singolo indirizzo IP pubblico. L'algoritmo SNAT per il mascheramento delle porte alloca in modo diverso le porte SNAT per il protocollo UDP rispetto al protocollo TCP.

#### <a name="tcp-snat-ports"></a><a name="tcp"></a>Porte SNAT TCP

Viene usata una porta SNAT per flusso verso un singolo indirizzo IP e porta di destinazione. Nel caso di più flussi TCP verso la stessa combinazione di indirizzo IP, porta e protocollo di destinazione, ogni flusso TCP utilizza una singola porta SNAT. Si garantisce così che i flussi siano univoci quando hanno origine dallo stesso indirizzo IP pubblico e quando sono destinati allo stesso indirizzo IP, alla stessa porta e allo stesso protocollo di destinazione. 

Più flussi destinati ognuno a un indirizzo IP, una porta e un protocollo diversi condividono una sola porta SNAT. L'indirizzo IP, la porta e il protocollo di destinazione rendono univoci i flussi senza la necessità di porte di origine aggiuntive per distinguere i flussi nello spazio indirizzi IP pubblici.

#### <a name="udp-snat-ports"></a><a name="udp"></a> Porte SNAT UDP

Le porte SNAT UDP vengono gestite da un algoritmo diverso rispetto alle porte SNAT TCP.  Load Balancer usa un algoritmo noto come "port-restricted cone NAT" per UDP.  Per ogni flusso, viene usata una porta SNAT indipendentemente dall'indirizzo IP e porta di destinazione.

#### <a name="snat-port-reuse"></a>Riutilizzo della porta SNAT

Una volta rilasciata una porta, la porta è disponibile per il riutilizzo in base alle esigenze.  È possibile considerare le porte SNAT come una sequenza dal più basso al più alto disponibile per un determinato scenario e la prima porta SNAT disponibile viene utilizzata per le nuove connessioni. 
 
#### <a name="exhaustion"></a>Esaurimento

Quando si esauriscono le risorse di porte SNAT, i flussi in uscita vengono completati dopo che i flussi esistenti rilasciano le porte SNAT. Il servizio Load Balancer recupera le porte SNAT alla chiusura del flusso e usa un [timeout per inattività di 4 minuti](#idletimeout) per il recupero delle porte SNAT dai flussi inattivi.

Le porte SNAT UDP in genere si esauriscono molto più rapidamente rispetto alle porte SNAT TCP a causa della differenza tra gli algoritmi utilizzati. È necessario progettare e scalare il test tenendo presente questa differenza.

Per informazioni sui modelli per la mitigazione delle condizioni che portano comunemente all'esaurimento delle porte SNAT, vedere la sezione [Gestione di SNAT](#snatexhaust).

### <a name="ephemeral-port-preallocation-for-port-masquerading-snat-pat"></a><a name="preallocatedports"></a>Preallocazione di porte temporanee per il mascheramento delle porte SNAT (PAT)

Azure usa un algoritmo per determinare il numero di porte SNAT preallocate disponibili in base alla dimensione del pool back-end quando si usa il mascheramento delle porte SNAT ([PAT](#pat)). Le porte SNAT sono porte temporanee disponibili per un particolare indirizzo di origine IP pubblico.

Lo stesso numero di porte SNAT viene allocato rispettivamente per UDP e TCP e utilizzato indipendentemente per ogni protocollo di trasporto IP.  Tuttavia, l'uso delle porte SNAT è diverso a seconda che il flusso sia UDP o TCP.

>[!IMPORTANT]
>La programmazione SNAT per SKU Standard è basata sul protocollo di trasporto IP e viene derivata dalla regola di bilanciamento del carico.  Se esiste una sola regola di bilanciamento del carico TCP, SNAT è disponibile solo per TCP. Se è presente solo una regola di bilanciamento del carico TCP ed è necessaria una connessione SNAT in uscita per UDP, creare una regola di bilanciamento del carico UDP dallo stesso front-end allo stesso pool back-end.  In questo modo, verrà attivata la programmazione SNAT per UDP.  Non è necessario un probe di integrità o una regola di lavoro.  La programmazione SNAT per SKU Basic programma sempre SNAT per il protocollo di trasporto IP, indipendentemente dal protocollo di trasporto specificato nella regole di bilanciamento del carico.

Azure prealloca le porte SNAT alla configurazione IP della scheda di interfaccia di rete di ogni macchina virtuale. Quando una configurazione IP viene aggiunta al pool, le porte SNAT vengono preallocate per questa configurazione IP in base alla dimensione del pool back-end. Quando vengono creati i flussi in uscita, [PAT](#pat) usa (fino al limite preallocato) e rilascia in modo dinamico tali porte alla chiusura del flusso o quando si verifica un [timeout per inattività](#idletimeout).

Nella tabella seguente sono riportate le preallocazioni delle porte SNAT per i livelli di dimensioni del pool back-end:

| Dimensioni del pool (istanze VM) | Porte SNAT preallocate per configurazione IP|
| --- | --- |
| 1-50 | 1.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1.000 | 32 |

>[!NOTE]
> Quando si usa Load Balancer Standard con [più front-end](load-balancer-multivip-overview.md), ogni indirizzo IP front-end moltiplica il numero di porte SNAT disponibili nella tabella precedente. Ad esempio, un pool back-end di 50 macchine virtuali con 2 regole di bilanciamento del carico, ognuna con indirizzo IP front-end distinti, userà 2048 porte SNAT (2 x 1024) per ogni configurazione IP. Vedere i dettagli per [più front-end](#multife).

Ricordare che il numero di porte SNAT disponibili non viene convertito direttamente in un numero di flussi. È possibile riutilizzare una singola porta SNAT per più destinazioni univoche. Le porte vengono usate solo se è necessario per rendere univoci i flussi. Per indicazioni sulla progettazione e la mitigazione, vedere la sezione su [come gestire questa risorsa soggetta a esaurimento](#snatexhaust) e la sezione che descrive [PAT](#pat).

La modifica delle dimensioni del pool back-end potrebbe influire su alcuni dei flussi stabiliti. Se le dimensioni del pool back-end aumentano e si esegue la transizione al livello successivo, metà delle porte SNAT preallocate vengono recuperate durante la transizione al livello di pool back-end più grande successivo. I flussi associati a una porta SNAT recuperata raggiungeranno il timeout e dovranno essere ristabiliti. Se viene tentato un nuovo flusso, il flusso avrà esito positivo immediatamente, purché siano disponibili porte preallocate.

Se le dimensioni del pool back-end diminuiscono e si ha una transizione a un livello più basso, il numero di porte SNAT disponibili aumenta. In questo caso, non vi sono effetti per le porte SNAT allocate esistenti e i rispettivi flussi.

Le allocazioni delle porte SNAT sono specifiche del protocollo di trasporto IP (i protocolli TCP e UDP vengono gestiti separatamente) e vengono rilasciate nelle condizioni seguenti:

### <a name="tcp-snat-port-release"></a>Rilascio di porte SNAT TCP

- Se uno dei server/client invia FINACK, la porta SNAT verrà rilasciata dopo 240 secondi.
- In presenza di una richiesta RST, la porta SNAT viene rilasciata dopo 15 secondi.
- Se è stato raggiunto il timeout di inattività, la porta viene rilasciata.

### <a name="udp-snat-port-release"></a>Rilascio di porte SNAT UDP

- Se è stato raggiunto il timeout di inattività, la porta viene rilasciata.

## <a name="problem-solving"></a><a name="problemsolving"></a> Risoluzione dei problemi 

Questa sezione è stata pensata per attenuare i problemi relativi all'esaurimento SNAT e che possono verificarsi con le connessioni in uscita in Azure.

### <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> Gestione dell'esaurimento delle porte SNAT (PAT)
[Le porte effimere](#preallocatedports) utilizzate per [PAT](#pat) sono una risorsa esauribile, come descritto in [VM autonoma senza un indirizzo IP pubblico](#defaultsnat) e una macchina virtuale con carico [bilanciato senza un indirizzo IP pubblico](#lb).

Sono disponibili diverse opzioni di mitigazione generali, se si è certi che verranno avviate numerose connessioni in uscita TCP o UDP allo stesso indirizzo IP e alla stessa porta di destinazione e se si riscontrano connessioni in uscita con errore o se il supporto tecnico ha comunicato il possibile esaurimento delle porte SNAT ([porte temporanee](#preallocatedports) preallocate usate da [PAT](#pat)). Esaminare queste opzioni e scegliere quella disponibile e ottimale per lo scenario specifico. È possibile che una o più opzioni risultino utili per gestire questo scenario.

In caso di problemi a comprendere il comportamento delle connessioni in uscita, è possibile usare le statistiche dello stack IP (netstat) o può essere utile osservare i comportamenti di connessione tramite le acquisizioni di pacchetti. È possibile eseguire queste acquisizioni di pacchetti nel sistema operativo guest dell'istanza o usare [Network Watcher per l'acquisizione dei pacchetti](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Modificare l'applicazione per riutilizzare le connessioni 
È possibile ridurre la richiesta di porte temporanee usate per SNAT usando di nuovo le connessioni nell'applicazione. Ciò vale soprattutto per i protocolli HTTP/1.1, in cui il riutilizzo della connessione è automatico. Altri protocolli che usano HTTP come trasporto, ad esempio REST, possono trarne vantaggio a loro volta. 

Il riutilizzo è sempre meglio delle singole connessioni TCP atomiche per ogni richiesta. Il riutilizzo garantisce transazioni TCP molto efficienti e con prestazioni più elevate.

#### <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>Modificare l'applicazione per usare pool di connessioni
Nella connessione è possibile usare una uno schema di pool della connessione, in cui le richieste sono distribuite internamente in un set predefinito di connessioni, ognuna delle quali viene riutilizzata quando possibile. Questo schema limita il numero di porte temporanee in uso e crea un ambiente più prevedibile. Può anche migliorare la velocità effettiva delle richieste, consentendo più operazioni simultanee, quando una connessione si blocca sulla risposta di un'operazione.  

Il pool delle connessioni potrebbe essere già presente nell'ambito usato per sviluppare l'applicazione o le impostazioni di configurazione dell'applicazione. È possibile combinare il pool di connessioni con il riutilizzo delle connessioni. Le richieste multiple impiegano quindi un numero prevedibile fisso di porte per la stessa porta e lo stesso indirizzo IP di destinazione. Le richieste traggono anche vantaggio dall'uso efficiente delle transazioni TCP che riducono la latenza e l'utilizzo delle risorse. Anche le transazioni UDP possono offrire vantaggi, perché gestire il numero di flussi UDP può a sua volta evitare condizioni di esaurimento e consente di gestire l'utilizzo delle porte SNAT.

#### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Modificare l'applicazione per usare una logica di ripetizione meno aggressiva
Quando vengono esaurite le [porte temporanee preallocate](#preallocatedports) usate per [PAT](#pat) o si verificano errori nell'applicazione, i tentativi di ripetizione aggressivi o di forza bruta senza logica di backoff e decadimento causano il verificarsi o la persistenza dell'esaurimento. È possibile ridurre la richiesta di porte temporanee usando una logica di ripetizione meno aggressiva. 

Le porte temporanee hanno un timeout di inattività di 4 minuti (non modificabile). Se i tentativi di ripetizione sono troppo aggressivi, l'esaurimento delle porte non viene eliminato autonomamente. La modalità e la frequenza con cui l'applicazione effettua nuovi tentativi di transazioni sono quindi un fattore fondamentale della progettazione.

#### <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Assegnare un IP pubblico a ogni macchina virtualeAssign a Public IP to each VM
L'assegnazione di un indirizzo IP pubblico modifica lo scenario in [IP pubblico in una macchina virtuale.](#ilpip) Tutte le porte temporanee dell'IP pubblico usate per ogni VM sono disponibili per la VM, (A differenza degli scenari in cui le porte effimere di un ip pubblico sono condivise con tutte le macchine virtuali associate al rispettivo pool back-end. Esistono compromessi da considerare, ad esempio il costo aggiuntivo degli indirizzi IP pubblici e il potenziale impatto della whitelisting di un numero elevato di singoli indirizzi IP.

>[!NOTE] 
>Questa opzione non è disponibile per i ruoli di lavoro Web.

#### <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Usare più front-end

Quando si usa un'istanza di Load Balancer Standard pubblica, si assegnano [più indirizzi IP front-end per le connessioni in uscita](#multife) e [si moltiplica il numero di porte SNAT disponibili](#preallocatedports).  Creare una configurazione IP front-end, una regola e un pool back-end per attivare la programmazione di SNAT per l'IP pubblico del front-end.  La regola non deve necessariamente funzionare e un probe di integrità non deve necessariamente avere esito positivo.  Se si usano più front-end anche per le connessioni in entrata (anziché solo per quelle in uscita), è consigliabile usare probe di integrità personalizzati per garantire l'affidabilità.

>[!NOTE]
>Nella maggior parte dei casi l'esaurimento delle porte SNAT indica una progettazione non valida.  Assicurarsi di conoscere il motivo dell'esaurimento delle porte prima di usare più front-end per aggiungere porte SNAT.  Potrebbe essere indice di un problema che può causare errori in un secondo momento.

#### <a name="scale-out"></a><a name="scaleout"></a>Scalabilità orizzontale

Le [porte preallocate](#preallocatedports) vengono assegnate in base alle dimensioni del pool back-end e raggruppate in livelli per ridurre al minimo le interruzioni quando alcune delle porte devono essere riallocate in base al successivo livello di dimensioni maggiore del pool back-end.  È possibile scegliere di aumentare l'intensità dell'utilizzo delle porte SNAT per un front-end specifico ridimensionando il pool back-end fino alle dimensioni massime per un determinato livello.  A questo scopo, le dimensioni dell'applicazione devono essere aumentate in modo efficiente.

Ad esempio, due macchine virtuali nel pool back-end devono avere 1024 porte SNAT disponibili per ogni configurazione IP, per un totale di 2048 porte SNAT per la distribuzione.  Se la distribuzione viene aumentata a 50 macchine virtuali, anche se il numero di porte preallocate resta costante per ogni macchina virtuale, potrà essere usato un totale di 51.200 (50 x 1024) porte SNAT dalla distribuzione.  Per aumentare le dimensioni della distribuzione, controllare il numero di [porte preallocate](#preallocatedports) per ogni livello per assicurarsi di definire la scalabilità orizzontale in base alle dimensioni massime per il rispettivo livello.  Se nell'esempio precedente si sceglie di applicare scalabilità orizzontale fino a 51 istanze anziché 50, si passa al livello successivo, finendo con meno porte SNAT per ogni macchina virtuale e in totale.

Se si applica la scalabilità orizzontale fino al successivo livello più alto delle dimensioni del pool back-end, è possibile che si verifichi il timeout di alcune connessioni in uscita se le porte allocate devono essere riallocate.  Se si usano solo alcune delle porte SNAT, la scalabilità orizzontale fino al successivo livello più alto delle dimensioni del pool back-end non è rilevante.  Metà delle porte esistenti verranno riallocate ogni volta che si passerà al successivo livello del pool back-end.  Se non si vuole che questo avvenga, è necessario definire la distribuzione in base alle dimensioni del livello.  In alternativa, assicurarsi che l'applicazione possa eseguire il rilevamento delle porte e nuovi tentativi in base alle esigenze.  I keep-alive TCP possono aiutare a rilevare i casi in cui le porte SNAT non funzionano più a causa della riallocazione.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Usare keep-alive per reimpostare il timeout di inattività per le connessioni uscita

Il timeout di inattività delle connessioni in uscita è di 4 minuti. Questo timeout è regolabile tramite [regole in uscita](../load-balancer/load-balancer-outbound-rules-overview.md#idletimeout). È inoltre possibile utilizzare il trasporto (ad esempio, keepalive TCP) o keepalive a livello di applicazione per aggiornare un flusso di inattività e reimpostare questo timeout di inattività, se necessario.  

Quando si usano keep-alive TCP, è sufficiente abilitarli sul lato della connessione. Ad esempio, è sufficiente abilitarli sul lato server solo per reimpostare il timer di inattività del flusso, mentre questo non è necessario per entrambi i lati per i keep-alive TCP avviati.  Si applicano concetti simili anche per il livello dell'applicazione, tra cui le configurazioni client-server di database.  Controllare il lato server per verificare le opzioni disponibili per keep-alive specifici dell'applicazione.

## <a name="discovering-the-public-ip-that-a-vm-uses"></a><a name="discoveroutbound"></a>Individuazione dell'IP pubblico usato da una VM
Esistono molti modi per determinare l'indirizzo IP di origine pubblica di una connessione in uscita. OpenDNS offre un servizio che consente di visualizzare l'indirizzo IP pubblico della macchina virtuale. 

Usando il comando nslookup è possibile inviare una query DNS per il nome myip.opendns.com al resolver OpenDNS. Il servizio restituisce l'indirizzo IP di origine usato per inviare la query. Quando si esegue la query seguente dalla macchina virtuale, la risposta è l'indirizzo IP pubblico usato per tale VM:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Impedire la connettività in uscita
A volte è preferibile che a una VM non sia consentito creare un flusso in uscita oppure potrebbe essere necessario gestire le destinazioni che possono essere raggiunte con i flussi in uscita o le destinazioni che possono iniziare flussi in ingresso. In questo caso è possibile usare i [gruppi di sicurezza di rete](../virtual-network/security-overview.md) per gestire le destinazioni che la macchina virtuale può raggiungere. È anche possibile usare i gruppi di sicurezza di rete per gestire la destinazione pubblica che può avviare i flussi in ingresso.

Quando si applica un gruppo di sicurezza di rete a una macchina virtuale con carico bilanciato, considerare i [tag del servizio](../virtual-network/security-overview.md#service-tags) e le [regole di sicurezza predefinite](../virtual-network/security-overview.md#default-security-rules). È necessario assicurarsi che la macchina virtuale riceva richieste di probe di integrità da Azure Load Balancer. 

Se un gruppo di sicurezza di rete blocca le richieste di probe di integrità dal tag AZURE_LOADBALANCER predefinito, il probe di integrità della macchina virtuale avrà esito negativo e la macchina virtuale sarà contrassegnata come non attiva. Load Balancer interrompe l'invio di nuovi flussi a tale macchina virtuale.

## <a name="limitations"></a>Limitazioni
- I ruoli di lavoro Web senza una rete virtuale e altri servizi della piattaforma Microsoft possono essere accessibili quando si usa solo Load Balancer Standard interno a causa di un effetto collaterale della modalità di funzionamento dei servizi pre-VNet e altre piattaforme di servizi. Non è necessario basarsi su questo effetto collaterale poiché il servizio stesso o la piattaforma sottostante sono soggetti a modifiche senza preavviso. Presupporre sempre che è necessario creare una connettività in uscita in modo esplicito se desiderato quando si usa solo Load Balancer Standard interno. Lo scenario [SNAT predefinito](#defaultsnat) 3 descritto in questo articolo non è disponibile.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Load Balancer Standard](load-balancer-standard-overview.md).
- Altre informazioni sulle [regole in uscita](load-balancer-outbound-rules-overview.md) per un'istanza di Load Balancer Standard pubblica.
- Vedere altre informazioni su [Azure Load Balancer](load-balancer-overview.md).
- Ulteriori informazioni sui gruppi di sicurezza di [rete](../virtual-network/security-overview.md).
- Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
