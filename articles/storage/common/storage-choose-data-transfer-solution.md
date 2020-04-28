---
title: Scegliere una soluzione di Azure per il trasferimento dei dati | Microsoft Docs
description: Informazioni su come scegliere una soluzione di Azure per il trasferimento dei dati in base alle dimensioni dei dati e alla larghezza di banda di rete disponibile nell'ambiente
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: df261dbf8af8ffdb4ccb10626594626150245748
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176397"
---
# <a name="choose-an-azure-solution-for-data-transfer"></a>Scegliere una soluzione di Azure per il trasferimento dei dati

Questo articolo offre una panoramica di alcune delle comuni soluzioni di trasferimento dei dati di Azure. L'articolo contiene anche i collegamenti alle opzioni consigliate a seconda della larghezza di banda di rete dell'ambiente e delle dimensioni dei dati da trasferire.

## <a name="types-of-data-movement"></a>Tipi di spostamento dati

Il trasferimento dei dati può essere eseguito offline o tramite la connessione di rete. Scegliere la soluzione a seconda delle condizioni seguenti:

- **Dimensioni dei dati**: dimensioni dei dati da trasferire
- **Frequenza di trasferimento**: inserimento dati occasionale o periodico
- **Rete**: larghezza di banda disponibile per il trasferimento dei dati nell'ambiente

Lo spostamento dati può essere dei tipi seguenti:

- **Trasferimento offline tramite dispositivi che è possibile spedire**: usare dispositivi fisici che è possibile spedire per eseguire un unico trasferimento dei dati in blocco offline. Microsoft invia un disco o un dispositivo sicuro specializzato. In alternativa, è possibile acquistare e spedire i propri dischi. Si copiano i dati sul dispositivo e quindi lo si spedisce ad Azure dove i dati vengono caricati.  Le opzioni disponibili in questo caso sono Data Box Disk, Data Box, Data Box Heavy e Importazione/Esportazione (con i propri dischi).

- **Trasferimento di rete**: si trasferiscono i dati in Azure tramite la connessione di rete. Questa operazione può essere eseguita in più modi.

    - **Interfaccia grafica**: se si trasferiscono occasionalmente solo pochi file e non è necessario automatizzare il trasferimento dei dati, è possibile scegliere uno strumento dell'interfaccia grafica, ad esempio Azure Storage Explorer o uno strumento di esplorazione basato sul Web, nel portale di Azure.
    - **Trasferimento con script o programmatico**: è possibile usare gli strumenti software ottimizzati forniti da Microsoft o chiamare direttamente le API REST o gli SDK. Gli strumenti gestibili tramite script sono AzCopy, Azure PowerShell e l'interfaccia della riga di comando di Azure. Per l'interfaccia programmatica, usare uno degli SDK per .NET, Java, Python, Node/JS, C++, Go, PHP o Ruby.
    - **Dispositivi locali**: viene fornito un dispositivo virtuale o fisico che si trova nel data center e ottimizza il trasferimento dei dati in rete. Questi dispositivi forniscono anche una cache locale dei file usati spesso. Il dispositivo fisico è Data Box Edge e il dispositivo virtuale è Data Box Gateway. Entrambi vengono eseguiti permanentemente in locale e si connettono ad Azure tramite la rete.
    - **Pipeline di dati gestiti**: è possibile configurare una pipeline cloud per trasferire regolarmente i file tra diversi servizi di Azure, l'ambiente locale o una combinazione dei due. Usare Azure Data Factory per configurare e gestire le pipeline di dati e spostare e trasformare i dati per l'analisi.

L'oggetto visivo seguente illustra le linee guida per scegliere i diversi strumenti di trasferimento dei dati di Azure a seconda della larghezza di banda di rete disponibile per il trasferimento, delle dimensioni dei dati da trasferire e della frequenza del trasferimento.

![Strumenti di trasferimento dati di Azure](media/storage-choose-data-transfer-solution/azure-data-transfer-options-3.png)

**I limiti massimi dei dispositivi di trasferimento offline (Data Box Disk, Data Box e Data Box Heavy) possono essere estesi effettuando più ordini di un tipo di dispositivo.*

## <a name="selecting-a-data-transfer-solution"></a>Selezione di una soluzione di trasferimento dei dati

Rispondere alle domande seguenti per selezionare una soluzione di trasferimento dei dati:

- La larghezza di banda di rete disponibile è ridotta o inesistente e si vogliono trasferire set di dati di grandi dimensioni?
  
    In caso affermativo, vedere: [scenario 1: trasferire set di impostazioni di grandi dimensioni con una larghezza di banda di rete insufficiente](storage-solution-large-dataset-low-network.md).
- Si vogliono trasferire set di dati di grandi dimensioni tramite la rete e la larghezza di banda di rete è moderata o elevata?

    In caso affermativo, vedere: [scenario 2: trasferire set di impostazioni di grandi dimensioni con larghezza di banda moderata e elevata](storage-solution-large-dataset-moderate-high-network.md).
- Si vogliono trasferire occasionalmente solo pochi file tramite la rete?

    In caso affermativo, vedere [scenario 3: trasferire piccoli set di impostazioni con una larghezza di banda di rete limitata a moderata](storage-solution-small-dataset-low-moderate-network.md).
- È necessario un trasferimento dei dati temporizzato a intervalli regolari?

    In caso affermativo, usare le opzioni di scripting/programmatiche descritte nello [scenario 4: trasferimenti periodici di dati](storage-solution-periodic-data-transfer.md).
- È necessario un trasferimento dei dati continuo?

    In caso affermativo, usare le opzioni nello [scenario 4: trasferimenti periodici di dati](storage-solution-periodic-data-transfer.md).

## <a name="data-transfer-feature-in-azure-portal"></a>Funzionalità di trasferimento dei dati in portale di Azure

È anche possibile passare all'account di archiviazione di Azure in portale di Azure e selezionare la funzionalità di **trasferimento dei dati** . Fornire la larghezza di banda di rete nell'ambiente, le dimensioni dei dati da trasferire e la frequenza di trasferimento dei dati. Vengono visualizzate le soluzioni di trasferimento dei dati ottimali corrispondenti alle informazioni fornite. 

## <a name="next-steps"></a>Passaggi successivi

- [Vedere un'introduzione ad Azure Storage Explorer](https://azure.microsoft.com/resources/videos/introduction-to-microsoft-azure-storage-explorer/).
- [Leggere una panoramica di AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10).
- [Guida introduttiva: caricare, scaricare ed elencare BLOB con PowerShell](../blobs/storage-quickstart-blobs-powershell.md)
- [Guida introduttiva: creare, scaricare ed elencare BLOB con interfaccia della riga di comando di Azure](../blobs/storage-quickstart-blobs-cli.md)
- Vengono fornite informazioni su:

    - [Azure Data Box, Azure Data Box Disk e Azure Data Box Heavy per i trasferimenti offline](https://docs.microsoft.com/azure/databox/).
    - [Azure Data Box Gateway e Azure Data Box Edge per i trasferimenti online](https://docs.microsoft.com/azure/databox-online/).
- [Informazioni su Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview).
- Usare le API REST per trasferire i dati

    - [In .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [In Java](https://docs.microsoft.com/java/api/overview/azure/storage)
