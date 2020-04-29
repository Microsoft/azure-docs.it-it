---
title: Configurare le regole dell'applicazione firewall di Azure con FQDN SQL
description: Questo articolo illustra come configurare FQDN SQL nelle regole dell'applicazione firewall di Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 858cfc9a8c15f1e33e688bb5086a58f194e7173f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79501506"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Configurare le regole dell'applicazione firewall di Azure con FQDN SQL

> [!IMPORTANT]
> Le regole dell'applicazione firewall di Azure con FQDN SQL sono attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È ora possibile configurare le regole dell'applicazione firewall di Azure con FQDN SQL. In questo modo è possibile limitare l'accesso dalle reti virtuali solo alle istanze di SQL Server specificate.

Con i nomi di dominio completi SQL è possibile filtrare il traffico:

- Dalla reti virtuali a un database SQL di Azure o a un Azure SQL Data Warehouse. Ad esempio: consentire l'accesso solo a *SQL-Server1.database.Windows.NET*.
- Da locale a istanze gestite di SQL di Azure o IaaS SQL in esecuzione in reti virtuali.
- Da spoke a spoke a istanze gestite di SQL di Azure o IaaS SQL in esecuzione in reti virtuali.

Durante l'anteprima pubblica, il filtro FQDN SQL è supportato solo in [modalità proxy](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) (porta 1433). Se si usa SQL nella modalità di reindirizzamento predefinita, è possibile filtrare l'accesso usando il tag del servizio SQL come parte delle [regole di rete](overview.md#network-traffic-filtering-rules).
Se si usano porte non predefinite per il traffico IaaS SQL, è possibile configurare tali porte nelle regole dell'applicazione firewall.

Le regole dell'applicazione con FQDN SQL sono attualmente disponibili in tutte le aree tramite il portale di Azure, l'interfaccia della riga di comando di Azure, REST e i modelli.

## <a name="configure-using-azure-cli"></a>Configurare usando l'interfaccia della riga di comando

1. Distribuire un [Firewall Azure usando l'interfaccia](deploy-cli.md)della riga di comando di Azure.
2. Se si filtra il traffico verso il database SQL di Azure, SQL Data Warehouse o SQL Istanza gestita, assicurarsi che la modalità di connettività SQL sia impostata su **proxy**. Per informazioni su come cambiare la modalità di connettività SQL, vedere [impostazioni di connettività SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).

   > [!NOTE]
   > La modalità *proxy* SQL può comportare una latenza maggiore rispetto al *Reindirizzamento*. Se si vuole continuare a usare la modalità di reindirizzamento, che è l'impostazione predefinita per i client che si connettono in Azure, è possibile filtrare l'accesso usando il [tag del servizio](service-tags.md) SQL nelle [regole di rete](tutorial-firewall-deploy-portal.md#configure-a-network-rule)del firewall.

3. Configurare una regola dell'applicazione con il nome di dominio completo SQL per consentire l'accesso a un server SQL:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Configurare con il Portale di Azure
1. Distribuire un [Firewall Azure usando l'interfaccia](deploy-cli.md)della riga di comando di Azure.
2. Se si filtra il traffico verso il database SQL di Azure, SQL Data Warehouse o SQL Istanza gestita, assicurarsi che la modalità di connettività SQL sia impostata su **proxy**. Per informazioni su come cambiare la modalità di connettività SQL, vedere [impostazioni di connettività SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-settings#change-connection-policy-via-azure-cli).  

   > [!NOTE]
   > La modalità *proxy* SQL può comportare una latenza maggiore rispetto al *Reindirizzamento*. Se si vuole continuare a usare la modalità di reindirizzamento, che è l'impostazione predefinita per i client che si connettono in Azure, è possibile filtrare l'accesso usando il [tag del servizio](service-tags.md) SQL nelle [regole di rete](tutorial-firewall-deploy-portal.md#configure-a-network-rule)del firewall.
3. Aggiungere la regola dell'applicazione con il protocollo, la porta e il nome di dominio completo SQL appropriati, quindi selezionare **Salva**.
   ![regola applicazione con FQDN SQL](media/sql-fqdn-filtering/application-rule-sql.png)
4. Accedere a SQL da una macchina virtuale in una VNet che filtra il traffico attraverso il firewall. 
5. Verificare che i [log del firewall di Azure](log-analytics-samples.md) mostrino che il traffico è consentito.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulle modalità di reindirizzamento e proxy SQL, vedere [architettura della connettività del database SQL di Azure](../sql-database/sql-database-connectivity-architecture.md).