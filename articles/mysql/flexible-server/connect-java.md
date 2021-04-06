---
title: 'Guida introduttiva: usare Java e JDBC con database di Azure per il server MySQLFlexible'
description: Informazioni su come usare Java e JDBC con un database di server flessibili di database di Azure per MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devcenter, devx-track-azurecli
ms.topic: quickstart
ms.devlang: java
ms.date: 01/16/2021
ms.openlocfilehash: 93a8a7644c7ca729f0e4fd201b88e995576d4bda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98605986"
---
# <a name="quickstart-use-java-and-jdbc-with-azure-database-for-mysql-flexible-server"></a>Guida introduttiva: usare Java e JDBC con il server flessibile database di Azure per MySQL

Questo argomento illustra la creazione di un'applicazione di esempio che usa Java e [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) per archiviare e recuperare le informazioni nel [server flessibile database di Azure per MySQL](./index.yml).

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure. Se non è disponibile, [ottenere una versione di valutazione gratuita](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](../../cloud-shell/quickstart.md) o [interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). È consigliabile usare Azure Cloud Shell in modo che l'accesso venga eseguito automaticamente e che sia possibile accedere a tutti gli strumenti necessari.
- Un'istanza [supportata di Java Development Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versione 8 (inclusa in Azure Cloud Shell).
- Strumento di compilazione di [Apache Maven](https://maven.apache.org/).

## <a name="prepare-the-working-environment"></a>Preparare l'ambiente di lavoro

Verranno usate le variabili di ambiente per limitare gli errori di digitazione e per semplificare la personalizzazione della configurazione seguente in base alle esigenze specifiche.

Configurare tali variabili di ambiente usando i comandi seguenti:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME= flexibleserverdb
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_MYSQL_USERNAME=demo
AZ_MYSQL_PASSWORD=<YOUR_MYSQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Sostituire i segnaposto con i valori seguenti, che vengono usati nell'intero articolo:

- `<YOUR_DATABASE_NAME>`: il nome del server MySQL. Deve essere univoco in Azure.
- `<YOUR_AZURE_REGION>`: l'area di Azure da usare. È possibile usare `eastus` per impostazione predefinita, ma è consigliabile configurare un'area più vicina a dove si risiede. Per l'elenco completo di aree disponibili, immettere `az account list-locations`.
- `<YOUR_MYSQL_PASSWORD>`: la password del server di database MySQL. La password deve essere composta da un minimo di otto caratteri di tre categorie seguenti: lettere maiuscole, lettere minuscole, numeri (0-9) e caratteri non alfanumerici (!, $, #, % e così via).
- `<YOUR_LOCAL_IP_ADDRESS>`: l'indirizzo IP del computer locale, da cui verrà eseguita l'applicazione Java. Un modo pratico per trovarlo è puntare il browser all'indirizzo [whatismyip.akamai.com](http://whatismyip.akamai.com/).

Successivamente, creare un gruppo di risorse:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> Per visualizzare i dati JSON e renderli più leggibili, viene usata l'utilità `jq`, installata per impostazione predefinita in [Azure Cloud Shell](https://shell.azure.com/).
> Se non si preferisce usare questa utilità, è possibile rimuovere tranquillamente la parte `| jq` di tutti i comandi che verranno usati.

## <a name="create-an-azure-database-for-mysql-instance"></a>Creare un'istanza di Database di Azure per MySQL

Il primo componente che verrà creato è un server MySQL gestito.

> [!NOTE]
> Per informazioni più dettagliate sulla creazione di server MySQL, vedere [Creare un server di Database di Azure per MySQL nel portale di Azure](./quickstart-create-server-portal.md).

In [Azure Cloud Shell](https://shell.azure.com/) eseguire lo script seguente:

```azurecli
az mysql flexible-server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name Standard_B1ms \
    --storage-size 5120 \
    --admin-user $AZ_MYSQL_USERNAME \
    --admin-password $AZ_MYSQL_PASSWORD \
    --public-access $AZ_LOCAL_IP_ADDRESS
  	| jq
```

Assicurarsi di immettere <>-IP-ADDRESS per accedere al server dal computer locale. Questo comando consente di creare un server di livello espandibile MySQL flessibile per lo sviluppo.

Il server MySQL creato ha un database vuoto denominato **flexibleserverdb**. Questo database verrà usato per questo articolo.

[Si verificano problemi? Informare l'it.](https://github.com/MicrosoftDocs/azure-docs/issues)

### <a name="create-a-new-java-project"></a>Creare un nuovo progetto Java

Usando l'IDE preferito, creare un nuovo progetto Java e aggiungere un file `pom.xml` nella relativa directory radice:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
    </dependencies>
</project>
```

Questo file è [Apache Maven](https://maven.apache.org/) che configura il progetto da usare:

- Java 8
- Un driver di MySQL recente per Java

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-mysql"></a>Preparare un file di configurazione per la connessione a Database di Azure per MySQL

Creare un file *src/main/resources/application.properties* e aggiungere:

```properties
url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
user=demo
password=$AZ_MYSQL_PASSWORD
```

- Sostituire le due variabili `$AZ_DATABASE_NAME` con il valore configurato all'inizio di questo articolo.
- Sostituire la variabile `$AZ_MYSQL_PASSWORD` con il valore configurato all'inizio di questo articolo.

> [!NOTE]
> Alla proprietà di configurazione `url` verrà aggiunto `?serverTimezone=UTC`, per indicare al driver JDBC di usare il formato di data UTC (o Coordinated Universal Time) per la connessione al database. In caso contrario, il server Java non userà lo stesso formato di data del database, causando un errore.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>Creare un file SQL per generare lo schema del database

Per creare uno schema del database, verrà usato un file *src/main/Resources/`schema.sql`* . Creare tale file con il contenuto seguente:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>Codice dell'applicazione

### <a name="connect-to-the-database"></a>Stabilire la connessione al database

Aggiungere quindi il codice Java che userà JDBC per archiviare e recuperare i dati dal server MySQL.

Creare un file *src/main/java/DemoApplication.java*, che contiene:

```java
package com.example.demo;

import com.mysql.cj.jdbc.AbandonedConnectionCleanupThread;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
        AbandonedConnectionCleanupThread.uncheckedShutdown();
    }
}
```

[Si verificano problemi? Informare l'it.](https://github.com/MicrosoftDocs/azure-docs/issues)

Questo codice Java userà i file *application.properties* e *schema.sql* creati in precedenza per stabilire una connessione con il server MySQL e creare uno schema in cui archiviare i dati.

In questo file è possibile notare che i metodi di inserimento, lettura, aggiornamento ed eliminazione dei dati sono stati commentati. Il codice di tali metodi verranno scritti nella parte restante di questo articolo e sarà possibile rimuovere i commenti uno dopo l'altro.

> [!NOTE]
> Le credenziali del database sono archiviate nelle proprietà *user* e *password* del file *application.properties*. Queste credenziali vengono usate durante l'esecuzione di `DriverManager.getConnection(properties.getProperty("url"), properties);`, perché il file delle proprietà viene passato come argomento.

> [!NOTE]
> La riga `AbandonedConnectionCleanupThread.uncheckedShutdown();` alla fine è un comando specifico del driver di MySQL per eliminare definitivamente un thread interno quando l'applicazione viene arrestata.
> Può essere ignorato senza problemi.

È ora possibile eseguire questa classe main con lo strumento preferito:

- Usando l'IDE, dovrebbe essere possibile fare clic con il pulsante destro del mouse sulla classe *DemoApplication* ed eseguirla.
- Con Maven è possibile eseguire l'applicazione eseguendo: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"`.

L'applicazione dovrà connettersi a Database di Azure per MySQL, creare uno schema del database e quindi chiudere la connessione, come si vedrà nei log della console:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Closing database connection
```

### <a name="create-a-domain-class"></a>Creare una classe di dominio

Creare una nuova classe Java `Todo` accanto alla classe `DemoApplication` e aggiungere il codice seguente:

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

Questa classe è un modello di dominio mappato alla tabella `todo` creata durante l'esecuzione dello script *schema.sql*.

### <a name="insert-data-into-azure-database-for-mysql"></a>Inserimento di dati in Database di Azure per MySQL

Nel file *src/main/java/DemoApplication.java* aggiungere il metodo seguente dopo il metodo main per inserire i dati nel database:

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

È ora possibile rimuovere il commento dalle due righe seguenti nel metodo `main`:

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

Se si esegue la classe main, si dovrebbe ottenere l'output seguente:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-database-for-mysql"></a>Lettura dei dati da Database di Azure per MySQL

A questo punto verranno letti i dati inseriti in precedenza per verificare il corretto funzionamento del codice.

Nel file *src/main/java/DemoApplication.java* aggiungere il metodo seguente dopo il metodo `insertData` per leggere i dati dal database:

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

È ora possibile rimuovere il commento dalla riga seguente nel metodo `main`:

```java
todo = readData(connection);
```

Se si esegue la classe main, si dovrebbe ottenere l'output seguente:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Closing database connection
```

[Si verificano problemi? Informare l'it.](https://github.com/MicrosoftDocs/azure-docs/issues)

### <a name="updating-data-in-azure-database-for-mysql"></a>Aggiornamento dei dati in Database di Azure per MySQL

A questo punto, verranno aggiornati i dati inseriti in precedenza.

Sempre nel file *src/main/java/DemoApplication.java* aggiungere il metodo seguente dopo il metodo `readData` per aggiornare i dati all'interno del database:

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

È ora possibile rimuovere il commento dalle due righe seguenti nel metodo `main`:

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

Se si esegue la classe main, si dovrebbe ottenere l'output seguente:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Update data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true}
[INFO   ] Closing database connection
```

### <a name="deleting-data-in-azure-database-for-mysql"></a>Eliminazione dei dati in Database di Azure per MySQL

A questo punto, verranno eliminati i dati inseriti in precedenza.

Sempre nel file *src/main/java/DemoApplication.java* aggiungere il metodo seguente dopo il metodo `updateData` per eliminare i dati all'interno del database:

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

È ora possibile rimuovere il commento dalla riga seguente nel metodo `main`:

```java
deleteData(todo, connection);
```

Se si esegue la classe main, si dovrebbe ottenere l'output seguente:

```
[INFO   ] Loading application properties
[INFO   ] Connecting to the database
[INFO   ] Database connection test: demo
[INFO   ] Create database schema
[INFO   ] Insert data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true}
[INFO   ] Update data
[INFO   ] Read data
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true}
[INFO   ] Delete data
[INFO   ] Read data
[INFO   ] There is no data in the database!
[INFO   ] Closing database connection
```

## <a name="clean-up-resources"></a>Pulire le risorse

Congratulazioni! È stata creata un'applicazione Java che usa JDBC per archiviare e recuperare i dati da Database di Azure per MySQL.

Per pulire tutte le risorse usate in questo argomento di avvio rapido, eliminare il gruppo di risorse con il comando seguente:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione del database MySQL a Database di Azure per MySQL usando dump e ripristino](../concepts-migrate-dump-restore.md)
