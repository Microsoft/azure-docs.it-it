---
title: Connettersi con Java - Database di Azure per MySQL
description: Questo argomento di avvio rapido offre un esempio di codice Java che è possibile usare per connettersi ai dati ed eseguire query da un'istanza di Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019
ms.topic: quickstart
ms.devlang: java
ms.date: 3/18/2020
ms.openlocfilehash: b5f1cbf2f822f350b1eeba032199676651364d84
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80983824"
---
# <a name="quickstart-use-java-to-connect-to-and-query-data-in-azure-database-for-mysql"></a>Guida introduttiva: Usare Java per connettersi ed eseguire query sui dati in Database di Azure per MySQL

In questo argomento di avvio rapido ci si connette a un'istanza di Database di Azure per MySQL usando un'applicazione Java e il driver JDBC MariaDB Connector/J. È quindi possibile usare istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database dalle piattaforme Mac, Ubuntu Linux e Windows. 

Questo argomento presuppone che si abbia familiarità con lo sviluppo con Java, ma non con Database di Azure per MySQL.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un'istanza di Database di Azure per il server MySQL. [Creare un database di Azure per il server MySQL tramite il portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md) o [Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](quickstart-create-mysql-server-database-using-azure-cli.md).
- La sicurezza della connessione al database di Azure per MySQL viene configurata con il firewall aperto e le impostazioni SSL configurate per l'applicazione.

## <a name="obtain-the-mariadb-connector"></a>Ottenere il connettore MariaDB

Per ottenere il connettore [MariaDB Connector/J](https://mariadb.com/kb/en/library/mariadb-connector-j/), usare uno degli approcci seguenti:
   - Usare il pacchetto Maven [mariadb-java-client](https://search.maven.org/search?q=a:mariadb-java-client) per includere la [dipendenza mariadb-java-client](https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client) nel file POM per il progetto.
   - Scaricare il driver JDBC [MariaDB Connector/J](https://downloads.mariadb.org/connector-java/) e includere il file JAR JDBC (ad esempio, mariadb-java-client-2.4.3.jar) nel percorso di classe dell'applicazione. Vedere la documentazione dell'ambiente relativa alle specifiche per il percorso delle classi, ad esempio [Apache Tomcat](https://tomcat.apache.org/tomcat-7.0-doc/class-loader-howto.html) o [Java SE](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/classpath.html)

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per MySQL. Sono necessari il nome del server completo e le credenziali di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Dal menu a sinistra nel portale di Azure scegliere **Tutte le risorse** e quindi cercare il server creato, ad esempio **mydemoserver**.
3. Selezionare il nome del server.
4. Nel pannello **Panoramica** del server prendere nota dei valori riportati in **Nome server** e **Nome di accesso dell'amministratore server**. Se si dimentica la password, in questo pannello è anche possibile reimpostarla.
 ![Nome del server del database di Azure per MySQL](./media/connect-java/azure-database-mysql-server-name.png)

## <a name="connect-create-table-and-insert-data"></a>Connettersi, creare tabelle e inserire dati

Usare il codice seguente per connettersi e caricare i dati usando la funzione con un'istruzione SQL **INSERT**. Il metodo [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) viene usato per connettersi a MySQL. I metodi [createStatement()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) ed execute() vengono usati per rilasciare e creare la tabella. L'oggetto prepareStatement viene usato per compilare i comandi di inserimento, con setString() e setInt() per associare i valori dei parametri. Il metodo executeUpdate() esegue il comando per ogni set di parametri per cui inserire i valori. 

Sostituire i parametri host, database, user e password con i valori specificati al momento della creazione del server e del database.

```java
import java.sql.*;
import java.util.Properties;

public class CreateTableInsertRows {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Drop previous table of same name if one exists.
                Statement statement = connection.createStatement();
                statement.execute("DROP TABLE IF EXISTS inventory;");
                System.out.println("Finished dropping table (if existed).");
    
                // Create table.
                statement.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
                System.out.println("Created table.");
                
                // Insert some data into table.
                int nRowsInserted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("INSERT INTO inventory (name, quantity) VALUES (?, ?);");
                preparedStatement.setString(1, "banana");
                preparedStatement.setInt(2, 150);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "orange");
                preparedStatement.setInt(2, 154);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "apple");
                preparedStatement.setInt(2, 100);
                nRowsInserted += preparedStatement.executeUpdate();
                System.out.println(String.format("Inserted %d row(s) of data.", nRowsInserted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
    
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}

```

## <a name="read-data"></a>Leggere i dati

Usare il codice seguente per leggere i dati con un'istruzione SQL **SELECT**. Il metodo [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) viene usato per connettersi a MySQL. I metodi [createStatement()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) ed executeQuery() vengono usati per connettersi ed eseguire l'istruzione select. I risultati vengono elaborati usando un oggetto ResultSet. 

Sostituire i parametri host, database, user e password con i valori specificati al momento della creazione del server e del database.

```java
import java.sql.*;
import java.util.Properties;

public class ReadTable {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
    
                Statement statement = connection.createStatement();
                ResultSet results = statement.executeQuery("SELECT * from inventory;");
                while (results.next())
                {
                    String outputString = 
                        String.format(
                            "Data row = (%s, %s, %s)",
                            results.getString(1),
                            results.getString(2),
                            results.getString(3));
                    System.out.println(outputString);
                }
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database."); 
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="update-data"></a>Aggiornare i dati

Usare il codice seguente per modificare i dati con un'istruzione SQL **UPDATE**. Il metodo [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) viene usato per connettersi a MySQL. I metodi [prepareStatement()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) ed executeUpdate() vengono usati per preparare ed eseguire l'istruzione update. 

Sostituire i parametri host, database, user e password con i valori specificati al momento della creazione del server e del database.

```java
import java.sql.*;
import java.util.Properties;

public class UpdateTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Modify some data in table.
                int nRowsUpdated = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?;");
                preparedStatement.setInt(1, 200);
                preparedStatement.setString(2, "banana");
                nRowsUpdated += preparedStatement.executeUpdate();
                System.out.println(String.format("Updated %d row(s) of data.", nRowsUpdated));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="delete-data"></a>Eliminare i dati

Usare il codice seguente per rimuovere i dati con un'istruzione SQL **DELETE**. Il metodo [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) viene usato per connettersi a MySQL.  I metodi [prepareStatement()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) ed executeUpdate() vengono usati per preparare ed eseguire l'istruzione delete. 

Sostituire i parametri host, database, user e password con i valori specificati al momento della creazione del server e del database.

```java
import java.sql.*;
import java.util.Properties;

public class DeleteTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Delete some data from table.
                int nRowsDeleted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("DELETE FROM inventory WHERE name = ?;");
                preparedStatement.setString(1, "orange");
                nRowsDeleted += preparedStatement.executeUpdate();
                System.out.println(String.format("Deleted %d row(s) of data.", nRowsDeleted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione del database MySQL a Database di Azure per MySQL usando dump e ripristino](concepts-migrate-dump-restore.md)
