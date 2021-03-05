---
title: Script per abilitare kdump in SAP HANA (istanze large) | Microsoft Docs
description: Script per abilitare kdump in SAP HANA (istanze large) HLI tipo I, HLI tipo II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e4b06cbcbef79e243116bddb33adbcf6476fac8a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213369"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>Kdump per SAP HANA in istanze Large di Azure (HLI)

La configurazione e l'abilitazione di kdump è un passaggio necessario per la risoluzione dei problemi relativi agli arresti anomali del sistema che non hanno una chiara ragione.
In alcuni casi un sistema si arresta in modo imprevisto, che non può essere spiegato da un problema hardware o infrastruttura.
In questi casi può trattarsi di un problema del sistema operativo o dell'applicazione e kdump consentirà a SUSE di determinare il motivo dell'arresto anomalo di un sistema.

## <a name="enable-kdump-service"></a>Abilita servizio kdump

Questo documento descrive i dettagli su come abilitare il servizio kdump in istanze large di Azure HANA (**tipo I e tipo II**)

## <a name="supported-skus"></a>SKU supportati

|  Tipo di istanza large di Hana   |  Fornitore del sistema operativo   |  Versione del pacchetto del sistema operativo   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Tipo I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Tipo I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Tipo II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Tipo II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Prerequisiti

- Il servizio kdump USA `/var/crash` la directory per scrivere i dump, assicurarsi che la partizione corrisponda a questa directory con spazio sufficiente per contenere i dump.

## <a name="setup-details"></a>Dettagli di configurazione

- Lo script per abilitare kdump è disponibile [qui](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh)
> [!NOTE]
> Questo script viene eseguito in base alla configurazione del Lab e il cliente prevede di contattare il fornitore del sistema operativo per eventuali ulteriori operazioni di ottimizzazione.
> Verrà eseguito il provisioning di un LUN separato per i server nuovi ed esistenti per il salvataggio dei dump e lo script si occuperà di configurare il file system fuori dal LUN.
> Microsoft non sarà responsabile dell'analisi del dump. Il cliente deve aprire un ticket con il fornitore del sistema operativo per analizzarlo.

- Eseguire questo script in un'istanza large di HANA usando il comando seguente

    > [!NOTE]
    > privilegio sudo necessario per eseguire questo comando.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Se il comando restituisce kdump è stato abilitato, assicurarsi di riavviare il sistema per applicare correttamente le modifiche.

- Se l'output del comando non è riuscito a eseguire determinate operazioni, l'uscita!!!!, il servizio kdump non è abilitato. Vedere la sezione [problema di supporto](#support-issue).

## <a name="test-kdump"></a>Test kdump

> [!NOTE]
>  L'operazione seguente attiverà un arresto anomalo del kernel e il riavvio del sistema.

- Attivare un arresto anomalo del kernel

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- Dopo il riavvio del sistema, controllare la `/var/crash` Directory per i log di arresto anomalo del kernel.

- Se `/var/crash` dispone di una directory con la data corrente, il kdump viene abilitato correttamente.

## <a name="support-issue"></a>Problemi di supporto

Se lo script ha esito negativo con un errore o kdump non è abilitato, generare la richiesta di servizio con il team di supporto Microsoft con i dettagli seguenti.

* ID sottoscrizione HLI

* Nome server

* Fornitore del sistema operativo

* Versione sistema operativo

* Versione del kernel

## <a name="related-documents"></a>Documenti correlati
- Per altre informazioni sulla [configurazione di kdump](https://www.suse.com/support/kb/doc/?id=3374462)
