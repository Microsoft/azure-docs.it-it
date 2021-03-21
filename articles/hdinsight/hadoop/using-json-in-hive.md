---
title: Analizzare & elaborare JSON con Apache Hive-Azure HDInsight
description: Informazioni su come usare i documenti JSON e analizzarli usando Apache Hive in Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 5bc9acea219e5d111700840149a26c127b47514d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943057"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Elaborare e analizzare documenti JSON tramite apache Hive in HDInsight di Azure

Informazioni su come elaborare e analizzare i file JavaScript Object Notation (JSON) tramite Apache Hive in HDInsight di Azure. Questo articolo usa il documento JSON seguente:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Il file è disponibile in `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Per altre informazioni su come usare l'archivio BLOB di Azure con HDInsight, vedere [Usare un archivio BLOB di Azure compatibile con Hadoop Distributed File System con Apache Hadoop in HDInsight](../hdinsight-hadoop-use-blob-storage.md). È possibile copiare il file nel contenitore predefinito del cluster.

In questo articolo si usa la console di Apache Hive. Per istruzioni su come aprire la console hive, vedere [usare la visualizzazione hive di Apache Ambari con Apache Hadoop in HDInsight](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> La vista Hive non è più disponibile in HDInsight 4.0.

## <a name="flatten-json-documents"></a>Rendere flat i documenti JSON

I metodi elencati nella sezione successiva richiedono che il documento JSON sia costituito da una singola riga. È quindi necessario rendere flat il documento JSON in una stringa. Se il documento JSON è già flat, è possibile saltare questo passaggio e passare alla sezione successiva relativa all'analisi dei dati JSON. Per rendere flat il documento JSON, eseguire lo script seguente:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Il file JSON non elaborato è disponibile in `wasb://processjson@hditutorialdata.blob.core.windows.net/`. La tabella hive **studentsraw punta al** punta al documento JSON non elaborato che non è bidimensionale.

La tabella Hive **StudentsOneLine** archivia i dati nel file system predefinito di HDInsight nel percorso **/json/students/**.

L'istruzione **INSERT** popola la tabella **StudentOneLine** con i dati JSON flat.

L'istruzione **SELECT** restituisce solo una riga.

Ecco l'output dell'istruzione **SELECT**:

![HDInsight Flat The JSON Document](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analizzare i documenti JSON in Hive

Hive offre tre meccanismi diversi per l'esecuzione di query nei documenti JSON, oppure è possibile scrivere:

* Usare la funzione definita dall'utente get_json_object.
* Usare la funzione definita dall'utente json_tuple.
* Usare il Serializzatore/Deserializzatore personalizzato (SerDe).
* Scrivere una funzione definita dall'utente personalizzata tramite Python o altri linguaggi. Per altre informazioni su come eseguire il codice Python con Hive, vedere [Funzione definita dall'utente Python con Apache Hive e Apache Pig](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Usare la funzione definita dall'utente get_json_object

Hive offre una funzione definita dall'utente predefinita denominata [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) che esegue query JSON durante il Runtime. Questo metodo accetta due argomenti: il nome della tabella e il nome del metodo. Il nome del metodo include il documento JSON flat e il campo JSON che deve essere analizzato. Viene ora esaminato un esempio per verificare il funzionamento di questa funzione definita dall'utente.

La query seguente restituisce il nome e il cognome di ogni studente:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Questo è l'output ottenuto quando si esegue la query nella finestra della console:

![Apache Hive ottiene UDF oggetto JSON](./media/using-json-in-hive/hdinsight-get-json-object.png)

La funzione definita dall'utente get-json_object presenta delle limitazioni:

* Poiché ogni campo della query richiede una nuova analisi della query, si ha un impatto sulle prestazioni.
* **GET\_JSON_OBJECT()** restituisce la rappresentazione di stringa di una matrice. Per convertirla in una matrice Hive, è necessario usare espressioni regolari per sostituire le parentesi quadre "[" and "]" e quindi è necessario anche chiamare anche una suddivisione per ottenere la matrice.

Questa conversione è il motivo per cui il wiki di hive consiglia di usare **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Usare la funzione definita dall'utente json_tuple

Un altro UDF fornito da hive è denominato [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), che offre prestazioni migliori rispetto [get_ _object JSON](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Questo metodo accetta un set di chiavi e una stringa JSON. Restituisce quindi una tupla di valori. La query seguente restituisce l'ID dello studente e il livello dal documento JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Output dello script nella console di Hive:

![Risultati della query Apache Hive JSON](./media/using-json-in-hive/hdinsight-json-tuple.png)

La `json_tuple` UDF usa la sintassi di [visualizzazione laterale](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) in hive, che consente \_ a JSON Tuple di creare una tabella virtuale applicando la funzione UDT a ogni riga della tabella originale. I documenti JSON complessi diventano troppo difficili da gestire a causa dell'uso ripetuto di **LATERAL VIEW**. Inoltre, **JSON_TUPLE** non è in grado di gestire JSON annidati.

### <a name="use-a-custom-serde"></a>Usare un'interfaccia SerDe personalizzata

SerDe è la scelta migliore per l'analisi dei documenti JSON nidificati. Consente di definire lo schema JSON e quindi è possibile usare lo schema per analizzare i documenti. Per istruzioni, vedere [Come usare un Serde JSON personalizzato con Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Riepilogo

Il tipo di operatore JSON in hive scelto dipende dallo scenario. Con un semplice documento JSON e un campo da cercare, scegliere la **GET_JSON_OBJECT** UDF di hive. Se è necessario cercare più di una chiave, è possibile usare **json_tuple**. Per i documenti annidati, usare il **SerDe JSON**.

## <a name="next-steps"></a>Passaggi successivi

Per gli articoli correlati, vedere:

* [Usare Apache Hive e HiveQL con Apache Hadoop in HDInsight per analizzare un file Apache log4j di esempio](./hdinsight-use-hive.md)
* [Analizzare i dati sui ritardi dei voli usando Interactive query in HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)