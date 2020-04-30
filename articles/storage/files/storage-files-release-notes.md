---
title: Note sulla versione dell'agente Sincronizzazione file di Azure | Microsoft Docs
description: Note sulla versione dell'agente Sincronizzazione file di Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 82d8c8ca94f3d0f4289902ae40928ff507e2d1ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81460665"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Note sulla versione dell'agente Sincronizzazione file di Azure
Sincronizzazione file di Azure consente di centralizzare le condivisioni file dell'organizzazione in File di Azure senza rinunciare alla flessibilità, alle prestazioni e alla compatibilità di un file server locale. Le installazioni Windows Server vengono trasformate in una cache rapida della condivisione file di Azure. Per accedere ai dati in locale, è possibile usare qualsiasi protocollo disponibile in Windows Server, inclusi SMB, NFS e FTPS. Si può usare qualsiasi numero di cache in tutto il mondo.

Questo articolo illustra le note sulla versione per le versioni supportate dell'agente Sincronizzazione file di Azure.

## <a name="supported-versions"></a>Versioni supportate
L'agente Sincronizzazione file di Azure supporta le versioni seguenti:

| Attività cardine | Numero di versione dell'agente | Data di rilascio | Stato |
|----|----------------------|--------------|------------------|
| Versione V10- [KB4522359](https://support.microsoft.com/en-us/help/4522409/azure-file-sync-agent-v10-release-march-2020)| 10.0.0.0 | 9 aprile 2020 | In corso |
| Aggiornamento cumulativo di dicembre 2019- [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 dicembre 2019 | Supportato |
| Versione v9- [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 dicembre 2019 | Supportato |
| Versione V8- [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 ottobre 2019 | Supportato |
| Aggiornamento cumulativo di luglio 2019- [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 luglio 2019 | Supportato |
| Aggiornamento cumulativo di luglio 2019- [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 luglio 2019 | Supportato |
| Versione v7- [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 giugno 2019 | Supportato |
| Aggiornamento cumulativo di giugno 2019- [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 giugno 2019 | Supportato-la versione dell'agente scadrà il 21 aprile 2020 |
| Aggiornamento cumulativo di giugno 2019- [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 giugno, 2019 | Supportato-la versione dell'agente scadrà il 21 aprile 2020 |
| Aggiornamento cumulativo di maggio 2019- [KB4489737](https://support.microsoft.com/help/4489737)| versione 6.1.0.0 | 7 maggio 2019 | Supportato-la versione dell'agente scadrà il 21 aprile 2020 |
| Versione V6- [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 aprile 2019 | Supportato-la versione dell'agente scadrà il 21 aprile 2020 |
| Versione V5 | 5.0.2.0-5.2.0.0 | N/D | Non supportato-le versioni dell'agente scadono il 18 marzo 2020 |
| Versione v4 | 4.0.1.0 - 4.3.0.0 | N/D | Non supportato-le versioni dell'agente sono scadute il 6 novembre 2019 |
| Versione V3 | 3.1.0.0-3.4.0.0 | N/D | Non supportato-le versioni dell'agente scadono il 19 agosto 2019 |
| Agenti pre-GA | 1.1.0.0 - 3.0.13.0 | N/D | Non supportato: versione dell'agente scaduta il 1° ottobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Criteri di aggiornamento dell'agente Sincronizzazione file di Azure
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10000"></a>Versione dell'agente 10.0.0.0
Le note sulla versione seguenti sono relative alla versione 10.0.0.0 dell'agente Sincronizzazione file di Azure (rilasciata il 9 aprile 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Miglioramento dello stato di sincronizzazione nel portale
    - Con la versione dell'agente V10, il portale di Azure inizierà a visualizzare il tipo di sessione di sincronizzazione in esecuzione. ad esempio download iniziale, download normale, richiamo in background (casi di ripristino di emergenza veloce) e simile. 

- Miglioramento dell'esperienza del portale di suddivisione in livelli cloud
    - Se si dispone di file che non riescono a eseguire il livello o richiamare, è ora possibile visualizzare gli errori di suddivisione in livelli nelle proprietà dell'endpoint server.
    - Per un endpoint server sono disponibili informazioni aggiuntive sulla suddivisione in livelli nel cloud:
        - Dimensioni cache locale
        - Efficienza di utilizzo della cache
        - Dettagli dei criteri di suddivisione in livelli nel cloud: dimensioni del volume, spazio disponibile corrente o data e ora dell'ultimo accesso del file meno recente nella cache locale.
    - Queste modifiche vengono fornite nella portale di Azure subito dopo la versione iniziale dell'agente V10.

- Supporto per lo stato di trasferimento del servizio di sincronizzazione archiviazione e/o dell'account di archiviazione in un tenant di Azure Active Directory (AAD) diverso
    - Sincronizzazione file di Azure supporta ora lo stato di trasferimento del servizio di sincronizzazione archiviazione e/o dell'account di archiviazione in un gruppo di risorse, una sottoscrizione o un tenant Azure AD diverso.
    
- Miglioramenti delle prestazioni e dell'affidabilità varie
    - Il rilevamento delle modifiche nella condivisione file di Azure potrebbe non riuscire se sono configurate le regole della rete virtuale (VNET) e del firewall nell'account di archiviazione.
    - Ridotto consumo di memoria associato al richiamo. 
    - Miglioramento delle prestazioni quando si usa il cmdlet [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .
    - Altri miglioramenti dell'affidabilità. 
    
### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione di nano server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di Sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può causare risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) per avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Quando si crea l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione devono trovarsi nello stesso tenant Azure AD. Una volta creato l'endpoint cloud, il servizio di sincronizzazione archiviazione e l'account di archiviazione possono essere spostati in tenant Azure AD diversi.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-9100"></a>Versione dell'agente 9.1.0.0
Le note sulla versione seguenti sono relative alla versione 9.1.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 12 dicembre 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 9.0.0.0.

Problema risolto in questa versione:  
- La sincronizzazione ha esito negativo e si verifica uno dei seguenti errori dopo l'aggiornamento a Sincronizzazione file di Azure Agent versione 9,0:
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Versione dell'agente 9.0.0.0
Le note sulla versione seguenti sono relative alla versione 9.0.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato il 2 dicembre 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per il ripristino self-service
    - Ora gli utenti possono ripristinare i file usando la funzionalità della versione precedente. Prima della versione v9, la funzionalità versione precedente non era supportata nei volumi in cui è stata abilitata la suddivisione in livelli nel cloud. Questa funzionalità deve essere abilitata separatamente per ogni volume, in cui è abilitato un endpoint con suddivisione in livelli nel cloud. Per altre informazioni, vedere  
[Ripristino self-service tramite versioni precedenti e VSS (servizio Copia Shadow del volume)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Supporto per dimensioni di condivisione file più grandi 
    - Sincronizzazione file di Azure supporta ora fino a 64TiB e 100 milioni file in un singolo spazio dei nomi di sincronizzazione.  
 
- Supporto della deduplicazione dati nel server 2019 
    - La deduplicazione dati è ora supportata con la suddivisione in livelli cloud abilitata in Windows Server 2019. Per supportare la deduplicazione dei dati nei volumi con suddivisione in livelli nel cloud, è necessario installare Windows Update [KB4520062](https://support.microsoft.com/help/4520062) . 
 
- Dimensioni minime ottimizzate per un file nel livello 
    - La dimensione minima del file per un file a livello è ora basata sulle dimensioni del cluster file system (raddoppiare le dimensioni del cluster file system). Per impostazione predefinita, ad esempio, la dimensione del cluster file system NTFS è 4KB, la dimensione minima risultante per un file a livello è 8KB. 
 
- Cmdlet di test della connettività di rete 
    - Come parte della configurazione di Sincronizzazione file di Azure, è necessario contattare più endpoint di servizio. Ognuno ha il proprio nome DNS che deve essere accessibile al server. Questi URL sono specifici anche per l'area in cui un server è registrato. Dopo la registrazione di un server, è possibile usare il cmdlet di test di connettività (PowerShell e utilità di registrazione del server) per testare le comunicazioni con tutti gli URL specifici del server. Questo cmdlet consente di risolvere i problemi quando la comunicazione incompleta impedisce al server di lavorare completamente con Sincronizzazione file di Azure e può essere usata per ottimizzare le configurazioni del proxy e del firewall.  
 
        Per eseguire il test della connettività di rete, eseguire i comandi di PowerShell seguenti: 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Rimuovi miglioramento endpoint server quando è abilitata la suddivisione in livelli cloud 
    - Come prima, la rimozione di un endpoint server non comporta la rimozione di file nella condivisione file di Azure. Tuttavia, il comportamento per i reparse point nel server locale è stato modificato. I punti di analisi (puntatori a file che non sono locali sul server) vengono eliminati quando si rimuove un endpoint server. I file completamente memorizzati nella cache rimarranno nel server. Questo miglioramento è stato effettuato per impedire la rimozione di un endpoint server da [file a livelli orfani](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) . Se l'endpoint server viene ricreato, i reparse point per i file a livelli verranno ricreati nel server.  
 
- Miglioramenti dell'affidabilità e delle prestazioni 
    - Errori di richiamo ridotti. La dimensione di richiamo viene ora regolata automaticamente in base alla larghezza di banda di rete. 
    - Miglioramento delle prestazioni di download quando si aggiunge un nuovo server a un gruppo di sincronizzazione. 
    - Riduzione dei file non sincronizzati a causa di conflitti di vincolo. 
    - I file hanno esito negativo o vengono richiamati in modo imprevisto in determinati scenari se il percorso dell'endpoint server è un punto di montaggio del volume.
    
### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione di nano server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di Sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può causare risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili. Per ulteriori informazioni, vedere [file a livelli non accessibili nel server dopo l'eliminazione di un endpoint server](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) per avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Se il file Pagefile. sys si trova in un volume in cui è abilitata la suddivisione in livelli nel cloud, i file potrebbero non riuscire a livello. Il file Pagefile. sys deve trovarsi in un volume in cui è disabilitata la suddivisione in livelli nel cloud.

## <a name="agent-version-8000"></a>Versione dell'agente 8.0.0.0
Le note sulla versione seguenti sono relative alla versione 8.0.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato l'8 ottobre 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Miglioramenti delle prestazioni di ripristino
    - Tempi di ripristino più rapidi per il ripristino eseguito tramite backup di Azure. I file ripristinati vengono sincronizzati di nuovo con Sincronizzazione file di Azure server molto più velocemente. 
- Miglioramento dell'esperienza del portale di suddivisione in livelli cloud  
    - Se sono presenti file a livelli che non possono essere richiamati, è ora possibile visualizzare gli errori di richiamo nelle proprietà dell'endpoint server. Inoltre, l'integrità dell'endpoint server ora Visualizza un errore e i passaggi di mitigazione se il driver del filtro di suddivisione in livelli cloud non è caricato nel server.
- Installazione più semplice dell'agente
    - Il modulo Az\AzureRM di PowerShell non è più necessario per registrare il server rendendo più semplice e veloce l'installazione.
- Miglioramenti delle prestazioni e dell'affidabilità varie

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione di nano server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di Sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può causare risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili. Per ulteriori informazioni, vedere [file a livelli non accessibili nel server dopo l'eliminazione di un endpoint server](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) per avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-7200"></a>Versione dell'agente 7.2.0.0
Le note sulla versione seguenti sono relative alla versione 7.2.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 24 luglio 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 7.0.0.0.

Elenco dei problemi risolti in questa versione:  
- L'agente di sincronizzazione archiviazione (FileSyncSvc) si arresta in modo anomalo se la configurazione del proxy è null.
- L'endpoint server avvierà BCDR (Error 0x80c80257-ECS_E_BCDR_IN_PROGRESS) se più endpoint nel server hanno lo stesso nome.
- Miglioramenti dell'affidabilità di suddivisione in livelli nel cloud.

## <a name="agent-version-7100"></a>Versione dell'agente 7.1.0.0
Le note sulla versione seguenti sono relative alla versione 7.1.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 12 luglio 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 7.0.0.0.

Elenco dei problemi risolti in questa versione:  
- L'accesso o l'esplorazione di una posizione di endpoint server tramite SMB è lento in Windows Server 2012 R2. 
- Maggiore utilizzo della CPU dopo l'installazione dell'agente Sincronizzazione file di Azure V6.
- Miglioramenti della telemetria di suddivisione in livelli nel cloud.
- Vari miglioramenti dell'affidabilità per cloud a livelli e sincronizzazione.

## <a name="agent-version-7000"></a>Versione dell'agente 7.0.0.0
Le note sulla versione seguenti sono relative alla versione 7.0.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato il 19 giugno 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per dimensioni di condivisione file più grandi
    - Con l'anteprima delle condivisioni file di Azure di dimensioni maggiori, vengono aumentati anche i limiti di supporto per sincronizzazione file. In questo primo passaggio, Sincronizzazione file di Azure supporta ora fino a 25 TB e 50 milioni di file in un singolo spazio dei nomi di sincronizzazione. Per richiedere l'anteprima della condivisione file di grandi dimensioni, compilare il https://aka.ms/azurefilesatscalesurveymodulo. 
- Supporto per l'impostazione del firewall e della rete virtuale negli account di archiviazione
    - Sincronizzazione file di Azure supporta ora l'impostazione del firewall e della rete virtuale negli account di archiviazione. Per configurare la distribuzione in modo che funzioni con l'impostazione firewall e rete virtuale, vedere [configurare le impostazioni del firewall e della rete virtuale](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- Cmdlet di PowerShell per sincronizzare immediatamente i file modificati nella condivisione file di Azure
    - Per sincronizzare immediatamente i file modificati nella condivisione file di Azure, è possibile usare il cmdlet di PowerShell Invoke-AzStorageSyncChangeDetection per avviare manualmente il rilevamento delle modifiche nella condivisione file di Azure. Questo cmdlet è destinato agli scenari in cui alcuni tipi di processi automatici apportano modifiche alla condivisione file di Azure o le modifiche vengono eseguite da un amministratore, ad esempio lo spostamento di file e directory nella condivisione. Per le modifiche degli utenti finali, è consigliabile installare l'agente di Sincronizzazione file di Azure in una macchina virtuale IaaS e fare in modo che gli utenti finali accedano alla condivisione file tramite la macchina virtuale IaaS. In questo modo tutte le modifiche si sincronizzano rapidamente con altri agenti senza la necessità di usare il cmdlet Invoke-AzStorageSyncChangeDetection. Per altre informazioni, vedere la documentazione di [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) .
- Miglioramento dell'esperienza del portale se si riscontrano file che non eseguono la sincronizzazione
    - Se sono presenti file che non riescono a eseguire la sincronizzazione, ora si differenziano gli errori temporanei e permanenti nel portale. Gli errori temporanei si risolvono in genere senza richiedere l'intervento dell'amministratore. Un file attualmente in uso, ad esempio, non verrà sincronizzato fino alla chiusura dell'handle di file. Per gli errori persistenti, viene ora visualizzato il numero di file interessati da ogni errore. Il numero di errori persistente viene inoltre visualizzato nella colonna file non sincronizzati di tutti gli endpoint server in un gruppo di sincronizzazione.
- Miglioramento del ripristino a livello di file di backup di Azure
    - I singoli file ripristinati con backup di Azure vengono ora rilevati e sincronizzati con l'endpoint server più velocemente.
- Miglioramento dell'affidabilità del cmdlet di richiamo a livelli cloud 
    - Il cmdlet Invoke-StorageSyncFileRecall consente ora ai clienti di specificare il numero di tentativi per file e il ritardo dei tentativi per file Analogamente a Robocopy. In precedenza, questo cmdlet richiamerebbe tutti i file a livelli in un percorso specifico in ordine casuale. Con il parametro New-Order, questo cmdlet richiamerà prima i dati più importanti e soddisferà i criteri di suddivisione in livelli nel cloud (arrestare il richiamo se i criteri di data sono soddisfatti o lo spazio disponibile del volume viene raggiunto, a seconda di quale evento si verifica per primo).
- Supporto solo per TLS 1,2 (TLS 1,0 e 1,1 è disabilitato)
    - Sincronizzazione file di Azure supporta ora l'uso di TLS 1,2 solo su server con TLS 1,0 e 1,1 disabilitato. Prima di questo miglioramento, la registrazione del server avrebbe esito negativo se TLS 1,0 e 1,1 è stato disabilitato nel server.
- Miglioramenti delle prestazioni e dell'affidabilità per la sincronizzazione e la suddivisione in livelli nel cloud
    - In questa versione sono stati apportati diversi miglioramenti all'affidabilità e alle prestazioni. Alcune di esse sono destinate a rendere più efficiente la suddivisione in livelli nel cloud e Sincronizzazione file di Azure come un intero lavoro migliore in tali situazioni quando è impostata una pianificazione della limitazione della larghezza di banda.

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione di nano server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di Sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può causare risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.

## <a name="agent-version-6300"></a>Versione dell'agente 6.3.0.0
Le note sulla versione seguenti sono relative alla versione 6.3.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 27 giugno 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- L'accesso o l'esplorazione di una posizione di endpoint server tramite SMB è lento in Windows Server 2012 R2 
- Maggiore utilizzo della CPU dopo l'installazione dell'agente Sincronizzazione file di Azure V6
- Miglioramenti della telemetria per la suddivisione in livelli nel cloud

## <a name="agent-version-6200"></a>Versione dell'agente 6.2.0.0
Le note sulla versione seguenti sono relative alla versione 6.2.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 13 giugno 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- Dopo aver creato un endpoint server, è possibile che si verifichi un utilizzo elevato della CPU quando il richiamo in background Scarica i file nel server
- Le operazioni di sincronizzazione e di suddivisione in livelli cloud possono avere esito negativo con errori ECS_E_SERVER_CREDENTIAL_NEEDED a causa della scadenza
- Il richiamo di un file potrebbe non riuscire se l'URL per il download del file contiene caratteri riservati 

## <a name="agent-version-6100"></a>Versione dell'agente versione 6.1.0.0
Le note sulla versione seguenti sono relative alla versione versione 6.1.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 6 maggio 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 6.0.0.0.

Elenco dei problemi risolti in questa versione:  
- L'interfaccia di amministrazione di Windows non è in grado di visualizzare la versione dell'agente e la configurazione degli endpoint server nei server in cui è installato Sincronizzazione file di Azure agente versione 6,0.

## <a name="agent-version-6000"></a>Versione dell'agente 6.0.0.0
Le note sulla versione seguenti sono relative alla versione 6.0.0.0 dell'agente di Sincronizzazione file di Azure (rilasciato il 22 aprile 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto aggiornamento automatico agenti
  - Il feedback è stato aggiunto e è stata aggiunta una funzionalità di aggiornamento automatico nell'agente di Sincronizzazione file di Azure server. Per ulteriori informazioni, vedere [sincronizzazione file di Azure criteri di aggiornamento dell'agente](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Supporto per gli ACL di condivisione file di Azure
  - Sincronizzazione file di Azure ha sempre supportato la sincronizzazione degli ACL tra gli endpoint server, ma gli ACL non sono stati sincronizzati con l'endpoint cloud (condivisione file di Azure). In questa versione è stato aggiunto il supporto per sincronizzare gli ACL tra gli endpoint server e cloud.
- Sessioni di sincronizzazione di caricamento e download parallele per un endpoint server 
  - Gli endpoint server supportano ora il caricamento e il download di file contemporaneamente. Non è più in attesa del completamento di un download, quindi è possibile caricare i file nella condivisione file di Azure. 
- Nuovi cmdlet di suddivisione in livelli cloud per ottenere lo stato di volume e suddivisione in livelli
  - È ora possibile usare due nuovi cmdlet di PowerShell locali per il server per ottenere informazioni su suddivisione in livelli nel cloud e richiamo di file. Eseguono le informazioni di registrazione di due canali di eventi sul server disponibili:
    - Get-StorageSyncFileTieringResult elenca tutti i file e i relativi percorsi non suddivisi in livelli e segnala il motivo per cui.
    - Get-StorageSyncFileRecallResult segnala tutti gli eventi di richiamo di file. Elenca tutti i file richiamati e il relativo percorso, nonché l'esito positivo o negativo per il richiamo.
  - Per impostazione predefinita, entrambi i canali di eventi possono archiviare fino a 1 MB ciascuno: è possibile aumentare la quantità di file segnalati aumentando le dimensioni del canale di eventi.
- Supporto per la modalità FIPS
  - Sincronizzazione file di Azure supporta ora l'abilitazione della modalità FIPS nei server in cui è installato l'agente di Sincronizzazione file di Azure.
    - Prima di abilitare la modalità FIPS sul server, installare l'agente Sincronizzazione file di Azure e il [modulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) nel server. Se FIPS è già abilitato nel server, [scaricare manualmente](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) il [modulo PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) nel server.
- Vari miglioramenti dell'affidabilità per la suddivisione in livelli e la sincronizzazione nel cloud

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nell'opzione di distribuzione di nano server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.

## <a name="agent-version-5200"></a>Versione dell'agente 5.2.0.0
Le note sulla versione seguenti sono relative alla versione 5.2.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 4 aprile 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 5.0.2.0.

Elenco dei problemi risolti in questa versione:  
- Miglioramenti dell'affidabilità per il trasferimento di dati offline e le funzionalità di ripresa del trasferimento dei dati
- Miglioramenti della telemetria di sincronizzazione

## <a name="agent-version-5100"></a>Versione dell'agente 5.1.0.0
Le note sulla versione seguenti sono relative alla versione 5.1.0.0 dell'agente Sincronizzazione file di Azure rilasciato il 7 marzo 2019. Queste note si aggiungono alle note sulla versione elencate per la versione 5.0.2.0.

Elenco dei problemi risolti in questa versione:  
- È possibile che i file non vengano sincronizzati con l'errore 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) se l'enumerazione delle modifiche non riesce nel server
- Se una sessione o un file di sincronizzazione riceve un errore 0x80072F78 (WININET_E_INVALID_SERVER_RESPONSE), Sync riproverà a ripetere l'operazione
- I file potrebbero non essere sincronizzati con l'errore 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Quando si richiamano i file, può verificarsi un utilizzo elevato della memoria.
- Miglioramenti della telemetria per la suddivisione in livelli nel cloud 

## <a name="agent-version-5020"></a>Versione dell'agente 5.0.2.0
Le note seguenti si riferiscono alla versione 5.0.2.0 dell'agente Sincronizzazione file di Azure resa disponibile il 12 febbraio 2019.

### <a name="improvements-and-issues-that-are-fixed"></a>Miglioramenti e problemi risolti

- Supporto per il cloud di Azure per enti pubblici
  - È stato aggiunto il supporto di anteprima per il cloud di Azure per enti pubblici. Richiede una sottoscrizione consentita e un download di agenti speciali da Microsoft. Per ottenere l'accesso all'anteprima, inviare un messaggio di posta [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)elettronica direttamente all'indirizzo.
- Supporto per la deduplicazione dei dati
    - La deduplicazione dei dati ha un supporto completo con cloud a livelli abilitato in Windows Server 2016 e Windows Server 2019. Abilitando la deduplicazione dei dati in un volume con cloud a livelli abilitato, è possibile memorizzare nella cache un numero maggiore di file in locale senza effettuare il provisioning di altro spazio di archiviazione.
- Supporto per il trasferimento dei dati offline (ad esempio tramite Data Box)
    - Migrare facilmente grandi quantità di dati in Sincronizzazione file di Azure secondo la modalità scelta. È possibile scegliere Azure Data Box, AzCopy e persino servizi di migrazione di terze parti. Non servono grandi quantità di larghezza di banda per trasferire i dati in Azure, nel caso di Data Box è sufficiente inviarli all'interno del servizio tramite posta elettronica. Per altre informazioni, vedere la [documentazione per il trasferimento dati offline](https://aka.ms/AFS/OfflineDataTransfer).
- Prestazioni di sincronizzazione migliorate
    - I clienti con più endpoint server nello stesso volume potrebbero aver riscontrato prestazioni di sincronizzazione più lente nelle versioni precedenti. Sincronizzazione file di Azure crea uno snapshot VSS temporaneo una volta al giorno nel server per sincronizzare i file con handle aperti. Sincronizzazione file ora supporta la sincronizzazione di più endpoint server in un volume quando è attiva una sessione di sincronizzazione VSS. Non occorrerà più attendere il completamento della sessione di sincronizzazione VSS perché la sincronizzazione possa riprendere in altri endpoint server nel volume.
- Funzionalità di monitoraggio nel portale migliorata
    - Sono stati aggiunti grafici nel portale del Servizio di sincronizzazione archiviazione per visualizzare:
        - Numero di file sincronizzati
        - Dimensione dei dati trasferiti
        - Numero di file che non eseguono la sincronizzazione
        - Dimensione dei dati richiamati
        - Stato di connettività del server
    - Per altre informazioni, vedere [Monitorare Sincronizzazione file di Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Scalabilità e affidabilità migliorate
    - Numero massimo di oggetti file system (directory e file) in una directory aumentato a 1.000.000. Il limite precedente era 200.000.
    - Sincronizzazione file effettuerà un tentativo di riprendere il trasferimento dei dati anziché una nuova trasmissione, quando un trasferimento viene interrotto per file di grandi dimensioni 

### <a name="evaluation-tool"></a>Strumento di valutazione
Prima di distribuire la Sincronizzazione file di Azure, è opportuno valutare se è compatibile con il sistema tramite lo strumento di valutazione di Sincronizzazione file di Azure. Questo strumento è un cmdlet di Azure PowerShell che consente di rilevare potenziali problemi con il file system e il set di dati, ad esempio caratteri non supportati o versione del sistema operativo non supportata. Per istruzioni sull'installazione e l'utilizzo, vedere la sezione [Strumento di valutazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) nella Guida alla pianificazione. 

### <a name="agent-installation-and-server-configuration"></a>Installazione dell'agente e configurazione del server
Per altre informazioni su come installare e configurare l'agente Sincronizzazione file di Azure con Windows Server, vedere [Pianificazione per la distribuzione di Sincronizzazione file di Azure](storage-sync-files-planning.md) e [Come distribuire Sincronizzazione file di Azure](storage-sync-files-deployment-guide.md).

- Il pacchetto di installazione dell'agente deve essere installato con autorizzazioni elevate (amministratore).
- L'agente non è supportato nelle opzioni di distribuzione Windows Server Core o Nano Server.
- L'agente è supportato solo in Windows Server 2019, Windows Server 2016 e Windows Server 2012 R2.
- L'agente richiede almeno 2 GiB di memoria. Se il server è in esecuzione in una macchina virtuale con memoria dinamica abilitata, la macchina virtuale deve essere configurata con un minimo di 2048 MiB di memoria.
- Il servizio agente di sincronizzazione archiviazione (FileSyncSvc) non supporta gli endpoint server che si trovano in un volume la cui directory di informazioni sul volume di sistema (SVI) è compressa. Questa configurazione porta a risultati imprevisti.
- La modalità FIPS non è supportata e deve essere disabilitata. 

### <a name="interoperability"></a>Interoperabilità
- Antivirus, backup e altre applicazioni che accedono a file a livelli possono causare un richiamo indesiderato se non rispettano l'attributo offline e ignorano il contenuto dei file. Per altre informazioni, vedere [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md).
- Gli screening dei file di Gestione risorse file server possono generare errori di sincronizzazione infiniti se i file sono bloccati a causa dello screening.
- L'esecuzione di sysprep in un server in cui è installato l'agente di Sincronizzazione file di Azure non è supportata e può portare a risultati imprevisti. L'agente di Sincronizzazione file di Azure deve essere installato dopo la distribuzione dell'immagine del server e il completamento dell'installazione minima di sysprep.

### <a name="sync-limitations"></a>Limitazioni della sincronizzazione
Gli elementi seguenti non vengono sincronizzati, ma il resto del sistema continuerà a funzionare normalmente:
- File con caratteri non supportati. Vedere [Guida alla risoluzione dei problemi](storage-sync-files-troubleshoot.md#handling-unsupported-characters) per un elenco dei caratteri non supportati.
- File o directory che terminano con un punto.
- Percorsi che contengono più di 2.048 caratteri.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza se superiore a 2 kB. Il problema si verifica solo se sono presenti più di 40 voci di controllo di accesso in un singolo elemento.
- Parte DACL (elenco di controllo di accesso discrezionale) di un descrittore di sicurezza usato per il controllo.
- Attributi estesi.
- Flussi dei dati alternativi.
- Reparse point.
- Collegamenti reali.
- La compressione (se impostata in un file server) non viene mantenuta quando le modifiche vengono sincronizzate con tale file da altri endpoint.
- File crittografati con EFS (o un'altra crittografia in modalità utente) che impediscono al servizio di leggere i dati.

    > [!Note]  
    > Sincronizzazione file di Azure crittografa sempre i dati in transito e i dati vengono sempre crittografati quando sono inattivi in Azure.
 
### <a name="server-endpoint"></a>Endpoint server
- Un endpoint server può essere creato solo in un volume NTFS. ReFS, FAT, FAT32 e altri file system non sono attualmente supportati da Sincronizzazione file di Azure.
- I file a più livelli diventano inaccessibili se non vengono richiamati prima di eliminare l'endpoint server. Per ripristinare l'accesso ai file, ricreare l'endpoint server. Se sono trascorsi 30 giorni dall'eliminazione dell'endpoint server o se l'endpoint cloud è stato eliminato, i file a più livelli che non sono stati richiamati saranno inutilizzabili.
- La suddivisione in livelli cloud non è supportata nel volume di sistema. Per creare un endpoint server nel volume di sistema, disabilitare la suddivisione in livelli nel cloud durante la creazione dell'endpoint server.
- Il clustering di failover è supportato solo con i dischi cluster, non con i volumi condivisi cluster.
- Un endpoint server non può essere annidato, ma può coesistere nello stesso volume parallelamente a un altro.
- Non archiviare un file di paging del sistema operativo o dell'applicazione in un percorso dell'endpoint server.
- Se il server viene rinominato, il nome del server nel portale non viene aggiornato.

### <a name="cloud-endpoint"></a>Endpoint cloud
- Sincronizzazione file di Azure supporta la modifica diretta della condivisione file di Azure. Qualsiasi modifica apportata alla condivisione file di Azure, tuttavia, deve prima essere individuata dal processo di rilevamento modifiche di Sincronizzazione file di Azure, che per un endpoint cloud viene avviato una volta ogni 24 ore. Le modifiche apportate a una condivisione file di Azure tramite il protocollo REST, poi, non aggiornano l'ora dell'ultima modifica di SMB e non vengono considerate come modifica dalla procedura di sincronizzazione.
- È possibile spostare il servizio di sincronizzazione archiviazione e/o l'account di archiviazione in un gruppo di risorse o in una sottoscrizione diversa all'interno del tenant esistente di Azure AD. Se l'account di archiviazione viene spostato, è necessario concedere l'accesso al servizio Sincronizzazione file ibrida nell'account di archiviazione (vedere [Garantire che Sincronizzazione file di Azure possa accedere all'account di archiviazione](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Sincronizzazione file di Azure non consente lo spostamento della sottoscrizione in un diverso tenant di Azure AD.

### <a name="cloud-tiering"></a>Suddivisione in livelli nel cloud
- Se un file a più livelli viene copiato in un'altra posizione usando Robocopy, il file risultante non è suddiviso in livelli. L'attributo offline potrebbe essere impostato perché Robocopy include erroneamente tale attributo nelle operazioni di copia.
- Quando si copiano file tramite robocopy, usare l'opzione /MIR per conservare i timestamp dei file. In questo modo i file meno recenti verranno archiviati a livelli prima dei file aperti più di recente.
- Quando si visualizzano le proprietà del file da un client SMB, l'attributo offline può non essere correttamente impostato a causa della memorizzazione nella cache SMB dei metadati del file.
