---
title: Analizzare i dati di Twitter con Apache Hive - Azure HDInsight
description: Informazioni su come usare Apache Hive e Apache Hadoop in HDInsight per trasformare i dati di Twitter non elaborati in una tabella Hive ricercabile.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: f3705170be28f33e5994bd00e363dc7ec7f94642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435606"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analizzare i dati di Twitter mediante Apache Hive e Apache Hadoop in HDInsight

Scopri come utilizzare [Apache Hive](https://hive.apache.org/) per elaborare i dati di Twitter. Il risultato è un elenco di utenti Twitter che hanno inviato il maggior numero di tweet contenenti una determinata parola.

> [!IMPORTANT]  
> I passaggi descritti in questo documento sono stati testati in HDInsight 3.6.

## <a name="get-the-data"></a>Ottenere i dati

Twitter consente di recuperare i dati relativi ad ogni tweet come documento JSON (JavaScript Object Notation) attraverso un'API REST. [OAuth](https://oauth.net) .

### <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter

1. Da un Web browser [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/)accedere a . Seleziona il link **Iscriviti ora** se non hai un account Twitter.

2. Selezionare **Crea nuova app**.

3. Compilare i campi **Name**, **Description**, **Website**. Per il campo **Website** è possibile creare un URL fittizio. Nella tabella seguente vengono mostrati alcuni valori di esempio da usare:

   | Campo | valore |
   |--- |--- |
   | Nome |MyHDInsightApp |
   | Descrizione |MyHDInsightApp |
   | Website |`https://www.myhdinsightapp.com` |

4. Selezionare **Sì, accetto**, quindi **Crea applicazione Twitter**.

5. Selezionare la scheda **Autorizzazioni.** L'autorizzazione predefinita è **Sola lettura**.

6. Selezionare la scheda **Keys and Access Tokens** .

7. Selezionare **Crea token di accesso**.

8. Selezionare **Test OAuth** nell'angolo superiore destro della pagina.

9. Compilare i campi **Consumer key**, **Consumer secret**, **Access token** e **Access token secret**.

### <a name="download-tweets"></a>Scaricare tweet

Il codice Python seguente consente di scaricare 10.000 tweet da Twitter e salvarli in un file denominato **tweets.txt**.

> [!NOTE]  
> I passaggi indicati di seguito vengono eseguiti nel cluster HDInsight, poiché Python è già installato.

1. Usare [il comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster, quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Utilizzare i seguenti comandi per installare [Tweepy,](https://www.tweepy.org/) [Progress bar](https://pypi.python.org/pypi/progressbar/2.2)e altri pacchetti obbligatori:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. Usare il comando seguente per creare un file denominato **gettweets.py**:

   ```bash
   nano gettweets.py
   ```

1. Modificare il codice riportato `Your access token`di `Your access token secret` seguito sostituendo `Your consumer secret`, `Your consumer key`, e con le informazioni pertinenti dell'applicazione Twitter. Quindi incolla il codice modificato come contenuto del file **gettweets.py.**

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > Modificare il filtro di argomenti nell'ultima riga per tenere traccia delle parole chiave comuni. L'uso delle parole chiave più diffuse al momento dell'esecuzione dello script consente di acquisire più velocemente i dati.

1. Usare **Ctrl + X**, quindi premere **Y** per salvare il file.

1. Usare il comando seguente per aprire il file e scaricare i tweet:

    ```bash
    python gettweets.py
    ```

    Viene visualizzato un indicatore di stato che mostra un valore fino al 100% mentre vengono scaricati i tweet.

   > [!NOTE]  
   > Se l'indicatore di stato impiega molto tempo per avanzare, è necessario modificare il filtro per monitorare gli argomenti di tendenza. Quando ci sono molti tweet sull'argomento nel filtro, è possibile ottenere rapidamente i 100 tweet necessari.

### <a name="upload-the-data"></a>Caricare i dati

Usare i comandi seguenti per aggiornare i dati nell'archiviazione HDInsight:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Questi comandi archiviano i dati in un percorso accessibile a tutti i nodi del cluster.

## <a name="run-the-hiveql-job"></a>Eseguire il processo di HiveQL

1. Utilizzare il comando seguente per creare un file contenente istruzioni [HiveQL:Use](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) the following command to create a file containing HiveQL statements:

   ```bash
   nano twitter.hql
   ```

    Usare il testo seguente come contenuto del file:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. Premere **Ctrl + X**, quindi **Y** per salvare il file.

1. Usare il comando riportato di seguito per eseguire lo script HiveQL contenuto nel file:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Questo comando esegue il file **twitter.hql**. Dopo il completamento della query, viene visualizzato un prompt `jdbc:hive2//localhost:10001/>`.

1. Dal prompt dei comandi Beeline usare la query seguente per verificare che i dati siano stati importati:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Viene restituito un massimo di 10 tweet contenenti la parola **Azure** nel testo del messaggio.

    > [!NOTE]  
    > Se è stato modificato il filtro nello script `gettweets.py`, sostituire **Azure** con uno dei filtri usati.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come trasformare un set di dati JSON non strutturato in una tabella [Apache Hive](https://hive.apache.org/) strutturata. Per altre informazioni su Hive in HDInsight, vedere i documenti seguenti:

* [Introduzione a HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analizzare i dati sui ritardi dei voli con HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
