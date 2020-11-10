---
title: Funzionalità di sicurezza usate con le macchine virtuali di Azure
titleSuffix: Azure security
description: Questo articolo offre informazioni generali sulle funzionalità di sicurezza principali di Azure che possono essere usate con Macchine virtuali di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2019
ms.author: terrylan
ms.openlocfilehash: d22ce3941b00903be8532caaa36a9ce55e2f2c6f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409791"
---
# <a name="azure-virtual-machines-security-overview"></a>Informazioni generali sulla sicurezza di Macchine virtuali di Azure
Questo articolo fornisce una panoramica delle funzionalità di sicurezza principali di Azure che possono essere usate con le macchine virtuali.

Con le Macchine virtuali di Azure è possibile distribuire in modo flessibile un'ampia gamma di soluzioni di elaborazione. Il servizio supporta Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e servizi BizTalk di Azure. In questo modo è possibile distribuire qualsiasi carico di lavoro e implementare qualsiasi lingua su quasi tutti i sistemi operativi.

Una macchina virtuale di Azure offre la flessibilità della virtualizzazione senza dover acquistare e gestire l'hardware fisico su cui è in esecuzione la macchina virtuale. È possibile compilare e distribuire le applicazioni con la certezza che i dati saranno protetti e al sicuro nei nostri data center che offrono livelli di sicurezza elevati.

Con Azure è possibile creare soluzioni conformi con sicurezza avanzata che:

* Proteggono le macchine virtuali da virus e malware.
* Applicano la crittografia ai dati sensibili.
* Proteggono il traffico di rete.
* Identificano e rilevano minacce.
* Soddisfano i requisiti di conformità.  

## <a name="antimalware"></a>Antimalware

Con Azure è possibile usare software antimalware a cura dei principali fornitori di soluzioni di sicurezza, come Microsoft, Symantec, Trend Micro e Kaspersky. Questo software consente di proteggere le macchine virtuali da file dannosi, adware e altre minacce.

Microsoft Antimalware per Servizi cloud e Macchine virtuali di Azure è una funzionalità di protezione in tempo reale che aiuta a identificare e rimuovere virus, spyware e altro software dannoso.  Microsoft Antimalware per Azure offre avvisi configurabili quando software dannoso o indesiderato tenta l'installazione o l'esecuzione nei sistemi di Azure.

Microsoft Antimalware per Azure è una soluzione con un agente singolo per applicazioni e ambienti tenant, progettata per l'esecuzione in background senza intervento da parte dell'utente. È possibile distribuire la protezione in base alle esigenze dei carichi di lavoro dell'applicazione, con una configurazione sicura per impostazione predefinita o avanzata personalizzata, incluso il monitoraggio antimalware.

Scopri di più su [Microsoft antimalware per Azure](antimalware.md) e sulle funzionalità di base disponibili.

Per altre informazioni sul software antimalware per la protezione delle macchine virtuali:

* [Distribuzione di soluzioni antimalware in macchine virtuali di Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale Windows](../../virtual-machines/extensions/trend.md)
* [Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Windows](../../virtual-machines/extensions/symantec.md)
* [Soluzioni di sicurezza in Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

Per una protezione ancora più potente, è consigliabile usare [Windows Defender Advanced Threat Protection](/windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection). Windows Defender ATP offre i vantaggi seguenti:

* [Riduzione della superficie di attacco](/windows/security/threat-protection/windows-defender-atp/overview-attack-surface-reduction)  
* [Protezione di nuova generazione](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)  
* [Protezione di endpoint e azioni di risposta](/windows/security/threat-protection/windows-defender-atp/overview-endpoint-detection-response)
* [Indagini automatizzate e azioni di correzione](/windows/security/threat-protection/windows-defender-atp/automated-investigations-windows-defender-advanced-threat-protection)
* [Punteggio di sicurezza](/windows/security/threat-protection/microsoft-defender-atp/tvm-microsoft-secure-score-devices)
* [Ricerca avanzata](/windows/security/threat-protection/windows-defender-atp/overview-hunting-windows-defender-advanced-threat-protection)
* [Gestione e API](/windows/security/threat-protection/windows-defender-atp/management-apis)
* [Microsoft Threat Protection](/windows/security/threat-protection/windows-defender-atp/threat-protection-integration)

Altre informazioni:

* [Introduzione a Windows Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)  
* [Panoramica delle funzionalità di Windows Defender ATP](/windows/security/threat-protection/windows-defender-atp/overview)  

## <a name="hardware-security-module"></a>Modulo di protezione hardware

Le protezioni con crittografia e autenticazione possono essere migliorate aumentando la sicurezza delle chiavi. È possibile semplificare la gestione e la sicurezza di chiavi e segreti critici archiviandoli nell'insieme di credenziali delle chiavi di Azure.

L'insieme di credenziali delle chiavi consente di archiviare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 livello 2. Le chiavi di crittografia di SQL Server per backup o [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) possono essere tutte archiviate nell'insieme di credenziali delle chiavi con qualsiasi chiave o segreto delle applicazioni. Le autorizzazioni e l'accesso per questi elementi protetti vengono gestiti tramite [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Altre informazioni:

* [Cos'è l'insieme di credenziali chiave di Azure?](../../key-vault/general/overview.md)
* [Blog sull'insieme di credenziali delle chiavi di Azure](/archive/blogs/kv/)

## <a name="virtual-machine-disk-encryption"></a>Crittografia dischi delle macchine virtuali

Crittografia dischi di Azure è una nuova funzionalità che consente di crittografare i dischi delle macchine virtuali Windows e Linux. Crittografia dischi di Azure usa la funzionalità [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) standard di settore di Windows e la funzionalità [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per il sistema operativo e i dischi dati.

La soluzione è integrata con Azure Key Vault per consentire di controllare e gestire le chiavi di crittografia dei dischi e i segreti nella sottoscrizione dell'insieme di credenziali delle chiavi. Questa soluzione assicura anche che tutti i dati nei dischi delle macchine virtuali vengano crittografati quando inattivi in Archiviazione di Azure.

Altre informazioni:

* [Crittografia dischi di Azure per macchine virtuali IaaS](./azure-disk-encryption-vms-vmss.md)
* [Guida introduttiva: Crittografare una macchina virtuale IaaS Windows con Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)

## <a name="virtual-machine-backup"></a>Backup di una macchina virtuale

Backup di Azure è una soluzione scalabile che consente di proteggere i dati delle applicazioni senza investimenti di capitale e con costi operativi minimi. Gli errori delle applicazioni possono danneggiare i dati e gli errori umani possono comportare l'introduzione di bug nelle applicazioni. Con Backup di Azure, le macchine virtuali che eseguono Windows e Linux sono protette.

Altre informazioni:

* [Informazioni su Backup di Azure](../../backup/backup-overview.md)
* [Domande frequenti sul servizio backup di Azure](../../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery

Una parte importante della strategia BCDR dell'organizzazione è capire come mantenere in esecuzione le app e i carichi di lavoro aziendali quando si verificano interruzioni pianificate e non pianificate. Azure Site Recovery consente di orchestrare la replica, il failover e il ripristino di carichi di lavoro e app in modo che siano disponibili da una posizione secondaria in caso di inattività di quella primaria.

Site Recovery:

* **Semplifica la strategia BCDR** : Site Recovery consente di gestire la replica, il failover e il ripristino di più carichi di lavoro e app aziendali da un'unica posizione. Site Recovery orchestra la replica e il failover, ma non intercetta i dati dell'applicazione né raccoglie le relative informazioni.
* **Offre una modalità di replica flessibile** : con Site Recovery è possibile replicare i carichi di lavoro in esecuzione in macchine virtuali Hyper-V, macchine virtuali VMware e server fisici Windows o Linux.
* **Supporta failover e ripristino** : Site Recovery consente il failover di test per supportare analisi del ripristino di emergenza senza interessare gli ambienti di produzione. È anche possibile eseguire failover pianificati senza perdita di dati per interruzioni previste o il failover non pianificato con perdita di dati minima, in base alla frequenza di replica, per emergenze impreviste. Dopo il failover è possibile eseguire il failback nei siti primari. Site Recovery fornisce i piani di ripristino che possono includere script e cartelle di lavoro di automazione di Azure per personalizzare il failover e il ripristino di applicazioni multilivello.
* **Elimina i data center secondari** : è possibile eseguire la replica in un sito secondario locale o in Azure. L'uso di Azure come destinazione per il ripristino di emergenza elimina i costi e la complessità correlati alla gestione di un sito secondario. I dati replicati vengono archiviati nell'archiviazione di Azure.
* **Si integra con le tecnologie BCDR esistenti** : Site Recovery interagisce con altre funzionalità BCDR dell'applicazione. È ad esempio possibile usare Site Recovery per proteggere il back-end SQL Server dei carichi di lavoro aziendali. È incluso il supporto nativo per SQL Server AlwaysOn, per la gestione del failover dei gruppi di disponibilità.

Altre informazioni:

* [Che cos'è Azure Site Recovery?](../../site-recovery/site-recovery-overview.md)
* [In che modo Azure Site Recovery funziona?](/azure/security/fundamentals/azure-to-azure-architecture)
* [Quali carichi di lavoro sono protetti da Azure Site Recovery?](../../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Reti virtuali

La connettività di rete è indispensabile per le macchine virtuali. Per supportare questo requisito, Azure richiede che le macchine virtuali siano connesse a una rete virtuale di Azure.

Una rete virtuale di Azure è un costrutto logico basato sull'infrastruttura di rete fisica di Azure. Ogni rete virtuale di Azure logica è isolata da tutte le altre reti virtuali di Azure. L'isolamento garantisce che il traffico di rete nelle distribuzioni di un utente non sia accessibile da altri clienti di Microsoft Azure.

Altre informazioni:

* [Panoramica della sicurezza di rete di Azure](network-overview.md)
* [Panoramica della rete virtuale](../../virtual-network/virtual-networks-overview.md)
* [Funzionalità di rete e partnership per scenari aziendali](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestione e reporting dei criteri di sicurezza

Il Centro sicurezza di Azure consente di prevenire, rilevare e gestire le minacce. Il Centro sicurezza offre maggiore visibilità e controllo sulla sicurezza delle risorse di Azure. Integra il monitoraggio della sicurezza e la gestione dei criteri in tutte le sottoscrizioni di Azure. Aiuta a rilevare le minacce che potrebbero altrimenti passare inosservate e opera con un ampio ecosistema di soluzioni per la sicurezza.

Il Centro sicurezza consente di ottimizzare e monitorare la sicurezza delle macchine virtuali:

* Fornendo [consigli sulla sicurezza](../../security-center/security-center-recommendations.md) per le macchine virtuali. I suggerimenti di esempio includono: applicare gli aggiornamenti del sistema, configurare gli endpoint ACL, abilitare antimalware, abilitare i gruppi di sicurezza di rete e applicare la crittografia del disco.
* Monitorando lo stato delle macchine virtuali.

Altre informazioni:

* [Introduzione al Centro sicurezza di Azure](../../security-center/security-center-introduction.md)
* [Domande frequenti sul Centro sicurezza di Azure](../../security-center/faq-general.md)
* [Pianificazione e gestione del Centro sicurezza di Azure](../../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformità

Macchine virtuali di Azure ha ottenuto le certificazioni per FISMA, FedRAMP, HIPAA, PCI DSS Livello 1 e altri importanti programmi di conformità. La certificazione consente alle applicazioni di Azure di soddisfare i requisiti di conformità e all'azienda di rispondere a un'ampia gamma di requisiti normativi locali e internazionali.

Altre informazioni:

* [Centro protezione Microsoft - Conformità](https://www.microsoft.com/en-us/trustcenter/compliance)
* [Trusted Cloud: sicurezza, privacy e conformità di Microsoft Azure](https://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)

## <a name="confidential-computing"></a>Confidential computing

Sebbene il computing riservato non sia tecnicamente parte della sicurezza delle macchine virtuali, l'argomento relativo alla sicurezza della macchina virtuale appartiene al soggetto di livello superiore della sicurezza "calcolo". Il computing riservato appartiene alla categoria "calcolo" della sicurezza.

Il computing riservato garantisce che, quando i dati sono "in chiaro", che è necessario per un'elaborazione efficiente, i dati sono protetti in un ambiente di esecuzione attendibile  https://en.wikipedia.org/wiki/Trusted_execution_environment (Tee, noto anche come enclave), un esempio di come illustrato nella figura seguente.  

Gli ambienti di esecuzione attendibili impediscono qualsiasi tentativo di visualizzare operazioni o dati interni dall'esterno, anche con un debugger. Consentono inoltre l'accesso ai dati solo a codice autorizzato. Se il codice è stato modificato o manomesso, le operazioni vengono negate e l'ambiente viene disabilitato. Gli ambienti di esecuzione attendibili applicano queste protezioni a tutto il ciclo di esecuzione del codice in essi contenuto.

Altre informazioni:

* [Introducing Azure confidential computing](https://azure.microsoft.com/blog/introducing-azure-confidential-computing/) (Introduzione al confidential computing di Azure)  
* [Confidential computing di Azure](https://azure.microsoft.com/blog/azure-confidential-computing/)  

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle [procedure di sicurezza consigliate](iaas.md) per le macchine virtuali e i sistemi operativi.