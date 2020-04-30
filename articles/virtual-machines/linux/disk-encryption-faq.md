---
title: 'Domande frequenti: crittografia dischi di Azure per macchine virtuali Linux'
description: Questo articolo fornisce le risposte alle domande frequenti sulla crittografia del disco Microsoft Azure per le macchine virtuali IaaS Linux.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 753d606e1fc2dc966c970a210cf6fc5066d5ed83
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81460138"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>Domande frequenti su crittografia dischi di Azure per macchine virtuali Linux

Questo articolo fornisce le risposte alle domande frequenti su crittografia dischi di Azure per macchine virtuali (VM) Linux. Per altre informazioni su questo servizio, vedere [Panoramica di crittografia dischi di Azure](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>Che cos'è crittografia dischi di Azure per le VM Linux?

Crittografia dischi di Azure per macchine virtuali Linux usa la funzionalità dm-crypt di Linux per fornire la crittografia completa del disco del sistema operativo * e dei dischi dati. Fornisce inoltre la crittografia del disco delle risorse effimere quando si usa la [funzionalità EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms). Il contenuto fluisce crittografato dalla macchina virtuale al back-end di archiviazione. Fornendo quindi la crittografia end-to-end con una chiave gestita dal cliente.
 
Vedere [macchine virtuali e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Dove è presente Crittografia dischi di Azure con disponibilità generale (GA)?

Crittografia dischi di Azure per macchine virtuali Linux è disponibile a livello generale in tutte le aree pubbliche di Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quali esperienze utente sono disponibili con Crittografia dischi di Azure?

La versione GA di Crittografia dischi di Azure supporta i modelli di Azure Resource Manager, Azure PowerShell e l'interfaccia della riga di comando di Azure. La presenza di diverse esperienze utente consente una maggiore flessibilità. Sono disponibili tre diverse opzioni per abilitare la crittografia dei dischi per le macchine virtuali. Per altre informazioni sull'esperienza utente e indicazioni dettagliate disponibili in crittografia dischi di Azure, vedere [scenari di crittografia dischi di Azure per Linux](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto costa Crittografia dischi di Azure?

Non è previsto alcun addebito per la crittografia dei dischi delle macchine virtuali con crittografia dischi di Azure, ma sono previsti addebiti associati all'uso di Azure Key Vault. Per altre informazioni sui costi associati ad Azure Key Vault, vedere la pagina [Prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Come si può iniziare a usare Crittografia dischi di Azure?

Per iniziare, leggere l'articolo [Azure Disk Encryption overview](disk-encryption-overview.md) (Panoramica di Crittografia dischi di Azure).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Quali sono le dimensioni e i sistemi operativi delle macchine virtuali che supportano crittografia dischi di Azure?

L'articolo [Panoramica di crittografia dischi di Azure](disk-encryption-overview.md) elenca le dimensioni delle [VM](disk-encryption-overview.md#supported-vms) e i [sistemi operativi delle macchine virtuali](disk-encryption-overview.md#supported-operating-systems) che supportano crittografia dischi di Azure.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>È possibile crittografare sia i volumi di avvio che i volumi di dati con Crittografia dischi di Azure?

Sì, è possibile crittografare sia i volumi di avvio che i volumi di dati oppure è possibile crittografare il volume dei dati senza dover prima crittografare il volume del sistema operativo. 

Dopo aver crittografato il volume del sistema operativo, la disabilitazione della crittografia nel volume del sistema operativo non è supportata. Per le VM Linux in un set di scalabilità, è possibile crittografare solo il volume di dati.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>È possibile crittografare un volume smontato con crittografia dischi di Azure?

No, crittografia dischi di Azure Crittografa solo i volumi montati.

## <a name="what-is-storage-server-side-encryption"></a>Che cos'è la crittografia lato server di archiviazione?

La crittografia lato server di archiviazione crittografa i dischi gestiti di Azure in archiviazione di Azure. I dischi gestiti vengono crittografati per impostazione predefinita con la crittografia lato server con una chiave gestita dalla piattaforma (a partire dal 10 giugno 2017). È possibile gestire la crittografia dei dischi gestiti con le proprie chiavi specificando una chiave gestita dal cliente. Per altre informazioni, vedere la pagina relativa alla [crittografia lato server di Azure Managed disks](disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>In che modo crittografia dischi di Azure è diversa dalla crittografia lato server di archiviazione con chiave gestita dal cliente e quando è consigliabile usare ogni soluzione?

Crittografia dischi di Azure fornisce la crittografia end-to-end per il disco del sistema operativo, i dischi dati e il disco di risorse temporaneo con una chiave gestita dal cliente.
- Se i requisiti includono la crittografia di tutte le versioni precedenti e della crittografia end-to-end, usare crittografia dischi di Azure. 
- Se i requisiti includono la crittografia dei soli dati inattivi con la chiave gestita dal cliente, usare la [crittografia lato server con chiavi gestite dal cliente](disk-encryption.md). Non è possibile crittografare un disco con crittografia dischi di Azure e crittografia lato server di archiviazione con chiavi gestite dal cliente. 
- Se la distribuzione Linux non è elencata in [sistemi operativi supportati per crittografia dischi di Azure](disk-encryption-overview.md#supported-operating-systems) o se si usa uno scenario indicato negli [scenari non supportati per Windows](disk-encryption-linux.md#unsupported-scenarios), prendere in considerazione la [crittografia lato server con chiavi gestite dal cliente](disk-encryption.md).
- Se i criteri dell'organizzazione consentono di crittografare il contenuto inattivo con una chiave gestita da Azure, non è necessaria alcuna azione. il contenuto è crittografato per impostazione predefinita. Per i dischi gestiti, il contenuto all'interno dell'archiviazione viene crittografato per impostazione predefinita con la crittografia lato server con chiave gestita dalla piattaforma. La chiave viene gestita dal servizio di archiviazione di Azure. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Ricerca per categorie ruotare i segreti o le chiavi di crittografia?

Per ruotare i segreti, è sufficiente chiamare lo stesso comando usato originariamente per abilitare la crittografia del disco, specificando un Key Vault diverso. Per ruotare la chiave di crittografia della chiave, chiamare lo stesso comando usato originariamente per abilitare la crittografia del disco, specificando la nuova crittografia della chiave. 

>[!WARNING]
> - Se in precedenza è stato usato [crittografia dischi di Azure con Azure ad app](disk-encryption-linux-aad.md) specificando Azure ad credenziali per crittografare questa macchina virtuale, sarà necessario continuare a usare questa opzione per crittografare la macchina virtuale. Non è possibile usare crittografia dischi di Azure in questa macchina virtuale crittografata perché non è uno scenario supportato, il che significa che la disattivazione dell'applicazione AAD per questa macchina virtuale crittografata non è ancora supportata.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Ricerca per categorie aggiungere o rimuovere una chiave di crittografia della chiave se non ne è stata originariamente usata una?

Per aggiungere una chiave di crittografia della chiave, chiamare di nuovo il comando di abilitazione passando il parametro della chiave di crittografia della chiave. Per rimuovere una chiave di crittografia della chiave, chiamare di nuovo il comando di abilitazione senza il parametro chiave di crittografia chiave.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Con Crittografia dischi di Azure sono disponibili BYOK (Bring Your Own Key)?

Sì, è possibile fornire le proprie chiavi di crittografia della chiave. Queste chiavi sono protette in Azure Key Vault, l'archivio delle chiavi per Crittografia dischi di Azure. Per altre informazioni sugli scenari di supporto delle chiavi di crittografia chiave, vedere [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>È possibile usare una chiave di crittografia della chiave creata in Azure?

Sì, è possibile usare Azure Key Vault per generare una chiave di crittografia della chiave per l'uso con Crittografia dischi di Azure. Queste chiavi sono protette in Azure Key Vault, l'archivio delle chiavi per Crittografia dischi di Azure. Per altre informazioni sulla chiave di crittografia della chiave, vedere [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>È possibile usare un servizio di gestione delle chiavi/modulo di protezione hardware locale per proteggere le chiavi di crittografia?

Non è possibile usare il servizio di gestione delle chiavi/modulo di protezione hardware locale per proteggere le chiavi di crittografia con Crittografia dischi di Azure. Per proteggere le chiavi di crittografia, è possibile usare solo il servizio Azure Key Vault. Per altre informazioni sugli scenari di supporto della chiave di crittografia chiave, vedere [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quali sono i prerequisiti per la configurazione di Crittografia dischi di Azure?

Per usare Crittografia dischi di Azure, è necessario rispettare alcuni prerequisiti. Vedere l'articolo [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](disk-encryption-key-vault.md) per creare un nuovo insieme di credenziali delle chiavi o configurare un insieme di credenziali delle chiavi esistente per l'accesso alla crittografia del disco per abilitare la crittografia e proteggere i segreti e le chiavi. Per altre informazioni sugli scenari di supporto della chiave di crittografia chiave, vedere [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quali sono i prerequisiti per la configurazione di Crittografia dischi di Azure con un'app Azure AD (versione precedente)?

Per usare Crittografia dischi di Azure, è necessario rispettare alcuni prerequisiti. Per creare un'applicazione Azure Active Directory, creare un nuovo insieme di credenziali delle chiavi o configurare un insieme di credenziali delle chiavi esistente per l'accesso alla crittografia del disco per abilitare la crittografia e proteggere i segreti e le chiavi, vedere [crittografia dischi di Azure con contenuto Azure ad](disk-encryption-linux-aad.md) . Per altre informazioni sugli scenari di supporto della chiave di crittografia chiave, vedere [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure ad](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>L'esecuzione di Crittografia dischi di Azure con un'app Azure AD (versione precedente) è ancora supportata?
Sì. L'esecuzione di Crittografia dischi con un'app Azure AD è ancora supportata. Tuttavia, quando si esegue la crittografia di nuove macchine virtuali, è consigliabile usare il nuovo metodo anziché un'app Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>È possibile eseguire la migrazione di macchine virtuali crittografate con un'app Azure AD alla crittografia senza tale app?
  Non esiste attualmente un percorso di migrazione diretto per le macchine crittografate con un'app Azure AD alla crittografia senza tale app. Non esiste nemmeno un percorso diretto dalla crittografia senza un'app Azure AD alla crittografia con tale app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Quale versione di Azure PowerShell è supportata da Crittografia dischi di Azure?

Usare la versione più recente di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Crittografia dischi di Azure *non* è supportato da Azure SDK versione 1.1.0.

> [!NOTE]
> L'estensione di anteprima "Microsoft. OSTCExtension. AzureDiskEncryptionForLinux" di Linux Azure Disk Encryption è deprecata. Questa estensione è stata pubblicata per la versione di anteprima di crittografia dischi di Azure. Non usare la versione di anteprima dell'estensione nella distribuzione di test o produzione.

> Per gli scenari di distribuzione come Azure Resource Manager (ARM), in cui è necessario distribuire l'estensione di crittografia dischi di Azure per la macchina virtuale Linux per abilitare la crittografia nella VM IaaS Linux, è necessario usare l'estensione supportata per la produzione di crittografia dischi di Azure "Microsoft. Azure. Security. AzureDiskEncryptionForLinux".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>È possibile applicare Crittografia dischi di Azure a un'immagine Linux personalizzata?

Non è possibile applicare Crittografia dischi di Azure a un'immagine Linux personalizzata. Sono consentite solo le immagini Linux per le distribuzioni supportate indicate in precedenza. Le immagini Linux personalizzate non sono attualmente supportate.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>È possibile applicare gli aggiornamenti a una VM Red Hat Linux che usa l'aggiornamento Yum?

Sì, è possibile eseguire un aggiornamento yum in una VM Red Hat Linux.  Per altre informazioni, vedere [crittografia dischi di Azure in una rete isolata](disk-encryption-isolated-network.md).

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Qual è il flusso di lavoro di crittografia dischi di Azure consigliato per Linux?

Di seguito è riportato il flusso di lavoro consigliato per ottenere risultati ottimali in Linux:
* Iniziare dall'immagine della raccolta non modificata corrispondente alla distribuzione e alla versione del sistema operativo desiderate
* Eseguire il backup di tutte le unità montate che verranno crittografate.  In questo modo è possibile eseguire il ripristino in caso di errore, ad esempio se la macchina virtuale viene riavviata prima del completamento della crittografia.
* Eseguire la crittografia (può richiedere più ore o addirittura giorni a seconda delle caratteristiche della macchina virtuale e delle dimensioni di qualsiasi disco dati collegato)
* Personalizzare e aggiungere il software all'immagine in base alle esigenze.

Se questo flusso di lavoro non è possibile, l'uso della [crittografia del servizio di archiviazione](../../storage/common/storage-service-encryption.md) a livello di account di archiviazione della piattaforma può essere un'alternativa alla crittografia completa del disco tramite dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Che cos'è il disco "volume BEK" o "/mnt/azure_bek_disk"?

"Volume di" è un volume di dati locale che archivia in modo sicuro le chiavi di crittografia per le VM di Azure crittografate.
> [!NOTE]
> Non eliminare né modificare il contenuto del disco. Non smontare il disco, perché la presenza delle chiavi di crittografia è necessaria per qualsiasi operazione di crittografia sulla VM IaaS.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Qual è il metodo di crittografia usato da Crittografia dischi di Azure?

Crittografia dischi di Azure usa la decrittografia predefinita di AES-XTS-plain64 con una chiave master del volume a 256 bit.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se si utilizzano EncryptFormatAll e vengono specificati tutti i tipi di volume, verranno cancellati i dati sulle unità dati già crittografati?
No, i dati non verranno cancellati da unità dati che sono già crittografate usando Crittografia dischi di Azure. Analogamente al modo in cui EncryptFormatAll non crittografa nuovamente l'unità del sistema operativo, non riapplica la crittografia all'unità dati già crittografata. Per altre informazioni, vedere [Criteri EncryptFormatAll ](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>Il file system XFS è supportato?
La crittografia dei dischi del sistema operativo XFS è supportata.

La crittografia dei dischi dati XFS è supportata solo quando si usa il parametro EncryptFormatAll. Il volume verrà riformattato, cancellando tutti i dati precedentemente presenti. Per altre informazioni, vedere [Criteri EncryptFormatAll ](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>È possibile eseguire il backup e il ripristino di una VM crittografata? 

Backup di Azure fornisce un meccanismo per il backup e il ripristino di VM crittografate all'interno della stessa sottoscrizione e area.  Per istruzioni, vedere backup [e ripristino di macchine virtuali crittografate con backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).  Il ripristino di una macchina virtuale crittografata in un'area diversa non è attualmente supportato.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Quali risorse sono disponibili per porre domande o inviare commenti?

È possibile porre domande o inviare commenti e suggerimenti sul [forum di Crittografia dischi di Azure disponibile](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Passaggi successivi
In questo documento sono state fornite le risposte alle domande più frequenti relative a Crittografia dischi di Azure. Per altre informazioni sul servizio, vedere gli articoli seguenti:

- [Panoramica di crittografia dischi di Azure](disk-encryption-overview.md)
- [Applicare la crittografia del disco nel Centro sicurezza Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Crittografia dei dati inattivi in Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
