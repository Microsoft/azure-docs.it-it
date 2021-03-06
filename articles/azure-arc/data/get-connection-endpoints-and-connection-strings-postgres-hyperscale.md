---
title: Ottenere gli endpoint di connessione e le stringhe di connessione del modulo per il gruppo di server con iperscalabilità PostgreSQL abilitato per Arc
titleSuffix: Azure Arc enabled data services
description: Ottenere gli endpoint di connessione e le stringhe di connessione del modulo per il gruppo di server con iperscalabilità PostgreSQL abilitato per Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: de7d23689ae984ea0abece5edb03cf8a0c3a9be1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670342"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Ottenere gli endpoint di connessione e le stringhe di connessione del modulo per il gruppo di server con iperscalabilità PostgreSQL abilitato per Arc

In questo articolo viene illustrato come recuperare gli endpoint di connessione per il gruppo di server e come creare stringhe di connessione che si utilizzeranno con le applicazioni e/o gli strumenti di.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Ottenere gli endpoint di connessione:

### <a name="from-cli-with-azdata"></a>Dall'interfaccia della riga di comando con azdata
#### <a name="1-connect-to-your-arc-data-controller"></a>1. connettersi al controller di dati Arc:
- Se è già stata aperta una sessione nell'host del controller di dati Arc, eseguire il comando seguente:
```console
azdata login
```

- Se non è presente una sessione aperta nell'host del controller di dati Arc: eseguire il comando seguente 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. Mostra gli endpoint di connessione
Eseguire il comando seguente:
```console
azdata arc postgres endpoint list -n <server group name>
```
ad esempio:
```console
azdata arc postgres endpoint list -n postgres01
```

Viene visualizzato l'elenco degli endpoint: l'endpoint PostgreSQL usato per connettere l'applicazione e usare gli endpoint database, Kibana e Grafana per log Analytics e il monitoraggio. Ad esempio: 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
Usare questi endpoint per:
- Formare le stringhe di connessione e connettersi con gli strumenti o le applicazioni client
- Accedere ai dashboard Grafana e Kibana dal browser

Ad esempio, è possibile usare l'istanza di end point denominata _PostgreSQL_ per connettersi a PSQL al gruppo di server. Ad esempio:
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - La password dell'utente _Postgres_ indicata nel punto finale denominato "istanza di _PostgreSQL_" è la password scelta per la distribuzione del gruppo di server.
> - Informazioni su azdata: il lease associato alla connessione dura circa 10 ore. Dopodiché è necessario riconnettersi. Se il lease è scaduto, viene ricevuto il messaggio di errore seguente quando si tenta di eseguire un comando con azdata (diverso dall'account di accesso di azdata): _errore: (401)_ 
>  motivo: intestazioni di risposta http _non autorizzate_ 
>  _: HTTPHeaderDict ({' date ':' Sun, 06 Sep 2020 16:58:38 GMT ',' Content-Length ':' 0',' www-Authenticate ':'_ 
>  _Basic realm = "login_ Credentials required", Bearer Error = "invalid_token", error_description = "il token è scaduto"'}) _ quando si verifica questo problema, è necessario riconnettersi a azdata come illustrato in precedenza.

## <a name="from-cli-with-kubectl"></a>Dall'interfaccia della riga di comando con kubectl
- Se il gruppo di server è di Postgres versione 12 (impostazione predefinita), il comando seguente:
```console
kubectl get postgresql-12/<server group name> -n <namespace name>
```
- Se il gruppo di server è di Postgres versione 11, il comando seguente:
```console
kubectl get postgresql-11/<server group name> -n <namespace name>
```

Questi comandi produrranno output come quello riportato di seguito. È possibile utilizzare tali informazioni per formare le stringhe di connessione:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>Stringhe di connessione del modulo:
Usare la tabella seguente di modelli di stringhe di connessione per il gruppo di server. Sarà quindi possibile copiarli e incollarli e personalizzarli in base alle esigenze:

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>PSQL

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sul [ridimensionamento (aggiunta di nodi di lavoro)](scale-out-postgresql-hyperscale-server-group.md) al gruppo di server
- Per informazioni su come [aumentare o ridurre la scalabilità (aumento/riduzione della memoria/VCore)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) del gruppo di server


