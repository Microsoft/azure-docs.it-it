---
title: Connettere i dati Barracuda ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore Barracuda Web Application Firewall (WAF) per connettere i log di Barracuda con Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 1131c8e79d9df2883dcf7e975af9732c21318a49
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621165"
---
# <a name="connect-your-barracuda-waf-appliance"></a>Connettere il dispositivo Barracuda WAF 

Il connettore Barracuda Web Application Firewall (WAF) consente di connettere facilmente i log di Barracuda con Azure Sentinel, visualizzare i dashboard, creare avvisi personalizzati e migliorare l'analisi. In questo modo è possibile ottenere informazioni più dettagliate sulla rete dell'organizzazione e migliorare le funzionalità di sicurezza. Azure Sentinel sfrutta l'integrazione nativa tra **Barracuda** e log Analytics Agent per offrire un'integrazione perfetta. 

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="configure-and-connect-barracuda-waf"></a>Configurare e connettere Barracuda WAF
Barracuda Web Application Firewall può integrare ed esportare i log direttamente in Sentinel di Azure tramite Log Analytics Agent.
1. Passare a [Barracuda WAF Configuration Flow](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)e seguire le istruzioni per configurare la connessione, usando i parametri seguenti:
    - **ID area di lavoro**: copiare il valore dell'ID dell'area di lavoro dalla pagina del connettore Azure Sentinel Barracuda.
    - **Chiave primaria**: copiare il valore della chiave primaria dalla pagina del connettore Azure Sentinel Barracuda.
1. Per usare lo schema pertinente in Log Analytics per gli eventi Barracuda, cercare **CommonSecurityLog** e **barracuda_CL**.


## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log si avviino in Log Analytics. 



## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come connettere Appliance Barracuda ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.


