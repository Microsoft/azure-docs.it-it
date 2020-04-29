---
title: Gestire l'accesso alla gestione di Azure con accesso condizionale in Azure AD
description: Informazioni sull'uso dell'accesso condizionale in Azure AD per gestire l'accesso alla gestione di Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77137405"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Gestire l'accesso alla gestione di Azure con l'accesso condizionale

> [!CAUTION]
> Assicurarsi di aver compreso il funzionamento dell'accesso condizionale prima di configurare un criterio per gestire l'accesso alla gestione di Azure. Assicurarsi di non creare condizioni che potrebbero bloccare il proprio accesso al portale.

L'accesso condizionale in Azure Active Directory (Azure AD) controlla l'accesso alle app cloud in base a condizioni specifiche specificate. Per consentire l'accesso, è possibile creare criteri di accesso condizionale che consentono o bloccano l'accesso in base al fatto che i requisiti dei criteri siano soddisfatti o meno. 

In genere, si usa l'accesso condizionale per controllare l'accesso alle app cloud. È inoltre possibile configurare i criteri per controllare l'accesso alla gestione di Azure in base a determinate condizioni (ad esempio, il rischio di accesso, il percorso o il dispositivo) e applicare dei requisiti come l'autenticazione a più fattori.

Per creare un criterio per la gestione di Azure, selezionare **Gestione di Microsoft Azure** in **App cloud** quando si sceglie l'app a cui applicare il criterio.

![Accesso condizionale per la gestione di Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Il criterio creato si applica a tutti gli endpoint di gestione di Azure, inclusi i seguenti:

- Portale di Azure
- Provider di Azure Resource Manager
- API di gestione del servizio classiche
- Azure PowerShell
- Portale di amministrazione delle sottoscrizioni di Visual Studio
- Azure DevOps
- Portale di Azure Data Factory

Si noti che il criterio si applica ad Azure PowerShell che chiama l'API di Azure Resource Manager. Non si applica ad [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) che chiama Microsoft Graph.


Per altre informazioni su come configurare e usare l'accesso condizionale, vedere [accesso condizionale in Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).
