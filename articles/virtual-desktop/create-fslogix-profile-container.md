---
title: FSLogix profile Containers NetApp desktop virtuale Windows-Azure
description: Come creare un contenitore di profili FSLogix usando Azure NetApp Files nel desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6a9f2c62d8e7f17f6ea8377982c79fef3dfbb97c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88002823"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>Creare un contenitore di profili con Azure NetApp Files e servizi di dominio Active Directory

È consigliabile usare i contenitori di profili FSLogix come soluzione di profilo utente per il [servizio desktop virtuale di Windows](overview.md). I contenitori di profili FSLogix archiviano un profilo utente completo in un singolo contenitore e sono progettati per eseguire il roaming dei profili in ambienti di elaborazione remota non permanenti come desktop virtuale di Windows. Quando si accede, il contenitore si connette dinamicamente all'ambiente di elaborazione usando un disco rigido virtuale (VHD) e un disco rigido virtuale Hyper-V supportati localmente (VHDX). Queste tecnologie avanzate di driver di filtro consentono al profilo utente di essere immediatamente disponibili e vengono visualizzate nel sistema esattamente come un profilo utente locale. Per altre informazioni sui contenitori di profili FSLogix, vedere [contenitori di profili FSLogix e file di Azure](fslogix-containers-azure-files.md).

È possibile creare contenitori di profili FSLogix usando [Azure NetApp files](https://azure.microsoft.com/services/netapp/), un servizio di piattaforma nativa di Azure facile da usare che consente ai clienti di effettuare rapidamente e in modo affidabile il provisioning di volumi SMB di livello aziendale per gli ambienti desktop virtuali Windows. Per ulteriori informazioni su Azure NetApp Files, vedere [che cos'è Azure NetApp files?](../azure-netapp-files/azure-netapp-files-introduction.md)

In questa guida viene illustrato come configurare un account di Azure NetApp Files e creare contenitori di profili FSLogix nel desktop virtuale di Windows.

Questo articolo presuppone che siano già stati configurati [pool host](create-host-pools-azure-marketplace.md) e che siano stati raggruppati in uno o più tenant nell'ambiente di desktop virtuale di Windows. Per informazioni su come configurare i tenant, vedere [creare un tenant in un desktop virtuale di Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) e [il post di Blog della community tecnica](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

Le istruzioni riportate in questa guida sono specifiche per gli utenti di desktop virtuali Windows. Per indicazioni più generali su come configurare Azure NetApp Files e creare contenitori di profili FSLogix all'esterno di desktop virtuale di Windows, vedere la [Guida introduttiva alla configurazione Azure NetApp files e creazione di un volume NFS](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>Questo articolo non illustra le procedure consigliate per la protezione dell'accesso alla condivisione Azure NetApp Files.

>[!NOTE]
>Se si sta cercando materiale di confronto sulle diverse opzioni di archiviazione del contenitore del profilo FSLogix in Azure, vedere [Opzioni di archiviazione per i contenitori di profili FSLogix](store-fslogix-profile.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di poter creare un contenitore di profili FSLogix per un pool host, è necessario:

- Configurare e configurare Desktop virtuale Windows
- Effettuare il provisioning di un pool host desktop virtuale Windows
- [Abilitare la sottoscrizione di Azure NetApp Files](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Configurare l'account di Azure NetApp Files

Per iniziare, è necessario configurare un account Azure NetApp Files.

1. Accedere al [portale di Azure](https://portal.azure.com). Assicurarsi che l'account disponga di autorizzazioni di collaboratore o di amministratore.

2. Selezionare l' **icona Azure cloud Shell** a destra della barra di ricerca per aprire Azure cloud Shell.

3. Quando Azure Cloud Shell è aperto, selezionare **PowerShell**.

4. Se è la prima volta che si usa Azure Cloud Shell, creare un account di archiviazione nella stessa sottoscrizione in cui si mantiene la Azure NetApp Files e il desktop virtuale di Windows.

   > [!div class="mx-imgBorder"]
   > ![La finestra dell'account di archiviazione con il pulsante Crea archiviazione nella parte inferiore della finestra evidenziata in rosso.](media/create-storage-button.png)

5. Una volta Azure Cloud Shell carica, eseguire i due cmdlet seguenti.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. Sul lato sinistro della finestra selezionare **tutti i servizi**. Immettere **Azure NetApp files** nella casella di ricerca visualizzata nella parte superiore del menu.

   > [!div class="mx-imgBorder"]
   > ![Screenshot di un utente che immette "Azure NetApp Files" nella casella di ricerca tutti i servizi. I risultati della ricerca mostrano la risorsa Azure NetApp Files.](media/azure-netapp-files-search-box.png)


7. Selezionare **Azure NetApp files** nei risultati della ricerca e quindi selezionare **Crea**.

8. Fare clic sul pulsante **Aggiungi**.
9. Quando viene visualizzata la scheda **nuovo account NetApp** , immettere i valori seguenti:

    - Per **nome**immettere il nome dell'account NetApp.
    - Per **Subscription (sottoscrizione**) selezionare la sottoscrizione per l'account di archiviazione configurato nel passaggio 4 dal menu a discesa.
    - Per **gruppo di risorse**selezionare un gruppo di risorse esistente dal menu a discesa o crearne uno nuovo selezionando **Crea nuovo**.
    - Per **località**selezionare l'area dell'account NetApp dal menu a discesa. Questa area deve essere la stessa area delle VM host della sessione.

   >[!NOTE]
   >Attualmente Azure NetApp Files non supporta il montaggio di un volume tra le aree.

10. Al termine, selezionare **Crea** per creare l'account NetApp.

## <a name="create-a-capacity-pool"></a>Creare un pool di capacità

Successivamente, creare un nuovo pool di capacità:

1. Passare al menu Azure NetApp Files e selezionare il nuovo account.
2. Nel menu account selezionare pool di **capacità** in servizio di archiviazione.
3. Selezionare **Aggiungi pool**.
4. Quando si apre la scheda **nuovo pool di capacità** , immettere i valori seguenti:

    - Per **nome**immettere un nome per il nuovo pool di capacità.
    - Per **livello di servizio**, selezionare il valore desiderato dal menu a discesa. Si consiglia **Premium** per la maggior parte degli ambienti.
       >[!NOTE]
       >L'impostazione Premium fornisce la velocità effettiva minima disponibile per un livello di servizio Premium, ovvero 256 MBps. Potrebbe essere necessario regolare questa velocità effettiva per un ambiente di produzione. La velocità effettiva finale è basata sulla relazione descritta in [limiti di velocità effettiva](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - Per **dimensione (tib)** immettere le dimensioni del pool di capacità più adatte alle proprie esigenze. La dimensione minima è 4 TiB.

5. Al termine, fare clic su **OK**.

## <a name="join-an-active-directory-connection"></a>Aggiungere una connessione Active Directory

Successivamente, è necessario partecipare a una connessione Active Directory.

1. Selezionare **Active Directory connessioni** nel menu sul lato sinistro della pagina, quindi selezionare il pulsante **Unisci** per aprire la pagina **Active Directory join** .

   > [!div class="mx-imgBorder"]
   > ![Screenshot del menu join Active Directory Connections.](media/active-directory-connections-menu.png)

2. Immettere i valori seguenti nella pagina **aggiungi Active Directory** per partecipare a una connessione:

    - Per **DNS primario**, immettere l'indirizzo IP del server DNS nell'ambiente in uso in grado di risolvere il nome di dominio.
    - Per **dominio**immettere il nome di dominio completo (FQDN).
    - Per il **prefisso server SMB (account computer)** immettere la stringa che si desidera aggiungere al nome dell'account del computer.
    - Per **username**(nome utente) immettere il nome dell'account con le autorizzazioni per l'aggiunta al dominio.
    - Per **password**immettere la password dell'account.

## <a name="create-a-new-volume"></a>Creare un nuovo volume

Sarà quindi necessario creare un nuovo volume.

1. Selezionare **volumi**, quindi selezionare **Aggiungi volume**.

2. Quando si apre la scheda **Crea volume** , immettere i valori seguenti:

    - Per **nome volume**immettere un nome per il nuovo volume.
    - Per **pool di capacità**, selezionare il pool di capacità appena creato dal menu a discesa.
    - Per **quota (GIB)** immettere le dimensioni del volume appropriate per l'ambiente.
    - In **rete virtuale**selezionare una rete virtuale esistente con connettività al controller di dominio dal menu a discesa.
    - In **subnet**selezionare **Crea nuovo**. Tenere presente che questa subnet verrà delegata a Azure NetApp Files.

3.  Selezionare **Avanti: protocollo \> \> ** per aprire la scheda protocollo e configurare i parametri di accesso al volume.

## <a name="configure-volume-access-parameters"></a>Configurare i parametri di accesso al volume

Dopo aver creato il volume, configurare i parametri di accesso al volume.

1.  Selezionare **SMB** come tipo di protocollo.
2.  In configurazione nel menu a discesa **Active Directory** selezionare la stessa directory in cui è stata originariamente stabilita [una connessione Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection). Tenere presente che è previsto un limite di una Active Directory per ogni sottoscrizione.
3.  Nella casella di testo **nome condivisione** immettere il nome della condivisione utilizzata dal pool di host della sessione e dai relativi utenti.

4.  Selezionare **Rivedi e crea** nella parte inferiore della pagina. Verrà visualizzata la pagina convalida. Dopo che il volume è stato convalidato correttamente, selezionare **Crea**.

5.  A questo punto, il nuovo volume inizierà a essere distribuito. Una volta completata la distribuzione, è possibile usare la condivisione Azure NetApp Files.

6.  Per visualizzare il percorso di montaggio, selezionare **Vai alla risorsa** e cercarlo nella scheda Panoramica.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della schermata di panoramica con una freccia rossa che punta al percorso di montaggio.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Configurare FSLogix nelle macchine virtuali (VM) host sessione

Questa sezione è basata sulla [creazione di un contenitore di profili per un pool host mediante una condivisione file](create-host-pools-user-profile.md).

1. [Scaricare il file con estensione zip dell'agente FSLogix](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) mentre si è ancora connessi alla macchina virtuale host sessione.

2. Decomprimere il file scaricato.

3. Nel file passare a **x64**  >  **releases** ed Run **FSLogixAppsSetup.exe**. Viene visualizzato il menu installazione.

4.  Se si dispone di un codice Product Key, immetterlo nella casella di testo Product Key.

5. Selezionare la casella di controllo accanto a accetto **i termini e le condizioni di licenza**.

6. Selezionare **Installa**.

7. Passare a **C: \\ programmi \\ FSLogix \\ app** per verificare che l'agente sia installato.

8. Dal menu Start eseguire **Regedit** come amministratore.

9. Passare a **Computer \\ HKEY_LOCAL_MACHINE \\ software \\ FSLogix**.

10. Creare una chiave denominata **profili**.

11.  Creare un valore denominato **Enabled** con un tipo di **REG_DWORD** impostato su un valore di dati pari a **1**.

12. Creare un valore denominato **VHDLocations** con un tipo **multistringa** e impostarne il valore di dati sull'URI per la condivisione di Azure NetApp files.

13. Creare un valore denominato **DeleteLocalProfileWhenVHDShouldApply** con un valore DWORD pari a 1 per evitare problemi con i profili locali esistenti prima di eseguire l'accesso.

     >[!WARNING]
     >Prestare attenzione durante la creazione del valore DeleteLocalProfileWhenVHDShouldApply. Quando il sistema profili FSLogix determina che un utente deve avere un profilo FSLogix, ma esiste già un profilo locale, il profilo locale verrà eliminato definitivamente dal contenitore del profilo. L'utente verrà quindi connesso con il nuovo profilo FSLogix.

## <a name="assign-users-to-session-host"></a>Assegna utenti a host sessione

1. Aprire **PowerShell ISE** come amministratore e accedere a desktop virtuale di Windows.

2. Eseguire i cmdlet seguenti:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Quando vengono richieste le credenziali, immettere le credenziali per l'utente con i ruoli autore tenant o proprietario Servizi Desktop remoto/collaboratore Servizi Desktop remoto sul tenant del desktop virtuale di Windows.

4. Eseguire i cmdlet seguenti per assegnare un utente a un gruppo di Desktop remoto:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Assicurarsi che gli utenti possano accedere alla condivisione file di Azure NetApp

1. Aprire il browser Internet e passare a <https://rdweb.wvd.microsoft.com/arm/webclient> .

2. Accedere con le credenziali di un utente assegnato al gruppo di Desktop remoto.

3. Una volta stabilita la sessione utente, accedere al portale di Azure con un account amministrativo.

4. Aprire **Azure NetApp files**, selezionare l'account Azure NetApp files, quindi selezionare **volumi**. Quando si apre il menu volumi, selezionare il volume corrispondente.

   > [!div class="mx-imgBorder"]
   > ![Una schermata dell'account NetApp configurato in precedenza nel portale di Azure con il pulsante volumi selezionati.](media/netapp-account.png)

5. Passare alla scheda **Panoramica** e verificare che il contenitore del profilo FSLogix stia usando lo spazio.

6. Connettersi direttamente a qualsiasi parte della macchina virtuale del pool host usando Desktop remoto e aprire **Esplora file.** Passare quindi al **percorso di montaggio** (nell'esempio seguente il percorso di montaggio è \\ \\ ANF-SMB-3863.gt1107.onmicrosoft.com \\ e-vol).

   All'interno di questa cartella deve essere presente un file VHD (o VHDX) del profilo come quello nell'esempio seguente.

   > [!div class="mx-imgBorder"]
   > ![Screenshot del contenuto della cartella nel percorso di montaggio. All'interno di un singolo file VHD denominato "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Passaggi successivi

È possibile usare i contenitori del profilo FSLogix per configurare una condivisione del profilo utente. Per informazioni su come creare condivisioni di profili utente con i nuovi contenitori, vedere [creare un contenitore di profili per un pool host usando una condivisione file](create-host-pools-user-profile.md).

È anche possibile creare una condivisione file File di Azure per archiviare il profilo FSLogix in. Per altre informazioni, vedere [creare una file di Azure condivisione file con un controller di dominio](create-file-share.md).