---
title: Connettere i dati del firewall di Azure ad Azure Sentinel
description: Informazioni su come connettere i dati del firewall di Azure a Sentinel di Azure.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: b21ce75bfb33b5a8869c63b7d3f71fb9f0c93768
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621296"
---
# <a name="connect-data-from-azure-firewall"></a>Connettere i dati dal firewall di Azure

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti. 

È possibile connettere i log del firewall di Azure ad Azure Sentinel, consentendo di visualizzare i dati di log nelle cartelle di lavoro, usarli per creare avvisi personalizzati e incorporarli per migliorare l'analisi.

Altre informazioni sul [monitoraggio dei log del firewall di Azure](../firewall/firewall-diagnostics.md).

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

## <a name="connect-to-azure-firewall"></a>Connettersi al firewall di Azure
    
1. Dal menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Selezionare **firewall di Azure** dalla raccolta Data Connector e quindi selezionare **Apri pagina connettore**  nel riquadro di anteprima.

1. Abilitare i **log di diagnostica** in tutti i firewall i cui log si desidera connettere:

    1. Selezionare il collegamento **Apri risorsa firewall di Azure >** .

    1. Dal menu di spostamento **Firewall** selezionare impostazioni di **diagnostica**.

    1. Selezionare **+ Aggiungi impostazioni di diagnostica** nella parte inferiore dell'elenco.

    1. Nella schermata **impostazioni di diagnostica** immettere un nome nel campo  **nome impostazioni di diagnostica** .
    
    1. Contrassegnare la casella **di controllo Invia a log Analytics** . Sotto di essa verranno visualizzati due nuovi campi. Scegliere la **sottoscrizione** pertinente e l' **area di lavoro log Analytics** (in cui risiede Azure Sentinel).

    1. Contrassegnare le caselle di controllo dei tipi di regole di cui si desidera inserire i log. È consigliabile usare **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule**.

    1. Selezionare **Save (Salva** ) nella parte superiore della schermata.

1. Per usare lo schema pertinente in Log Analytics per gli avvisi del firewall di Azure, cercare **AzureDiagnostics**.

> [!NOTE]
>
> Con questo particolare connettore dati, gli indicatori di stato della connettività (uno stripe di colori nella raccolta di connettori dati e nelle icone di connessione accanto ai nomi dei tipi di dati) vengono visualizzati come connessi (verde) solo se i dati sono stati *inseriti* in un determinato momento nelle ultime due settimane. Una volta passate due settimane senza inserimento dati, il connettore verrà visualizzato come disconnesso. Il momento in cui arrivano più dati, lo stato *connesso* restituirà.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere i log del firewall di Azure a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).