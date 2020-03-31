---
title: Creare un cluster di Azure Data Explorer & database con l'interfaccia della riga di comando di AzureCreate an Azure Data Explorer cluster & DB with Azure CLI
description: Informazioni su come creare un database e un cluster di Esplora dati di Azure tramite l'interfaccia della riga di comando di Azure
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 6b8c2924e50da095c3bc5c7db2d2bf48ef5a27c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561936"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-azure-cli"></a>Creare un database e un cluster di Esplora dati di Azure usando l'interfaccia della riga di comando di Azure

> [!div class="op_single_selector"]
> * [Portale](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modello ARM](create-cluster-database-resource-manager.md)

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. In this article, you create a cluster and a database by using Azure CLI.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, è necessaria una sottoscrizione di Azure.To complete this article, you need an Azure subscription. Se non ne hai uno, [crea un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per controllare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Configurare i parametri dell'interfaccia della riga di comando

I passaggi seguenti non sono necessari se si eseguono i comandi in Azure Cloud Shell. Se si esegue l'interfaccia della riga di comando in locale, seguire questa procedura per accedere ad Azure e impostare la sottoscrizione corrente:

1. Eseguire questo comando per accedere ad Azure:

    ```azurecli-interactive
    az login
    ```

1. Impostare la sottoscrizione in cui si vuole creare il cluster. Sostituire `MyAzureSub` con il nome della sottoscrizione di Azure che si vuole usare:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Creare il cluster di Esplora dati di Azure

1. Creare il cluster tramite il comando seguente:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | name | *azureclitest* | Nome del cluster.|
   | sku | *D13_v2* | SKU usato per il cluster. |
   | resource-group | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |

    Sono disponibili altri parametri facoltativi che è possibile usare, ad esempio la capacità del cluster.

1. Per verificare se il cluster è stato creato correttamente, eseguire il comando seguente:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Se il risultato contiene `provisioningState` con il valore `Succeeded`, il cluster è stato creato correttamente.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Creare il database nel cluster di Esplora dati di Azure

1. Creare il database tramite il comando seguente:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period P365D --hot-cache-period P31D
    ```

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | cluster-name | *azureclitest* | Nome del cluster in cui verrà creato il database.|
   | name | *clidatabase* | Nome del database.|
   | resource-group | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |
   | soft-delete-period | *P365D* | Indica il periodo di tempo in cui i dati verranno mantenuti disponibili in modo che sia possibile eseguire una query. Per altre informazioni, vedere [Criteri di conservazione](/azure/kusto/concepts/retentionpolicy). |
   | hot-cache-period | *P31D* | Indica il periodo di tempo in cui i dati verranno conservati nella cache. Per altre informazioni, vedere [Criteri della cache](/azure/kusto/concepts/cachepolicy). |

1. Eseguire il comando seguente per vedere il database creato:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

A questo punto sono disponibili un cluster e un database.

## <a name="clean-up-resources"></a>Pulire le risorse

* Se hai intenzione di seguire gli altri articoli, mantieni le risorse che hai creato.
* Per pulire le risorse, eliminare il cluster. Quando si elimina un cluster, vengono eliminati anche tutti i database al suo interno. Usare il comando seguente per eliminare il cluster:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire l'inserimento di dati usando la libreria Python di Azure Data ExplorerIngest data using the Azure Data Explorer Python library](python-ingest-data.md)
