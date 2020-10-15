---
title: Creare visualizzazioni per analizzare i dati di log in monitoraggio di Azure | Microsoft Docs
description: Usando Progettazione viste in monitoraggio di Azure, è possibile creare visualizzazioni personalizzate che vengono visualizzate nel portale di Azure e contengono un'ampia gamma di visualizzazioni sui dati nell'area di lavoro Log Analytics. Questo articolo include una panoramica su Progettazione visualizzazioni e sulle procedure per la creazione e la modifica delle visualizzazioni personalizzate.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/04/2020
ms.openlocfilehash: f3ae081c8657a3ee6a27b0f9208c3eb4e4e745cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87759734"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Creare visualizzazioni personalizzate usando Progettazione viste in monitoraggio di Azure
Utilizzando Progettazione viste in monitoraggio di Azure, è possibile creare un'ampia gamma di visualizzazioni personalizzate nell'portale di Azure che consentono di visualizzare i dati nell'area di lavoro di Log Analytics. Questo articolo presenta una panoramica su Progettazione visualizzazioni e sulle procedure per la creazione e la modifica delle visualizzazioni personalizzate.

> [!IMPORTANT]
> Le visualizzazioni in monitoraggio di Azure sono state passate a [cartelle di lavoro](workbooks-overview.md) che forniscono funzionalità aggiuntive. Per informazioni dettagliate sulla conversione delle visualizzazioni esistenti nelle cartelle di lavoro di, vedere la [Guida alla transizione di Progettazione viste di monitoraggio di Azure alla cartella di lavoro](view-designer-conversion-overview.md) .
 


Per altre informazioni su Progettazione visualizzazioni, vedere:

* [Informazioni di riferimento sul riquadro](view-designer-tiles.md): offre una guida di riferimento sulle impostazioni relative a ogni riquadro disponibile nelle visualizzazioni personalizzate.
* [Informazioni di riferimento sulle parti della visualizzazione](view-designer-parts.md): offre una guida di riferimento sulle impostazioni relative alle parti della visualizzazione disponibili nelle visualizzazioni personalizzate.


## <a name="concepts"></a>Concetti
Le visualizzazioni vengono visualizzate nella pagina **Panoramica** di monitoraggio di Azure nel portale di Azure. Aprire questa pagina dal menu **Monitoraggio di Azure** facendo clic su **Altro** sotto la sezione **Informazioni dettagliate**. I riquadri in ogni visualizzazione personalizzata vengono visualizzati in ordine alfabetico e i riquadri per le soluzioni di monitoraggio vengono installati nella stessa area di lavoro.

![Pagina di panoramica](media/view-designer/overview-page.png)

Le visualizzazioni create con Progettazione visualizzazioni contengono gli elementi descritti nella tabella seguente:

| Parte | Descrizione |
|:--- |:--- |
| Riquadri | Vengono visualizzati nella pagina **Panoramica** di monitoraggio di Azure. Ogni riquadro mostra un riepilogo visivo della visualizzazione personalizzata che rappresenta. Ciascun tipo di riquadro offre una visualizzazione diversa dei record. Selezionare un riquadro per mostrare una visualizzazione personalizzata. |
| Visualizzazione personalizzata | Viene mostrata quando si seleziona un riquadro. Ciascuna visualizzazione contiene una o più parti della visualizzazione. |
| Parti della visualizzazione | Presentare una visualizzazione dei dati nell'area di lavoro Log Analytics in base a una o più [query di log](../log-query/log-query-overview.md). La maggior parte delle parti include un'intestazione, che offre una visualizzazione di alto livello, e un elenco che mostra i risultati principali. Ciascun tipo di parte offre una visualizzazione diversa dei record nell'area di lavoro Log Analytics. Per eseguire una query di log che fornisce record dettagliati, selezionare gli elementi nella parte. |

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per creare o modificare le visualizzazioni, è necessario disporre almeno [delle autorizzazioni a livello di collaboratore](manage-access.md#manage-access-using-azure-permissions) nell'area di lavoro log Analytics. Se non si dispone di questa autorizzazione, l'opzione Progettazione viste non verrà visualizzata nel menu.


## <a name="work-with-an-existing-view"></a>Usare una visualizzazione esistente
Le visualizzazioni create con Progettazione visualizzazioni mostrano le opzioni seguenti:

![Menu della panoramica](media/view-designer/overview-menu.png)

Le opzioni sono descritte nella tabella seguente:

| Opzione | Descrizione |
|:--|:--|
| Aggiorna   | Aggiorna la visualizzazione con i dati più recenti. | 
| Log      | Apre il [log Analytics](../log-query/log-query-overview.md) per analizzare i dati con le query di log. |
| Modifica       | Apre la visualizzazione in Progettazione visualizzazioni per modificarne il contenuto e la configurazione.  |
| Clone      | Crea una nuova visualizzazione e la apre in Progettazione visualizzazioni. La nuova visualizzazione ha lo stesso nome dell'originale, con l'aggiunta di *Copia*. |
| Intervallo di date | Impostare l'intervallo di tempo e di date per i dati inclusi nella vista. Questo intervallo di date viene applicato prima di eventuali intervalli di date impostati nelle query nella vista.  |
| +          | Definire un filtro personalizzato che viene definito per la vista. |


## <a name="create-a-new-view"></a>Creazione di una nuova vista
È possibile creare una nuova vista in Progettazione visualizzazioni selezionando **Progettazione visualizzazioni** nel menu dell'area di lavoro Log Analytics.

![Riquadro Progettazione viste](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Uso di Progettazione visualizzazioni
Progettazione visualizzazioni consente di creare nuove visualizzazioni o di modificare quelle esistenti. 

Progettazione visualizzazioni presenta tre sezioni: 
* **Progettazione**: contiene la visualizzazione personalizzata che si sta creando o modificando. 
* **Controlli**: contiene le parti e i riquadri aggiunti alla sezione **Progettazione**. 
* **Proprietà**: mostra le proprietà dei riquadri o delle parti selezionate.

![Progettazione viste](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Configurare il riquadro della visualizzazione
Una vista personalizzata può avere solo un singolo riquadro. Per visualizzare il riquadro corrente o selezionarne uno alternativo, selezionare la scheda **Riquadro** nella sezione **Controllo**. La sezione **Proprietà** mostra le proprietà del riquadro corrente. 

È possibile configurare le proprietà del riquadro in base alle informazioni contenute in [Informazioni di riferimento sul riquadro](view-designer-tiles.md) e quindi fare clic su **Applica** per salvare le modifiche.

### <a name="configure-the-visualization-parts"></a>Configurare le parti della visualizzazione
Una vista può includere qualsiasi numero di parti della visualizzazione. Per aggiungere parti a una visualizzazione, selezionare la scheda **Visualizzazione** e quindi una parte della visualizzazione. La sezione **Proprietà** mostra le proprietà della parte selezionata. 

È possibile configurare le proprietà della visualizzazione in base alle informazioni contenute in [Informazioni di riferimento sulle parti della visualizzazione](view-designer-parts.md) e quindi fare clic su **Applica** per salvare le modifiche.

Le visualizzazioni hanno solo una riga per le parti della visualizzazione. È possibile ridisporre le parti esistenti trascinandole in una nuova posizione.

È possibile rimuovere una parte della visualizzazione selezionando **X** nell'angolo superiore destro della parte.


### <a name="menu-options"></a>Opzioni del menu
Le opzioni per l'uso delle visualizzazioni in modalità di modifica sono descritte nella tabella seguente.

![Menu Modifica](media/view-designer/edit-menu.png)

| Opzione | Descrizione |
|:--|:--|
| Salva        | Salva le modifiche e chiude la visualizzazione. |
| Annulla      | Annulla le modifiche e chiude la visualizzazione. |
| Eliminare la visualizzazione | Elimina la visualizzazione. |
| Esportazione      | Esporta la visualizzazione in un [modello di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) da importare in un'altra area di lavoro. Il nome del file corrisponde al nome della visualizzazione e ha estensione *omsview*. |
| Importa      | Importa il file *omsview* esportato da un'altra area di lavoro. Questa azione sovrascrive la configurazione della visualizzazione esistente. |
| Clone       | Crea una nuova visualizzazione e la apre in Progettazione visualizzazioni. La nuova visualizzazione ha lo stesso nome dell'originale, con l'aggiunta di *Copia*. |

## <a name="next-steps"></a>Passaggi successivi
* Aggiungere [Riquadri](view-designer-tiles.md) alla vista personalizzata.
* Aggiungere [parti della visualizzazione](view-designer-parts.md) alla visualizzazione personalizzata.
