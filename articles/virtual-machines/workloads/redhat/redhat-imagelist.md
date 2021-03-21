---
title: Immagini Red Hat Enterprise Linux disponibili in Azure
description: Informazioni sulle immagini Red Hat Enterprise Linux in Microsoft Azure
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 0a4d2ef5b7f367130151fabda3f1d97b65605931
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676042"
---
# <a name="red-hat-enterprise-linux-rhel-images-available-in-azure"></a>Immagini Red Hat Enterprise Linux (RHEL) disponibili in Azure
Azure offre un'ampia gamma di immagini RHEL per diversi casi d'uso.

> [!NOTE]
> Tutte le immagini RHEL sono disponibili nei cloud pubblici di Azure e Azure per enti pubblici. Non sono disponibili nei cloud di Azure Cina.

## <a name="list-of-rhel-images"></a>Elenco di immagini RHEL
Questo è un elenco di immagini RHEL disponibili in Azure. Se non diversamente specificato, tutte le immagini sono partizionate in LVM e collegate a normali repository RHEL (non EUS, non E4S). Le immagini seguenti sono attualmente disponibili per l'uso generale:

> [!NOTE]
> Le immagini non ELABORAte non vengono più prodotte a favore delle immagini partizionate LVM. LVM offre diversi vantaggi rispetto allo schema di partizionamento RAW (non LVM) più vecchio, incluse le opzioni di ridimensionamento delle partizioni notevolmente più flessibili.

Offerta| SKU | Partizionamento | Provisioning | Note
:----|:----|:-------------|:-------------|:-----
RHEL          | 6.7      | RAW    | Agente Linux | Supporto del ciclo di vita esteso disponibile dal 1 ° dicembre. [Per altri dettagli, vedere qui.](redhat-extended-lifecycle-support.md)
|             | 6.8      | RAW    | Agente Linux | Supporto del ciclo di vita esteso disponibile dal 1 ° dicembre. [Per altri dettagli, vedere qui.](redhat-extended-lifecycle-support.md)
|             | 6.9      | RAW    | Agente Linux | Supporto del ciclo di vita esteso disponibile dal 1 ° dicembre. [Per altri dettagli, vedere qui.](redhat-extended-lifecycle-support.md)
|             | 6.10     | RAW    | Agente Linux | Supporto del ciclo di vita esteso disponibile dal 1 ° dicembre. [Per altri dettagli, vedere qui.](redhat-extended-lifecycle-support.md)
|             | 7-RAW    | RAW    | Agente Linux | Famiglia di immagini RHEL 7. x. <br> Collegato a repository regolari per impostazione predefinita (non EUS).
|             | 7-LVM    | LVM    | Agente Linux | Famiglia di immagini RHEL 7. x. <br> Collegato a repository regolari per impostazione predefinita (non EUS). Se si sta cercando un'immagine RHEL standard da distribuire, usare questo set di immagini e/o la relativa controparte di seconda generazione.
|             | 7lvm-gen2| LVM    | Agente Linux | Famiglia di immagini di generazione 2, RHEL 7. x. <br> Collegato a repository regolari per impostazione predefinita (non EUS). Se si sta cercando un'immagine RHEL standard da distribuire, usare questo set di immagini e/o la relativa controparte di generazione 1.
|             | 7-RAW-CI | RAW-CI | Cloud-init  | Famiglia di immagini RHEL 7. x. <br> Collegato a repository regolari per impostazione predefinita (non EUS).
|             | 7.2      | RAW    | Agente Linux |
|             | 7.3      | RAW    | Agente Linux |
|             | 7.4      | RAW    | Agente Linux | Allegato ai repository EUS per impostazione predefinita, a partire dal 2019 aprile.
|             | 74-Gen2  | RAW    | Agente Linux | Allegato ai repository EUS per impostazione predefinita.
|             | 7,5      | RAW    | Agente Linux | Allegato ai repository EUS per impostazione predefinita a partire dal 2019 giugno.
|             | 75-Gen2  | RAW    | Agente Linux | Allegato ai repository EUS per impostazione predefinita.
|             | 7.6      | RAW    | Agente Linux | Collegato ai repository EUS per impostazione predefinita a partire dal 2019 maggio.
|             | 76-Gen2  | RAW    | Agente Linux | Allegato ai repository EUS per impostazione predefinita.
|             | 7.7      | LVM    | Agente Linux | Allegato ai repository EUS per impostazione predefinita.
|             | 77-gen2  | LVM    | Agente Linux | Allegato ai repository EUS per impostazione predefinita.
|             | 7.8      | LVM    | Agente Linux | Allegati a repository regolari (EUS non disponibile per RHEL 7,8)
|             | 78-Gen2  | LVM    | Agente Linux | Allegati a repository regolari (EUS non disponibile per RHEL 7,8)
|             | 7.9      | LVM    | Agente Linux | Allegati a repository regolari (EUS non disponibile per RHEL 7,9)
|             | 79-Gen2  | LVM    | Agente Linux | Allegati a repository regolari (EUS non disponibile per RHEL 7,9)
|             | 8-LVM    | LVM    | Agente Linux | Famiglia di immagini RHEL 8. x. Collegato a repository regolari.
|             | 8-LVM-Gen2| LVM    | Agente Linux | Famiglia di immagini Hyper-V Generation 2-RHEL 8. x. Collegato a repository regolari.
|             | 8        | LVM    | Agente Linux | Immagini RHEL 8,0.
|             | 8-Gen2   | LVM    | Agente Linux | Hyper-V Generation 2-RHEL 8,0 images.
|             | 8.1      | LVM    | Agente Linux | Allegato ai repository EUS per impostazione predefinita.
|             | 81gen2   | LVM    | Agente Linux | Hyper-V Generation 2: allegato a repository EUS a partire dal 2020 novembre.
|             | 8.1-ci   | LVM    | Agente Linux | Allegato ai repository EUS a partire dal 2020 novembre.
|             | 81-ci-gen2| LVM    | Agente Linux | Hyper-V Generation 2: allegato a repository EUS a partire dal 2020 novembre.
|             | 8.2      | LVM    | Agente Linux | Allegato ai repository EUS a partire dal 2020 novembre.
|             | 82gen2   | LVM    | Agente Linux | Hyper-V Generation 2: allegato a repository EUS a partire dal 2020 novembre.
|             | 8.3   | LVM    | Agente Linux |  Allegati a repository regolari (EUS non disponibile per RHEL 8,3)
|             | 83-Gen2   | LVM    | Agente Linux |Generazione 2 di Hyper-V-collegata a repository regolari (EUS non disponibile per RHEL 8,3)
RHEL-SAP      | 7.4      | LVM    | Agente Linux | RHEL 7,4 per app aziendali e SAP HANA. Collegato ai repository E4S, addebiterà un Premium per SAP e RHEL, oltre alla tariffa di calcolo di base.
|             | 74sap-Gen2| LVM    | Agente Linux | RHEL 7,4 per app aziendali e SAP HANA. Immagine di generazione 2. Collegato ai repository E4S, addebiterà un Premium per SAP e RHEL, oltre alla tariffa di calcolo di base.
|             | 7,5       | LVM    | Agente Linux | RHEL 7,5 per app aziendali e SAP HANA. Collegato ai repository E4S, addebiterà un Premium per SAP e RHEL, oltre alla tariffa di calcolo di base.
|             | 75sap-Gen2| LVM    | Agente Linux | RHEL 7,5 per app aziendali e SAP HANA. Immagine di generazione 2. Collegato ai repository E4S, addebiterà un Premium per SAP e RHEL, oltre alla tariffa di calcolo di base.
|             | 7.6       | LVM    | Agente Linux | RHEL 7,6 per app aziendali e SAP HANA. Collegato ai repository E4S, addebiterà un Premium per SAP e RHEL, oltre alla tariffa di calcolo di base.
|             | 76sap-Gen2| LVM    | Agente Linux | RHEL 7,6 per app aziendali e SAP HANA. Immagine di generazione 2. Collegato ai repository E4S, addebiterà un Premium per SAP e RHEL, oltre alla tariffa di calcolo di base.
|             | 7.7       | LVM    | Agente Linux | RHEL 7,7 per app aziendali e SAP HANA. Collegato ai repository E4S, addebiterà un Premium per SAP e RHEL, oltre alla tariffa di calcolo di base.
RHEL-SAP-HANA (da rimuovere nel 2020 novembre) | 6.7       | RAW    | Agente Linux | RHEL 6,7 per SAP HANA. Obsoleto a favore delle immagini RHEL-SAP. Questa immagine verrà rimossa nel 2020 novembre. Altre informazioni sulle offerte cloud SAP di Red Hat sono disponibili [qui](https://access.redhat.com/articles/3751271).
|             | 7.2       | LVM    | Agente Linux | RHEL 7,2 per SAP HANA. Obsoleto a favore delle immagini RHEL-SAP. Questa immagine verrà rimossa nel 2020 novembre. Altre informazioni sulle offerte cloud SAP di Red Hat sono disponibili [qui](https://access.redhat.com/articles/3751271).
|             | 7.3       | LVM    | Agente Linux | RHEL 7,3 per SAP HANA. Obsoleto a favore delle immagini RHEL-SAP. Questa immagine verrà rimossa nel 2020 novembre. Altre informazioni sulle offerte cloud SAP di Red Hat sono disponibili [qui](https://access.redhat.com/articles/3751271).
RHEL-SAP-APPS | 6.8       | RAW    | Agente Linux | RHEL 6,8 per SAP Business Applications. Obsoleto a favore delle immagini RHEL-SAP.
|             | 7.3       | LVM    | Agente Linux | RHEL 7,3 per SAP Business Applications. Obsoleto a favore delle immagini RHEL-SAP.
|             | 7.4       | LVM    | Agente Linux | RHEL 7,4 per SAP Business Applications.
|             | 7.6       | LVM    | Agente Linux | RHEL 7,6 per SAP Business Applications.
|             | 7.7       | LVM    | Agente Linux | RHEL 7,7 per SAP Business Applications.
|             | 77-gen2       | LVM    | Agente Linux | RHEL 7,7 per SAP Business Applications. Immagine di generazione 2
|             | 8.1       | LVM    | Agente Linux | RHEL 8,1 per SAP Business Applications.
|             | 81-Gen2      | LVM    | Agente Linux | RHEL 8,1 per SAP Business Applications. Immagine di generazione 2.
|             | 8.2       | LVM    | Agente Linux | RHEL 8,2 per SAP Business Applications.
|             | 82-Gen2      | LVM    | Agente Linux | RHEL 8,2 per SAP Business Applications. Immagine di generazione 2.
RHEL-HA       | 7.4       | LVM    | Agente Linux | RHEL 7,4 con componente aggiuntivo a disponibilità elevata. In viene addebitata una tariffa Premium per la disponibilità elevata e RHEL oltre alla tariffa di calcolo di base. Obsoleto a favore delle immagini RHEL-SAP-HA.
|             | 7,5       | LVM    | Agente Linux | RHEL 7,5 con componente aggiuntivo a disponibilità elevata. In viene addebitata una tariffa Premium per la disponibilità elevata e RHEL oltre alla tariffa di calcolo di base. Obsoleto a favore delle immagini RHEL-SAP-HA.
|             | 7.6       | LVM    | Agente Linux | RHEL 7,6 con componente aggiuntivo a disponibilità elevata. In viene addebitata una tariffa Premium per la disponibilità elevata e RHEL oltre alla tariffa di calcolo di base. Obsoleto a favore delle immagini RHEL-SAP-HA.
RHEL-SAP-HA   | 7.4          | LVM    | Agente Linux | RHEL 7,4 per SAP con disponibilità elevata e servizi di aggiornamento. Allegato ai repository E4S. Viene addebitata una tariffa Premium per i repository SAP e a disponibilità elevata, oltre a RHEL, oltre ai costi di calcolo di base.
|             | 74sapha-Gen2 | LVM    | Agente Linux | RHEL 7,4 per SAP con disponibilità elevata e servizi di aggiornamento. Immagine di generazione 2. Allegato ai repository E4S. Viene addebitata una tariffa Premium per i repository SAP e a disponibilità elevata, oltre a RHEL, oltre ai costi di calcolo di base.
|             | 7,5          | LVM    | Agente Linux | RHEL 7,5 per SAP con disponibilità elevata e servizi di aggiornamento. Allegato ai repository E4S. Viene addebitata una tariffa Premium per i repository SAP e a disponibilità elevata, oltre a RHEL, oltre ai costi di calcolo di base.
|             | 7.6          | LVM    | Agente Linux | RHEL 7,6 per SAP con disponibilità elevata e servizi di aggiornamento. Allegato ai repository E4S. Viene addebitata una tariffa Premium per i repository SAP e a disponibilità elevata, oltre a RHEL, oltre ai costi di calcolo di base.
|             | 76sapha-Gen2 | LVM    | Agente Linux | RHEL 7,6 per SAP con disponibilità elevata e servizi di aggiornamento. Immagine di generazione 2. Allegato ai repository E4S. Viene addebitata una tariffa Premium per i repository SAP e a disponibilità elevata, oltre a RHEL, oltre ai costi di calcolo di base.
|             | 7.7          | LVM    | Agente Linux | RHEL 7,7 per SAP con disponibilità elevata e servizi di aggiornamento. Allegato ai repository E4S. Viene addebitata una tariffa Premium per i repository SAP e a disponibilità elevata, oltre a RHEL, oltre ai costi di calcolo di base.
|             | 77sapha-Gen2 | LVM    | Agente Linux | RHEL 7,7 per SAP con disponibilità elevata e servizi di aggiornamento. Immagine di generazione 2. Allegato ai repository E4S. Viene addebitata una tariffa Premium per i repository SAP e a disponibilità elevata, oltre a RHEL, oltre ai costi di calcolo di base.
|             | 8.1          | LVM    | Agente Linux | RHEL 8,1 per SAP con disponibilità elevata e servizi di aggiornamento. Allegato ai repository E4S. Viene addebitata una tariffa Premium per i repository SAP e a disponibilità elevata, oltre a RHEL, oltre ai costi di calcolo di base.
|             | 81sapha-Gen2          | LVM    | Agente Linux | RHEL 8,1 per SAP con disponibilità elevata e servizi di aggiornamento. Immagini di generazione 2 collegate a repository E4S. Viene addebitata una tariffa Premium per i repository SAP e a disponibilità elevata, oltre a RHEL, oltre ai costi di calcolo di base.
|             | 8.2          | LVM    | Agente Linux | RHEL 8,2 per SAP con disponibilità elevata e servizi di aggiornamento. Viene addebitata una tariffa Premium per i repository SAP e a disponibilità elevata, oltre a RHEL, oltre ai costi di calcolo di base.
|             | 82sapha-Gen2          | LVM    | Agente Linux | RHEL 8,2 per SAP con disponibilità elevata e servizi di aggiornamento. Immagini di generazione 2 collegate a repository E4S. Viene addebitata una tariffa Premium per i repository SAP e a disponibilità elevata, oltre a RHEL, oltre ai costi di calcolo di base.
rhel-byos     |RHEL-lvm74| LVM    | Agente Linux | Le immagini RHEL 7,4 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm75| LVM    | Agente Linux | Le immagini RHEL 7,5 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm76| LVM    | Agente Linux | Le immagini RHEL 7,6 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm76-Gen2| LVM    | Agente Linux | Le immagini RHEL 7,6 Generation 2 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |rhel-lvm77| LVM    | Agente Linux | Le immagini RHEL 7,7 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm77-Gen2| LVM    | Agente Linux | Le immagini RHEL 7,7 Generation 2 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm78| LVM    | Agente Linux | Le immagini RHEL 7,8 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm78-Gen2| LVM    | Agente Linux | Le immagini RHEL 7,8 Generation 2 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm8 | LVM    | Agente Linux | Le immagini RHEL 8,0 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm8-Gen2 | LVM    | Agente Linux | Le immagini RHEL 8,0 Generation 2 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm81 | LVM    | Agente Linux | Le immagini RHEL 8,1 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm81-Gen2 | LVM    | Agente Linux | Le immagini RHEL 8,1 Generation 2 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm82 | LVM    | Agente Linux | Le immagini RHEL 8,2 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.
|             |RHEL-lvm82-Gen2 | LVM    | Agente Linux | Le immagini RHEL 8,2 Generation 2 BYOS, non associate ad alcuna origine di aggiornamenti, non addebiteranno un Premium RHEL.

> [!NOTE]
> L'offerta di prodotto RHEL-SAP-HANA è considerata la fine del ciclo di vita di Red Hat. Le distribuzioni esistenti continueranno a funzionare normalmente, ma Red Hat consiglia ai clienti di eseguire la migrazione dalle immagini RHEL-SAP-HANA alle immagini RHEL-SAP-HA, che include i repository SAP HANA e il componente aggiuntivo a disponibilità elevata. Altre informazioni sulle offerte cloud SAP di Red Hat sono disponibili [qui](https://access.redhat.com/articles/3751271).

## <a name="next-steps"></a>Passaggi successivi
* Scopri di più sulle [Immagini Red Hat in Azure](./redhat-images.md).
* Altre informazioni su [Red Hat Update Infrastructure](./redhat-rhui.md).
* Scopri di più sull' [offerta RHEL BYOS](./byos.md).
* Informazioni sui criteri di supporto di Red Hat per tutte le versioni di RHEL sono reperibili alla pagina [Red Hat Enterprise Linux Life Cycle (Ciclo di vita di Red Hat Enterprise Linux)](https://access.redhat.com/support/policy/updates/errata).
