---
title: Front-end multipli-Azure Load Balancer
description: Con questo percorso di apprendimento, iniziare con una panoramica di più front-end in Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 2192531aec7800314c6748740262f8746da0c4fc
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956373"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Più front-end per Azure Load Balancer

Azure Load Balancer consente di eseguire il bilanciamento del carico dei servizi su più porte, più indirizzi IP o entrambi. È possibile usare le definizioni di servizio di bilanciamento del carico interno e pubblico per eseguire il bilanciamento del carico dei flussi in un set di macchine virtuali.

Questo articolo descrive i principi fondamentali di questa funzionalità, i concetti importanti e i vincoli. Se si intende esporre i servizi in un solo indirizzo IP, sono disponibili istruzioni semplificate per le configurazioni di servizi di bilanciamento del carico [pubblici](load-balancer-get-started-internet-portal.md) o [interni](load-balancer-get-started-ilb-arm-portal.md). L'aggiunta di più front-end è un'operazione incrementale rispetto a una configurazione con un solo front-end. Usando i concetti illustrati in questo articolo è possibile espandere una configurazione semplificata in qualsiasi momento.

Quando si definisce un'istanza di Azure Load Balancer, viene connessa una configurazione di pool front-end e back-end con regole. Il probe di integrità a cui fa riferimento la regola viene usato per determinare il modo in cui i nuovi flussi vengono inviati a un nodo nel pool back-end. Il front-end (noto anche come VIP, indirizzo IP virtuale) è definito da una tupla di 3 elementi costituita da un indirizzo IP (pubblico o interno), un protocollo di trasporto (UDP o TCP) e un numero di porta della regola di bilanciamento del carico. Il pool back-end è una raccolta di configurazioni IP di macchine virtuali (parte della risorsa NIC) cui fa riferimento il pool back-end di bilanciamento del carico.

La tabella seguente contiene alcune configurazioni front-end di esempio:

| Front-end | Indirizzo IP | protocol | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

La tabella descrive quattro front-end diversi. I front-end 1, 2 e 3 rappresentano un unico front-end con più regole. Viene usato lo stesso indirizzo IP, ma la porta o il protocollo sono diversi per ogni front-end. I front-end 1 e 4 sono un esempio di più front-end, in cui lo stesso protocollo e la stessa porta front-end vengono riusati in più front-end.

Azure Load Balancer offre flessibilità nella definizione di regole del servizio di bilanciamento del carico. Una regola dichiara il modo in cui viene eseguito il mapping di un indirizzo e una porta nel front-end all'indirizzo e alla porta di destinazione nel back-end. L'eventuale riutilizzo delle porte back-end tra regole dipende dal tipo di regola. Ogni tipo di regola presenta requisiti specifici che possono influire sulla configurazione degli host e la progettazione dei probe. Esistono due tipi di regole:

1. La regola predefinita senza riutilizzo delle porte back-end
2. La regola con indirizzo IP mobile che prevede il riutilizzo delle porte back-end

Azure Load Balancer consente di combinare i due tipi di regole nella stessa configurazione del servizio di bilanciamento del carico. Il servizio di bilanciamento del carico può usare le regole contemporaneamente per una determinata macchina virtuale o una qualsiasi combinazione, purché siano rispettati i vincoli della regola. Il tipo di regola scelto dipende dai requisiti dell'applicazione e dalla complessità correlata al supporto della configurazione. È necessario valutare quali tipi di regole siano più adatti allo scenario.

Questi scenari verranno approfonditi iniziando con il comportamento predefinito.

## <a name="rule-type-1-no-backend-port-reuse"></a>Regola di tipo 1: nessun riutilizzo delle porte back-end

![Figura con più front-end e un front-end in colore verde e viola](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

In questo scenario i front-end sono configurati nel modo seguente:

| Front-end | Indirizzo IP | protocol | port |
| --- | --- | --- | --- |
| ![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![front-end viola](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Il DIP è la destinazione del flusso in ingresso. Nel pool di back-end, ogni macchina virtuale espone il servizio desiderato su una porta univoca in un DIP. Questo servizio è associato al front-end tramite una definizione di regole.

Si definiscono due regole:

| Regola | Mapping frontend | Al pool back-end |
| --- | --- | --- |
| 1 |![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png)  DIP2:80 |
| 2 |![Indirizzo VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png)  DIP2:81 |

Il mapping completo in Azure Load Balancer sarà ora il seguente:

| Regola | Indirizzo IP front-end | protocol | port | Destination | port |
| --- | --- | --- | --- | --- | --- |
| ![regola verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |Indirizzo IP DIP |80 |
| ![regola viola](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |Indirizzo IP DIP |81 |

Ogni regola deve produrre un flusso con una combinazione univoca di indirizzo IP di destinazione e porta di destinazione. Modificando la porta di destinazione del flusso, più regole possono inviare flussi allo stesso DIP in porte diverse.

I probe di integrità vengono sempre indirizzati al DIP di una macchina virtuale. È necessario assicurarsi che il probe rifletta lo stato della VM.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regola di tipo 2: riutilizzo delle porte back-end tramite indirizzo IP mobile

Azure Load Balancer consente di riusare la porta front-end in più front-end indipendentemente dal tipo di regola usato. Alcuni scenari di applicazione preferiscono o richiedono l'uso della stessa porta da parte di più istanze dell'applicazione in una singola macchina virtuale nel pool back-end. Esempi comuni di riutilizzo delle porte includono il clustering per la disponibilità elevata, le appliance virtuali di rete e l'esposizione di più endpoint TLS senza rieseguire la crittografia di rete.

Per riutilizzare la porta di back-end tra più regole, è necessario abilitare l'indirizzo IP mobile nella definizione della regola.

Il termine "indirizzo IP mobile" appartiene alla terminologia di Azure e fa riferimento a una parte di ciò che è noto come Direct Server Return (DSR). Il DSR è costituito da due parti: una topologia di flussi e uno schema di mappatura degli indirizzi IP. A livello di piattaforma, Azure Load Balancer viene sempre eseguito in una topologia di flussi DSR indipendentemente dal fatto che l'indirizzo IP mobile sia abilitato o meno. Ciò significa che la parte in uscita di un flusso viene sempre correttamente riscritta in un flusso direttamente nell'origine.

Con il tipo di regola predefinito, Azure espone uno schema di mappatura degli indirizzi IP tradizionale per il servizio di bilanciamento del carico per semplificarne l'uso. L'abilitazione dell'indirizzo IP mobile modifica lo schema di mappatura degli indirizzi IP per consentire una maggiore flessibilità, come illustrato di seguito.

Il diagramma seguente illustra questa configurazione:

![Figura con più front-end e un front-end in colore verde e viola con DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Per questo scenario, ogni macchina virtuale nel pool back-end ha tre interfacce di rete:

* DIP: scheda di interfaccia di rete virtuale associata alla macchina virtuale (configurazione IP della risorsa interfaccia di rete di Azure)
* Front-end 1: un'interfaccia di loopback all'interno del sistema operativo guest configurato con l'indirizzo IP di Front-end 1
* Front-end 2: un'interfaccia di loopback all'interno del sistema operativo guest configurato con l'indirizzo IP di Front-end 2

Per ogni macchina virtuale nel pool back-end, eseguire i comandi seguenti al prompt dei comandi di Windows.

Per ottenere l'elenco dei nomi di interfaccia presenti nella macchina virtuale, digitare il comando seguente:

```console
netsh interface show interface 
```

Per la scheda di interfaccia di rete della VM (gestione di Azure), digitare il comando seguente:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
```

(sostituire interfacet con il nome di questa interfaccia)

Per ogni interfaccia di loopback aggiunta, ripetere i comandi seguenti:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
```

(sostituire InterfaceName con il nome di questa interfaccia di loopback)

```console
netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
```

(sostituire InterfaceName con il nome di questa interfaccia di loopback)

> [!IMPORTANT]
> La configurazione delle interfacce di loopback viene eseguita all'interno del sistema operativo guest. Questa configurazione non viene eseguita o gestita da Azure. Senza questa configurazione, le regole non funzionano. Le definizioni dei probe di integrità usano il DIP della macchina virtuale anziché l'interfaccia di loopback che rappresenta il front-end DSR. Il servizio deve quindi mettere a disposizione le risposte probe su una porta DIP che rifletta lo stato del servizio offerto nell'interfaccia di loopback che rappresenta il front-end DSR.


Si supponga la stessa configurazione front-end dello scenario precedente:

| Front-end | Indirizzo IP | protocol | port |
| --- | --- | --- | --- |
| ![front-end verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![front-end viola](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Si definiscono due regole:

| Regola | Front-end | Mapping al pool back-end |
| --- | --- | --- |
| 1 |![regola](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (in VM1 e VM2) |
| 2 |![regola](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (in VM1 e VM2) |

La tabella seguente illustra il mapping completo nel servizio di bilanciamento del carico:

| Regola | Indirizzo IP front-end | protocol | port | Destination | port |
| --- | --- | --- | --- | --- | --- |
| ![regola verde](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |uguale a front-end (65.52.0.1) |uguale a front-end (80) |
| ![regola viola](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |uguale a front-end (65.52.0.2) |uguale a front-end (80) |

La destinazione del flusso in ingresso è l'indirizzo IP del front-end nell'interfaccia di loopback della macchina virtuale. Ogni regola deve produrre un flusso con una combinazione univoca di indirizzo IP di destinazione e porta di destinazione. Modificando l'indirizzo IP di destinazione del flusso, è possibile riusare la porta nella stessa macchina virtuale. Il servizio viene esposto al servizio di bilanciamento del carico associandolo all'indirizzo IP del front-end e alla porta dell'interfaccia di loopback corrispondente.

Si noti che in questo esempio non viene modificata la porta di destinazione. Anche se questo è uno scenario con indirizzo IP mobile, Azure Load Balancer supporta anche la definizione di una regola per la riscrittura della porta di destinazione back-end e per differenziarla dalla porta di destinazione front-end.

Il tipo di regola con indirizzo IP mobile è alla base di diversi modelli di configurazione del servizio di bilanciamento di carico. Un esempio attualmente disponibile è la configurazione [SQL AlwaysOn con più listener](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) . Altri scenari di questo tipo verranno documentati nel tempo.

## <a name="limitations"></a>Limitazioni

* Le configurazioni di più front-end sono supportate solo con le macchine virtuali IaaS.
* Con la regola dell'indirizzo IP mobile, l'applicazione deve usare la configurazione IP primaria per i flussi SNAT in uscita. Se l'applicazione si associa all'indirizzo IP front-end configurato nell'interfaccia di loopback del sistema operativo guest, il SNAT in uscita di Azure non è disponibile per riscrivere il flusso in uscita e il flusso ha esito negativo.  Esaminare gli [scenari in uscita](load-balancer-outbound-connections.md).
* Gli indirizzi IP pubblici hanno un effetto sulla fatturazione. Per altre informazioni, vedere [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Si applicano i limiti delle sottoscrizioni. Per altre informazioni, vedere i [limiti del servizio](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) .

## <a name="next-steps"></a>Passaggi successivi

- Verificare le [connessioni in uscita](load-balancer-outbound-connections.md) per comprendere l'impatto di più server front-end sul comportamento delle connessioni in uscita.
