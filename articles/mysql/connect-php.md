---
title: Connettersi con PHP - Database di Azure per MySQL
description: Questa guida introduttiva fornisce diversi esempi di codice PHP che è possibile usare per connettersi ai dati ed eseguire query da Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: ea1042d28102734429640c7e3c66fc6f1a70f950
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83870137"
---
# <a name="azure-database-for-mysql-use-php-to-connect-and-query-data"></a>Database di Azure per MySQL: Usare PHP per connettersi ai dati ed eseguire query sui dati
Questa guida introduttiva illustra come connettersi a un database di Azure per MySQL usando un'applicazione [PHP](https://secure.php.net/manual/intro-whatis.php). Spiega come usare le istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database. Questo argomento presuppone che si abbia familiarità con lo sviluppo con PHP, ma non con Database di Azure per MySQL.

## <a name="prerequisites"></a>Prerequisiti
Questa guida introduttiva usa le risorse create in una delle guide seguenti come punto di partenza:
- [Create an Azure Database for MySQL server using Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md) (Creare un database di Azure per il server MySQL usando il portale di Azure)
- [Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> Verificare che l'indirizzo IP da cui ci si sta connettendo sia stato aggiunto alle regole del firewall del server usando il [portale di Azure](./howto-manage-firewall-using-portal.md) o l’[interfaccia della riga di comando di Azure](./howto-manage-firewall-using-cli.md)

## <a name="install-php"></a>Installare PHP
Installare PHP nel server o creare un'[app Web](../app-service/overview.md) di Azure che includa PHP.

### <a name="macos"></a>MacOS
- Scaricare [PHP versione 7.1.4](https://secure.php.net/downloads.php).
- Installare PHP e vedere il [manuale di PHP](https://secure.php.net/manual/install.macosx.php) per le altre opzioni di configurazione.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
- Scaricare [PHP versione 7.1.4 non thread-safe (x64)](https://secure.php.net/downloads.php).
- Installare PHP e vedere il [manuale di PHP](https://secure.php.net/manual/install.unix.php) per le altre opzioni di configurazione.

### <a name="windows"></a>Windows
- Scaricare [PHP versione 7.1.4 non thread-safe (x64)](https://windows.php.net/download#php-7.1).
- Installare PHP e vedere il [manuale di PHP](https://secure.php.net/manual/install.windows.php) per le altre opzioni di configurazione.

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per MySQL. Sono necessari il nome del server completo e le credenziali di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra nel portale di Azure fare clic su **Tutte le risorse** e quindi cercare il server creato, ad esempio **mydemoserver**.
3. Fare clic sul nome del server.
4. Nel pannello **Panoramica** del server prendere nota dei valori riportati in **Nome server** e **Nome di accesso dell'amministratore server**. Se si dimentica la password, in questo pannello è anche possibile reimpostarla.
 ![Nome del server del database di Azure per MySQL](./media/connect-php/1_server-overview-name-login.png)

## <a name="connect-and-create-a-table"></a>Connettersi e creare una tabella
Usare il codice seguente per connettersi e creare una tabella usando l'istruzione SQL **CREATE TABLE**. 

Il codice usa la classe di **estensioni MySQL Improved** (mysqli) inclusa in PHP. Il codice chiama i metodi [mysqli_init](https://secure.php.net/manual/mysqli.init.php) e [mysqli_real_connect](https://secure.php.net/manual/mysqli.real-connect.php) per la connessione a MySQL. Chiama quindi il metodo [mysqli_query](https://secure.php.net/manual/mysqli.query.php) per eseguire la query. Chiama infine il metodo [mysqli_close](https://secure.php.net/manual/mysqli.close.php) per chiudere la connessione.

Sostituire i parametri host, username, password e db_name con i valori personalizzati. 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="insert-data"></a>Inserire i dati
Usare il codice seguente per connettersi e inserire i dati usando un'istruzione SQL **INSERT**.

Il codice usa la classe di **estensioni MySQL Improved** (mysqli) inclusa in PHP. Il codice usa il metodo [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) per creare un'istruzione insert preparata, quindi associa i parametri per ogni valore di colonna inserito usando il metodo [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Il codice esegue l'istruzione usando il metodo [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) e successivamente chiude l'istruzione usando il metodo [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Sostituire i parametri host, username, password e db_name con i valori personalizzati. 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## <a name="read-data"></a>Leggere i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **SELECT**.  Il codice usa la classe di **estensioni MySQL Improved** (mysqli) inclusa in PHP. Il codice usa il metodo [mysqli_query](https://secure.php.net/manual/mysqli.query.php) per eseguire la query SQL e il metodo [mysqli_fetch_assoc](https://secure.php.net/manual/mysqli-result.fetch-assoc.php) per recuperare le righe risultanti.

Sostituire i parametri host, username, password e db_name con i valori personalizzati. 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="update-data"></a>Aggiornare i dati
Usare il codice seguente per connettersi e aggiornare i dati usando un'istruzione SQL **UPDATE**.

Il codice usa la classe di **estensioni MySQL Improved** (mysqli) inclusa in PHP. Il codice usa il metodo [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) per creare un'istruzione update preparata, quindi associa i parametri per ogni valore di colonna aggiornato usando il metodo [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Il codice esegue l'istruzione usando il metodo [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) e successivamente chiude l'istruzione usando il metodo [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Sostituire i parametri host, username, password e db_name con i valori personalizzati. 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## <a name="delete-data"></a>Eliminare i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **DELETE**. 

Il codice usa la classe di **estensioni MySQL Improved** (mysqli) inclusa in PHP. Il codice usa il metodo [mysqli_prepare](https://secure.php.net/manual/mysqli.prepare.php) per creare un'istruzione delete preparata, quindi associa i parametri per la clausola where nell'istruzione usando il metodo [mysqli_stmt_bind_param](https://secure.php.net/manual/mysqli-stmt.bind-param.php). Il codice esegue l'istruzione usando il metodo [mysqli_stmt_execute](https://secure.php.net/manual/mysqli-stmt.execute.php) e successivamente chiude l'istruzione usando il metodo [mysqli_stmt_close](https://secure.php.net/manual/mysqli-stmt.close.php).

Sostituire i parametri host, username, password e db_name con i valori personalizzati. 

```php
<?php
$host = 'mydemoserver.mysql.database.azure.com';
$username = 'myadmin@mydemoserver';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Connettersi a Database di Azure per MySQL tramite SSL](howto-configure-ssl.md)
