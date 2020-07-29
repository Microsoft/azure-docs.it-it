---
title: Esplorare i dati in una macchina virtuale di SQL Server - Processo di data science per i team
description: Esplora e elabora i dati e genera le funzionalità usando Python o SQL in una macchina virtuale SQL Server in Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e387d5f7ee0b1926457717b30b03bbfeb8d70a1c
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027427"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Elaborazione dei dati della macchina virtuale di SQL Server in Azure
Questo documento descrive come esplorare i dati e creare funzionalità per i dati archiviati in una VM di SQL Server su Azure. Questo obiettivo può essere completato da data wrangling usando SQL o usando un linguaggio di programmazione come Python.

> [!NOTE]
> Le istruzioni SQL di esempio fornite nel documento presuppongono che i dati si trovino in SQL Server. In caso contrario, fare riferimento al processo di analisi scientifica dei dati cloud per visualizzare informazioni su come spostare i dati in SQL Server.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>Uso di SQL
In questa sezione, vengono descritte le seguenti attività di gestione dei dati usando SQL:

1. [Esplorazione dei dati](#sql-dataexploration)
2. [Generazione di funzionalità](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Esplorazione dei dati
Di seguito, sono riportati alcuni script SQL di esempio da utilizzare per esplorare gli archivi dati in SQL Server.

> [!NOTE]
> Per un esempio pratico, è possibile usare il [set di dati dei taxi di New York City](https://www.andresmh.com/nyctaxitrips/) e fare riferimento all'IPNB sulla [gestione dei dati di New York City tramite IPython Notebook e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) per una procedura dettagliata end-to-end.
> 
> 

1. Visualizzare il numero di osservazioni per giorno
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Visualizzare i livelli in una colonna di categoria 
   
    `select  distinct <column_name> from <databasename>`
3. Visualizzare il numero di livelli in una combinazione di due colonne di categoria 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Visualizzare la distribuzione per colonne numeriche
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Generazione di funzionalità
In questa sezione viene descritto come creare funzionalità tramite SQL:  

1. [Generazione di funzionalità basate sul conteggio](#sql-countfeature)
2. [Creazione di contenitori per la creazione di funzionalità](#sql-binningfeature)
3. [Implementazione delle funzionalità da una singola colonna](#sql-featurerollout)

> [!NOTE]
> Dopo aver creato le funzionalità aggiuntive, è possibile aggiungerle come colonne alla tabella esistente oppure creare una nuova tabella con le funzionalità aggiuntive e la chiave primaria, che può essere unita alla tabella originale. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Generazione di funzionalità basate sul conteggio
Gli esempi seguenti illustrano due modi per generare funzionalità di conteggio. Nel primo metodo viene usata la somma condizionale e nel secondo la clausola "where". Questi risultati possono quindi essere Uniti alla tabella originale (usando le colonne chiave primaria) per avere le funzionalità di conteggio insieme ai dati originali.

```sql
select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 
```

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Creazione di contenitori per la creazione di funzionalità
L'esempio seguente illustra come generare funzionalità in contenitori, inserendo una colonna numerica (con cinque contenitori) che può essere usata come funzionalità:

```sql
SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>
```

### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Implementazione delle funzionalità da una singola colonna
In questa sezione viene illustrato come implementare una singola colonna in una tabella per generare funzionalità aggiuntive. In questo esempio si presuppone che nella tabella dalla quale si tenta di creare la funzionalità sia presente una colonna relativa alla latitudine o alla longitudine.

Di seguito è riportata una breve introduzione sui dati di posizione relativi a latitudine e longitudine (risorse assegnate da stackoverflow [Come misurare la precisione di latitudine e longitudine?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Questa guida è utile per comprendere prima di includere la località come una o più funzionalità:

* Il segno indica se l'utente si trova a Nord, Sud, Ovest o Est.
* Una cifra nell'ordine delle centinaia diversa da zero indica che si sta usando la longitudine invece della latitudine.
* Una cifra nell'ordine delle decine indica una posizione a circa 1.000 km. Inoltre, fornisce informazioni utili sul continente o sull'oceano in cui si trova l'utente.
* La cifra nell'ordine dell'unità (un grado decimale) indica posizioni fino a 111 km (60 miglia nautiche, circa 69 miglia standard). Può indicare approssimativamente lo stato, il paese o l'area geografica in cui ci si trova.
* La prima posizione decimale è caratterizzata da un valore massimo di 11,1 km: consente di rilevare la posizione di una grande città da una circostante.
* La seconda posizione decimale è caratterizzata da un valore massimo di 1,1 km: consente di dividere un paese dall'altro.
* La terza posizione decimale è caratterizzata da un valore massimo di 110 m: consente di identificare un campo agricolo o una sede istituzionale.
* La quarta posizione decimale è caratterizzata da un valore massimo di 11 m: consente di identificare una porzione di terreno. La sua accuratezza è paragonabile a quella di un'unità GPS non corretta e senza interferenze.
* La quinta posizione decimale è caratterizzata da un valore massimo di 1,1 m e consente di distinguere un albero da un altro. Un'accuratezza di questo tipo, con le unità GPS commerciali, può essere raggiunta soltanto con una correzione differenziale.
* La sesta posizione decimale è caratterizzata da un valore massimo di 0,11 m: è possibile usarla per visualizzare i dettagli delle strutture, per progettare panorami e costruire strade. È più che sufficiente per rilevare i movimenti dei ghiacciai e dei fiumi. È possibile ottenere questa accuratezza eseguendo misurazioni accurate con il GPS, quale quello corretto in modo differenziale.

Le informazioni sulla posizione possono essere inserite nelle funzionalità nel modo seguente: separando le informazioni su regioni, posizioni e città. È anche possibile chiamare un endpoint REST, ad esempio l'API di Bing Maps, disponibile in [trovare una posizione in base al punto](https://msdn.microsoft.com/library/ff701710.aspx) per ottenere le informazioni sull'area/quartiere.

```sql
select 
    <location_columnname>
    ,round(<location_columnname>,0) as l1        
    ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
    ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
    ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
    ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
    ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
    ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
from <tablename>
```

Queste funzionalità basate sulla posizione possono essere usate anche per generate altre funzionalità di conteggio, come descritto in precedenza. 

> [!TIP]
> A livello di programmazione, è possibile inserire i record usando il linguaggio preferito. Potrebbe essere necessario inserire i dati in blocchi per migliorare l'efficienza di scrittura (per un esempio di come farlo usando pyodbc, vedere l'articolo dedicato a [un esempio HelloWorld per l'accesso a SQL Server con python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python). Un'altra alternativa consiste nell'inserire i dati nel database usando l' [utilità BCP](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Connessione ad Azure Machine Learning
La funzionalità appena creata può essere aggiunta come una colonna a una tabella esistente oppure archiviata in una nuova tabella e unita a quella originale ai fini dell'apprendimento automatico. È possibile generare funzionalità o accedere a quelle già create usando il modulo [Import Data][import-data] (Importazione dati) di Azure Machine Learning, come illustrato di seguito:

![lettori azureml][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Utilizzo di un linguaggio di programmazione quale Python
L'uso di Python per esplorare i dati e generare funzionalità quando i dati si trovano in SQL Server funziona in modo analogo all'elaborazione dei dati nel BLOB di Azure con Python illustrata nell'articolo su come [elaborare i dati BLOB di Azure in un ambiente di analisi scientifica dei dati](data-blob.md). Caricare i dati dal database in un frame di dati Pandas per un'ulteriore elaborazione. In questa sezione, è stato descritto il processo di connessione al database per caricare dati all'interno di un frame di dati.

Il seguente formato della stringa di connessione può essere usato per connettersi a un database di SQL Server da Pyhton usando pyodbc (sostituire il nome del server, quello del database, il nome utente e la password con i valori personalizzati):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

La [libreria Pandas](https://pandas.pydata.org/) in Python fornisce una vasta gamma di strutture di dati e strumenti di analisi dei dati per la manipolazione dei dati per la programmazione Python. Il codice seguente consente di leggere i risultati restituiti da un database di SQL Server all'interno di un frame di dati di Pandas.

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

A questo punto, è possibile usare il frame di dati di Pandas come illustrato nell'articolo su come [elaborare i dati BLOB di Azure in un ambiente di analisi scientifica dei dati](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Analisi scientifica dei dati di Azure nell'esempio di azione
Per un esempio della procedura dettagliata end-to-end del processo di analisi scientifica dei dati di Azure usando un set di dati pubblici, vedere [Processo di analisi scientifica dei dati di Azure in azione](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

