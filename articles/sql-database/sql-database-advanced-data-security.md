---
title: Sicurezza dei dati avanzata
description: Informazioni sulle funzionalità per l'individuazione e la classificazione di dati sensibili, per la gestione delle vulnerabilità dei database e per il rilevamento di attività anomale che potrebbero indicare una minaccia al database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
ms.author: memildin
author: memildin
manager: rkarlin
ms.reviewer: vanto
ms.date: 03/31/2019
ms.openlocfilehash: aed0bcb79dedf057c5943cea9f4b4399b2f630cb
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677458"
---
# <a name="advanced-data-security-for-azure-sql-database"></a>Sicurezza dei dati avanzata per il database SQL di Azure

Sicurezza dei dati avanzata è un pacchetto unificato che include le funzionalità di sicurezza avanzate SQL. Include funzionalità per l'individuazione e la classificazione di dati sensibili, il rilevamento e l'attenuazione di potenziali vulnerabilità dei database e il rilevamento di attività anomale che possono indicare una minaccia per il database. Offre una posizione unica per l'abilitazione e la gestione di queste funzionalità.

## <a name="overview"></a>Panoramica

Advanced Data Security (ADS) offre una serie di funzionalità avanzate di sicurezza SQL, tra cui l'individuazione dei dati & classificazione, la valutazione delle vulnerabilità e Advanced Threat Protection.

- [Data Discovery & Classification](sql-database-data-discovery-and-classification.md) offre funzionalità integrate nel database SQL di Azure per l'individuazione, la classificazione, l'etichettatura & la segnalazione dei dati sensibili nei database. Consente di visualizzare lo stato di classificazione del database e di tenere traccia dell'accesso ai dati sensibili all'interno del database e all'esterno di questo ambito.
- [Valutazione della vulnerabilità](sql-vulnerability-assessment.md) è un servizio semplice da configurare che consente di individuare, monitorare e risolvere potenziali vulnerabilità del database. Consente di visualizzare lo stato di sicurezza e prevede passaggi utili per risolvere i problemi di sicurezza e migliorare la protezione del database.
- [Advanced Threat Protection](sql-database-threat-detection-overview.md) rileva attività anomale che indicano tentativi insoliti e potenzialmente dannosi di accedere o sfruttare i database. Monitora in modo continuo il database in caso di attività sospette e fornisce avvisi di sicurezza immediati su potenziali vulnerabilità, attacchi SQL injection e in caso di modelli di accesso ai database anomali. Gli avvisi della funzionalità di Advanced Threat Protection includono dettagli sulle attività sospette e consigliano azioni per l'analisi e la mitigazione della minaccia.

Abilitare SQL Advanced Data Security una volta per abilitare tutte le funzionalità incluse. Con un solo clic, è possibile abilitare ADS per tutti i database nel server di database SQL o nell'istanza gestita. Per abilitare o gestire le impostazioni di Sicurezza dei dati avanzata è necessario il ruolo di [gestore della sicurezza SQL](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager), il ruolo di amministratore del database SQL o il ruolo di amministratore di SQL Server. 

I prezzi di ADS sono allineati con il livello Standard del Centro sicurezza di Azure, dove ogni server di database SQL o istanza gestita viene conteggiato come un nodo. Le nuove risorse protette danno diritto a una versione di valutazione gratuita del livello Standard del Centro sicurezza. Per altre informazioni, vedere la [pagina dei prezzi del Centro sicurezza di Azure](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="getting-started-with-ads"></a>Introduzione a ADS

I passaggi seguenti consentono di iniziare a usare Advanced Data Security.

## <a name="1-enable-ads"></a>1. Abilitare ADS

Abilitare ADS passando a **Sicurezza avanzata dei dati** sotto l'intestazione **Sicurezza** per il server di database SQL o l'istanza gestita. Per abilitare ADS per tutti i database nel server di database o istanza gestita, fare clic su **Enable Advanced Data Security on the server** (Abilitare Advanced Data Security nel server).

> [!NOTE]
> Un account di archiviazione viene creato e configurato automaticamente per archiviare i risultati dell'analisi **di valutazione delle vulnerabilità.** Se ADS è già stato abilitato per un altro server nello stesso gruppo di risorse e nello stesso paese di risorse, viene usato l'account di archiviazione esistente.

![Abilitare ADS](./media/sql-advanced-protection/enable_ads.png) 

> [!NOTE]
> Il costo di Advanced Data Security è allineato al prezzo del livello Standard del Centro sicurezza di Azure per ogni nodo, dove un nodo è l'intero server di database SQL o l'istanza gestita. Si paga solo una volta per proteggere tutti i database sul server di database o istanza gestita con ADS. È possibile provare Advanced Data Security con una versione di valutazione gratuita.

## <a name="2-start-classifying-data-tracking-vulnerabilities-and-investigating-threat-alerts"></a>2. Iniziare a classificare i dati, tenere traccia delle vulnerabilità e analizzare gli avvisi sulle minacce

Fare clic sulla scheda **Individuazione dati e classificazione** per visualizzare le colonne sensibili consigliate e per classificare i dati con etichette di riservatezza persistenti. Fare clic sulla scheda **Valutazione della vulnerabilità** per visualizzare e gestire le analisi e i report sulle vulnerabilità e tenere traccia del livello di sicurezza. Se sono stati ricevuti avvisi di sicurezza, fare clic sulla scheda **Protezione avanzata dalle** minacce per visualizzare i dettagli degli avvisi e visualizzare un report consolidato su tutti gli avvisi nella sottoscrizione di Azure tramite la pagina Avvisi di sicurezza del Centro sicurezza di Azure.If security alerts have been received, click the Advanced Threat Protection card to view details of the alerts and to view a consolidated report on all alerts in your Azure subscription subscription via the Azure Security Center security alerts page.

## <a name="3-manage-ads-settings-on-your-sql-database-server-or-managed-instance"></a>3. Gestire le impostazioni di ADS nel server di database SQL o nell'istanza gestita

Per visualizzare e gestire le impostazioni di Sicurezza dei dati avanzata, passare a **Sicurezza dei dati avanzata** sotto l'intestazione **Sicurezza** per l'istanza gestita o il server di database SQL. In questa pagina è possibile abilitare o disabilitare ADS e modificare le impostazioni di valutazione delle vulnerabilità e Advanced Threat Protection per l'intero server di database SQL o l'istanza gestita.

![Impostazioni del server](./media/sql-advanced-protection/server_settings.png) 

## <a name="4-manage-ads-settings-for-a-sql-database"></a>4. Gestire le impostazioni ADS per un database SQL

Per eseguire l'override delle impostazioni di Sicurezza dei dati avanzata per uno specifico database, selezionare la casella di controllo **Abilita Sicurezza dei dati avanzata al livello del database**. Utilizzare questa opzione solo se si dispone di un particolare requisito per ricevere avvisi di Advanced Threat Protection o risultati di valutazione delle vulnerabilità separati per il singolo database, al posto o in aggiunta agli avvisi e ai risultati ricevuti per tutti i database nel server di database o nell'istanza gestita.

Una volta selezionata la casella di controllo, è possibile configurare le impostazioni rilevanti per questo database.
 
![Impostazioni del database e di Advanced Threat Protection](./media/sql-advanced-protection/database_threat_detection_settings.png) 

Dal riquadro del database di Sicurezza dei dati avanzata è possibile raggiungere anche le impostazioni di sicurezza avanzata per il server di database o l'istanza gestita. Fare clic su **Impostazioni** nel riquadro principale di Advanced Data Security e fare clic su **View Advanced Data Security server settings** (Visualizza le impostazioni del server di Advanced Data Security). 

![Impostazioni database](./media/sql-advanced-protection/database_settings.png) 

## <a name="next-steps"></a>Passaggi successivi 

- Ulteriori informazioni su [Data Discovery & Classification](sql-database-data-discovery-and-classification.md) 
- Ulteriori informazioni sulla [valutazione della vulnerabilità](sql-vulnerability-assessment.md) 
- Ulteriori informazioni su [Advanced Threat Protection](sql-database-threat-detection.md)
- Altre informazioni sul Centro sicurezza di [AzureLearn](https://docs.microsoft.com/azure/security-center/security-center-intro) more about Azure Security Center
