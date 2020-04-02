---
title: Soluzione di gestione nelle procedure consigliate di AzureManagement solution in Azure best practices
description: In questo articolo vengono forniti suggerimenti per la creazione di un file di soluzione di gestione. Informazioni su come usare origini dati, runbook, visualizzazioni e avvisi.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 7cb300297336edcce4294b800520ad570b12bcde
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548172"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Procedure consigliate per la creazione di soluzioni di gestione in Azure (anteprima)
> [!NOTE]
> Questa è una documentazione preliminare per la creazione di soluzioni di gestione in Azure attualmente disponibili in versione di anteprima. Qualsiasi schema descritto di seguito è soggetto a modifiche.  

Questo articolo riporta le procedure consigliate per la [creazione di una soluzione di gestione](solutions-solution-file.md) in Azure.  Queste informazioni verranno aggiornate quando saranno identificate procedure consigliate aggiuntive.

## <a name="data-sources"></a>Origini dati
- È possibile [configurare le origini dati con un modello di Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), ma non devono essere incluse nel file della soluzione.  Il motivo è che la configurazione delle origini dati non è attualmente idempotente, vale a dire che la soluzione potrebbe sovrascrivere la configurazione esistente nell'area di lavoro dell'utente.<br><br>Ad esempio la soluzione potrebbe richiedere eventi di avviso e di errore dal registro eventi dell'applicazione.  Se si specifica questo come origine dati nella soluzione, si rischia di rimuovere eventi informativi, se l'utente ha configurato questa impostazione nella sua area di lavoro.  Se sono stati inclusi tutti gli eventi, potrebbero essere raccolti troppi eventi informativi nell'area di lavoro dell'utente.

- Se la soluzione richiede dati da una delle origini dati standard, è necessario definire questo come prerequisito.  Dichiarare nella documentazione che il cliente deve configurare l'origine dati da solo.  
- Aggiungere un messaggio di [verifica del flusso di dati](../../azure-monitor/platform/view-designer-tiles.md) alle viste della soluzione per indicare all'utente le origini dati che devono essere configurate per la raccolta dei dati richiesti.  Questo messaggio viene mostrato nel riquadro della vista quando i dati necessari non vengono trovati.


## <a name="runbooks"></a>Runbook
- Aggiungere una [pianificazione di automazione](../../automation/automation-schedules.md) per ogni runbook della soluzione che deve essere eseguito in modo pianificato.
- Includere nella soluzione il [modulo IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) che dovrà essere usato da runbook per la scrittura dei dati nel repository di Log Analytics.  Configurare la soluzione in modo che [faccia riferimento](solutions-solution-file.md#solution-resource) a questa risorsa affinché rimanga se la soluzione viene rimossa.  In questo modo più soluzioni possono condividere il modulo.
- Utilizzare le [variabili di automazione](../../automation/automation-schedules.md) per fornire valori alla soluzione che gli utenti potranno modificare in seguito.  Anche se la soluzione è configurata per contenere la variabile, il suo valore può comunque essere modificato.

## <a name="views"></a>Viste
- Tutte le soluzioni devono includere una singola vista che viene mostrata nel portale dell'utente.  Questa vista può contenere più [parti della visualizzazione](../../azure-monitor/platform/view-designer-parts.md) per illustrare i diversi set di dati.
- Aggiungere un messaggio di [verifica del flusso di dati](../../azure-monitor/platform/view-designer-tiles.md) alle viste della soluzione per indicare all'utente le origini dati che devono essere configurate per la raccolta dei dati richiesti.
- Configurare la soluzione in modo che [contenga](solutions-solution-file.md#solution-resource) la vista, affinché venga rimossa se la soluzione viene rimossa.

## <a name="alerts"></a>Avvisi
- Definire l'elenco dei destinatari come parametro nel file della soluzione, in modo che l'utente possa definirli quando installa la soluzione.
- Configurare la soluzione in modo che [faccia riferimento](solutions-solution-file.md#solution-resource) alle regole di avviso, affinché gli utenti possano modificare la loro configurazione.  Potrebbe voler apportare modifiche come cambiare l'elenco dei destinatari, cambiare la soglia di avviso o disabilitare la regola di avviso. 


## <a name="next-steps"></a>Passaggi successivi
* Illustra il processo di base di [progettazione e creazione di una soluzione di gestione](solutions-creating.md).
* Informazioni su come [creare un file di soluzione](solutions-solution-file.md).
* [Aggiungere ricerche salvate e avvisi](solutions-resources-searches-alerts.md) alla soluzione di gestione.
* [Aggiungere viste](solutions-resources-views.md) alla soluzione di gestione.
* [Aggiungere runbook di automazione e altre risorse](solutions-resources-automation.md) alla soluzione di gestione.

