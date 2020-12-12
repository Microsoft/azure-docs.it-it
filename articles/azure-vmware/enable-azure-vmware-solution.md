---
title: Come abilitare la risorsa della soluzione VMware di Azure
description: Informazioni su come inviare una richiesta di supporto per abilitare la risorsa della soluzione VMware di Azure. È anche possibile richiedere più host nel cloud privato della soluzione VMware di Azure esistente.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 8bcb61c07a3f0310754cd50931f4576e836deffb
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346538"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Come abilitare la risorsa della soluzione VMware di Azure
Informazioni su come inviare una richiesta di supporto per abilitare la risorsa della [soluzione VMware di Azure](introduction.md) . È anche possibile richiedere più host nel cloud privato della soluzione VMware di Azure esistente.

## <a name="eligibility-criteria"></a>Criteri di idoneità

Sarà necessario un account Azure in una sottoscrizione di Azure. La sottoscrizione di Azure deve essere conforme a uno dei criteri seguenti:

* Una sottoscrizione in un [Enterprise Agreement di Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) con Microsoft.
* Una sottoscrizione gestita da Cloud Solution Provider (CSP) in un provider CSP esistente di Azure offre un contratto o un piano di Azure.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Abilitare la soluzione VMware di Azure per i clienti con contratto Enterprise
Prima di creare la risorsa della soluzione VMware di Azure, è necessario inviare un ticket di supporto per allocare gli host. Dopo la ricezione della richiesta da parte del team di supporto, sono necessari fino a cinque giorni per completare la richiesta e allocare gli host. Se è disponibile un cloud privato della soluzione Azure VMware ed è necessaria l'allocazione di altri host, sarà necessario seguire lo stesso processo.


1. Nella portale di Azure in Guida e **supporto tecnico** creare una **[nuova richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support)** e fornire le informazioni seguenti per il ticket:
   - **Tipo di problema:** Tecnici
   - **Sottoscrizione:** Selezionare la sottoscrizione
   - **Servizio:** Tutti i servizi > soluzione VMware di Azure
   - **Risorsa:** Domanda generale 
   - **Riepilogo:** Capacità necessaria
   - **Tipo di problema:** Capacity Management Issues (Problemi di gestione della capacità)
   - **Sottotipo del problema:** Customer Request for Additional Host Quota/Capacity (Richiesta del cliente di capacità/quota host aggiuntiva)

1. Nella **Descrizione** del ticket di supporto, nella scheda **Dettagli** , fornire:

   - POC o produzione 
   - Nome area
   - Numero di host
   - Altri dettagli

   >[!NOTE]
   >La soluzione VMware di Azure consiglia un minimo di tre host per avviare il cloud privato e per gli host con ridondanza N + 1. 

1. Selezionare **Verifica + crea** per inviare la richiesta.

   Per confermare la richiesta, l'addetto al supporto richiederà fino a cinque giorni lavorativi.

   >[!IMPORTANT] 
   >Se si dispone già di una soluzione VMware di Azure esistente e si richiede un host aggiuntivo, si noti che per l'assegnazione degli host sono necessari cinque giorni lavorativi. 

1. Prima di poter effettuare il provisioning degli host, assicurarsi di registrare il provider di risorse **Microsoft. AVS** nella portale di Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Per informazioni su come registrare il provider di risorse in altri modi, vedere [Provider e tipi di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Abilitare la soluzione VMware di Azure per i clienti CSP 

I CSP devono usare il centro per i [partner Microsoft](https://partner.microsoft.com) per abilitare la soluzione VMware di Azure per i clienti. Questo articolo usa il [piano CSP di Azure](/partner-center/azure-plan-lp.md) come esempio per illustrare la procedura di acquisto per i partner.

   >[!IMPORTANT] 
   >Il servizio della soluzione VMware di Azure non fornisce un multi-tenant richiesto. I partner di hosting che lo richiedono non sono supportati. 

1. In **centro** per i partner selezionare **CSP** per accedere all'area **clienti** .

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Area clienti del centro per i partner Microsoft" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Selezionare il cliente, quindi selezionare **Aggiungi prodotti**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft Partner Center" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Selezionare **piano di Azure** e quindi selezionare **Aggiungi al carrello**. 

1. Esaminare e completare la configurazione generale della sottoscrizione del piano Azure per il cliente. Per ulteriori informazioni, vedere la documentazione del centro per i [partner Microsoft](/partner-center/azure-plan-manage.md).

Dopo aver configurato il piano di Azure e le autorizzazioni di controllo degli accessi in base al ruolo di [Azure](/partner-center/azure-plan-manage.md) necessarie per la sottoscrizione, è necessario coinvolgere Microsoft per abilitare la quota per una sottoscrizione di piano di Azure. Accedere portale di Azure dal [centro per i partner Microsoft](https://partner.microsoft.com) usando **la procedura di amministrazione per conto di** (Aobo).

1. Accedere al [Centro per i partner](https://partner.microsoft.com).

1. Selezionare **CSP** per accedere all'area **Customers** .

1. Espandere Dettagli cliente e selezionare **portale di gestione di Microsoft Azure**.

1. In portale di Azure in **Guida e supporto tecnico** creare una **[nuova richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support)** e fornire le informazioni seguenti per il ticket:
   - **Tipo di problema:** Tecnici
   - **Sottoscrizione:** Selezionare la sottoscrizione
   - **Servizio:** Tutti i servizi > soluzione VMware di Azure
   - **Risorsa:** Domanda generale 
   - **Riepilogo:** Capacità necessaria
   - **Tipo di problema:** Capacity Management Issues (Problemi di gestione della capacità)
   - **Sottotipo del problema:** Customer Request for Additional Host Quota/Capacity (Richiesta del cliente di capacità/quota host aggiuntiva)

1. Nella **Descrizione** del ticket di supporto, nella scheda **Dettagli** , fornire:

   - POC o produzione 
   - Nome area
   - Numero di host
   - Altri dettagli
   - È destinato a ospitare più clienti?

   >[!NOTE]
   >La soluzione VMware di Azure consiglia un minimo di tre host per avviare il cloud privato e per gli host con ridondanza N + 1. 

1. Selezionare **Verifica + crea** per inviare la richiesta.

   Per confermare la richiesta, l'addetto al supporto richiederà fino a cinque giorni lavorativi.

   >[!IMPORTANT] 
   >Se si dispone già di una soluzione VMware di Azure esistente e si richiede un host aggiuntivo, si noti che per l'assegnazione degli host sono necessari cinque giorni lavorativi. 

1. Se la sottoscrizione viene gestita dal provider di servizi, il team di amministrazione deve accedere portale di Azure usando di nuovo la procedura **di amministratore per conto di** (Aobo) dal centro per i partner. Una portale di Azure avviare un'istanza di [cloud Shell](../cloud-shell/overview.md) e registrare il provider di risorse **Microsoft. AVS** e procedere con la distribuzione del cloud privato della soluzione VMware di Azure.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Per informazioni su come registrare il provider di risorse in altri modi, vedere [Provider e tipi di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md).

1. Se la sottoscrizione viene gestita direttamente dal cliente, la registrazione del provider di risorse **Microsoft. AVS** deve essere eseguita da un utente che disponga di autorizzazioni sufficienti nella sottoscrizione. per informazioni dettagliate e per la registrazione del provider di risorse, vedere i [tipi e](../azure-resource-manager/management/resource-providers-and-types.md) i provider di risorse di Azure. 


## <a name="next-steps"></a>Passaggi successivi

Dopo aver abilitato la risorsa della soluzione VMware di Azure e aver installato la rete corretta, è possibile [creare un cloud privato](tutorial-create-private-cloud.md).
