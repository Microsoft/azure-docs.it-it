---
title: Gestire i lab per le classi in Azure Lab Services | Microsoft Docs
description: Informazioni su come creare e configurare un lab per le classi, visualizzare tutti i lab per le classi, condividere il collegamento di registrazione con un utente del lab o eliminare un lab.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 46c53c99c12ade986ab913bf013b652a931a4d22
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257743"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Gestire i lab per le classi in Azure Lab Services 
Questo articolo descrive come creare ed eliminare un lab per le classi, nonché come visualizzare tutti i lab per le classi in un account lab. 

## <a name="prerequisites"></a>Prerequisiti
Per configurare un lab per le classi in un account del lab, è necessario essere un membro del ruolo **Autore di laboratori** nell'account del lab. L'account usato per creare un account del lab viene aggiunto automaticamente a questo ruolo. Un proprietario del lab può aggiungere altri utenti al ruolo Autore di laboratori seguendo i passaggi nell'articolo seguente: [Aggiungere un utente al ruolo di Autore di laboratori](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Creare un lab per le classi

1. Accedere al [sito Web di Azure Lab Services](https://labs.azure.com). Internet Explorer 11 non è ancora supportato. 
2. Selezionare **Accedi** e immettere le credenziali. Selezionare o immettere un **ID utente** appartenente al ruolo **Autore di laboratori** nell'account lab e quindi immettere la password. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Selezionare **New lab** (Nuovo lab). 
    
    ![Creare un lab per le classi](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. Nella finestra **New Lab** (Nuovo lab) eseguire queste operazioni: 
    1. Specificare un **nome** per il lab. 
    2. Selezionare le **dimensioni delle macchine virtuali** necessarie per la classe. Per l'elenco delle dimensioni disponibili, vedere la sezione [Dimensioni della macchina virtuale.](#vm-sizes) 
    3. Selezionare **l'immagine** della macchina virtuale che si vuole usare per il lab della classe. Se si seleziona un'immagine di Linux, viene visualizzata un'opzione per abilitare la connessione Desktop remoto. Per informazioni, vedere [Abilitare Connessione desktop remoto per Linux](how-to-enable-remote-desktop-linux.md).

        Se è stato eseguito l'accesso usando le credenziali del proprietario dell'account lab, verrà visualizzata un'opzione per abilitare altre immagini per il lab. Per ulteriori informazioni, consultate [Abilitare le immagini al momento della creazione del lab.](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)
    4. Esaminare il **prezzo orario totale** visualizzato nella pagina. 
    6. Selezionare **Salva**.

        ![Finestra New lab](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Viene visualizzata un'opzione per selezionare un percorso per il lab se l'account lab è stato configurato per [consentire al creatore del lab](allow-lab-creator-pick-lab-location.md) di selezionare l'opzione del percorso lab. 
4. Nella pagina **Virtual machine credentials** (Credenziali macchina virtuale) specificare le credenziali predefinite per tutte le VM del lab.
    1. Specificare il **nome dell'utente** per tutte le macchine virtuali del lab.
    2. Specificare la **password** per l'utente. 

        > [!IMPORTANT]
        > Prendere nota del nome utente e della password perché non verranno più visualizzati.
    3. Disabilitare **l'opzione Usa la stessa password per tutte le macchine virtuali** se si desidera che gli studenti impostino le proprie password. Questo passaggio è **facoltativo**. 

        Un insegnante può scegliere di usare la stessa password per tutte le macchine virtuali nel lab o consentire agli studenti di impostare password per le macchine virtuali. Per impostazione predefinita, questa impostazione è abilitata per tutte le immagini Windows e Linux ad eccezione di Ubuntu. Quando si seleziona **Ubuntu** VM, questa impostazione è disabilitata, pertanto agli studenti verrà richiesto di impostare una password quando accedono per la prima volta.  

        ![Finestra New lab](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Selezionare **quindi Avanti** nella pagina **Credenziali macchina virtuale.** 
5. Nella pagina Criteri lab eseguire la procedura seguente:On the **Lab policies** page, do the following steps:
    1. Immettere il numero di ore assegnate per ogni utente (**quota per ogni utente)** al di fuori dell'orario pianificato per il lab. 
    2. Per l'opzione **Arresto automatico delle macchine virtuali,** specificare se si vuole che la macchina virtuale venga arrestata automaticamente quando l'utente si disconnette. È anche possibile specificare per quanto tempo la macchina virtuale deve attendere la riconnessione dell'utente prima dell'arresto automatico. Per altre informazioni, vedere [Abilitare l'arresto automatico delle macchine virtuali alla disconnessione.](how-to-enable-shutdown-disconnect.md)
    3. Selezionare quindi **Fine**. 

        ![Quota per ogni utente](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Dovrebbe essere visualizzata la schermata seguente, che mostra lo stato di creazione della VM modello. La creazione del modello nel lab richiede fino a 20 minuti. 

    ![Stato di creazione della VM modello](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Nella pagina **Modello** eseguire la procedura seguente: Questi passaggi sono facoltativi per l'esercitazione. **optional**

    2. Connettersi alla macchina virtuale modello selezionando **Connect** (Connetti). Se si tratta di una macchina virtuale modello di Linux, scegliere se si vuole effettuare la connessione con SSH o RDP (se RDP è abilitato).
    1. Selezionare **Reset password** (Reimposta password) per reimpostare la password per la macchina virtuale. 
    1. Installare e configurare il software nella macchina virtuale modello. 
    1. **Arrestare** la macchina virtuale.  
    1. Immettere una **descrizione** per il modello.
10. Sulla barra degli strumenti della pagina **Template** (Modello) selezionare **Publish** (Pubblica). 

    ![Pulsante per la pubblicazione del modello](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > L'operazione di pubblicazione non può essere annullata. 
8. Nella pagina **Publish template** (Pubblica modello) immettere il numero di macchine virtuali da creare nel lab, quindi selezionare **Publish** (Pubblica). 

    ![Pubblicazione del modello: numero di VM](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Nella pagina viene visualizzato lo **stato di pubblicazione** del modello. Questo processo può richiedere fino a un'ora. 

    ![Pubblicazione del modello - stato](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Passare alla pagina **Pool di macchine virtuali** scegliendo Macchine virtuali dal menu sinistro oppure selezionando il riquadro Macchine virtuali. Verificare che vengano visualizzate macchine virtuali con lo stato **Non assegnato**. Queste macchine virtuali non sono ancora assegnate agli studenti. Devono essere nello stato **Arrestato**. In questa pagina è possibile avviare la macchina virtuale di uno studente, connettersi alla macchina virtuale, arrestare la macchina virtuale ed eliminare la macchina virtuale. In questa pagina è possibile avviare le macchine virtuali o consentire agli studenti di avviarle. 

    ![Macchine virtuali nello stato arrestato](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    In questa pagina vengono eseguite le attività seguenti, ma non completarle per questa esercitazione. Questi passaggi hanno uno scopo unicamente informativo: 
    
    1. Per cambiare la capacità del lab, ossia il numero di VM al suo interno, selezionare **Lab capacity** (Capacità del lab) sulla barra degli strumenti.
    2. Per avviare tutte le VM contemporaneamente, selezionare **Start all** (Avvia tutte) sulla barra degli strumenti. 
    3. Per avviare una specifica VM, selezionare la freccia in già in **Status** (Stato) e quindi selezionare **Start** (Avvia). Per avviare una VM, è anche possibile selezionarla nella prima colonna e quindi selezionare **Start** (Avvia) sulla barra degli strumenti.                

### <a name="vm-sizes"></a>Dimensioni delle macchine virtuali  

| Dimensione | Core | RAM | Descrizione | 
| ---- | ----- | --- | ----------- | 
| Piccolo | 2 | 3,5 GB | Questa dimensione è più adatta per la riga di comando, l'apertura del browser web, i server web a basso traffico, i database di piccole e medie dimensioni. |
| Media | 4 | 7 GB | Questa dimensione è più adatta per i database relazionali, la memorizzazione nella cache in memoria e l'analisi | 
| Medio (virtualizzazione annidata) | 4 | 16 GB | Questa dimensione è più adatta per i database relazionali, la memorizzazione nella cache in memoria e l'analisi. Questa dimensione supporta anche la virtualizzazione annidata. <p>Questa dimensione può essere usata in scenari in cui ogni studente ha bisogno di più macchine virtuali. Gli insegnanti possono usare la virtualizzazione annidata per configurare alcune macchine virtuali annidate di piccole dimensioni all'interno della macchina virtuale. </p> |
| large | 8 | 32 GB | Questa dimensione è più adatta per le applicazioni che richiedono CPU più veloci, migliori prestazioni del disco locale, database di grandi dimensioni, cache di memoria di grandi dimensioni. Questa dimensione supporta anche la virtualizzazione annidata |  
| GPU piccolo (visualizzazione) | 6 | 56 GB | Questa dimensione è più adatta per la visualizzazione remota, lo streaming, il gioco, la codifica utilizzando framework come OpenGL e DirectX. | 
| GPU piccola (Compute) | 6 | 56 GB | Questa dimensione è ideale per applicazioni ad alta intensità di elaborazione e di rete come l'intelligenza artificiale e le applicazioni di deep learning. | 
| GPU medio (visualizzazione) | 12 | 112 GB | Questa dimensione è più adatta per la visualizzazione remota, lo streaming, il gioco, la codifica utilizzando framework come OpenGL e DirectX. | 

> [!NOTE]
> Azure Lab Services installa e configura automaticamente i driver GPU necessari quando si crea un lab con immagini GPU.  

## <a name="view-all-classroom-labs"></a>Visualizzare tutti i lab per le classi
1. Accedere al [portale di Azure Lab Services](https://labs.azure.com).
2. Selezionare **Accedi**. Selezionare o immettere un **ID utente** appartenente al ruolo **Autore di laboratori** nell'account lab e quindi immettere la password. Azure Lab Services supporta gli account aziendali e gli account Microsoft. 
3. Confermare che sia possibile visualizzare tutti i lab dell'account del lab selezionato. Nel riquadro del lab vengono visualizzati il numero di macchine virtuali nel lab e la quota per ogni utente (al di fuori dell'ora pianificata).

    ![Tutti i lab](../media/how-to-manage-classroom-labs/all-labs.png)
3. Dall'elenco a discesa nella parte superiore, selezionare un account del lab differente. Tutti i lab dell'account selezionato sono visibili. 

## <a name="delete-a-classroom-lab"></a>Eliminare un lab per le classi
1. Nel riquadro del lab selezionare tre puntini (...) nell'angolo e quindi selezionare **Elimina**. 

    ![Pulsante Elimina](../media/how-to-manage-classroom-labs/delete-button.png)
3. Nella finestra di dialogo **Elimina lab** selezionare **Elimina** per continuare l'eliminazione. 

## <a name="switch-to-another-classroom-lab"></a>Passare a un altro lab per le classi
Per passare a un altro lab per le classi, in quello corrente selezionare l'elenco a discesa di lab nell'account del lab nella parte superiore.

![Selezionare il lab dall'elenco a discesa nella parte superiore](../media/how-to-manage-classroom-labs/switch-lab.png)

È anche possibile creare un nuovo lab usando **Il nuovo lab** in questo elenco a discesa. 

> [!NOTE]
> È anche possibile usare il modulo Az.LabServices PowerShell (anteprima) per gestire i lab. Per ulteriori informazioni, vedere la [home page di Az.LabServices in GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Per passare a un altro account lab, selezionare l'elenco a discesa accanto all'account lab e selezionare l'altro account lab. 

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Configurare e pubblicare modelli come proprietario](how-to-create-manage-template.md)
- [Configurare e controllare l'uso di un lab come proprietario](how-to-configure-student-usage.md)
- [Come utente di lab, accedere ai lab per le classi](how-to-use-classroom-lab.md)

