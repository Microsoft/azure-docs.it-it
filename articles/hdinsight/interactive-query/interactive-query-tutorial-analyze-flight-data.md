---
title: 'Esercitazione: Operazioni di estrazione, trasformazione e caricamento (ETL) con Interactive Query - Azure HDInsight'
description: 'Esercitazione: Informazioni su come estrarre i dati da un set di dati CSV non elaborato. Trasformarlo con Interactive Query in HDInsight. Caricare quindi i dati trasformati in Database SQL di Azure usando Apache Sqoop.'
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 07/02/2019
ms.openlocfilehash: 1aab6b4fe4ffafb3cbba60b2b3774d2577d3d53c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562468"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Esercitazione: Estrarre, trasformare e caricare dati usando Interactive Query in Azure HDInsight

In questa esercitazione verrà scaricato un file di dati CSV non elaborato dei dati di volo disponibili pubblicamente. Verrà importato nell'archiviazione cluster HDInsight e quindi i dati verranno trasformati usando Interactive Query in Azure HDInsight. Dopo averli trasformati, i dati vengono caricati in un database SQL di Database SQL di Azure tramite [Apache Sqoop](https://sqoop.apache.org/).

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Scaricare i dati di esempio sui voli
> * Caricare i dati in un cluster HDInsight
> * Trasformare i dati usando Interactive Query
> * Creare una tabella in un database di Database SQL di Azure
> * Usare Sqoop per esportare i dati in un database di Database SQL di Azure

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Interactive Query in HDInsight. Consultare [Creare cluster di Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selezionare **Interactive Query** per **Tipo di cluster**.

* Database nel database SQL di Azure. Usare il database come archivio dati di destinazione. Se non si ha un database in Database SQL di Azure, vedere [Creare un database in Database SQL di Azure](../../azure-sql/database/single-database-create-quickstart.md).

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Scaricare i dati relativi ai voli

1. Passare alla pagina [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?gnoyr_VQ=FGJ).

2. Nella pagina deselezionare tutti i campi e quindi selezionare i valori seguenti:

   | Nome | valore |
   | --- | --- |
   | Filter Year |2019 |
   | Filter Period |January |
   | Campi |`Year, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay`. |

3. Selezionare **Download**. Si otterrà un file con estensione zip con i campi dati selezionati.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Caricare i dati in un cluster HDInsight

Esistono diversi modi per caricare i dati nell'archivio associato a un cluster HDInsight. In questa sezione si usa `scp` per caricare i dati. Per informazioni sugli altri modi per caricare i dati, vedere [Caricare dati di HDInsight](../hdinsight-upload-data.md).

1. Caricare il file ZIP nel nodo head del cluster HDInsight. Modificare il comando seguente sostituendo `FILENAME` con il nome del file ZIP e `CLUSTERNAME` con il nome del cluster HDInsight. Aprire quindi un prompt dei comandi, impostare la directory di lavoro sul percorso del file e quindi immettere il comando.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Se richiesto, specificare Sì o No per continuare. Il testo non è visibile nella finestra durante la digitazione.

2. Al termine del caricamento connettersi al cluster tramite SSH. Modificare il comando seguente sostituendo `CLUSTERNAME` con il nome del cluster HDInsight. Immettere quindi il comando seguente:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Configurare la variabile di ambiente una volta stabilita la connessione SSH. Sostituire `FILE_NAME`, `SQL_SERVERNAME`, `SQL_DATABASE`, `SQL_USER` e `SQL_PASWORD` con i valori appropriati. Quindi immettere il comando:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Decomprimere il file ZIP immettendo il comando seguente:

    ```bash
    unzip $FILENAME.zip
    ```

5. Creare una directory nell'archivio di HDInsight e quindi copiare il file CSV nella directory immettendo il comando seguente:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Trasformare i dati usando una query Hive

Esistono diversi modi per eseguire un processo Hive in un cluster HDInsight. In questa sezione si usa [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) per eseguire un processo Hive. Per informazioni su altri metodi di esecuzione di un processo Hive, vedere [Usare Apache Hive in HDInsight](../hadoop/hdinsight-use-hive.md).

Come parte del processo Hive, importare i dati contenuti nel file con estensione csv in una tabella Hive denominata **Delays**.

1. Dal prompt SSH già disponibile per il cluster HDInsight usare il comando seguente per creare e modificare un nuovo file denominato **flightdelays.hql**:

    ```bash
    nano flightdelays.hql
    ```

2. Usare il testo seguente come contenuto del file:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Per salvare il file premere **CTRL+X**, quindi immettere **Y**.

4. Per avviare Hive ed eseguire il file **flightdelays.hql**, usare il comando seguente:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Al termine dell'esecuzione dello script **flightdelays.hql** usare il comando seguente per aprire una sessione Beeline interattiva:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. Quando si riceve il prompt `jdbc:hive2://localhost:10001/>`, usare la query seguente per recuperare i dati dai dati sui ritardi dei voli importati:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Grazie a questa query vengono recuperati un elenco di città in cui si sono verificati ritardi meteo e il tempo di ritardo medio che può essere salvato in `/tutorials/flightdelays/output`. Sqoop leggerà in seguito i dati da questo percorso e li esporterà nel database SQL di Azure.

7. Per uscire da Beeline, immettere `!quit` al prompt dei comandi.

## <a name="create-a-sql-database-table"></a>Creare una tabella del database SQL

Sono disponibili diversi modi per connettersi al database SQL per creare una tabella. Nei seguenti passaggi viene usato [FreeTDS](http://www.freetds.org/) dal cluster HDInsight.

1. Per installare FreeTDS, usare il comando seguente dalla connessione SSH aperta al cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Al termine dell'installazione usare il comando seguente per connettersi al database SQL.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    L'output che si riceve è simile al testo seguente:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. Al prompt `1>`, immettere il codice seguente:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    Dopo aver immesso l'istruzione `GO`, vengono valutate le istruzioni precedenti. Questa istruzione crea una tabella denominata **delays**, con un indice cluster.

    Per verificare che la tabella sia stata creata, usare la query seguente:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    L'output è simile al testo seguente:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Per uscire dall'utilità tsql, immettere `exit` al prompt `1>`.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Esportare i dati nel database SQL tramite Apache Sqoop

Nelle sezioni precedenti i dati trasformati sono stati copiati nel percorso `/tutorials/flightdelays/output`. In questa sezione viene usato Sqoop per esportare i dati da `/tutorials/flightdelays/output` alla tabella creata in Database SQL di Azure.

1. Verificare che Sqoop possa vedere il database SQL immettendo il comando seguente:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Questo comando restituisce un elenco di database, compreso il database in cui è stata creata in precedenza la tabella `delays`.

2. Esportare i dati da `/tutorials/flightdelays/output` alla tabella `delays` immettendo il comando seguente:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop si connette al database contenente la tabella `delays` ed esporta i dati dalla directory `/tutorials/flightdelays/output` nella tabella `delays`.

3. Al termine dell'esecuzione del comando sqoop, usare l'utilità tsql per connettersi al database immettendo il comando seguente:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Usare le istruzioni seguenti per verificare che i dati siano stati esportati nella tabella delays:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Dovrebbe essere visualizzato un elenco di dati della tabella. La tabella include il nome della città e il tempo di ritardo medio dei voli per la città.

    Digitare `exit` per uscire dall'utilità tsql.

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non è in uso. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, eliminare i cluster quando non vengono usati è una scelta economicamente conveniente.

Per eliminare un cluster, vedere [Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è importato un file CSV di dati non elaborati in un archivio cluster HDInsight e quindi si sono trasformati i dati usando Interactive Query in Azure HDInsight.  Passare all'esercitazione successiva per informazioni sul connettore Apache Hive Warehouse.

> [!div class="nextstepaction"]
> [Integrare Apache Spark e Apache Hive con il connettore Hive Warehouse](./apache-hive-warehouse-connector.md)
