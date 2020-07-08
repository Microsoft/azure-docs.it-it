---
title: Soluzione VMware di Azure per CloudSimple-modello di autorizzazione cloud privato
description: Descrive il modello di autorizzazione, i gruppi e le categorie del cloud privato CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 28c4dc7831f97d66eb4d47f08e640344d5cca0d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77014947"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>Modello di autorizzazione del cloud privato CloudSimple di VMware vCenter

CloudSimple mantiene l'accesso amministrativo completo all'ambiente del cloud privato. A ogni cliente di CloudSimple vengono concessi privilegi amministrativi sufficienti per poter distribuire e gestire le macchine virtuali nell'ambiente.  Se necessario, è possibile inoltrare temporaneamente i privilegi per eseguire le funzioni amministrative.

## <a name="cloud-owner"></a>Proprietario del cloud

Quando si crea un cloud privato, viene creato un utente **CloudOwner** nel dominio Single Sign-on di vCenter, con accesso al **ruolo proprietario cloud** per gestire gli oggetti nel cloud privato. Questo utente può anche configurare origini di [identità vCenter](set-vcenter-identity.md)aggiuntive e altri utenti per il cloud privato vCenter.

> [!NOTE]
> L'utente predefinito per il cloud privato di CloudSimple è cloudowner@cloudsimple.local quando viene creato un cloud privato.

## <a name="user-groups"></a>Gruppi di utenti

Un gruppo denominato **cloud-Owner-Group** viene creato durante la distribuzione di un cloud privato. Gli utenti di questo gruppo possono amministrare diverse parti dell'ambiente vSphere nel cloud privato. A questo gruppo vengono assegnati automaticamente i privilegi del **ruolo proprietario cloud** e l'utente **CloudOwner** viene aggiunto come membro di questo gruppo.  CloudSimple crea gruppi aggiuntivi con privilegi limitati per semplificare la gestione.  È possibile aggiungere qualsiasi utente a questi gruppi creati in precedenza e i privilegi definiti di seguito vengono assegnati automaticamente agli utenti nei gruppi.

### <a name="pre-created-groups"></a>Gruppi creati in precedenza

| Nome gruppo | Scopo | Ruolo |
| -------- | ------- | ------ |
| Gruppo del proprietario cloud | I membri di questo gruppo dispongono di privilegi amministrativi per il cloud privato vCenter | [Cloud-Owner-Role](#cloud-owner-role) |
| Cloud-Global-cluster-admin-Group | I membri di questo gruppo hanno privilegi amministrativi sul cluster vCenter del cloud privato | [Cloud-cluster-admin-Role](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-admin-Group | I membri di questo gruppo possono gestire l'archiviazione nel cloud privato vCenter | [Cloud-storage-admin-Role](#cloud-storage-admin-role) |
| Cloud-Global-Network-Admin-Group | I membri di questo gruppo possono gestire la rete e i gruppi di porte distribuite nel cloud privato vCenter | [Cloud-Network-Admin-Role](#cloud-network-admin-role) |
| Cloud-Global-VM-admin-Group | I membri di questo gruppo possono gestire le macchine virtuali nel cloud privato vCenter | [Cloud-VM-admin-Role](#cloud-vm-admin-role) |

Per concedere a singoli utenti le autorizzazioni per la gestione del cloud privato, creare gli account utente da aggiungere ai gruppi appropriati.

> [!CAUTION]
> I nuovi utenti devono essere aggiunti solo a *cloud-Owner-Group*, *cloud-Global-cluster-admin-* Group, *cloud-Global-Storage-admin-Group*, *cloud-Global-Network-Admin-Group* o, *cloud-Global-VM-admin-Group*.  Gli utenti aggiunti al gruppo *Administrators* verranno rimossi automaticamente.  Solo gli account di servizio devono essere aggiunti al gruppo *Administrators* e gli account del servizio non devono essere usati per accedere all'interfaccia utente Web di vSphere.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Elenco dei privilegi vCenter per i ruoli predefiniti

### <a name="cloud-owner-role"></a>Cloud-Owner-Role

| **Categoria** | **Privilegio** |
|----------|-----------|
| **Avvisi** | Conferma avviso <br> Crea allarme <br> Disabilita azione allarme <br> Modifica allarme <br> Rimuovi allarme <br> Imposta stato allarme |
| **Autorizzazioni** | Modifica autorizzazione |
| **Raccolta contenuto** | Aggiungi elemento di libreria <br> Crea libreria locale <br> Crea libreria sottoscritta <br> Elimina elemento libreria <br> Elimina libreria locale <br> Elimina libreria sottoscritta <br> Scaricare i file <br> Rimuovi elemento libreria <br> Rimuovi libreria sottoscritta <br> Importa archiviazione <br> Informazioni sulla sottoscrizione Probe <br> Lettura archiviazione <br> Elemento della libreria di sincronizzazione <br> Libreria sottoscritta Sync <br> Introspezione del tipo <br> Aggiorna impostazioni di configurazione <br> Aggiornare i file <br> Aggiorna libreria <br> Aggiorna elemento libreria <br> Aggiorna libreria locale <br> Aggiorna libreria sottoscritta <br> Visualizza impostazioni di configurazione |
| **Operazioni di crittografia** | Aggiungi disco <br> Clone <br> Decrypt <br> Direct Access <br> Encrypt <br> Crittografa nuovo <br> Gestisci KMS <br> Gestire i criteri di crittografia <br> Gestire le chiavi <br> Migrazione <br> Ricrypt <br> Registra macchina virtuale <br> Registra host |
| **gruppo dvPort** | Create <br> Elimina <br> Modifica <br> Operazione sui criteri <br> Operazione di ambito |
| **Datastore** | Alloca spazio <br> Browse Datastore (Sfoglia archivio dati) <br> Configura archivio dati <br> Operazioni sui file di basso livello <br> Spostare l'archivio dati <br> Rimuovi archivio dati <br> Rimuovi file <br> Rinomina archivio dati <br> Aggiornare i file della macchina virtuale <br> Aggiornare i metadati della macchina virtuale |
| **Agent Manager ESX** | File di configurazione <br> Modifica <br> Visualizzazione |
| **Estensione** | Registra estensione <br> Annulla registrazione estensione <br> Aggiorna estensione |
| **Provider stats esterno**| Registrazione <br> Unregister  <br> Aggiornamento |
| **Cartella** | Creazione cartella <br> Elimina cartella <br> Sposta cartella <br> Rinomina cartella |
| **Globale** | Annulla attività <br> Pianificazione della capacità <br> Diagnostica <br> Metodi Disable <br> Metodi Enable <br> Tag globale <br> Salute <br> Licenze <br> Evento log <br> Gestire attributi personalizzati <br> Proxy <br> Azione script <br> Service Manager <br> Impostare un attributo personalizzato <br> Tag di sistema |
| **Provider di aggiornamento integrità** | Registrazione <br> Unregister  <br> Aggiornamento |
| **Configurazione > host** | Configurazione della partizione di archiviazione |
| **Inventario > host** | Modificare il cluster |
| **Codifica vSphere** | Assegnare o annullare l'assegnazione del tag vSphere <br> Crea tag vSphere <br> Crea categoria di tag vSphere <br> Elimina tag vSphere <br> Elimina categoria di tag vSphere <br> Modifica tag vSphere <br> Modifica categoria di tag vSphere <br> Modificare il campo UsedBy per Category <br> Modificare il campo UsedBy per il tag |
| **Network** | Assign network <br> Configurare <br> Sposta rete <br> Rimuovi |
| **Prestazioni** | Modificare gli intervalli |
| **Profilo host** | Visualizza |
| **Risorsa** | Applica suggerimento <br> Assegnare vApp al pool di risorse <br> Assign virtual machine to resource pool <br> Crea pool di risorse <br> Esegui la migrazione della macchina virtuale spenta <br> Esegui la migrazione della macchina virtuale accesa <br> Modificare il pool di risorse <br> Spostare il pool di risorse <br> Query vMotion <br> Rimuovi pool di risorse <br> Rinominare il pool di risorse |
| **Attività pianificata** | Creare attività <br> Modifica attività <br> Rimuovi attività <br> Esegui attività |
| **Sessioni** | Rappresenta utente <br> Message <br> Convalida sessione <br> Visualizzare e arrestare le sessioni |
| **Cluster di archivio dati** | Configurare un cluster di archivio dati |
| **Archiviazione basata su profili** | Aggiornamento dell'archiviazione basata sul profilo <br> Visualizzazione archiviazione basata su profili |
| **Viste di archiviazione** | Configurazione del servizio <br> Visualizza |
| **Attività** | Creare un'attività <br> Aggiorna attività |
| **Servizio di trasferimento**| Gestione <br> Monitorare |
| **vApp** | Aggiungi macchina virtuale <br> Assegnare il pool di risorse <br> Assegna vApp <br> Clone <br> Create <br> Elimina <br> Esportazione <br> Importa <br> Sposta <br> Spegnimento <br> Accendere <br> Rinomina <br> Sospendi <br> Unregister  <br> Visualizza ambiente OVF <br> configurazione dell'applicazione vApp <br> configurazione dell'istanza di vApp <br> configurazione di vApp managedBy <br> configurazione della risorsa vApp |
| **VRMPolicy** | VRMPolicy query <br> Aggiornare VRMPolicy |
| **Configurazione > macchina virtuale** | Aggiungi disco esistente <br> Aggiungi nuovo disco <br> Aggiungi o Rimuovi dispositivo <br> Avanzato <br> Cambia Conteggio CPU <br> Cambia risorsa <br> Configurare managedBy <br> Rilevamento modifiche disco <br> Lease del disco <br> Visualizzare le impostazioni di connessione <br> Estendi disco virtuale <br> Dispositivo USB host <br> Memory <br> Modificare le impostazioni del dispositivo <br> Compatibilità tolleranza di errore query <br> Eseguire query sui file di proprietà <br> Dispositivo RAW <br> Ricarica da percorso <br> Rimuovi disco <br> Rinomina <br> Reimposta informazioni Guest <br> Imposta annotazione <br> Impostazioni <br> Selezione host file <br> Imposta/Nascondi elemento padre fork <br> Sblocca macchina virtuale <br> Aggiornare la compatibilità delle macchine virtuali |
| **Macchine virtuali > operazioni Guest** | Modifica alias operazione Guest <br> Query alias operazione Guest <br> Modifiche alle operazioni Guest <br> Esecuzione del programma operativo guest <br> Query sull'operazione Guest |
| **Interazione > macchina virtuale** | Domanda di risposta <br> Operazione di backup nella macchina virtuale <br> Configurare supporti CD <br> Configurare supporti floppy <br> Interazione tra console <br> Crea screenshot <br> Deframmenta tutti i dischi <br> Connessione del dispositivo <br> Trascinamento della selezione <br> Gestione del sistema operativo guest per l'API VIX <br> Inserire codici di analisi HID USB <br> Sospendere o sospendere <br> Eseguire operazioni di cancellazione o compattazione <br> Spegnimento <br> Accendere <br> Registrare la sessione nella macchina virtuale <br> Riproduci sessione sulla macchina virtuale <br> Reset <br> Riprendi tolleranza di errore <br> Sospendi <br> Sospendi tolleranza di errore <br> Failover di test <br> Riavviare il test della macchina virtuale secondaria <br> Disattiva tolleranza di errore <br> Attiva tolleranza di errore <br> Installare gli strumenti VMware |
| **Inventario > macchina virtuale** | Crea da esistente <br> Create new <br> Sposta <br> Registrazione <br> Rimuovi <br> Unregister  |
| **Provisioning di > della macchina virtuale** | Consenti l'accesso al disco <br> Consenti accesso ai file <br> Allow read-only disk access <br> Consenti il download della macchina virtuale <br> Consenti caricamento file macchina virtuale <br> Clona modello <br> Clona macchina virtuale <br> Crea modello dalla macchina virtuale <br> Personalizza <br> Distribuire il modello <br> Contrassegna come modello <br> Contrassegna come macchina virtuale <br> Modificare la specifica di personalizzazione <br> Alza di livello i dischi <br> Leggi le specifiche di personalizzazione |
| **Configurazione del servizio > macchina virtuale** | Allow notifications <br> Consenti il polling delle notifiche degli eventi globali <br> Gestisci configurazioni del servizio <br> Modificare la configurazione del servizio <br> Configurazioni del servizio query <br> Lettura della configurazione del servizio |
| **Gestione snapshot > macchina virtuale** | Create snapshot <br> Rimuovi snapshot <br> Rinomina snapshot <br> Ripristina snapshot |
| **Macchina virtuale > la replica vSphere** | Configurare la replica <br> Gestire la replica <br> Monitorare la replica |
| **vService** | Crea dipendenza <br> Elimina dipendenza <br> Riconfigurare la configurazione della dipendenza <br> Aggiorna dipendenza |

### <a name="cloud-cluster-admin-role"></a>Cloud-cluster-admin-Role

| **Categoria** | **Privilegio** |
|----------|-----------|
| **Datastore** | Alloca spazio <br> Browse Datastore (Sfoglia archivio dati) <br> Configura archivio dati <br> Operazioni sui file di basso livello <br> Rimuovi archivio dati <br> Rinomina archivio dati <br> Aggiornare i file della macchina virtuale <br> Aggiornare i metadati della macchina virtuale |
| **Cartella** | Creazione cartella <br> Elimina cartella <br> Sposta cartella <br> Rinomina cartella |
| **Configurazione > host**  | Configurazione della partizione di archiviazione |
| **Codifica vSphere** | Assegnare o annullare l'assegnazione del tag vSphere <br> Crea tag vSphere <br> Crea categoria di tag vSphere <br> Elimina tag vSphere <br> Elimina categoria di tag vSphere <br> Modifica tag vSphere <br> Modifica categoria di tag vSphere <br> Modificare il campo UsedBy per Category <br> Modificare il campo UsedBy per il tag |
| **Network** | Assign network |
| **Risorsa** | Applica suggerimento <br> Assegnare vApp al pool di risorse <br> Assign virtual machine to resource pool <br> Crea pool di risorse <br> Esegui la migrazione della macchina virtuale spenta <br> Esegui la migrazione della macchina virtuale accesa <br> Modificare il pool di risorse <br> Spostare il pool di risorse <br> Query vMotion <br> Rimuovi pool di risorse <br> Rinominare il pool di risorse |
| **vApp** | Aggiungi macchina virtuale <br> Assegnare il pool di risorse <br> Assegna vApp <br> Clone <br> Create <br> Elimina <br> Esportazione <br> Importa <br> Sposta <br> Spegnimento <br> Accendere <br> Rinomina <br> Sospendi <br> Unregister  <br> Visualizza ambiente OVF <br> configurazione dell'applicazione vApp <br> configurazione dell'istanza di vApp <br> configurazione di vApp managedBy <br> configurazione della risorsa vApp |
| **VRMPolicy** | VRMPolicy query <br> Aggiornare VRMPolicy |
| **Configurazione > macchina virtuale** | Aggiungi disco esistente <br> Aggiungi nuovo disco <br> Aggiungi o Rimuovi dispositivo <br> Avanzato <br> Cambia Conteggio CPU <br> Cambia risorsa <br> Configurare managedBy <br> Rilevamento modifiche disco <br> Lease del disco <br> Visualizzare le impostazioni di connessione <br> Estendi disco virtuale <br> Dispositivo USB host <br> Memory <br> Modificare le impostazioni del dispositivo <br> Compatibilità tolleranza di errore query <br> Eseguire query sui file di proprietà <br> Dispositivo RAW <br> Ricarica da percorso <br> Rimuovi disco <br> Rinomina <br> Reimposta informazioni Guest <br> Imposta annotazione <br> Impostazioni <br> Selezione host file <br> Imposta/Nascondi elemento padre fork <br> Sblocca macchina virtuale <br> Aggiornare la compatibilità delle macchine virtuali |
| **Macchine virtuali > operazioni Guest** | Modifica alias operazione Guest <br> Query alias operazione Guest <br> Modifiche alle operazioni Guest <br> Esecuzione del programma operativo guest <br> Query sull'operazione Guest |
| **Interazione > macchina virtuale** | Domanda di risposta <br> Operazione di backup nella macchina virtuale <br> Configurare supporti CD <br> Configurare supporti floppy <br> Interazione tra console <br> Crea screenshot <br> Deframmenta tutti i dischi <br> Connessione del dispositivo <br> Trascinamento della selezione <br> Gestione del sistema operativo guest per l'API VIX <br> Inserire codici di analisi HID USB <br> Sospendere o sospendere <br> Eseguire operazioni di cancellazione o compattazione <br> Spegnimento <br> Accendere <br> Registrare la sessione nella macchina virtuale <br> Riproduci sessione sulla macchina virtuale <br> Reset <br> Riprendi tolleranza di errore <br> Sospendi <br> Sospendi tolleranza di errore <br> Failover di test <br> Riavviare il test della macchina virtuale secondaria <br> Disattiva tolleranza di errore <br> Attiva tolleranza di errore <br> Installare gli strumenti VMware
| **Inventario > macchina virtuale** | Crea da esistente <br> Create new <br> Sposta <br> Registrazione <br> Rimuovi <br> Unregister  |
| **Provisioning di > della macchina virtuale** | Consenti l'accesso al disco <br> Consenti accesso ai file <br> Allow read-only disk access <br> Consenti il download della macchina virtuale <br> Consenti caricamento file macchina virtuale <br> Clona modello <br> Clona macchina virtuale <br> Crea modello dalla macchina virtuale <br> Personalizza <br> Distribuire il modello <br> Contrassegna come modello <br> Contrassegna come macchina virtuale <br> Modificare la specifica di personalizzazione <br> Alza di livello i dischi  <br> Leggi le specifiche di personalizzazione |
| **Configurazione del servizio > macchina virtuale** | Allow notifications <br> Consenti il polling delle notifiche degli eventi globali <br> Gestisci configurazioni del servizio <br> Modificare la configurazione del servizio <br> Configurazioni del servizio query <br> Lettura della configurazione del servizio
| **Gestione snapshot > macchina virtuale** | Create snapshot <br> Rimuovi snapshot <br> Rinomina snapshot <br> Ripristina snapshot |
| **Macchina virtuale > la replica vSphere** | Configurare la replica <br> Gestire la replica <br> Monitorare la replica |
| **vService** | Crea dipendenza <br> Elimina dipendenza <br> Riconfigurare la configurazione della dipendenza <br> Aggiorna dipendenza |

### <a name="cloud-storage-admin-role"></a>Cloud-storage-admin-Role

| **Categoria** | **Privilegio** |
|----------|-----------|
| **Datastore** | Alloca spazio <br> Browse Datastore (Sfoglia archivio dati) <br> Configura archivio dati <br> Operazioni sui file di basso livello <br> Rimuovi archivio dati <br> Rinomina archivio dati <br> Aggiornare i file della macchina virtuale <br> Aggiornare i metadati della macchina virtuale |
| **Configurazione > host** | Configurazione della partizione di archiviazione |
| **Cluster di archivio dati** | Configurare un cluster di archivio dati |
| **Archiviazione basata su profili** | Aggiornamento dell'archiviazione basata sul profilo <br> Visualizzazione archiviazione basata su profili |
| **Viste di archiviazione** | Configurazione del servizio <br> Visualizza |

### <a name="cloud-network-admin-role"></a>Cloud-Network-Admin-Role

| **Categoria** | **Privilegio** |
|----------|-----------|
| **gruppo dvPort** | Create <br> Elimina <br> Modifica <br> Operazione sui criteri <br> Operazione di ambito |
| **Network** | Assign network <br> Configurare <br> Sposta rete <br> Rimuovi |
| **Configurazione > macchina virtuale** | Modificare le impostazioni del dispositivo |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-admin-Role

| **Categoria** | **Privilegio** |
|----------|-----------|
| **Datastore** | Alloca spazio <br> Browse Datastore (Sfoglia archivio dati) |
| **Network** | Assign network |
| **Risorsa** | Assign virtual machine to resource pool <br> Esegui la migrazione della macchina virtuale spenta <br> Esegui la migrazione della macchina virtuale accesa
| **vApp** | Esportazione <br> Importa |
| **Configurazione > macchina virtuale** | Aggiungi disco esistente <br> Aggiungi nuovo disco <br> Aggiungi o Rimuovi dispositivo <br> Avanzato <br> Cambia Conteggio CPU <br> Cambia risorsa <br> Configurare managedBy <br> Rilevamento modifiche disco <br> Lease del disco <br> Visualizzare le impostazioni di connessione <br> Estendi disco virtuale <br> Dispositivo USB host <br> Memory <br> Modificare le impostazioni del dispositivo <br> Compatibilità tolleranza di errore query <br> Eseguire query sui file di proprietà <br> Dispositivo RAW <br> Ricarica da percorso <br> Rimuovi disco <br> Rinomina <br> Reimposta informazioni Guest <br> Imposta annotazione <br> Impostazioni <br> Selezione host file <br> Imposta/Nascondi elemento padre fork <br> Sblocca macchina virtuale <br> Aggiornare la compatibilità delle macchine virtuali |
| **Macchine virtuali >operazioni Guest** | Modifica alias operazione Guest <br> Query alias operazione Guest <br> Modifiche alle operazioni Guest <br> Esecuzione del programma operativo guest <br> Query sull'operazione Guest    |
| **Interazione >macchina virtuale** | Domanda di risposta <br> Operazione di backup nella macchina virtuale <br> Configurare supporti CD <br> Configurare supporti floppy <br> Interazione tra console <br> Crea screenshot <br> Deframmenta tutti i dischi <br> Connessione del dispositivo <br> Trascinamento della selezione <br> Gestione del sistema operativo guest per l'API VIX <br> Inserire codici di analisi HID USB <br> Sospendere o sospendere <br> Eseguire operazioni di cancellazione o compattazione <br> Spegnimento <br> Accendere <br> Registrare la sessione nella macchina virtuale <br> Riproduci sessione sulla macchina virtuale <br> Reset <br> Riprendi tolleranza di errore <br> Sospendi <br> Sospendi tolleranza di errore <br> Failover di test <br> Riavviare il test della macchina virtuale secondaria <br> Disattiva tolleranza di errore <br> Attiva tolleranza di errore <br> Installare gli strumenti VMware |
| **Inventario >macchina virtuale** | Crea da esistente <br> Create new <br> Sposta <br> Registrazione <br> Rimuovi <br> Unregister  |
| **Provisioning di >della macchina virtuale** | Consenti l'accesso al disco <br> Consenti accesso ai file <br> Allow read-only disk access <br> Consenti il download della macchina virtuale <br> Consenti caricamento file macchina virtuale <br> Clona modello <br> Clona macchina virtuale <br> Crea modello dalla macchina virtuale <br> Personalizza <br> Distribuire il modello <br> Contrassegna come modello <br> Contrassegna come macchina virtuale <br> Modificare la specifica di personalizzazione <br> Alza di livello i dischi <br> Leggi le specifiche di personalizzazione |
| **Configurazione del servizio >macchina virtuale** | Allow notifications <br> Consenti il polling delle notifiche degli eventi globali <br> Gestisci configurazioni del servizio <br> Modificare la configurazione del servizio <br> Configurazioni del servizio query <br> Lettura della configurazione del servizio
| **Gestione snapshot >macchina virtuale** | Create snapshot <br> Rimuovi snapshot <br> Rinomina snapshot <br> Ripristina snapshot |
| **Macchina virtuale >la replica vSphere** | Configurare la replica <br> Gestire la replica <br> Monitorare la replica |
| **vService** | Crea dipendenza <br> Elimina dipendenza <br> Riconfigurare la configurazione della dipendenza <br> Aggiorna dipendenza |
