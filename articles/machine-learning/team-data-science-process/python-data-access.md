---
title: Accedere a set di dati con la libreria client Python - Processo di data science per i team
description: Installare e usare la libreria client Python per accedere e gestire i dati di Azure Machine Learning in modo protetto da un ambiente Python locale.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b773241fdff41323272422f99e88f0d01e884055
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889553"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Accedere a set di dati con Python mediante la libreria client Python di Azure Machine Learning
L'anteprima della libreria client Python di Microsoft Azure Machine Learning consente l'accesso sicuro a set di dati di Azure Machine Learning da un ambiente Python locale, nonché la creazione e la gestione di set di dati in un'area di lavoro.

Questo argomento fornisce istruzioni su come:

* Installare la libreria client Python di Machine Learning
* Accedere e caricare set di dati, fornendo istruzioni su come ottenere l'autorizzazione per accedere a set di dati di Azure Machine Learning dall'ambiente Python locale
* Accedere ai set di dati intermedi di un esperimento
* usare la libreria client Python per enumerare i set di dati, accedere ai metadati, leggere il contenuto di un set di dati, creare nuovi set di dati e aggiornare i set di dati esistenti

## <a name="prerequisites"></a><a name="prerequisites"></a>Prerequisiti
La libreria client Python è stata testata negli ambienti seguenti:

* Windows, Mac e Linux
* Python 2,7 e 3.6 +

Presenta una dipendenza dai pacchetti seguenti:

* requests
* python-dateutil
* pandas

È consigliabile usare una distribuzione Python, ad esempio [Anaconda](https://www.anaconda.com/) o [Canopy](https://store.enthought.com/downloads/), inclusa in Python, IPython e i tre pacchetti installati ed elencati precedentemente. Sebbene IPython non sia obbligatorio, costituisce un ambiente ottimale per la manipolazione e la visualizzazione dei dati in modo interattivo.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Come installare la libreria client Python di Azure Machine Learning
Installare la libreria client di Azure Machine Learning Python per completare le attività descritte in questo argomento. Questa libreria è disponibile nell' [indice del pacchetto python](https://pypi.python.org/pypi/azureml). Per installarla nel proprio ambiente Python, eseguire il comando seguente dall'ambiente Python locale:

```console
pip install azureml
```

In alternativa, è possibile scaricare e installare dalle origini su [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

```console
python setup.py install
```

Se nel proprio computer è installato Git, è possibile usare pip per installarla direttamente dal repository Git.

```console
pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git
```

## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Usare frammenti di codice per accedere ai set di impostazioni
La libreria client Python consente l'accesso a livello di codice a set di dati esistenti in esperimenti in esecuzione.

Dall'interfaccia Web Azure Machine Learning Studio (classica), è possibile generare frammenti di codice che includono tutte le informazioni necessarie per scaricare e deserializzare i set di dati come oggetti di dataframe Pandas nel computer locale.

### <a name="security-for-data-access"></a><a name="security"></a>Sicurezza per l'accesso ai dati
I frammenti di codice forniti da Azure Machine Learning Studio (classico) per l'uso con la libreria client Python includono l'ID dell'area di lavoro e il token di autorizzazione. che offrono l'accesso completo all'area di lavoro e pertanto devono essere protetti, come una password.

Per motivi di sicurezza, le funzionalità dei frammenti di codice sono disponibili solo per gli utenti il cui ruolo nell'area di lavoro è impostato su **Owner** . Il ruolo viene visualizzato in Azure Machine Learning Studio (classico) nella pagina **utenti** in **Impostazioni**.

![Screenshot mostra le impostazioni nella pagina utenti di Azure Machine Learning Studio.][security]

Se il proprio ruolo non è impostato su **Owner**, è possibile chiedere di essere nuovamente invitati con il ruolo di proprietario o chiedere il frammento di codice al proprietario dell'area di lavoro.

Per ottenere il token di autorizzazione, è possibile scegliere una delle opzioni seguenti:

* Chiedere un token a un proprietario. I proprietari possono accedere ai token di autorizzazione dalla pagina impostazioni dell'area di lavoro in Azure Machine Learning Studio (versione classica). Selezionare **Settings** (Impostazioni) dal riquadro sinistro e fare clic su **AUTHORIZATION TOKENS** (Token di autorizzazione) per visualizzare i token primari e secondari. Sebbene per il frammento di codice sia possibile usare sia i token di autorizzazione primari sia quelli secondari, è consigliabile che i proprietari condividano solo i token di autorizzazione secondari.

   ![Token di autorizzazione](./media/python-data-access/ml-python-access-settings-tokens.png)

* Chiedere di essere promossi al ruolo di proprietario: un proprietario corrente dell'area di lavoro deve prima rimuovere l'utente dall'area di lavoro, quindi inviarlo nuovamente come proprietario.

Una volta ottenuti l'ID dell'area di lavoro e il token di autorizzazione, gli sviluppatori sono in grado di accedere all'area di lavoro usando il frammento di codice indipendentemente dal loro ruolo.

I token di autorizzazione vengono gestiti nella pagina **AUTHORIZATION TOKENS** in **SETTINGS**. È possibile rigenerarli, ma questa procedura revoca l'accesso al token precedente.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Accedere a set di dati da un'applicazione Python locale
1. In Machine Learning Studio (classico), fare clic su **set di impostazioni** nella barra di spostamento a sinistra.
2. Selezionare il set di dati a cui si desidera accedere. È possibile selezionare qualsiasi set di dati dall'elenco **MY DATASETS** o **SAMPLES**.
3. Sulla barra degli strumenti inferiore fare clic su **Generate Data Access Code**(Genera codice di accesso ai dati). Se i dati si presentano in un formato non compatibile con la raccolta client di Python, questo pulsante non è attivo.
   
    ![Screenshot mostra i set di dati con il codice di generazione del codice di accesso ai dati.][datasets]
4. Selezionare il frammento di codice dalla finestra che viene visualizzata e copiarlo negli Appunti.
   
    ![Pulsante per generare il codice di accesso ai dati][dataset-access-code]
5. Incollare il codice nel blocco appunti dell'applicazione Python locale.
   
    ![Incollare il codice nel blocco appunti][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Accedere a set di dati intermedi da esperimenti di Machine Learning
Dopo l'esecuzione di un esperimento in Machine Learning Studio (versione classica), è possibile accedere ai set di impostazioni intermedi dai nodi di output dei moduli. I set di dati intermedi sono costituiti da dati creati e usati per i passaggi intermedi quando è in esecuzione uno strumento di modello.

È possibile accedere ai set di dati intermedi sono se si trovano in un formato compatibile con la libreria client Python.

Sono supportati i formati seguenti (le costanti per questi formati sono disponibili nella `azureml.DataTypeIds` classe):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

È possibile determinare il formato passando il puntatore del mouse su un nodo di output del modulo. Il formato viene visualizzato in una descrizione comando insieme al nome del nodo.

Alcuni moduli, ad esempio [Split][split], eseguono l'output in un formato denominato `Dataset`, che non è supportato dalla libreria client Python.

![Formato del set di dati][dataset-format]

Per ottenere un output in un formato supportato, è necessario usare un modulo di conversione, ad esempio [Convert to CSV][convert-to-csv] (Converti in CSV).

![Formato GenericCSV][csv-format]

I passaggi seguenti illustrano un esempio in cui si crea e si esegue un esperimento e si accede al set di dati intermedio.

1. Creare un nuovo esperimento.
2. Inserire un modulo **Adult Census Income Binary Classification dataset** .
3. Inserire un modulo [Split][split] e connetterne l'input all'output del modulo del set di dati.
4. Inserire un modulo [Convert to CSV][convert-to-csv] (Converti in CSV) e connetterne l'input a uno degli output del modulo [Split][split] (Dividi).
5. Salvare l'esperimento, eseguirlo e attendere il completamento del processo.
6. Fare clic sul nodo di output del modulo [Convert to CSV][convert-to-csv] (Converti in CSV).
7. Nel menu di scelta rapida visualizzato selezionare **Generate Data Access Code** (Genera codice di accesso ai dati).
   
    ![Menu di scelta rapida][experiment]
8. Selezionare il frammento di codice dalla finestra che viene visualizzata e copiarlo negli Appunti.
   
    ![Generare il codice di accesso dal menu di scelta rapida][intermediate-dataset-access-code]
9. Incollare il codice nel blocco appunti.
   
    ![Incollare il codice nel blocco appunti][ipython-intermediate-dataset]
10. È possibile visualizzare i dati usando matplotlib. In questo caso, viene creato un istogramma per la colonna dell'età.
    
    ![Istogramma][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Usare la libreria client Python di Machine Learning per accedere, leggere, creare e gestire set di dati
### <a name="workspace"></a>Area di lavoro
L'area di lavoro è il punto di ingresso della libreria client Python. Fornire la `Workspace` classe con l'ID dell'area di lavoro e il token di autorizzazione per creare un'istanza:

```python
ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
               authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')
```

### <a name="enumerate-datasets"></a>Enumerare set di dati
Per enumerare tutti i set di dati presenti in un'area di lavoro:

```python
for ds in ws.datasets:
    print(ds.name)
```

Per enumerare solo i set di dati creati dall'utente:

```python
for ds in ws.user_datasets:
    print(ds.name)
```

Per enumerare solo i set di dati di esempio:

```python
for ds in ws.example_datasets:
    print(ds.name)
```

È possibile accedere a un set di dati per nome (si applica la distinzione maiuscole/minuscole):

```python
ds = ws.datasets['my dataset name']
```

In alternativa, è possibile accedere tramite indice:

```python
ds = ws.datasets[0]
```

### <a name="metadata"></a>Metadati
I set di dati sono composti non solo dal contenuto ma anche da metadati (i set di dati intermedi rappresentano un'eccezione a questa regola poiché non contengono metadati).

Alcuni valori di metadati vengono assegnati dall'utente in fase di creazione:

* `print(ds.name)`
* `print(ds.description)`
* `print(ds.family_id)`
* `print(ds.data_type_id)`

Altri valori vengono assegnati da Azure Machine Learning:

* `print(ds.id)`
* `print(ds.created_date)`
* `print(ds.size)`

Vedere la classe `SourceDataset` per altre informazioni sui metadati disponibili.

### <a name="read-contents"></a>Leggere il contenuto
I frammenti di codice forniti da Machine Learning Studio (classico) scaricano e deserializzano automaticamente il set di dati in un oggetto di dataframe Pandas. Questa operazione viene eseguita con il metodo `to_dataframe` :

```python
frame = ds.to_dataframe()
```

Se lo si preferisce, è possibile scaricare i dati non elaborati ed eseguire la deserializzazione manualmente. Questa è l'unica alternativa per i formati come "ARFF" che la libreria client Python non è attualmente in grado di deserializzare.

Per leggere il contenuto come file di testo:

```python
text_data = ds.read_as_text()
```

Per leggere il contenuto come file binario:

```python
binary_data = ds.read_as_binary()
```

È anche possibile aprire un flusso nel contenuto:

```python
with ds.open() as file:
    binary_data_chunk = file.read(1000)
```

### <a name="create-a-new-dataset"></a>Creare un nuovo set di dati
La libreria client Python consente di caricare set di dati dal programma Python, per poterli usare nell'area di lavoro.

Se i dati si trovano in un oggetto DataFrame pandas, usare il codice seguente:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_dataframe(
    dataframe=frame,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Se invece i dati sono già serializzati, è possibile usare:

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_raw_data(
    raw_data=raw_data,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

La libreria client Python è in grado di serializzare un oggetto DataFrame pandas nei formati seguenti (le costanti per questi formati sono disponibili nella classe `azureml.DataTypeIds`):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Aggiornare un set di dati esistente
Se si tenta di caricare un nuovo set di dati con un nome corrispondente a un set di dati esistente, si potrebbe ottiene un errore di conflitto.

Per aggiornare un set di dati esistente, è necessario prima ottenere un riferimento a tale set di dati:

```python
dataset = ws.datasets['existing dataset']

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Quindi, è necessario usare `update_from_dataframe` per serializzare e sostituire il contenuto del set di dati in Azure:

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(frame2)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

Se si desidera serializzare i dati in un formato diverso, specificare un valore per il parametro `data_type_id` facoltativo.

```python
from azureml import DataTypeIds

dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    data_type_id=DataTypeIds.GenericTSV,
)

print(dataset.data_type_id) # 'GenericTSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

È possibile anche impostare una nuova descrizione specificando un valore per il parametro `description` .

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    description='data up to feb 2015',
)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to feb 2015'
```

È possibile anche impostare un nuovo nome specificando un valore per il parametro `name` . D'ora in poi sarà possibile recuperare il set di dati usando solo il nuovo nome. Il codice seguente aggiorna i dati, il nome e la descrizione.

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    name='existing dataset v2',
    description='data up to feb 2015',
)

print(dataset.data_type_id)                    # 'GenericCSV'
print(dataset.name)                            # 'existing dataset v2'
print(dataset.description)                     # 'data up to feb 2015'

print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
print(ws.datasets['existing dataset'].name)    # IndexError
```

I parametri `data_type_id`, `name` e `description` sono facoltativi e, per impostazione predefinita, vengono ripristinati ai valori precedenti. Il parametro `dataframe` è sempre obbligatorio.

Se i dati sono già serializzati, usare `update_from_raw_data` anziché `update_from_dataframe`. Il funzionamento è analogo: è sufficiente passare `raw_data` invece di `dataframe`.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: /azure/machine-learning/studio-module-reference/convert-to-csv
[split]: /azure/machine-learning/studio-module-reference/split-data