---
title: "Esercitazione: Creare un'app multicontenitore"
description: Informazioni su come creare un'app multicontenitore in Servizio app di Azure che contiene un'app WordPress e un contenitore MySQL, nonché configurare l'app WordPress.
keywords: servizio app di azure, app web, linux, docker, compose, multicontenitore, multi-contenitore, app web per contenitori, più contenitori, contenitore, wordpress, db azure per mysql, database di produzione con contenitori
author: msangapu-msft
ms.topic: tutorial
ms.date: 10/31/2020
ms.author: msangapu
ms.custom: cli-validate, devx-track-azurecli
ms.openlocfilehash: e73d681cadaddd32290cec40ae7a9c8f6e8ac758
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005648"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>Esercitazione: Creare un'app multi-contenitore (anteprima) in App Web per contenitori

> [!NOTE]
> La funzionalità multi-contenitore è disponibile in anteprima.

[App Web per contenitori](overview.md#app-service-on-linux) offre un modo flessibile per usare le immagini Docker. In questa esercitazione si apprenderà come creare un'app multi-contenitore usando WordPress e MySQL. Questa esercitazione verrà completata in Cloud Shell, ma gli stessi comandi possono essere eseguiti anche in locale con l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) (2.0.32 o versioni successive).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Convertire una configurazione Docker Compose per l'uso con App Web per contenitori
> * Distribuire un'app multi-contenitore in Azure
> * Aggiungere le impostazioni applicazione
> * Usare l'archiviazione permanente per i contenitori
> * Connettersi a Database di Azure per MySQL
> * Risolvere gli errori

[!INCLUDE [Free trial note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione è necessario avere una buona conoscenza di [Docker Compose](https://docs.docker.com/compose/).

## <a name="download-the-sample"></a>Scaricare l'esempio

Per questa esercitazione si userà il file Compose di [Docker](https://docs.docker.com/compose/wordpress/#define-the-project), ma modificato in modo da includere Database di Azure per MySQL, l'archiviazione persistente e Redis. Il file di configurazione è disponibile negli [Esempi di Azure](https://github.com/Azure-Samples/multicontainerwordpress). Per le opzioni di configurazione supportate, vedere [Opzioni di Docker Compose](configure-custom-container.md#docker-compose-options).

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

In Cloud Shell creare una directory di esercitazione e passare a questa directory.

```bash
mkdir tutorial

cd tutorial
```

Eseguire quindi il comando seguente per clonare il repository dell'app di esempio nella directory di esercitazione. Quindi, passare alla directory `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

In Cloud Shell creare un gruppo di risorse con il comando [`az group create`](/cli/azure/group#az-group-create). L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella località *Stati Uniti centro-meridionali*. Per visualizzare tutte le località supportate per il servizio app in Linux nel livello **Standard**, eseguire il comando [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

In genere, il gruppo di risorse e le risorse vengono create in un'area vicina alla località dell'utente.

Al termine del comando, un output JSON visualizza le proprietà del gruppo di risorse.

## <a name="create-an-azure-app-service-plan"></a>Creare un piano di servizio app di Azure

In Cloud Shell creare un piano di servizio app nel gruppo di risorse con il comando [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create).

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

L'esempio seguente crea un piano di servizio app denominato `myAppServicePlan` nel piano tariffario **Standard** (`--sku S1`) e in un contenitore Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Al termine della creazione del piano di servizio app, Cloud Shell visualizza informazioni simili all'esempio seguente:

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>Docker Compose con contenitori WordPress e MySQL

## <a name="create-a-docker-compose-app"></a>Creare un'app Docker Compose

In Cloud Shell creare un'[app Web](overview.md) multi-contenitore nel piano di servizio app `myAppServicePlan` con il comando [az webapp create](/cli/azure/webapp#az-webapp-create). Non dimenticare di sostituire _\<app-name>_ con un nome univoco dell'app.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Dopo la creazione dell'app Web, Cloud Shell visualizza un output simile all'esempio seguente:

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>Passare all'app

Passare all'app distribuita all'indirizzo `http://<app-name>.azurewebsites.net`. Il caricamento dell'app può richiedere alcuni minuti. Se si riceve un errore, attendere qualche minuto e quindi aggiornare il browser. Se si verificano problemi, per la risoluzione esaminare i [log dei contenitori](#find-docker-container-logs).

![App multi-contenitore di esempio in App Web per contenitori][1]

La creazione di un'app multi-contenitore in App Web per contenitori è stata **completata**. Ora si configurerà l'app per l'uso di Database di Azure per MySQL. Non installare WordPress in questo momento.

## <a name="connect-to-production-database"></a>Connettersi al database di produzione

Non è consigliabile usare contenitori di database in un ambiente di produzione. I contenitori locali non sono scalabili. In questo caso si userà Database di Azure per MySQL, che è scalabile.

### <a name="create-an-azure-database-for-mysql-server"></a>Creare un server Database di Azure per MySQL

Creare un server Database di Azure per MySQL con il comando [`az mysql server create`](/cli/azure/mysql/server#az-mysql-server-create).

Nel comando seguente sostituire il segnaposto _&lt;mysql-server-name>_ con il nome del server MySQL (i caratteri validi sono `a-z`, `0-9` e `-`). Poiché questo nome fa parte del nome host del server MySQL (`<mysql-server-name>.database.windows.net`) è necessario che sia univoco a livello globale.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen5_1 --version 5.7
```

La creazione del server può richiedere alcuni minuti. Al termine della creazione del server MySQL, Cloud Shell visualizza informazioni simili all'esempio seguente:

<pre>
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "southcentralus",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
}
</pre>

### <a name="configure-server-firewall"></a>Configurare il firewall del server

Creare una regola del firewall per il server MySQL per consentire le connessioni client usando il comando [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create). Quando sia l'IP iniziale che l'IP finale sono impostati su 0.0.0.0, il firewall viene aperto solo per le altre risorse di Azure.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> È possibile rendere ancora più restrittiva la regola del firewall [usando solo gli indirizzi IP in uscita usati dall'app](overview-inbound-outbound-ips.md#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Creare il database di WordPress

```azurecli-interactive
az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name wordpress
```

Al termine della creazione del database, Cloud Shell visualizza informazioni simili all'esempio seguente:

<pre>
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
</pre>

### <a name="configure-database-variables-in-wordpress"></a>Configurare le variabili di database in WordPress

Per connettere l'app WordPress a questo nuovo server MySQL occorre configurare alcune variabili di ambiente specifiche di WordPress, incluso il percorso della CA SSL definito da `MYSQL_SSL_CA`. Nell'[immagine personalizzata](#use-a-custom-image-for-mysql-ssl-and-other-configurations) più avanti viene fornito il certificato [Baltimore CyberTrust Root](https://www.digicert.com/digicert-root-certificates.htm) di [DigiCert](https://www.digicert.com/).

Per apportare queste modifiche, usare il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) in Cloud Shell. Le impostazioni delle app applicano la distinzione tra maiuscole e minuscole e sono separate da spazi.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WORDPRESS_DB_HOST="<mysql-server-name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql-server-name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

Al termine della creazione delle impostazioni app, Cloud Shell visualizza informazioni simili all'esempio seguente:

<pre>
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "&lt;mysql-server-name&gt;.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@&lt;mysql-server-name&gt;"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
</pre>

Per altre informazioni sulle variabili di ambiente, vedere [Configurare le variabili di ambiente](configure-custom-container.md#configure-environment-variables).

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>Usare un'immagine personalizzata per SSL in MySQL e altre configurazioni

Per impostazione predefinita, Database di Azure per MySQL usa SSL. WordPress richiede una configurazione aggiuntiva per l'uso di SSL con MySQL. L'immagine ufficiale di WordPress non contiene la configurazione aggiuntiva, ma per comodità è stata preparata un'[immagine personalizzata](https://github.com/Azure-Samples/multicontainerwordpress). Nella pratica, si aggiungeranno le modifiche desiderate alla propria immagine.

L'immagine personalizzata si basa sull'immagine ufficiale di [WordPress dall'hub Docker](https://hub.docker.com/_/wordpress/). In questa immagine personalizzata sono state apportate le modifiche seguenti per Database di Azure per MySQL:

* [Aggiunge il file di certificato Baltimore Cyber Trust Root per SSL a MySQL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [Usa l'impostazione app per il certificato dell'autorità di certificazione SSL per MySQL nel file wp-config.php di WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [Aggiunge la definizione WordPress per MYSQL_CLIENT_FLAGS necessaria per SSL in MySQL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

Per Redis (da usare in una sezione successiva) sono state apportate le modifiche seguenti:

* [Aggiunge l'estensione PHP per Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Aggiunge l'utilità di decompressione necessaria per l'estrazione dei file.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Aggiunge il plug-in WordPress Redis Object Cache 1.3.8.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Usa l'impostazione app per il nome host Redis nel file wp-config.php di WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Per usare l'immagine personalizzata è necessario aggiornare il file docker-compose-wordpress.yml. In Cloud Shell digitare `nano docker-compose-wordpress.yml` per aprire l'editor di testo Nano. Modificare `image: wordpress` in modo da usare `image: mcr.microsoft.com/azuredocs/multicontainerwordpress`. Il contenitore del database non è più necessario. Rimuovere le sezioni `db`, `environment`, `depends_on` e `volumes` dal file di configurazione. Il file deve essere simile al codice seguente:

```yaml
version: '3.3'

services:
   wordpress:
     image: mcr.microsoft.com/azuredocs/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

Salvare le modifiche e uscire da Nano. Usare il comando `^O` per salvare e `^X` per uscire.

### <a name="update-app-with-new-configuration"></a>Aggiornare l'app con la nuova configurazione

In Cloud Shell riconfigurare l'[app Web](overview.md) multi-contenitore con il comando [az webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set). Non dimenticare di sostituire _\<app-name>_ con il nome dell'app Web creata in precedenza.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Al termine della riconfigurazione dell'app, Cloud Shell visualizza informazioni simili all'esempio seguente:

<pre>
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Passare all'app

Passare all'app distribuita all'indirizzo `http://<app-name>.azurewebsites.net`. Ora l'app usa Database di Azure per MySQL.

![App multi-contenitore di esempio in App Web per contenitori][1]

## <a name="add-persistent-storage"></a>Aggiungere l'archiviazione permanente

L'app multi-contenitore è ora in esecuzione in App Web per contenitori. Tuttavia, se si installa WordPress adesso e si riavvia l'app in un secondo momento, si scoprirà che l'installazione di WordPress non è più presente. Questo accade perché la configurazione Docker Compose attualmente punta a un percorso di archiviazione all'interno del contenitore. I file installati nel contenitore non vengono mantenuti dopo il riavvio dell'app. In questa sezione si [aggiungerà l'archiviazione permanente](configure-custom-container.md#use-persistent-shared-storage) al contenitore WordPress.

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Per usare l'archiviazione permanente, è necessario abilitare questa impostazione all'interno del servizio app. Per apportare questa modifica, usare il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) in Cloud Shell. Le impostazioni delle app applicano la distinzione tra maiuscole e minuscole e sono separate da spazi.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Al termine della creazione delle impostazioni app, Cloud Shell visualizza informazioni simili all'esempio seguente:

<pre>
[
  &lt; JSON data removed for brevity. &gt;
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
</pre>

### <a name="modify-configuration-file"></a>Modificare il file di configurazione

In Cloud Shell digitare `nano docker-compose-wordpress.yml` per aprire l'editor di testo Nano.

L'opzione `volumes` esegue il mapping del file system a una directory all'interno del contenitore. `${WEBAPP_STORAGE_HOME}` è una variabile di ambiente nel servizio app mappata all'archiviazione permanente per l'app. Si userà questa variabile di ambiente nell'opzione volumes, in modo che i file di WordPress vengano installati nell'archiviazione permanente anziché nel contenitore. Apportare al file le modifiche seguenti:

Nella sezione `wordpress` aggiungere un'opzione `volumes` in modo che rispecchi il codice seguente:

```yaml
version: '3.3'

services:
   wordpress:
     image: mcr.microsoft.com/azuredocs/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>Aggiornare l'app con la nuova configurazione

In Cloud Shell riconfigurare l'[app Web](overview.md) multi-contenitore con il comando [az webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set). Non dimenticare di sostituire _\<app-name>_ con un nome univoco dell'app.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

Dopo l'esecuzione del comando, l'output restituito è simile all'esempio seguente:

<pre>
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Passare all'app

Passare all'app distribuita all'indirizzo `http://<app-name>.azurewebsites.net`.

Ora il contenitore WordPress usa Database di Azure per MySQL e l'archiviazione permanente.

## <a name="add-redis-container"></a>Aggiungere il contenitore Redis

 L'immagine ufficiale di WordPress non include le dipendenze per Redis. Queste dipendenze e la configurazione aggiuntiva necessaria per l'uso di Redis con WordPress sono state preparate per comodità degli utenti in questa [immagine personalizzata](https://github.com/Azure-Samples/multicontainerwordpress). Nella pratica, si aggiungeranno le modifiche desiderate alla propria immagine.

L'immagine personalizzata si basa sull'immagine ufficiale di [WordPress dall'hub Docker](https://hub.docker.com/_/wordpress/). In questa immagine personalizzata sono state apportate le modifiche seguenti per Redis:

* [Aggiunge l'estensione PHP per Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Aggiunge l'utilità di decompressione necessaria per l'estrazione dei file.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Aggiunge il plug-in WordPress Redis Object Cache 1.3.8.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Usa l'impostazione app per il nome host Redis nel file wp-config.php di WordPress.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Aggiungere il contenitore Redis alla fine del file di configurazione in modo che risulti simile all'esempio seguente:

```yaml
version: '3.3'

services:
   wordpress:
     image: mcr.microsoft.com/azuredocs/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always

   redis:
     image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
     environment: 
      - ALLOW_EMPTY_PASSWORD=yes
     restart: always
```

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Per usare Redis è necessario abilitare l'impostazione `WP_REDIS_HOST` all'interno del servizio app. Si tratta di un'*impostazione necessaria* perché WordPress possa comunicare con l'host Redis. Per apportare questa modifica, usare il comando [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) in Cloud Shell. Le impostazioni delle app applicano la distinzione tra maiuscole e minuscole e sono separate da spazi.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WP_REDIS_HOST="redis"
```

Al termine della creazione delle impostazioni app, Cloud Shell visualizza informazioni simili all'esempio seguente:

<pre>
[
  &lt; JSON data removed for brevity. &gt;
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@&lt;mysql-server-name&gt;"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
</pre>

### <a name="update-app-with-new-configuration"></a>Aggiornare l'app con la nuova configurazione

In Cloud Shell riconfigurare l'[app Web](overview.md) multi-contenitore con il comando [az webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set). Non dimenticare di sostituire _\<app-name>_ con un nome univoco dell'app.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Dopo l'esecuzione del comando, l'output restituito è simile all'esempio seguente:

<pre>
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
</pre>

### <a name="browse-to-the-app"></a>Passare all'app

Passare all'app distribuita all'indirizzo `http://<app-name>.azurewebsites.net`.

Completare i passaggi e installare WordPress.

### <a name="connect-wordpress-to-redis"></a>Connettere WordPress a Redis

Accedere all'interfaccia di amministrazione di WordPress. Nel riquadro di spostamento a sinistra, selezionare **Plugin** (Plug-in), quindi selezionare **Installed Plugins** (Plug-in installati).

![Selezionare i plug-in di WordPress][2]

Tutti i plug-in vengono visualizzati qui

Nella pagina dei plug-in individuare **Redis Object Cache** e fare clic su **Activate** (Attiva).

![Attivare Redis][3]

Fare clic su **Settings** (Impostazioni).

![Fare clic su Settings (Impostazioni)][4]

Fare clic sul pulsante **Enable Object Cache** (Abilita cache oggetti).

![Fare clic sul pulsante Enable Object Cache (Abilita cache oggetti)][5]

WordPress si connette al server Redis. Lo **stato** della connessione viene visualizzato nella stessa pagina.

![WordPress si connette al server Redis. Lo **stato** della connessione viene visualizzato nella stessa pagina.][6]

WordPress è ora **connesso** a Redis. A questo punto, l'app pronta per la produzione usa **Database di Azure per MySQL, l'archiviazione permanente e Redis**. È ora possibile aumentare il numero di istanze per il piano di servizio app.

## <a name="find-docker-container-logs"></a>Trovare i log dei contenitori Docker

Se si verificano problemi relativi all'uso di più contenitori, è possibile accedere ai log dei contenitori passando a: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

L'output sarà simile all'esempio seguente:

<pre>
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://&lt;app-name&gt;.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
</pre>

Saranno disponibili un log per ogni contenitore e un log aggiuntivo per il processo padre. Copiare il valore `href` corrispondente nel browser per visualizzare il log.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:
> [!div class="checklist"]
> * Convertire una configurazione Docker Compose per l'uso con App Web per contenitori
> * Distribuire un'app multi-contenitore in Azure
> * Aggiungere le impostazioni applicazione
> * Usare l'archiviazione permanente per i contenitori
> * Connettersi a Database di Azure per MySQL
> * Risolvere gli errori

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](app-service-web-tutorial-custom-domain.md)

In alternativa, consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare il contenitore personalizzato](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
