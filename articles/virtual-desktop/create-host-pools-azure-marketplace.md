---
title: Pool di host di Desktop virtuale Windows nel portale di Azure - Azure
description: Come creare un pool di host di Desktop virtuale Windows tramite il portale di Azure.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 03/10/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1003ab20f839469235d6bd7949ec3b2df4dd4f25
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447982"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Esercitazione: Creare un pool di host con il portale di Azure

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). Gli oggetti creati con Desktop virtuale Windows (versione classica) non possono essere gestiti con il portale di Azure.

I pool di host sono una raccolta di una o più macchine virtuali identiche all'interno di ambienti Desktop virtuale Windows. Ogni pool di host può contenere un gruppo di app con cui gli utenti possono interagire come farebbero in un desktop fisico.

Questo articolo illustra il processo di configurazione per la creazione di un pool di host per un ambiente Desktop virtuale Windows tramite il portale di Azure. Questo metodo fornisce un'interfaccia utente basata su browser per creare un pool di host in Desktop virtuale Windows, creare un gruppo di risorse con le macchine virtuali di una sottoscrizione di Azure, aggiungere tali macchine virtuali al dominio di Active Directory (AD) e registrare le macchine virtuali con Desktop virtuale Windows.

## <a name="prerequisites"></a>Prerequisiti

Per creare un pool di host, è necessario immettere i parametri seguenti:

- Nome dell'immagine della macchina virtuale
- Configurazione della macchina virtuale
- Proprietà di dominio e di rete
- Proprietà del pool di host di Desktop virtuale Windows

Sarà necessario anche conoscere quanto segue:

- Dove si trova l'origine dell'immagine da usare, ovvero se si tratta di un'immagine della raccolta di Azure o di un'immagine personalizzata.
- Credenziali di aggiunta al dominio.

Assicurarsi anche di aver registrato il provider di risorse Microsoft.DesktopVirtualization. Se non è già stato fatto, passare a **Sottoscrizioni**, selezionare il nome della sottoscrizione e quindi **Provider di risorse**. Cercare DesktopVirtualization, selezionare Microsoft.DesktopVirtualization e quindi Registra.

Quando si crea un pool di host di Desktop virtuale Windows con il modello di Azure Resource Manager, è possibile creare una macchina virtuale dalla raccolta di Azure, da un'immagine gestita o da un'immagine non gestita. Per altre informazioni su come creare immagini della macchina virtuale, vedere [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) e [Creare un'immagine gestita di una macchina virtuale generalizzata in Azure](../virtual-machines/windows/capture-image-resource.md).

Se non si ha già una sottoscrizione di Azure, assicurarsi di [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare a seguire queste istruzioni.

## <a name="begin-the-host-pool-setup-process"></a>Iniziare il processo di configurazione del pool di host

Per iniziare a creare il nuovo pool di host:

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com/).
   
   >[!NOTE]
   > Se si accede al portale US Gov, passare invece a [https://portal.azure.us/](https://portal.azure.us/).

2. Immettere **Desktop virtuale Windows** nella barra di ricerca, quindi individuare e selezionare **Desktop virtuale Windows** in Servizi.

3. Nella pagina di panoramica di **Desktop virtuale Windows** selezionare **Crea un pool di host**.

4. Nella scheda **Informazioni di base** selezionare la sottoscrizione corretta in Dettagli progetto.

5. Selezionare **Crea nuovo** per creare un nuovo gruppo di risorse o selezionare un gruppo di risorse esistente dall'elenco a discesa.

6. Immettere un nome univoco per il pool di host.

7. Nel campo Località selezionare l'area in cui si vuole creare il pool di host dall'elenco a discesa.

   L'area geografica di Azure associata alle aree selezionate è la posizione in cui verranno archiviati i metadati per questo pool di host e i relativi oggetti correlati. Assicurarsi di scegliere le aree all'interno dell'area geografica in cui si vuole archiviare i metadati del servizio.

     > [!div class="mx-imgBorder"]
     > ![Screenshot del portale di Azure che mostra il campo Località con la località Stati Uniti orientali selezionata. Accanto al campo viene visualizzato il testo "I metadati saranno archiviati in Stati Uniti orientali".](media/portal-location-field.png)
  
   >[!NOTE]
   > Se si vuole creare il pool host in [un'area supportata](data-locations.md) al di fuori degli Stati Uniti, è necessario registrare nuovamente il provider di risorse. Dopo la nuova registrazione, verranno visualizzate le altre aree nell'elenco a discesa per selezionare il percorso. Per informazioni su come ripetere la registrazione, vedere l'articolo sulla risoluzione dei problemi di [creazione del pool host](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects) .

8. In Tipo di pool di host selezionare se il pool di host sarà **Personale** o **In pool**.

    - Se si sceglie **Personale**, selezionare **Automatico** o **Diretto** nel campo Tipo di assegnazione.

      > [!div class="mx-imgBorder"]
      > ![Screenshot del menu a discesa del campo Tipo di assegnazione. L'utente ha selezionato Automatico.](media/assignment-type-field.png)

9.  Se si sceglie **In pool**, immettere le informazioni seguenti:

     - Per **Limite massimo sessioni** immettere il numero massimo di utenti per cui si vuole eseguire il bilanciamento del carico in un singolo host di sessione.
     - Per **Algoritmo di bilanciamento del carico** scegliere bilanciamento in ampiezza o bilanciamento del carico in profondità, in base al modello di utilizzo.

       > [!div class="mx-imgBorder"]
       > ![Screenshot del campo Tipo di assegnazione con "In pool" selezionato. L'utente passa il puntatore del mouse sopra Bilanciamento in ampiezza nel menu a discesa del bilanciamento del carico.](media/pooled-assignment-type.png)

10. Selezionare **Avanti: Macchine virtuali >** .

11. Se le macchine virtuali sono già state create e si vuole usarle con il nuovo pool di host, selezionare **No**, **Avanti: Area di lavoro >** e passare alla sezione [Informazioni sull'area di lavoro](#workspace-information). Se si vuole creare nuove macchine virtuali e registrarle nel nuovo pool di host, selezionare **Sì**.

Ora che è stata completata la prima parte, passare alla parte successiva del processo di configurazione in cui viene creata la macchina virtuale.

## <a name="virtual-machine-details"></a>Dettagli della macchina virtuale

Ora che è stata eseguita la prima parte, è necessario configurare la macchina virtuale.

Per configurare la macchina virtuale all'interno del processo di configurazione del pool di host:

1. In **Gruppo di risorse** scegliere il gruppo di risorse in cui si vogliono creare le macchine virtuali. Può essere un gruppo di risorse diverso rispetto a quello usato per il pool di host.

2. Successivamente, fornire un **Prefisso del nome** per denominare le macchine virtuali create dal processo di configurazione. Il suffisso verrà `-` con numeri a partire da 0.

3. Scegliere l'**area della macchina virtuale** in cui creare le macchine virtuali. Può essere la stessa area di quella selezionata per il pool di host o un'area diversa.
   
4. Scegliere quindi l'opzione disponibilità più adatta alle proprie esigenze. Per ulteriori informazioni sull'opzione più adatta alle proprie esigenze, vedere [Opzioni di disponibilità per le macchine virtuali in Azure](../virtual-machines/availability.md) e [le domande frequenti](faq.md#which-availability-option-is-best-for-me).
   
   > [!div class="mx-imgBorder"]
   > [Screenshot del menu a discesa zona di disponibilità. Viene evidenziata l'opzione "zona di disponibilità".](media/availability-zone.png)

5. Scegliere quindi l'immagine che deve essere usata per creare la macchina virtuale. È possibile scegliere **Raccolta** o **BLOB di archiviazione**.

    - Se si sceglie **Raccolta**, selezionare una delle immagini consigliate dal menu a discesa:

      - Windows 10 Enterprise multisessione, versione 1909
      - Windows 10 Enterprise multisessione, versione 1909 + Microsoft 365 Apps
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise multisessione, versione 2004
      - Windows 10 Enterprise multisessione, versione 2004 + Microsoft 365 Apps

      Se non viene visualizzata l'immagine desiderata, selezionare **Visualizza tutte le immagini**, che consente di selezionare un'altra immagine nella raccolta o un'immagine fornita da Microsoft e altri editori. Assicurarsi che l'immagine scelta sia una delle [Immagini del sistema operativo supportate](overview.md#supported-virtual-machine-os-images).

      > [!div class="mx-imgBorder"]
      > ![Screenshot del Marketplace con un elenco di immagini di Microsoft visualizzate.](media/marketplace-images.png)

      È anche possibile passare a **Elementi personali** e scegliere un'immagine personalizzata già caricata.

      > [!div class="mx-imgBorder"]
      > ![Screenshot della scheda Elementi personali.](media/my-items.png)

    - Se si sceglie **archiviazione BLOB**, è possibile usare la propria immagine di compilazione tramite Hyper-V o una macchina virtuale di Azure. È sufficiente immettere il percorso dell'immagine nel BLOB di archiviazione come URI.
   
   Il percorso dell'immagine è indipendente dall'opzione di disponibilità, ma la resilienza della zona dell'immagine determina se l'immagine può essere usata con la zona di disponibilità. Se si seleziona una zona di disponibilità durante la creazione dell'immagine, assicurarsi di usare un'immagine della raccolta con la resilienza della zona abilitata. Per altre informazioni sull'opzione di resilienza della zona da usare, vedere [le domande frequenti](faq.md#which-availability-option-is-best-for-me).

6. Successivamente, scegliere le **dimensioni della macchina virtuale** che si desidera utilizzare. È possibile mantenere le dimensioni predefinite così come sono oppure selezionare **Modifica dimensioni** per modificare le dimensioni. Se si seleziona **Modifica dimensioni**, nella finestra visualizzata scegliere le dimensioni della macchina virtuale appropriate al proprio carico di lavoro.

7. In **Numero di macchine virtuali** specificare il numero di macchine virtuali che si vuole creare per il pool di host.

    >[!NOTE]
    >Il processo di configurazione può creare fino a 400 macchine virtuali durante la configurazione del pool di host e ogni processo di configurazione della macchina virtuale crea quattro oggetti nel gruppo di risorse. Poiché il processo di creazione non controlla la quota della sottoscrizione, assicurarsi che il numero di macchine virtuali immesso rientri nei limiti delle API e delle macchine virtuali di Azure per il gruppo di risorse e la sottoscrizione. È possibile aggiungere altre macchine virtuali al termine della creazione del pool di host.

8. Scegliere il tipo di dischi del sistema operativo che si vuole usare per le macchine virtuali: SSD Standard, SSD Premium o HDD Standard.

9. In Rete e sicurezza selezionare la **rete virtuale** e la **subnet** in cui inserire le macchine virtuali create. Assicurarsi che la rete virtuale possa connettersi al controller di dominio, poiché sarà necessario aggiungere le macchine virtuali all'interno della rete virtuale al dominio. I server DNS della rete virtuale selezionata devono essere configurati per l'uso dell'IP del controller di dominio.

10. Seleziona il tipo di gruppo di sicurezza desiderato: **Di base**, **Avanzato** o **Nessuno**.

    Se si seleziona **Di base**, è necessario selezionare se si vuole che tutte le porte in ingresso siano aperte. Se si seleziona **Sì**, scegliere dall'elenco le porte standard a cui consentire le connessioni in ingresso.

    >[!NOTE]
    >Per una maggiore sicurezza, si consiglia di non aprire porte in ingresso pubbliche.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina del gruppo di sicurezza che mostra un elenco di porte disponibili in un menu a discesa.](media/available-ports.png)

    Se si sceglie **Avanzato**, selezionare un gruppo di sicurezza di rete esistente che è stato già configurato.

11. Successivamente, selezionare se si vuole che le macchine virtuali vengano aggiunte a un dominio e a un'unità organizzativa specifici. Se si sceglie **Sì**, specificare il dominio da aggiungere. Facoltativamente, è possibile aggiungere un'unità organizzativa specifica in cui si vuole inserire le macchine virtuali. Se si sceglie **No**, le macchine virtuali verranno aggiunte al dominio corrispondente al suffisso indicato in **Nome dell'entità utente di aggiunta a un dominio Active Directory**.

    - Quando si specifica un'unità organizzativa, assicurarsi di usare il percorso completo (nome distinto) e senza virgolette.

12. In account amministratore di dominio immettere le credenziali per la Dominio di Active Directory amministratore della rete virtuale selezionata. Per questo account non può essere abilitata l'autenticazione a più fattori (MFA). In caso di aggiunta a un dominio di Azure Active Directory Domain Services (Azure AD DS), è necessario che l'account faccia parte del gruppo Administrators di Azure AD DC e che la password dell'account funzioni in Azure AD DS.

13. Selezionare **Avanti: Area di lavoro >** .

A questo punto, è possibile iniziare la fase successiva della configurazione del pool di host, ovvero la registrazione del gruppo di app in un'area di lavoro.

## <a name="workspace-information"></a>Informazioni sull'area di lavoro

Per impostazione predefinita, il processo di configurazione del pool di host crea un gruppo di applicazioni desktop. Affinché il pool di host funzioni come previsto, è necessario pubblicare questo gruppo di app per utenti o gruppi di utenti ed è necessario registrare il gruppo di app in un'area di lavoro.

Per registrare il gruppo di app desktop in un'area di lavoro:

1. Selezionare **Sì**.

   Se si seleziona **No**, è possibile registrare il gruppo di app in un secondo momento, ma è consigliabile eseguire la registrazione dell'area di lavoro appena possibile, in modo che il pool di host funzioni correttamente.

2. Scegliere quindi se si vuole creare una nuova area di lavoro o selezionare un'area di lavoro esistente. Sarà possibile registrare il gruppo di app solo nelle aree di lavoro create nella stessa posizione del pool di host.

3. Facoltativamente, è possibile selezionare **Avanti: Tag >** .

    Qui è possibile aggiungere i tag per raggruppare gli oggetti con i metadati per semplificare le operazioni degli amministratori.

4. Al termine, selezionare **Rivedi e crea**.

     >[!NOTE]
     >Il processo di convalida di verifica e creazione non controlla se la password soddisfa gli standard di sicurezza o se l'architettura è corretta, quindi è necessario verificare eventuali problemi con uno di questi elementi.

5. Esaminare le informazioni sulla distribuzione per verificare che tutto sia corretto. Al termine, selezionare **Crea**. Viene avviato il processo di distribuzione, che crea gli oggetti seguenti:

     - Nuovo pool di host.
     - Gruppo di app desktop.
     - Un'area di lavoro, se si è scelto di crearla.
     - Se si sceglie di registrare il gruppo di app desktop, la registrazione verrà completata.
     - Macchine virtuali, se si è scelto di crearle, che vengono aggiunte al dominio e registrate con il nuovo pool di host.
     - Un collegamento di download per un modello di Azure Resource Manager in base alla configurazione.

Dopo questa verifica, la procedura è terminata.

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Eseguire il modello di Resource Manager per effettuare il provisioning di un nuovo pool di host

Se invece si preferisce usare un processo automatizzato, [scaricare il modello di Azure Resource Manager](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) per effettuare il provisioning del nuovo pool host.

>[!NOTE]
>Se si usa un processo automatizzato per creare l'ambiente, è necessaria la versione più recente del file JSON di configurazione. Il file JSON è disponibile [qui](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list).

## <a name="next-steps"></a>Passaggi successivi

Dopo la creazione del pool di host è possibile popolarlo con i programmi RemoteApp. Per altre informazioni su come gestire le app in Desktop virtuale Windows, passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Esercitazione: Gestire i gruppi di app](./manage-app-groups.md)
