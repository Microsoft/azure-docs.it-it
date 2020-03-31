---
title: 'Riferimento: CentOS Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Dettagli sugli strumenti inclusi nella macchina virtuale CentOS Data Science
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: db49a9f5e0e6675d93cb58d6af9c92fac21e8b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525839"
---
# <a name="reference-centos-linux-data-science-virtual-machine"></a>Riferimento: Macchina virtuale di analisi scientifica dei dati CentOS (Linux)

La Data Science Virtual Machine (DSVM) Linux è un'immagine di macchina virtuale di Azure basata su CentOS. La DSVM Linux include una raccolta di strumenti preinstallati che è possibile usare per l'analisi dei dati e Machine Learning. 

I principali componenti software inclusi in una DSVM Linux sono:

* Sistema operativo della distribuzione Linux CentOS.
* Microsoft Machine Learning Server.
* Distribuzione di Anaconda Python, versioni 3.5 e 2.7, incluse le più comuni librerie di analisi dei dati.
* JuliaPro, un'accurata distribuzione del linguaggio Julia con librerie di analisi dei dati e scientifiche ampiamente diffuse.
* Istanza di Spark autonomo e Hadoop a nodo singolo (HDFS, YARN).
* JupyterHub, un server Jupyter Notebook multiutente che supporta kernel R, Python, PySpark e Julia.
* Azure Storage Explorer
* Interfaccia della riga di comando di Azure per la gestione delle risorse di Azure.
* Database PostgresSQL.
* Strumenti di Machine Learning:
  * [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) (CNTK), un toolkit software di Deep Learning sviluppato da Microsoft Research.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit), un sistema veloce di Machine Learning che supporta tecniche come online, hashing, allreduce, reduction, learning2search e apprendimento attivo e interattivo.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/), uno strumento che consente un'implementazione rapida e accurata di albero con boosting rapida e accurata.
  * [Rattle](https://togaware.com/rattle/), uno strumento che consente di iniziare a usare con facilità l'analisi dei dati e Machine Learning in R. Rattle offre funzionalità di esplorazione e modellazione dei dati basate su GUI tramite la generazione automatica di codice R.
* Azure SDK in Java, Python, Node.js, Ruby e PHP.
* Librerie in R e Python da usare in Azure Machine Learning e altri servizi di Azure.
* Editor e strumenti di sviluppo (RStudio, PyCharm, IntelliJ, Emacs, gedit, vi).

La data science comporta l'iterazione di una sequenza di attività quali:

1. Trovare, caricare e preelaborare i dati.
1. Compilare e testare i modelli.
1. Distribuire i modelli per l'uso in applicazioni intelligenti.

Gli esperti di dati usano vari strumenti per completare queste attività. Trovare le versioni corrette del software e quindi scaricarle, compilarle e installarle può essere un'operazione dispersiva in termini di tempo.

La DSVM Linux può semplificare questo carico in modo sostanziale. Usarla per avviare rapidamente i progetti di analisi. La DSVM Linux consente di svolgere attività in diversi linguaggi, ad esempio R, Python, SQL, Java e C++. Eclipse offre un IDE di facile utilizzo per lo sviluppo e il test del codice. Azure SDK, incluso nella DSVM, consente di compilare le applicazioni usando vari servizi in Linux sulla piattaforma cloud Microsoft. Sono preinstallati altri linguaggi, tra cui Ruby, Perl, PHP e Node.js.

Per questa immagine di DSVM non sono previsti costi per il software. Si pagano solo le spese d'uso dell'hardware di Azure, valutate in base alle dimensioni della macchina virtuale di cui si esegue il provisioning con l'immagine della DSVM. Per altre informazioni sui costi delle risorse di calcolo, vedere il [listino della Data Science Virtual Machine per Linux (CentOS)](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) in Azure Marketplace.


## <a name="machine-learning-server"></a>Machine Learning Server

R è uno dei linguaggi più diffusi per l'analisi dei dati e il Machine Learning. Se si vuole usare R per l'esecuzione di analisi, è necessario che nella DSVM sia installato Machine Learning Server con Microsoft R Open e Math Kernel Library. Math Kernel Library ottimizza le operazioni matematiche comuni negli algoritmi analitici. R Open è pienamente compatibile con CRAN-R. Tutte le librerie R pubblicate in CRAN possono essere installate in R Open. 

È possibile usare Machine Learning Server per ridimensionare e operazionalizzare i modelli R in servizi Web. È possibile modificare i programmi R in uno degli editor predefiniti, ad esempio RStudio, VI o Emacs. L'editor Emacs è preinstallato nella DSVM. Il pacchetto Emacs ESS (Emacs Speaks Statistics) semplifica l'uso di file R nell'editor Emacs.

Per aprire la console R, nella shell immettere **R**. Questo comando consente di passare a un ambiente interattivo. Per sviluppare il programma R, si usa in genere un editor come Emacs o vi e quindi si eseguono gli script all'interno di R. Con RStudio si ottiene un IDE completo con interfaccia grafica per sviluppare programmi R.

Nella DSVM è incluso uno script R che è possibile usare per installare i [principali 20 pacchetti](https://www.kdnuggets.com/2015/06/top-20-r-packages.html). È possibile eseguire questo script nell'interfaccia interattiva di R. Come indicato in precedenza, per aprire tale interfaccia, immettere **R** nella shell.  

## <a name="python"></a>Python

Anaconda Python viene installato con gli ambienti Python 3.5 e 2.7. L'ambiente 2.7 è chiamato _root_ e l'ambiente 3.5 è chiamato _py35_. Questa distribuzione contiene il linguaggio Python di base con circa 300 dei più diffusi pacchetti di matematica, ingegneria e analisi dei dati.

L'ambiente py35 è il valore predefinito. Per attivare l'ambiente radice (2.7), usare questo comando:

```bash
source activate root
```

Per attivare di nuovo l'ambiente py35, usare questo comando:

```bash
source activate py35
```

Per richiamare la sessione interattiva di Python, immettere **python** nella shell. 

Installare altre librerie di Python usando Conda o pip. Per pip, attivare innanzitutto l'ambiente corretto se non si vuole usare il valore predefinito:

```bash
source activate root
pip install <package>
```

In alternativa, specificare il percorso completo a pip:

```bash
/anaconda/bin/pip install <package>
```

Per Conda, è necessario specificare sempre il nome dell'ambiente (py35 oppure radice):

```bash
conda install <package> -n py35
```

Se si usa un'interfaccia grafica o è configurato X11 Forwarding, è possibile immettere il comando **pycharm** per aprire l'IDE PyCharm di Python. È possibile usare gli editor di testo predefiniti. È inoltre possibile usare Spyder, un IDE Python incluso nelle distribuzioni di Anaconda Python. Spyder richiede un desktop con interfaccia grafica o X11 Forwarding. Nel desktop con interfaccia grafica è disponibile un collegamento a Spyder.

## <a name="jupyter-notebook"></a>Notebook Jupyter

La distribuzione Anaconda include anche Jupyter Notebook, un ambiente per condividere codice e analisi. Accedere a Jupyter Notebook tramite JupyterHub. Per eseguire l'accesso, usare il nome utente e la password locali di Linux.

Il server Jupyter Notebook è preconfigurato con i kernel Python 2, Python 3 e R. Usare l'icona del desktop **Jupyter Notebook** per aprire il browser e accedere al server Jupyter Notebook. Se si accede alla DSVM tramite SSH o il client X2Go, è anche possibile accedere al server Jupyter Notebook all'indirizzo \/https://localhost:8000/.

> [!NOTE]
> Se vengono visualizzati avvisi relativi al certificato, scegliere di continuare.

È possibile accedere al server Jupyter Notebook da qualsiasi host, Immettere **\//\<https: nome DNS DSVM o\>indirizzo IP :8000/**.

> [!NOTE]
> La porta 8000 è aperta nel firewall per impostazione predefinita quando viene effettuato il provisioning della DSVM. 

Microsoft ha incluso nel pacchetto i notebook di esempio: uno in Python e uno in R. È possibile visualizzare il collegamento agli esempi nella home page di Jupyter Notebook dopo aver eseguito l'autenticazione in Jupyter Notebook con il nome utente e la password locali di Linux. Per creare un nuovo notebook, selezionare **Nuovo**e quindi selezionare il kernel del linguaggio da usare. Se il pulsante **Nuovo** non è visualizzato, selezionare l'icona **Jupyter** in alto a sinistra per passare alla home page del server notebook.

## <a name="spark-standalone"></a>Spark autonomo 

Un'istanza della modalità autonoma di Spark è preinstallata nella DSVM Linux per agevolare lo sviluppo di applicazioni Spark in locale prima di testarle e distribuirle in cluster di grandi dimensioni. 

È possibile eseguire programmi PySpark attraverso il kernel Jupyter. Quando si apre Jupyter, selezionare il pulsante **Nuovo** per visualizzare un elenco di kernel disponibili. **Spark - Python** è il kernel PySpark che consente di creare applicazioni Spark usando il linguaggio Python. È anche possibile usare un IDE Python come PyCharm o Spyder per creare un programma Spark. 

Nell'istanza autonoma lo stack Spark viene eseguito nel programma client chiamante. Questa funzionalità velocizza e semplifica la risoluzione dei problemi rispetto allo sviluppo in un cluster Spark.

Jupyter fornisce un notebook PySpark di esempio. Si trova nella directory SparkML all'interno della d home directory di Jupyter ($HOME/notebooks/SparkML/pySpark). 

Se si programma in R per Spark, è possibile usare Machine Learning Server, SparkR o sparklyr. 

Prima di operare in un contesto Spark in Machine Learning Server, è necessario eseguire un unico passaggio di configurazione per abilitare un'istanza locale di HDFS e YARN Hadoop a nodo singolo. Per impostazione predefinita, i servizi Hadoop sono installati ma disabilitati su DSVM. Per abilitare il servizi Hadoop, eseguire i comandi seguenti come radice la prima volta:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

È possibile arrestare i servizi correlati a Hadoop quando non sono necessari eseguendo `systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn`.

La directory /dsvm/samples/MRS contiene un esempio che illustra come sviluppare e testare Machine Learning Server in un contesto Spark remoto (l'istanza autonoma di Spark nella DSVM).

## <a name="ides-and-editors"></a>IDE ed editor

È possibile scegliere tra diversi editor di codice, tra cui vi/VIM, Emacs, gedit, PyCharm, RStudio, Eclipse, LaTeX e IntelliJ. 

* gedit, Eclipse, IntelliJ, R Studio e PyCharm sono editor grafico. Per usarli, è necessario aver eseguito l'accesso a un desktop con interfaccia grafica. Per aprirli, usare i collegamenti del desktop e del menu dell'applicazione.

* VIM e Emacs sono editor basati su testo. In Emacs il pacchetto di componenti aggiuntivi ESS semplifica l'uso di R nell'editor Emacs. Per altre informazioni, visitare il [sito Web ESS](https://ess.r-project.org/).

* Eclipse è un IDE estensibile open source che supporta più linguaggi. L'IDE Eclipse per sviluppatori Java è la versione installata nella DSVM. Per estendere l'ambiente, è possibile installare i plug-in per diversi linguaggi comuni. 

  Il plug-in Azure Toolkit for Eclipse viene installato con Eclipse nella DSVM. È possibile usarlo per creare, sviluppare, testare e distribuire applicazioni Azure tramite l'ambiente di sviluppo Eclipse che supporta linguaggi come Java.

  Con Azure Toolkit for Eclipse, nella DSVM viene installato anche Azure SDK per Java, che fornisce l'accesso a diversi servizi di Azure da un ambiente Java. 
  
  Per ulteriori informazioni, vedere [Toolkit di Azure per Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

* LaTeX viene installato tramite il pacchetto texlive, insieme a un pacchetto di componenti aggiuntivi Emacs denominato [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Questo pacchetto semplifica la creazione di documenti LaTeX in Emacs. 

## <a name="databases"></a>Database

La DSVM Linux consente di accedere a diversi strumenti di database e da riga di comando.

### <a name="postgressql"></a>PostgresSQL

Il database open source PostgresSQL è disponibile nella DSVM con i servizi in esecuzione e initdb completati. È necessario creare database e utenti. Per altre informazioni, vedere la [documentazione di PostgresSQL](https://www.postgresql.org/docs/).  

### <a name="squirrel-sql"></a>SQuirreL SQL

SQuirreL SQL è un client SQL con interfaccia grafica che può connettersi a vari database (inclusi SQL Server, PostgreSQL e MySQL) ed eseguire query SQL. È possibile eseguire SQuirrel SQL da una sessione desktop con interfaccia grafica (ad esempio tramite il client X2Go) usando un'icona del desktop. In alternativa, è possibile eseguire il client usando il comando seguente nella shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh /usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Prima di usarlo per la prima volta, è necessario configurare i driver e gli alias di database. I driver JDBC si trovano nel percorso /usr/share/java/jdbcdrivers.

Per altre informazioni, vedere [SQuirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-sql-server"></a>Strumenti da riga di comando per l'accesso a SQL Server

Anche nel pacchetto driver ODBC per SQL Server sono disponibili due strumenti da riga di comando:

* **bcp**: lo strumento bcp copia in blocco i dati tra un'istanza di SQL ServerSQL Server e un file di dati in un formato specificato dall'utente. È possibile usarlo per importare un numero elevato di nuove righe nelle tabelle di SQL Server o per esportare dati delle tabelle in file di dati. Per importare dati in una tabella, è necessario usare un file in un formato creato per tale tabella. In alternativa, è necessario conoscere la struttura della tabella e i tipi di dati validi per le relative colonne.

  Per altre informazioni, vedere [Connettersi con bcp](https://msdn.microsoft.com/library/hh568446.aspx).

* **sqlcmd**: è possibile utilizzare l'utilità sqlcmd per immettere istruzioni Transact-SQLTransact-SQL, procedure di sistema e file di script al prompt dei comandi. L'utilità sqlcmd usa ODBC per eseguire batch Transact-SQL.

  Per altre informazioni, vedere [Connettersi con sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

  > [!NOTE]
  > Questo strumento presenta alcune differenze tra le piattaforme Linux e Windows. Per informazioni dettagliate, vedere la documentazione di .

### <a name="database-access-libraries"></a>Librerie di accesso al database

Le librerie per l'accesso al database sono disponibili in R e Python:

* In R è possibile usare il pacchetto RODBC o dplyr per eseguire query o istruzioni SQL sul server di database.
* In Python la libreria pyodbc fornisce l'accesso al database con ODBC come livello sottostante.

## <a name="azure-tools"></a>Strumenti di Azure

Nella DSVM sono installati gli strumenti di Azure seguenti:

* **Interfaccia della riga di comando**di Azure: è possibile usare l'interfaccia della riga di comando in Azure per creare e gestire le risorse di Azure tramite comandi della shell. Per aprire gli strumenti di Azure, immettere **Azure help**. Per altre informazioni, vedere la [pagina di documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* **Azure Storage Explorer:** Azure Storage Explorer è uno strumento grafico che è possibile usare per esplorare gli oggetti archiviati nell'account di archiviazione di Azure e per caricare e scaricare dati da e verso i BLOB di Azure.Azure Storage Explorer : Azure Storage Explorer is a graphical tool that you can use to browse through the objects that you have stored in your Azure storage account, and to upload and download data to and from Azure blobs. È possibile accedere a Esplora archivi dall'icona del collegamento sul desktop. È anche possibile aprire questo strumento da un prompt della shell digitando **StorageExplorer**. È necessario aver eseguito l'accesso da un client X2Go o avere configurato X11 Forwarding.
* **Librerie di Azure:** le librerie seguenti sono preinstallate in DSVM:Azure libraries : The following libraries are preinstalled on the DSVM:
  
  * **Python**: le librerie correlate ad Azure in Python sono *azure*, *azureml*, *pydocumentdb*e *pyodbc*. Le prime tre librerie consentono di accedere ai servizi di archiviazione di Azure, Azure Machine Learning e Azure Cosmos DB, ovvero un database NoSQL in Azure. La quarta libreria, pyodbc (insieme ai driver Microsoft ODBC per SQL Server), consente l'accesso da Python a SQL Server, al database SQL di Azure e ad Azure SQL Data Warehouse tramite un'interfaccia ODBC. Immettere **pip list** per vedere elencate tutte le librerie. Assicurarsi di eseguire questo comando in Python sia nell'ambiente 2.7 che 3.5.
  * **R**: Le librerie correlate ad Azure in R sono AzureML e RODBC.R : The Azure-related libraries in R are AzureML and RODBC.
  * **Java:** l'elenco delle librerie Java di Azure è disponibile nella directory /dsvm/sdk/AzureSDKJava nella dSVM. Le librerie principali sono le API di archiviazione e gestione di Azure, Azure Cosmos DB e i driver JDBC per SQL Server.  

È possibile accedere al [portale di Azure](https://portal.azure.com) dal browser Firefox preinstallato. Nel portale di Azure si possono creare, gestire e monitorare le risorse di Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning è un servizio cloud completamente gestito che è possibile usare per creare, distribuire e condividere soluzioni di analisi predittiva. È possibile compilare gli esperimenti e i modelli da Azure Machine Learning Studio (classico). Per accedere a Azure Machine Learning da un Web browser in DSVM, passare a [Microsoft Azure Machine Learning](https://studio.azureml.net).

Dopo aver eseguito l'accesso ad Azure Machine Learning Studio (classico), è possibile usare un canvas di sperimentazione per creare un flusso logico per gli algoritmi di apprendimento automatico. È anche possibile accedere a un'istanza di Jupyter Notebook ospitata in Azure Machine Learning. Il blocco appunti può funzionare senza problemi con gli esperimenti in Azure Machine Learning Studio (classico). 

Operazionalizzare i modelli di Machine Learning creati eseguendone il wrapping in un'interfaccia del servizio Web. In questo modo i client scritti in qualsiasi linguaggio possono richiamare le stime dai modelli di Machine Learning. Per altre informazioni, vedere [Documentazione su Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

È anche possibile creare modelli personalizzati in R o Python nella DSVM e quindi distribuirli nell'ambiente di produzione in Azure Machine Learning. Per supportare questa funzionalità, Microsoft ha installato librerie in R (**AzureML**) e Python (**azureml**).

Per informazioni su come distribuire i modelli in R e Python in Azure Machine Learning, vedere [Dieci cose da fare con una Data Science Virtual Machine](vm-do-ten-things.md).

> [!NOTE]
> Le istruzioni riportate in questo articolo[Dieci cose da fare con una Data Science Virtual Machine](vm-do-ten-things.md)sono destinate alla versione Windows della DSVM. Tuttavia, le informazioni sulla distribuzione di modelli in Azure Machine Learning si applicano anche alla DSVM Linux.

## <a name="machine-learning-tools"></a>Strumenti di Machine Learning

La DSVM include alcuni strumenti e algoritmi di Machine Learning precompilati e preinstallati localmente. incluse le seguenti:

* **Microsoft Cognitive Toolkit**: Un toolkit di deep learning.
* **Vowpal Wabbit**: algoritmo di apprendimento rapido online.
* **XGBoost**: Uno strumento che fornisce algoritmi di albero ottimizzati e potenziati.
* **Python**: Anaconda Python integra algoritmi Machine Learning con librerie come Scikit-learn. È possibile installare altre librerie usando il comando `pip install` .
* **R**: Una libreria completa di funzioni di apprendimento automatico è disponibile per le librerie R. Preinstalled includono lm, glm, randomForest e rpart. È possibile installare altre librerie eseguendo `install.packages(<lib name>)`.

Microsoft Cognitive Toolkit, Vowpal Wabbit e XGBoost vengono descritti in maggior dettaglio nelle sezioni successive.

### <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

Microsoft Cognitive Toolkit è un toolkit open source di Deep Learning. Si tratta di uno strumento da riga di comando già incluso in PATH.

Per un esempio di base, eseguire i comandi seguenti nella shell:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Per altre informazioni, vedere il [repository CNTK di GitHub](https://github.com/Microsoft/CNTK) e il [wiki di CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit è un sistema di Machine Learning che usa tecniche come online, hashing, allreduce, reduction, learning2search, nonché apprendimento attivo e interattivo.

Per eseguire lo strumento con un esempio di base, usare i comandi seguenti:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

La directory di demo di Vowpal Wabbit include altre demo più grandi. Per altre informazioni su Vowpal Wabbit, vedere il [repository Vowpal Wabbit di GitHub](https://github.com/JohnLangford/vowpal_wabbit) e il [wiki di Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>XGBoost

La libreria XGBoost è progettata e ottimizzata per gli algoritmi di albero con boosting. L'obiettivo di questa libreria consiste nello spingere i limiti di calcolo dei computer fino ai massimi livelli necessari per fornire una funzionalità di boosting degli alberi su larga scala che sia portabile, scalabile e accurata.

XGBoost è disponibile sia come riga di comando che come libreria R.

Per usare la libreria XGBoost in R, avviare una sessione R interattiva (immettere **R** nella shell), quindi caricare la libreria.

Ecco un semplice esempio eseguibile al prompt di R:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Per eseguire la riga di comando di XGBoost, usare i comandi seguenti nella shell:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Nella directory specificata viene scritto un file con estensione model. Per informazioni su questo esempio di demo in GitHub, vedere [Classificazione binaria](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Per altre informazioni su XGBoost, vedere la [documentazione di XGBoost](https://xgboost.readthedocs.org/en/latest/) e il [repository di GitHub XGBoost](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle (*R* *A*nalytical *T*ool *T*o *L*earn *E*asily) usa la funzionalità di esplorazione e modellazione dei dati basate su GUI. Rattle:
- Presenta riepiloghi statistici e visivi dei dati.
- Trasforma i dati che possono essere modellati rapidamente.
- Crea modelli con e senza supervisione dai dati.
- Presenta le prestazioni dei modelli graficamente.
- Assegna punteggi ai nuovi set di dati.
- Genera codice R.
- Replica le operazioni nell'interfaccia utente che possono essere eseguite direttamente in R o usate come punto di partenza per altre analisi.

Per eseguire Rattle, è necessario aver eseguito l'accesso a un desktop con interfaccia grafica. In un terminale immettere **R** per aprire l'ambiente R. Al prompt di R immettere i comandi seguenti:

```R
library(rattle)
rattle()
```

Si apre un'interfaccia grafica con un set di schede. Seguire questi passaggi di avvio rapido in Rattle per usare un set di dati meteo di esempio e creare un modello. In alcuni passaggi viene chiesto di installare e caricare automaticamente specifici pacchetti R necessari non ancora disponibili nel sistema.

> [!NOTE]
> Se non si hanno le autorizzazioni per installare il pacchetto nella directory di sistema (impostazione predefinita), è possibile che nella finestra della console R venga visualizzata la richiesta di installare i pacchetti nella libreria personale. In questo caso, immettere **y** (Sì).

1. Scegliere **Execute**(Esegui).
1. Viene visualizzata una finestra di dialogo che chiede di caricare il set di dati meteo di esempio. Selezionare **Yes** (Sì) per caricare l'esempio.
1. Selezionare la scheda **Model** (Modello).
1. Selezionare **Execute** (Esegui) per creare un albero delle decisioni.
1. Selezionare **Draw** (Progetta) per visualizzare l'albero delle decisioni.
1. Selezionare l'opzione **Forest** (Foresta) e quindi **Execute** per creare una foresta casuale.
1. Selezionare la scheda **Evaluate** (Valuta).
1. Selezionare l'opzione **Risk** (Rischio) e quindi **Execute** per visualizzare due tracciati delle prestazioni per **Risk (Cumulative)** (Rischio cumulativo).
1. Selezionare la scheda **Log** per visualizzare il codice R generato per le operazioni precedenti. (A causa di un bug nella versione corrente di **#** Rattle, è necessario inserire un carattere davanti **a Esporta questo registro** nel testo del registro.)
1. Selezionare il pulsante **Export** (Esporta) per salvare il file di script R denominato *weather_script.R* nella home directory.

È possibile uscire da Rattle e R. A questo punto è possibile modificare lo script R generato. In alternativa, usare lo script così com'è ed eseguirlo in qualsiasi momento per ripetere tutti i passaggi eseguiti nell'interfaccia utente di Rattle. Si tratta di un modo rapido, specialmente per gli utenti meno esperti di R, per eseguire funzioni di analisi e Machine Learning in un'interfaccia grafica semplice, generando automaticamente codice in R da modificare o per l'apprendimento.

## <a name="next-steps"></a>Passaggi successivi

Altre domande? Prendere in considerazione la creazione di un ticket di [supporto](https://azure.microsoft.com/support/create-ticket/).