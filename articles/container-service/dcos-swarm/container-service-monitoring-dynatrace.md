---
title: '(DEPRECATO) Monitorare un cluster del controller di dominio/sistema operativo di Azure: Dynatrace'
description: Monitorare un cluster DC/OS del servizio Azure Container con Dynatrace. Distribuire Dynatrace OneAgent tramite il dashboard di DC/OS.
author: MartinGoodwell
ms.service: container-service
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: ab6bb116c93aad8501da21dc5688d7e39f4195fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82166190"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(DEPRECATO) Monitorare un cluster del controller di dominio/sistema operativo del servizio Azure Container con Dynatrace SaaS/Gestito

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Questo articolo mostra come distribuire [Dynatrace](https://www.dynatrace.com/) OneAgent per monitorare tutti i nodi dell'agente nel cluster del servizio Azure Container. Per questa configurazione, è necessario un account con Dynatrace SaaS/Managed. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Managed
Dynatrace è una soluzione di monitoraggio nativa del cloud per gli ambienti cluster e dei contenitori altamente dinamici. Consente di ottimizzare le distribuzioni dei contenitori e le allocazioni di memoria usando i dati di utilizzo in tempo reale. È in grado di individuare automaticamente problemi a livello di applicazioni e infrastruttura fornendo una baseline automatizzata, oltre alla correlazione dei problemi e al rilevamento delle cause principali.

La figura seguente mostra l'interfaccia utente di Dynatrace:

![Interfaccia utente di Dynatrace](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>Prerequisiti 
[Distribuire](container-service-deployment.md) ed [eseguire la connessione](./../container-service-connect.md) a un cluster configurato dal servizio Azure Container. Esplorare l' [interfaccia utente](container-service-mesos-marathon-ui.md)di Marathon. Passare a [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/) per configurare un account Saas dynaTrace.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Configurare una distribuzione Dynatrace con Marathon
Questi passaggi illustrano come configurare e distribuire le applicazioni Dynatrace nel cluster con Marathon.

1. Accedere all'interfaccia utente del controller di dominio/sistema operativo da `http://localhost:80/`. Una volta nell'interfaccia utente di DC/OS, passare alla scheda **Universe** (Universo) e quindi cercare **Dynatrace**.

    ![Dynatrace in Universe (Universo) di DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. Per completare la configurazione, è necessario un account Dynatrace SaaS o un account di prova gratuito. Dopo avere eseguito l'accesso al dashboard di Dynatrace, selezionare **Deploy Dynatrace** (Distribuisci Dynatrace).

    ![Dynatrace - Set up PaaS integration (Configura integrazione Paas)](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. Nella pagina selezionare **Set up PaaS integration** (Configura integrazione Paas). 

    ![Token API di Dynatrace](./media/container-service-monitoring-dynatrace/api-token.png) 

4. Immettere il token API nella configurazione di Dynatrace OneAgent in Universe (Universo) di DC/OS. 

    ![Configurazione di Dynatrace OneAgent in Universe (Universo) di DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. Impostare le istanze sul numero di nodi che si intende eseguire. È anche possibile impostare un numero superiore, ma DC/OS tenterà di trovare nuove istanze finché tale numero non viene effettivamente raggiunto. Se si preferisce, è possibile impostarlo su un valore come 1000000. In questo caso, ogni volta che viene aggiunto un nuovo nodo al cluster, Dynatrace distribuisce automaticamente un agente per il nuovo nodo, ma DC/OS tenterà costantemente di distribuire altre istanze.

    ![Configurazione di Dynatrace nelle istanze di Universe (Universo) di DC/OS](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo avere installato il pacchetto, tornare al dashboard di Dynatrace. È possibile esplorare le diverse metriche di utilizzo per i contenitori all'interno del cluster. 
