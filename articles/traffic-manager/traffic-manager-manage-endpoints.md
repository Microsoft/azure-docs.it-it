---
title: Gestione endpoint in Gestione traffico di Azure | Documentazione Microsoft
description: Questo articolo aiuterà ad aggiungere, rimuovere, abilitare e disabilitare gli endpoint da Gestione traffico di Azure.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: rohink
ms.openlocfilehash: 2aee4282fe6a66151ea39018f0d6c0db51f2c027
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939126"
---
# <a name="add-disable-enable-or-delete-endpoints"></a>Aggiungere, disabilitare, abilitare o eliminare gli endpoint

La funzionalità App Web nell’Azure App Service offre già funzionalità di routing del traffico failover e round robin per i siti Web che si trovano all'interno di un data center, indipendentemente dalla modalità del sito Web. Gestione traffico di Azure consente di specificare il routing del traffico failover e round robin per i siti Web e i servizi cloud che si trovano in data center diversi. Il primo passaggio necessario a fornire tale funzionalità consiste nell'aggiungere l'endpoint del servizio cloud o del sito Web a Gestione traffico.

È anche possibile disabilitare singoli endpoint che appartengono a un profilo di Gestione traffico. Quando si disabilita un endpoint questo continua a far parte del profilo, sebbene quest'ultimo si comporti come se non fosse incluso. Questa azione è utile per rimuovere temporaneamente un endpoint che si trova in modalità di manutenzione o è in corso di ridistribuzione. Quando l'endpoint è di nuovo attivo e in esecuzione, è possibile abilitarlo.

> [!NOTE]
> La disabilitazione di un endpoint non ha nulla a che vedere con il relativo stato di distribuzione in Azure. Un endpoint integro rimane attivo e in grado di ricevere traffico anche se è disabilitato in Gestione traffico. La disabilitazione di un endpoint in un profilo non influisce sul relativo stato in un altro profilo.

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>Per aggiungere un endpoint di servizio cloud o servizio app a un profilo di Gestione traffico

1. Da un browser accedere al portale di [Azure.](https://portal.azure.com)
2. Nella barra di ricerca del portale cercare il nome del **profilo di Gestione traffico** che si vuole modificare e quindi fare clic su tale profilo nei risultati visualizzati.
3. Nel pannello **Profilo di Gestione traffico** fare clic su **Endpoint** nella sezione **Impostazioni**.
4. Nel pannello **Endpoint** visualizzato fare clic su **Aggiungi**.
5. Nel pannello **Aggiungi endpoint** procedere come segue:
    1. In **Tipo** fare clic su **Endpoint di Azure**.
    2. Indicare un **nome** con il quale si desidera identificare l'endpoint.
    3. Per **Tipo di risorsa di destinazione** scegliere il tipo di risorsa appropriato nell'elenco a discesa.
    4. Per **Risorsa di destinazione** fare clic sul selettore **Scegli** per visualizzare un elenco delle risorse nella stessa sottoscrizione nel **pannello Risorse**. Nel pannello **Risorse** visualizzato selezionare il servizio da aggiungere come primo endpoint.
    5. In **Priorità** selezionare **1**. In questo modo tutto il traffico viene instradato a questo endpoint se è integro.
    6. Mantenere deselezionata l'opzione **Aggiungi come disabilitato**.
    7. Fare clic su **OK**.
6.  Ripetere i passaggi 4 e 5 per aggiungere l'endpoint di Azure successivo. Assicurarsi di aggiungerlo con il valore di **Priorità** impostato su **2**.
7.  Una volta completata l'aggiunta di entrambi gli endpoint, essi vengono visualizzati nel pannello **Profilo di gestione traffico** insieme al relativo stato di monitoraggio **Online**.

> [!NOTE]
> Dopo aver aggiunto o rimosso un endpoint da un profilo usando il metodo di routing del traffico *Failover*, non è più possibile ordinare l'elenco priorità failover come desiderato. È possibile modificare l'ordine dell'Elenco priorità failover nella pagina di configurazione. Per ulteriori informazioni, vedere [Configurare il routing del traffico failover](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Per disabilitare un endpoint

1. Da un browser accedere al portale di [Azure.](https://portal.azure.com)
2. Nella barra di ricerca del portale cercare il nome del **profilo di Gestione traffico** che si vuole modificare e quindi fare clic su tale profilo nei risultati visualizzati.
3. Nel pannello **Profilo di Gestione traffico** fare clic su **Endpoint** nella sezione **Impostazioni**. 
4. Fare clic sull'endpoint che si desidera disabilitare.
5. Nel pannello **Endpoint** modificare lo stato dell'endpoint impostandolo su **Disabilitato** e quindi fare clic su **Salva**.
6. I client continuano a inviare traffico all'endpoint per la durata (TTL). È possibile modificare la durata (TTL) nella pagina di configurazione del profilo di Gestione traffico.

## <a name="to-enable-an-endpoint"></a>Per abilitare un endpoint

1. Da un browser accedere al portale di [Azure.](https://portal.azure.com)
2. Nella barra di ricerca del portale cercare il nome del **profilo di Gestione traffico** che si vuole modificare e quindi fare clic su tale profilo nei risultati visualizzati.
3. Nel pannello **Profilo di Gestione traffico** fare clic su **Endpoint** nella sezione **Impostazioni**. 
4. Fare clic sull'endpoint che si desidera abilitare.
5. Nel pannello **Endpoint** modificare lo stato dell'endpoint impostandolo su **Abilitato** e quindi fare clic su **Salva**.
6. I client continuano a inviare traffico all'endpoint per la durata (TTL). È possibile modificare la durata (TTL) nella pagina di configurazione del profilo di Gestione traffico.

## <a name="to-delete-an-endpoint"></a>Per eliminare un endpoint

1. Da un browser accedere al portale di [Azure.](https://portal.azure.com)
2. Nella barra di ricerca del portale cercare il nome del **profilo di Gestione traffico** che si vuole modificare e quindi fare clic su tale profilo nei risultati visualizzati.
3. Nel pannello **Profilo di Gestione traffico** fare clic su **Endpoint** nella sezione **Impostazioni**. 
4. Fare clic sull'endpoint che si desidera eliminare.
5. Nel pannello **Endpoint** fare clic su **Elimina**


## <a name="next-steps"></a>Passaggi successivi

* [Gestire un profilo di Gestione traffico](traffic-manager-manage-profiles.md)
* [Configure routing methods](traffic-manager-configure-routing-method.md) (Configurare metodi di routing)
* [Risoluzione dei problemi relativi allo stato Danneggiato di Gestione traffico](traffic-manager-troubleshooting-degraded.md)
* [Considerazioni sulle prestazioni di gestione traffico](traffic-manager-performance-considerations.md)
* [Operazioni per Gestione traffico (informazioni di riferimento API REST)](https://go.microsoft.com/fwlink/p/?LinkID=313584)

