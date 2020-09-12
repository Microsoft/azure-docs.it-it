---
title: Configurare un contenitore Linux personalizzato
description: Informazioni su come configurare un contenitore Linux personalizzato nel servizio app Azure. Questo articolo illustra le attività di configurazione più comuni.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9a27abe5457cf8adf2963db545c629134ae53709
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566977"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Configurare un contenitore Linux personalizzato per il servizio app Azure

Questo articolo illustra come configurare un contenitore Linux personalizzato da eseguire nel servizio app Azure.

Questa guida fornisce concetti chiave e istruzioni per la contenitori di app Linux nel servizio app. Se non si è mai usato app Azure servizio, seguire prima la [Guida introduttiva ai contenitori personalizzati](quickstart-custom-container.md?pivots=container-linux) e l' [esercitazione](tutorial-custom-container.md?pivots=container-linux) . Sono inoltre disponibili un' [esercitazione](tutorial-multi-container-app.md)e una [Guida introduttiva per l'app multicontenitore](quickstart-multi-container.md) .

## <a name="configure-port-number"></a>Configurare il numero di porta

Per impostazione predefinita, il servizio app presuppone che il contenitore personalizzato sia in ascolto sulla porta 80. Il server Web nell'immagine personalizzata può usare una porta diversa da 80. Si indica ad Azure la porta utilizzata dal contenitore personalizzato usando l'impostazione dell' `WEBSITES_PORT` app. La pagina di GitHub per l'[esempio di Python in questa esercitazione](https://github.com/Azure-Samples/docker-django-webapp-linux) indica che è necessario impostare `WEBSITES_PORT` su _8000_. È possibile impostarlo eseguendo [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) il comando nella cloud Shell. Ad esempio:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Il contenitore personalizzato può usare variabili di ambiente che devono essere fornite esternamente. È possibile passarli in eseguendo [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) il comando nella cloud Shell. Ad esempio:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Questo metodo funziona sia per le app a contenitore singolo che per le app a più contenitori, in cui le variabili di ambiente sono specificate nel file *Docker-compose. yml* .

## <a name="use-persistent-shared-storage"></a>Usa archiviazione condivisa persistente

È possibile usare la directory */Home* nell'file System dell'app per salvare in modo permanente i file tra i riavvii e condividerli tra le istanze. Il `/home` nell'app viene fornito per consentire all'app contenitore di accedere a un archivio permanente.

Quando l'archiviazione persistente è disabilitata, le Scritture nella `/home` Directory non vengono rese persistenti tra i riavvii dell'app o tra più istanze. L'unica eccezione è la `/home/LogFiles` Directory, che viene usata per archiviare i log di Docker e del contenitore. Quando è abilitata l'archiviazione persistente, tutte le Scritture nella `/home` Directory sono persistenti ed è possibile accedervi da tutte le istanze di un'app con scalabilità orizzontale.

Per impostazione predefinita, l'archiviazione persistente è *abilitata* e l'impostazione non è esposta nelle impostazioni dell'applicazione. Per disabilitarlo, impostare l' `WEBSITES_ENABLE_APP_SERVICE_STORAGE` impostazione dell'app eseguendo il [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) comando nella cloud Shell. Ad esempio:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> È anche possibile [configurare una risorsa di archiviazione permanente](configure-connect-to-azure-storage.md?pivots=platform-linux).

## <a name="enable-ssh"></a>Abilitare SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Per consentire a un contenitore personalizzato di supportare SSH, è necessario aggiungerlo alla Dockerfile stessa.

> [!TIP]
> Tutti i contenitori Linux predefiniti hanno aggiunto le istruzioni SSH nei repository di immagini. È possibile seguire le istruzioni seguenti con il [ repositoryNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) per vedere come è abilitato.

- Usare l'istruzione [Run](https://docs.docker.com/engine/reference/builder/#run) per installare il server SSH e impostare la password per l'account radice su `"Docker!"` . Per un'immagine basata su [Alpine Linux](https://hub.docker.com/_/alpine), ad esempio, sono necessari i comandi seguenti:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Questa configurazione non consente connessioni esterne al contenitore. SSH è disponibile solo tramite `https://<app-name>.scm.azurewebsites.net` e autenticato con le credenziali di pubblicazione.

- Aggiungere [questo file di sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) all'archivio immagini e usare l'istruzione [Copy](https://docs.docker.com/engine/reference/builder/#copy) per copiare il file nella directory *nella/etc/ssh/* Per ulteriori informazioni sui file di *sshd_config* , vedere la [documentazione di OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Il file *sshd_config* deve includere gli elementi seguenti:
    > - `Ciphers` deve includere almeno un elemento di questo elenco: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - `MACs` deve includere almeno un elemento di questo elenco: `hmac-sha1,hmac-sha1-96`.

- Usare l'istruzione [Expose](https://docs.docker.com/engine/reference/builder/#expose) per aprire la porta 2222 nel contenitore. Sebbene la password radice sia nota, la porta 2222 non è accessibile da Internet. È accessibile solo dai contenitori all'interno della rete Bridge di una rete virtuale privata.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- Nello script di avvio per il contenitore avviare il server SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Per un esempio, vedere come il [ contenitore predefinitoNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) avvia il server SSH.

## <a name="access-diagnostic-logs"></a>Accedere ai log di diagnostica

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="change-the-docker-image-of-a-custom-container"></a>Modificare l'immagine Docker di un contenitore personalizzato

Per modificare un'app contenitore personalizzata esistente dall'immagine Docker corrente a una nuova immagine, usare il comando seguente:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Usare un'immagine da un registro privato

Per usare un'immagine da un registro privato, ad esempio Azure Container Registry, eseguire il comando seguente:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

Per *\<username>* e *\<password>* , specificare le credenziali di accesso per l'account del registro di sistema privato.

## <a name="configure-multi-container-apps"></a>Configurare app multicontenitore

- [Usare l'archiviazione persistente in Docker Compose](#use-persistent-storage-in-docker-compose)
- [Limitazioni di anteprima](#preview-limitations)
- [Opzioni di Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Usare l'archiviazione persistente in Docker Compose

Per il corretto funzionamento di app a più contenitori come WordPress è necessario disporre di un archivio permanente. Per abilitarla, la configurazione del Docker Compose deve puntare a una posizione di archiviazione *all'esterno* del contenitore. Le posizioni di archiviazione all'interno del contenitore non rendono permanente le modifiche oltre il riavvio dell'app.

Abilitare l'archiviazione permanente impostando l' `WEBSITES_ENABLE_APP_SERVICE_STORAGE` impostazione dell'app usando il comando [AZ webapp config appSettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) in cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

Nel file *Docker-compose. yml* mappare l' `volumes` opzione a `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME` è una variabile di ambiente nel servizio app mappata all'archiviazione permanente per l'app. Ad esempio:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Limiti di anteprima

Il multicontenitore è attualmente in fase di anteprima. Le funzionalità della piattaforma del servizio app seguenti non sono supportate:

- Autenticazione/Autorizzazione
- Identità gestite
- CORS

### <a name="docker-compose-options"></a>Opzioni di Docker Compose

Negli elenchi seguenti sono illustrate le opzioni di configurazione Docker Compose supportate e non supportate:

#### <a name="supported-options"></a>Opzioni supportate

- .
- entrypoint
- ambiente
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Opzioni non supportate

- build (non consentita)
- depends_on (ignorata)
- networks (ignorata)
- secrets (ignorata)
- porte diverse da 80 e 8080 (ignorate)

> [!NOTE]
> Eventuali altre opzioni non esplicitamente richiamate verranno ignorate in anteprima pubblica.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire la distribuzione da un repository di contenitore privato](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [Esercitazione: App WordPress multicontenitore](tutorial-multi-container-app.md)
