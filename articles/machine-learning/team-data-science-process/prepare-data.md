---
title: Preparare i dati per ML Studio (classico) - Team Data Science Process
description: Pre-elaborare e pulire i dati in modo da prepararli per un uso efficiente nei processi di Machine Learning.
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
ms.openlocfilehash: caedcf313ab809e9607907545f26ca1b62bbeca7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720045"
---
# <a name="tasks-to-prepare-data-for-enhanced-machine-learning"></a>Attività per preparare i dati per operazioni avanzate con Machine Learning
I dati di pre-elaborazione e pulizia sono attività importanti che devono essere eseguite prima di poter usare un set di dati per il training del modello. I dati non elaborati sono fastidiosi, non affidabili e potrebbero non contenere alcuni valori. Utilizzare tali dati per la modellazione può produrre risultati fuorvianti. Queste attività fanno parte del Processo di analisi scientifica dei dati per i team (TDSP) e generalmente seguono un'analisi iniziale di un set di dati usato per individuare e pianificare la pre-elaborazione necessaria. Per altre istruzioni su TDSP, vedere i passaggi descritti nell'articolo relativo al [Processo di analisi scientifica dei dati per i team](overview.md).

Le attività di pre-elaborazione e pulizia, ad esempio l'attività di esplorazione dei dati, possono essere eseguite in un'ampia gamma di ambienti, ad esempio SQL o Hive o Azure Machine Learning Studio (classico) e con vari strumenti e linguaggi, ad esempio R o Python, a seconda di dove i dati viene memorizzato e come viene formattato. Poiché TDSP è iterativo per natura, queste attività possono essere eseguite in vari passaggi nel flusso di lavoro del processo.

Questo articolo introduce vari concetti e attività di elaborazione dei dati che possono essere eseguiti prima o dopo l'inserimento dei dati in Azure Machine Learning Studio (classico).

Per un esempio di esplorazione dei dati e pre-elaborazione eseguita all'interno di Azure Machine Learning Studio (classico), vedere il video [Sulla pre-elaborazione](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) dei dati.

## <a name="why-pre-process-and-clean-data"></a>Perché pre-elaborare e pulire i dati?
I dati reali vengono raccolti da diverse origini e processi e possono contenere irregolarità o dati danneggiati in grado di compromettere la qualità del set di dati. Di seguito sono riportati i tipici problemi relativi alla qualità di dati che si verificano:

* **Dati incompleti**: nei dati mancano attributi o non sono presenti valori.
* **Dati fastidiosi**: i dati contengono record o outlier errati.
* **Dati incoerenti**: i dati contengono record in conflitto o discrepanze.

La qualità dei dati è un prerequisito per modelli predittivi di qualità. Per evitare "spazzatura in entrata e in uscita" e migliorare la qualità dei dati e quindi le prestazioni del modello, è fondamentale condurre un'analisi dell'integrità dei dati per individuare tempestivamente problemi relativi ai dati e prendere decisioni in merito alle fasi di elaborazione e pulizia dei dati corrispondenti.

## <a name="what-are-some-typical-data-health-screens-that-are-employed"></a>Quali sono le tipiche analisi dell'integrità dei dati che vengono impiegate?
È possibile controllare la qualità generale dei dati controllando:

* Numero di **record**.
* Il numero di **attributi** (o **feature).**
* Tipi **di dati** dell'attributo (nominale, ordinale o continuo).
* Numero di **valori mancanti.**
* **Dati ben formati.**
  * Se i dati sono in formato TSV o CSV, verificare che i separatori di colonne e righe separino correttamente colonne e righe.
  * Se i dati sono in formato HTML o XML, verificare che i dati siano formulati correttamente in base ai rispettivi standard.
  * L'analisi potrebbe inoltre essere necessaria per estrarre informazioni strutturate da dati non strutturati o semistrutturati.
* **Record di dati incoerenti**. Verificare l'intervallo di valori consentiti. Ad esempio, se i dati contengono GPA studente (media dei punti di pendenza), verificare se il GPA è compreso nell'intervallo designato, ad esempio 0.

Quando si riscontrano problemi con i dati, sono necessari passaggi di **elaborazione,** che spesso comportalano la pulizia dei valori mancanti, la normalizzazione dei dati, la discretizzazione, l'elaborazione del testo per rimuovere e/o sostituire i caratteri incorporati che possono influire sull'allineamento dei dati, i tipi di dati misti nei campi comuni e altri.

**Azure Machine Learning utilizza dati tabulari ben formati.**  Se i dati sono già in formato tabulare, la pre-elaborazione dei dati può essere eseguita direttamente con Azure Machine Learning Studio (classico) in Machine Learning.If the data is already in tabular form, data pre-processing can be performed directly with Azure Machine Learning Studio (classic) in the Machine Learning.  Se i dati non sono in formato tabulare, bensì in XML, l'analisi potrebbe essere necessaria per convertire i dati in formato tabulare.  

## <a name="what-are-some-of-the-major-tasks-in-data-pre-processing"></a>Quali sono alcune delle principali attività di pre-elaborazione dei dati?
* **Pulizia dei dati**: Inserire i valori mancanti, rilevare e rimuovere i dati rumorosi e gli outlier.
* **Trasformazione dei dati**: Normalizzare i dati per ridurre le dimensioni e il rumore.
* **Riduzione dei dati**: Record di dati di esempio o attributi per una più facile gestione dei dati.
* **Discretizzazione dei dati**: Convertire gli attributi continui in attributi di categoria per la facilità d'uso con determinati metodi di apprendimento automatico.
* **Pulizia del testo**: rimuovere i caratteri incorporati che possono causare il disallineamento dei dati, ad esempio tabulazioni incorporate in un file di dati separato da tabulazioni, incorporate nuove righe che potrebbero interrompere record, ad esempio.

Nelle sezioni seguenti vengono riportati alcuni dei passaggi di elaborazione dei dati.

## <a name="how-to-deal-with-missing-values"></a>Come trattare i valori mancanti?
Per gestire i valori mancanti, è consigliabile innanzitutto identificare il motivo per cui mancano dei valori per una gestione più efficiente del problema. I metodi di gestione dei valori mancanti tipici sono:

* **Eliminazione**: rimuovere i record con valori mancanti
* **Sostituzione fittizia**: sostituire un valore mancante con un valore fittizio, ad esempio, *unknown* per valori categorici o 0 per valori numerici.
* **Sostituzione con media**: se i dati mancanti sono numerici, sostituirli con la media.
* **Sostituzione con elementi frequenti**: se i dati mancanti sono categorici, sostituirli con gli elementi più frequenti
* **Sostituzione con regressione**: usare il metodo della regressione per sostituire i valori mancanti con valori regrediti.  

## <a name="how-to-normalize-data"></a>Come normalizzare i dati?
La normalizzazione dei dati ridimensiona i valori numerici in un intervallo specificato. I metodi di normalizzazione dei dati più comuni includono:

* **Normalizzazione Min-Max**: consente di trasformare i dati in un intervallo in modo lineare, ad esempio compreso tra 0 e 1, dove il valore min corrisponde a 0 e il valore massimo a 1.
* **Normalizzazione Z-score**: consente di ridurre i dati in base alla media e alla deviazione standard. Si divide la differenza tra dati e media per la deviazione standard.
* **Scalabilità decimale**: consente di ridurre i dati spostando il punto decimale del valore dell'attributo.  

## <a name="how-to-discretize-data"></a>Come eseguire la discretizzazione dei dati?
I dati possono essere discretizzati convertendo i valori continui in attributi o intervalli nominali. Ciò può essere fatto nei seguenti modi:

* **Creazione di contenitori di uguale ampiezza**: si divide l'intervallo di tutti i possibili valori di un attributo in N gruppi della stessa dimensione a cui si assegnano i valori che rientrano in un contenitore con il numero di contenitore.
* **Creazione di contenitori di uguale altezza**: si divide l'intervallo di tutti i possibili valori di un attributo in N gruppi, ciascuno contenente lo stesso numero di istanze, quindi si assegnano i valori che rientrano in un contenitore con il numero di contenitore.  

## <a name="how-to-reduce-data"></a>Come ridurre i dati?
Esistono vari metodi per ridurre le dimensioni dei dati e renderne più semplice la gestione. A seconda della dimensione e del dominio dei dati, è possibile utilizzare i seguenti metodi:

* **Campionamento record**: i record di dati vengono campionati e viene scelto solo un sottoinsieme rappresentativo.
* **Campionamento attributi**: selezionare solo un sottoinsieme degli attributi più importanti dai dati.  
* **Aggregazione**: i dati vengono divisi in gruppi e per ogni gruppo vengono memorizzati i numeri. Ad esempio, i numeri dei ricavi giornalieri di una catena di ristoranti negli ultimi 20 anni possono essere aggregati in ricavi mensili per ridurre le dimensioni dei dati.  

## <a name="how-to-clean-text-data"></a>Come pulire i dati di testo?
**I campi di testo nei dati tabulari** possono includere caratteri che influiscono sull'allineamento delle colonne e/o sui limiti dei record. Ad esempio, le tabulazioni incorporate in un file separato da tabulazioni causano il disallineamento delle colonne e i caratteri di nuova riga incorporati interrompono le righe del record. Una gestione non corretta della codifica del testo durante la scrittura o la lettura del testo comporta la perdita di informazioni, l'introduzione involontaria di caratteri illeggibili (ad esempio i valori Null) e può anche influire sull'analisi del testo. Potrebbero essere necessarie un'attenta analisi e la modifica per pulire i campi di testo e consentire il corretto allineamento e/o estrarre i dati strutturati da dati di testo non strutturati o semistrutturati.

**esplorazione dei dati** offre una visualizzazione anticipata dei dati. Durante questa fase è possibile individuare una serie di problemi ed è possibile usare metodi idonei per risolverli.  È importante porre domande, come ad esempio, qual è l'origine del problema e come il problema potrebbe presentarsi. Questo processo consente inoltre di decidere i passaggi di elaborazione dei dati che devono essere eseguiti per risolverli. L'identificazione dei casi d'uso finali e delle persone può essere utilizzata anche per definire la priorità del lavoro di elaborazione dei dati.

## <a name="references"></a>Riferimenti
> *Data Mining: Concepts and Techniques*, Third Edition, Morgan Kaufmann, 2011, Jiawei Han, Micheline Kamber, and Jian Pei
> 
> 

