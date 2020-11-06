---
title: 'Converti in DataSet: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Convert to DataSet in Azure Machine Learning Designer per convertire l'input di dati nel formato del set di dati interno.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 671a8f7c6fa4c20ef4fc88f57d4a946a84614389
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420870"
---
# <a name="convert-to-dataset"></a>Eseguire la conversione in set di dati

Questo articolo descrive come usare il modulo Convert to DataSet in Azure Machine Learning Designer per convertire i dati di una pipeline nel formato interno della finestra di progettazione.
  
La conversione non è necessaria nella maggior parte dei casi. Azure Machine Learning converte in modo implicito i dati nel relativo formato di set di dati nativo quando viene eseguita un'operazione sui dati. 

Si consiglia di salvare i dati nel formato del set di dati se è stato eseguito un tipo di normalizzazione o di pulizia su un set di dati e si desidera assicurarsi che le modifiche vengano utilizzate in altre pipeline.  
  
> [!NOTE]
> Convert to DataSet cambia solo il formato dei dati. Non salva una nuova copia dei dati nell'area di lavoro. Per salvare il set di dati, fare doppio clic sulla porta di output, selezionare **Salva come set di dati** e immettere un nuovo nome.  
  
## <a name="how-to-use-convert-to-dataset"></a>Come usare Convert to DataSet  

È consigliabile usare il modulo [Modifica metadati](edit-metadata.md) per preparare il set di dati prima di usare Convert to DataSet. È possibile aggiungere o modificare i nomi delle colonne, modificare i tipi di dati e apportare altre modifiche in base alle esigenze.

1.  Aggiungere il modulo Convert to DataSet alla pipeline. È possibile trovare questo modulo nella categoria **trasformazione dati** nella finestra di progettazione. 

2. Connetterlo a qualsiasi modulo che restituisce un set di dati.   

    Fino a quando i dati sono [tabulari](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py), è possibile convertirli in un set di dati. Sono inclusi i dati caricati tramite l' [importazione dati](import-data.md), i dati creati tramite [immissione manuale dei dati](enter-data-manually.md)o i set di dati trasformati tramite [Applica trasformazione](apply-transformation.md).

3.  Nell'elenco a discesa **azione** indicare se si desidera eseguire una pulizia dei dati prima di salvare il set di dati:  
  
    - **None** : usare i dati così come sono.  
  
    - **SetMissingValue** : impostare un valore specifico su un valore mancante nel set di dati. Il segnaposto predefinito è il carattere punto interrogativo (?), ma è possibile usare l'opzione  **Custom missing value** per immettere un valore diverso. Ad esempio, se si immette un **taxi** per un **valore mancante personalizzato** , tutte le istanze di **taxi** nel set di dati verranno modificate nel valore mancante.
  
    - **ReplaceValues** : usare questa opzione per specificare un singolo valore esatto da sostituire con qualsiasi altro valore esatto. È possibile sostituire i valori mancanti o i valori personalizzati impostando il metodo **Replace** :

      - **Mancante** : selezionare questa opzione per sostituire i valori mancanti nel set di dati di input. Per **nuovo valore** , immettere il valore con cui sostituire i valori mancanti.
      - **Personalizzata** : selezionare questa opzione per sostituire i valori personalizzati nel set di dati di input. Per **valore personalizzato** , immettere il valore che si desidera trovare. Se, ad esempio, i dati contengono la stringa `obs` usata come segnaposto per i valori mancanti, immettere `obs` . Per **nuovo valore** , immettere il nuovo valore con cui sostituire la stringa originale.
  
    Si noti che l'operazione **ReplaceValues** si applica solo alle corrispondenze esatte. Ad esempio, queste stringhe non saranno interessate: `obs.` , `obsolete` .  
 
  
5.  Inviare la pipeline.  

## <a name="results"></a>Risultati

+  Per salvare il set di dati risultante con un nuovo nome, selezionare il **set di dati Icon Register** sotto la scheda **Outputs** nel riquadro destro del modulo.  
  
## <a name="technical-notes"></a>Note tecniche  

-   Tutti i moduli che accettano un set di dati come input possono anche prendere i dati nel file CSV o nel file TSV. Prima dell'esecuzione di qualsiasi codice del modulo, gli input vengono pre-elaborati. La pre-elaborazione equivale all'esecuzione del modulo Convert to DataSet nell'input.  
  
-   Non è possibile eseguire la conversione dal formato SVMLight a un set di dati.  
  
-   Quando si specifica un'operazione di sostituzione personalizzata, l'operazione di ricerca e sostituzione viene applicata ai valori completi. Non sono consentite corrispondenze parziali. Ad esempio, è possibile sostituire un 3 con un-1 o con 33, ma non è possibile sostituire un 3 in un numero a due cifre, ad esempio 35.  
  
-   Per le operazioni di sostituzione personalizzata, la sostituzione ha esito negativo in modo invisibile all'utente se si usa un carattere sostitutivo non conforme al tipo di dati corrente della colonna.  

  
## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning.