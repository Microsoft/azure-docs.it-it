---
title: Advanced Threat Protection per Azure Cosmos DB
description: Informazioni su come Azure Cosmos DB fornisce la crittografia dei dati inattivi e la relativa implementazione.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77614844"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Advanced Threat Protection per Azure Cosmos DB (anteprima)

Advanced Threat Protection per Azure Cosmos DB fornisce un ulteriore livello di intelligence per la sicurezza che rileva tentativi insoliti e potenzialmente dannosi di accesso o exploit Azure Cosmos DB account. Questo livello di protezione consente di risolvere le minacce, anche senza essere un esperto di sicurezza e di integrarle con i sistemi di monitoraggio della sicurezza centralizzati.

Gli avvisi di sicurezza vengono attivati quando si verificano anomalie nelle attività. Questi avvisi di sicurezza sono integrati con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/)e vengono inviati anche tramite posta elettronica agli amministratori della sottoscrizione, con informazioni dettagliate sull'attività sospetta e indicazioni su come analizzare e correggere le minacce.

> [!NOTE]
>
> * Advanced Threat Protection per Azure Cosmos DB è attualmente disponibile solo per l'API SQL.
> * Advanced Threat Protection per Azure Cosmos DB non è attualmente disponibile in Azure per enti pubblici e aree cloud sovrane.

Per un'analisi completa degli avvisi di sicurezza, è consigliabile abilitare la [registrazione diagnostica in Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), che registra le operazioni nel database stesso, incluse le operazioni CRUD su tutti i documenti, i contenitori e i database.

## <a name="threat-types"></a>Tipi di minacce

Advanced Threat Protection per Azure Cosmos DB rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accesso o exploit dei database. Attualmente può attivare gli avvisi seguenti:

- **Accesso da posizioni insolite**: questo avviso viene generato quando viene apportata una modifica nel modello di accesso a un account Azure Cosmos, in cui un utente si è connesso all'endpoint Azure Cosmos DB da una posizione geografica insolita. In alcuni casi, l'avviso rileva un'azione legittima, ovvero una nuova applicazione o un'operazione di manutenzione dello sviluppatore. In altri casi, l'avviso rileva un'azione dannosa da un dipendente precedente, un utente malintenzionato esterno e così via.

- **Estrazione di dati insolita**: questo avviso viene generato quando un client estrae una quantità insolita di dati da un account Azure Cosmos DB. Questo può essere il sintomo di alcuni exfiltration di dati eseguiti per trasferire tutti i dati archiviati nell'account in un archivio dati esterno.

## <a name="set-up-advanced-threat-protection"></a>Configurare Advanced Threat Protection

### <a name="set-up-atp-using-the-portal"></a>Configurare ATP usando il portale

1. Avviare il portale di Azure all' [https://portal.azure.com](https://portal.azure.com/)indirizzo.

2. Dall'account Azure Cosmos DB scegliere **sicurezza avanzata**dal menu **Impostazioni** .

    ![Configurare ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. Nel pannello **Advanced Security** Configuration:

    * Fare clic sull'opzione **Advanced Threat Protection** per impostarla **su on**.
    * Fare clic su **Salva** per salvare i criteri di Advanced Threat Protection nuovi o aggiornati.   

### <a name="set-up-atp-using-rest-api"></a>Configurare ATP con l'API REST

Usare i comandi dell'API REST per creare, aggiornare o ottenere l'impostazione di Advanced Threat Protection per un account Azure Cosmos DB specifico.

* [Advanced Threat Protection-crea](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Advanced Threat Protection-Ottieni](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Configurare ATP usando Azure PowerShell

Usare i cmdlet di PowerShell seguenti:

* [Abilitare Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Ottenere Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Disabilitare Advanced Threat Protection](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Uso dei modelli di Gestione risorse di Azure

Usare un modello di Azure Resource Manager per configurare Cosmos DB con Advanced Threat Protection abilitata.
Per altre informazioni, vedere [creare un account CosmosDB con Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="using-azure-policy"></a>Uso di criteri di Azure

Usare un criterio di Azure per abilitare la protezione avanzata dalle minacce per Cosmos DB.

1. Avviare la pagina **criteri di Azure-definizioni** e cercare il criterio **Distribuisci Advanced Threat Protection per Cosmos DB** .

    ![Criteri di ricerca](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Fare clic sul criterio **Distribuisci Advanced Threat Protection per CosmosDB** , quindi fare clic su **assegna**.

    ![Selezionare una sottoscrizione o un gruppo](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. Dal campo **ambito** fare clic sui tre puntini, selezionare una sottoscrizione o un gruppo di risorse di Azure, quindi fare clic su **Seleziona**.

    ![Pagina definizioni criteri](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Immettere gli altri parametri e fare clic su **assegna**.

## <a name="manage-atp-security-alerts"></a>Gestione degli avvisi di sicurezza ATP

Quando si verificano Azure Cosmos DB anomalie dell'attività, viene generato un avviso di sicurezza con informazioni sull'evento di sicurezza sospetto. 

 Dal centro sicurezza di Azure è possibile esaminare e gestire gli [avvisi di sicurezza](../security-center/security-center-alerts-overview.md)correnti.  Fare clic su un avviso specifico nel [Centro sicurezza](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) per visualizzare le possibili cause e le azioni consigliate per analizzare e ridurre la potenziale minaccia. Nell'immagine seguente viene illustrato un esempio di dettagli dell'avviso disponibili nel centro sicurezza.

 ![Dettagli minaccia](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Viene inoltre inviata una notifica di posta elettronica con i dettagli dell'avviso e le azioni consigliate. Nell'immagine seguente viene illustrato un esempio di messaggio di posta elettronica di avviso.

 ![Dettagli dell'avviso](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Avvisi di Cosmos DB ATP

 Per visualizzare un elenco degli avvisi generati durante il monitoraggio di account Azure Cosmos DB, vedere la sezione [avvisi Cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) nella documentazione del Centro sicurezza di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [registrazione diagnostica in Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Scopri di più sul [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
