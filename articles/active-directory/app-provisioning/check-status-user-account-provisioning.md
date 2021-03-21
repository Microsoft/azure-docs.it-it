---
title: Segnalare il provisioning automatico degli account utente alle applicazioni SaaS
description: Informazioni su come controllare lo stato dei processi di provisioning automatico dell'account utente e risolvere i problemi di provisioning di singoli utenti.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/09/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5d93cec201feeb43700fe849f2fddc8eaf22488a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579485"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Esercitazione: creazione di report sul provisioning automatico degli account utente

Azure Active Directory (Azure AD) include un [servizio di provisioning degli account utente](user-provisioning.md) che consente di automatizzare il provisioning del deprovisioning degli account utente nelle app SaaS e in altri sistemi, allo scopo di gestire il ciclo di vita delle identità end-to-end. Azure AD supporta i connettori di provisioning utenti preintegrati per tutte le applicazioni e i sistemi con le esercitazioni sul provisioning degli utenti [qui](../saas-apps/tutorial-list.md).

In questo articolo viene descritto come controllare lo stato dei processi di provisioning in seguito alla loro configurazione e come risolvere i problemi di provisioning di singoli utenti e gruppi.

## <a name="overview"></a>Panoramica

I connettori di provisioning vengono impostati e configurati tramite il [portale di Azure](https://portal.azure.com) in base alla [documentazione disponibile](../saas-apps/tutorial-list.md) per l'applicazione supportata. Una volta configurati e in esecuzione, i processi di provisioning possono essere segnalati tramite uno dei due metodi seguenti:

* **Portale di Azure** : questo articolo descrive principalmente il recupero delle informazioni del report dall' [portale di Azure](https://portal.azure.com), che fornisce un report di riepilogo del provisioning, oltre ai log di controllo dettagliati del provisioning per una determinata applicazione.
* **API di controllo**: Azure Active Directory offre anche un'API di controllo che consente di abilitare il recupero programmatico dei log di controllo dettagliati sul provisioning. Vedere [Informazioni di riferimento sull'API di controllo di Azure Active Directory](/graph/api/resources/directoryaudit) per la documentazione specifica sull'uso di questa API. Sebbene questo articolo non si concentri in particolare sull'uso dell'API, contiene informazioni dettagliate sui tipi di eventi di provisioning registrati nel log di controllo.

### <a name="definitions"></a>Definizioni

Questo articolo usa i termini seguenti, qui definiti:

* **Sistema di origine**: il repository degli utenti da cui il servizio di provisioning di Azure AD esegue la sincronizzazione. Azure Active Directory è il sistema di origine per la maggior parte dei connettori di provisioning preintegrati; esistono tuttavia alcune eccezioni, ad esempio la sincronizzazione in ingresso di Workday.
* **Sistema di destinazione**: il repository di utenti con cui il servizio di provisioning di Azure AD esegue la sincronizzazione. Si tratta in genere di un'applicazione SaaS (esempi: Salesforce, ServiceNow, G Suite, Dropbox for business), ma in alcuni casi può essere un sistema locale come Active Directory (ad esempio, la sincronizzazione in ingresso della giornata lavorativa per Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Ottenere i report di provisioning dal portale di Azure

Per ottenere informazioni sui report di provisioning per una determinata applicazione, iniziare avviando il [portale di Azure](https://portal.azure.com) e **Azure Active Directory** &gt; log di provisioning delle **app aziendali** &gt; **(anteprima)** nella sezione **attività** . È anche possibile passare all'applicazione aziendale per la quale è stato configurato il provisioning. Ad esempio, se si esegue il provisioning di utenti in LinkedIn Elevate, il percorso di navigazione per i dettagli dell'applicazione è:

**Azure Active Directory &gt; Applicazioni aziendali &gt; All applications (Tutte le applicazioni) &gt; LinkedIn Elevate**

Da qui è possibile accedere sia all'indicatore di stato del provisioning che ai log di provisioning, come descritto di seguito.

## <a name="provisioning-progress-bar"></a>Indicatore di stato del provisioning

L'indicatore di [stato del provisioning](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) è visibile nella scheda **provisioning** per una determinata applicazione. Si trova nella sezione **stato corrente** e Mostra lo stato del ciclo iniziale o incrementale corrente. In questa sezione vengono inoltre illustrate le operazioni seguenti:

* Il numero totale di utenti e gruppi che sono stati sincronizzati e che attualmente si trovano nell'ambito per il provisioning tra il sistema di origine e il sistema di destinazione.
* Ora dell'ultima sincronizzazione. In genere, le sincronizzazioni si verificano ogni 20-40 minuti, dopo il completamento di un [ciclo iniziale](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) .
* Indica se un [ciclo iniziale](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) è stato completato o meno.
* Eventuale quarantena applicata a un processo di provisioning e motivo dello stato di quarantena, ad esempio errore di comunicazione con il sistema di destinazione a causa di credenziali di amministratore non valide.

Lo **stato corrente** deve essere il primo aspetto degli amministratori per controllare l'integrità operativa del processo di provisioning.

 ![Report di riepilogo](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Log di provisioning (anteprima)

Tutte le attività eseguite dal servizio di provisioning vengono registrate nei [log di provisioning](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)di Azure ad. È possibile accedere ai log di provisioning nel portale di Azure selezionando **Azure Active Directory** &gt; log di provisioning di **app aziendali** &gt; **(anteprima)** nella sezione **attività** . È possibile cercare i dati di provisioning in base al nome dell'utente o all'identificatore nel sistema di origine o nel sistema di destinazione. Per informazioni dettagliate, vedere [log di provisioning (anteprima)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). I tipi di eventi dell'attività registrati includono:

## <a name="troubleshooting"></a>Risoluzione dei problemi

Il report di riepilogo del provisioning e i log di provisioning svolgono un ruolo chiave per aiutare gli amministratori a risolvere i problemi di provisioning degli account utente.

Per informazioni aggiuntive su come risolvere i problemi di provisioning automatico dell'utente in base agli scenari, vedere [Problemi di configurazione e provisioning degli utenti in un'applicazione](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)