---
title: Raccolta di origini dati JSON personalizzate con l'agente di Log Analytics per Linux in Monitoraggio di Azure
description: È possibile raccogliere origini dati JSON personalizzate in Monitoraggio di Azure tramite l'agente di Log Analytics per Linux.  Queste origini dati personalizzate possono essere semplici script che restituiscono JSON, ad esempio curl, o uno degli oltre 300 plug-in di FluentD. Questo articolo descrive la configurazione necessaria per questa raccolta di dati.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 4d4e868a181c216c29eeb5772b3e4c6108f514b1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616887"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Raccolta di origini dati JSON personalizzate con l'agente di Log Analytics per Linux in Monitoraggio di Azure
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

È possibile raccogliere origini dati JSON personalizzate in [Monitoraggio di Azure](../platform/data-platform.md) tramite l'agente di Log Analytics per Linux.  Queste origini dati personalizzate possono essere semplici script che restituiscono JSON, ad esempio [curl](https://curl.haxx.se/) o uno dei 300 plug-in di [fluentd](https://www.fluentd.org/plugins/all). Questo articolo descrive la configurazione necessaria per questa raccolta di dati.


> [!NOTE]
> Per i dati JSON personalizzati è necessario l'agente di Log Analytics per Linux v1.1.0-217+

## <a name="configuration"></a>Configurazione

### <a name="configure-input-plugin"></a>Configurare il plug-in di input

Per raccogliere dati JSON in Monitoraggio di Azure, aggiungere `oms.api.` all'inizio di un tag FluentD in un plug-in di input.

Di seguito, ad esempio, è riportato un file di configurazione separato `exec-json.conf` in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Viene usato il plug-in FluentD `exec` per eseguire un comando curl ogni 30 secondi.  L'output di questo comando viene raccolto dal plug-in di output JSON.

```xml
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

Per il file di configurazione aggiunto in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` sarà necessario modificare la proprietà con il comando seguente.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Configurare il plug-in di output 
Aggiungere la configurazione del plug-in di output seguente alla configurazione principale presente in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` o come un file di configurazione separato inserito in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```xml
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Riavviare l'agente di Log Analytics per Linux
Riavviare il servizio dell'agente di Log Analytics per Linux con il comando seguente.

```console
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="output"></a>Output
I dati verranno raccolti in Monitoraggio di Azure con un record di tipo `<FLUENTD_TAG>_CL`.

Ad esempio, il tag personalizzato `tag oms.api.tomcat` in Monitoraggio di Azure verrà raccolto con un record di tipo `tomcat_CL`.  È possibile recuperare tutti i record di questo tipo con la query di log seguente.

```console
Type=tomcat_CL
```

Sono supportate anche origini dati JSON annidate, che tuttavia vengono indicizzate in base al campo padre. I dati JSON seguenti, ad esempio, vengono restituiti da una query di log come `tag_s : "[{ "a":"1", "b":"2" }]`.

```json
{
    "tag": [{
      "a":"1",
      "b":"2"
    }]
}
```


## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulle [query di log](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni. 
