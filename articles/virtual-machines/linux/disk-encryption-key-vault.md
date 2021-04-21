---
title: Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure
description: Questo articolo illustra i passaggi per la creazione e la configurazione di un insieme di credenziali delle chiavi per l'Crittografia dischi di Azure in una macchina virtuale Linux.
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 29f6f01ece0536d93ca18ca54379757adc120308
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818783"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure

Crittografia dischi di Azure usa Azure Key Vault per controllare e gestire segreti e chiavi di crittografia dei dischi.  Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../../key-vault/general/overview.md) e [Proteggere l'insieme di credenziali delle chiavi](../../key-vault/general/security-features.md). 

> [!WARNING]
> - Se in precedenza è stato usato il servizio Crittografia dischi di Azure con Azure AD per crittografare una macchina virtuale, sarà necessario continuare a usare questa opzione per crittografare la VM. Per informazioni dettagliate, vedere [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure con Azure AD (versione precedente)](disk-encryption-key-vault-aad.md).

La creazione e la configurazione di un insieme di credenziali delle chiavi per l'uso con Crittografia dischi di Azure prevede tre passaggi:

1. Creazione di un gruppo di risorse, se necessario.
2. Creazione di un insieme di credenziali delle chiavi. 
3. Impostazione di criteri di accesso avanzati per l'insieme di credenziali delle chiavi.

Questi passaggi sono illustrati negli argomenti di avvio rapido seguenti:

- [Creare e crittografare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](disk-encryption-cli-quickstart.md)
- [Creare e crittografare una macchina virtuale Linux con Azure PowerShell](disk-encryption-powershell-quickstart.md)

Se si preferisce, è anche possibile generare o importare una chiave di crittografia della chiave.

> [!Note]
> I passaggi descritti in questo articolo sono automatizzati nello [script dell'interfaccia della riga di comando dei prerequisiti di Crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started) e nello [script PowerShell dei prerequisiti di Crittografia dischi di Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Installare gli strumenti e connettersi ad Azure

Per completare i passaggi descritti in questo articolo, è possibile usare l'[interfaccia della riga di comando di Azure](/cli/azure/), il [modulo Az di Azure PowerShell](/powershell/azure/) oppure il [portale di Azure](https://portal.azure.com). 

Mentre il portale è accessibile tramite il browser, l'interfaccia della riga di comando di Azure e Azure PowerShell richiedono l'installazione locale. Vedere la sezione "Installare gli strumenti e connettersi ad Azure" nell'argomento [Scenari di crittografia dischi di Azure per macchine virtuali Linux](disk-encryption-linux.md#install-tools-and-connect-to-azure).

### <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Prima di usare l'interfaccia della riga di comando di Azure o Azure PowerShell, è necessario connettersi alla sottoscrizione di Azure. A questo scopo, è possibile [eseguire l'accesso con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli), [eseguire l'accesso con Azure PowerShell](/powershell/azure/authenticate-azureps) oppure specificare le credenziali del portale di Azure quando richiesto.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>Passaggi successivi

- [Script dell'interfaccia della riga di comando dei prerequisiti di Crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell per i prerequisiti di Crittografia dischi di Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Scenari di crittografia dischi di Azure per macchine virtuali Linux](disk-encryption-linux.md)
- Informazioni su come [risolvere i problemi di Crittografia dischi di Azure](disk-encryption-troubleshooting.md)
- [Script di esempio per la Crittografia dischi di Azure](disk-encryption-sample-scripts.md)
