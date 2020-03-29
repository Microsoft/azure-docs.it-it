---
title: Gestire una macchina virtuale di Azure con la raccolta dell'inventario | Microsoft Docs
description: Gestire una macchina virtuale di Azure con la raccolta dell'inventario
services: automation
ms.subservice: change-inventory-management
keywords: inventario, automazione, modifica, gestione
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: d0324038b8a38d7eba84e5472b8f90439b0322c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844820"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Gestire una macchina virtuale di Azure con la raccolta dell'inventario

È possibile abilitare il monitoraggio dell'inventario per una macchina virtuale di Azure dalla pagina delle risorse della macchina virtuale. È possibile raccogliere e visualizzare le seguenti informazioni di inventario nei computer:

- Software Windows (applicazioni Windows e aggiornamenti di Windows), servizi, file e chiavi del Registro di sistema
- Daemon (pacchetti) software Linux e file

Questo metodo offre un'interfaccia utente basata sul browser per la configurazione della raccolta dell'inventario.

## <a name="before-you-begin"></a>Prima di iniziare

Se non si dispone di una sottoscrizione di Azure, [creare un account gratuito.](https://azure.microsoft.com/free/)

Questo articolo presuppone che sia disponibile una macchina virtuale in cui configurare la soluzione. Se non si ha una macchina virtuale di Azure, [crearne una](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale](https://portal.azure.com/)di Azure .

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Abilitare la raccolta dell'inventario dalla pagina delle risorse della macchina virtuale

1. Nel riquadro sinistro del portale di Azure selezionare **Macchine virtuali**.
2. Nell'elenco delle macchine virtuali selezionare una macchina virtuale.
3. In **Operazioni** scegliere **Inventario** dal menu **Risorsa**.
4. Selezionare un'area di lavoro di Log Analytics per archiviare i log dei dati.
    Se non sono disponibili aree di lavoro per l'area, viene chiesto se creare un'area di lavoro e un account di automazione predefiniti.
5. Per avviare il caricamento del computer, selezionare **Abilita**.

   ![Visualizzare le opzioni di onboarding](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Una barra di stato notifica che si sta abilitando la soluzione. Il processo potrebbe richiedere fino a 15 minuti, durante i quali è possibile chiudere la finestra oppure tenerla aperta per visualizzare una notifica quando la soluzione viene abilitata. È possibile monitorare lo stato della distribuzione dal riquadro delle notifiche.

   ![Visualizzare la soluzione di inventario immediatamente dopo l'onboarding](./media/automation-vm-inventory/inventory-onboarded.png)

Al termine della distribuzione, la barra di stato scompare. Il sistema sta ancora raccogliendo i dati di inventario, che quindi potrebbero non essere ancora visibili. La raccolta completa dei dati può richiedere 24 ore.

## <a name="configure-your-inventory-settings"></a>Configurare le impostazioni di inventario

Per impostazione predefinita, sono configurati per la raccolta il software, i servizi di Windows e i daemon Linux. Per raccogliere i dati di inventario dei file e del Registro di sistema di Windows, configurare le impostazioni della raccolta dell'inventario.

1. Nella visualizzazione **Inventario** selezionare il pulsante **Modifica impostazioni** nella parte superiore della finestra.
2. Per aggiungere una nuova impostazione della raccolta, passare alla categoria delle impostazioni che si vuole aggiungere selezionando le schede **Registro di sistema di Windows**, **File Windows** e **File Linux**.
3. Selezionare la categoria appropriata e fare clic su **Aggiungi** nella parte superiore della finestra.

Le tabelle seguenti forniscono informazioni sulle proprietà che possono essere configurate per le diverse categorie.

### <a name="windows-registry"></a>Registro di sistema di Windows

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata        |
|Nome elemento     | Nome descrittivo del file da rilevare        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file        |
|Chiave del Registro di sistema di Windows   | Percorso in cui cercare il file, ad esempio "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

### <a name="windows-files"></a>File Windows

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata        |
|Nome elemento     | Nome descrittivo del file da rilevare        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file        |
|Immettere il percorso     | Percorso in cui cercare il file, ad esempio "c:\temp\myfile.txt"

### <a name="linux-files"></a>File di Linux

|Proprietà  |Descrizione  |
|---------|---------|
|Attivato     | Determina se l'impostazione viene applicata        |
|Nome elemento     | Nome descrittivo del file da rilevare        |
|Gruppo     | Nome del gruppo per il raggruppamento logico dei file        |
|Immettere il percorso     | Percorso in cui cercare il file, ad esempio "/etc/*.conf"       |
|Tipo di percorso     | Tipo di elemento da rilevare. I valori possibili sono File e Directory        |
|Ricorsione     | Determina se viene usata la ricorsione per la ricerca dell'elemento da rilevare.        |
|Usa Sudo     | Questa impostazione determina se viene usato sudo per la ricerca dell'elemento.         |
|Collegamenti     | Questa impostazione determina come vengono gestiti i collegamenti simbolici durante l'attraversamento delle directory.<br> **Ignora** - Ignora i collegamenti simbolici e non include i file/directory a cui si fa riferimento<br>**Segui** : segue i collegamenti simbolici durante la ricorsione e include anche i file/directory a cui si fa riferimento<br>**Gestisci**: segue i collegamenti simbolici e consente la modifica del trattamento del contenuto restituito      |

## <a name="manage-machine-groups"></a>Gestire i gruppi di computer

Inventario consente di creare e visualizzare i gruppi di computer nei log di Monitoraggio di Azure.Inventory allows you to create and view machine groups in Azure Monitor logs. I gruppi di computer sono raccolte di computer definite da una query nei log di Monitoraggio di Azure.Machine groups are collections of machines defined by a query in Azure Monitor logs.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Per visualizzare i gruppi di computer, selezionare la scheda **Gruppi di computer** nella pagina Inventario.

![Visualizzare i gruppi di computer nella pagina Inventario](./media/automation-vm-inventory/inventory-machine-groups.png)

Selezionando un gruppo di computer nell'elenco, si apre la pagina Gruppi di computer. Questa pagina mostra i dettagli del gruppo di computer. Questi dettagli includono la query di Log Analytics usata per definire il gruppo. Nella parte inferiore della pagina è presente un elenco su più pagine dei computer che fanno parte di tale gruppo.

![Visualizzare la pagina del gruppo di computer](./media/automation-vm-inventory/machine-group-page.png)

Fare clic sul pulsante **+ Clona** per clonare il gruppo di computer. È necessario assegnare al gruppo un nuovo nome e un alias. In questa fase è possibile modificare la definizione. Dopo aver modificato la query, fare clic su **Convalida query** per visualizzare in anteprima i computer che verranno selezionati. Quando si è ottenuto il gruppo desiderato, fare clic su **Crea** per creare il gruppo di computer

Se si desidera creare un nuovo gruppo di computer, selezionare **Crea un gruppo**di computer . Questo pulsante apre la pagina **Crea un gruppo di computer** in cui è possibile definire il nuovo gruppo. Fare clic su **Crea** per creare il gruppo.

![Creare il nuovo gruppo di computer](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Disconnettere la macchina virtuale dalla gestione

Per rimuovere la macchina virtuale dalla gestione dell'inventario:

1. Nel riquadro sinistro del portale di Azure selezionare **Log Analytics** e quindi selezionare l'area di lavoro usata durante il caricamento della macchina virtuale.
2. Nella categoria **Origini dati dell'area di lavoro** nella finestra **Log Analytics** scegliere **Macchine virtuali** dal menu **Risorsa**.
3. Nell'elenco selezionare la macchina virtuale che si vuole disconnettere. La macchina virtuale avrà un segno di spunta verde accanto alla voce **Questa area di lavoro** nella colonna **Connessione OMS**.

   >[!NOTE]
   >OMS è ora indicato come log di Monitoraggio di Azure.OMS is now referred to as Azure Monitor logs.
   
4. Nella parte superiore della pagina successiva selezionare **Disconnetti**.
5. Nella finestra di conferma fare clic su **Sì**.
    Questa azione disconnette la macchina virtuale dalla gestione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla gestione delle modifiche nelle impostazioni dei file e del Registro di sistema nelle macchine virtuali, vedere [Rilevare le modifiche software nell'ambiente in uso con la soluzione di rilevamento modifiche](../log-analytics/log-analytics-change-tracking.md).
* Per informazioni sulla gestione degli aggiornamenti di Windows e dei pacchetti nelle macchine virtuali, vedere [Soluzione Gestione aggiornamenti in Azure](../operations-management-suite/oms-solution-update-management.md).

