---
title: Configurare un gruppo di failover
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Informazioni su come configurare un gruppo di failover automatico per un database SQL di Azure (singolo e in pool) e SQL Istanza gestita usando il portale di Azure, l'interfaccia della riga di comando di Azure e PowerShell.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/14/2019
ms.openlocfilehash: a2f0cb683669aa092493c8080d5e4646cf9706c3
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477935"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Configurare un gruppo di failover per database SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Questo argomento illustra come configurare un gruppo di [failover](auto-failover-group-overview.md) automatico per database SQL di Azure e Istanza gestita di SQL di Azure.

## <a name="single-database"></a>Database singolo

Creare il gruppo di failover e aggiungerne uno singolo usando il portale di Azure o PowerShell.

### <a name="prerequisites"></a>Prerequisiti

Considerare i prerequisiti seguenti:

- L'account di accesso del server e le impostazioni del firewall per il server secondario devono corrispondere a quello del server primario.

### <a name="create-failover-group"></a>Create failover group

# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il gruppo di failover e aggiungerne il database singolo usando il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare il database da aggiungere al gruppo di failover.
1. In **Nome del server** selezionare il nome del server per aprire le impostazioni del server.

   ![Aprire il server per database singolo](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Nel riquadro **Impostazioni** selezionare **Gruppi di failover** e quindi selezionare **Aggiungi gruppo** per creare un nuovo gruppo di failover.

   ![Aggiungere un nuovo gruppo di failover](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Nella pagina **Gruppo di** failover immettere o selezionare i valori necessari e quindi selezionare **Crea.**

   - **Database all'interno del** gruppo: scegliere il database da aggiungere al gruppo di failover. Aggiungendo il database al gruppo di failover viene avviato automaticamente il processo di replica geografica.

   ![Aggiungere il database SQL al gruppo di failover](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gruppo di failover e aggiungervi il database tramite PowerShell.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      â€“ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      â€“FailoverGroupName $failoverGroupName `
      â€“FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

---

### <a name="test-failover"></a>Failover di test

Testare il failover del gruppo di failover usando portale di Azure o PowerShell.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Testare il failover del gruppo di failover con il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare "Azure SQL" nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare il database da aggiungere al gruppo di failover.

   ![Aprire il server per database singolo](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Selezionare **Gruppi di** failover nel **riquadro** Impostazioni e quindi scegliere il gruppo di failover appena creato.
  
   ![Selezionare il gruppo di failover nel portale](./media/auto-failover-group-configure/select-failover-group.png)

1. Verificare qual è il server primario e quale quello secondario.
1. Selezionare **Failover** nel riquadro attività per eseguire il failover del gruppo di failover contenente il database.
1. Selezionare **Sì** nella finestra dell'avviso che informa che le sessioni TDS verranno disconnesse.

   ![Failover del gruppo di failover contenente il database](./media/auto-failover-group-configure/failover-sql-db.png)

1. Verificare quale server è ora il server primario e quale quello secondario. Se il failover è riuscito, i due server dovrebbero essersi scambiati i ruoli.
1. Selezionare di nuovo **Failover** per ripristinare i ruoli originali dei server.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testare il failover del gruppo di failover con PowerShell.  

Verificare il ruolo della replica secondaria:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Effettuare il failover nel server secondario:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

Ripristinare il gruppo di failover nel server primario:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Se è necessario eliminare il database secondario, rimuoverlo dal gruppo di failover prima di eliminarlo. L'eliminazione di un database secondario prima della rimozione dal gruppo di failover può causare comportamenti imprevisti.

## <a name="elastic-pool"></a>Pool elastico

Creare il gruppo di failover e aggiungerne un pool elastico usando il portale di Azure o PowerShell.  

### <a name="prerequisites"></a>Prerequisiti

Considerare i prerequisiti seguenti:

- L'account di accesso del server e le impostazioni del firewall per il server secondario devono corrispondere a quello del server primario.

### <a name="create-the-failover-group"></a>Creare il gruppo di failover

Creare il gruppo di failover per il pool elastico usando portale di Azure o PowerShell.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il gruppo di failover e aggiungerne il pool elastico usando il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare "Azure SQL" nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare il pool elastico da aggiungere al gruppo di failover.
1. Nel riquadro **Panoramica** selezionare il nome del server in **Nome del server** per aprire le impostazioni del server.
  
   ![Aprire il server per il pool elastico](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. Nel riquadro **Impostazioni** selezionare **Gruppi di failover** e quindi selezionare **Aggiungi gruppo** per creare un nuovo gruppo di failover.

   ![Aggiungere un nuovo gruppo di failover](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. Nella pagina **Gruppo di** failover immettere o selezionare i valori necessari e quindi selezionare **Crea**. Creare un nuovo server secondario o selezionare un server secondario esistente.

1. Selezionare **Database all'interno del gruppo** e quindi scegliere il pool elastico da aggiungere al gruppo di failover. Se nel server secondario non esiste già un pool elastico, viene visualizzato un avviso che richiede di creare un pool elastico nel server secondario. Selezionare l'avviso e quindi scegliere **OK** per creare il pool elastico nel server secondario.

   ![Aggiungere il pool elastico al gruppo di failover](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. Selezionare **Seleziona** per applicare le impostazioni del pool elastico al gruppo di failover, quindi selezionare **Crea** per creare il gruppo di failover. Aggiungendo il pool elastico al gruppo di failover viene avviato automaticamente il processo di replica geografica.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gruppo di failover e aggiungerne il pool elastico usando PowerShell.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
       â€“ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       â€“FailoverGroupName $failoverGroupName `
       â€“FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully."

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...."
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..."
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases
   Write-host "Databases added to failover group successfully."
  ```

---

### <a name="test-failover"></a>Failover di test

Testare il failover del pool elastico usando portale di Azure o PowerShell.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Eseguire il failover del gruppo di failover nel server secondario e quindi eseguire il fail back usando il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare "Azure SQL" nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare il pool elastico da aggiungere al gruppo di failover.
1. Nel riquadro **Panoramica** selezionare il nome del server in **Nome del server** per aprire le impostazioni del server.

   ![Aprire il server per il pool elastico](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. Nel riquadro **Impostazioni** selezionare **Gruppi di failover** e quindi selezionare il gruppo di failover creato nella sezione 2.
  
   ![Selezionare il gruppo di failover nel portale](./media/auto-failover-group-configure/select-failover-group.png)

1. Verificare qual è il server primario e quale quello secondario.
1. Selezionare **Failover** nel riquadro attività per eseguire il failover del gruppo di failover che contiene il pool elastico.
1. Selezionare **Sì** nella finestra dell'avviso che informa che le sessioni TDS verranno disconnesse.

   ![Failover del gruppo di failover contenente il database](./media/auto-failover-group-configure/failover-sql-db.png)

1. Verificare qual è il server primario e quale quello secondario. Se il failover è riuscito, i due server dovrebbero essersi scambiati i ruoli.
1. Selezionare di nuovo **Failover** per ripristinare le impostazioni originali del gruppo di failover.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testare il failover del gruppo di failover con PowerShell.

Verificare il ruolo della replica secondaria:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Effettuare il failover nel server secondario:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

---

> [!IMPORTANT]
> Se è necessario eliminare il database secondario, rimuoverlo dal gruppo di failover prima di eliminarlo. L'eliminazione di un database secondario prima della rimozione dal gruppo di failover può causare comportamenti imprevisti.

## <a name="sql-managed-instance"></a>Istanza gestita di SQL

Creare un gruppo di failover tra due istanze gestite Istanza gestita di SQL di Azure usando il portale di Azure o PowerShell.

È necessario configurare [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) o creare un gateway per la rete virtuale di ogni istanza di SQL Istanza gestita, connettere i due gateway e quindi creare il gruppo di failover. 

Distribuire entrambe le istanze gestite in [aree associate](../../best-practices-availability-paired-regions.md) per motivi di prestazioni. Le istanze gestite che si trovano in aree geografiche associate hanno prestazioni di gran lunga migliori di quelle che si trovano in aree non associate. 

### <a name="prerequisites"></a>Prerequisiti

Si considerino i prerequisiti seguenti:

- L'istanza gestita secondaria deve essere vuota.
- L'intervallo di subnet per la rete virtuale secondaria non deve sovrapporsi all'intervallo di subnet della rete virtuale primaria.
- Le regole di confronto e il fuso orario dell'istanza gestita secondaria devono corrispondere a quello dell'istanza gestita primaria.
- Quando si connettono i due gateway, **la chiave condivisa** deve essere la stessa per entrambe le connessioni.

### <a name="create-primary-virtual-network-gateway"></a>Creare un gateway di rete virtuale primario

Se ExpressRoute non è [stato configurato,](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)è possibile creare il gateway di rete virtuale primario con il portale di Azure o PowerShell.

> [!NOTE]
> Lo SKU del gateway influisce sulle prestazioni della velocità effettiva. Questo articolo distribuisce un gateway con lo SKU di base ( `HwGw1` ). Per ottenere una velocità effettiva più elevata, distribuire uno SKU superiore (ad esempio: `VpnGw3`). Per tutte le opzioni disponibili, vedere [SKU del gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il gateway di rete virtuale primario usando il portale di Azure.

1. Nel [portale di Azure](https://portal.azure.com) passare al gruppo di risorse e selezionare la risorsa **Rete virtuale** per l'istanza gestita primaria.
1. Selezionare **Subnet** in **Impostazioni** e quindi selezionare l'opzione per aggiungere una nuova **subnet del gateway**. Lasciare invariati i valori predefiniti.

   ![Aggiungere il gateway per l'istanza gestita primaria](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. Dopo aver creato il gateway della subnet, selezionare **Crea risorsa** dal riquadro di spostamento a sinistra e quindi digitare `Virtual network gateway` nella casella di ricerca. Selezionare la risorsa **Gateway di rete virtuale** pubblicata da **Microsoft**.

   ![Creare un nuovo gateway di rete virtuale](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. Compilare i campi obbligatori per configurare il gateway nell'istanza gestita primaria.

   La tabella seguente mostra i valori necessari per il gateway dell'istanza gestita primaria:

    | **Campo** | valore |
    | --- | --- |
    | **Sottoscrizione** |  Sottoscrizione in cui trova l'istanza gestita primaria. |
    | **Nome** | Nome del gateway di rete virtuale. |
    | **Area** | Area in cui trova l'istanza gestita primaria. |
    | **Tipo di gateway** | Selezionare **VPN**. |
    | **Tipo di VPN** | Selezionare **Basato su route** |
    | **SKU**| Lasciare l'impostazione predefinita `VpnGw1`. |
    | **Posizione**| Posizione in cui si trova l'istanza gestita secondaria e la rete virtuale secondaria.   |
    | **Rete virtuale**| Selezionare la rete virtuale per l'istanza gestita secondaria. |
    | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
    | **Nome indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP. |
    | &nbsp; | &nbsp; |

1. Lasciare invariati gli altri valori predefiniti e quindi selezionare **Rivedi e crea** per rivedere le impostazioni del gateway di rete virtuale.

   ![Impostazioni del gateway primario](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. Selezionare **Crea** per creare il nuovo gateway di rete virtuale.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gateway di rete virtuale primario usando PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000

   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location

   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id

   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Creare un gateway di rete virtuale secondario

Creare il gateway di rete virtuale secondario usando portale di Azure o PowerShell.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Ripetere i passaggi nella sezione precedente per creare la subnet della rete virtuale e il gateway per l'istanza gestita secondaria. Compilare i campi obbligatori per configurare il gateway per l'istanza gestita secondaria.

La tabella seguente mostra i valori necessari per il gateway dell'istanza gestita secondaria:

   | **Campo** | valore |
   | --- | --- |
   | **Sottoscrizione** |  Sottoscrizione in cui trova l'istanza gestita secondaria. |
   | **Nome** | Nome del gateway di rete virtuale, ad esempio `secondary-mi-gateway`. |
   | **Area** | Area in cui trova l'istanza gestita secondaria. |
   | **Tipo di gateway** | Selezionare **VPN**. |
   | **Tipo di VPN** | Selezionare **Basato su route** |
   | **SKU**| Lasciare l'impostazione predefinita `VpnGw1`. |
   | **Posizione**| Posizione in cui si trova l'istanza gestita secondaria e la rete virtuale secondaria.   |
   | **Rete virtuale**| Selezionare la rete virtuale creata nella sezione 2, ad esempio `vnet-sql-mi-secondary`. |
   | **Indirizzo IP pubblico**| Selezionare **Crea nuovo**. |
   | **Nome indirizzo IP pubblico**| Immettere un nome per l'indirizzo IP, ad esempio `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Impostazioni del gateway secondario](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gateway di rete virtuale secondario usando PowerShell.

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000

   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location

   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id

   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn

   $gw2
   ```

---

### <a name="connect-the-gateways"></a>Connettere i gateway

Creare connessioni tra i due gateway usando il portale di Azure o PowerShell.

È necessario creare due connessioni: la connessione dal gateway primario al gateway secondario e quindi la connessione dal gateway secondario al gateway primario.

La chiave condivisa usata per entrambe le connessioni deve essere la stessa per ogni connessione.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare connessioni tra i due gateway usando il portale di Azure.

1. Selezionare **Crea una risorsa** nel [portale di Azure](https://portal.azure.com).
1. Digitare `connection` nella casella di ricerca e quindi premere INVIO per avviare la ricerca. Verrà visualizzala la risorsa **Connessione**, pubblicata da Microsoft.
1. Fare clic su **Crea** per creare la connessione.
1. Nella scheda **Informazioni di** base selezionare i valori seguenti e quindi selezionare **OK.**
    1. Selezionare `VNet-to-VNet` per **Tipo di connessione**.
    1. Selezionare la sottoscrizione dall'elenco a discesa.
    1. Selezionare il gruppo di risorse per l'istanza gestita nell'elenco a discesa.
    1. Selezionare dall'elenco a discesa la località dell'istanza gestita primaria.
1. Nella scheda **Impostazioni** selezionare o immettere i valori seguenti e quindi selezionare **OK:**
    1. Scegliere il gateway di rete primario per **Primo gateway di rete virtuale**, ad esempio `Primary-Gateway`.  
    1. Scegliere il gateway di rete secondario per **Secondo gateway di rete virtuale**, ad esempio `Secondary-Gateway`.
    1. Selezionare la casella di controllo accanto a **Stabilisci connettività bidirezionale**.
    1. Lasciare il nome predefinito della connessione primaria o rinominarla con un valore di propria scelta.
    1. Specificare una **chiave condivisa (PSK)** per la connessione, ad esempio `mi1m2psk`.

   ![Creare una connessione gateway](./media/auto-failover-group-configure/create-gateway-connection.png)

1. Nella scheda **Riepilogo** esaminare le impostazioni per la connessione bidirezionale e quindi selezionare **OK** per creare la connessione.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare connessioni tra i due gateway usando PowerShell.

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection

   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"

   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Creare il gruppo di failover

Creare il gruppo di failover per le istanze gestite usando il portale di Azure o PowerShell.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Creare il gruppo di failover per le istanze gestite di SQL usando il portale di Azure.

1. Selezionare **Azure SQL** nel menu a sinistra nel [portale di Azure](https://portal.azure.com). Se **Azure SQL** non è presente nell'elenco, selezionare **Tutti i servizi** e quindi digitare Azure SQL nella casella di ricerca. (Facoltativo) Selezionare la stella accanto ad **Azure SQL** per aggiungerlo ai Preferiti e come elemento del riquadro di spostamento sinistro.
1. Selezionare l'istanza gestita primaria da aggiungere al gruppo di failover.  
1. In **Impostazioni** passare a Gruppi **di failover dell'istanza** e quindi scegliere **Aggiungi gruppo** per aprire la pagina Gruppo di **failover dell'istanza.**

   ![Aggiungere un gruppo di failover](./media/auto-failover-group-configure/add-failover-group.png)

1. Nella pagina **Gruppo di failover dell'istanza** digitare il nome del gruppo di failover e quindi scegliere l'istanza gestita secondaria dall'elenco a discesa. Fare clic su **Crea** per creare il gruppo di failover.

   ![Create failover group](./media/auto-failover-group-configure/create-failover-group.png)

1. Al termine della distribuzione del gruppo di failover, si tornerà alla pagina **Gruppo di failover**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Creare il gruppo di failover per le istanze gestite usando PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

---

### <a name="test-failover"></a>Failover di test

Testare il failover del gruppo di failover usando portale di Azure o PowerShell.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Testare il failover del gruppo di failover con il portale di Azure.

1. Passare all'istanza gestita _secondaria_ con il [portale di Azure](https://portal.azure.com) e selezionare **Gruppi di failover dell'istanza** nelle impostazioni.
1. Verificare quale istanza gestita è quella primaria e quale la secondaria.
1. Selezionare **Failover** e quindi selezionare **Sì** nell'avviso relativo alla disconnessione delle sessioni TDS.

   ![Eseguire il failover del gruppo di failover](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. Verificare quale istanza manged è l'istanza primaria e quale istanza è la secondaria. Se il failover è riuscito, le due istanze dovrebbero essersi scambiate i ruoli.

   ![Le istanze gestite si sono scambiate i ruoli dopo il failover](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. Passare alla nuova istanza gestita _secondaria_ e selezionare **Failover** ancora una volta per eseguire il failback dell'istanza primaria al ruolo primario.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testare il failover del gruppo di failover con PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="use-private-link"></a>Usare Collegamento privato

L'uso di un collegamento privato consente di associare un server logico a un indirizzo IP privato specifico all'interno della rete virtuale e della subnet. 

Per usare un collegamento privato con il gruppo di failover, eseguire le operazioni seguenti:

1. Verificare che i server primario e secondario siano in [un'area abbinata.](../../best-practices-availability-paired-regions.md) 
1. Creare la rete virtuale e la subnet in ogni area per ospitare gli endpoint privati per i server primari e secondari in modo che siano presenti spazi di indirizzi IP non sovrapposti. Ad esempio, l'intervallo di indirizzi della rete virtuale primaria 10.0.0.0/16 e l'intervallo di indirizzi della rete virtuale secondaria di 10.0.0.1/16 si sovrappone. Per altre informazioni sugli intervalli di indirizzi della rete virtuale, vedere il blog sulla [progettazione di reti virtuali di Azure.](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/)
1. Creare un [endpoint privato e una zona DNS privato azure per il server primario.](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint) 
1. Creare anche un endpoint privato per il server secondario, ma questa volta scegliere di riutilizzare la stessa zona DNS privato creata per il server primario. 
1. Dopo aver stabilito il collegamento privato, è possibile creare il gruppo di failover seguendo i passaggi descritti in precedenza in questo articolo. 


## <a name="locate-listener-endpoint"></a>Individuare l'endpoint del listener

Dopo aver configurato il gruppo di failover, aggiornare la stringa di connessione per l'applicazione all'endpoint del listener. In questo modo l'applicazione verrà connessa al listener del gruppo di failover, anziché al database primario, al pool elastico o al database dell'istanza. In questo modo, non è necessario aggiornare manualmente la stringa di connessione ogni volta che viene eseguito il fall over dell'entità di database e il traffico viene instradato a qualsiasi entità attualmente primaria.

L'endpoint del listener è nel formato ed è visibile nel `fog-name.database.windows.net` portale di Azure, quando si visualizza il gruppo di failover:

![Stringa di connessione del gruppo di failover](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>Commenti

- La rimozione di un gruppo di failover per un database singolo o in pool non arresta la replica e non elimina il database replicato. È necessario arrestare manualmente la replica geografica ed eliminare il database dal server secondario se si vuole aggiungere un database singolo o in pool a un gruppo di failover dopo che è stato rimosso. Se non si esegue questa operazione, potrebbe verificarsi un errore simile a quando si tenta di `The operation cannot be performed due to multiple errors` aggiungere il database al gruppo di failover.

## <a name="next-steps"></a>Passaggi successivi

Per la procedura dettagliata di configurazione di un gruppo di failover, vedere le esercitazioni seguenti:

- [Aggiungere un database singolo a un gruppo di failover](failover-group-add-single-database-tutorial.md)
- [Aggiungere un pool elastico a un gruppo di failover](failover-group-add-elastic-pool-tutorial.md)
- [Aggiungere un'istanza gestita a un gruppo di failover](../managed-instance/failover-group-add-instance-tutorial.md)

Per una panoramica delle opzioni database SQL di Azure disponibilità elevata, vedere [Replica geografica](active-geo-replication-overview.md) e gruppi di [failover automatico.](auto-failover-group-overview.md)
