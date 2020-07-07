---
title: Configurare gli ambienti di data science in Azure - Processo di data science per i team
description: Configurare gli ambienti per l'analisi scientifica dei dati in Azure da usare nel processo di analisi scientifica dei dati eseguito dai team.
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
ms.openlocfilehash: 6d1185e13397b3e466bd1e8d609a46f2f68bf390
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80063944"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Impostare gli ambienti per la scienza dei dati per l'uso nel Processo di analisi scientifica dei dati per i team
Il Processo di analisi scientifica dei dati per i team usa diversi ambienti per la scienza dei dati per l'archiviazione, l'elaborazione e l'analisi dei dati. Tra questi rientrano l'archiviazione BLOB di Azure, diversi tipi di macchine virtuali di Azure, i cluster HDInsight (Hadoop) e le aree di lavoro di Azure Machine Learning. La scelta riguardante quale ambiente usare dipende dal tipo e dalla quantità di dati da modellare e dalla destinazione di quei dati nel cloud. 

* Per indicazioni sulle questioni da prendere in considerazione per prendere questa decisione, vedere [Pianificazione dell'ambiente di analisi scientifica dei dati di Azure Machine Learning](plan-your-environment.md). 
* Per un catalogo di alcuni degli scenari che potrebbero verificarsi quando si esegue l'analisi avanzata, vedere [Scenarios for the Team Data Science Process](plan-sample-scenarios.md)

Gli articoli seguenti descrivono come configurare i diversi ambienti di data science usati dal processo di data science per i team.

* [Azure storage-account](../../storage/common/storage-account-create.md) (Account di archiviazione di Azure)
* [HDInsight (Hadoop) cluster](customize-hadoop-cluster.md) (Cluster HDInsight (Hadoop))
* [Area di lavoro Azure Machine Learning Studio (classica)](../studio/create-workspace.md)

La **macchina virtuale per l'analisi scientifica dei dati di Microsoft** è disponibile anche come immagine di macchina virtuale di (VM) Azure. Questa macchina virtuale è pre-installata e configurata con diversi strumenti diffusi, usati comunemente per l'analisi dei dati e l'apprendimento automatico. La macchina virtuale per l'analisi scientifica dei dati è disponibile in Windows e Linux. Per altre informazioni, vedere [Introduzione alla Data Science Virtual Machine basata su cloud per Linux e Windows](../data-science-virtual-machine/overview.md).

Scopri come creare:

- [DSVM Windows](../data-science-virtual-machine/provision-vm.md)
- [Data Science Virtual Machine di Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [Data Science Virtual Machine di CentOS](../data-science-virtual-machine/linux-dsvm-intro.md)
