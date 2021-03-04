---
title: Crittografia lato server dei dischi gestiti di Azure
description: Archiviazione di Azure protegge i dati crittografandoli nello stato inattivo prima di renderli persistenti nei cluster di archiviazione. È possibile usare chiavi gestite dal cliente per gestire la crittografia con chiavi personalizzate oppure è possibile fare affidamento sulle chiavi gestite da Microsoft per la crittografia dei dischi gestiti.
author: roygara
ms.date: 03/02/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a1fbd536943023d3e6724b9c1638f7a0bd97d847
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036946"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Crittografia lato server di archiviazione su disco di Azure

La crittografia lato server (SSE) protegge i dati e consente di soddisfare gli obblighi di sicurezza e conformità dell'organizzazione. Per impostazione predefinita, SSE crittografa automaticamente i dati archiviati in dischi gestiti di Azure (dischi del sistema operativo e dischi dati) per impostazione predefinita quando vengono salvati in modo permanente nel cloud. 

I dati nei dischi gestiti di Azure sono crittografati in modo trasparente con la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, una delle crittografie a blocchi più solide disponibili, conforme a FIPS 140-2. Per altre informazioni sui moduli crittografici sottostanti i dischi gestiti di Azure, vedere [API Cryptography Next Generation](/windows/desktop/seccng/cng-portal)

La crittografia lato server non influisce sulle prestazioni dei dischi gestiti e non sono previsti costi aggiuntivi. 

> [!NOTE]
> I dischi temporanei non sono dischi gestiti e non sono crittografati da SSE, a meno che non si abiliti la crittografia nell'host.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

È possibile basarsi sulle chiavi gestite dalla piattaforma per la crittografia del disco gestito oppure è possibile gestire la crittografia usando le proprie chiavi. Se si sceglie di gestire la crittografia con le proprie chiavi, è possibile specificare una *chiave gestita dal cliente* da usare per crittografare e decrittografare tutti i dati presenti nei dischi gestiti. 

Le sezioni seguenti descrivono in modo più dettagliato tutte le opzioni per la gestione delle chiavi.

### <a name="platform-managed-keys"></a>Chiavi gestite dalla piattaforma

Per impostazione predefinita, i dischi gestiti usano chiavi di crittografia gestite dalla piattaforma. Tutti i dischi gestiti, gli snapshot, le immagini e i dati scritti nei dischi gestiti esistenti vengono automaticamente crittografati a riposo con chiavi gestite dalla piattaforma.

### <a name="customer-managed-keys"></a>Chiavi gestite dal cliente

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Restrizioni

Per il momento, le chiavi gestite dal cliente presentano le restrizioni seguenti:

- Se questa funzionalità è abilitata per il disco, non è possibile disabilitarla.
    Se è necessario risolvere il problema, è necessario copiare tutti i dati usando il [modulo Azure PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) o l'interfaccia della riga di comando di [Azure](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk), in un disco gestito completamente diverso che non usa chiavi gestite dal cliente.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Aree supportate

Le chiavi gestite dal cliente sono disponibili in tutte le aree in cui sono disponibili i dischi gestiti.

La rotazione automatica delle chiavi è in anteprima e disponibile solo nelle aree geografiche seguenti:

- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti centro-meridionali
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Europa settentrionale
- Europa occidentale
- Francia centrale

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory Azure AD a un'altra, l'identità gestita associata a Managed Disks non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere [Trasferimento di una sottoscrizione tra directory di Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

Per abilitare le chiavi gestite dal cliente per Managed disks, vedere gli articoli che illustrano come abilitarla con il [modulo Azure PowerShell](windows/disks-enable-customer-managed-keys-powershell.md), l'interfaccia della riga di comando di [Azure](linux/disks-enable-customer-managed-keys-cli.md) o l' [portale di Azure](disks-enable-customer-managed-keys-portal.md). Per informazioni su come abilitare le chiavi gestite dal cliente con la rotazione automatica delle chiavi, vedere [configurare un Azure Key Vault e DiskEncryptionSet con rotazione automatica delle chiavi (anteprima)](windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview).

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Crittografia in host-end-to-end Encryption per i dati della VM

Quando si Abilita la crittografia nell'host, la crittografia viene avviata nell'host della macchina virtuale, il server Azure a cui è allocata la macchina virtuale. I dati per il disco temporaneo e le cache del disco del sistema operativo/dati vengono archiviati nell'host VM. Dopo aver abilitato la crittografia nell'host, tutti questi dati vengono crittografati inattivi e flussi crittografati nel servizio di archiviazione, dove vengono salvati in modo permanente. In sostanza, la crittografia nell'host crittografa i dati da end a end. La crittografia nell'host non usa la CPU della macchina virtuale e non influisca sulle prestazioni della VM. 

I dischi temporanei e i dischi del sistema operativo temporanei vengono crittografati a riposo con chiavi gestite dalla piattaforma quando si Abilita la crittografia end-to-end. Le cache del sistema operativo e del disco dati vengono crittografate a riposo con chiavi gestite dal cliente o gestite dalla piattaforma, a seconda del tipo di crittografia del disco selezionato. Se, ad esempio, un disco è crittografato con chiavi gestite dal cliente, la cache per il disco viene crittografata con le chiavi gestite dal cliente e se un disco viene crittografato con le chiavi gestite dalla piattaforma, la cache per il disco viene crittografata con le chiavi gestite dalla piattaforma.

### <a name="restrictions"></a>Restrizioni

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Aree supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>Dimensioni delle macchine virtuali supportate

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Per abilitare la crittografia end-to-end usando la crittografia nell'host, vedere gli articoli che illustrano come abilitarla con il [modulo Azure PowerShell](windows/disks-enable-host-based-encryption-powershell.md), l'interfaccia della riga di comando di [Azure](linux/disks-enable-host-based-encryption-cli.md)o l' [portale di Azure](disks-enable-host-based-encryption-portal.md).

## <a name="double-encryption-at-rest"></a>Crittografia a doppio inattivo

I clienti sensibili alla sicurezza elevata interessati dal rischio associato a un algoritmo di crittografia specifico, un'implementazione o una chiave compromessa possono ora optare per un ulteriore livello di crittografia usando un algoritmo/modalità di crittografia diverso a livello di infrastruttura usando chiavi di crittografia gestite dalla piattaforma. Questo nuovo livello può essere applicato a sistemi operativi e dischi dati, snapshot e immagini salvati in modo permanente, che verranno tutti crittografati a riposo con la crittografia doppia.

### <a name="supported-regions"></a>Aree supportate

La crittografia doppia è disponibile in tutte le aree in cui sono disponibili i dischi gestiti.

Per abilitare la crittografia doppia per i dischi gestiti, vedere gli articoli che illustrano come abilitarla con il [modulo Azure PowerShell](windows/disks-enable-double-encryption-at-rest-powershell.md), l' [interfaccia](linux/disks-enable-double-encryption-at-rest-cli.md) della riga di comando di Azure o l' [portale di Azure](disks-enable-double-encryption-at-rest-portal.md).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Crittografia lato server e Crittografia dischi di Azure

[Crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md) sfrutta la funzionalità [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux o la funzionalità [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) di Windows per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della VM guest.  La crittografia lato server con chiavi gestite dal cliente migliora la funzionalità Crittografia dischi di Azure poiché consente di usare qualsiasi tipo e immagine di sistema operativo per le macchine virtuali crittografando i dati nel servizio di archiviazione.
> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory di Azure AD a un'altra, l'identità gestita associata ai dischi gestiti non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere [Trasferimento di una sottoscrizione tra directory di Azure AD](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Passaggi successivi

- Abilitare la crittografia end-to-end usando la crittografia nell'host con il [modulo Azure PowerShell](windows/disks-enable-host-based-encryption-powershell.md), l'interfaccia della riga di comando di [Azure](linux/disks-enable-host-based-encryption-cli.md)o l' [portale di Azure](disks-enable-host-based-encryption-portal.md).
- Abilitare la crittografia doppia per i dischi gestiti con il [modulo Azure PowerShell](windows/disks-enable-double-encryption-at-rest-powershell.md), l'interfaccia della riga di comando di [Azure](linux/disks-enable-double-encryption-at-rest-cli.md) o l' [portale di Azure](disks-enable-double-encryption-at-rest-portal.md).
- Abilitare le chiavi gestite dal cliente per i dischi gestiti con il [modulo Azure PowerShell](windows/disks-enable-customer-managed-keys-powershell.md), l'interfaccia della riga di comando di [Azure](linux/disks-enable-customer-managed-keys-cli.md) o l' [portale di Azure](disks-enable-customer-managed-keys-portal.md).
- [Esplorare i modelli di Azure Resource Manager per la creazione di dischi crittografati con chiavi gestite dal cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Cos'è l'insieme di credenziali chiave di Azure?](../key-vault/general/overview.md)