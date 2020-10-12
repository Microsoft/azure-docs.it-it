---
title: Contenitore di profili di archiviazione FSLogix desktop virtuale Windows-Azure
description: Opzioni per l'archiviazione del profilo FSLogix di desktop virtuale Windows in archiviazione di Azure.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b1a5e36232e74caa34037efbbb0da0c39051998
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89568694"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Opzioni di archiviazione per i contenitori del profilo FSLogix in desktop virtuale di Windows

Azure offre più soluzioni di archiviazione che è possibile usare per archiviare il contenitore del profilo FSLogix. Questo articolo confronta le soluzioni di archiviazione offerte da Azure per i contenitori di profili utente FSLogix per desktop virtuale di Windows. Per la maggior parte dei clienti, è consigliabile archiviare i contenitori del profilo FSLogix in File di Azure.

Desktop virtuale di Windows offre i contenitori del profilo FSLogix come soluzione di profilo utente consigliata. FSLogix è progettato per eseguire il roaming dei profili in ambienti di elaborazione remoti, ad esempio Desktop virtuale Windows. Al momento dell'accesso, questo contenitore viene collegato dinamicamente all'ambiente di elaborazione usando un disco rigido virtuale (VHD) supportato in modo nativo e un disco rigido virtuale Hyper-V (VHDX). Il profilo utente è immediatamente disponibile e viene visualizzato nel sistema esattamente come un profilo utente nativo.

Le tabelle seguenti confrontano le soluzioni di archiviazione offerte da archiviazione di Azure per i profili utente del contenitore del profilo FSLogix di desktop virtuali Windows.

## <a name="azure-platform-details"></a>Dettagli della piattaforma Azure

|Funzionalità|File di Azure|Azure NetApp Files|Spazi di archiviazione diretta|
|--------|-----------|------------------|---------------------|
|Caso d'uso|Scopo generico|Prestazioni o migrazione ultra da NetApp locale|Multipiattaforma|
|Servizio piattaforma|Sì, soluzione nativa di Azure|Sì, soluzione nativa di Azure|No, self-Managed|
|Disponibilità a livello di area|Tutte le aree|[Selezionare le aree](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Tutte le aree|
|Ridondanza|Ridondanza locale/con ridondanza della zona/con ridondanza geografica|Con ridondanza locale|Ridondanza locale/con ridondanza della zona/con ridondanza geografica|
|Livelli e prestazioni|Standard<br>Premium<br>Fino a un massimo di 100.000 IOPS per condivisione con 5 GBps per condivisione a circa 3 ms di latenza|Standard<br>Premium<br>Ultra<br>Fino a 320K (16K) IOPS con 4,5 GBps per volume a circa 1 ms di latenza|HDD Standard: fino a 500 IOPS per disco<br>SDD Standard: limiti fino a 4K IOPS per disco<br>SSD Premium: fino a 20.000 IOPS per disco<br>È consigliabile usare dischi Premium per Spazi di archiviazione diretta|
|Capacità|100 TiB per condivisione|100 TiB per volume, fino a 12,5 PiB per sottoscrizione|Massimo 32 TiB per disco|
|Infrastruttura necessaria|Dimensioni minime condivisione 1 GiB|Pool di capacità minimo 4 TiB, dimensione minima del volume 100 GiB|Due macchine virtuali in Azure IaaS (+ cloud Witness) o almeno tre VM senza e i costi per i dischi|
|Protocolli|SMB 2.1/3. e REST|NFSv3, NFSv 4.1 (anteprima), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Dettagli sulla gestione di Azure

|Funzionalità|File di Azure|Azure NetApp Files|Spazi di archiviazione diretta|
|--------|-----------|------------------|---------------------|
|Access|Cloud, locale e ibrido (sincronizzazione file di Azure)|Cloud, locale (tramite ExpressRoute)|Cloud, locale|
|Backup|Integrazione di snapshot di backup di Azure|Snapshot Azure NetApp Files|Integrazione di snapshot di backup di Azure|
|Sicurezza e conformità|[Tutti i certificati supportati di Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO completato|[Tutti i certificati supportati di Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integrazione di Azure Active Directory|[Active Directory e Azure Active Directory Domain Services nativi](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Azure Active Directory Domain Services e Active Directory nativo](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Active Directory nativo o solo supporto Azure Active Directory Domain Services|

Una volta scelto il metodo di archiviazione, vedere [prezzi di desktop virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/) per informazioni sui piani tariffari.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui contenitori di profili FSLogix, i dischi dei profili utente e altre tecnologie per i profili utente, vedere la tabella in [contenitori di profili FSLogix e file di Azure](fslogix-containers-azure-files.md).

Se si è pronti per creare i propri contenitori di profili FSLogix, iniziare con una di queste esercitazioni:

- [Introduzione ai contenitori di profili di FSLogix su File di Azure nel desktop virtuale di Windows](create-file-share.md)
- [Creare un contenitore di profili FSLogix per un pool host usando i file NetApp di Azure](create-fslogix-profile-container.md)
- Le istruzioni in [distribuire una file server con scalabilità orizzontale spazi di archiviazione diretta a due nodi per l'archiviazione UPD in Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) si applicano anche quando si usa un contenitore di profili FSLogix anziché un disco del profilo utente

È anche possibile iniziare dall'inizio e configurare la propria soluzione desktop virtuale Windows in [creare un tenant in un desktop virtuale di Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
