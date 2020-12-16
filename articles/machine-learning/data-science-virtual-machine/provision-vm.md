---
title: 'Guida introduttiva: Creare una macchina virtuale data science Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Configurare e creare una macchina virtuale di Data Science in Azure per l'analisi dei dati e l'apprendimento automatico.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: e876ddc9b4df32cad5bbf15215da5ce2f912bba1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109726"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Guida introduttiva: Configurare la Data Science Virtual Machine per Windows

Iniziare a usare una Data Science Virtual Machine di Windows Server 2019.

## <a name="prerequisite"></a>Prerequisito

Per creare una Data Science Virtual Machine Windows, è necessaria una sottoscrizione di Azure. [Provare Azure gratuitamente](https://azure.com/free).
Si noti che gli account gratuiti di Azure non supportano gli SKU delle macchine virtuali abilitate per GPU.

## <a name="create-your-dsvm"></a>Creare la propria DSVM

Per creare un'istanza di DSVM:

1. Passare al [portale di Azure](https://portal.azure.com). È possibile che venga richiesto di accedere all'account Azure, se non è stato ancora eseguito l'accesso.
1. Trovare l'elenco delle macchine virtuali digitando "data science virtual machine", quindi selezionare "Data Science Virtual Machine - Windows 2019".

1. Selezionare quindi il pulsante **Crea** nella parte inferiore.

1. Si verrà reindirizzati al pannello "Crea macchina virtuale".

1. Compilare la scheda **Nozioni di base**.
      * **Sottoscrizione** Se si hanno più sottoscrizioni, selezionare quella in cui verrà creata e fatturata la macchina virtuale. È necessario disporre di privilegi di creazione delle risorse per questa sottoscrizione.
      * **Gruppo di risorse**: creare un nuovo gruppo di risorse o selezionarne uno esistente.
      * **Nome macchina virtuale**: immettere il nome della macchina virtuale. È il nome con cui verrà visualizzata nel portale di Azure.
      * **Località**: selezionare il data center più appropriato. Per l'accesso più veloce alla rete, in genere è il data center che include la maggior parte dei dati o è più vicino alla propria posizione fisica. Vedere altre informazioni sulle [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Immagine**: Lasciare il valore predefinito.
      * **Size**: questo campo dovrebbe essere completato automaticamente con una dimensione appropriata per carichi di lavoro generali. Vedere altre informazioni sulle [dimensioni delle VM Windows in Azure](../../virtual-machines/sizes.md).
      * **Nome utente**: immettere il nome utente dell'amministratore. È il nome utente che verrà usato per accedere alla macchina virtuale e non deve necessariamente essere uguale al nome utente Azure.
      * **Password**: immettere la password che si userà per accedere alla macchina virtuale.    
1. Selezionare **Rivedi e crea**.
1. **Rivedi e crea**
   * Verificare che tutte le informazioni immesse siano corrette. 
   * Selezionare **Create** (Crea).


> [!NOTE]
> * Non vengono addebitati costi di licenza per il software precaricato nella macchina virtuale. Viene invece addebitato il costo di calcolo per le dimensioni del server scelte nel passaggio **Dimensione**.
> * Il provisioning richiede da 10 a 20 minuti. È possibile visualizzare lo stato della macchina virtuale nel portale di Azure.

## <a name="access-the-dsvm"></a>Accedere alla DSVM

Dopo la creazione e il provisioning della VM, seguire la procedura illustrata per [connettersi alla macchina virtuale basata su Azure](../../marketplace/azure-vm-create-using-approved-base.md). Usare le credenziali dell'account amministratore configurate nel passaggio **Informazioni di base** per la creazione di una macchina virtuale. 

Si è pronti per iniziare a usare gli strumenti installati e configurati nella VM. Molti strumenti sono accessibili tramite i riquadri del menu **Start** e le icone del desktop.

<a name="tools"></a>


## <a name="next-steps"></a>Passaggi successivi

* Esplorare gli strumenti disponibili nella DSVM aprendo il menu **Start**.
* Per informazioni su Azure Machine Learning, vedere [Informazioni su Azure Machine Learning](../overview-what-is-azure-ml.md) e seguire le [esercitazioni](../index.yml).
* Leggere l'articolo [Data science con Data Science Virtual Machine per Windows in Azure](./vm-do-ten-things.md)
