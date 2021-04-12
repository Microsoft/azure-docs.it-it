---
title: Esporta modello in portale di Azure
description: Usare portale di Azure per esportare un modello di Azure Resource Manager dalle risorse nella sottoscrizione.
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 3cc790b67b6076236a550c1fa202e0d173fb360e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731939"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Esportazione di una singola e più risorse in un modello in portale di Azure

Per semplificare la creazione di modelli di Azure Resource Manager, è possibile esportare un modello da risorse esistenti. Il modello esportato consente di comprendere la sintassi e le proprietà JSON che distribuiscono le risorse. Per automatizzare le distribuzioni future, iniziare con il modello esportato e modificarlo per lo scenario.

Gestione risorse consente di selezionare una o più risorse da esportare in un modello. È possibile concentrarsi esattamente sulle risorse necessarie nel modello.

Questo articolo illustra come esportare i modelli tramite il portale. È anche possibile usare l'interfaccia della riga di comando di [Azure](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)o l' [API REST](/rest/api/resources/resources/resourcegroups/exporttemplate).

## <a name="choose-the-right-export-option"></a>Scegliere l'opzione di esportazione corretta

Per esportare un modello sono disponibili due modi:

* **Esportare da un gruppo di risorse o una risorsa**. Questa opzione genera un nuovo modello da risorse esistenti. Il modello esportato è uno "snapshot" dello stato corrente del gruppo di risorse. È possibile esportare un intero gruppo di risorse o risorse specifiche all'interno di tale gruppo di risorse.

* **Esporta prima della distribuzione o dalla cronologia**. Questa opzione consente di recuperare una copia esatta di un modello utilizzato per la distribuzione.

A seconda dell'opzione scelta, i modelli esportati hanno qualità differenti.

| Da gruppo di risorse o risorsa | Prima della distribuzione o dalla cronologia |
| --------------------- | ----------------- |
| Il modello è uno snapshot dello stato corrente delle risorse. Sono incluse eventuali modifiche manuali apportate dopo la distribuzione. | Il modello Mostra solo lo stato delle risorse al momento della distribuzione. Eventuali modifiche manuali apportate dopo la distribuzione non sono incluse. |
| È possibile selezionare le risorse da esportare in un gruppo di risorse. | Sono incluse tutte le risorse per una distribuzione specifica. Non è possibile selezionare un subset di tali risorse o aggiungere risorse aggiunte in un momento diverso. |
| Il modello include tutte le proprietà per le risorse, incluse alcune proprietà che in genere non vengono impostate durante la distribuzione. Potrebbe essere necessario rimuovere o pulire queste proprietà prima di riutilizzare il modello. | Il modello include solo le proprietà necessarie per la distribuzione. Il modello è pronto per l'uso. |
| Il modello probabilmente non include tutti i parametri necessari per il riutilizzo. La maggior parte dei valori delle proprietà è hardcoded nel modello. Per ridistribuire il modello in altri ambienti, è necessario aggiungere parametri che aumentano la capacità di configurare le risorse.  È possibile deselezionare **Includi parametri** per poter creare parametri personalizzati. | Il modello include parametri che facilitano la ridistribuzione in ambienti diversi. |

Esportare il modello da un gruppo di risorse o una risorsa quando:

* È necessario acquisire le modifiche apportate alle risorse effettuate dopo la distribuzione originale.
* Si desidera selezionare le risorse esportate.

Esportare il modello prima della distribuzione o dalla cronologia, quando:

* Si desidera un modello di facile utilizzo.
* Non è necessario includere le modifiche apportate dopo la distribuzione originale.

## <a name="limitations"></a>Limitazioni

Quando si esporta da un gruppo di risorse o una risorsa, il modello esportato viene generato dagli [schemi pubblicati](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) per ogni tipo di risorsa. In alcuni casi lo schema non dispone della versione più recente per un tipo di risorsa. Controllare il modello esportato per assicurarsi che includa le proprietà necessarie. Se necessario, modificare il modello esportato per usare la versione dell'API necessaria.

La funzionalità Esporta modello non supporta l'esportazione di risorse Azure Data Factory. Per informazioni su come è possibile esportare Data Factory risorse, vedere [copiare o clonare una data factory in Azure Data Factory](../../data-factory/copy-clone-data-factory.md).

Per esportare le risorse create tramite il modello di distribuzione classica, è necessario [eseguirne la migrazione al modello di distribuzione gestione risorse](../../virtual-machines/migration-classic-resource-manager-overview.md).

Se viene visualizzato un avviso quando si esporta un modello che indica che un tipo di risorsa non è stato esportato, è comunque possibile individuare le proprietà per tale risorsa. Per informazioni sulle diverse opzioni per la visualizzazione delle proprietà delle risorse, vedere [individuazione delle proprietà delle risorse](view-resources.md). È anche possibile esaminare l' [API REST di Azure](/rest/api/azure/) per il tipo di risorsa.

È previsto un limite di 200 risorse nel gruppo di risorse in cui si crea il modello esportato. Se si tenta di esportare un gruppo di risorse con più di 200 risorse, viene visualizzato il messaggio di errore `Export template is not supported for resource groups more than 200 resources` .

## <a name="export-template-from-a-resource-group"></a>Esportare il modello da un gruppo di risorse

Per esportare una o più risorse da un gruppo di risorse:

1. Selezionare il gruppo di risorse che contiene le risorse che si desidera esportare.

1. Selezionare una o più risorse selezionando le caselle di controllo.  Per selezionare tutto, selezionare la casella di controllo a sinistra di **nome**. La voce di menu **Esporta modello** viene abilitata solo dopo aver selezionato almeno una risorsa.

   ![Esporta tutte le risorse](./media/export-template-portal/select-all-resources.png)

    Nello screenshot è selezionato solo l'account di archiviazione.
1. Selezionare **Esporta modello**.

1. Il modello esportato viene visualizzato ed è disponibile per il download e la distribuzione.

   ![Mostrare il modello](./media/export-template-portal/show-template.png)

   I **parametri di inclusione** sono selezionati per impostazione predefinita.  Quando questa opzione è selezionata, durante la generazione del modello verranno inclusi tutti i parametri del modello. Per creare parametri personalizzati, impostare questa casella di controllo in modo da non includerli.

## <a name="export-template-from-a-resource"></a>Esportare il modello da una risorsa

Per esportare una risorsa:

1. Selezionare il gruppo di risorse contenente la risorsa che si vuole esportare.

1. Selezionare la risorsa che si vuole esportare per aprire la risorsa.

1. Per tale risorsa selezionare **Esporta modello** nel riquadro sinistro.

   ![Esporta risorsa](./media/export-template-portal/export-single-resource.png)

1. Il modello esportato viene visualizzato ed è disponibile per il download e la distribuzione. Il modello contiene solo la risorsa singola. I **parametri di inclusione** sono selezionati per impostazione predefinita.  Quando questa opzione è selezionata, durante la generazione del modello verranno inclusi tutti i parametri del modello. Per creare parametri personalizzati, impostare questa casella di controllo in modo da non includerli.

## <a name="export-template-before-deployment"></a>Esporta modello prima della distribuzione

1. Selezionare il servizio di Azure che si vuole distribuire.

1. Immettere i valori per il nuovo servizio.

1. Dopo aver superato la convalida, ma prima di avviare la distribuzione selezionare **scaricare un modello per l'automazione**.

   ![Scaricare il modello](./media/export-template-portal/download-before-deployment.png)

1. Il modello viene visualizzato ed è disponibile per il download e la distribuzione.


## <a name="export-template-after-deployment"></a>Esporta modello dopo la distribuzione

È possibile esportare il modello usato per distribuire le risorse esistenti. Il modello che si ottiene è esattamente quello usato per la distribuzione.

1. Selezionare il gruppo di risorse che si vuole esportare.

1. Selezionare il collegamento in **Distribuzioni**.

   ![Selezionare la cronologia delle distribuzioni](./media/export-template-portal/select-deployment-history.png)

1. Selezionare una delle distribuzioni dalla cronologia delle distribuzioni.

   ![Selezionare la distribuzione](./media/export-template-portal/select-details.png)

1. Selezionare **modello**. Viene visualizzato il modello usato per questa distribuzione ed è disponibile per il download.

   ![Selezionare il modello](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come esportare modelli con l'interfaccia della riga di comando di [Azure](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)o l' [API REST](/rest/api/resources/resources/resourcegroups/exporttemplate).
- Per informazioni sulla sintassi del modello di Gestione risorse, vedere [comprendere la struttura e la sintassi dei modelli di Azure Resource Manager](template-syntax.md).
- Per informazioni su come sviluppare modelli, vedere le [esercitazioni dettagliate](../index.yml).
- Per visualizzare gli schemi del modello di Azure Resource Manager, vedere informazioni di [riferimento sui modelli](/azure/templates/).