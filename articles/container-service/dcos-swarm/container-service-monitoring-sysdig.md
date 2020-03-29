---
title: (DEPRECATO) Monitorare un cluster del servizio Azure Container con Sysdig
description: Monitorare un cluster del servizio Azure Container con Sysdig.
author: sauryadas
ms.service: container-service
ms.topic: conceptual
ms.date: 08/08/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: a22d48554573e2517b318f6172b759864bf46612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277741"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-sysdig"></a>(DEPRECATO) Monitorare un cluster del servizio Azure Container con Sysdig

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

In questo articolo verranno distribuiti agenti di Sysdig in tutti i nodi agente nel cluster del servizio Azure Container. Per questa configurazione, è necessario un account con Sysdig. 

## <a name="prerequisites"></a>Prerequisiti
[Distribuire](container-service-deployment.md) e [connettere](../container-service-connect.md) un cluster configurato dal servizio Azure Container. Esplorare l' [interfaccia utente](container-service-mesos-marathon-ui.md)di Marathon. Passare [https://app.sysdigcloud.com](https://app.sysdigcloud.com) a configurare un account cloud Sysdig. 

## <a name="sysdig"></a>Sysdig
Sysdig è un servizio di monitoraggio che consente di monitorare i contenitori nel cluster. Sysdig non solo consente di risolvere problemi, ma include anche le metriche di monitoraggio di base per CPU, rete, memoria e I/O. Con Sysdig è facile determinare quali contenitori sono più usati o quali usano la maggior quantità di memoria e CPU. Questa visualizzazione è disponibile nella sezione della panoramica, attualmente nella versione beta. 

![Interfaccia utente di Sysdig](./media/container-service-monitoring-sysdig/sysdig6.png) 

## <a name="configure-a-sysdig-deployment-with-marathon"></a>Configurare una distribuzione Sysdig con Marathon
Questi passaggi illustrano come configurare e distribuire le applicazioni Sysdig nel cluster con Marathon. 

Accedere all'interfaccia utente [http://localhost:80/](http://localhost:80/) di DC/OS tramite Once nell'interfaccia utente DC/OS passare all'"Universo", che si trova in basso a sinistra e quindi cercare "Sysdig".

![Sysdig in Universe (Universo) per il controller di dominio/sistema operativo](./media/container-service-monitoring-sysdig/sysdig1.png)

Per completare la configurazione, è necessario un account cloud Sysdig o un account di valutazione gratuito. Una volta connessi al sito Web del cloud di Sysdig, fare clic sul nome utente. Nella pagina verrà visualizzata la chiave di accesso. 

![Chiave API di Sysdig](./media/container-service-monitoring-sysdig/sysdig2.png) 

Immettere quindi la chiave di accesso nella configurazione di Sysdig in Universe (Universo) per il controller di dominio/sistema operativo. 

![Configurazione di Sysdig in Universe (Universo) per il controller di dominio/sistema operativo](./media/container-service-monitoring-sysdig/sysdig3.png)

Impostare ora le istanze su 10000000 in modo che, quando viene aggiunto un nuovo nodo al cluster, Sysdig distribuirà automaticamente un agente nel nuovo nodo. Questa è una soluzione provvisoria per verificare che Sysdig venga distribuito in tutti i nuovi agenti del cluster. 

![Configurazione di Sysdig nelle istanze di Universe (Universo) per il controller di dominio/sistema operativo](./media/container-service-monitoring-sysdig/sysdig4.png)

Una volta installato il pacchetto, tornare all'interfaccia utente di Sysdig per esplorare le diverse metriche di utilizzo per i contenitori nel cluster. 

È anche possibile installare i dashboard specifici di Mesos e Marathon tramite la [creazione guidata nuovo dashboard](https://app.sysdigcloud.com/#/dashboards/new).
