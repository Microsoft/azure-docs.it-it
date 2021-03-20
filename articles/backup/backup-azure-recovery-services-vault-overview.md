---
title: Panoramica degli insiemi di credenziali di Servizi di ripristino
description: Panoramica degli insiemi di credenziali dei servizi di ripristino.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 0ed37446e1ccf0780f924143c8f063964adf0004
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755116"
---
# <a name="recovery-services-vaults-overview"></a>Panoramica di insiemi di credenziali di Servizi di ripristino

In questo articolo vengono descritte le funzionalità di un insieme di credenziali di Servizi di ripristino. Un insieme di credenziali di Servizi di ripristino è un'entità di archiviazione di Azure che ospita i dati. I dati sono in genere copie di dati o informazioni di configurazione per macchine virtuali, carichi di lavoro, server o workstation. È possibile usare gli insiemi di credenziali di Servizi di ripristino per contenere dati di backup per vari servizi di Azure, ad esempio database SQL di Azure e macchine virtuali IaaS, Linux o Windows. Gli insiemi di credenziali di Servizi di ripristino supportano System Center DPM, Windows Server, server di Backup di Azure e altro ancora. Gli insiemi di credenziali di Servizi di ripristino semplificano l'organizzazione dei dati di backup, riducendo al minimo l'overhead di gestione. Gli insiemi di credenziali dei servizi di ripristino sono basati sul modello di Azure Resource Manager di Azure, che offre funzionalità come:

- **Funzionalità avanzate per proteggere i dati di backup**: con gli insiemi di credenziali di Servizi di ripristino, Backup di Azure offre funzionalità di sicurezza per proteggere i backup nel cloud. Queste funzionalità di sicurezza garantiscono la protezione dei backup e ripristinano i dati in modo sicuro anche se i server di produzione e di backup vengono compromessi. [Scopri di più](backup-azure-security-feature.md)

- **Monitoraggio centralizzato per l'ambiente IT ibrido**: con gli insiemi di credenziali di Servizi di ripristino, è possibile monitorare non solo le [macchine virtuali IaaS di Azure](backup-azure-manage-vms.md) ma anche le [risorse locali](backup-azure-manage-windows-server.md#manage-backup-items) da un portale centrale. [Scopri di più](backup-azure-monitoring-built-in-monitor.md)

- **Controllo degli accessi in base al ruolo di Azure (RBAC** di Azure): il controllo degli accessi in base al ruolo di Azure offre un controllo di gestione degli accessi [Azure offre diversi ruoli predefiniti](../role-based-access-control/built-in-roles.md) mentre Backup di Azure dispone di tre [ruoli predefiniti per la gestione dei punti di ripristino](backup-rbac-rs-vault.md). Gli insiemi di credenziali dei servizi di ripristino sono compatibili con il controllo degli accessi in base al ruolo di Azure, che limita l'accesso al set di ruoli utente definito. [Scopri di più](backup-rbac-rs-vault.md)

- **Eliminazione** temporanea: con l'eliminazione temporanea, anche se un attore malintenzionato Elimina un backup (o i dati di backup vengono accidentalmente eliminati), i dati di backup vengono conservati per 14 giorni aggiuntivi, consentendo il ripristino di tale elemento di backup senza perdita di dati. Gli altri 14 giorni di conservazione per i dati di backup nello stato "eliminazione temporanea" non comportano alcun costo. [Altre informazioni](backup-azure-security-feature-cloud.md)

- **Ripristino tra più aree**: il ripristino tra più aree (CRR) consente di ripristinare macchine virtuali di Azure in un'area secondaria, ovvero un'area abbinata ad Azure. Abilitando questa funzionalità a [livello](backup-create-rs-vault.md#set-cross-region-restore)di insieme di credenziali, è possibile ripristinare i dati replicati nell'area secondaria in qualsiasi momento, quando si sceglie. In questo modo è possibile ripristinare i dati dell'area secondaria per la conformità del controllo e durante gli scenari di interruzione, senza attendere che Azure dichiari un'emergenza (a differenza delle impostazioni GRS dell'insieme di credenziali). [Altre informazioni](backup-azure-arm-restore-vms.md#cross-region-restore)

## <a name="storage-settings-in-the-recovery-services-vault"></a>Impostazioni di archiviazione nell'insieme di credenziali di servizi di ripristino

Un insieme di credenziali dei Servizi di ripristino è un'entità che archivia i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup associati alle macchine virtuali protette.

- Backup di Azure gestisce automaticamente lo spazio di archiviazione per l'insieme di credenziali. Vedere come [è possibile modificare le impostazioni di archiviazione](./backup-create-rs-vault.md#set-storage-redundancy).

- Per altre informazioni sulla ridondanza di archiviazione, vedere gli articoli relativi alla ridondanza [geografica](../storage/common/storage-redundancy.md#geo-zone-redundant-storage), [locale](../storage/common/storage-redundancy.md#locally-redundant-storage) e di [zona](../storage/common/storage-redundancy.md#zone-redundant-storage) .

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Impostazioni di crittografia nell'insieme di credenziali di servizi di ripristino

Questa sezione illustra le opzioni disponibili per la crittografia dei dati di backup archiviati nell'insieme di credenziali di servizi di ripristino.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>Crittografia dei dati di backup tramite chiavi gestite dalla piattaforma

Per impostazione predefinita, tutti i dati vengono crittografati usando chiavi gestite dalla piattaforma. Non è necessario eseguire alcuna azione esplicita da parte dell'utente per abilitare questa crittografia. si applica a tutti i carichi di lavoro di cui viene eseguito il backup nell'insieme di credenziali di Servizi di ripristino.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>Crittografia dei dati di backup tramite chiavi gestite dal cliente

È possibile scegliere di crittografare i dati usando chiavi di crittografia di proprietà e gestite dall'utente. Backup di Azure consente di usare le chiavi RSA archiviate nel Azure Key Vault per la crittografia dei backup. La chiave di crittografia usata per crittografare i backup può essere diversa da quella usata per l'origine. I dati vengono protetti usando una chiave di crittografia dei dati basata su AES 256, che a sua volta è protetta usando le chiavi. In questo modo si ottiene il controllo completo sui dati e sulle chiavi. Per consentire la crittografia, è necessario concedere all'insieme di credenziali dei servizi di ripristino l'accesso alla chiave di crittografia nel Azure Key Vault. È possibile disabilitare la chiave o revocare l'accesso ogni volta che è necessario. Tuttavia, è necessario abilitare la crittografia usando le chiavi prima di provare a proteggere gli elementi nell'insieme di credenziali.

Scopri di più su come crittografare i dati di backup [usando chiavi gestite dal cliente](encryption-at-rest-with-cmk.md).

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) è un consulente cloud personalizzato che consente di ottimizzare l'uso di Azure. Analizza l'utilizzo di Azure e fornisce indicazioni tempestive per ottimizzare e proteggere le distribuzioni. Fornisce consigli in quattro categorie: disponibilità elevata, sicurezza, prestazioni e costi.

Azure Advisor fornisce le [raccomandazioni](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion) orarie per le macchine virtuali di cui non è stato eseguito il backup, quindi non è possibile eseguire il backup di macchine virtuali importanti. È anche possibile controllare le raccomandazioni eseguendone la ripetizione.  È possibile selezionare la raccomandazione e abilitare il backup sulle VM inline specificando l'insieme di credenziali (in cui verranno archiviati i backup) e i criteri di backup (pianificazione dei backup e conservazione delle copie di backup).

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Scenari supportati e non supportati per l'insieme di credenziali](backup-support-matrix.md#vault-support)
- [Domande frequenti sull'insieme di credenziali](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti:

- [Eseguire il backup di una macchina virtuale IaaS](backup-azure-arm-vms-prepare.md)
- [Eseguire il backup del server di Backup di Azure](backup-azure-microsoft-azure-backup.md)
- [Eseguire il backup di Windows Server](backup-windows-with-mars-agent.md)
