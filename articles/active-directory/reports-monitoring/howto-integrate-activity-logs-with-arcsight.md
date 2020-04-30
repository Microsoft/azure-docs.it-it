---
title: Integrare i log con ArcSight usando monitoraggio di Azure | Microsoft Docs
description: Informazioni su come integrare i log di Azure Active Directory con ArcSight usando monitoraggio di Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f03b146331069371106c1857f2acc68b566d3c5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129238"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integrare i log di Azure Active Directory con ArcSight usando Monitoraggio di Azure

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) è una soluzione SIEM (informazioni di sicurezza e gestione degli eventi) che aiuta a rilevare e rispondere alle minacce alla sicurezza nella piattaforma. È ora possibile indirizzare i log di Azure Active Directory (Azure AD) ad ArcSight tramite Monitoraggio di Azure usando il connettore ArcSight per Azure AD. Questa funzionalità consente di monitorare il tenant in caso di violazione della sicurezza tramite ArcSight.  

In questo articolo viene descritto come indirizzare i log di Azure AD ad ArcSight tramite Monitoraggio di Azure. 

## <a name="prerequisites"></a>Prerequisiti

Per usare questa funzionalità, sono necessari:
* Un hub eventi di Azure contenente i log attività di Azure AD. Informazioni su come [trasmettere i log attività a un hub eventi](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Un'istanza configurata di ArcSight Syslog NG Daemon SmartConnector (SmartConnector) o ArcSight Load Balancer. Se gli eventi vengono inviati ad ArcSight Load Balancer, questi vengono inviati di conseguenza allo SmartConnector da parte del Load Balancer.

Scaricare e aprire la [Guida alla configurazione di ArcSight SmartConnector per Hub eventi di Monitoraggio di Azure](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292). Questa guida contiene i passaggi necessari per installare e configurare ArcSight SmartConnector per Monitoraggio di Azure. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrare i log Azure AD con ArcSight

1. In primo luogo, completare i passaggi nella sezione **Prerequisiti** della Guida alla configurazione. Questa sezione è costituita dai passaggi seguenti:
    * Impostare le autorizzazioni utente in Azure per assicurarsi che ci sia un utente con il ruolo **proprietario** per distribuire e configurare il connettore.
    * Aprire le porte nel server con Syslog NG Daemon SmartConnector, in modo che sia accessibile da Azure. 
    * La distribuzione esegue uno script Windows PowerShell, quindi è necessario abilitare PowerShell per eseguire gli script sul computer in cui si desidera distribuire il connettore.

2. Seguire i passaggi nella sezione **Distribuzione del connettore** della Guida di configurazione per distribuire il connettore. Questa sezione illustra come scaricare ed estrarre il connettore, configurare le proprietà dell'applicazione ed eseguire lo script di distribuzione dalla cartella estratta. 

3. Usare la procedura descritta in **Verifica della distribuzione in Azure** per assicurarsi che il connettore venga configurato e funzioni correttamente. Verificare gli elementi seguenti:
    * Le funzioni di Azure necessarie vengono create nella sottoscrizione di Azure.
    * I log di Azure AD vengono trasmessi alla destinazione corretta. 
    * Le impostazioni dell'applicazione nella distribuzione di vengono rese persistenti nelle impostazioni dell'applicazione nelle app per le funzioni di Azure. 
    * In Azure viene creato un nuovo gruppo di risorse per ArcSight, con un'applicazione Azure AD per il connettore ArcSight e gli account di archiviazione contenenti i file con mapping in formato CEF.

4. Infine, completare le fasi successive alla distribuzione nella sezione **Configurazioni post-distribuzione** della Guida alla configurazione. Questa sezione illustra come eseguire una configurazione aggiuntiva se si sta usando un piano di servizio app per impedire che le app per le funzioni siano inattive dopo un periodo di timeout, configurare lo streaming dei log delle risorse dall'hub eventi e aggiornare il certificato SysLog NG daemon SmartConnector keystore per associarlo all'account di archiviazione appena creato.

5. Nella Guida alla configurazione viene inoltre illustrato come personalizzare le proprietà del connettore in Azure e come aggiornare e disinstallare il connettore. È inoltre disponibile una sezione sul miglioramento delle prestazioni, tra cui l'aggiornamento a un [piano a consumo Azure](https://azure.microsoft.com/pricing/details/functions) e la configurazione di ArcSight Load Balancer se il carico dell'evento è maggiore di quello che può gestire un singolo Syslog NG Daemon SmartConnector.

## <a name="next-steps"></a>Passaggi successivi

[Guida alla configurazione di ArcSight SmartConnector per Hub eventi di Monitoraggio di Azure](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)
