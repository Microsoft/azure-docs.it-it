---
title: Aggiornare la versione di un cluster autonomo
description: Aggiornare il codice di Azure Service Fabric che esegue un cluster autonomo di Service Fabric.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 5831fcffe718e4966c3444a29d35c5bbd55e5f57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842938"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>Aggiornare la versione di Service Fabric eseguita nel cluster 

La possibilità di aggiornare un sistema moderno è fondamentale per il successo a lungo termine del prodotto. Un cluster di Azure Service Fabric è una risorsa di cui si è proprietari. Questo articolo descrive come aggiornare la versione di Service Fabric in esecuzione in un cluster autonomo.

> [!NOTE]
> Verificare che il cluster esegua sempre una versione di Service Fabric supportata. Quando Microsoft annuncia il rilascio di una nuova versione di Service Fabric, viene segnalato il termine del periodo di supporto per la versione precedente dopo un minimo di 60 giorni dalla data dell'annuncio. Le nuove versioni vengono annunciate nel [blog del team di Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). A questo punto è possibile scegliere la nuova versione.
>
>

È possibile aggiornare il cluster alla nuova versione solo se si usa una configurazione del nodo di tipo produzione, in cui ogni nodo di Service Fabric viene allocato in una macchina virtuale o fisica separata. Se si usa un cluster di sviluppo in cui sono presenti più nodi di Service Fabric in un'unica macchina virtuale o computer fisico, è necessario creare di nuovo il cluster con la nuova versione.

Due flussi di lavoro distinti possono aggiornare il cluster alla versione di Service Fabric più recente o a una versione supportata. Un flusso di lavoro è per i cluster con connettività che scaricano automaticamente la versione più recente. L'altro flusso di lavoro è per i cluster senza connettività che quindi non scaricano la versione più recente di Service Fabric.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Abilitare l'aggiornamento automatico della versione di Service Fabric del cluster
Per impostare il cluster per il download di aggiornamenti di Service Fabric quando Microsoft rilascia una nuova versione, impostare la configurazione cluster `fabricClusterAutoupgradeEnabled` su *true*. Per selezionare manualmente una versione supportata di Service Fabric per il cluster, impostare la configurazione cluster `fabricClusterAutoupgradeEnabled` su *false*.

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Aggiornare i cluster con la connettività per scaricare il codice e la configurazione più recenti
Seguire questa procedura per aggiornare il cluster a una versione supportata, se i nodi del cluster hanno la connettività Internet all'[Area download Microsoft](https://download.microsoft.com).

Per i cluster che hanno la connettività all'[Area download Microsoft](https://download.microsoft.com), viene periodicamente verificata la disponibilità di nuove versioni di Service Fabric.

Quando è disponibile una nuova versione di Service Fabric, il pacchetto viene scaricato localmente nel cluster e ne viene eseguito il provisioning per l'aggiornamento. Inoltre, per informare il cliente di questa nuova versione, il sistema visualizza un avviso esplicito di integrità del cluster simile al seguente:

"Il supporto per la versione corrente del cluster [numero versione] termina il [data]."

Quando il cluster inizia a eseguire la versione più recente, l'avviso non viene più visualizzato.

Quando viene visualizzato l'avviso di integrità del cluster, aggiornare il cluster:

1. Connettersi al cluster da qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel cluster. Il computer in cui viene eseguito lo script non deve necessariamente far parte del cluster.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Ottenere l'elenco delle versioni di Service Fabric a cui è possibile eseguire l'aggiornamento.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    L'output dovrebbe essere simile al seguente:

    ![Ottenere le versioni di Service Fabric][getfabversions]
3. Avviare un aggiornamento del cluster a una versione disponibile usando il comando [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade) di Windows PowerShell.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Per monitorare lo stato di avanzamento dell'aggiornamento, è possibile usare Service Fabric Explorer o eseguire il comando di PowerShell seguente:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Per specificare criteri di integrità personalizzati per il comando Start-ServiceFabricClusterUpgrade, vedere la documentazione relativa a [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Dopo aver risolto i problemi che hanno determinato il ripristino dello stato precedente, avviare di nuovo l'aggiornamento ripetendo la procedura descritta prima.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Aggiornare i cluster *senza la connettività* per scaricare il codice e la configurazione più recenti
Seguire questa procedura per aggiornare il cluster a una versione supportata, se i nodi del cluster non hanno la connettività Internet all'[Area download Microsoft](https://download.microsoft.com).

> [!NOTE]
> Se si esegue un cluster non connesso a Internet, per informazioni sul rilascio di nuove versioni, è necessario monitorare il [blog del team di Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric). Il sistema non visualizza alcun avviso di integrità del cluster per informare l'utente di una nuova versione.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Confronto tra provisioning automatico e provisioning manuale
Per consentire il download e la registrazione automatici per la versione più recente del codice, impostare il servizio di aggiornamento di Service Fabric. Per istruzioni, vedere *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* nel [pacchetto autonomo](service-fabric-cluster-standalone-package-contents.md).

Per il processo manuale, seguire le istruzioni riportate di seguito.

Modificare la configurazione del cluster per impostare la proprietà seguente su *false* prima di avviare un aggiornamento della configurazione:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Per informazioni dettagliate sull'uso, vedere il comando di PowerShell [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Prima di avviare l'aggiornamento della configurazione, verificare di aggiornare "clusterConfigurationVersion" nel file JSON.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Flusso di lavoro per l'aggiornamento del cluster

1. Eseguire [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade) da uno dei nodi del cluster e annotare il valore di *TargetCodeVersion*.

2. Eseguire il comando seguente da un computer connesso a Internet per elencare tutte le versioni compatibili di l'aggiornamento con la versione corrente e scaricare il pacchetto corrispondente dai collegamenti di download associati:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Connettersi al cluster da qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel cluster. Il computer in cui viene eseguito lo script non deve necessariamente far parte del cluster.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Copiare il pacchetto scaricato nell'archivio immagini del cluster.

5. Registrare il pacchetto copiato.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Avviare un aggiornamento del cluster a una versione disponibile.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    È possibile monitorare lo stato di avanzamento dell'aggiornamento in Service Fabric Explorer oppure è possibile eseguire questo comando di PowerShell:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Per specificare criteri di integrità personalizzati per il comando Start-ServiceFabricClusterUpgrade, vedere la documentazione relativa a [Start-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/start-servicefabricclusterupgrade).

    Dopo aver risolto i problemi che hanno determinato il ripristino dello stato precedente, avviare di nuovo l'aggiornamento ripetendo la procedura descritta prima.

## <a name="next-steps"></a>Passaggi successivi
* [Aggiornare la configurazione di un cluster autonomo](service-fabric-cluster-config-upgrade-windows-server.md)
* Personalizzare alcune [impostazioni di un cluster di Service Fabric](service-fabric-cluster-fabric-settings.md).
* [Aumentare o ridurre le istanze del cluster](service-fabric-cluster-scale-in-out.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
