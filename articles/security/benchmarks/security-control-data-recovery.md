---
title: Controllo di sicurezza di Azure-ripristino dei dati
description: Ripristino dei dati del controllo di sicurezza di Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408577"
---
# <a name="security-control-data-recovery"></a>Controllo di sicurezza: ripristino dei dati

Assicurarsi che tutti i dati di sistema, le configurazioni e i segreti vengano automaticamente sottoposti a backup a intervalli regolari.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1: garantire l'esecuzione regolare di backup automatizzati

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 9.1 | 10.1 | Customer |

Abilitare backup di Azure e configurare l'origine di backup (VM di Azure, SQL Server o condivisioni file), nonché la frequenza e il periodo di memorizzazione desiderati.

- [Come abilitare backup di Azure](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: eseguire backup completi del sistema e il backup di tutte le chiavi gestite dal cliente

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 9.2 | 10.2 | Customer |

Abilitare backup di Azure e VM di destinazione, nonché i periodi di conservazione e frequenza desiderati. Eseguire il backup delle chiavi gestite dal cliente in Azure Key Vault.

- [Come abilitare backup di Azure](https://docs.microsoft.com/azure/backup/)

- [Come eseguire il backup di chiavi di Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: convalidare tutti i backup, incluse le chiavi gestite dal cliente

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 9.3 | 10.3 | Customer |

Verificare la possibilità di eseguire periodicamente il ripristino dei dati del contenuto all'interno di backup di Azure. Testare il ripristino delle chiavi gestite dal cliente sottoposte a backup.

- [Come ripristinare i file dal backup della macchina virtuale di Azure](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Come ripristinare le chiavi di Key Vault in Azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: garantire la protezione dei backup e delle chiavi gestite dal cliente

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 9.4 | 10,4 | Customer |

Per il backup in locale, la crittografia dei dati inattivi viene eseguita con l'uso della passphrase immessa durante il backup in Azure. Per le macchine virtuali di Azure, i dati inattivi sono crittografati usando la crittografia del servizio di archiviazione. Usare il controllo degli accessi in base al ruolo per proteggere i backup e le chiavi gestite dal cliente.  

Abilitare l'eliminazione temporanea e ripulire la protezione in Key Vault per proteggere le chiavi da eliminazioni accidentali o dannose.  Se si usa archiviazione di Azure per archiviare i backup, abilitare l'eliminazione temporanea per salvare e ripristinare i dati quando vengono eliminati BLOB o snapshot BLOB. 

- [Informazioni sul controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Come abilitare l'eliminazione temporanea e ripulire la protezione in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Eliminazione temporanea per i BLOB di Archiviazione di Azure ](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>Passaggi successivi

- Vedere il controllo di sicurezza successivo: [risposta agli eventi imprevisti](security-control-incident-response.md)