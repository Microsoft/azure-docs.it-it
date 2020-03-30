---
title: Configurare connessioni cluster di Azure Service Fabric sicure
description: Scoprire come usare Visual Studio per configurare connessioni sicure supportate dal cluster di Service Fabric di Azure.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 11f76153726d3fc92118fb46cc61b4627ab6a1b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464087"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Configurare connessioni protette a un cluster Service Fabric da Visual Studio
Scoprire come usare Visual Studio per accedere in modo sicuro a un cluster di Service Fabric di Azure con i criteri di controllo di accesso configurati.

## <a name="cluster-connection-types"></a>Tipi di connessione del cluster
Il cluster di Service Fabric di Azure supporta due tipi di connessioni: connessioni **non sicure** e connessioni sicure **basate sui certificati x509**. Per i cluster di Service Fabric ospitati in locale sono supportate anche le autenticazioni **di Windows** e **dSTS.** È necessario configurare il tipo di connessione cluster durante la creazione del cluster. Una volta creato, il tipo di connessione non può essere modificato.

Gli strumenti di Service Fabric di Visual Studio supportano tutti i tipi di autenticazione per la connessione a un cluster per la pubblicazione. Vedere [Configurazione di cluster di infrastruttura di servizi dal portale di Azure](service-fabric-cluster-creation-via-portal.md) per istruzioni su come configurare un cluster Service Fabric sicuro.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Configurare le connessioni del cluster in profili di pubblicazione
Se si pubblica un progetto di Service Fabric da Visual Studio, usare la finestra di dialogo **Pubblica applicazione di Service Fabric** per scegliere un cluster di Azure Service Fabric. In **Endpoint connessione** selezionare un cluster esistente nella sottoscrizione.

![La finestra di dialogo **Pubblica applicazione di Service Fabric** viene usata per configurare una connessione di Service Fabric.][publishdialog]

La finestra di dialogo **Pubblica applicazione di Service Fabric** convalida automaticamente la connessione del cluster. Se richiesto, accedere all'account di Azure. Se la convalida riesce, significa che il sistema ha i certificati corretti installati per la connessione al cluster in modo sicuro, o il cluster non è protetto. Gli errori di convalida possono essere causati da problemi di rete o se il sistema non è stato correttamente configurato per connettersi a un cluster sicuro.

![La finestra di dialogo **Pubblica applicazione di Service Fabric** convalida una connessione al cluster di Service Fabric esistente correttamente configurata.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Per connettersi a un cluster protetto
1. Assicurarsi che sia possibile accedere a uno dei certificati client attendibili per il cluster di destinazione. Il certificato viene condiviso in genere come un file di scambio di informazioni personali (.pfx). Vedere [Configurazione di cluster di infrastruttura di servizi dal portale di Azure](service-fabric-cluster-creation-via-portal.md) per informazioni su come configurare il server e garantire l'accesso a un client.
2. Installare il certificato attendibile. A tale scopo, fare doppio clic sul file .pfx, o usare lo script di PowerShell Import-PfxCertificate per importare i certificati. Installare il certificato in **Cert:\LocalMachine\My**. È anche possibile accettare tutte le impostazioni predefinite durante l'importazione del certificato.
3. Scegliere il comando **Pubblica...** nel menu di scelta rapida del progetto per aprire la finestra di dialogo **Pubblica applicazione Azure** e quindi selezionare il cluster di destinazione. Lo strumento automaticamente risolve la connessione e salva i parametri di connessione protetta nel profilo di pubblicazione.
4. Facoltativo: è possibile modificare il profilo di pubblicazione in modo da specificare una connessione sicura al cluster.
   
   Poiché si sta modificando manualmente il file XML del profilo di pubblicazione per specificare le informazioni del certificato, assicurarsi di prendere nota del nome di archivio del certificati, la posizione dell’archivio e l’identificazione personale del certificato. È necessario fornire questi valori per il nome dell'archivio del certificato e il percorso dell'archivio. Per altre informazioni, vedere [Procedura: recuperare l'identificazione personale di un certificato](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx).
   
   È possibile usare i parametri *ClusterConnectionParameters* per specificare i parametri di PowerShell da utilizzare quando ci si connette al cluster di Service Fabric. I parametri validi sono quelli che vengono accettati dal cmdlet Connect-ServiceFabricCluster. Per un elenco dei parametri disponibili, vedere [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) .
   
   Se si pubblica in un cluster remoto, è necessario specificare i parametri appropriati per tale cluster specifico. Di seguito è riportato un esempio per la connessione a un cluster non sicuro:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Di seguito è riportato un esempio per la connessione a un cluster protetto basato sul certificato x509:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Modificare tutte le altre impostazioni necessarie, ad esempio i parametri di aggiornamento e la posizione del file del parametro dell'applicazione, quindi pubblicare l'applicazione dalla finestra di dialogo **Publish Service Fabric Application** in Visual Studio.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sull'accesso ai cluster di Infrastruttura di servizi, vedere l'articolo relativo a come [visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png
