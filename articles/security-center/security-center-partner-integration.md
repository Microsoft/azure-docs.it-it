---
title: Integrare soluzioni di sicurezza nel Centro sicurezza di Azure | Microsoft Docs
description: Informazioni sull'integrazione del Centro sicurezza di Azure con i partner per una migliore sicurezza complessiva delle risorse di Azure.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.openlocfilehash: ff23a1fa4b631fc10163f22d94ccdbd8cbe657c2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099251"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrare soluzioni di sicurezza nel Centro sicurezza di Azure
Questo documento aiuta a gestire le soluzioni di sicurezza già connesse al Centro sicurezza di Azure e ad aggiungerne di nuove.

## <a name="integrated-azure-security-solutions"></a>Soluzioni di sicurezza di Azure integrate
Il Centro sicurezza semplifica l'abilitazione di soluzioni di sicurezza integrate in Azure. Questa caratteristica offre i vantaggi seguenti:

- **Distribuzione semplificata**: il Centro sicurezza offre provisioning semplificato delle soluzioni dei partner integrate. Per soluzioni come antimalware e valutazione della vulnerabilità, il Centro sicurezza può effettuare il provisioning dell'agente nelle macchine virtuali. Per le appliance firewall, il Centro sicurezza può gestire la maggior parte delle configurazioni di rete necessarie.
- **Rilevamenti integrati**: gli eventi di sicurezza delle soluzioni dei partner vengono raccolti, aggregati e visualizzati automaticamente come parte degli avvisi e degli eventi imprevisti del Centro sicurezza. Questi eventi vengono anche combinati con i rilevamenti di altre origini per offrire funzionalità avanzate di rilevamento delle minacce.
- **Gestione e monitoraggio dell'integrità unificati**: i clienti possono usare eventi di integrità integrati per monitorare tutte le soluzioni dei partner in un attimo. Sono disponibili funzionalità di gestione di base, con un facile accesso alla configurazione avanzata con la soluzione partner.

Attualmente, le soluzioni di sicurezza integrate includono la valutazione della vulnerabilità da [Qualys](https://www.qualys.com/public-cloud/#azure) e [Rapid7](https://www.rapid7.com/products/insightvm/) e [Microsoft Azure Web Application Firewall su applicazione Azure gateway](../web-application-firewall/ag/ag-overview.md).

> [!NOTE]
> Il Centro sicurezza non installa l'agente di Log Analytics in appliance virtuali partner perché la maggior parte dei fornitori di sicurezza impedisce l'esecuzione di agenti esterni nei propri dispositivi.

Per altre informazioni sull'integrazione degli strumenti di analisi delle vulnerabilità da Qualys, incluso uno scanner integrato disponibile per i clienti di Azure Defender, vedere [valutazioni delle vulnerabilità per le macchine virtuali di Azure](deploy-vulnerability-assessment-vm.md).

Il Centro sicurezza offre anche l'analisi delle vulnerabilità per:

* Database SQL: vedere [Esplorare i report di valutazione della vulnerabilità nel dashboard di valutazione della vulnerabilità](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports)
* Immagini di Azure Container Registry. vedere [usare Azure Defender per registri contenitori per analizzare le vulnerabilità delle immagini](defender-for-container-registries-usage.md)

## <a name="how-security-solutions-are-integrated"></a>Modalità di integrazione delle soluzioni di sicurezza
Le soluzioni di sicurezza di Azure distribuite dal Centro sicurezza vengono connesse automaticamente. È anche possibile connettere altre origini dati di sicurezza, inclusi i computer in esecuzione in locale o in altri cloud.

[![Integrazione di soluzioni di partner](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gestire soluzioni di sicurezza di Azure integrate e altre origini dati

1. Nel [portale di Azure](https://azure.microsoft.com/features/azure-portal/) aprire **Centro sicurezza**.

1. Dal menu del Centro sicurezza selezionare **soluzioni di sicurezza**.

Dalla pagina **soluzioni di sicurezza** è possibile visualizzare l'integrità delle soluzioni di sicurezza di Azure integrate ed eseguire attività di gestione di base.

### <a name="connected-solutions"></a>Soluzioni connesse

La sezione **soluzioni connesse** include le soluzioni di sicurezza attualmente connesse al centro sicurezza. Mostra anche lo stato di integrità di ogni soluzione.  

![Soluzioni connesse](./media/security-center-partner-integration/connected-solutions.png)

Una soluzione partner può avere uno degli stati seguenti:

* **Integro** (verde): nessun problema di integrità.
* Non **integro** (rosso): si è verificato un problema di integrità che richiede attenzione immediata.
* **Segnalazione interrotta** (arancione): la soluzione ha interrotto la segnalazione dello stato di integrità.
* **Non segnalato** (grigio): la soluzione non ha ancora segnalato alcun dato e i dati di integrità non sono disponibili. Lo stato di una soluzione può non essere segnalato se è stato connesso di recente ed è ancora in distribuzione.

> [!NOTE]
> Se i dati relativi allo stato di integrità non sono disponibili, il Centro sicurezza mostra la data e l'ora dell'ultimo evento ricevuto, per indicare se la soluzione invia segnalazioni o meno. Se non sono disponibili dati di integrità e non è stato ricevuto alcun avviso negli ultimi 14 giorni, il Centro sicurezza indica che la soluzione non è integra o non segnala.
>
>

Selezionare **Visualizza** per ulteriori informazioni e opzioni, ad esempio:

   - **Console soluzione** : apre l'esperienza di gestione per questa soluzione.
   - **Collega macchina virtuale** : apre la pagina collega applicazioni. In questo pannello è possibile connettere risorse alla soluzione del partner.
   - **Eliminare la soluzione**
   - **Configurare**

   ![Dettagli della soluzione di un partner](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Discovered solutions (Soluzioni individuate)

Il Centro sicurezza individua automaticamente le soluzioni di sicurezza in esecuzione in Azure ma non connesse al centro sicurezza e visualizza le soluzioni nella sezione **soluzioni individuate** . Queste soluzioni includono soluzioni di Azure, come [Azure ad Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)e soluzioni partner.

> [!NOTE]
> Abilitare **Azure Defender** a livello di sottoscrizione per la funzionalità soluzioni individuate. Per altre informazioni [, vedere Guida introduttiva: abilitare Azure Defender](enable-azure-defender.md).

Selezionare **Connetti** in una soluzione per l'integrazione con il Centro sicurezza e ricevere notifiche relative agli avvisi di sicurezza.

### <a name="add-data-sources"></a>Aggiungere origini dati

La sezione **Aggiungi origini dati** include altre origini dati disponibili che è possibile connettere. Per istruzioni sull'aggiunta di dati da una qualsiasi di queste origini, fare clic su **AGGIUNGI**.

![Origini dati](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come integrare soluzioni partner nel Centro sicurezza. Per informazioni su come configurare un'integrazione con Sentinel di Azure o qualsiasi altro SIEM, vedere [esportare continuamente i dati del Centro sicurezza](continuous-export.md).