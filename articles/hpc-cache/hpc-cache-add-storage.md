---
title: Aggiungere spazio di archiviazione a una cache HPC di Azure
description: Come definire le destinazioni di archiviazione in modo che la cache HPC di Azure possa usare il sistema NFS locale o i contenitori BLOB di Azure per l'archiviazione di file a lungo termine
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: v-erkel
ms.openlocfilehash: dde29d02f3dbf10ca068d6b3f1ef6c326c206370
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195044"
---
# <a name="add-storage-targets"></a>Aggiungere destinazioni di archiviazione

Le *destinazioni di archiviazione* sono archiviazione back-end per i file a cui si accede tramite una cache HPC di Azure. È possibile aggiungere l'archiviazione NFS (ad esempio un sistema hardware locale) o archiviare i dati nel BLOB di Azure.

È possibile definire fino a dieci destinazioni di archiviazione diverse per una cache. La cache presenta tutte le destinazioni di archiviazione in uno spazio dei nomi aggregato.

Tenere presente che le esportazioni di archiviazione devono essere accessibili dalla rete virtuale della cache. Per l'archiviazione hardware locale, potrebbe essere necessario configurare un server DNS in grado di risolvere i nomi host per l'accesso con archiviazione NFS. Per altre informazioni, vedere [accesso DNS](hpc-cache-prereqs.md#dns-access).

Aggiungere le destinazioni di archiviazione dopo la creazione della cache. La procedura è leggermente diversa a seconda che si stia aggiungendo un archivio BLOB di Azure o un'esportazione NFS. Di seguito sono riportati i dettagli per ognuno di essi.

## <a name="open-the-storage-targets-page"></a>Aprire la pagina Destinazioni di archiviazione

Dalla portale di Azure aprire l'istanza di cache e fare clic su **destinazioni di archiviazione** nella barra laterale sinistra. La pagina Destinazioni di archiviazione elenca tutte le destinazioni esistenti e fornisce un collegamento per aggiungerne una nuova.

![screenshot del collegamento destinazioni di archiviazione nella barra laterale, sotto l'intestazione Configura, che è tra le impostazioni delle intestazioni di categoria e il monitoraggio](media/hpc-cache-storage-targets-sidebar.png)

## <a name="add-a-new-azure-blob-storage-target"></a>Aggiungere una nuova destinazione di archiviazione BLOB di Azure

Una nuova destinazione di archiviazione BLOB necessita di un contenitore BLOB vuoto o di un contenitore popolato con i dati nel formato file system del cloud di cache HPC di Azure. Altre informazioni sul precaricamento di un contenitore BLOB in [spostare i dati nell'archivio BLOB di Azure](hpc-cache-ingest.md).

È possibile creare un nuovo contenitore da questa pagina immediatamente prima di aggiungerlo.

![screenshot della pagina Aggiungi destinazione di archiviazione, popolato con le informazioni per una nuova destinazione di archiviazione BLOB di Azure](media/hpc-cache-add-blob.png)

Per definire un contenitore BLOB di Azure, immettere queste informazioni.

* **Nome destinazione di archiviazione** : impostare un nome che identifichi la destinazione di archiviazione nella cache HPC di Azure.
* **Tipo di destinazione** : scegliere **BLOB**.
* **Account di archiviazione** : selezionare l'account che si vuole usare.

  Per accedere all'account di archiviazione, è necessario autorizzare l'istanza della cache, come descritto in [aggiungere i ruoli di accesso](#add-the-access-control-roles-to-your-account).

  Per informazioni sul tipo di account di archiviazione che è possibile usare, vedere [requisiti di archiviazione BLOB](hpc-cache-prereqs.md#blob-storage-requirements).

* **Contenitore di archiviazione** : selezionare il contenitore BLOB per la destinazione oppure fare clic su **Crea nuovo**.

  ![screenshot della finestra di dialogo per specificare il nome e il livello di accesso (privato) per il nuovo contenitore](media/add-blob-new-container.png)

* **Percorso dello spazio dei nomi virtuale** : impostare il percorso del file per il client per questa destinazione di archiviazione. Per ulteriori informazioni sulla funzionalità spazio dei nomi virtuale, vedere [configurare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .

Al termine, fare clic su **OK** per aggiungere la destinazione di archiviazione.

> [!NOTE]
> Se il firewall dell'account di archiviazione è impostato in modo da limitare l'accesso solo alle "reti selezionate", usare la soluzione alternativa temporanea documentata in [aggirare le impostazioni del firewall dell'account di archiviazione BLOB](hpc-cache-blob-firewall-fix.md).

### <a name="add-the-access-control-roles-to-your-account"></a>Aggiungere i ruoli di controllo di accesso all'account

Cache HPC di Azure usa il [controllo degli accessi in base al ruolo (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/index) per autorizzare il servizio cache ad accedere all'account di archiviazione per le destinazioni di archiviazione BLOB di Azure.

Il proprietario dell'account di archiviazione deve aggiungere in modo esplicito i ruoli collaboratore [account di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-contributor) e collaboratore [dati BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) per l'utente "HPC cache Resource Provider".

È possibile eseguire questa operazione in anticipo oppure facendo clic su un collegamento nella pagina in cui si aggiunge una destinazione di archiviazione BLOB. Tenere presente che possono essere necessari fino a cinque minuti per la propagazione delle impostazioni dei ruoli nell'ambiente Azure, quindi è necessario attendere alcuni minuti dopo aver aggiunto i ruoli prima di creare una destinazione di archiviazione.

Passaggi per aggiungere i ruoli RBAC:

1. Aprire la pagina **controllo di accesso (IAM)** per l'account di archiviazione. Il collegamento nella pagina **Aggiungi destinazione di archiviazione** apre automaticamente questa pagina per l'account selezionato.

1. **+** Fare clic su nella parte superiore della pagina e scegliere **Aggiungi un'assegnazione di ruolo**.

1. Selezionare il ruolo "collaboratore account di archiviazione" nell'elenco.

1. Nel campo **assegna accesso a** lasciare il valore predefinito selezionato ("Azure ad utente, gruppo o entità servizio").  

1. Nel campo **Seleziona** cercare "HPC".  Questa stringa deve corrispondere a un'entità servizio denominata "HPC cache Resource Provider". Fare clic su tale entità per selezionarla.

   > [!NOTE]
   > Se la ricerca di "HPC" non funziona, provare a usare la stringa "storagecache". Per gli utenti che hanno partecipato alle anteprime (prima di GA) potrebbe essere necessario usare il nome precedente per l'entità servizio.

1. Fare clic sul pulsante **Salva** nella parte inferiore.

1. Ripetere questo processo per assegnare il ruolo "collaboratore dati BLOB di archiviazione".  

![screenshot della GUI Aggiungi assegnazione ruolo](media/hpc-cache-add-role.png)

## <a name="add-a-new-nfs-storage-target"></a>Aggiungere una nuova destinazione di archiviazione NFS

Una destinazione di archiviazione NFS ha più campi di quelli della destinazione di archiviazione BLOB. Questi campi specificano come raggiungere l'esportazione dell'archiviazione e come memorizzare nella cache i dati in modo efficiente. Una destinazione di archiviazione NFS consente inoltre di creare più percorsi dello spazio dei nomi se l'host NFS dispone di più di un'esportazione disponibile.

![Screenshot della pagina Aggiungi destinazione di archiviazione con destinazione NFS definita](media/add-nfs-target.png)

> [!NOTE]
> Prima di creare una destinazione di archiviazione NFS, assicurarsi che il sistema di archiviazione sia accessibile dalla cache HPC di Azure e soddisfi i requisiti di autorizzazione. La creazione della destinazione di archiviazione avrà esito negativo se la cache non riesce ad accedere al sistema di archiviazione. Per informazioni dettagliate, vedere [requisiti di archiviazione NFS](hpc-cache-prereqs.md#nfs-storage-requirements) e [risolvere i problemi di configurazione NAS e di destinazione archiviazione NFS](troubleshoot-nas.md) .

Fornire queste informazioni per una destinazione di archiviazione supportata da NFS:

* **Nome destinazione di archiviazione** : impostare un nome che identifichi la destinazione di archiviazione nella cache HPC di Azure.

* **Tipo di destinazione** : scegliere **NFS**.

* Nome **host** : immettere l'indirizzo IP o il nome di dominio completo per il sistema di archiviazione NFS. Usare un nome di dominio solo se la cache ha accesso a un server DNS in grado di risolvere il nome.

* **Modello di utilizzo** : scegliere uno dei profili di caching dei dati in base al flusso di lavoro, descritto di seguito in [scegliere un modello di utilizzo](#choose-a-usage-model).

### <a name="nfs-namespace-paths"></a>Percorsi dello spazio dei nomi NFS

Una destinazione di archiviazione NFS può avere più percorsi virtuali, purché ogni percorso rappresenti un'esportazione o una sottodirectory diversa nello stesso sistema di archiviazione.

Creare tutti i percorsi da una destinazione di archiviazione.

È possibile [aggiungere e modificare i percorsi dello spazio dei nomi](hpc-cache-edit-storage.md) in una destinazione di archiviazione in qualsiasi momento.

Immettere questi valori per ogni percorso dello spazio dei nomi:

* **Percorso dello spazio dei nomi virtuale** : impostare il percorso del file per il client per questa destinazione di archiviazione. Per ulteriori informazioni sulla funzionalità spazio dei nomi virtuale, vedere [configurare lo spazio dei nomi aggregato](hpc-cache-namespace.md) .

* **Percorso di esportazione NFS** : immettere il percorso dell'esportazione NFS.

* **Percorso sottodirectory** : se si vuole montare una sottodirectory specifica dell'esportazione, immetterla qui. In caso contrario, lasciare vuoto questo campo.

Al termine, fare clic su **OK** per aggiungere la destinazione di archiviazione.

### <a name="choose-a-usage-model"></a>Scegliere un modello di utilizzo
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Quando si crea una destinazione di archiviazione che punta a un sistema di archiviazione NFS, è necessario scegliere il modello di utilizzo per tale destinazione. Questo modello determina il modo in cui i dati vengono memorizzati nella cache.

Sono disponibili tre opzioni:

* **Read Heavy, scritture rare** : usare questa opzione se si vuole velocizzare l'accesso in lettura ai file statici o modificati raramente.

  Questa opzione consente di memorizzare nella cache i file letti dai client, ma di passare immediatamente le scritture all'archiviazione back-end. I file archiviati nella cache non vengono mai confrontati con i file nel volume di archiviazione NFS.

  Non usare questa opzione se esiste il rischio che un file venga modificato direttamente nel sistema di archiviazione senza prima scriverlo nella cache. In tal caso, la versione memorizzata nella cache del file non verrà mai aggiornata con le modifiche apportate dal back-end e il set di dati può diventare incoerente.

* **Scritture superiori al 15%** : questa opzione consente di velocizzare le prestazioni di lettura e scrittura. Quando si usa questa opzione, tutti i client devono accedere ai file tramite la cache HPC di Azure anziché montare direttamente l'archiviazione back-end. I file memorizzati nella cache avranno modifiche recenti che non sono archiviate nel back-end.

  In questo modello di utilizzo i file nella cache non vengono controllati in base ai file nell'archiviazione back-end. Si presuppone che la versione memorizzata nella cache del file sia più aggiornata. Un file modificato nella cache viene scritto nel sistema di archiviazione back-end dopo che è stato inserito nella cache per un'ora senza ulteriori modifiche.

* I **client scrivono nella destinazione NFS, ignorando la cache** . scegliere questa opzione se i client nel flusso di lavoro scrivono i dati direttamente nel sistema di archiviazione senza prima scrivere nella cache. I file richiesti dai client vengono memorizzati nella cache, ma tutte le modifiche apportate ai file dal client vengono passate immediatamente al sistema di archiviazione back-end.

  Con questo modello di utilizzo, i file nella cache vengono spesso controllati rispetto alle versioni back-end per gli aggiornamenti. Questa verifica consente di modificare i file all'esterno della cache mantenendo la coerenza dei dati.

In questa tabella vengono riepilogate le differenze del modello di utilizzo:

| Modello di utilizzo | Modalità di memorizzazione nella cache | Verifica del back-end | Ritardo massimo write-back |
| ---- | ---- | ---- | ---- |
| Lettura di scritture complesse e non frequenti | Lettura | Never | nessuno |
| Scritture superiori al 15% | Lettura/Scrittura | Never | 1 ora |
| Client che ignorano la cache | Lettura | 30 secondi | nessuno |

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato le destinazioni di archiviazione, prendere in considerazione una delle seguenti attività:

* [Montare la cache HPC di Azure](hpc-cache-mount.md)
* [Spostare i dati nell'archivio BLOB di Azure](hpc-cache-ingest.md)

Se è necessario aggiornare le impostazioni, è possibile [modificare una destinazione di archiviazione](hpc-cache-edit-storage.md).
