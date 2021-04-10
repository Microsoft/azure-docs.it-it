---
title: Creare cluster di Apache Hadoop usando modelli-Azure HDInsight
description: Informazioni su come creare cluster per HDInsight usando modelli di Resource Manager
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/07/2020
ms.openlocfilehash: 43f9736ce902d4c195d6b07cfdf7011ba9ca0c2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702743"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Questo articolo illustra diversi modi per creare cluster Azure HDInsight usando [modelli di Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md). Per informazioni su altri strumenti e funzionalità per la creazione di cluster, fare clic sul selettore di schede nella parte superiore di questa pagina. Vedere anche [metodi di creazione di cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Modelli di Gestione risorse

Un modello di Gestione risorse rende più semplice creare le risorse seguenti per l'applicazione in un'unica operazione coordinata:

* Cluster HDInsight e le relative risorse dipendenti, ad esempio l'account di archiviazione predefinito.
* Altre risorse, ad esempio il database SQL di Azure per usare [Apache Sqoop](https://sqoop.apache.org/).

Nel modello si definiscono le risorse necessarie per l'applicazione. È anche possibile specificare i parametri di distribuzione per immettere valori per ambienti diversi. Il modello è composto da JSON ed espressioni da usare per creare valori per la distribuzione.

È possibile trovare esempi di modelli HDInsight nei [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight). Usare lo strumento multipiattaforma [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) con l'[estensione per Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) o un editor di testo per salvare il modello in un file sulla workstation.

Per altre informazioni sui modelli di Resource Manager, consultare gli articoli e gli esempi seguenti:

* [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/templates/template-syntax.md)
* [Distribuire un'applicazione con i modelli di Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* Informazioni di riferimento sul modello [Microsoft.HDInsight/clusters](/azure/templates/microsoft.hdinsight/allversions)
* [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Generare modelli

Gestione risorse consente di esportare un modello di Gestione risorse dalle risorse esistenti nella sottoscrizione usando diversi strumenti. Il modello generato può essere usato per ottenere informazioni sulla sintassi del modello o per automatizzare la ridistribuzione della soluzione in base alle esigenze. Per ulteriori informazioni, vedere [Export templates](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Distribuire tramite il portale

È possibile distribuire un modello di Gestione risorse tramite il portale di Azure. Per altre informazioni, vedere [Distribuire risorse da un modello personalizzato](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Distribuire tramite PowerShell

È possibile distribuire un modello di Gestione risorse tramite Azure PowerShell. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) e [Distribuire un modello di Resource Manager privato con un token di firma di accesso condiviso e Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Eseguire la distribuzione usando l'interfaccia della riga di comando di Azure

È possibile distribuire un modello di Gestione risorse tramite l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md) e [Distribuire un modello di Resource Manager privato con un token di firma di accesso condiviso e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Distribuire tramite l'API REST

È possibile distribuire un modello di Gestione risorse tramite l'API REST. Per altre informazioni, vedere [Distribuire le risorse con i modelli e l'API REST di Resource Manager](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Distribuire con Visual Studio

 Usare Visual Studio per creare un progetto del gruppo di risorse e distribuirlo in Azure mediante l'interfaccia utente. Selezionare il tipo di risorse da includere nel progetto. Tali risorse vengono aggiunte automaticamente al modello di Resource Manager. Il progetto fornisce anche uno script di PowerShell per distribuire il modello.

Per un'introduzione all'uso di Visual Studio con gruppi di risorse, vedere [Creazione e distribuzione di gruppi di risorse di Azure tramite Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* Per altri modelli correlati a HDInsight, vedere [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Per un esempio di distribuzione delle risorse tramite la libreria client .NET, vedere [Distribuire le risorse usando le librerie .NET e un modello](/previous-versions/azure/virtual-machines/windows/csharp-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Per un esempio dettagliato di distribuzione di un'applicazione, vedere [Effettuare il provisioning di microservizi e distribuirli in modo prevedibile in Azure](../app-service/deploy-complex-application-predictably.md).
* Per indicazioni sulla distribuzione della soluzione in ambienti diversi, vedere [Ambienti di sviluppo e test in Microsoft Azure](../devtest-labs/devtest-lab-overview.md).
* Per informazioni sulle sezioni del modello di Azure Resource Manager, vedere [Creazione di modelli](../azure-resource-manager/templates/template-syntax.md).
* Per un elenco delle funzioni che è possibile usare in un modello di Azure Resource Manager, vedere [Funzioni di modello](../azure-resource-manager/templates/template-functions.md).