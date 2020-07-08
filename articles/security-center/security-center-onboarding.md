---
title: Sicurezza avanzata del livello standard-Centro sicurezza di Azure
description: " Informazioni su come caricare il livello Standard del Centro sicurezza di Azure per la sicurezza avanzata. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: memildin
ms.openlocfilehash: be26a9d4c66412518079de303ac0764d979c3e7c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77912053"
---
# <a name="onboarding-to-azure-security-center-standard-for-enhanced-security"></a>Caricamento del livello Standard di Centro sicurezza di Azure per la sicurezza avanzata
Eseguire l'aggiornamento al livello Standard di Centro sicurezza di Azure per la sicurezza avanzata per sfruttare i vantaggi della gestione della sicurezza avanzata e la protezione dalle minacce per i carichi di lavoro cloud ibridi. È possibile provare gratuitamente il livello Standard. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/security-center/) del Centro sicurezza.

Il livello standard del Centro sicurezza include:

- **Sicurezza ibrida**: per ottenere una visualizzazione unificata della sicurezza in tutti i carichi di lavoro locali e cloud; applicare i criteri di sicurezza e la valutazione continua della sicurezza dei carichi di lavoro cloud ibridi per garantire la conformità con gli standard di sicurezza; raccogliere, eseguire ricerche e analizzare i dati di sicurezza da diverse origini, inclusi firewall e altre soluzioni partner.
- **Avvisi di sicurezza** : usare l'analisi avanzata e il Microsoft Intelligent Security Graph per ottenere un limite rispetto agli attacchi informatici in continua evoluzione. Sfrutta i vantaggi delle analisi comportamentali predefinite e di apprendimento automatico per identificare gli attacchi e gli exploit zero-day. Monitora le reti, i computer e i servizi cloud per rilevare attacchi imminenti e attività post violazione. Semplifica l'investigazione con strumenti interattivi e intelligence per le minacce contestuale.
- **Controlli di accesso e dell'applicazione**, che blocca malware e altre applicazioni indesiderate applicando i consigli per l'inserimento nell'elenco elementi consentiti adattati ai carichi di lavoro specifici e basati su tecnologia per l'apprendimento automatico. Riduce la superficie di attacco alla rete con l'accesso JIT (Just-In-Time) controllato alle porte di gestione nelle macchine virtuali di Azure per ridurre drasticamente l'esposizione ad attacchi di forza bruta e ad altri attacchi di rete.

## <a name="detecting-unprotected-resources"></a>Rilevamento delle risorse non protette
Il Centro sicurezza rileva automaticamente le sottoscrizioni o le aree di lavoro di Azure non abilitate per il livello Standard del Centro sicurezza di Azure. Sono incluse le sottoscrizioni Azure che usano il livello Gratuito del Centro di sicurezza e le aree di lavoro che non dispongono della soluzione di sicurezza abilitata.

È possibile aggiornare un'intera sottoscrizione di Azure al livello standard, che viene ereditato da tutte le risorse supportate nella sottoscrizione. Applicare il livello standard a un'area di lavoro si applica a tutte le risorse che inviano report all'area di lavoro.

> [!NOTE]
> Si consiglia di gestire i costi e limitare la quantità di dati raccolti per una soluzione limitandola a un determinato set di agenti. Il [targeting della soluzione](../operations-management-suite/operations-management-suite-solution-targeting.md) consente di applicare un ambito alla soluzione e avere come target un sottoinsieme di computer nell'area di lavoro. Se si usa una soluzione di targeting, il Centro sicurezza di Azure elenca l'area di lavoro come priva di una soluzione.
>
>

## <a name="upgrade-an-azure-subscription-or-workspace"></a>Aggiornare una sottoscrizione o area di lavoro di Azure
Per aggiornare una sottoscrizione o un'area di lavoro a standard:
1. Scegliere **Introduzione** dal menu principale del Centro sicurezza.
  ![Introduzione](./media/security-center-onboarding/get-started.png)
2. In **Aggiorna**, il Centro sicurezza di Azure elenca tutte le sottoscrizioni e i carichi di lavoro idonei per il caricamento. 
   - È possibile espandere **Applica la versione di prova gratuita** per visualizzare un elenco di tutte le sottoscrizioni e aree di lavoro con il relativo stato di idoneità di valutazione.
   -    È possibile aggiornare le sottoscrizioni e le aree di lavoro che non sono idonee per la versione di prova gratuita.
   -    È possibile selezionare le aree di lavoro e le sottoscrizioni idonee per avviare la versione di prova gratuita.
3.  Fare clic su **Avvia versione di prova gratuita** per avviare la versione di prova gratuita nelle sottoscrizioni selezionate.
  ![Selezionare la sottoscrizione](./media/security-center-onboarding/select-subscription.png)


   > [!NOTE]
   > Le funzionalità gratuite del Centro sicurezza vengono applicate solo alle macchine virtuali di Azure e VMSS. Non vengono applicate ai computer non Azure. Se si seleziona standard, le funzionalità standard vengono applicate a tutte le macchine virtuali di Azure, ai set di scalabilità di VM e ai computer non Azure che inviano report all'area di lavoro. Si consiglia di applicare il piano Standard per fornire la sicurezza avanzata per le risorse Azure e non Azure.
   >

## <a name="onboard-non-azure-computers"></a>Eseguire il caricamento di computer non Azure
Il Centro di sicurezza di Azure consente di monitorare le condizioni di sicurezza dei computer non Azure, ma è necessario prima caricare queste risorse. È possibile aggiungere computer non Azure dal pannello **Introduzione** o dal pannello **Calcolo**. Verranno esaminati in dettaglio entrambi i metodi.

### <a name="add-new-non-azure-computers-from-getting-started"></a>Aggiungere nuovi computer non Azure da **Introduzione**

1. Tornare a **Introduzione**.
2. Selezionare la scheda **Introduzione**.

   ![Non Azure](./media/security-center-onboarding/non-azure.png)

3. Fare clic su **Configura** in **Aggiungi nuovi computer non Azure**. Viene visualizzato un elenco delle aree di lavoro di Log Analytics. L'elenco include, se applicabile, l'area di lavoro predefinita creata dal Centro sicurezza quando è stato abilitato il provisioning automatico. Selezionare l'area di lavoro che si desidera usare.

   ![Aggiungere computer non Azure][7]

Se sono disponibili, le aree di lavoro esistenti vengono elencate sotto **Add new Non-Azure computers** (Aggiungi nuovi computer non Azure). È possibile aggiungere computer a un'area di lavoro esistente o creare una nuova area di lavoro. Per creare una nuova area di lavoro, selezionare il collegamento per **aggiungere una nuova area di lavoro**.

### <a name="add-new-non-azure-computers-from-compute"></a>Aggiungere nuovi computer non Azure da **Calcolo**

**Creare una nuova area di lavoro e aggiungere i computer**

1. In **Add new non-Azure computers** (Aggiungi nuovi computer non Azure) selezionare **add a new workspace** (Aggiungi una nuova area di lavoro).

   ![Aggiungere una nuova area di lavoro][4]

2. In **sicurezza e controllo**selezionare **area di lavoro OMS** per creare una nuova area di lavoro.
   > [!NOTE]
   > Le aree di lavoro OMS sono ora denominate aree di lavoro di Log Analytics.
3. In **Area di lavoro OMS** immettere le informazioni per l'area di lavoro.
4. Nell'**area di lavoro di OMS**selezionare **OK**. Dopo avere selezionato OK, verrà visualizzato un collegamento per scaricare un agente Windows o Linux e le chiavi per l'ID dell'area di lavoro da usare nella configurazione dell'agente.
5. In **Sicurezza e controllo** selezionare **OK**.

**Selezionare un'area di lavoro esistente e aggiungere i computer**

È possibile anche aggiungere un computer seguendo il flusso di lavoro da **Onboarding**, come illustrato in precedenza. È possibile aggiungere un computer seguendo il flusso di lavoro da **Calcolo**. In questo esempio, si usa **Calcolo**.

1. Tornare al menu principale del Centro sicurezza di Azure e al dashboard **Panoramica**.

   ![Panoramica][5]

2. Selezionare **Compute & apps** (Risorse di calcolo e app).
3. In **Compute & apps** (Risorse di calcolo e app) selezionare **Aggiungi computer**.

   ![Pannello Calcolo][6]

4. In **Add new non-Azure computers** (Aggiungi nuovi computer non Azure) selezionare un'area di lavoro a cui connettere il proprio computer e fare clic su **Aggiungi computer**.

   ![Aggiungere computer][7]

   Il pannello **Agente diretto** fornisce un collegamento per scaricare un agente Windows o Linux, nonché l'ID dell'area di lavoro e le chiavi da usare nella configurazione dell'agente.

## <a name="next-steps"></a>Passaggi successivi
Questo articolo illustra come caricare risorse Azure e non Azure per trarre vantaggio dalla sicurezza avanzata del Centro sicurezza di Azure. Per eseguire altre operazioni con le risorse caricate, vedere

- [Abilita raccolta dati](security-center-enable-data-collection.md)
- [Report di intelligence sulle minacce](security-center-threat-report.md)
- [Accesso JIT alle macchine virtuali](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-onboarding/onboard.png
[2]: ./media/security-center-onboarding/onboard-subscription.png
[3]: ./media/security-center-onboarding/get-started.png
[4]: ./media/security-center-onboarding/create-workspace.png
[5]: ./media/security-center-onboarding/overview.png
[6]: ./media/security-center-onboarding/compute-blade.png
[7]: ./media/security-center-onboarding/add-computer.png
[8]: ./media/security-center-onboarding/onboard-workspace.png
