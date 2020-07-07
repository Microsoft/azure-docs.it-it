---
title: Creare eventi imprevisti dagli avvisi in Azure Sentinel | Microsoft Docs
description: Informazioni su come creare eventi imprevisti dagli avvisi in Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 1593b96ae8412632120e8977635a4193996ca88d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025119"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Creare automaticamente eventi imprevisti dagli avvisi di sicurezza Microsoft

Gli avvisi generati nelle soluzioni di sicurezza Microsoft connesse ad Azure Sentinel, ad esempio Microsoft Cloud App Security e Azure Advanced Threat Protection, non creano automaticamente eventi imprevisti in Azure Sentinel. Per impostazione predefinita, quando si connette una soluzione Microsoft ad Azure Sentinel, tutti gli avvisi generati in tale servizio verranno archiviati come dati non elaborati in Azure Sentinel e più precisamente nella tabella Avviso di sicurezza dell'area di lavoro di Azure Sentinel. È quindi possibile usare tali dati come tutti gli altri dati non elaborati connessi in Sentinel.

È possibile configurare facilmente Azure Sentinel in modo da creare automaticamente eventi imprevisti ogni volta che viene attivato un avviso in una soluzione di sicurezza Microsoft connessa, seguendo le istruzioni riportate in questo articolo.

## <a name="prerequisites"></a>Prerequisiti
Per abilitare la creazione degli eventi imprevisti da avvisi del servizio di sicurezza, è necessario [connettere le soluzioni di sicurezza Microsoft](connect-data-sources.md#data-connection-methods).

## <a name="using-microsoft-security-incident-creation-analytic-rules"></a>Uso delle regole di analisi per la creazione di eventi imprevisti per la sicurezza Microsoft

Usare le regole predefinite disponibili in Azure Sentinel per scegliere le soluzioni di sicurezza Microsoft connesse che dovrebbero creare automaticamente gli eventi imprevisti di Azure Sentinel in tempo reale. È anche possibile modificare le regole per definire opzioni più specifiche per filtrare quali avvisi generati dalla soluzione di sicurezza Microsoft devono creare eventi imprevisti in Azure Sentinel. Ad esempio, è possibile scegliere di creare automaticamente eventi imprevisti di Azure Sentinel solo da avvisi con gravità alta del Centro sicurezza di Azure.

1. Nella portale di Azure in Azure Sentinel selezionare**Analisi**.

1. Selezionare la scheda **Rule templates** (Modelli di regole) per visualizzare tutte le regole di analisi predefinite.

    ![Modelli di regole](media/incidents-from-alerts/rule-templates.png)

1. Scegliere il modello di regola di analisi **Microsoft security** (Sicurezza Microsoft) da usare e fare clic su **Crea regola**.

    ![Regola di analisi sicurezza](media/incidents-from-alerts/security-analytics-rule.png)

1. È possibile modificare i dettagli della regola e scegliere di filtrare gli avvisi che creeranno gli eventi imprevisti in base alla gravità dell'avviso o al testo contenuto nel nome dell'avviso.  
      
    Ad esempio, se si sceglie **Centro sicurezza di Azure** nel campo **Microsoft security service** (Servizio di sicurezza Microsoft) e si sceglie **High** (Alta) nel campo **Filter by severity** (Filtra per gravità), solo gli avvisi del Centro sicurezza di Azure con gravità alta creeranno automaticamente eventi imprevisti in Azure Sentinel.  

    ![Creazione guidata regole](media/incidents-from-alerts/create-rule-wizard.png)

1. È anche possibile creare una nuova regola di **Microsoft security** (Sicurezza Microsoft) per filtrare gli avvisi di servizi di sicurezza Microsoft diversi facendo clic su **+Crea** e selezionando **Microsoft incident creation rule** (Regola di creazione eventi imprevisti Microsoft).

    ![Regola di creazione eventi imprevisti](media/incidents-from-alerts/incident-creation-rule.png)

  È possibile creare più di una regola di analisi **Microsoft security** (Sicurezza Microsoft) per tipo di **servizio di sicurezza Microsoft**. Non vengono creati eventi imprevisti duplicati perché ogni regola viene usata come filtro. Anche se un avviso corrisponde a più di una regola di analisi **Microsoft security** (Sicurezza Microsoft), viene creato solo un evento imprevisto di Azure Sentinel.

## <a name="enable-incident-generation-automatically-during-connection"></a>Abilitare automaticamente la generazione di eventi imprevisti durante la connessione
 Quando si connette una soluzione di sicurezza Microsoft, è possibile scegliere se gli avvisi della soluzione di sicurezza devono generare automaticamente eventi imprevisti in Azure Sentinel.

1. Connettere un'origine dati della soluzione di sicurezza Microsoft. 

   ![Generare gli eventi imprevisti della sicurezza](media/incidents-from-alerts/generate-security-incidents.png)

1. In **Create incidents** (Crea eventi imprevisti) selezionare **Enable** (Abilita) per abilitare la regola di analisi predefinita che crea automaticamente gli eventi imprevisti dagli avvisi generati nel servizio di sicurezza connesso. È quindi possibile modificare questa regola in **Analisi** e quindi **Active rules** (Regole attive).

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a usare Azure Sentinel, è necessario avere una sottoscrizione di Microsoft Azure. Se non si ha una sottoscrizione, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/free/).
- Informazioni su come [caricare i dati in Azure Sentinel](quickstart-onboard.md) e [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
