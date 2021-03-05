---
title: Panoramica di Azure Stack Edge Mini R | Microsoft Docs
description: Informazioni su Azure Stack Edge Mini R, una soluzione di archiviazione per applicazioni militari che usa un dispositivo fisico portatile con una batteria per il trasferimento in Azure tramite Wi-Fi.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/03/2021
ms.author: alkohli
ms.openlocfilehash: 7c28eb604de4f6e23f37b6ca2fcab3fdc976f237
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125200"
---
# <a name="what-is-the-azure-stack-edge-mini-r"></a>Che cos'è Azure Stack Edge Mini R?

Azure Stack Edge Mini R è un dispositivo di edge computing resistente e ultra portatile progettato per l'uso in ambienti difficili. Azure Stack Edge Mini R è una soluzione di hardware come servizio. Microsoft fornisce un dispositivo gestito dal cloud che funge da gateway di archiviazione di rete e include una VPU (Vision Processing Unit) incorporata che abilita l'inferenza per l'intelligenza artificiale accelerata.

Questo articolo contiene una panoramica della soluzione Azure Stack Edge Mini R, con le principali funzionalità e gli scenari in cui è possibile distribuire questo dispositivo.


## <a name="key-capabilities"></a>Funzionalità principali

Azure Stack Edge Mini R include le funzionalità seguenti:

|Funzionalità |Descrizione  |
|---------|---------|
|Hardware resistente| Dispositivo resistente progettato per ambienti difficili.|
|Ultra portatile| Fattore di forma ultra portatile a batteria.|
|Gestione dal cloud|Il dispositivo e il servizio vengono gestiti tramite il portale di Azure.|
|Carichi di lavoro per l'edge computing|Consente l'analisi, l'elaborazione e il filtraggio dei dati.<br>Supporta le macchine virtuali e i carichi di lavoro in contenitori. |
|Inferenza per intelligenza artificiale accelerata| Abilitata dalla VPU Intel Movidius Myriad X.|
|Connessioni cablate e wireless | Consente i trasferimenti di dati cablati e wireless.|
|Accesso ai dati     | Indirizzare l'accesso ai dati dai BLOB del servizio di archiviazione di Azure e da File di Azure usando le API cloud per un'ulteriore elaborazione dei dati nel cloud. La cache locale nel dispositivo viene usata per l'accesso rapido ai file usati più di recente.|
|Modalità disconnessa|  Il dispositivo e il servizio possono essere gestiti facoltativamente tramite l'hub di Azure Stack. È possibile distribuire, eseguire e gestire le applicazioni in modalità offline. <br> La modalità disconnessa supporta scenari di caricamento offline.|
|Protocolli FTP supportati      |Supporto per protocolli SMB, NFS e REST standard per l'inserimento di dati. <br> Per altre informazioni sulle versioni supportate, vedere i [requisiti di sistema di Azure Stack Edge Mini R](azure-stack-edge-gpu-system-requirements.md).|
|Aggiornamento dati     | Possibilità di aggiornare i file locali con quelli più recenti nel cloud.|
|Crittografia doppia    | L'uso dell'unità con crittografia automatica offre il primo livello di crittografia. La VPN fornisce il secondo livello di crittografia. Supporto di BitLocker per crittografare i dati in locale e proteggerne il trasferimento nel cloud tramite *HTTPS*.|
|Limitazione larghezza di banda| Limitare l'utilizzo della larghezza di banda durante le ore di picco.|

## <a name="use-cases"></a>Casi d'uso

Ecco i vari scenari in cui è possibile usare Azure Stack Edge Mini R per l'inferenza rapida di Machine Learning (ML) al perimetro e la pre-elaborazione dei dati prima dell'invio ad Azure.

- **Inferenza con Azure Machine Learning**: con Azure Stack Edge Mini R è possibile eseguire modelli di Machine Learning per ottenere risultati rapidi su cui intervenire prima che i dati vengano inviati al cloud. Il set di dati completo può essere facoltativamente trasferito per continuare a ripetere il training dei modelli di Machine Learning e migliorarli. Per altre informazioni su come usare i modelli con accelerazione hardware di Azure Machine Learning nel dispositivo Azure Stack Edge Mini R, vedere [Distribuire modelli con accelerazione hardware di Azure Machine Learning in Azure Stack Edge Mini R](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Pre-elaborare i dati**: trasformare i dati tramite opzioni di calcolo come contenitori o macchine virtuali prima dell'invio ad Azure per creare un set di dati su cui intervenire. La pre-elaborazione può essere usata per:

    - Aggregare i dati.
    - Modificare i dati, ad esempio per rimuovere i dati personali.
    - Creare subset dei dati per ottimizzare l'archiviazione e la larghezza di banda o per ulteriori analisi.
    - Analizzare e rispondere a eventi IoT.

- **Trasferire i dati tramite rete in Azure**: usare Azure Stack Edge Mini R per trasferire rapidamente i dati in Azure e consentire ulteriori attività di analisi e calcolo o a scopo di archiviazione.

## <a name="components"></a>Componenti

La soluzione Azure Stack Edge Mini R è costituita da una risorsa Azure Stack Edge, da un dispositivo fisico Azure Stack Edge Mini R, ultra portatile e resistente, e da un'interfaccia utente Web locale.

* **Dispositivo fisico Azure Stack Edge Mini R**: dispositivo di calcolo e archiviazione resistente e ultra portatile fornito da Microsoft. Il dispositivo ha una batteria integrata e pesa poco più di 3 kg.

    ![Dispositivo Azure Stack Edge Mini R](media/azure-stack-edge-mini-r-overview/perspective-view-1.png)

* **Risorsa Azure Stack Edge**: una risorsa del portale di Azure che consente di gestire un dispositivo Azure Stack Edge Mini R resistente da un'interfaccia Web accessibile da aree geografiche diverse. Usare la risorsa Azure Stack Edge per creare e gestire risorse, visualizzare e gestire dispositivi e avvisi e gestire condivisioni.  

* **Interfaccia utente Web locale di Azure Stack Edge Mini R**: interfaccia utente locale basata su browser nel dispositivo Azure Stack Edge Mini R destinata principalmente alla configurazione iniziale del dispositivo. Usare l'interfaccia utente Web locale anche per eseguire la diagnostica, arrestare e riavviare il dispositivo Azure Stack Edge Pro, visualizzare i log di copia e contattare il supporto tecnico Microsoft per inviare una richiesta di servizio.


## <a name="region-availability"></a>Aree di disponibilità

Il dispositivo fisico Azure Stack Edge Mini R, la risorsa di Azure e l'account di archiviazione di destinazione in cui trasferire i dati non devono necessariamente trovarsi tutti nella stessa area.

- **Aree di disponibilità**: per un elenco delle aree in cui la risorsa Azure Stack Edge è disponibile, vedere l'[elenco dei prodotti Azure disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Disponibilità del dispositivo**: per un elenco di tutti i paesi in cui il dispositivo Azure Stack Edge Mini R è disponibile, passare alla sezione Disponibilità nella scheda Azure Stack Edge Mini R in [Prezzi di Azure Stack Edge Mini R](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeMiniR).

- **Account di archiviazione di destinazione**: gli account che archiviano i dati sono disponibili in tutte le aree di Azure. Per prestazioni ottimali, le aree in cui gli account archiviano i dati di Azure Stack Edge Mini R devono trovarsi nelle vicinanze del dispositivo. Un account di archiviazione lontano dal dispositivo si traduce in lunghe latenze e prestazioni ridotte.


## <a name="next-steps"></a>Passaggi successivi

- Vedere i [requisiti di sistema di Azure Stack Edge Mini R](azure-stack-edge-gpu-system-requirements.md).