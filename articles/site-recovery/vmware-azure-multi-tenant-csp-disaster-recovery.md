---
title: Configurare il ripristino di emergenza di VMware in Azure in un ambiente multi-tenancy tramite Site Recovery e il programma Cloud Solution Provider (CSP) | Microsoft Docs
description: Descrive come configurare il ripristino di emergenza di VMware in un ambiente multi-tenant con Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: fb2a8a7bb14758ab21eb2183a119f456b53c8562
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654950"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>Configurare il ripristino di emergenza di VMware in un ambiente multi-tenancy con il programma Cloud Solution Provider (CSP)

Il [programma CSP](https://partner.microsoft.com/cloud-solution-provider) promuove storie migliori per i servizi cloud Microsoft, tra cui Microsoft 365, Enterprise Mobility Suite e Microsoft Azure. Con CSP, i partner acquisiscono una relazione end-to-end con i clienti e diventano il punto di contatto primario della relazione. I partner possono distribuire le sottoscrizioni di Azure per i clienti e combinarle con le proprie offerte personalizzate a valore aggiunto.

Con [Azure Site Recovery](site-recovery-overview.md), i partner possono gestire il ripristino di emergenza per i clienti direttamente tramite CSP. Possono in alternativa usare il programma CSP per configurare gli ambienti di Site Recovery e consentire ai clienti di gestire le proprie esigenze di ripristino di emergenza in modalità self-service. In entrambi gli scenari, i partner rappresentano l'anello di congiunzione tra Site Recovery e i clienti. I partner gestiscono la relazione con i clienti, addebitando loro l'uso di Site Recovery.

Questo articolo descrive come creare e gestire le sottoscrizioni di tenant tramite CSP, per uno scenario di replica VMware multi-tenant.

## <a name="prerequisites"></a>Prerequisiti

Per configurare la replica VMware è necessario eseguire queste operazioni:

- [Preparare](tutorial-prepare-azure.md) le risorse di Azure, inclusi un abbonamento ad Azure, una rete virtuale di Azure e un account di archiviazione.
- [Preparare](vmware-azure-tutorial-prepare-on-premises.md) i server e le macchine virtuali VMware locali.
- Per ogni tenant, creare un server di gestione separato che possa comunicare con le macchine virtuali tenant e i server vCenter. Solo il partner deve avere diritti di accesso a questo server di gestione. Altre informazioni sugli [ambienti multi-tenant](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Creare un account tenant

1. Accedere all'account CSP tramite il [Centro per i partner Microsoft](https://partnercenter.microsoft.com/).
2. Nel menu **Dashboard** selezionare **Clienti**.
3. Nella pagina visualizzata fare clic sul pulsante **Aggiungi cliente**.
4. Nella pagina **Nuovo cliente** compilare tutti i dettagli dell'account per il tenant.

    ![Pagina Informazioni account](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Quindi fare clic su **Successivo: Sottoscrizioni**.
6. Nella pagina di selezione delle sottoscrizioni selezionare la casella di controllo **Microsoft Azure**. È possibile aggiungere altre sottoscrizioni subito o in qualsiasi momento nel futuro.
7. Nella pagina **Revisione** verificare i dettagli del tenant e quindi fare clic su **Invia**.
8. Dopo aver creato l'account tenant verrà visualizzata una pagina di conferma con i dettagli dell'account predefinito e la password per la sottoscrizione. Salvare le informazioni e modificare eventualmente la password in un secondo momento tramite la pagina di accesso del portale di Azure.

Queste informazioni possono essere condivise con il tenant così come sono oppure, se necessario, è possibile creare e condividere un account separato.

## <a name="access-the-tenant-account"></a>Accedere all'account tenant

È possibile accedere alla sottoscrizione del tenant tramite il dashboard del Centro per i partner Microsoft.

1. Nella pagina **Clienti** fare clic sul nome dell'account tenant.
2. Nella pagina **Sottoscrizioni** dell'account tenant è possibile monitorare le sottoscrizioni esistenti per l'account e aggiungerne altre, se necessario.
3. Per gestire le operazioni di ripristino di emergenza del tenant, selezionare l'opzione **Tutte le risorse (portale di Azure)**. Verrà così consentito l'accesso alle sottoscrizioni di Azure del tenant.

    ![Collegamento Tutte le risorse](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. È possibile verificare l'accesso facendo clic sul collegamento di Azure Active Directory nella parte superiore destra del portale di Azure.

    ![Collegamento di Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

A questo punto è possibile eseguire tutte le operazioni di Site Recovery per il tenant nel portale di Azure. Per accedere alla sottoscrizione del tenant tramite CSP per il ripristino di emergenza gestito, seguire la procedura descritta in precedenza.

## <a name="assign-tenant-access-to-the-subscription"></a>Assegnare l'accesso tenant alla sottoscrizione

1. Verificare che sia configurata l'infrastruttura del ripristino di emergenza. I partner accedere alle sottoscrizioni del tenant tramite il portale CSP, a prescindere che il ripristino di emergenza sia gestito o self-service. Configurare l'insieme di credenziali e registrare l'infrastruttura nelle sottoscrizioni del tenant.
1. Fornire al tenant l' [account creato](#create-a-tenant-account).
1. È possibile aggiungere un nuovo utente alla sottoscrizione tenant tramite il portale CSP come indicato di seguito:

    1. Accedere alla pagina delle sottoscrizioni CSP del tenant e selezionare l'opzione **Users and licenses** (Utenti e licenze).

       ![Pagina delle sottoscrizioni CSP del tenant](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    1. A questo punto, creare un nuovo utente immettendo i relativi dettagli e selezionando le autorizzazioni oppure caricando l'elenco di utenti in un file CSV.

    1. Dopo aver creato un nuovo utente, tornare alla portale di Azure. Nella pagina **Sottoscrizione** selezionare la sottoscrizione da usare.

    1. Selezionare **controllo di accesso (IAM)**, quindi fare clic su **assegnazioni di ruolo**.

    1. Fare clic su **Aggiungi assegnazione ruolo** per aggiungere un utente con il livello di accesso pertinente. Gli utenti creati tramite il portale CSP vengono visualizzati nella scheda Assegnazioni di ruolo.

        ![Aggiungere un utente](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Per la maggior parte delle operazioni di gestione, il ruolo *collaboratore* è sufficiente. Gli utenti con questo livello di accesso possono eseguire qualsiasi operazione su una sottoscrizione, tranne modificare i livelli di accesso. Per questa operazione è infatti richiesto il livello di accesso *Proprietario*.
- Site Recovery ha anche tre [ruoli utente predefiniti](site-recovery-role-based-linked-access-control.md) che possono essere usati per limitare ulteriormente i livelli di accesso in base alle esigenze.

## <a name="multi-tenant-environments"></a>Ambienti multi-tenant

Esistono tre modelli multi-tenant principali:

* **Provider di servizi di hosting condiviso**: il partner possiede l'infrastruttura fisica e usa risorse condivise, ad esempio vCenter, data center, archiviazione fisica e così via, per ospitare le macchine virtuali di più tenant nella stessa infrastruttura. Il partner può fornire il ripristino di emergenza come servizio gestito oppure il tenant può essere proprietario del ripristino di emergenza tramite una soluzione self-service.

* **Provider di servizi di hosting dedicato**: il partner possiede l'infrastruttura fisica, ma usa risorse dedicate, ad esempio più vCenter, archivi dati fisici e così via, per ospitare le macchine virtuali di ogni tenant in un'infrastruttura separata. Il partner può fornire il ripristino di emergenza come servizio gestito oppure il tenant può essere proprietario di un'apposita soluzione self-service.

* **Provider di servizi gestiti**: il cliente possiede l'infrastruttura fisica che ospita le macchine virtuali e il partner provvede ad abilitare e gestire il ripristino di emergenza.

Impostando le sottoscrizioni di tenant come descritto in questo articolo, è possibile iniziare rapidamente ad abilitare i clienti in uno dei relativi modelli multi-tenant. Altre informazioni sui diversi modelli multi-tenant e l'abilitazione di controlli di accesso locali [qui](vmware-azure-multi-tenant-overview.md).

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sul [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](site-recovery-role-based-linked-access-control.md) per gestire le distribuzioni Azure Site Recovery.
- Altre informazioni sull' [architettura della replica](vmware-azure-architecture.md)da VMware ad Azure.
- [Esaminare l'esercitazione](vmware-azure-tutorial.md) per la replica di macchine virtuali VMware in Azure.
Altre informazioni sugli [ambienti multi-tenant](vmware-azure-multi-tenant-overview.md) per la replica di macchine virtuali VMware in Azure.
