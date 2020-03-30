---
title: Gestione dei dati di Automazione di Azure
description: Questo articolo contiene più argomenti per la gestione di un ambiente di Automazione di Azure.  Include attualmente conservazione dei dati e backup del ripristino di emergenza di Automazione di Azure in Automazione di Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 340fa19b6f9f07e192123900bc96b07bb016542f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132887"
---
# <a name="managing-azure-automation-data"></a>Gestione dei dati di Automazione di Azure

Questo articolo contiene più argomenti per la gestione di un ambiente di Automazione di Azure.

## <a name="data-retention"></a>Conservazione dei dati

Quando si elimina una risorsa in Automazione di Azure, questa viene conservata per 30 giorni a scopo di controllo prima di essere rimossa in modo permanente. Non è possibile visualizzare o utilizzare la risorsa durante questo periodo di tempo. Questi criteri sono applicabili anche alle risorse appartenenti a un account di Automazione eliminato.

Automazione di Azure elimina e rimuove in modo permanente i processi più vecchi di 30 giorni.

La tabella seguente riepiloga i criteri di conservazione per diverse risorse.

| Dati | Policy |
|:--- |:--- |
| Account |Rimozione definitiva 30 giorni dopo l'eliminazione dell'account da parte di un utente. |
| Asset |Rimozione definitiva 30 giorni dopo l'eliminazione dell'asset da parte di un utente o 30 giorni dopo che l'account che contiene l'asset viene eliminato da un utente. |
| Moduli |Rimozione definitiva 30 giorni dopo l'eliminazione del modulo da parte di un utente o 30 giorni dopo che l'account che contiene il modulo viene eliminato da un utente. |
| Runbook |Rimozione definitiva 30 giorni dopo l'eliminazione della risorsa da parte di un utente o 30 giorni dopo che l'account che contiene la risorsa viene eliminato da un utente. |
| Processi |Eliminati e rimossi definitivamente 30 giorni dopo l'ultima modifica. ad esempio dopo il completamento, l'arresto o la sospensione del processo. |
| Configurazioni di nodo/File MOF |La configurazione del nodo precedente viene rimossa definitivamente 30 giorni dopo la generazione di una nuova configurazione del nodo. |
| Nodi DSC |Rimosso definitivamente 30 giorni dopo l'annullamento della registrazione del nodo dall'account di automazione tramite il portale di Azure o il cmdlet [Unregister-AzureRMAutomationDscNode](https://docs.microsoft.com/powershell/module/azurerm.automation/unregister-azurermautomationdscnode) in Windows PowerShell. I nodi vengono inoltre rimossi definitivamente 30 giorni dopo che l'account che contiene il nodo viene eliminato da un utente. |
| Report sul nodo |Vengono rimossi in modo permanente 90 giorni dopo la generazione di un nuovo report per quel nodo |

I criteri di conservazione sono applicabili a tutti gli utenti e non è attualmente possibile personalizzarli.

Tuttavia, se è necessario conservare i dati per un periodo di tempo più lungo, è possibile inoltrare i log dei processi di runbook ai log di Monitoraggio di Azure.However, if you need to retain data for a longer period of time, you can forward runbook job logs to Azure Monitor logs. Per altre informazioni, vedere inoltrare i dati dei processi di [Automazione di Azure ai log](automation-manage-send-joblogs-log-analytics.md)di Monitoraggio di Azure.For further information, review forward Azure Automation job data to Azure Monitor logs .

## <a name="backing-up-azure-automation"></a>Backup di Automazione di Azure

Quando si elimina un account di automazione in Microsoft Azure, vengono eliminati tutti gli oggetti presenti nell'account, ad esempio Runbook, moduli, configurazioni, impostazioni, processi e asset. Non sarà possibile ripristinare gli oggetti dopo l'eliminazione dell'account.  È possibile usare le informazioni seguenti per eseguire il backup dei contenuti dell'account di Automazione prima di eliminarlo.

### <a name="runbooks"></a>Runbook

È possibile esportare i runbook in file di script usando il portale di Azure o il cmdlet [Get-AzureAutomationRunbookDefinition](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationrunbookdefinition) in Windows PowerShell. Questi file di script possono essere importati in un account di Automazione, come illustrato in [Creazione o importazione di un Runbook](/previous-versions/azure/dn643637(v=azure.100)).

### <a name="integration-modules"></a>Moduli di integrazione

Non è possibile esportare i moduli di integrazione da Automazione di Azure. È necessario assicurare che siano disponibili all'esterno dell'account di Automazione.

### <a name="assets"></a>Asset

Non è possibile esportare [asset](/previous-versions/azure/dn939988(v=azure.100)) da Automazione di Azure.  Usando il portale di Azure, è necessario annotare i dettagli di variabili, credenziali, certificati, connessioni e pianificazioni.  È quindi necessario creare manualmente eventuali asset usati dai Runbook importati in un altro account di Automazione.

È possibile usare i [cmdlet di Azure](https://docs.microsoft.com/powershell/module/azurerm.automation#automation) per recuperare i dettagli di asset non crittografati e salvarli come riferimento futuro o creare asset equivalenti in un altro account di Automazione.

Non è possibile recuperare il valore delle variabili crittografate o il campo password delle credenziali mediante i cmdlet. Se non si conoscono questi valori, sarà possibile recuperarli da un Runbook attraverso le attività [Get-AutomationVariable](/previous-versions/azure/dn940012(v=azure.100)) e [Get-AutomationPSCredential](/previous-versions/azure/dn940015(v=azure.100)).

Non è possibile esportare certificati da Automazione di Azure. È necessario assicurarsi che eventuali certificati siano disponibili all'esterno di Azure.

### <a name="dsc-configurations"></a>Configurazioni DSC

È possibile esportare le configurazioni in file di script tramite il portale di Azure o il cmdlet [Export-AzureRmAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/azurerm.automation/export-azurermautomationdscconfiguration) in Windows PowerShell. Queste configurazioni possono essere importate e usate in un altro account di automazione.

## <a name="geo-replication-in-azure-automation"></a>Replica geografica in Automazione di Azure

Replica geografica, standard negli account di automazione di Azure, backup dei dati di account in un'area geografica diversa per la ridondanza. È possibile scegliere un'area primaria quando si configura l’account, poi un'area secondaria viene assegnata automaticamente. I dati secondari, copiati dall'area primaria, vengono continuamente aggiornati in caso di perdita di dati.  

Nella tabella seguente vengono illustrate le associazioni di aree primarie e secondarie disponibili:

| Primaria | Secondari |
| --- | --- |
| Stati Uniti centro-meridionali |Stati Uniti centro-settentrionali |
| Stati Uniti orientali 2 |Stati Uniti centrali |
| Europa occidentale |Europa settentrionale |
| Asia sud-orientale |Asia orientale |
| Giappone orientale |Giappone occidentale |

Nell'eventualità improbabile che vengano persi dei dati di un’area primaria, Microsoft tenta di ripristinarli. Qualora non fosse possibile ripristinare i dati primari, viene eseguito il failover geografico e i clienti interessati saranno informati attraverso la propria sottoscrizione.
