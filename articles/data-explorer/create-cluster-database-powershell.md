---
title: Creare un cluster di Azure Data Explorer & il database usando PowershellCreate an Azure Data Explorer cluster & DB using Powershell
description: Informazioni su come creare un database e un cluster di Esplora dati di Azure tramite PowerShell
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 690c3e281e65f54f240c70f7a6e5038f54102c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560593"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Creare un database e un cluster di Esplora dati di Azure tramite PowerShell

> [!div class="op_single_selector"]
> * [Portale](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modello ARM](create-cluster-database-resource-manager.md)  

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. In this article, you create a cluster and a database by using Powershell. È possibile eseguire i cmdlet e gli script di PowerShell in Windows, Linux o [Azure Cloud Shell](../cloud-shell/overview.md) con [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) per creare e configurare cluster e database di Esplora dati di Azure.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.4 o successiva. Eseguire `az --version` per controllare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Configurare i parametri

I passaggi seguenti non sono necessari se si eseguono i comandi in Azure Cloud Shell. Se si esegue l'interfaccia della riga di comando in locale, seguire i passaggi 1 e 2 per accedere ad Azure e impostare la sottoscrizione corrente:

1. Eseguire questo comando per accedere ad Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Impostare la sottoscrizione in cui si vuole creare il cluster:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Quando si esegue l'interfaccia della riga di comando di Azure in locale o in Azure Cloud Shell, è necessario installare il modulo Az.Kusto nel proprio dispositivo:

    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Creare il cluster di Esplora dati di Azure

1. Creare il cluster tramite il comando seguente:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | Nome | *mykustocluster* | Nome del cluster.|
   | Sku | *D13_v2* | SKU usato per il cluster. |
   | ResourceGroupName | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |

    Sono disponibili altri parametri facoltativi che è possibile usare, ad esempio la capacità del cluster.

1. Per verificare se il cluster è stato creato correttamente, eseguire il comando seguente:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Se il risultato contiene `provisioningState` con il valore `Succeeded`, il cluster è stato creato correttamente.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Creare il database nel cluster di Esplora dati di Azure

1. Creare il database tramite il comando seguente:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Impostazione** | **Valore consigliato** | **Descrizione campo**|
   |---|---|---|
   | ClusterName | *mykustocluster* | Nome del cluster in cui verrà creato il database.|
   | Nome | *mykustodatabase* | Nome del database.|
   | ResourceGroupName | *testrg* | Il nome del gruppo di risorse in cui verrà creato il cluster. |
   | SoftDeletePeriod | *3650:00:00:00* | Periodo di tempo in cui i dati verranno mantenuti disponibili in modo che sia possibile eseguire una query. |
   | HotCachePeriod | *3650:00:00:00* | Periodo di tempo in cui i dati verranno conservati nella cache. |

1. Eseguire il comando seguente per vedere il database creato:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

A questo punto sono disponibili un cluster e un database.

## <a name="clean-up-resources"></a>Pulire le risorse

* Se hai intenzione di seguire gli altri articoli, mantieni le risorse che hai creato.
* Per pulire le risorse, eliminare il cluster. Quando si elimina un cluster, vengono eliminati anche tutti i database al suo interno. Usare il comando seguente per eliminare il cluster:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Altri comandi Az.Kusto](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Inserire dati usando .NET Standard SDK di Esplora dati di Azure (anteprima)](net-standard-ingest-data.md)
