---
title: Risolvere i problemi di installazione locale del cluster di Azure Service Fabric
description: In questo articolo viene illustrata una serie di suggerimenti per la risoluzione dei problemi del cluster di sviluppo locale
ms.topic: conceptual
ms.date: 02/23/2018
ms.openlocfilehash: 20948cd1626c02d73fb6e9ef096b552bbab627fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575908"
---
# <a name="troubleshoot-your-local-development-cluster-setup"></a>Risolvere i problemi di configurazione del cluster di sviluppo locale
Se si verifica un problema durante l'interazione con il cluster di sviluppo di Service Fabric di Azure locale, esaminare i suggerimenti seguenti per possibili soluzioni.

## <a name="cluster-setup-failures"></a>Errori di configurazione del cluster
### <a name="cannot-clean-up-service-fabric-logs"></a>Impossibile pulire i log di Infrastruttura di servizi
#### <a name="problem"></a>Problema
Quando si esegue lo script DevClusterSetup, viene visualizzato l'errore seguente:

```output
Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
At line:1 char:1 + .\DevClusterSetup.ps1
+ ~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
+ FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1
```

#### <a name="solution"></a>Soluzione
Chiudere la finestra di PowerShell corrente e aprire una nuova finestra di PowerShell come amministratore. Ora è possibile eseguire lo script correttamente.

## <a name="cluster-connection-failures"></a>Errori di connessione del cluster

### <a name="type-initialization-exception"></a>Eccezione di inizializzazione del tipo
#### <a name="problem"></a>Problema
Quando ci si connette al cluster in PowerShell, viene visualizzato l'errore TypeInitializationException per System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Soluzione
La variabile di percorso non è stata impostata correttamente durante l'installazione. Disconnettersi da Windows e accedere nuovamente. Il percorso risulterà aggiornato.

### <a name="cluster-connection-fails-with-object-is-closed"></a>La connessione del cluster ha esito negativo con il messaggio "L’oggetto è chiuso"
#### <a name="problem"></a>Problema
Una chiamata a Connect-ServiceFabricCluster ha esito negativo con un errore simile a quello indicato di seguito:

```output
Connect-ServiceFabricCluster : The object is closed.
At line:1 char:1
+ Connect-ServiceFabricCluster
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
+ FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster
```

#### <a name="solution"></a>Soluzione
Chiudere la finestra di PowerShell corrente e aprire una nuova finestra di PowerShell come amministratore.

### <a name="fabric-connection-denied-exception"></a>Eccezione di connessione a Fabric negata
#### <a name="problem"></a>Problema
Durante il debug da Visual Studio, si verifica un errore FabricConnectionDeniedException.

#### <a name="solution"></a>Soluzione
Questo errore si verifica in genere quando si cerca di avviare un processo host servizio manualmente.

Assicurarsi di non disporre di progetti di servizio impostati come progetti di avvio nella soluzione. Solo i progetti di applicazione di Infrastruttura di servizi devono essere impostati come progetti di avvio.

> [!TIP]
> Se, in seguito alla configurazione, il cluster locale inizia a presentare un comportamento anomalo, può essere ripristinato con l'applicazione dell'area di notifica Local Cluster Manager. In questo modo viene rimosso il cluster esistente e ne viene configurato uno nuovo. Tenere presente che verranno rimosse anche tutte le applicazioni distribuite e i dati associati.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Comprendere e risolvere i problemi del cluster con report di integrità del sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

