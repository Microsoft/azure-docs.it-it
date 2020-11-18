---
title: Connettere i dati DNS in Sentinel di Azure | Microsoft Docs
description: Informazioni su come connettere qualsiasi Domain Name Server (DNS) in esecuzione in Windows ad Azure Sentinel installando un agente nel computer DNS.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 7701fc6d90fd9ebc7ec29f0ffdd7d050c58c036c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655664"
---
# <a name="connect-your-domain-name-server"></a>Connetti la Domain Name Server

> [!IMPORTANT]
> Il connettore dati DNS in Sentinel di Azure è attualmente disponibile in anteprima pubblica.
> Questa funzionalità viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile connettere qualsiasi DNS (Domain Name Server) in esecuzione in Windows ad Azure Sentinel. Questa operazione viene eseguita installando un agente nel computer DNS. Usando i log DNS, è possibile ottenere informazioni dettagliate sulla sicurezza, sulle prestazioni e sulle operazioni nell'infrastruttura DNS dell'organizzazione tramite la raccolta, l'analisi e la correlazione di log analitici e di controllo e altri dati correlati dai server DNS.

Quando si Abilita la connessione al log DNS, è possibile:
- Identificare i client che tentano di risolvere nomi di dominio dannosi
- Identificare i record di risorse non aggiornati
- Identificare i nomi di dominio sottoposti frequentemente a query e i client DNS loquaci
- Visualizzare il carico di richieste nei server DNS
- Visualizzare gli errori di registrazione di DNS dinamici

## <a name="connected-sources"></a>Origini connesse

La tabella seguente descrive le origini connesse che sono supportate da questa soluzione:

| **Origine connessa** | **Supporto tecnico** | **Descrizione** |
| --- | --- | --- |
| [Agenti di Windows](../azure-monitor/platform/agent-windows.md) | Sì | La soluzione raccoglie le informazioni DNS dagli agenti Windows. |
| [Agenti Linux](../azure-monitor/learn/quick-collect-linux-computer.md) | No | La soluzione non raccoglie le informazioni DNS dagli agenti Linux diretti. |
| [Gruppo di gestione di System Center Operations Manager](../azure-monitor/platform/om-agents.md) | Sì | La soluzione raccoglie le informazioni DNS dagli agenti di un gruppo di gestione di Operations Manager connesso. Non è necessaria una connessione diretta dall'agente Operations Manager a Monitoraggio di Azure. I dati vengono inoltrati dal gruppo di gestione all'area di lavoro Log Analytics. |
| [Account di archiviazione di Azure](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) | No | La soluzione non usa le risorse di archiviazione di Azure. |

### <a name="data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati

La soluzione raccoglie i dati relativi all'inventario e agli eventi DNS dai server DNS in cui è installato un agente Log Analytics. I dati relativi all'inventario, come il numero di server, zone e record di risorse DNS, vengono raccolti eseguendo i cmdlet di PowerShell per DNS. I dati vengono aggiornati ogni due giorni. I dati relativi agli eventi vengono raccolti quasi in tempo reale dai [log di analisi e di controllo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) offerti dalle funzionalità avanzate di registrazione e diagnostica DNS di Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Connettere il dispositivo DNS

1. Nel portale di Azure Sentinel selezionare **connettori dati** e scegliere il riquadro **DNS (anteprima)** .
1. Se i computer DNS sono in Azure:
    1. Fare clic su **Installa agente nella macchina virtuale Windows di Azure**.
    1. Nell'elenco **macchine virtuali** selezionare il computer DNS di cui si vuole eseguire lo streaming in Sentinel di Azure. Assicurarsi che si tratta di una macchina virtuale Windows.
    1. Nella finestra che viene visualizzata per la macchina virtuale fare clic su **Connetti**.  
    1. Fare clic su **Abilita** nella finestra **connettore DNS** . 

2. Se il computer DNS non è una macchina virtuale di Azure:
    1. Fare clic su **Installa agente in computer non Azure**.
    1. Nella finestra **agente diretto** selezionare **Scarica agente Windows (64 bit)** o **scarica agente Windows (bit 32)**.
    1. Installare l'agente nel computer DNS. Copiare l' **ID dell'area di lavoro**, la **chiave primaria** e la **chiave secondaria** e usarli quando richiesto durante l'installazione.

3. Per usare lo schema pertinente in Log Analytics per i log DNS, cercare **: eventi DNS**.

## <a name="validate"></a>Convalida 

In Log Analytics cercare lo schema **: eventi DNS** e verificare che siano presenti eventi.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se le query di ricerca non vengono visualizzate in Azure Sentinel, attenersi alla procedura seguente per visualizzare correttamente le query:
1. Attivare i [registri analisi DNS nei server](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Verificare che: eventi DNS sia visualizzato nell'elenco Log Analytics raccolta.
3. Attivare [analisi DNS di Azure](../azure-monitor/insights/dns-analytics.md).
4. In Azure Analisi DNS, in **configurazione**, modificare le impostazioni, salvarle, quindi modificarle di nuovo, se necessario, e quindi salvarle nuovamente.
5. Controllare Azure DNS Analytics per verificare che le query vengano visualizzate.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Appliance DNS locali ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).