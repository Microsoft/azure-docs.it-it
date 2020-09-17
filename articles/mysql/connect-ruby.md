---
title: 'Avvio rapido: Connettersi con Ruby - Database di Azure per MySQL'
description: Questa guida introduttiva fornisce diversi esempi di codice Ruby che è possibile usare per connettersi ai dati ed eseguire query da Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.devlang: ruby
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 15bbce208475a85e7be6efbadebcb4e43c2d8d17
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90029104"
---
# <a name="quickstart-use-ruby-to-connect-and-query-data-in-azure-database-for-mysql"></a>Avvio rapido: Usare Ruby per connettersi ai dati ed eseguire query in Database di Azure per MySQL

Questa guida introduttiva illustra come connettersi a un database di Azure per MySQL usando un'applicazione [Ruby](https://www.ruby-lang.org) e Gem [mysql2](https://rubygems.org/gems/mysql2) dalle piattaforme Windows, Ubuntu Linux e Mac. Spiega come usare le istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database. Questo argomento presuppone che si abbia familiarità con lo sviluppo con Ruby, ma non con Database di Azure per MySQL.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva usa le risorse create in una delle guide seguenti come punto di partenza:
- [Create an Azure Database for MySQL server using Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md) (Creare un database di Azure per il server MySQL usando il portale di Azure)
- [Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

> [!IMPORTANT] 
> Verificare che l'indirizzo IP da cui ci si sta connettendo sia stato aggiunto alle regole del firewall del server usando il [portale di Azure](./howto-manage-firewall-using-portal.md) o l’[interfaccia della riga di comando di Azure](./howto-manage-firewall-using-cli.md)

## <a name="install-ruby"></a>Installare Ruby
Installare Ruby, Gem e la libreria MySQL2 nel computer. 

### <a name="windows"></a>Windows
1. Scaricare e installare la versione 2.3 di [Ruby](https://rubyinstaller.org/downloads/).
2. Avviare un nuovo prompt dei comandi (cmd) dal menu Start.
3. Passare alla directory di Ruby per la versione 2.3. `cd c:\Ruby23-x64\bin`
4. Verificare l'installazione di Ruby eseguendo il comando `ruby -v` per visualizzare la versione installata.
5. Verificare l'installazione di Gem eseguendo il comando `gem -v` per visualizzare la versione installata.
6. Compilare il modulo Mysql2 per Ruby con Gem eseguendo il comando `gem install mysql2`.

### <a name="macos"></a>MacOS
1. Eseguire il comando `brew install ruby` per installare Ruby usando Homebrew. Per altre opzioni di installazione, vedere la [documentazione sull'installazione](https://www.ruby-lang.org/en/documentation/installation/#homebrew) di Ruby.
2. Verificare l'installazione di Ruby eseguendo il comando `ruby -v` per visualizzare la versione installata.
3. Verificare l'installazione di Gem eseguendo il comando `gem -v` per visualizzare la versione installata.
4. Compilare il modulo Mysql2 per Ruby con Gem eseguendo il comando `gem install mysql2`.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. Installare Ruby eseguendo il comando `sudo apt-get install ruby-full`. Per altre opzioni di installazione, vedere la [documentazione sull'installazione](https://www.ruby-lang.org/en/documentation/installation/) di Ruby.
2. Verificare l'installazione di Ruby eseguendo il comando `ruby -v` per visualizzare la versione installata.
3. Installare gli aggiornamenti più recenti per Gem eseguendo il comando `sudo gem update --system`.
4. Verificare l'installazione di Gem eseguendo il comando `gem -v` per visualizzare la versione installata.
5. Installare gcc, make e altri strumenti di compilazione eseguendo il comando `sudo apt-get install build-essential`.
6. Installare le librerie di sviluppo client MySQL eseguendo il comando `sudo apt-get install libmysqlclient-dev`.
7. Compilare il modulo Mysql2 per Ruby con Gem eseguendo il comando `sudo gem install mysql2`.

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per MySQL. Sono necessari il nome del server completo e le credenziali di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Nel menu a sinistra nel portale di Azure fare clic su **Tutte le risorse** e quindi cercare il server creato, ad esempio **mydemoserver**.
3. Fare clic sul nome del server.
4. Nel pannello **Panoramica** del server prendere nota dei valori riportati in **Nome server** e **Nome di accesso dell'amministratore server**. Se si dimentica la password, in questo pannello è anche possibile reimpostarla.
 ![Nome del server del database di Azure per MySQL](./media/connect-ruby/1_server-overview-name-login.png)

## <a name="run-ruby-code"></a>Eseguire il codice Ruby 
1. Incollare in file di testo il codice Ruby riportato nelle sezioni seguenti, quindi salvare i file nella cartella del progetto con l'estensione rb, ad esempio `C:\rubymysql\createtable.rb` o `/home/username/rubymysql/createtable.rb`.
2. Per eseguire il codice, avviare il prompt dei comandi o la shell Bash. Passare alla cartella del progetto `cd rubymysql`
3. Per eseguire l'applicazione, digitare quindi il comando Ruby seguito dal nome del file, ad esempio `ruby createtable.rb`.
4. Nel sistema operativo Windows, se l'applicazione Ruby non è presente nella variabile di ambiente PATH potrebbe essere necessario usare il percorso completo per avviare l'applicazione Ruby, ad esempio `"c:\Ruby23-x64\bin\ruby.exe" createtable.rb`

## <a name="connect-and-create-a-table"></a>Connettersi e creare una tabella
Usare il codice seguente per connettersi e creare una tabella usando l'istruzione SQL **CREATE TABLE**, seguita dalle istruzioni SQL **INSERT INTO** per aggiungere righe nella tabella.

Il codice usa un metodo [mysql2::client](https://www.rubydoc.info/gems/mysql2) class .new() per la connessione ad Azure Database per MySQL. Chiama quindi il metodo [query()](https://www.rubydoc.info/gems/mysql2#Usage) più volte per eseguire i comandi DROP, CREATE TABLE e INSERT INTO. Chiama infine il metodo [close()](https://www.rubydoc.info/gems/mysql2/Mysql2/Client#close-instance_method) per chiudere la connessione prima di terminare.

Sostituire le stringhe `host`, `database`, `username` e `password` con valori personalizzati. 
```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Drop previous table of same name if one exists
    client.query('DROP TABLE IF EXISTS inventory;')
    puts 'Finished dropping table (if existed).'

    # Drop previous table of same name if one exists.
    client.query('CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);')
    puts 'Finished creating table.'

    # Insert some data into table.
    client.query("INSERT INTO inventory VALUES(1, 'banana', 150)")
    client.query("INSERT INTO inventory VALUES(2, 'orange', 154)")
    client.query("INSERT INTO inventory VALUES(3, 'apple', 100)")
    puts 'Inserted 3 rows of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="read-data"></a>Leggere i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **SELECT**. 

Il codice usa un metodo [mysql2::client](https://www.rubydoc.info/gems/mysql2) class.new() per la connessione a Database di Azure per MySQL. Chiama quindi il metodo [query()](https://www.rubydoc.info/gems/mysql2#Usage) per eseguire i comandi SELECT. Chiama infine il metodo [close()](https://www.rubydoc.info/gems/mysql2/Mysql2/Client#close-instance_method) per chiudere la connessione prima di terminare.

Sostituire le stringhe `host`, `database`, `username` e `password` con valori personalizzati. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Read data
    resultSet = client.query('SELECT * from inventory;')
    resultSet.each do |row|
        puts 'Data row = (%s, %s, %s)' % [row['id'], row['name'], row['quantity']]
    end
    puts 'Read ' + resultSet.count.to_s + ' row(s).'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="update-data"></a>Aggiornare i dati
Usare il codice seguente per connettersi e aggiornare i dati usando un'istruzione SQL **UPDATE**.

Il codice usa un metodo [mysql2::client](https://www.rubydoc.info/gems/mysql2) class .new() per la connessione ad Azure Database per MySQL. Chiama quindi il metodo [query()](https://www.rubydoc.info/gems/mysql2#Usage) per eseguire i comandi UPDATE. Chiama infine il metodo [close()](https://www.rubydoc.info/gems/mysql2/Mysql2/Client#close-instance_method) per chiudere la connessione prima di terminare.

Sostituire le stringhe `host`, `database`, `username` e `password` con valori personalizzati. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Update data
   client.query('UPDATE inventory SET quantity = %d WHERE name = %s;' % [200, '\'banana\''])
   puts 'Updated 1 row of data.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```


## <a name="delete-data"></a>Eliminare i dati
Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **DELETE**. 

Il codice usa un metodo [mysql2::client](https://www.rubydoc.info/gems/mysql2) class .new() per la connessione ad Azure Database per MySQL. Chiama quindi il metodo [query()](https://www.rubydoc.info/gems/mysql2#Usage) per eseguire i comandi DELETE. Chiama infine il metodo [close()](https://www.rubydoc.info/gems/mysql2/Mysql2/Client#close-instance_method) per chiudere la connessione prima di terminare.

Sostituire le stringhe `host`, `database`, `username` e `password` con valori personalizzati. 

```ruby
require 'mysql2'

begin
    # Initialize connection variables.
    host = String('mydemoserver.mysql.database.azure.com')
    database = String('quickstartdb')
    username = String('myadmin@mydemoserver')
    password = String('yourpassword')

    # Initialize connection object.
    client = Mysql2::Client.new(:host => host, :username => username, :database => database, :password => password)
    puts 'Successfully created connection to database.'

    # Delete data
    resultSet = client.query('DELETE FROM inventory WHERE name = %s;' % ['\'orange\''])
    puts 'Deleted 1 row.'

# Error handling
rescue Exception => e
    puts e.message

# Cleanup
ensure
    client.close if client
    puts 'Done.'
end
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./concepts-migrate-import-export.md)
