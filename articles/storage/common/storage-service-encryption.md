---
title: Crittografia del servizio di archiviazione di Azure per dati inattivi
description: Archiviazione di Azure protegge i dati mediante la crittografia automatica prima di renderli permanente nel cloud. È possibile basarsi sulle chiavi gestite da Microsoft per la crittografia dei dati nell'account di archiviazione oppure è possibile gestire la crittografia con chiavi personalizzate.
services: storage
author: tamram
ms.service: storage
ms.date: 03/23/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 0688e14b77d885132d6c3fbaa44bed117cc7cf9d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641120"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Crittografia del servizio di archiviazione di Azure per dati inattivi

Archiviazione di Azure usa la crittografia lato server (SSE) per crittografare automaticamente i dati quando vengono salvati in modo permanente nel cloud. La crittografia di archiviazione di Azure protegge i tuoi dati e ti aiuta a soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

## <a name="about-azure-storage-encryption"></a>Informazioni sulla crittografia di archiviazione di Azure

I dati in archiviazione di Azure vengono crittografati e decrittografati in modo trasparente usando la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2. La crittografia di archiviazione di Azure è simile alla crittografia BitLocker per Windows.

La crittografia di archiviazione di Azure è abilitata per tutti gli account di archiviazione, inclusi Gestione risorse e gli account di archiviazione classici. La crittografia di archiviazione di Azure non può essere disabilitata. Poiché i dati sono protetti per impostazione predefinita, non è necessario modificare il codice o le applicazioni per sfruttare la crittografia di archiviazione di Azure.

I dati in un account di archiviazione vengono crittografati indipendentemente dal livello di prestazioni (standard o Premium), dal livello di accesso (ad accesso frequente o sporadico) o dal modello di distribuzione (Azure Resource Manager o classica). Vengono crittografati anche tutti i BLOB nel livello archivio. Tutte le opzioni di ridondanza di archiviazione di Azure supportano la crittografia e tutti i dati nelle aree primaria e secondaria vengono crittografati quando la replica geografica è abilitata. Tutte le risorse di archiviazione di Azure vengono crittografate, inclusi BLOB, dischi, file, code e tabelle. Vengono crittografati anche tutti i metadati degli oggetti. Non sono previsti costi aggiuntivi per la crittografia di archiviazione di Azure.

Ogni BLOB in blocchi, BLOB di accodamento o BLOB di pagine che è stato scritto in archiviazione di Azure dopo il 20 ottobre 2017 è crittografato. I BLOB creati prima di questa data continuano a essere crittografati da un processo in background. Per forzare la crittografia di un blob creato prima del 20 ottobre 2017, è possibile riscrivere il BLOB. Per informazioni su come controllare lo stato di crittografia di un BLOB, vedere [controllare lo stato della crittografia di un BLOB](../blobs/storage-blob-encryption-status.md).

Per altre informazioni sui moduli di crittografia sottostanti la crittografia di archiviazione di Azure, vedere [Cryptography API: Next Generation](/windows/desktop/seccng/cng-portal).

Per informazioni sulla crittografia e la gestione delle chiavi per Azure Managed disks, vedere la pagina relativa alla [crittografia lato server di Azure Managed disks](../../virtual-machines/disk-encryption.md).

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Per impostazione predefinita, i dati in un nuovo account di archiviazione vengono crittografati con chiavi gestite da Microsoft. È possibile continuare a utilizzare le chiavi gestite da Microsoft per la crittografia dei dati oppure è possibile gestire la crittografia con le proprie chiavi. Se si sceglie di gestire la crittografia con le proprie chiavi, sono disponibili due opzioni. È possibile utilizzare entrambi i tipi di gestione delle chiavi o entrambi:

- È possibile specificare una *chiave gestita dal cliente* da usare per crittografare e decrittografare i dati nell'archivio BLOB e in file di Azure. <sup>1, 2</sup> le chiavi gestite dal cliente devono essere archiviate in Azure Key Vault o Azure Key Vault modello di protezione hardware (HSM) gestito (anteprima). Per altre informazioni sulle chiavi gestite dal cliente, vedere [usare chiavi gestite dal cliente per la crittografia di archiviazione di Azure](./customer-managed-keys-overview.md).
- È possibile specificare una *chiave fornita dal cliente* per le operazioni di archiviazione BLOB. Un client che effettua una richiesta di lettura o scrittura sull'archiviazione BLOB può includere una chiave di crittografia sulla richiesta per un controllo granulare sulla modalità di crittografia e decrittografia dei dati BLOB. Per altre informazioni sulle chiavi fornite dal cliente, vedere [fornire una chiave di crittografia per una richiesta all'archiviazione BLOB](../blobs/encryption-customer-provided-keys.md).

La tabella seguente confronta le opzioni di gestione delle chiavi per la crittografia di archiviazione di Azure.

| Parametro di gestione delle chiavi | chiavi gestite da Microsoft | Chiavi gestite dal cliente | Chiavi fornite dal cliente |
|--|--|--|--|
| Operazioni di crittografia/decrittografia | Azure | Azure | Azure |
| Servizi di archiviazione di Azure supportati | Tutti | Archiviazione BLOB, File di Azure<sup>1, 2</sup> | Archiviazione BLOB |
| Archiviazione chiavi | Archivio chiavi Microsoft | Azure Key Vault o Key Vault HSM | Archivio chiavi personalizzato del cliente |
| Responsabilità della rotazione delle chiavi | Microsoft | Customer | Customer |
| Controllo chiave | Microsoft | Customer | Customer |

<sup>1</sup> per informazioni sulla creazione di un account che supporta l'uso di chiavi gestite dal cliente con l'archiviazione code, vedere [creare un account che supporta chiavi gestite dal cliente per le code](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> per informazioni sulla creazione di un account che supporta l'uso di chiavi gestite dal cliente con l'archiviazione tabelle, vedere [creare un account che supporta chiavi gestite dal cliente per le tabelle](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

> [!NOTE]
> Le chiavi gestite da Microsoft vengono ruotate in modo appropriato in base ai requisiti di conformità. Se si dispone di requisiti specifici per la rotazione delle chiavi, Microsoft consiglia di passare alle chiavi gestite dal cliente per poter gestire e controllare la rotazione autonomamente.

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>Crittografare doppiamente i dati con la crittografia dell'infrastruttura

I clienti che richiedono livelli elevati di garanzia che i dati sono sicuri possono anche abilitare la crittografia AES a 256 bit a livello di infrastruttura di archiviazione di Azure. Quando è abilitata la crittografia dell'infrastruttura, i dati in un account di archiviazione vengono crittografati due volte &mdash; a livello di servizio e una volta a livello di infrastruttura &mdash; con due diversi algoritmi di crittografia e due chiavi diverse. La doppia crittografia dei dati di archiviazione di Azure protegge da uno scenario in cui uno degli algoritmi o delle chiavi di crittografia potrebbe essere compromesso. In questo scenario, il livello di crittografia aggiuntivo continua a proteggere i dati.

La crittografia a livello di servizio supporta l'utilizzo di chiavi gestite da Microsoft o chiavi gestite dal cliente con Azure Key Vault. La crittografia a livello di infrastruttura si basa su chiavi gestite da Microsoft e usa sempre una chiave separata.

Per altre informazioni su come creare un account di archiviazione che Abilita la crittografia dell'infrastruttura, vedere [creare un account di archiviazione con crittografia dell'infrastruttura abilitata per la doppia crittografia dei dati](infrastructure-encryption-enable.md).

## <a name="next-steps"></a>Passaggi successivi

- [Cos'è l'insieme di credenziali chiave di Azure?](../../key-vault/general/overview.md)
- [Chiavi gestite dal cliente per la crittografia di archiviazione di Azure](customer-managed-keys-overview.md)
- [Ambiti di crittografia per l'archiviazione BLOB](../blobs/encryption-scope-overview.md)
- [Fornire una chiave di crittografia per una richiesta all'archiviazione BLOB](../blobs/encryption-customer-provided-keys.md)
