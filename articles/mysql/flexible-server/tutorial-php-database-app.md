---
title: "Esercitazione: Creare un'app PHP (Laravel) con il server flessibile di Database di Azure per MySQL"
description: Questa esercitazione illustra come creare un'app PHP con un server flessibile.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: tutorial
ms.devlang: php
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 93e605cb20d593750100ec8e340a7ad74c4dd385
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587894"
---
# <a name="tutorial-build-a-php-laravel-and-mysql-flexible-server-preview-app-in-azure-app-service"></a>Esercitazione: Creare un'app PHP (Laravel) e un'app del server flessibile MySQL (anteprima) nel servizio app di Azure


:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="App Web PHP in Azure con server flessibile":::

Il [Servizio app di Azure](../../app-service/overview.md) offre un servizio di hosting Web con scalabilità elevata e applicazione automatica di patch basato sul sistema operativo Linux. Questa esercitazione illustra come creare un'app PHP in Azure e connetterla a un database MySQL. Al termine, sarà disponibile un'app [Laravel](https://laravel.com/) in esecuzione nel Servizio app di Azure in Linux.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Configurare un'app PHP (Laravel) con MySQL locale
> * Creare un server flessibile MySQL (anteprima)
> * Connettere un'app PHP al server flessibile MySQL (anteprima)
> * Distribuire l'app nel servizio app di Azure
> * Aggiornare il modello di dati e ridistribuire l'app
> * Gestire l'app nel portale di Azure

Se non si ha una [sottoscrizione di Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), creare un [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

1. [Installare Git](https://git-scm.com/)
2. [Installare PHP 5.6.4 o versione successiva](https://php.net/downloads.php)
3. [Installare Composer](https://getcomposer.org/doc/00-intro.md)
4. Abilitare le seguenti estensioni PHP richieste da Laravel: OpenSSL, PDO-MySQL, Mbstring, Tokenizer, XML
5. [Installare e avviare MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)

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

Nella finestra del terminale passare a una directory vuota in cui è possibile clonare l'applicazione di esempio.  Eseguire il comando seguente per clonare l'archivio di esempio.

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

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="PHP si connette correttamente a MySQL":::

Per arrestare PHP, digitare `Ctrl + C` nel terminale.

## <a name="create-a-mysql-flexible-server-preview"></a>Creare un server flessibile MySQL (anteprima)
In questo passaggio viene creato un database MySQL nel [server flessibile di Database di Azure per MySQL](../index.yml), disponibile in anteprima pubblica. Successivamente viene configurata l'applicazione PHP per la connessione al database. In [Azure Cloud Shell](../../cloud-shell/overview.md) creare un server con il comando [`az flexible-server create`](/cli/azure/mysql/server#az-mysql-flexible-server-create).

```azurecli-interactive
az mysql flexible-server create  --resource-group myResourceGroup --public-access <IP-Address>
```

> [!IMPORTANT]
> - Prendere nota dei valori di **nome server** e **stringa di connessione**, che verranno usati nel passaggio successivo per connettersi ed eseguire la migrazione dei dati di Laravel.
> - Per l'argomento **IP-Address**, specificare l'indirizzo IP del computer client. Il server è bloccato quando viene creato ed è necessario consentire l'accesso al computer client per gestire il server in locale.

### <a name="configure-server-firewall-to-allow-web-app-to-connect-to-the-server"></a>Configurare il firewall del server per consentire all'app Web di connettersi

In Cloud Shell creare una regola del firewall per il server MySQL per consentire le connessioni client tramite il comando az mysql server firewall-rule create. Quando l'indirizzo IP iniziale e l'indirizzo IP finale sono impostati su ```0.0.0.0```, il firewall viene aperto solo per altri servizi di Azure che non hanno un indirizzo IP statico per la connessione al server.

```azurecli
az mysql flexible-server firewall-rule create --name allanyAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

### <a name="connect-to-production-mysql-server-locally"></a>Connettersi al server MySQL di produzione in locale

Nella finestra del terminale locale connettersi al server MySQL in Azure. Usare i valori specificati precedentemente per ```<admin-user>``` e ```<mysql-server-name>```. Quando viene richiesta una password, usare la password specificata al momento della creazione del database in Azure.

```bash
mysql -u <admin-user> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-production-database"></a>Creare un database di produzione

Al prompt `mysql` creare un database.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Creare un utente con autorizzazioni

Creare un utente di database denominato _phpappuser_ e assegnare all'utente tutti i privilegi per il database `sampledb`. Per semplicità, nell'esercitazione viene usata la password _MySQLAzure2020_.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2020';
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

Chiudere la connessione al server digitando `quit`.

```sql
quit
```

## <a name="connect-app-to-mysql-flexible-server"></a>Connettere l'app al server flessibile MySQL

In questo passaggio si connette l'applicazione PHP al database MySQL creato in Database di Azure per MySQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Configurare la connessione al database

Nella radice del repository creare un file _.env.production_ e copiare le variabili seguenti nel file. Sostituire il segnaposto _&lt;mysql-server-name>_ in *DB_HOST*  e *DB_USERNAME*.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql-server-name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

Salvare le modifiche.

> [!TIP]
> Per proteggere le informazioni di connessione MySQL, il file è già escluso dal repository Git (vedere _.gitignore_ nella radice del repository). Successivamente verrà illustrato come configurare le variabili di ambiente nel servizio app per la connessione al database in Database di Azure per MySQL. Con le variabili di ambiente non è necessario il file *.env* nel servizio app.
>

### <a name="configure-tlsssl-certificate"></a>Configurare il certificato TLS/SSL

Per impostazione predefinita, il server flessibile MySQL impone connessioni TLS dai client. Per la connessione al database MySQL in Azure, è necessario usare il [certificato con estensione _pem_ fornito dal server flessibile di Database di Azure per MySQL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem). Scaricare [questo certificato](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) e inserirlo nella cartella **SSL** nella copia locale del repository delle app di esempio.

Aprire _config/database.php_ e aggiungere i parametri `sslmode` e `options` a `connections.mysql`, come illustrato nel codice seguente.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL') && extension_loaded('pdo_mysql')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/DigiCertGlobalRootCA.crt.pem',
    ] : []
],
```

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

:::image type="content" source="media/tutorial-php-database-app/mysql-connect-success.png" alt-text="Connessione di PHP a Database di Azure per MySQL":::

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

FTP e l'istanza Git locale possono essere usati per la distribuzione in un'app Web tramite un utente della distribuzione. Dopo aver configurato l'utente della distribuzione, è possibile usarlo per tutte le distribuzioni di Azure. Il nome utente e la password della distribuzione a livello di account sono diversi dalle credenziali della sottoscrizione di Azure.

Per configurare l'utente della distribuzione, eseguire il comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) in Azure Cloud Shell. Sostituire _&lt;username>_ e _&lt;password>_ con il nome utente e la password della distribuzione.

Il nome utente deve essere univoco in Azure e per i push Git locali non deve contenere il simbolo "\@".
La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli.

```bash
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

L'output JSON mostra la password come Null. Se si riceve l'errore 'Conflitto'. Dettagli: 409, cambiare il nome utente. Se si riceve l'errore 'Richiesta non valida'. Dettagli: 400, usare una password più complessa. **Registrare il nome utente e la password da usare per distribuire le app Web.**

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

In Cloud Shell creare un piano di servizio app nel gruppo di risorse con il comando [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create). L'esempio seguente crea un piano di servizio app denominato myAppServicePlan nel piano tariffario gratuito (--sku F1) e in un contenitore Linux (--is-linux).

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux

<a name="create"></a>

### <a name="create-a-web-app"></a>Creare un'app Web

Creare un'[app Web](../../app-service/overview.md#app-service-on-linux) nel piano di servizio app myAppServicePlan.

In Cloud Shell è possibile usare il comando [az webapp create](/cli/azure/webapp#az-webapp-create). Nell'esempio seguente sostituire _&lt;app-name>_ con un nome di app univoco globale. I caratteri validi sono `a-z`, `0-9` e `-`. Il runtime è impostato su `PHP|7.0`. Per visualizzare tutti i runtime supportati, eseguire [az webapp list-runtimes --linux](/cli/azure/webapp#az-webapp-list-runtimes).

```bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
```

Dopo la creazione dell'app Web, l'interfaccia della riga di comando di Azure mostra un output simile all'esempio seguente:

```
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

È stata creata una nuova app Web vuota, con la distribuzione Git abilitata.

> [!NOTE]
> L'URL remoto di Git viene visualizzato nella proprietà deploymentLocalGitUrl, con il formato https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git. Salvare questo URL, perché è necessario in un secondo momento.

### <a name="configure-database-settings"></a>Configurare le impostazioni del database

Nel servizio app, le variabili di ambiente vengono configurate come _impostazioni dell'app_ usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

Il comando seguente configura le impostazioni dell'app `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` e `DB_PASSWORD`. Sostituire i segnaposto _&lt;appname>_ e _&lt;mysql-server-name>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DB_HOST="<mysql-server-name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
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

In Cloud Shell impostare la chiave applicazione nell'app del servizio app usando il comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set). Sostituire i segnaposto _&lt;appname>_ e _&lt;outputofphpartisankey:generate>_ .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"` indica a Laravel di restituire le informazioni di debug quando l'app distribuita rileva errori. Quando si esegue un'applicazione di produzione, è consigliabile impostarla su `false`.

### <a name="set-the-virtual-application-path"></a>Impostare il percorso virtuale dell'applicazione

Il [ciclo di vita dell'applicazione Laravel](https://laravel.com/docs/5.4/lifecycle) ha inizio nella directory _public_ anziché nella directory radice dell'applicazione. L'immagine Docker PHP predefinita per il servizio app usa Apache e non consente di personalizzare `DocumentRoot` per Laravel. È tuttavia possibile usare `.htaccess` per riscrivere tutte le richieste in modo da puntare a _/public_ anziché alla directory radice. Nella radice del repository è già stato aggiunto un elemento `.htaccess` a tale scopo. In questo modo, l'applicazione Laravel è pronta per essere distribuita.

Per altre informazioni, vedere [Modificare la radice del sito](../../app-service/configure-language-php.md?pivots=platform-linux#change-site-root).

### <a name="push-to-azure-from-git"></a>Effettuare il push in Azure da Git

Nella finestra del terminale locale aggiungere un'istanza remota di Azure al repository Git locale. Sostituire _&lt;deploymentLocalGitUrl-from-create-step>_ con l'URL del Git remoto salvato in [Creare un'app Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando Git Credential Manager richiede le credenziali, assicurarsi di immettere quelle create in **Configurare un utente della distribuzione**, non quelle usate per accedere al portale di Azure.

```bash
git push azure main
```

L'esecuzione del comando può richiedere alcuni minuti. Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:

<pre>
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
&lt; Output has been truncated for readability &gt;
</pre>

### <a name="browse-to-the-azure-app"></a>Passare all'app Azure

Passare a `http://<app-name>.azurewebsites.net` e aggiungere alcune attività all'elenco.

:::image type="content" source="media/tutorial-php-database-app/php-mysql-in-azure.png" alt-text="App Web PHP in Azure":::

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

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox.png" alt-text="Casella di controllo aggiuntiva all'attività":::

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
git push azure main
```

Dopo aver completato `git push`, passare all'app Azure e testare la nuova funzionalità.

:::image type="content" source="media/tutorial-php-database-app/complete-checkbox-published.png" alt-text="Modifiche al modello e al database pubblicate in Azure":::

Le attività aggiunte vengono mantenute nel database. Gli aggiornamenti allo schema di dati non modificano i dati esistenti.

## <a name="clean-up-resources"></a>Pulire le risorse
Nei passaggi precedenti sono state create risorse di Azure in un gruppo di risorse. Se si ritiene che queste risorse non saranno necessarie in futuro, eliminare il gruppo di risorse eseguendo questo comando in Cloud Shell:

```bash
az group delete --name myResourceGroup
```

<a name="next"></a>

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come gestire le risorse nel portale di Azure](../../azure-resource-manager/management/manage-resources-portal.md) <br/>
> [!div class="nextstepaction"]
> [Come gestire il server](how-to-manage-server-cli.md)
