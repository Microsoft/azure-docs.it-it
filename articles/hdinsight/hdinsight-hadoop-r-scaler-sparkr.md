---
title: Usare ScaleR e SparkR con Azure HDInsight
description: Usare Scaler e Sparkr per la manipolazione dei dati e lo sviluppo di modelli con ML Services in Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/26/2019
ms.openlocfilehash: 95fcca289b0776cc19464b13eb7d243ca4f8d5ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945509"
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>Uso combinato di ScaleR e SparkR in HDInsight

Questo documento illustra come stimare i ritardi di arrivo dei voli usando un modello di regressione logistica **ScaleR**. Nell'esempio vengono usati dati sul ritardo dei voli e dati sulle condizioni atmosferiche, uniti in join tramite **SparkR**.

Sebbene entrambi i pacchetti vengano eseguiti sul motore di esecuzione Spark di Apache Hadoop, vengono bloccati dalla condivisione dei dati in memoria perché ognuno di essi richiede le rispettive sessioni Spark. Finché questo problema non verrà risolto in una versione futura di ML Server, la soluzione alternativa consiste nel mantenere sessioni di Spark non sovrapposte e scambiare i dati tramite file intermedi. Le istruzioni riportate di seguito mostrano che questi requisiti sono semplici da rispettare.

Questo esempio è stato condiviso inizialmente in un discorso tenuto a Strata 2016 da Mario Inchiosa e Roni Burd. Il discorso è disponibile nel webinar [Building a Scalable Data Science Platform with R](https://channel9.msdn.com/blogs/Cloud-and-Enterprise-Premium/Building-A-Scalable-Data-Science-Platform-with-R-and-Hadoop) (Compilazione di una piattaforma Data Science scalabile con R).

Il codice è stato scritto originariamente per ML Server in esecuzione in Spark in un cluster HDInsight di Azure. Tuttavia, il concetto di combinazione dell'uso di SparkR e ScaleR in un unico script è valido anche nel contesto di ambienti locali.

I passaggi in questo documento presuppongono un livello intermedio di conoscenza di R e della libreria [ScaleR](/machine-learning-server/r/concept-what-is-revoscaler) di ML Server. Questo scenario è stato introdotto in [sparkr](https://spark.apache.org/docs/2.1.0/sparkr.html) .

## <a name="the-airline-and-weather-datasets"></a>Set di dati relativi alle compagnie aeree e alle previsioni meteo

I dati di volo sono disponibili negli [archivi del governo statunitense](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236). È disponibile anche come file zip da [AirOnTimeCSV.zip](https://packages.revolutionanalytics.com/datasets/AirOnTime87to12/AirOnTimeCSV.zip).

I dati meteo suddivisi per mese possono essere scaricati come file ZIP in formato non elaborato dal [repository della National Oceanic and Atmospheric Administration](https://www.ncdc.noaa.gov/orders/qclcd/). Per questo esempio, scaricare i dati per maggio 2007 - dicembre 2012. Usare i file dei dati orari e il file `YYYYMMMstation.txt` all'interno di ognuno degli ZIP.

## <a name="setting-up-the-spark-environment"></a>Configurazione dell'ambiente Spark

Usare il codice seguente per configurare l'ambiente Spark:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Successivamente, aggiungere `Spark_Home` al percorso di ricerca per i pacchetti R. Questo consente di usare SparkR e inizializzare una sessione SparkR:

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Preparazione dei dati meteo

Per preparare i dati meteo, creare un subset per le colonne necessarie per la modellazione: 

- "Visibility"
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- "WindSpeed"
- "Altimeter"

Aggiungere quindi un codice aeroporto associato a una stazione meteo e convertire le misurazioni dall'ora locale in ora UTC.

Iniziare creando un file per eseguire il mapping delle informazioni sulla stazione meteo (WBAN) al codice dell'aeroporto. Il codice seguente legge ognuno dei file di dati meteorologici non elaborati, i subset per le colonne necessarie, unisce il file di mapping della stazione meteo, modifica la data e l'ora delle misurazioni in UTC e quindi scrive una nuova versione del file:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>Importazione dei dati relativi al meteo e alle compagnie aeree per i DataFrame di Spark

A questo punto verrà usata la funzione SparkR [read.df()](https://spark.apache.org/docs/latest/api/R/read.df.html) per importare i dati relativi al meteo e alle compagnie aeree nei DataFrame di Spark. Questa funzione, come molti altri metodi Spark, viene eseguita in modo differito, vale a dire che vengono accodate per l'esecuzione, ma non vengono eseguite fino a quando richiesto.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Pulizia e trasformazione dei dati

Successivamente viene eseguita una pulizia sui dati della compagnia aerea importati per rinominare le colonne. Verranno tenute le sole variabili necessarie e gli orari di partenza pianificati saranno arrotondati all'ora più vicina per consentire l'unione con i dati meteo più recenti alla partenza:

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

A questo punto si eseguiranno operazioni simili sui dati meteo:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Unione in join dei dati meteo e delle compagnie aeree

La funzione [join()](https://spark.apache.org/docs/latest/api/R/join.html) di SparkR verrà ora usata per eseguire un left outer join dei dati sulle compagnie aeree e dei dati meteo in base ai valori di datetime e AirportID di partenza. Il outer join consente di conservare tutti i record di dati della compagnia aerea anche se non sono presenti dati meteorologici corrispondenti. Dopo il join verranno rimosse alcune colonne ridondanti e verranno rinominate le colonne conservate per rimuovere il prefisso DataFrame introdotto dal join.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

In modo analogo verrà eseguito il join dei dati meteo e dei dati delle compagnie aeree in base ai valori datetime e AirportID di arrivo:

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Salvare i risultati in un file con formato CSV per lo scambio con ScaleR

Questa azione completa i join che è necessario eseguire con SparkR. I dati verranno salvati da "joinedDF5" del DataFrame di Spark finale in un file CSV per ScaleR e quindi verrà chiusa la sessione di SparkR. È possibile indicare in modo esplicito a SparkR di salvare il file CSV risultante in 80 partizioni separate per abilitare un parallelismo sufficiente durante l'elaborazione in ScaleR:

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Importare in XDF per l'uso da parte di ScaleR

È possibile usare direttamente il file CSV dei dati relativi al meteo e alle compagnie aeree aggiunti per la modellazione tramite un'origine dati di testo ScaleR. Ma viene importato prima in XDF, perché è più efficiente quando si eseguono più operazioni sul set di dati:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for ML Services 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Suddivisione dei dati per il training e il test

rxDataStep viene usato per suddividere i dati del 2012 per il test e mantenere il resto per il training:

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Eseguire il training e il test di un modello di regressione logistica

A questo punto è possibile compilare un modello. Per visualizzare l'influenza dei dati meteorologici sul ritardo nell'orario di arrivo, viene usata la routine di regressione logistica di scaler. Questa viene usata per la modellazione se un ritardo di arrivo maggiore di 15 minuti è influenzato dal meteo negli aeroporti di partenza e arrivo:

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Vediamo ora come funziona sui dati di test effettuando alcune stime e osservando ROC e AUC.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Assegnazione di punteggi in un'altra posizione

È possibile anche usare il modello per l'assegnazione di punteggi ai dati in un'altra piattaforma Salvando il file in un file RDS e quindi trasferendo e importando tale Servizi Desktop remoto in un ambiente di assegnazione dei punteggi di destinazione come Microsoft SQL Server R Services. È importante assicurarsi che i livelli di fattore dei dati a cui assegnare un punteggio corrispondano a quelli in cui è stato compilato il modello. Questa corrispondenza può essere eseguita estraendo e salvando le informazioni sulle colonne associate ai dati di modellazione tramite la funzione scaler `rxCreateColInfo()` e quindi applicando le informazioni della colonna all'origine dati di input per la stima. Nell'esempio di codice seguente vengono salvate alcune righe del set di dati di test e vengono estratte e utilizzate le informazioni sulle colonne di questo esempio nello script di stima:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come è possibile combinare l'uso di Sparkr per la manipolazione dei dati con scaler per lo sviluppo di modelli in Hadoop Spark. In questo scenario è necessario gestire sessioni di Spark separate eseguendo una sola sessione alla volta e scambiando i dati tramite file CSV. Anche se semplice, questo processo sarà ancora più semplice nella prossima versione di ML Services in cui SparkR e ScaleR potranno condividere una sessione di Spark e pertanto i relativi DataFrame.

## <a name="next-steps-and-more-information"></a>Passaggi successivi e altre informazioni

- Per ulteriori informazioni sull'utilizzo di ML Server in Apache Spark, vedere la [Guida introduttiva](/machine-learning-server/r/how-to-revoscaler-spark).

- Per informazioni su ML Services in HDInsight, vedere [Panoramica di ml Services in HDInsight](r-server/r-server-overview.md).

Per altre informazioni sull'uso di SparkR, vedere:

- [Documento Apache sparkr](https://spark.apache.org/docs/2.1.0/sparkr.html).

- [Panoramica di SparkR](/azure/databricks/spark/latest/sparkr/overview)
