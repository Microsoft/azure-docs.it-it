---
title: Raccogliere dati da CollectD in Monitoraggio di Azure | Microsoft Docs
description: CollectD è un daemon Linux open source che, a intervalli regolari, raccoglie dati dalle applicazioni e informazioni a livello di sistema.  Questo articolo fornisce informazioni sulla raccolta di dati da CollectD in Monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: 6848b4e0463be803fb4f41797d933386cb575a0c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732106"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Raccogliere dati da CollectD su agenti Linux in Monitoraggio di Azure
[CollectD](https://collectd.org/) è un daemon Linux open source che, a intervalli regolari, raccoglie metriche sulle prestazioni dalle applicazioni e informazioni a livello di sistema. Applicazioni di esempio includono Java Virtual Machine (JVM), MySQL Server e Nginx. Questo articolo fornisce informazioni sulla raccolta di dati sulle prestazioni da CollectD in Monitoraggio di Azure.

Un elenco completo dei plug-in disponibili è contenuto nella [Tabella dei plug-in](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Panoramica di CollectD](media/data-sources-collectd/overview.png)

Nell'agente di Log Analytics per Linux è inclusa la configurazione di CollectD seguente per indirizzare i dati di CollectD all'agente.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

```xml
LoadPlugin write_http

<Plugin write_http>
   <Node "oms">
      URL "127.0.0.1:26000/oms.collectd"
      Format "JSON"
      StoreRates true
   </Node>
</Plugin>
```

Se si usa una versione di CollectD precedente a 5.5, adottare invece la configurazione seguente.

```xml
LoadPlugin write_http

<Plugin write_http>
   <URL "127.0.0.1:26000/oms.collectd">
      Format "JSON"
      StoreRates true
   </URL>
</Plugin>
```

La configurazione di CollectD usa il plug-in predefinito `write_http` per inviare dati delle metriche sulle prestazioni all'agente di Log Analytics per Linux attraverso la porta 26000. 

> [!NOTE]
> Se necessario, questa porta può essere configurata su una porta personalizzata.

L'agente di Log Analytics per Linux resta in ascolto delle metriche di CollectD sulla porta 26000 e quindi le converte in metriche per lo schema di Monitoraggio di Azure. Di seguito è illustrata la configurazione dell'agente di Log Analytics per Linux `collectd.conf`.

```xml
<source>
   type http
   port 26000
   bind 127.0.0.1
</source>

<filter oms.collectd>
   type filter_collectd
</filter>
```

> [!NOTE]
> Collectd per impostazione predefinita è impostato su Read values a un [intervallo](https://collectd.org/wiki/index.php/Interval)di 10 secondi. Poiché questo influisca direttamente sul volume dei dati inviati ai log di monitoraggio di Azure, potrebbe essere necessario ottimizzare questo intervallo nella configurazione di collectd per ottenere un giusto equilibrio tra i requisiti di monitoraggio e i costi e l'utilizzo associati per i log di monitoraggio di Azure.

## <a name="versions-supported"></a>Versioni supportate
- Monitoraggio di Azure supporta attualmente CollectD versione 4.8 e versioni successive.
- Per la raccolta delle metriche di CollectD è necessario l'agente di Log Analytics per Linux versione 1.1.0-217 o successiva.


## <a name="configuration"></a>Configurazione
Di seguito è riportata la procedura di base per configurare la raccolta di dati CollectD in Monitoraggio di Azure.

1. Configurare CollectD per l'invio di dati all'agente di Log Analytics per Linux con il plug-in write_http.  
2. Configurare l'agente di Log Analytics per Linux in modo che resti in ascolto dei dati di CollectD sulla porta appropriata.
3. Riavviare CollectD e l'agente di Log Analytics per Linux.

### <a name="configure-collectd-to-forward-data"></a>Configurare CollectD per l'inoltro di dati 

1. Per indirizzare dati di CollectD all'agente di Log Analytics per Linux, è necessario che `oms.conf` sia stato aggiunto alla directory di configurazione di CollectD. La destinazione di questo file dipende dalla distribuzione di Linux presente sul computer.

    Se la directory di configurazione di CollectD si trova in /etc/collectd.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf
    ```

    Se la directory di configurazione di CollectD si trova in /etc/collectd/collectd.conf.d/:

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf
    ```

    >[!NOTE]
    >Per versioni di CollectD antecedenti a 5.5, è necessario modificare i tag in `oms.conf`, come illustrato in precedenza.
    >

2. Copiare collectd.conf nella directory di configurazione di omsagent dell'area di lavoro desiderata.

    ```console
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf
    ```

3. Riavviare CollectD e l'agente di Log Analytics per Linux con i comandi seguenti.

    ```console
    sudo service collectd restart
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Conversione dello schema di metriche di CollectD nello schema di Monitoraggio di Azure
Per mantenere un modello comune tra le metriche dell'infrastruttura già raccolte dall'agente di Log Analytics per Linux e le nuove metriche raccolte da CollectD, viene usato il mapping dello schema seguente:

| Campo metrica CollectD | Campo di Monitoraggio di Azure |
|:--|:--|
| `host` | Computer |
| `plugin` | nessuno |
| `plugin_instance` | Nome dell'istanza<br>Se **plugin_instance** è *null*, InstanceName="*_Total*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Se **type_instance** è *null*, CounterName=**blank** |
| `dsnames[]` | CounterName |
| `dstypes` | nessuno |
| `values[]` | CounterValue |

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [query di log](../logs/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 
* Usare [campi personalizzati](../logs/custom-fields.md) per analizzare i dati dei record Syslog nei singoli campi.