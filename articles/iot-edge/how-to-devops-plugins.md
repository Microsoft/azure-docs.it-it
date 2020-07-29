---
title: Abilitare CI/CD con plug-in Jenkins - Azure IoT Edge | Microsoft Docs
description: L'estensione Azure IoT Edge per Jenkins consente di integrare le attività di sviluppo e distribuzione di IoT Edge nella soluzione DevOps esistente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76510244"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrare Azure IoT Edge con le pipeline di Jenkins

Azure IoT Edge include il supporto integrato per Azure DevOps e Azure DevOps Projects, ma fornisce anche un plug-in per espandere le funzionalità di DevOps a Jenkins. [Jenkins](https://jenkins.io/) è un server di automazione open source che usa i plug-in per supportare molti tipi di progetti di sviluppo e distribuzione, tra cui IoT Edge.

Il plug-in Azure IoT Edge per Jenkins è incentrato su integrazione e distribuzione continue. È possibile creare una pipeline di compilazione e push che compila i moduli e inserisce le immagini del contenitore nel registro contenitori. Quindi, creare una pipeline di versione che consente di distribuire moduli ai dispositivi IoT Edge.

Prima di iniziare a usare il plug-in IoT Edge per Jenkins, è necessario un hub IoT in Azure e un registro contenitori per contenere le immagini del contenitore. Usare un'entità servizio di Azure per concedere le autorizzazioni di collaboratore di Jenkins all'hub IoT in modo che il plug-in possa creare le distribuzioni per i dispositivi IoT Edge.

Per iniziare, trovare le informazioni di installazione e utilizzo del [plug-in Azure IoT Edge per Jenkins](https://plugins.jenkins.io/azure-iot-edge).
