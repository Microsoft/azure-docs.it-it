---
title: Usare Istanza gestita SQL di Azure con Azure-SQL Server Integration Services (SSIS) in Azure Data Factory
description: Informazioni su come usare Istanza gestita SQL di Azure con SQL Server Integration Services (SSIS) in Azure Data Factory.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 190def0e6e2f77d330d2307753dc9e9d53c55dd7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564144"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Usare Istanza gestita SQL di Azure con SQL Server Integration Services (SSIS) in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

È ora possibile spostare progetti, pacchetti e carichi di lavoro di SQL Server Integration Services (SSIS) nel cloud di Azure. Distribuire, eseguire e gestire progetti e pacchetti SSIS nel database SQL di Azure o in SQL Istanza gestita con strumenti noti come SQL Server Management Studio (SSMS). Questo articolo evidenzia le aree specifiche seguenti quando si usa Istanza gestita SQL di Azure con Azure-SSIS Integration Runtime (IR):

- [Effettuare il provisioning di un Azure-SSIS IR con il catalogo SSIS (SSISDB) ospitato da Azure SQL Istanza gestita](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [Eseguire pacchetti SSIS con un processo dell'agente di Istanza gestita di SQL di Azure](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Eseguire la pulizia dei log di SSISDB con un processo dell'agente di Istanza gestita di SQL di Azure](#clean-up-ssisdb-logs)
- [Failover di Azure-SSIS IR con Istanza gestita SQL di Azure](configure-bcdr-azure-ssis-integration-runtime.md)
- [Eseguire la migrazione di carichi di lavoro SSIS locali a SSIS in ADF con SQL di Azure Istanza gestita come destinazione del carico di lavoro del database](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Effettuare il provisioning di Azure-SSIS IR con SSISDB ospitato da Azure SQL Istanza gestita

### <a name="prerequisites"></a>Prerequisiti

1. [Abilitare Azure Active Directory (Azure ad) in istanza gestita SQL di Azure](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance), quando si sceglie l'autenticazione Azure Active Directory.

1. Scegliere la modalità di connessione di SQL Istanza gestita, su endpoint privato o su endpoint pubblico:

    - Tramite endpoint privato (scelta consigliata)

        1. Scegliere la rete virtuale a cui aggiungere Azure-SSIS IR:
            - All'interno della stessa rete virtuale dell'istanza gestita, con una **subnet diversa**.
            - All'interno di una rete virtuale diversa da quella dell'istanza gestita, tramite il peering di rete virtuale (che è limitato alla stessa area a causa dei vincoli di peering VNet globali) o una connessione dalla rete virtuale alla rete virtuale.

            Per altre informazioni sulla connettività di SQL Istanza gestita, vedere [connettere l'applicazione al istanza gestita SQL di Azure](../azure-sql/managed-instance/connect-application-instance.md).

        1. [Configurare la rete virtuale](#configure-virtual-network).

    - Tramite endpoint pubblico

        Le istanze gestite di SQL di Azure possono fornire connettività sugli [endpoint pubblici](../azure-sql/managed-instance/public-endpoint-configure.md). I requisiti in ingresso e in uscita devono essere conformi per consentire il traffico tra SQL Istanza gestita e Azure-SSIS IR:

        - Azure-SSIS IR non all'interno di una rete virtuale (scelta consigliata)

            **Requisito in ingresso di SQL istanza gestita** per consentire il traffico in ingresso da Azure-SSIS IR.

            | Protocollo di trasporto | Source (Sorgente) | Intervallo di porte di origine | Destination | Intervallo di porte di destinazione |
            |---|---|---|---|---|
            |TCP|Tag del servizio cloud di Azure|*|VirtualNetwork|3342|

            Per altre informazioni, vedere [Consentire il traffico dell'endpoint pubblico nel gruppo di sicurezza di rete](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).

        - Azure-SSIS IR all'interno di una rete virtuale

            Esiste uno scenario speciale quando SQL Istanza gestita si trova in un'area non supportata da Azure-SSIS IR, Azure-SSIS IR si trova all'interno di una rete virtuale senza peering VNet a causa della limitazione del peering VNet globale. In questo scenario, **Azure-SSIS IR all'interno di una rete virtuale** connette SQL istanza gestita **su un endpoint pubblico**. Usare le regole del gruppo di sicurezza di rete (NSG) per consentire il traffico tra SQL Istanza gestita e Azure-SSIS IR:

            1. **Requisito in ingresso di SQL istanza gestita** per consentire il traffico in ingresso da Azure-SSIS IR.

                | Protocollo di trasporto | Source (Sorgente) | Intervallo di porte di origine | Destination |Intervallo di porte di destinazione |
                |---|---|---|---|---|
                |TCP|Indirizzo IP statico di Azure-SSIS IR <br> Per informazioni dettagliate, vedere [Usare un indirizzo IP pubblico personalizzato per Azure-SSIS IR](join-azure-ssis-integration-runtime-virtual-network.md#publicIP).|*|VirtualNetwork|3342|

             1. **Requisito in uscita del Azure-SSIS IR** per consentire il traffico in uscita a SQL istanza gestita.

                | Protocollo di trasporto | Source (Sorgente) | Intervallo di porte di origine | Destination |Intervallo di porte di destinazione |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[Indirizzo IP dell'endpoint pubblico di SQL Istanza gestita](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>Configurare la rete virtuale

1. **Autorizzazione utente**. Per l'utente che crea l'istanza di Azure-SSIS IR è necessaria un'[assegnazione di ruolo](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) almeno nella risorsa Azure Data Factory, con una delle opzioni descritte di seguito.

    - Usare il ruolo predefinito Collaboratore Rete. Questo ruolo richiede l'autorizzazione _Microsoft.Network/\*_ , la quale ha un ambito molto maggiore del necessario.
    - Creare un ruolo personalizzato che include solo l'autorizzazione _Microsoft.Network/virtualNetworks/\*/join/action_ necessaria. Se si vogliono usare indirizzi IP pubblici personalizzati per Azure-SSIS IR, quando viene aggiunto a una rete virtuale di Azure Resource Manager, includere nel ruolo anche l'autorizzazione _Microsoft.Network/publicIPAddresses/*/join/action_.

1. **Rete virtuale**.

    1. Verificare che il gruppo di risorse della rete virtuale possa creare ed eliminare determinate risorse di rete di Azure.

        Il runtime di integrazione Azure-SSIS deve creare alcune risorse di rete nello stesso gruppo di risorse della rete virtuale. Tali risorse includono:
        - Un servizio di bilanciamento del carico di Azure, con nome *\<Guid> -azurebatch-cloudserviceloadbalancer*
        - Un gruppo di sicurezza di rete, denominato * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Un indirizzo IP pubblico di Azure denominato -azurebatch-cloudservicepublicip

        Queste risorse verranno create all'avvio di Azure-SSIS IR e verranno eliminate all'arresto di Azure-SSIS IR. Per evitare di impedire l'arresto di Azure-SSIS IR, non riusare queste risorse di rete in altre risorse.

    1. Verificare che non sia presente alcun [blocco delle risorse](../azure-resource-manager/management/lock-resources.md) per il gruppo di risorse o la sottoscrizione a cui appartiene la rete virtuale. Se si configura un blocco di sola lettura/eliminazione, l'avvio e l'arresto di Azure-SSIS IR avranno esito negativo o si bloccheranno.

    1. Verificare che non siano presenti criteri di Azure che impediscono la creazione delle risorse seguenti nel gruppo di risorse o nella sottoscrizione a cui appartiene la rete virtuale:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

    1. Consentire il traffico sulla regola del gruppo di sicurezza di rete (NSG), per consentire il traffico tra Istanza gestita SQL e Azure-SSIS IR e il traffico necessario per Azure-SSIS IR.
        1. **Requisito in ingresso di SQL istanza gestita** per consentire il traffico in ingresso da Azure-SSIS IR.

            | Protocollo di trasporto | Source (Sorgente) | Intervallo di porte di origine | Destination | Intervallo di porte di destinazione | Commenti |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|Se il criterio di connessione del server di database SQL è impostato su **Proxy** anziché su **Reindirizzamento**, è necessaria solo la porta 1433.|

        1. **Requisito in uscita di Azure-SSIS IR**, per consentire il traffico in uscita a SQL istanza gestita e altro traffico necessario per Azure-SSIS IR.

        | Protocollo di trasporto | Source (Sorgente) | Intervallo di porte di origine | Destination | Intervallo di porte di destinazione | Commenti |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |Consentire il traffico in uscita a SQL Istanza gestita. Se il criterio di connessione è impostato su **Proxy** anziché su **Reindirizzamento**, è necessaria solo la porta 1433. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | I nodi di Azure-SSIS IR nella rete virtuale usano questa porta per accedere ai servizi di Azure, ad esempio Archiviazione di Azure e Hub eventi di Azure. |
        | TCP | VirtualNetwork | * | Internet | 80 | (Facoltativo) I nodi di Azure-SSIS IR nella rete virtuale usano questa porta per scaricare un elenco di revoche di certificati da Internet. Se si blocca questo traffico, si potrebbe verificare un downgrade delle prestazioni all'avvio del runtime di integrazione e si potrebbe perdere la possibilità di controllare l'elenco di revoche per l'utilizzo dei certificati. Se si vuole limitare ulteriormente la destinazione a determinati nomi di dominio completi (FQDN), vedere [Usare Azure ExpressRoute o una route definita dall'utente](./join-azure-ssis-integration-runtime-virtual-network.md#route).|
        | TCP | VirtualNetwork | * | Archiviazione | 445 | (Facoltativo) Questa regola è necessaria solo quando si vuole eseguire un pacchetto SSIS archiviato in File di Azure. |
        |||||||

        1. **Requisito in ingresso di Azure-SSIS IR**, per consentire il traffico necessario per Azure-SSIS IR:

        | Protocollo di trasporto | Source (Sorgente) | Intervallo di porte di origine | Destination | Intervallo di porte di destinazione | Commenti |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (se si aggiunge il runtime di integrazione a una rete virtuale di Resource Manager) <br/><br/>10100, 20100, 30100 (se si aggiunge il runtime di integrazione a una rete virtuale classica)| Il servizio Data Factory usa queste porte per comunicare con i nodi di Azure-SSIS IR nella rete virtuale. <br/><br/> Sia che si crei o meno un gruppo di sicurezza di rete a livello di subnet, Data Factory configura sempre un gruppo di sicurezza di rete al livello delle schede di interfaccia di rete collegate alle macchine virtuali che ospitano Azure-SSIS IR. Il gruppo di sicurezza di rete a livello di scheda di interfaccia di rete consente solo il traffico in entrata dagli indirizzi IP di Data Factory nelle porte specificate. Anche se si aprono queste porte al traffico Internet a livello di subnet, il traffico proveniente da indirizzi IP diversi dagli indirizzi IP di Data Factory viene bloccato a livello di scheda di interfaccia di rete. |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (Facoltativo) Questa regola è necessaria solo quando il supporto tecnico Microsoft richiede al cliente l'apertura per la risoluzione dei problemi avanzata. È possibile chiudere subito dopo la risoluzione dei problemi. Il tag di servizio **CorpNetSaw** consente l'uso del desktop remoto solo alle workstation con accesso sicuro nella rete aziendale Microsoft. Questo tag di servizio non può essere selezionato dal portale ed è disponibile solo tramite Azure PowerShell o l'interfaccia della riga di comando di Azure. <br/><br/> La porta 3389 è aperta per impostazione predefinita nel gruppo di sicurezza di rete a livello di scheda di interfaccia di rete e Microsoft consente di controllare tale porta nel gruppo di sicurezza di rete a livello di subnet. Nel frattempo, per impostazione predefinita, la porta 3389 in uscita è stata disabilitata a scopo di protezione da Azure-SSIS IR nella regola di Windows Firewall per ogni nodo del runtime di integrazione. |
        |||||||

    1. Per altre informazioni, vedere [Configurazione della rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration):
        - Se si usano indirizzi IP pubblici personalizzati per Azure-SSIS IR
        - Se si usa un server DNS (Domain Name System) personalizzato
        - Se si usa Azure ExpressRoute o una route definita dall'utente
        - Se si usa un'istanza personalizzata di Azure-SSIS IR

### <a name="provision-azure-ssis-integration-runtime"></a>Effettuare il provisioning di Azure-SSIS Integration Runtime

1. Selezionare SQL Istanza gestita endpoint privato o pubblico.

    Quando si esegue il [provisioning di Azure-SSIS IR](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) nell'app portale di Azure/ADF, nella pagina Impostazioni SQL usare l' **endpoint privato** di SQL istanza gestita o l' **endpoint pubblico** durante la creazione del catalogo SSIS (SSISDB).

    Il nome host dell'endpoint pubblico presenta il formato <nome_ig>.public.<zona_dns>.database.windows.net e per la connessione viene usata la porta 3342.  

    ![Screenshot mostra il programma di installazione di Integration Runtime con il catalogo di create s I S selezionato e l'endpoint del server di database del catalogo immesso.](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. Selezionare l'autenticazione di Azure AD, quando applicabile.

    ![catalog-public-endpoint](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Per altre informazioni su come abilitare l'autenticazione Azure AD, vedere [abilitare Azure ad in istanza gestita SQL di Azure](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Aggiungere Azure-SSIS IR alla rete virtuale, quando applicabile.

    Nella pagina Impostazioni avanzate selezionare la rete virtuale e la subnet a cui eseguire l'aggiunta.
    
    Quando si trova all'interno della stessa rete virtuale di SQL Istanza gestita, scegliere una **subnet diversa** da quella di SQL istanza gestita. 

    Per altre informazioni su come aggiungere Azure-SSIS IR a una rete virtuale, vedere [Aggiungere Azure-SSIS Integration Runtime a una rete virtuale](join-azure-ssis-integration-runtime-virtual-network.md).

    ![Screenshot mostra le impostazioni avanzate di installazione di Integration Runtime, in cui è possibile selezionare una rete virtuale per l'aggiunta del runtime.](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Per altre informazioni su come creare Azure-SSIS IR, vedere [Creare un runtime di integrazione SSIS di Azure in Azure Data Factory](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime).

## <a name="clean-up-ssisdb-logs"></a>Eseguire la pulizia dei log di SSISDB

I criteri di conservazione dei log di SSISDB sono definiti dalle proprietà seguenti in [catalog.catalog_properties](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database):

- OPERATION_CLEANUP_ENABLED

    Se il valore è TRUE, i dettagli e i messaggi delle operazioni anteriori a RETENTION_WINDOW (giorni) vengono eliminati dal catalogo. Se il valore è FALSE, tutti i dettagli e i messaggi delle operazioni vengono archiviati nel catalogo. Nota: la pulizia delle operazioni viene eseguita da un processo di SQL Server.

- RETENTION_WINDOW

    Numero di giorni di archiviazione nel catalogo dei dettagli e dei messaggi dell'operazione. Quando il valore è -1, il periodo di conservazione è infinito. Nota: se non si vuole eseguire alcuna pulizia, impostare OPERATION_CLEANUP_ENABLED su FALSE.

Per rimuovere i log di SSISDB non inclusi nel periodo di conservazione impostato dall'amministratore, è possibile attivare la stored procedure `[internal].[cleanup_server_retention_window_exclusive]`. Facoltativamente, è possibile pianificare l'esecuzione del processo di SQL Istanza gestita Agent per attivare il stored procedure.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire pacchetti SSIS con un processo dell'agente di Istanza gestita di SQL di Azure](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Configurare continuità aziendale e ripristino di emergenza (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [Eseguire la migrazione di carichi di lavoro SSIS locali a SSIS in ADF](scenario-ssis-migration-overview.md)