---
title: Creare caratteristiche in SQL Server tramite SQL e Python - Processo di data science per i team
description: Creare caratteristiche per i dati archiviati in una macchine virtuale di SQL Server in Azure usando SQL e Python, come parte del processo di data science per i team.
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
ms.openlocfilehash: 3c20bf1c5856276c4c7ee0e37ed4ef2120d1d93d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322034"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Creare funzionalità per i dati in SQL Server tramite SQL e Python
Questo documento illustra come creare funzionalità per i dati archiviati in una VM di SQL Server in Azure che consentono agli algoritmi di apprendere in modo più efficiente dai dati. A tale scopo, è possibile usare SQL o un linguaggio di programmazione come Python. Entrambi gli approcci sono illustrati di seguito.

Questa attività è un passaggio del [Processo di analisi scientifica dei dati per i team (TDSP)](./index.yml).

> [!NOTE]
> Per un esempio pratico, è possibile consultare il [set di dati dei taxi di NYC](https://www.andresmh.com/nyctaxitrips/) e fare riferimento all'IPNB intitolato [Gestione dei dati di NYC usando IPython Notebook e SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) per una procedura dettagliata end-to-end.
> 
> 

## <a name="prerequisites"></a>Prerequisiti
Questo articolo presuppone che l'utente abbia:

* Creato un account di archiviazione di Azure. Per istruzioni, vedere [Creare un account di archiviazione di Azure](../../storage/common/storage-account-create.md)
* I dati vengono archiviati in SQL Server. In caso contrario, consultare [Spostamento dei dati in un database SQL di Azure per Azure Machine Learning](move-sql-azure.md) per istruzioni su come spostare i dati disponibili.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Creazione di funzionalità con SQL
In questa sezione viene descritto come creare funzionalità tramite SQL:  

* [Generazione di funzionalità basate sul conteggio](#sql-countfeature)
* [Creazione di contenitori per la creazione di funzionalità](#sql-binningfeature)
* [Implementazione delle funzionalità da una singola colonna](#sql-featurerollout)

> [!NOTE]
> Dopo aver creato le funzionalità aggiuntive, è possibile aggiungerle come colonne alla tabella esistente oppure creare una nuova tabella con le funzionalità aggiuntive e la chiave primaria, che può essere unita alla tabella originale.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Creazione di funzionalità basate sul conteggio
In questo documento vengono descritte due modalità per creare funzionalità di conteggio. Nel primo metodo viene utilizzata la somma condizionale, mentre nel secondo la clausola "where". Queste nuove funzionalità possono quindi essere unite alla tabella originale (usando colonne chiave primaria) per avere le funzionalità di conteggio insieme ai dati originali.

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

Di seguito, viene riportata una breve introduzione sui dati di posizione relativi a latitudine e longitudine (risorse assegnate da stackoverflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Ecco alcuni concetti utili da conoscere sui dati di posizione prima di creare funzionalità dal campo:

* Il segno indica se l'utente si trova a Nord, Sud, Ovest o Est del globo.
* Una cifra dell'ordine delle centinaia diversa da zero indica la longitudine, non la latitudine in uso.
* Una cifra nell'ordine delle decine indica una posizione a circa 1.000 km. Offre informazioni utili sul continente o sull'oceano in cui si trova l'utente.
* La cifra nell'ordine dell'unità (un grado decimale) indica posizioni fino a 111 km (60 miglia nautiche, circa 69 miglia standard). Indica approssimativamente lo stato o il paese di grandi dimensioni in cui ci troviamo.
* La prima posizione decimale è caratterizzata da un valore massimo di 11,1 km: consente di rilevare la posizione di una grande città da una circostante.
* La seconda posizione decimale è caratterizzata da un valore massimo di 1,1 km: consente di dividere un paese dall'altro.
* La terza posizione decimale è caratterizzata da un valore massimo di 110 m: consente di identificare un campo agricolo o una sede istituzionale.
* La quarta posizione decimale è caratterizzata da un valore massimo di 11 m: consente di identificare una porzione di terreno. La sua accuratezza è paragonabile a quella di un'unità GPS non corretta e senza interferenze.
* La quinta posizione decimale è caratterizzata da un valore massimo di 1,1 m e consente di distinguere un albero da un altro. Un'accuratezza di questo tipo, con le unità GPS commerciali, può essere raggiunta soltanto con una correzione differenziale.
* Il sesto numero decimale è un valore fino a 0,11 m: è possibile usare questo livello per definire in dettaglio le strutture, per progettare i paesaggi, costruire strade. È più che sufficiente per rilevare i movimenti dei ghiacciai e dei fiumi. Questo obiettivo può essere effettuato eseguendo misure accurate con il GPS, ad esempio il GPS con correzione differenziale.

Le informazioni sulla posizione possono essere inserite nelle funzionalità separando le informazioni su regioni, posizioni e città. Una volta può anche chiamare un endpoint REST, ad esempio l'API di Bing Maps (vedere `https://msdn.microsoft.com/library/ff701710.aspx` per ottenere le informazioni sull'area/quartiere).

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
> A livello di programmazione, è possibile inserire i record usando il linguaggio preferito. Potrebbe essere necessario inserire i dati in blocchi per migliorare l'efficienza di scrittura. [Ecco un esempio di come procedere usando pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Un'altra alternativa consiste nell'inserire i dati nel database usando l' [utilità BCP](/sql/tools/bcp-utility)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Connessione ad Azure Machine Learning
La funzionalità appena creata può essere aggiunta come una colonna a una tabella esistente oppure archiviata in una nuova tabella e unita a quella originale ai fini dell'apprendimento automatico. È possibile creare o accedere alle funzionalità già create utilizzando il modulo [Import Data](/azure/machine-learning/studio-module-reference/import-data) in Azure ML, come descritto di seguito:

![Lettori di Azure ML](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Utilizzo di un linguaggio di programmazione quale Python
L'uso di Python per creare funzionalità quando i dati si trovano in SQL Server funziona in modo analogo all'elaborazione dei dati nei BLOB di Azure tramite Python. Per il confronto, vedere [Elaborare i dati BLOB di Azure nell'ambiente data science](data-blob.md). Caricare i dati dal database in un frame di dati pandas per elaborarli più approfonditamente. In questa sezione, è stato descritto il processo di connessione al database e di caricamento dati all'interno di un frame di dati.

Il seguente formato della stringa di connessione può essere usato per connettersi a un database di SQL Server da Pyhton usando pyodbc (sostituire il nome del server, quello del database, il nome utente e la password con i valori personalizzati):

```python
#Set up the SQL Azure connection
import pyodbc
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

La [libreria Pandas](https://pandas.pydata.org/) in Python fornisce una vasta gamma di strutture di dati e strumenti di analisi dei dati per la manipolazione dei dati per la programmazione Python. Il codice seguente consente di leggere i risultati restituiti da un database di SQL Server all'interno di un frame di dati di Pandas:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

A questo punto è possibile usare il frame di dati di Pandas, come descritto nell'argomento relativo alla [creazione di funzionalità per i dati di archiviazione BLOB di Azure tramite Panda](./explore-data-blob.md).