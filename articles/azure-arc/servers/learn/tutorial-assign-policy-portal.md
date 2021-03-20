---
title: 'Esercitazione: Nuova assegnazione di criteri con il portale di Azure'
description: In questa esercitazione di avvio rapido viene usato il portale di Azure per creare un'assegnazione di Criteri di Azure per identificare le risorse non conformi.
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 9a07e490525ce532f8f843b30b3b83715e65ce3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91826598"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Esercitazione: Creare un'assegnazione di criteri per identificare le risorse non conformi

Il primo passaggio per comprendere la conformità in Azure consiste nell'identificare lo stato delle risorse. Criteri di Azure supporta il controllo dello stato del server con abilitazione di Azure Arc con i criteri di Configurazione guest. I criteri di Configurazione guest non applicano le configurazioni, ma controllano solo le impostazioni all'interno del computer. Questa esercitazione illustra il processo di creazione e assegnazione di un criterio, per identificare i server con abilitazione di Azure Arc in cui non è installato l'agente di Log Analytics.

Al termine di questo processo, verranno identificate le macchine virtuali in cui non è installato l'agente di Log Analytics per Windows o Linux e che sono quindi _non conformi_ all'assegnazione di criteri.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-policy-assignment"></a>Creare un'assegnazione di criteri

In questa esercitazione verrà creata un'assegnazione di criteri e verrà assegnata la definizione dei criteri _\[Anteprima]: L'agente di Log Analytics deve essere installato nelle macchine virtuali Linux in Azure Arc_ corrispondente.

1. Avviare il servizio Criteri di Azure nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Criteri**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="Cercare Criteri in Tutti i servizi" border="false":::

1. Selezionare **Assegnazioni** a sinistra nella pagina Criteri di Azure. Un'assegnazione è un criterio che è stato assegnato per l'implementazione in un ambito specifico.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="Selezionare la pagina Assegnazioni dalla pagina Panoramica dei criteri" border="false":::

1. Selezionare **Assegna criterio** nella parte superiore della pagina **Criteri - Assegnazioni**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="Assegnare una definizione dei criteri nella pagina Assegnazioni" border="false":::

1. Nella pagina **Assegna criterio** selezionare il valore di **Ambito** facendo clic sui puntini di sospensione e quindi selezionando un gruppo di gestione o una sottoscrizione. Facoltativamente, selezionare un gruppo di risorse. L'ambito determina le risorse o il raggruppamento di risorse a cui viene applicata l'assegnazione di criteri Fare quindi clic su **Seleziona** nella parte inferiore della pagina **Ambito**.

   Questo esempio usa la sottoscrizione di **Parnell Aerospace**, ma le opzioni disponibili sono diverse.

1. Le risorse possono essere escluse in base all'**Ambito**. Le **esclusioni** iniziano a un livello inferiore rispetto al livello dell'**Ambito**. Le **esclusioni** sono facoltative quindi per il momento è possibile lasciare vuoto il campo.

1. Selezionare i puntini di sospensione per **Definizione criteri** per aprire l'elenco delle definizioni disponibili. Criteri di Azure include definizioni di criteri predefinite che è possibile usare. Sono disponibili molte definizioni, ad esempio:

   - Imporre un tag e il relativo valore
   - Applicare un tag e il relativo valore
   - Eredita un tag dal gruppo di risorse se mancante

   Per un elenco parziale di tutti i criteri predefiniti disponibili, vedere [Esempi di Criteri di Azure](../../../governance/policy/samples/index.md).

1. Eseguire una ricerca nell'elenco delle definizioni dei criteri per trovare la definizione _\[Anteprima]: L'agente di Log Analytics deve essere installato nelle macchine virtuali Windows in Azure Arc_ se è stato abilitato l'agente Server con abilitazione di Azure Arc in una macchina virtuale basata su Windows. Per una macchina virtuale basata su Linux, trovare la definizione _\[Anteprima]: L'agente di Log Analytics deve essere installato nelle macchine virtuali Linux in Azure Arc_ corrispondente. Fare clic su tale criterio e fare clic su **Select** (Seleziona).

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="Trovare la definizione di criteri corretta" border="false":::

1. Il valore di **Nome dell'assegnazione** viene popolato automaticamente con il nome dei criteri selezionato, che è possibile modificare. Per questo esempio, lasciare _\[Anteprima]: L'agente di Log Analytics deve essere installato nelle macchine virtuali Windows in Azure Arc_ o _\[Anteprima]: L'agente di Log Analytics deve essere installato nelle macchine virtuali Linux in Azure Arc_, a seconda del tipo selezionato. È anche possibile aggiungere una **descrizione** facoltativa. La descrizione fornisce informazioni dettagliate su questa assegnazione dei criteri.
   Il campo **Assegnato da** verrà compilato automaticamente in base all'utente che ha eseguito l'accesso. Questo campo è facoltativo, quindi è possibile inserire valori personalizzati.

1. Lasciare l'opzione **Crea un'identità gestita** deselezionata. Questa casella _deve_ essere selezionata quando il criterio o l'iniziativa include un criterio con l'effetto [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists). Dal momento che il criterio usato per questa guida introduttiva non lo include, lasciare il campo vuoto. Per altre informazioni, vedere le [identità gestite](../../../active-directory/managed-identities-azure-resources/overview.md) e il [funzionamento della sicurezza di monitoraggio e aggiornamento](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).

1. Fare clic su **Assegna**.

A questo punto si è pronti per identificare le risorse non conformi e comprendere così lo stato di conformità dell'ambiente.

## <a name="identify-non-compliant-resources"></a>Identificare risorse non conformi

Selezionare **Assegnazioni** a sinistra nella pagina. Individuare quindi l'assegnazione dei criteri **\[Anteprima]: L'agente di Log Analytics deve essere installato nelle macchine virtuali Windows in Azure Arc** o **\[Anteprima]: L'agente di Log Analytics deve essere installato nelle macchine virtuali Linux in Azure Arc** creata.

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="Dettagli della conformità nella pagina Conformità ai criteri" border="false":::

Le eventuali risorse esistenti non conformi a questa nuova assegnazione verranno visualizzate nella scheda **Non-compliant resources** (Risorse non conformi).

Quando viene valutata una condizione per le risorse esistenti e tale condizione risulta soddisfatta, le risorse vengono contrassegnate come non conformi ai criteri. La tabella seguente illustra il funzionamento dei diversi effetti dei criteri in base alla valutazione della condizione per lo stato di conformità risultante. Anche se nel portale di Azure non viene visualizzata la logica di valutazione, vengono mostrati i risultati relativi allo stato di conformità. Lo stato di conformità può risultare conforme o non conforme.

| **Stato della risorsa** | **Effetto** | **Valutazione dei criteri** | **Stato di conformità** |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Non conforme |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Conforme |
| Nuovo | Audit, AuditIfNotExist\* | True | Non conforme |
| Nuovo | Audit, AuditIfNotExist\* | False | Conforme |

\* Gli effetti Append, DeployIfNotExist e AuditIfNotExist richiedono che l'istruzione IF sia TRUE.
Richiedono inoltre che la condizione di esistenza sia FALSE per lo stato non conforme. Se è TRUE, la condizione IF attiva la valutazione della condizione di esistenza per le risorse correlate.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere l'assegnazione creata, eseguire la procedura seguente:

1. Selezionare **Conformità** (o **Assegnazioni**) sul lato sinistro della pagina Criteri di Azure e individuare la definizione dei criteri **\[Anteprima]: L'agente di Log Analytics deve essere installato nelle macchine virtuali Windows in Azure Arc** o **\[Anteprima]: L'agente di Log Analytics deve essere installato nelle macchine virtuali Linux in Azure Arc** creata.

1. Fare clic con il pulsante destro del mouse sull'assegnazione dei criteri e selezionare **Elimina assegnazione**.

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="Eliminare un'assegnazione dalla pagina Conformità" border="false":::

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata assegnata una definizione dei criteri a un ambito ed è stato valutato il report di conformità. La definizione dei criteri garantisce che tutte le risorse nell'ambito siano conformi e identifica quelle che non lo sono. A questo punto si è pronti per monitorare i server con abilitazione di Azure Arc con Monitoraggio di Azure per le macchine virtuali.

Per informazioni su come monitorare e visualizzare le prestazioni, eseguire il processo e le relative dipendenze dalla macchina virtuale, procedere all'esercitazione:

> [!div class="nextstepaction"]
> [Abilita Monitoraggio di Azure per le macchine virtuali](tutorial-enable-vm-insights.md)