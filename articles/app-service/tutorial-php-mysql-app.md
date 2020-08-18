---
title: 'Esercitazione: App PHP con MySQL'
description: Informazioni su come ottenere un'app PHP che è possibile usare in Azure con connessione a un database MySQL in Azure. Laravel verrà usato nell'esercitazione.
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.devlang: php
ms.topic: tutorial
ms.date: 06/15/2020
ms.custom: mvc, cli-validate, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 68aaf431f7cca0366b7d77d320357d8ceb525933
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88082571"
---
# <a name="tutorial-build-a-php-and-mysql-app-in-azure-app-service"></a>Esercitazione: Compilare un'app PHP e MySQL nel Servizio app di Azure

::: zone pivot="platform-windows"  

Il [Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e applicazione automatica di patch basato sul sistema operativo Windows. Questa esercitazione illustra come creare un'app PHP in Azure e connetterla a un database MySQL. Al termine, sarà disponibile un'app [Laravel](https://laravel.com/) in esecuzione nel Servizio app di Azure in Windows.

::: zone-end

::: zone pivot="platform-linux"

Il [Servizio app di Azure](overview.md) offre un servizio di hosting Web con scalabilità elevata e applicazione automatica di patch basato sul sistema operativo Linux. Questa esercitazione illustra come creare un'app PHP in Azure e connetterla a un database MySQL. Al termine, sarà disponibile un'app [Laravel](https://laravel.com/) in esecuzione nel Servizio app di Azure in Linux.

::: zone-end

![App PHP in esecuzione nel Servizio app di Azure](./media/tutorial-php-mysql-app/complete-checkbox-published.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un database MySQL in Azure
> * Connettere un'app PHP a MySQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* [Installare Git](https://git-scm.com/)
* [Installare PHP 5.6.4 o versione successiva](https://php.net/downloads.php)
* [Installare Composer](https://getcomposer.org/doc/00-intro.md)
* Abilitare le seguenti estensioni PHP richieste da Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
* [Installare e avviare MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>Preparare MySQL in locale

In questo passaggio, si crea un database nel server locale di MySQL da usare in questa esercitazione.

### <a name="connect-to-local-mysql-server"></a>Connettersi al server MySQL locale

In una finestra terminale connettersi al server MySQL locale. È possibile usare questa finestra del terminale per eseguire tutti i comandi presenti in questa esercitazione.

```bash
mysql -u root -p
```

Se viene richiesto di immettere una password, immettere la password per l'account `root`. Se non si ricorda la password dell'account radice, vedere [MySQL: come reimpostare la password radice](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

Se il comando viene eseguito correttamente, il server MySQL è in esecuzione. In caso contrario, assicurarsi che il server MySQL locale sia stato avviato seguendo la [procedura successiva all'installazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### <a name="create-a-database-locally"></a>Creare un database in locale

Al prompt `mysql` creare un database.

```sql 
CREATE DATABASE sampledb;
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>Creare un'app PHP in locale
In questo passaggio si ottiene un'applicazione di esempio Laravel, se ne configurare la connessione al database e la si esegue in locale. 

### <a name="clone-the-sample"></a>Clonare l'esempio

Nella finestra del terminale usare il comando `cd` per passare a una directory di lavoro.

Eseguire il comando seguente per clonare l'archivio di esempio.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

Eseguire `cd` nella directory clonata.
Installare i pacchetti necessari.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>Configurare la connessione di MySQL

Nella radice del repository creare un file denominato *.env*. Copiare le variabili seguenti nel file *.env*. Sostituire il segnaposto _&lt;root_password>_ con la password dell'utente ROOT MySQL.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Per informazioni su come viene usato il file _.env_ da Laravel, vedere [Laravel Environment Configuration](https://laravel.com/docs/5.4/configuration#environment-configuration) (Configurazione dell'ambiente Laravel).

### <a name="run-the-sample-locally"></a>Eseguire l'esempio in locale

Eseguire le [migrazioni del database di Laravel](https://laravel.com/docs/5.4/migrations) per creare le tabelle necessarie all'applicazione. Per visualizzare le tabelle create nelle migrazioni, esaminare la directory _database/migrations_ nel repository Git.

```bash
php artisan migrate
```

Generare una nuova chiave per l'applicazione Laravel.

```bash
php artisan key:generate
```

Eseguire l'applicazione.

```bash
php artisan serve
```

Andare a `http://localhost:8000` in un browser. Nella pagina aggiungere alcune attività.

![PHP si connette correttamente a MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Per arrestare PHP, digitare `Ctrl + C` nel terminale.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Creare MySQL in Azure

In questo passaggio viene creato un database MySQL in [Database di Azure per MySQL](/azure/mysql). Successivamente viene configurata l'applicazione PHP per la connessione al database.

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>Creare un server MySQL

In Cloud Shell creare un server in Database di Azure per MySQL con il comando [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create).

Nel comando seguente sostituire con un nome di server univoco il segnaposto *\<mysql-server-name>* , con un nome utente il segnaposto *\<admin-user>* e con una password il segnaposto *\<admin-password>* . Poiché il nome del server viene usato come parte dell'endpoint MySQL (`https://<mysql-server-name>.mysql.database.azure.com`), il nome deve essere univoco in tutti i server in Azure. Per informazioni dettagliate sulla selezione dello SKU del database per MySQL, vedere [Creare un database di Azure per il server MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-cli#create-an-azure-database-for-mysql-server).

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name> --location "West Europe" --admin-user <admin-user> --admin-password <admin-password> --sku-name B_Gen5_1
```

Al termine della creazione del server MySQL, l'interfaccia della riga di comando di Azure mostra informazioni simili all'esempio seguente:

<pre>
{
  "administratorLogin": "&lt;admin-user&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;mysql-server-name&gt;.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/&lt;mysql-server-name&gt;",
  "location": "westeurope",
  "name": "&lt;mysql-server-name&gt;",
  "resourceGroup": "myResourceGroup",
  ...
  -  &lt; Output has been truncated for readability &gt;
}
</pre>

### <a name="configure-server-firewall"></a>Configurare il firewall del server

In Cloud Shell creare una regola del firewall per il server MySQL per consentire le connessioni client usando il comando [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create). Quando sia l'IP iniziale che l'IP finale sono impostati su 0.0.0.0, il firewall viene aperto solo per le altre risorse di Azure. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> È possibile rendere ancora più restrittiva la regola del firewall [usando solo gli indirizzi IP in uscita usati dall'app](overview-inbound-outbound-ips.md#find-outbound-ips).
>

In Cloud Shell eseguire di nuovo il comando per consentire l'accesso dal computer locale sostituendo *\<your-ip-address>* con l'[indirizzo IPv4 locale](https://www.whatsmyip.org/).

```azurecli-interactive
az mysql server firewall-rule create --name AllowLocalClient --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="connect-to-production-mysql-server-locally"></a>Connettersi al server MySQL di produzione in locale

Nella finestra del terminale locale connettersi al server MySQL in Azure. Usare il valore specificato in precedenza per _&lt;admin-user>_ e _&lt;mysql-server-name>_ . Quando viene richiesta una password, usare la password specificata al momento della creazione del database in Azure.

```bash
mysql -u <admin-user>@<mysql-server-name> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Creare un database di produzione

Al prompt `mysql` creare un database.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Creare un utente con autorizzazioni

Creare un utente di database denominato _phpappuser_ e assegnare all'utente tutti i privilegi per il database `sampledb`. Per semplificare l'esercitazione, usare _MySQLAzure2017_ come password.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Connettere l'app a MySQL di Azure

In questo passaggio si connette l'applicazione PHP al database MySQL creato in Database di Azure per MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurare la connessione al database

Nella radice del repository creare un file _.env.production_ e copiare le variabili seguenti nel file. Sostituire il segnaposto _&lt;mysql-server-name>_ in *DB_HOST* e *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql-server-name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Salvare le modifiche.

> [!TIP]
> Per proteggere le informazioni di connessione MySQL, il file è già escluso dal repository Git (vedere _.gitignore_ nella radice del repository). Successivamente verrà illustrato come configurare le variabili di ambiente nel servizio app per la connessione al database in Database di Azure per MySQL. Con le variabili di ambiente non è necessario il file *.env* nel servizio app.
>

### <a name="configure-tlsssl-certificate"></a>Configurare il certificato TLS/SSL

Per impostazione predefinita, il database di Azure per MySQL impone le connessioni TLS dai client. Per la connessione al database MySQL in Azure, è necessario usare il [certificato con estensione _pem_ fornito da Database di Azure per MySQL](../mysql/howto-configure-ssl.md).

Aprire _config/database.php_ e aggiungere i parametri `sslmode` e `options` a `connections.mysql`, come illustrato nel codice seguente.

::: zone pivot="platform-windows"  

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

::: zone-end

::: zone pivot="platform-linux"

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem',
    ] : []
],
```

::: zone-end

Per praticità, nel repository è disponibile il certificato `BaltimoreCyberTrustRoot.crt.pem` per questa esercitazione. 

### <a name="test-the-application-locally"></a>Testare l'applicazione in locale

Eseguire le migrazioni del database Laravel con _.env.production_ come file dell'ambiente per creare le tabelle nel database MySQL in Database di Azure per MySQL. Tenere presente che _. env.production_ include le informazioni di connessione al database MySQL in Azure.

```bash
php artisan migrate --env=production --force
```

_.env.production_ non dispone ancora di una chiave applicazione valida. Generarne una nuova nel terminale.

```bash
php artisan key:generate --env=production --force
```

Eseguire l'applicazione di esempio con _.env.production_ come file dell'ambiente.

```bash
php artisan serve --env=production
```

Accedere a `http://localhost:8000`. Se la pagina viene caricata senza errori, l'applicazione PHP si connette al database MySQL in Azure.

Nella pagina aggiungere alcune attività.

![Connessione di PHP a Database di Azure per MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

Per arrestare PHP, digitare `Ctrl + C` nel terminale.

### <a name="commit-your-changes"></a>Eseguire il commit delle modifiche

Eseguire i comandi Git seguenti per eseguire il commit delle modifiche:

```bash
git add .
git commit -m "database.php updates"
```

L'app è pronta per la distribuzione.

## <a name="deploy-to-azure"></a>Distribuisci in Azure

In questo passaggio viene distribuita l'applicazione PHP connessa a MySQL nel servizio app di Azure.

### <a name="configure-a-deployment-user"></a>Configurare un utente della distribuzione

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

<a name="create"></a>
### <a name="create-a-web-app"></a>Creare un'app Web

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

::: zone-end

### <a name="configure-database-settings"></a>Configurare le impostazioni del database

Nel servizio app, le variabili di ambiente vengono configurate come _impostazioni dell'app_ usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).

Il comando seguente configura le impostazioni dell'app `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Sostituire i segnaposto _&lt;appname>_ e _&lt;mysql-server-name>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql-server-name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

È possibile usare il metodo [getenv](https://www.php.net/manual/en/function.getenv.php) di PHP per accedere alle impostazioni. Il codice Laravel usa un wrapper [env](https://laravel.com/docs/5.4/helpers#method-env) su `getenv` di PHP. Ad esempio, la configurazione di MySQL in _config/database.php_ è simile al codice seguente:

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Configurare le variabili di ambiente di Laravel

Laravel richiede una chiave di applicazione nel servizio app. È possibile configurarla con le impostazioni dell'app.

Nella finestra del terminale locale usare `php artisan` per generare una nuova chiave applicazione senza salvarla in _.env_.

```bash
php artisan key:generate --show
```

In Cloud Shell impostare la chiave applicazione nell'app del servizio app usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set). Sostituire i segnaposto _&lt;appname>_ e _&lt;outputofphpartisankey:generate>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` indica a Laravel di restituire le informazioni di debug quando l'app distribuita rileva errori. Quando si esegue un'applicazione di produzione, è consigliabile impostarla su `false`.

### <a name="set-the-virtual-application-path"></a>Impostare il percorso virtuale dell'applicazione

::: zone pivot="platform-windows"  

Impostare il percorso virtuale dell'applicazione per l'app. Questo passaggio è necessario perché il [ciclo di vita dell'applicazione Laravel](https://laravel.com/docs/5.4/lifecycle) ha inizio nella directory _public_ anziché nella directory radice dell'applicazione. Gli altri framework PHP il cui ciclo di vita si avvia nella directory radice funzionano anche senza la configurazione manuale del percorso dell'applicazione virtuale.

In Cloud Shell impostare il percorso virtuale dell'applicazione con il comando [`az resource update`](/cli/azure/resource#az-resource-update). Sostituire il segnaposto _&lt;app-name>_ .

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Per impostazione predefinita, il servizio app di Azure fa in modo che il percorso virtuale dell'applicazione radice ( _/_ ) punti alla directory radice dei file dell'applicazione distribuiti (_sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

Il [ciclo di vita dell'applicazione Laravel](https://laravel.com/docs/5.4/lifecycle) ha inizio nella directory _public_ anziché nella directory radice dell'applicazione. L'immagine Docker PHP predefinita per il servizio app usa Apache e non consente di personalizzare `DocumentRoot` per Laravel. È tuttavia possibile usare `.htaccess` per riscrivere tutte le richieste in modo da puntare a _/public_ anziché alla directory radice. Nella radice del repository è già stato aggiunto un elemento `.htaccess` a tale scopo. In questo modo, l'applicazione Laravel è pronta per essere distribuita.

Per altre informazioni, vedere [Modificare la radice del sito](configure-language-php.md#change-site-root).

::: zone-end

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

::: zone pivot="platform-windows"  

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

> [!NOTE]
> Si può notare che, alla fine, il processo di distribuzione installa i pacchetti [Composer](https://getcomposer.org/). Il servizio app non esegue tali automazioni durante la distribuzione predefinita, pertanto questo repository di esempio include tre file aggiuntivi nella directory radice per abilitarlo:
>
> - `.deployment` - Questo file indica al servizio app di eseguire `bash deploy.sh` come script di distribuzione personalizzato.
> - `deploy.sh` - Script di distribuzione personalizzato. Se si esamina il file, si noterà che esegue `php composer.phar install` dopo `npm install`.
> - `composer.phar` - Gestione pacchetti Composer.
>
> È possibile usare questo approccio per aggiungere uno o più passaggi alla distribuzione basata su Git al servizio app. Per altre informazioni, vedere [Custom Deployment Script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) (Script di distribuzione personalizzata).
>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Passare all'app Azure

Passare a `http://<app-name>.azurewebsites.net` e aggiungere alcune attività all'elenco.

![App PHP in esecuzione nel Servizio app di Azure](./media/tutorial-php-mysql-app/php-mysql-in-azure.png)

L'app PHP basata su dati è in esecuzione nel servizio app di Azure.

## <a name="update-model-locally-and-redeploy"></a>Aggiornare il modello in locale e rieseguire la distribuzione

In questo passaggio viene apportata una modifica semplice al modello di dati `task` e all'app Web e quindi viene pubblicato l'aggiornamento in Azure.

Per lo scenario delle attività, l'applicazione viene modificata per poter contrassegnare un'attività come completata.

### <a name="add-a-column"></a>Aggiungere una colonna

Nella finestra del terminale locale passare alla radice del repository Git.

Generare una nuova migrazione di database per la tabella `tasks`:

```bash
php artisan make:migration add_complete_column --table=tasks
```

Questo comando mostra il nome del file di migrazione che viene generato. Trovare il file in _database/migrations_ e aprirlo.

Sostituire il metodo `up` con il codice seguente:

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

Il codice precedente aggiunge una colonna booleana nella tabella `tasks` denominata `complete`.

Sostituire il metodo `down` con il codice seguente per l'azione di ripristino dello stato precedente:

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

Nella finestra del terminale locale eseguire le migrazioni del database Laravel per apportare la modifica nel database locale.

```bash
php artisan migrate
```

In base alla [convenzione di denominazione Laravel](https://laravel.com/docs/5.4/eloquent#defining-models), il modello `Task` (vedere _app/Task.php_) esegue il mapping alla tabella `tasks` per impostazione predefinita.

### <a name="update-application-logic"></a>Aggiornare la logica dell'applicazione

Aprire il file *routes/web.php*. L'applicazione definisce qui le route e la logica di business.

Alla fine del file aggiungere una route con il codice seguente:

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

Il codice precedente esegue un semplice aggiornamento del modello di dati tramite l'attivazione e la disattivazione del valore di `complete`.

### <a name="update-the-view"></a>Aggiornare la visualizzazione

Aprire il file *resources/views/tasks.blade.php*. Trovare il tag di apertura `<tr>` e sostituirlo con:

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

Il codice precedente modifica il colore di riga in base al completamento dell'attività.

Nella riga successiva il codice è quello riportato di seguito:

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

Sostituire l'intera riga con il codice seguente:

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

Il codice precedente aggiunge il pulsante di invio che fa riferimento alla route definita in precedenza.

### <a name="test-the-changes-locally"></a>Testare le modifiche in locale

Nella finestra del terminale locale eseguire il server di sviluppo dalla directory radice del repository Git.

```bash
php artisan serve
```

Per visualizzare la modifica dello stato dell'attività, passare a `http://localhost:8000` e selezionare la casella di controllo.

![Casella di controllo aggiuntiva all'attività](./media/tutorial-php-mysql-app/complete-checkbox.png)

Per arrestare PHP, digitare `Ctrl + C` nel terminale.

### <a name="publish-changes-to-azure"></a>Pubblicare le modifiche in Azure

Nella finestra del terminale locale eseguire le migrazioni del database Laravel con la stringa di connessione di produzione per apportare la modifica nel database di Azure.

```bash
php artisan migrate --env=production --force
```

Eseguire il commit di tutte le modifiche in Git e quindi effettuare il push delle modifiche al codice in Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

Dopo aver completato `git push`, passare all'app Azure e testare la nuova funzionalità.

![Modifiche al modello e al database pubblicate in Azure](media/tutorial-php-mysql-app/complete-checkbox-published.png)

Le attività aggiunte vengono mantenute nel database. Gli aggiornamenti allo schema di dati non modificano i dati esistenti.

## <a name="stream-diagnostic-logs"></a>Eseguire lo streaming dei log di diagnostica

::: zone pivot="platform-windows"  

Mentre l'applicazione PHP è in esecuzione nel servizio app di Azure, è possibile fare in modo che i log di console siano inviati tramite pipe al terminale. Ciò consente di ottenere gli stessi messaggi di diagnostica per il debug degli errori dell'applicazione.

Per avviare lo streaming dei log, usare il comando [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) in Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

Dopo avere avviato lo streaming dei log, aggiornare l'app di Azure nel browser per ricevere traffico Web. I log di console vengono inviati tramite pipe al terminale. Se i log di console non sono immediatamente visibili, controllare nuovamente dopo 30 secondi.

Per interrompere lo streaming dei log in qualsiasi momento, digitare `Ctrl`+`C`.

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

> [!TIP]
> Un'applicazione PHP può usare lo standard [error_log()](https://php.net/manual/function.error-log.php) da inviare alla console. L'applicazione di esempio usa questo approccio in _app/Http/routes.php_.
>
> Come framework Web, [Laravel usa Monolog](https://laravel.com/docs/5.4/errors) come provider di registrazione. Per vedere come ottenere da Monolog l'output dei messaggi sulla console, vedere [PHP: Come usare Monolog per accedere alla console (php://out)](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>
>

## <a name="manage-the-azure-app"></a>Gestire l'app Azure

Accedere al [portale di Azure](https://portal.azure.com) per gestire l'app creata.

Nel menu a sinistra fare clic su **Servizi app** e quindi sul nome dell'app Azure.

![Passaggio all'app di Azure nel portale](./media/tutorial-php-mysql-app/access-portal.png)

Verrà visualizzata la pagina Panoramica dell'app. Nella pagina è possibile eseguire attività di gestione di base come l'arresto, l'avvio, il riavvio e l'eliminazione.

Il menu a sinistra consente di visualizzare le pagine di configurazione dell'app.

![Pagina del servizio app nel portale di Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un database MySQL in Azure
> * Connettere un'app PHP a MySQL
> * Distribuire l'app in Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Eseguire lo streaming dei log di diagnostica in Azure
> * Gestire l'app nel portale di Azure

Passare all'esercitazione successiva per apprendere come eseguire il mapping di un nome DNS personalizzato all'app.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il mapping di un nome DNS personalizzato all'app Web](app-service-web-tutorial-custom-domain.md)

In alternativa, consultare altre risorse:

> [!div class="nextstepaction"]
> [Configurare l'app PHP](configure-language-php.md)
