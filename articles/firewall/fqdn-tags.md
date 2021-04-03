---
title: Panoramica dei tag FQDN per Firewall di Azure
description: Un tag FQDN rappresenta un gruppo di nomi di dominio completi (FQDN) associati a servizi Microsoft noti.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c0bd2f6a021baae80dafcc3d544e1062d8e022e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94653335"
---
# <a name="fqdn-tags-overview"></a>Panoramica dei tag FQDN

Un tag FQDN rappresenta un gruppo di nomi di dominio completi (FQDN) associati a servizi Microsoft noti. È possibile usare un tag FQDN nelle regole delle applicazioni per consentire il necessario passaggio del traffico in uscita attraverso il firewall.

Ad esempio, per consentire manualmente il passaggio del traffico di Windows Update attraverso il firewall, è necessario creare più regole dell'applicazione in base alla documentazione Microsoft. Con i tag FQDN, è possibile creare una regola dell'applicazione e includere il tag **Windows Update** permettendo al traffico diretto agli endpoint di Microsoft Windows Update di passare attraverso il firewall.

Non è possibile creare tag FQDN personalizzati, né è possibile specificare quali FQDN sono inclusi all'interno di un tag. Microsoft gestisce gli FQDN inclusi nel tag FQDN e aggiorna quest'ultimo in caso di modifica degli FQDN. 

<!--- screenshot of application rule with a FQDN tag.-->

La tabella seguente mostra i tag FQDN attuali che è possibile usare. Microsoft gestisce questi tag e periodicamente ne aggiungerà altri.

## <a name="current-fqdn-tags"></a>Tag FQDN correnti

|Tag FQDN  |Descrizione  |
|---------|---------|
|Windows Update     |Consente l'accesso in uscita a Microsoft Update come descritto in [Come configurare un firewall per gli aggiornamenti software](/mem/configmgr/sum/get-started/install-a-software-update-point).|
|Diagnostica per Windows|Consente l'accesso in uscita a tutti gli [endpoint di Diagnostica Windows](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Active Protection Service (MAPS)|Consente l'accesso in uscita a [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Ambiente del servizio app|Consente l'accesso in uscita al traffico della piattaforma Ambiente del servizio app. Questo tag non copre gli endpoint di archiviazione ed SQL specifici del cliente creati da Ambiente del servizio app. Queste devono essere abilitate tramite [endpoint di servizio](../virtual-network/tutorial-restrict-network-access-to-resources.md) o aggiunte manualmente.<br><br>Per altre informazioni sull'integrazione di firewall di Azure con l'ambiente del servizio app, vedere [bloccare un ambiente del servizio app](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Backup di Azure|Consente l'accesso in uscita ai servizi Backup di Azure.|
|Azure HDInsight|Consente l'accesso in uscita per il traffico della piattaforma HDInsight. Questo tag non copre il traffico SQL o di archiviazione specifico del cliente da HDInsight. Abilitarli usando gli [endpoint di servizio](../virtual-network/tutorial-restrict-network-access-to-resources.md) o aggiungerli manualmente.|
|WindowsVirtualDesktop (WVD)|Consente il traffico della piattaforma desktop virtuale Windows in uscita. Questo tag non copre gli endpoint del bus di servizio e di archiviazione specifici della distribuzione creati da WVD. Sono inoltre necessarie le regole di rete DNS e KMS. Per altre informazioni sull'integrazione di firewall di Azure con WVD, vedere [usare il firewall di Azure per proteggere le distribuzioni di desktop virtuali di Windows](protect-windows-virtual-desktop.md).|
|Servizio Azure Kubernetes|Consente l'accesso in uscita a AKS. Per altre informazioni, vedere [usare il firewall di Azure per proteggere le distribuzioni di Azure Kubernetes Service (AKS)](protect-azure-kubernetes-service.md).|

> [!NOTE]
> Quando si seleziona il tag FQDN nella regola per un'applicazione, il campo protocol:port deve essere impostato su **https**.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come distribuire Firewall di Azure, vedere [Esercitazione: Distribuire e configurare Firewall di Azure tramite il portale di Azure](tutorial-firewall-deploy-portal.md).