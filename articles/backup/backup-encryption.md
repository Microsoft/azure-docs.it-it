---
title: Crittografia in Backup di Azure
description: Informazioni su come le funzionalità di crittografia in backup di Azure consentono di proteggere i dati di backup e soddisfare le esigenze di sicurezza dell'azienda.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: c9b1b2782a34285ae194f2998a7cd053cf3c0c70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96325680"
---
# <a name="encryption-in-azure-backup"></a>Crittografia in Backup di Azure

Tutti i dati di cui è stato eseguito il backup vengono crittografati automaticamente quando vengono archiviati nel cloud usando la crittografia di archiviazione di Azure, che consente di soddisfare gli impegni di sicurezza e conformità. I dati inattivi vengono crittografati usando la crittografia AES a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2. Oltre alla crittografia dei dati inattivi, tutti i dati di backup in transito vengono trasferiti tramite HTTPS. Rimane sempre nella rete backbone di Azure.

## <a name="levels-of-encryption-in-azure-backup"></a>Livelli di crittografia in backup di Azure

Backup di Azure include la crittografia su due livelli:

- **Crittografia dei dati nell'insieme di credenziali di servizi di ripristino**
  - **Uso delle chiavi gestite dalla piattaforma**: per impostazione predefinita, tutti i dati vengono crittografati usando chiavi gestite dalla piattaforma. Non è necessario eseguire alcuna azione esplicita da parte dell'utente per abilitare questa crittografia. si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di Servizi di ripristino.
  - **Uso delle chiavi gestite dal cliente**: quando si esegue il backup delle macchine virtuali di Azure, è possibile scegliere di crittografare i dati usando chiavi di crittografia di proprietà e gestite dall'utente. Backup di Azure consente di usare le chiavi RSA archiviate nel Azure Key Vault per la crittografia dei backup. La chiave di crittografia usata per crittografare i backup può essere diversa da quella usata per l'origine. I dati vengono protetti usando una chiave di crittografia dei dati basata su AES 256, che a sua volta è protetta usando le chiavi. In questo modo si ottiene il controllo completo sui dati e sulle chiavi. Per consentire la crittografia, è necessario concedere all'insieme di credenziali dei servizi di ripristino l'accesso alla chiave di crittografia nel Azure Key Vault. È possibile disabilitare la chiave o revocare l'accesso ogni volta che è necessario. Tuttavia, è necessario abilitare la crittografia usando le chiavi prima di provare a proteggere gli elementi nell'insieme di credenziali. Fare clic [qui](encryption-at-rest-with-cmk.md) per altre informazioni.
  - **Crittografia a livello di infrastruttura**: oltre a crittografare i dati nell'insieme di credenziali di servizi di ripristino usando chiavi gestite dal cliente, è anche possibile scegliere di disporre di un ulteriore livello di crittografia configurato nell'infrastruttura di archiviazione. Questa crittografia dell'infrastruttura viene gestita dalla piattaforma. Insieme alla crittografia dei dati inattivi con chiavi gestite dal cliente, consente la crittografia a due livelli dei dati di backup. La crittografia dell'infrastruttura può essere configurata solo se si sceglie prima di tutto di usare le proprie chiavi per la crittografia dei computer inattivi. La crittografia dell'infrastruttura USA chiavi gestite dalla piattaforma per la crittografia dei dati.
- **Crittografia specifica del carico di lavoro di cui viene eseguito il backup**  
  - **Backup di macchine virtuali di Azure**: backup di Azure supporta il backup di macchine virtuali con dischi crittografati tramite [chiavi gestite dalla piattaforma](../virtual-machines/disk-encryption.md#platform-managed-keys), nonché [chiavi gestite dal cliente](../virtual-machines/disk-encryption.md#customer-managed-keys) di proprietà e gestite dall'utente. Inoltre, è anche possibile eseguire il backup delle macchine virtuali di Azure con il sistema operativo o i dischi dati crittografati tramite [crittografia dischi di Azure](backup-azure-vms-encryption.md#encryption-support-using-ade). ADE USA BitLocker per le macchine virtuali Windows e DM-Crypt per le macchine virtuali Linux, per eseguire la crittografia nel guest.

>[!NOTE]
>La crittografia dell'infrastruttura è attualmente in anteprima limitata ed è disponibile solo nelle aree Stati Uniti orientali, Stati Uniti West2, Stati Uniti centro-meridionali, US Gov Arizona e US GOV Virginia. Se si vuole usare la funzionalità in una di queste aree, compilare [questo modulo](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) e inviare un messaggio di posta elettronica all'indirizzo [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](../storage/common/storage-service-encryption.md)
- Domande [frequenti su backup di Azure](backup-azure-backup-faq.md#encryption) per eventuali domande sulla crittografia