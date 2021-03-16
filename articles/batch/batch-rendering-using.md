---
title: Uso delle funzionalità di rendering
description: Informazioni su come usare le funzionalità di rendering di Azure Batch. Provare a usare l'applicazione Batch Explorer, direttamente o chiamandola da un plug-in di applicazione client.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2020
ms.topic: how-to
ms.openlocfilehash: dc3d2cc53b478b1ec955d8f4b3717b0407772849
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496627"
---
# <a name="using-azure-batch-rendering"></a>Uso del rendering di Azure Batch

> [!IMPORTANT]
> Le immagini di macchine virtuali per il rendering e le licenze di pagamento per utilizzo sono state [deprecate e verranno ritirate il 29 febbraio 2024](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/). Per utilizzare batch per il rendering, [è necessario utilizzare un'immagine di macchina virtuale personalizzata e licenze di applicazioni standard.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

Sono disponibili diversi modi per usare il rendering di Azure Batch:

* API:
  * Scrivere codice usando una delle API di Batch.  Gli sviluppatori possono integrare le funzionalità di Azure Batch nelle applicazioni esistenti o nel flusso di lavoro, su un'istanza locale o nel cloud.
* Strumenti da riga di comando:
  * Per scrivere script per l'uso di Batch, è possibile usare la [riga di comando di Azure](/cli/azure/) o [PowerShell](/powershell/azure/).
  * In particolare, il [supporto dei modelli dell'interfaccia della riga di comando di Batch](./batch-cli-templates.md) semplifica notevolmente la creazione di pool e l'invio di processi.
* Interfaccia utente di Batch Explorer:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) è uno strumento client multipiattaforma che consente anche la gestione e il monitoraggio degli account di Batch.
  * Per ognuna delle applicazioni per il rendering, vengono forniti numerosi modelli di pool e processi che possono essere usati per creare pool e inviare processi in tutta semplicità.  Nell'interfaccia utente dell'applicazione è elencato un set di modelli e i file dei modelli sono accessibili da GitHub.
  * È possibile creare da zero modelli personalizzati o copiare e modificare i modelli forniti da GitHub.
* Plug-in dell'applicazione client:
  * Sono disponibili plug-in che permettono l'uso del rendering di Batch direttamente dalle applicazioni di progettazione e modellazione client.  I plug-in principalmente richiamano l'applicazione Batch Explorer con informazioni contestuali sul modello 3D corrente e includono funzionalità che consentono di gestire gli asset.

Il modo migliore per usare il rendering di Azure Batch e anche il più semplice per gli utenti finali, che non sono sviluppatori ed esperti di Azure, consiste nell'usare l'applicazione Batch Explorer, direttamente o richiamandola da un plug-in dell'applicazione client.

## <a name="using-batch-explorer"></a>Uso di Batch Explorer

Per un'esercitazione dettagliata sull'uso di Batch Explorer per eseguire il rendering, vedere l'[esercitazione di Blender](./tutorial-rendering-batchexplorer-blender.md).

### <a name="download-and-install"></a>Scaricare e installare

[Sono disponibili download](https://azure.github.io/BatchExplorer/) di Batch Explorer per Windows, OSX e Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Uso di modelli per creare pool ed eseguire processi

Per l'uso con Batch Explorer è disponibile un set completo di modelli che semplifica la creazione di pool e l'invio di processi per le varie applicazioni per il rendering senza dover specificare tutte le proprietà necessarie per creare pool, processi e attività direttamente con Batch.  I modelli disponibili in Batch Explorer sono archiviati e consultabili in [un repository GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Raccolta di Batch Explorer](./media/batch-rendering-using/batch-explorer-gallery.png)

Sono modelli che soddisfano le esigenze di tutte le applicazioni delle immagini di macchine virtuali per il rendering presenti nel Marketplace.  Per ogni applicazione esistono più modelli, inclusi i modelli di pool per soddisfare le esigenze di pool di CPU e GPU, pool Windows e Linux. I modelli di processi includono il rendering di Blender con fotogrammi completi o affiancati e il rendering distribuito di V-Ray. Il set di modelli disponibili verrà ampliato nel tempo per soddisfare altre funzionalità di Batch, ad esempio la scalabilità automatica del pool.

È anche possibile creare modelli personalizzati da zero o modificando i modelli forniti. Per usare i modelli personalizzati, selezionare l'elemento 'Modelli locali' nella sezione 'Raccolta' di Batch Explorer.

### <a name="file-system-and-data-movement"></a>File system e spostamento dati

La sezione 'Dati' in Batch Explorer consente di copiare i file tra un file system locale e gli account di Archiviazione di Azure.

## <a name="next-steps"></a>Passaggi successivi

Per esempi del rendering di Batch provare le due esercitazioni:

* [Eseguire il rendering con l'interfaccia della riga di comando di Azure](./tutorial-rendering-cli.md)
* [Eseguire il rendering con Batch Explorer](./tutorial-rendering-batchexplorer-blender.md)
