---
title: Distribuire monitoraggio di Azure per le soluzioni SAP con la portale di Azure
description: Distribuire monitoraggio di Azure per le soluzioni SAP con la portale di Azure
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: d9febb4efba85d47abe1cc11a3cb52dc0393c036
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672005"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Distribuire monitoraggio di Azure per le soluzioni SAP con portale di Azure

Il monitoraggio di Azure per le risorse di soluzioni SAP può essere creato tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal). Questo metodo fornisce un'interfaccia utente basata su browser per distribuire monitoraggio di Azure per le soluzioni SAP e configurare i provider.

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-monitoring-resource"></a>Crea risorsa di monitoraggio

1. Selezionare **monitoraggio di Azure per le soluzioni SAP** da **Azure Marketplace**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Immagine mostra la selezione dell'offerta monitoraggio di Azure per soluzioni SAP da Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Nella scheda **nozioni di base** specificare i valori necessari. Se applicabile, è possibile usare un'area di lavoro Log Analytics esistente.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Visualizzazione delle opzioni di configurazione portale di Azure." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. Quando si seleziona una rete virtuale, assicurarsi che i sistemi da monitorare siano raggiungibili dall'interno di tale VNET. 

   > [!IMPORTANT]
   > Selezionando **share** per la condivisione di dati con Microsoft, i team di supporto forniscono supporto aggiuntivo.

## <a name="configure-providers"></a>Configurare provider

### <a name="sap-hana-provider"></a>Provider di SAP HANA 

1. Selezionare la scheda **provider** per aggiungere i provider che si desidera configurare. È possibile aggiungere più provider uno dopo l'altro oppure aggiungerli dopo la distribuzione della risorsa di monitoraggio. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Mostra la scheda provider per aggiungere altri provider al monitoraggio di Azure per le soluzioni SAP." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Selezionare **Aggiungi provider** e scegliere **SAP Hana** dall'elenco a discesa. 

   > [!IMPORTANT]
   > Verificare che SAP HANA provider sia configurato per SAP HANA nodo ' Master '.

3. Immettere l'indirizzo IP privato per il server HANA.

4. Immettere il nome del tenant del database che si desidera utilizzare. È possibile scegliere qualsiasi tenant, tuttavia è consigliabile usare **SYSTEMDB** perché consente una matrice più ampia di aree di monitoraggio. 

5. Immettere il numero di porta SQL associato al database HANA. Il numero di porta deve essere nel formato **[3]**  +  **[istanza #]**  +  **[13]**. Ad esempio, 30013. 

6. Immettere il nome utente del database che si desidera utilizzare. Verificare che per l'utente del database siano assegnati i ruoli di **monitoraggio** e **lettura Catalogo** . 

7. Al termine, selezionare **Aggiungi provider**. Continuare ad aggiungere altri provider in base alle esigenze oppure selezionare **Verifica + crea** per completare la distribuzione.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Immagine delle opzioni di configurazione quando si aggiungono informazioni sul provider." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Provider del cluster a disponibilità elevata (pacemaker)

1. Selezionare il **cluster a disponibilità elevata (pacemaker)** nell'elenco a discesa. 

   > [!IMPORTANT]
   > Per configurare il provider del cluster a disponibilità elevata (pacemaker), verificare che ha_cluster_provider sia installato in ogni nodo. Per altre informazioni, vedere l'utilità di [esportazione cluster a disponibilità elevata](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Immettere l'endpoint Prometheus nel formato http://IP:9664/metrics . 
 
3. Immettere l'ID di sistema (SID), il nome host e il nome del cluster.

4. Al termine, selezionare **Aggiungi provider**. Continuare ad aggiungere altri provider in base alle esigenze oppure selezionare **Verifica + crea** per completare la distribuzione.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Image Mostra le opzioni correlate al provider pacemaker del cluster a disponibilità elevata." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="os-linux-provider"></a>Provider del sistema operativo (Linux) 

1. Selezionare sistema operativo (Linux) nell'elenco a discesa 

> [!IMPORTANT]
> Per configurare il provider del sistema operativo (Linux), assicurarsi che Node_Exporter sia installato in ogni istanza di BareMetal. Per ulteriori informazioni, vedere [Node_Exporter](https://github.com/prometheus/node_exporter)

2. Immettere un nome, che sarà l'identificatore per l'istanza di BareMetal.
3. Immettere l'endpoint dell'utilità di esportazione del nodo nel formato http://IP:9100/metrics .
4. Al termine, selezionare **Aggiungi provider**. Continuare ad aggiungere altri provider in base alle esigenze oppure selezionare **Verifica + crea**   per completare la distribuzione. 


### <a name="microsoft-sql-server-provider"></a>Provider Microsoft SQL Server

1. Prima di aggiungere il provider di Microsoft SQL Server, è necessario eseguire lo script seguente in SQL Server Management Studio per creare un utente con le autorizzazioni appropriate necessarie per configurare il provider.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Selezionare **Aggiungi provider** e scegliere **Microsoft SQL Server** dall'elenco a discesa. 

3. Compilare i campi usando le informazioni associate all'Microsoft SQL Server. 

4. Al termine, selezionare **Aggiungi provider**. Continuare ad aggiungere altri provider in base alle esigenze oppure selezionare **Verifica + crea** per completare la distribuzione.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Image Mostra informazioni correlate all'aggiunta del provider di Microsoft SQL Server." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su [monitoraggio di Azure per le soluzioni SAP](azure-monitor-overview.md)
