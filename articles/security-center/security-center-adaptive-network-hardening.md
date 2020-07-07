---
title: Protezione avanzata della rete adattiva nel centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come usare i modelli di traffico effettivi per rafforzare le regole dei gruppi di sicurezza di rete (NSG) e migliorare ulteriormente il comportamento di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80385079"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Protezione avanzata della rete adattiva nel centro sicurezza di Azure
Informazioni su come configurare la protezione avanzata della rete adattiva nel centro sicurezza di Azure.

## <a name="what-is-adaptive-network-hardening"></a>Che cos'è la protezione avanzata della rete adattiva?
L'applicazione dei [gruppi di sicurezza di rete (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) per filtrare il traffico da e verso le risorse migliora il comportamento di sicurezza della rete. Tuttavia, è comunque possibile che si verifichino alcuni casi in cui il traffico effettivo che scorre attraverso NSG è un subset delle regole NSG definite. In questi casi, migliorare ulteriormente il comportamento di sicurezza può essere effettuato grazie alla protezione avanzata delle regole NSG, in base ai modelli di traffico effettivi.

La protezione avanzata della rete adattiva fornisce consigli per rafforzare ulteriormente le regole NSG. Usa un algoritmo di Machine Learning che determina il traffico effettivo, la configurazione attendibile nota, l'Intelligence per le minacce e altri indicatori di compromissione, quindi fornisce consigli per consentire il traffico solo da tuple IP/porte specifiche.

Ad esempio, supponiamo che la regola NSG esistente consenta il traffico da 140.20.30.10/24 sulla porta 22. La raccomandazione per la protezione avanzata della rete adattiva, basata sull'analisi, consiste nel limitare l'intervallo e consentire il traffico da 140.23.30.10/29, ovvero un intervallo di indirizzi IP più restrittivo e negare tutto il traffico a tale porta.

>[!TIP]
> Le raccomandazioni per la protezione avanzata della rete adattiva sono supportate solo sulle seguenti porte specifiche (per UDP e TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![Visualizzazione protezione avanzata della rete](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Visualizzare gli avvisi e le regole di protezione avanzata della rete adattiva

1. Nel centro **sicurezza selezionare rete**  ->  **Adaptive Network hardening**. Le VM di rete sono elencate in tre schede separate:
   * **Risorse non integre**: VM che attualmente presentano raccomandazioni e avvisi attivati eseguendo l'algoritmo di protezione avanzata della rete adattiva. 
   * **Risorse integre**: VM senza avvisi e raccomandazioni.
   * **Risorse non analizzate**: le macchine virtuali in cui non è possibile eseguire l'algoritmo di protezione avanzata della rete adattiva a causa di uno dei motivi seguenti:
      * Le **macchine virtuali sono macchine virtuali classiche**: sono supportate solo macchine virtuali Azure Resource Manager.
      * **I dati disponibili non sono sufficienti**: per generare raccomandazioni accurate per la protezione avanzata del traffico, il Centro sicurezza richiede almeno 30 giorni di dati sul traffico.
      * La **macchina virtuale non è protetta da ASC standard**: solo le VM impostate per il piano tariffario standard del Centro sicurezza sono idonee per questa funzionalità.

     ![risorse non integre](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. Dalla scheda **risorse non integre** selezionare una macchina virtuale per visualizzare gli avvisi e le regole di protezione avanzata consigliate da applicare.

    ![protezione avanzata degli avvisi](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Esaminare e applicare le regole consigliate per la protezione avanzata della rete adattiva

1. Dalla scheda **risorse non integre** selezionare una macchina virtuale. Sono elencati gli avvisi e le regole di protezione avanzata consigliate.

     ![regole di protezione avanzata](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Nella scheda **regole** sono elencate le regole che la protezione avanzata della rete adattiva consiglia di aggiungere. Nella scheda **avvisi** sono elencati gli avvisi generati a causa del traffico, che si propagano alla risorsa, che non rientra nell'intervallo IP consentito nelle regole consigliate.

2. Se si desidera modificare alcuni parametri di una regola, è possibile modificarli, come illustrato in [modificare una regola](#modify-rule).
   > [!NOTE]
   > È anche possibile [eliminare](#delete-rule) o [aggiungere](#add-rule) una regola.

3. Selezionare le regole che si desidera applicare al NSG, quindi fare clic su **applica**.

      > [!NOTE]
      > Le regole imposte vengono aggiunte a NSG (s) che proteggono la macchina virtuale. Una macchina virtuale può essere protetta da un NSG associato alla scheda di interfaccia di rete o alla subnet in cui risiede la macchina virtuale o a entrambe.

    ![Imponi regole](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Modificare una regola <a name ="modify-rule"> </a>

Potrebbe essere necessario modificare i parametri di una regola consigliata. Ad esempio, potrebbe essere necessario modificare gli intervalli IP consigliati.

Alcune linee guida importanti per la modifica di una regola di protezione avanzata della rete adattiva:

* È possibile modificare i parametri solo per le regole "Allow". 
* Non è possibile modificare le regole "Consenti" per diventare regole "Nega". 

  > [!NOTE]
  > La creazione e la modifica delle regole di "negazione" vengono eseguite direttamente nel NSG. Per altre informazioni, vedere [creare, modificare o eliminare un gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* Una regola **Deny all traffic** è l'unico tipo di regola "Deny" che verrebbe elencata e non può essere modificata. È tuttavia possibile eliminarla (vedere [eliminare una regola](#delete-rule)).
  > [!NOTE]
  > Quando si esegue l'algoritmo, il Centro sicurezza non identifica il traffico che deve essere consentito, in base alla configurazione NSG esistente, è consigliabile utilizzare una regola di **negazione di tutto il traffico** . Pertanto, la regola consigliata consiste nel negare tutto il traffico alla porta specificata. Il nome di questo tipo di regola viene visualizzato come "*generato dal sistema*". Dopo l'applicazione di questa regola, il nome effettivo in NSG sarà una stringa costituita dal protocollo, direzione del traffico, "Nega" e un numero casuale.

*Per modificare una regola di protezione avanzata della rete adattiva:*

1. Per modificare alcuni parametri di una regola, nella scheda **regole** fare clic sui tre puntini di sospensione (...) alla fine della riga della regola, quindi fare clic su **modifica**.

   ![Modifica regola](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Nella finestra **Modifica regola** aggiornare i dettagli che si desidera modificare e fare clic su **Salva**.

   > [!NOTE]
   > Dopo aver fatto clic su **Salva**, la regola è stata modificata. *Tuttavia, non è stato applicato a NSG.* Per applicarla, è necessario selezionare la regola nell'elenco e fare clic su **applica** (come illustrato nel passaggio successivo).

   ![Modifica regola](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Per applicare la regola aggiornata, dall'elenco selezionare la regola aggiornata e fare clic su **applica**.

    ![Applica regola](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Aggiungi una nuova regola <a name ="add-rule"> </a>

È possibile aggiungere una regola "Consenti" non consigliata dal centro sicurezza.

> [!NOTE]
> Qui è possibile aggiungere solo le regole "Allow". Se si desidera aggiungere regole di "negazione", è possibile eseguire questa operazione direttamente nel NSG. Per altre informazioni, vedere [creare, modificare o eliminare un gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Per aggiungere una regola di protezione avanzata della rete adattiva:*

1. Fare clic su **Aggiungi regola** (presente nell'angolo in alto a sinistra).

   ![Aggiungi regola](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Nella finestra **nuova regola** immettere i dettagli e fare clic su **Aggiungi**.

   > [!NOTE]
   > Dopo aver fatto clic su **Aggiungi**, la regola è stata aggiunta correttamente ed è elencata con le altre regole consigliate. Tuttavia, non è stato applicato a NSG. Per attivarla, è necessario selezionare la regola nell'elenco e fare clic su **applica** (come illustrato nel passaggio successivo).

3. Per applicare la nuova regola, dall'elenco selezionare la nuova regola e fare clic su **applica**.

    ![Applica regola](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Eliminare una regola <a name ="delete-rule"> </a>

Quando necessario, è possibile eliminare una regola consigliata per la sessione corrente. Ad esempio, è possibile determinare che l'applicazione di una regola consigliata potrebbe bloccare il traffico legittimo.

*Per eliminare una regola di protezione avanzata della rete adattiva per la sessione corrente:*

1. Nella scheda **regole** fare clic sui tre puntini di sospensione (...) alla fine della riga della regola, quindi fare clic su **Elimina**.  

    ![regole di protezione avanzata](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)