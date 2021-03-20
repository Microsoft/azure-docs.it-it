---
title: Importare dati nella finestra di progettazione
titleSuffix: Azure Machine Learning
description: Informazioni su come importare dati in Azure Machine Learning Designer usando Azure Machine Learning set di dati e il modulo Import Data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: a2cc0840b7ba4b26cf9f5b1219fc189230870774
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97739859"
---
# <a name="import-data-into-azure-machine-learning-designer"></a>Importare dati in Azure Machine Learning Designer

In questo articolo si spiega come importare dati nella finestra di progettazione per creare soluzioni personalizzate. È possibile importare i dati nella finestra di progettazione in due modi: 

* **Set di dati in Azure Machine Learning**: registrare i [set di dati](concept-data.md#datasets) in Azure Machine Learning per abilitare le funzionalità avanzate che consentono di gestire i dati.
* **Importare il modulo dati**: usare il modulo [Import Data](algorithm-module-reference/import-data.md) per accedere direttamente ai dati dalle origini dati online.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-azure-machine-learning-datasets"></a>Uso dei set di dati di Azure Machine Learning

Consigliamo di usare i [set di dati](concept-data.md#datasets) per importare i dati nella finestra di progettazione. Quando si registra un set di dati, è possibile sfruttare appieno funzionalità avanzate come [il controllo delle versioni e il rilevamento](how-to-version-track-datasets.md) e [il monitoraggio dei dati](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Registrazione di un set di dati

È possibile registrare i set di dati esistenti [a livello di codice con l'SDK](how-to-create-register-datasets.md#datasets-sdk) o [visivamente in Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).

È anche possibile registrare l'output di qualsiasi modulo della finestra di progettazione come set di dati.

1. Selezionare il modulo che restituisce i dati da registrare.

1. Nel riquadro Proprietà selezionare **output + log**  >  **registra set di dati**.

    ![Screenshot che mostra come passare all'opzione Registra set di dati](media/how-to-designer-import-data/register-dataset-designer.png)

Se i dati di output del modulo sono in formato tabulare, è necessario scegliere di registrare l'output come set di dati di **file** o **tabulare**.

 - Il **set di dati del file** registra la cartella di output del modulo come set di dati di file. La cartella di output contiene un file di dati e i metadati che la finestra di progettazione utilizza internamente. Selezionare questa opzione se si desidera continuare a utilizzare il set di dati registrato nella finestra di progettazione. 

 - Il set di dati **tabulare** registra solo il file di dati di output del modulo come set di dati tabulare. Questo formato è facilmente utilizzabile da altri strumenti, ad esempio in Machine Learning automatizzato o Python SDK. Selezionare questa opzione se si prevede di utilizzare il set di dati registrato al di fuori della finestra di progettazione.  
 

### <a name="use-a-dataset"></a>Uso di un set di dati

I set di strumenti registrati sono disponibili nella tavolozza dei moduli, in **set di impostazioni**. Per usare un set di dati, trascinarlo e rilascialo nelle canvas della pipeline. Quindi, connettere la porta di output del set di dati ad altri moduli nell'area di disegno. 

Se si registra un set di dati del file, il tipo di porta di output del set di dati è **AnyDirectory**. Se si registra un set di dati tabulare, il tipo di porta di output del set di dati, se **DataFrameDirectory**. Si noti che se si connette la porta di output del set di dati ad altri moduli nella finestra di progettazione, è necessario allineare il tipo di porta di set di dati e moduli.

![Screenshot che mostra la posizione dei set di dati salvati nella tavolozza della finestra di progettazione](media/how-to-designer-import-data/use-datasets-designer.png)


> [!NOTE]
> La finestra di progettazione supporta il [controllo delle versioni dei DataSet](how-to-version-track-datasets.md). Specificare la versione del set di dati nel pannello Proprietà del modulo DataSet.

### <a name="limitations"></a>Limitazioni 

- Attualmente è possibile visualizzare solo i set di dati tabulari nella finestra di progettazione. Se si registra un set di dati di file all'esterno di progettazione, non è possibile visualizzarlo nell'area di disegno della finestra di progettazione.
- Il set di dati è archiviato in rete virtuale (VNet). Se si desidera visualizzare, è necessario abilitare l'identità gestita dell'area di lavoro dell'archivio dati.
    1. Passare all'archivio dati correlato e fare clic su **Aggiorna** credenziali 
     :::image type="content" source="./media/resource-known-issues/datastore-update-credential.png" alt-text="Aggiorna"::: credenziali
    1. Selezionare **Sì** per abilitare l'identità gestita dell'area di lavoro.
    :::image type="content" source="./media/resource-known-issues/enable-workspace-managed-identity.png" alt-text="Abilita identità gestita dell'area di lavoro":::

## <a name="import-data-using-the-import-data-module"></a>Importazione dei dati usando il modulo Importa dati

Sebbene sia consigliabile usare i set di dati per importare i dati, è anche possibile usare il modulo [Import Data](algorithm-module-reference/import-data.md). Il modulo Import Data salta la registrazione del set di dati in Azure Machine Learning e importa i dati direttamente da un [archivio dati](concept-data.md#datastores) o da un URL HTTP.

Per informazioni dettagliate su come usare il modulo Import Data, vedere la [pagina di riferimento di Importa dati](algorithm-module-reference/import-data.md).

> [!NOTE]
> Se nel set di dati sono presenti troppe colonne, potrebbe verificarsi il seguente errore: "Convalida non riuscita a causa della limitazione delle dimensioni". Per evitare che si verifichi, [registrare il set di dati nell'interfaccia Set di dati](how-to-connect-data-ui.md#create-datasets).

## <a name="supported-sources"></a>Origini supportate

In questa sezione sono elencate le origini dati supportate dalla finestra di progettazione. I dati della finestra di progettazione provengono da un archivio dati o da un [set di dati tabulare](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Origini di archivio dati
Per un elenco delle origini di archivio dati supportate, vedere [Accesso ai dati nei servizi di archiviazione di Azure](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Origini di set di dati tabulari

La finestra di progettazione supporta i set di dati tabulari creati dalle origini seguenti:
 * File delimitati
 * File JSON
 * File Parquet
 * Query SQL

## <a name="data-types"></a>Tipi di dati

La finestra di progettazione riconosce internamente i tipi di dati seguenti:

* string
* Integer
* Decimal
* Boolean
* Data

La finestra di progettazione usa un tipo di dati interno per passare i dati tra i moduli. È possibile convertire in modo esplicito i dati in formato tabella dati tramite il modulo [Convert to Dataset](algorithm-module-reference/convert-to-dataset.md). I moduli che accettano formati diversi da quello interno convertono i dati in modo automatico prima di passare al modulo successivo.

## <a name="data-constraints"></a>Vincoli dei dati

I moduli della finestra di progettazione sono limitati dalle dimensioni della destinazione di calcolo. Per i set di dati più grandi è necessario usare una risorsa di calcolo Azure Machine Learning più grande. Per altre informazioni sulle destinazioni di calcolo di Azure Machine Learning vedere [Cosa sono le destinazioni di calcolo in Azure Machine Learning?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="access-data-in-a-virtual-network"></a>Accedere ai dati in una rete virtuale

Se l'area di lavoro si trova in una rete virtuale, è necessario eseguire passaggi di configurazione aggiuntivi per visualizzare i dati nella finestra di progettazione. Per altre informazioni su come usare gli archivi dati e i set di dati in una rete virtuale, vedere [usare Azure Machine Learning Studio in una rete virtuale di Azure](how-to-enable-studio-virtual-network.md).

## <a name="next-steps"></a>Passaggi successivi

Scopri i concetti fondamentali della finestra di progettazione con questa [esercitazione: stimare il prezzo dell'automobile con la finestra di progettazione](tutorial-designer-automobile-price-train-score.md).
