---
title: Panoramica dell'esempio di progetto di definizione della base di Cloud Adoption Framework
description: Panoramica e architettura del progetto di esempio di definizione della base di Cloud Adoption Framework (CAF) per Azure.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: 43c962ca472d2933e2f1add01b22b5fc969163fe
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471723"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Panoramica del progetto di esempio di definizione della base di Microsoft Cloud Adoption Framework per Azure

Il progetto di definizione della base di Microsoft Cloud Adoption Framework (CAF) per Azure consente di distribuire un set di infrastrutture di base e controlli di criteri richiesti per la prima applicazione Azure destinata ad ambienti di produzione. Questo progetto di base è basato sul modello consigliato disponibile in Cloud Adoption Framework.

## <a name="architecture"></a>Architecture

Il progetto di esempio di definizione della base di Cloud Adoption Framework distribuisce le risorse dell'infrastruttura consigliate in Azure che possono essere usate dalle organizzazioni per predisporre i controlli di base necessari per gestire l'ambiente cloud. Con questo esempio verranno distribuite e imposte risorse, criteri e modelli che permetteranno a un'organizzazione di iniziare a usare Azure in modo sicuro.

:::image type="complex" source="../../media/caf-blueprints/caf-foundation-architecture.png" alt-text="Base di Cloud Adoption Framework: l'immagine illustra gli elementi installati previsti dalle linee guida di Cloud Adoption Framework per la creazione di una base iniziale da usare con Azure." border="false":::
   Illustra un'architettura di Azure ottenuta dalla distribuzione del progetto di base di Cloud Adoption Framework.  È applicabile a una sottoscrizione con gruppi di risorse costituita da un account di archiviazione per l'archiviazione dei log e Log Analytics configurato per l'archiviazione nell'account di archiviazione. Illustra anche Azure Key Vault impostato con la configurazione standard di Centro sicurezza di Azure. È possibile accedere a tutte queste infrastrutture di base usando Azure Active Directory e applicarle usando Criteri di Azure.     
:::image-end:::

Questa implementazione include diversi servizi di Azure usati per offrire una base sicura, completamente monitorata e pronta per l'azienda. L'ambiente è costituito da:

- Un'istanza di [Azure Key Vault](../../../../key-vault/general/overview.md) usata per ospitare i segreti per le macchine virtuali distribuite nell'ambiente dei servizi condivisi
- [Analisi dei log](../../../../azure-monitor/overview.md), distribuito per garantire la registrazione di tutte le azioni e tutti i servizi in una posizione centrale dal momento in cui viene avviata la distribuzione sicura negli [account di archiviazione](../../../../storage/common/storage-introduction.md) per la registrazione diagnostica
- [Centro sicurezza di Azure](../../../../security-center/security-center-introduction.md) (versione standard), distribuito per fornire la protezione dalle minacce per i carichi di lavoro di cui è stata eseguita la migrazione
- Il progetto consente di impostare e distribuire definizioni di [Criteri di Azure](../../../policy/overview.md):
  - Definizioni dei criteri:
    - Applicare l'assegnazione di tag (CostCenter) a gruppi di risorse
    - Accodare risorse nel gruppo di risorse con il tag CostCenter
    - Area di Azure consentita per risorse e gruppi di risorse
    - SKU dell'account di archiviazione consentiti (da scegliere durante la distribuzione)
    - SKU di macchine virtuali di Azure consentiti (da scegliere durante la distribuzione)
    - Richiedere la distribuzione di Network Watcher 
    - Richiedere la crittografia di trasferimento sicuro dell'account di archiviazione di Azure
    - Rifiutare tipi di risorsa (da scegliere durante la distribuzione)  
  - Iniziative per i criteri:
    - Abilitare il monitoraggio nel Centro sicurezza di Azure (più di 100 definizioni di criteri)

Tutti questi elementi sono conformi a procedure comprovate pubblicate in [Centro architetture di Azure - Architetture di riferimento di Azure](/azure/architecture/reference-architectures/).

> [!NOTE]
> Il progetto di definizione della base di Cloud Adoption Framework predispone un'architettura di base per i carichi di lavoro.
> Sarà comunque necessario distribuire i carichi di lavoro su cui viene definita questa architettura di base.

Per altre informazioni, vedere [Microsoft Cloud Adoption Framework per Azure - Idoneità](/azure/cloud-adoption-framework/ready/).

## <a name="next-steps"></a>Passaggi successivi

Sono state esaminate la panoramica e l'architettura del progetto di esempio di definizione della base di Cloud Adoption Framework.

> [!div class="nextstepaction"]
> [Progetto di definizione della base di Cloud Adoption Framework - Procedura di distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
