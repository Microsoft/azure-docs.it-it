---
title: Come installare e usare le visualizzazioni di log Analytics | Microsoft Docs
description: Informazioni su come installare e usare le visualizzazioni di log Analytics per Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17026e4cfbe69e36c8e459aa259fe16b1c9d80d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74014428"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Installare e usare le viste di analisi dei log per Azure Active Directory

Le viste di analisi dei log per Azure Active Directory consentono di analizzare ed eseguire ricerche nei log attività di Azure AD nel tenant di Azure AD. I log attività di Azure AD includono:

* Log di controllo: il [report delle attività del log di controllo](concept-audit-logs.md) consente di accedere alla cronologia di ogni attività eseguita nel tenant.
* Log di accesso: il [report delle attività di accesso](concept-sign-ins.md) consente di determinare chi ha eseguito le attività segnalate nei log di controllo.

## <a name="prerequisites"></a>Prerequisiti

Per usare le viste di analisi dei log, è necessario:

* Un'area di lavoro Log Analytics nella sottoscrizione di Azure. Informazioni su [come creare un'area di lavoro Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Completare prima di tutto la procedura per [indirizzare i log attività di Azure AD all'area di lavoro Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Scaricare le viste dal [repository GitHub](https://aka.ms/AADLogAnalyticsviews) nel computer locale.

## <a name="install-the-log-analytics-views"></a>Installare le viste di analisi dei log

1. Passare all'area di lavoro Log Analytics. A tale scopo, passare innanzitutto al [portale di Azure](https://portal.azure.com) e selezionare **Tutti i servizi**. Digitare **Log Analytics** nella casella di testo e selezionare **Aree di lavoro di Log Analytics**. Selezionare l'area di lavoro a cui sono stati indirizzati i log attività, come parte dei prerequisiti.
2. Selezionare **Visualizza finestra di progettazione**, selezionare **Importa** e quindi selezionare **Scegli file** per importare le viste dal computer locale.
3. Selezionare le viste scaricate dai prerequisiti e selezionare **Salva** per salvare l'importazione. Eseguire questa operazione per la vista **Azure AD Account Provisioning Events** (Eventi di provisioning account Azure AD) e la vista **Sign-ins Events** (Eventi di accesso).

## <a name="use-the-views"></a>Usare le viste

1. Passare all'area di lavoro Log Analytics. A tale scopo, passare innanzitutto al [portale di Azure](https://portal.azure.com) e selezionare **Tutti i servizi**. Digitare **Log Analytics** nella casella di testo e selezionare **Aree di lavoro di Log Analytics**. Selezionare l'area di lavoro a cui sono stati indirizzati i log attività, come parte dei prerequisiti.

2. Nell'area di lavoro selezionare **Riepilogo dell'area di lavoro**. Dovrebbero essere visualizzate le tre viste seguenti:

    * **Azure AD Account Provisioning Events**(Eventi di provisioning account Azure AD): questa vista mostra i report relativi al controllo delle attività di provisioning, ad esempio il numero di nuovi utenti di cui è stato effettuato il provisioning e gli errori di provisioning, il numero di utenti aggiornati e gli errori di aggiornamento, nonché il numero di utenti di cui è stato annullato il provisioning e gli errori corrispondenti.    
    * **Sign-ins Events** (Eventi di accesso): questa vista mostra i report rilevanti relativi al monitoraggio delle attività di accesso, ad esempio gli accessi per applicazione, utente e dispositivo, nonché una visualizzazione di riepilogo che riporta il numero di accessi nel tempo.

3. Selezionare una di queste viste per passare ai singoli report. È anche possibile impostare avvisi per qualsiasi parametro dei report. Ad esempio, di seguito viene descritto come impostare un avviso per ogni errore di accesso. Selezionare prima di tutto la vista **Sign-ins Events** (Eventi di accesso), selezionare il report **Sign-in errors over time** (Errori di accesso nel tempo) e quindi selezionare **Analytics** (Analisi) per aprire la pagina dei dettagli con la query effettiva associata al report. 

    ![Dettagli](./media/howto-install-use-log-analytics-views/details.png)


4. Selezionare **Imposta avviso** e quindi selezionare **Ogni volta che l'operazione di ricerca log personalizzata è &lt;logica non definita&gt;** nella sezione **Criteri di avviso**. Poiché si vuole generare un avviso ogni volta che si verifica un errore di accesso, impostare la **Soglia** della logica di avviso predefinita su **1** e quindi selezionare **Fine**. 

    ![Configurare la logica dei segnali](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Immettere un nome e descrizione per l'avviso e impostare il livello di gravità su **Avviso**.

    ![Creare una regola](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Selezionare il gruppo di azioni per l'avviso. In generale, può trattarsi di un team a cui si vuole inviare notifica tramite posta elettronica o SMS o può essere un'attività automatizzata tramite webhook, runbook, funzioni, app per la logica o soluzioni di gestione dei servizi IT esterne. Vedere le informazioni su come [Creare e gestire gruppi di azione nel portale di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Selezionare **Crea regola di avviso** per creare l'avviso. A questo punto si riceverà un avviso ogni volta che si verifica un errore di accesso.

## <a name="next-steps"></a>Passaggi successivi

* [Come analizzare i log attività con i log di Monitoraggio di Azure](howto-analyze-activity-logs-log-analytics.md)
* [Introduzione ai log di Monitoraggio di Azure nel portale di Azure](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)