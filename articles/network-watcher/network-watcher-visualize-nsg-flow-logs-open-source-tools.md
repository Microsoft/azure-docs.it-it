---
title: Visualizzare i log di flusso NSG-Elastic stack
titleSuffix: Azure Network Watcher
description: Gestire e analizzare i log di flusso dei gruppi di sicurezza di rete in Azure con Network Watcher ed Elastic Stack.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 0896df301718c74e63a9e18c74615130fa80c952
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986254"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualizzare i log dei flussi dei gruppi di sicurezza di rete di Azure Network Watcher con strumenti open source

I log dei flussi dei gruppi di sicurezza di rete contengono informazioni utili per comprendere il traffico IP in ingresso e in uscita nei gruppi di sicurezza di rete. Questi log mostrano i flussi in ingresso e in uscita in base alle regole, alla scheda di interfaccia di rete a cui si applica il flusso, a informazioni a 5 tuple sul flusso (IP di origine/destinazione, porta di origine/destinazione e protocollo) e al fatto che il traffico sia stato consentito o rifiutato.

Analizzare manualmente i log dei flussi e ottenerne informazioni significative può essere difficile. Esistono tuttavia diversi strumenti open source che possono semplificare la visualizzazione di questi dati. Questo articolo presenta una soluzione per visualizzare questi log con Elastic Stack, che consentirà di indicizzare e visualizzare rapidamente i log dei flussi in un dashboard Kibana.

## <a name="scenario"></a>Scenario

In questo articolo si configurerà una soluzione che consentirà di visualizzare i log dei flussi dei gruppi di sicurezza di rete con Elastic Stack.  Un plug-in di input Logstash otterrà i log dei flussi direttamente dal BLOB del servizio di archiviazione configurato per contenerli. Successivamente, con Elastic Stack, i log dei flussi verranno indicizzati e usati per creare un dashboard Kibana per visualizzare le informazioni.

![Il diagramma mostra uno scenario che consente di visualizzare i log di flusso dei gruppi di sicurezza di rete con Elastic stack.][scenario]

## <a name="steps"></a>Passaggi

### <a name="enable-network-security-group-flow-logging"></a>Abilitare la registrazione dei flussi dei gruppi di sicurezza di rete
Per questo scenario, è necessario abilitare la registrazione dei flussi dei gruppi di sicurezza di rete in almeno un gruppo di sicurezza di rete nel proprio account. Per istruzioni sull'abilitazione dei log del flusso di sicurezza di rete, vedere l'articolo [Introduzione alla registrazione dei flussi per i gruppi di sicurezza di rete](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Configurare Elastic Stack
Connettendo i log dei flussi dei gruppi di sicurezza di rete con Elastic Stack, è possibile creare un dashboard Kibana che consente di eseguire ricerche e analisi, creare grafici e ottenere informazioni significative dai log.

#### <a name="install-elasticsearch"></a>Installare Elasticsearch

1. Elastic Stack versione 5.0 e successive richiede Java 8. Eseguire il comando `java -version` per controllare la versione in uso. Se Java non è installato, vedere la documentazione sui [JDK supportati da Azure](https://aka.ms/azure-jdks).
2. Scaricare il pacchetto binario corretto per il proprio sistema:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Per altri metodi di installazione, vedere [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html) (Installazione di Elasticsearch)

3. Verificare che Elasticsearch sia in esecuzione con questo comando:

    ```bash
    curl http://127.0.0.1:9200
    ```

    La risposta visualizzata sarà simile a questa:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Per altre istruzioni sull'installazione di Elasticsearch, vedere [Istruzioni di installazione](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Installare Logstash

1. Per installare Logstash, eseguire questi comandi:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Successivamente è necessario configurare Logstash per accedere ai log dei flussi e analizzarli. Creare un file logstash.conf usando:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Aggiungere il contenuto seguente al file:

   ```
   input {
      azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
            container => "insights-logs-networksecuritygroupflowevent"
            codec => "json"
            # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
            # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
            file_head_bytes => 12
            file_tail_bytes => 2
            # Enable / tweak these settings when event is too big for codec to handle.
            # break_json_down_policy => "with_head_tail"
            # break_json_batch_count => 2
        }
      }

      filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"}

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
      add_field => {
                  "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                  "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                  "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                  "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                  "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                  "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                  "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                  "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                  "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
                   "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
                   "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
                   "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
                   "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
                   }
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
       match => ["unixtimestamp" , "UNIX"]
     }
    }
   output {
     stdout { codec => rubydebug }
     elasticsearch {
       hosts => "localhost"
       index => "nsg-flow-logs"
     }
   }  
   ```

Per altre istruzioni sull'installazione di Logstash, vedere la [documentazione ufficiale](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installare il plug-in di input Logstash per l'archiviazione BLOB di Azure

Questo plug-in Logstash consentirà di accedere direttamente ai log dei flussi dall'account di archiviazione designato. Per installare questo plug-in, nella directory di installazione Logstash predefinita (in questo caso /usr/share/logstash/bin) eseguire il comando:

```bash
logstash-plugin install logstash-input-azureblob
```

Per avviare Logstash, eseguire questo comando:

```bash
sudo /etc/init.d/logstash start
```

Per altre informazioni sul plug-in, vedere la [documentazione](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Installare Kibana

1. Eseguire questi comandi per installare Kibana:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Per eseguire Kibana, usare questi comandi:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Per visualizzare l'interfaccia Web di Kibana, passare a `http://localhost:5601`
4. Per questo scenario, il modello di indice usato per i log dei flussi è "nsg-flow-logs". È possibile modificare il modello di indice nella sezione "output" del file logstash.conf.
5. Per visualizzare il dashboard Kibana in remoto, creare una regola dei gruppi di sicurezza di rete in ingresso che consenta l'accesso alla **porta 5601**.

### <a name="create-a-kibana-dashboard"></a>Creare un dashboard Kibana

Un dashboard di esempio per visualizzare le tendenze e i dettagli degli avvisi è illustrato nell'immagine seguente:

![Figura 1][1]

Scaricare il [file del dashboard](https://aka.ms/networkwatchernsgflowlogdashboard), il [file delle visualizzazioni](https://aka.ms/networkwatchernsgflowlogvisualizations) e il [file della ricerca salvata](https://aka.ms/networkwatchernsgflowlogsearch).

Nella scheda **Management** (Gestione) di Kibana passare a **Saved Objects** (Oggetti salvati) e importare tutti e tre i file. Dalla scheda **Dashboard** è quindi possibile aprire e caricare il dashboard di esempio.

È anche possibile creare visualizzazioni e dashboard personalizzati per le metriche a cui si è interessati. Per altre informazioni sulla creazione di visualizzazioni Kibana, vedere la [documentazione ufficiale](https://www.elastic.co/guide/en/kibana/current/visualize.html) di Kibana.

### <a name="visualize-nsg-flow-logs"></a>Visualizzare i log dei flussi dei gruppi di sicurezza di rete

Il dashboard di esempio offre diverse visualizzazioni dei log dei flussi.

1. Flussi per decisione/direzione nel tempo: grafici di serie temporali che mostrano il numero dei flussi nel periodo di tempo specificato. È possibile modificare l'unità di tempo e l'intervallo di entrambe queste visualizzazioni. Il grafico dei flussi per decisione mostra la proporzione tra le decisioni di consentire e di rifiutare il traffico che sono state prese, mentre quello dei flussi per direzione mostra la proporzione tra traffico in ingresso e in uscita. Questi oggetti visivi consentono di esaminare le tendenze del traffico nel tempo e individuare eventuali picchi o modelli insoliti.

   ![Screenshot mostra un dashboard di esempio con i flussi in base a decisione e direzione nel tempo.][2]

2. Flussi per porta di origine/destinazione: grafici a torta che mostrano la suddivisione dei flussi sulle rispettive porte. Questa visualizzazione consente di verificare le porte più usate. Facendo clic su una porta specifica nel grafico a torta, il resto del dashboard viene filtrato in modo da visualizzare i flussi di tale porta.

   ![Screenshot mostra un dashboard di esempio con flussi in base alla porta di origine e di destinazione.][3]

3. Numero di flussi e data e ora del primo log: metriche che mostrano il numero di flussi registrato e la data del primo log acquisito.

   ![Screenshot mostra un dashboard di esempio con il numero di flussi e il primo tempo di registrazione.][4]

4. Flussi per gruppo di sicurezza di rete e regola: grafico a barre che mostra la distribuzione dei flussi in ogni gruppo di sicurezza di rete nonché la distribuzione delle regole all'interno di ogni gruppo. Questo grafico consente di determinare il gruppo di sicurezza di rete e le regole che hanno generato la maggiore quantità di traffico.

   ![Screenshot mostra un dashboard di esempio con i flussi per N S G e la regola.][5]

5. 10 principali IP di origine/destinazione: grafici a barre che mostrano i 10 principali indirizzi IP di origine e di destinazione. È possibile modificare i grafici in modo da visualizzare un numero maggiore o minore di indirizzi IP principali. Questi grafici consentono di rilevare gli indirizzi IP più ricorrenti nonché le decisioni di consentire o rifiutare il traffico prese nei confronti di ogni IP.

   ![Screenshot mostra un dashboard di esempio con I flussi in base ai primi dieci indirizzi I P di origine e di destinazione.][6]

6. Tuple dei flussi: questa tabella mostra le informazioni contenute in ogni tupla dei flussi, nonché il gruppo di sicurezza di rete e la regola corrispondenti.

   ![Screenshot mostra le tuple del flusso in una tabella.][7]

Usando la barra per le query nella parte superiore è possibile filtrare il dashboard in base a qualsiasi parametro dei flussi, come ID sottoscrizione, gruppi di risorse, regola o qualsiasi altra variabile a cui si è interessati. Per altre informazioni su query e filtri di Kibana, vedere la [documentazione ufficiale](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Conclusioni

Combinando i log dei flussi dei gruppi di sicurezza di rete con Elastic Stack, si è ottenuto uno strumento personalizzabile ed efficace per visualizzare il traffico di rete. Questi dashboard consentono di ottenere e condividere rapidamente informazioni significative sul traffico di rete, nonché di applicare filtri e ricercare potenziali anomalie. Usando Kibana, è possibile personalizzare i dashboard e creare visualizzazioni specifiche per soddisfare qualsiasi esigenza in termini di sicurezza, controllo e conformità.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI, vedere [Visualizzare i log dei flussi dei gruppi di sicurezza di rete con Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
