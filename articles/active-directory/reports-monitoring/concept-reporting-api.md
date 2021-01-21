---
title: Introduzione all'API di creazione report di Azure AD | Microsoft Docs
description: Come iniziare a usare l'API di creazione report di Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2027f713baf2b8039187e933ffc7bb0b68b5cc5f
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631962"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Introduzione all'API di creazione report di Azure Active Directory

Azure Active Directory fornisce un'ampia gamma di [report](overview-reports.md), che contengono informazioni utili per le applicazioni, ad esempio i sistemi di informazioni di sicurezza e gestione degli eventi (SIEM) e gli strumenti di controllo e business intelligence. 

Usando l'API Microsoft Graph per i report di Azure AD è possibile ottenere l'accesso ai dati a livello di codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti.

Questo articolo offre una panoramica dell'API di creazione report, con informazioni su come accedervi.

Nel caso di problemi, vedere [Come ottenere supporto per Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).

## <a name="prerequisites"></a>Prerequisiti

Per accedere all'API di creazione report con o senza intervento dell'utente, è necessario:

1. Assegnare i ruoli (Ruolo con autorizzazioni di lettura per la sicurezza, Amministratore della sicurezza, Amministratore globale)
2. Registrare un'applicazione
3. Concedere le autorizzazioni
4. Ottenere le impostazioni di configurazione

Per istruzioni dettagliate vedere i [prerequisiti di accesso all'API di creazione report di Azure AD](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>Endpoint API 

L'endpoint API Microsoft Graph per i log di controllo è `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits`, mentre l'endpoint API Microsoft Graph per gli accessi è `https://graph.microsoft.com/v1.0/auditLogs/signIns`. Per altre informazioni, vedere le [informazioni di riferimento sull'API di controllo](/graph/api/resources/directoryaudit) e le [informazioni di riferimento sull'API di accesso](/graph/api/resources/signIn).

È possibile usare l' [API rilevamento dei rischi di Identity Protection](/graph/api/resources/identityriskevent?view=graph-rest-beta) per ottenere l'accesso a livello di codice ai rilevamenti di sicurezza usando Microsoft Graph. Per altre informazioni, vedere [Introduzione a Azure Active Directory Identity Protection e Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md). 
  
È anche possibile usare l' [API dei log di provisioning](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) per ottenere l'accesso programmatico agli eventi di provisioning nel tenant. 

## <a name="apis-with-microsoft-graph-explorer"></a>API con Microsoft Graph Explorer

È possibile usare [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) per verificare i dati dell'API di accesso e di controllo. Assicurarsi di accedere al proprio account con entrambi i pulsanti di accesso nell'interfaccia utente di Graph Explorer e impostare le autorizzazioni **AuditLog.Read.All** e **Directory.Read.All** per il tenant come illustrato.   

![Graph Explorer](./media/concept-reporting-api/graph-explorer.png)

![Interfaccia utente di Autorizzazioni di modifica](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Usare i certificati per accedere all'API di creazione report di Azure AD 

Usare l'API di creazione report di Azure AD con certificati se si prevede di recuperare i dati dei report senza l'intervento dell'utente.

Per istruzioni dettagliate vedere [Ottenere dati con l'API di creazione report di Azure AD con certificati](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Passaggi successivi

 * [Prerequisiti di accesso all'API di creazione report ](howto-configure-prerequisites-for-reporting-api.md) 
 * [Ottenere dati con l'API di creazione report di Azure AD con i certificati](tutorial-access-api-with-certificates.md)
 * [Risolvere gli errori relativi all'API di creazione report di Azure AD](troubleshoot-graph-api.md)
