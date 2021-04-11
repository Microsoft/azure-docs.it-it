---
title: Distribuire il servizio Gestione dispositivi StorSimple in Azure | Microsoft Docs
description: Informazioni sui passaggi necessari per la creazione, l'eliminazione, la migrazione del servizio e la gestione della chiave di registrazione del servizio.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 85d66b5e4f7e95d114cbf0b4c681d73eebb93a67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076565"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Distribuire il servizio Gestione dispositivi StorSimple per i dispositivi StorSimple serie 8000

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Panoramica

Il servizio Gestione dispositivi StorSimple viene eseguito in Microsoft Azure e si connette a più dispositivi StorSimple. Dopo aver creato il servizio, è possibile usarlo per gestire tutti i dispositivi connessi al servizio Gestione dispositivi StorSimple da un'unica posizione centrale, con una conseguente riduzione del carico amministrativo.

In questa esercitazione vengono descritti i passaggi necessari per la creazione, l'eliminazione, la migrazione del servizio e la gestione della chiave di registrazione del servizio. Le informazioni contenute in questo articolo si applicano solo ai dispositivi StorSimple serie 8000. Per altre informazioni sugli array virtuali StorSimple, vedere le informazioni su come [distribuire un servizio Gestione dispositivi StorSimple per l'array virtuale StorSimple](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Il portale di Azure supporta i dispositivi che eseguono l'aggiornamento 5.0 o versione successiva. Se il dispositivo non è aggiornato, installare l'aggiornamento 5 immediatamente. Per altre informazioni, vedere [Installare l'aggiornamento 5](storsimple-8000-install-update-5.md). 
> - Se si usa un'appliance cloud StorSimple (8010/8020), non è possibile aggiornare un'appliance cloud. Usare la versione più recente del software per creare una nuova appliance cloud con l'aggiornamento 5.0 e quindi effettuare il failover nella nuova appliance cloud creata. 
> - Tutti i dispositivi che eseguono l'aggiornamento 4.0 o versioni precedenti saranno caratterizzati da una funzionalità di gestione ridotta. 

## <a name="create-a-service"></a>Creare un servizio
Per creare un servizio Gestione dispositivi StorSimple, è necessario disporre di:

* Una sottoscrizione con un contratto Enterprise Agreement
* Un account di archiviazione di Microsoft Azure attivo
* Le informazioni di fatturazione usate per la gestione degli accessi

Sono consentite solo le sottoscrizioni con un contratto Enterprise Agreement. È inoltre possibile scegliere di generare un account di archiviazione predefinito al momento della creazione del servizio.

Un singolo servizio può gestire più dispositivi, ma un dispositivo non può estendersi a più servizi. Una grande impresa può avere più istanze del servizio per lavorare con diverse sottoscrizioni, organizzazioni o anche percorsi di distribuzione. 

> [!NOTE]
> Per gestire dispositivi StorSimple serie 8000 e array virtuali StorSimple sono necessarie istanze separate del servizio Gestione dispositivi StorSimple.

Per creare un servizio, attenersi alla procedura seguente.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Per ogni servizio Gestione dispositivi StorSimple, sono disponibili gli attributi seguenti:

* **Nome**: nome assegnato al servizio Gestione dispositivi StorSimple al momento della creazione. **Il nome del servizio non può essere modificato dopo la creazione del servizio. Questo vale anche per altre entità, ad esempio dispositivi, volumi, contenitori di volumi e criteri di backup che non possono essere rinominate nella portale di Azure.**
* **Stato** - Stato del servizio, che può essere **Attivo**, **In fase di creazione** oppure **Online**.
* **Posizione** : posizione geografica in cui verrà distribuito il dispositivo StorSimple.
* **Sottoscrizione** - Sottoscrizione di fatturazione associata al servizio.

## <a name="delete-a-service"></a>Eliminare un servizio

Prima di eliminare un servizio, verificare che non sia usato da dispositivi connessi. Se il servizio è in uso, disattivare i dispositivi connessi. L'operazione di disattivazione interromperà la connessione tra il dispositivo e il servizio mantenendo i dati del dispositivo nel cloud.

> [!IMPORTANT]
> Dopo che un servizio è stato eliminato, l'operazione non può essere annullata. Sarà necessario ripristinare le impostazioni predefinite di ogni dispositivo che usava il servizio prima che il dispositivo possa essere usato con un altro servizio. In questo scenario, i dati locali sul dispositivo e la configurazione vanno persi.

Per eliminare un servizio, attenersi alla procedura seguente.

### <a name="to-delete-a-service"></a>Per eliminare un servizio

1. Cercare il servizio da eliminare. Fare clic sull'icona **Risorse** e quindi immettere i termini appropriati per la ricerca. Nei risultati della ricerca selezionare il servizio da eliminare.

    ![Cercare il servizio da eliminare](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Verrà visualizzato il pannello del servizio Gestione dispositivi StorSimple. Fare clic su **Elimina**.

    ![Delete service](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Nella richiesta di conferma fare clic su **Sì**. L'eliminazione del servizio può richiedere alcuni minuti.

    ![Confermare l'eliminazione](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Ottenere la chiave di registrazione del servizio

Dopo aver creato un servizio, è necessario registrare il dispositivo StorSimple. Per registrare il primo dispositivo StorSimple, è necessaria la chiave di registrazione del servizio. Per registrare altri dispositivi con un servizio StorSimple esistente, sono necessarie la chiave di registrazione e la chiave DEK del servizio (che viene generata durante la registrazione sul primo dispositivo). Per altre informazioni sulla chiave DEK del servizio, vedere [Sicurezza in StorSimple](storsimple-8000-security.md). È possibile ottenere la chiave di registrazione accedendo a **Chiavi** nel pannello Gestione dispositivi StorSimple.

Per ottenere la chiave di registrazione del servizio, attenersi alla procedura seguente.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Conservare la chiave di registrazione del servizio in una posizione sicura. Questa chiave e la chiave DEK del servizio saranno necessarie per registrare altri dispositivi con il servizio. Dopo aver ottenuto la chiave di registrazione del servizio, è necessario configurare il dispositivo tramite l'interfaccia di Windows PowerShell per StorSimple.

Per informazioni dettagliate sull'uso della chiave di registrazione, vedere [Passaggio 3: Configurare e registrare il dispositivo tramite Windows PowerShell per StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Rigenerare la chiave di registrazione del servizio
La rigenerazione di una chiave di registrazione del servizio deve essere effettuata quando è necessario eseguire la rotazione delle chiavi o se l'elenco di amministratori del servizio è stato modificato. Quando si rigenera la chiave, quest'ultima viene usata solo per registrare dispositivi successivi. I dispositivi già registrati non sono interessati da questo processo.

Per rigenerare una chiave di registrazione del servizio, attenersi alla procedura seguente.

### <a name="to-regenerate-the-service-registration-key"></a>Per rigenerare la chiave di registrazione del servizio
1. In **Gestione dispositivi StorSimple** passare a **Gestione&gt;** **Chiavi**.
    
    ![Pannello Vai al pannello chiavi](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Nel pannello **Chiavi** fare clic su **Rigenera**.

    ![Fare clic su Rigenera](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Nel pannello **Rigenera la chiave di registrazione del servizio** esaminare l'azione necessaria quando le chiavi vengono rigenerate. Tutti i dispositivi successivi registrati con questo servizio useranno la nuova chiave di registrazione. Fare clic su **Rigenera** per confermare. Al termine della rigenerazione, si riceverà una notifica.

    ![Confermare la rigenerazione](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Verrà visualizzata una nuova chiave di registrazione del servizio.

5. Copiare la chiave e salvarla per registrare eventuali nuovi dispositivi con il servizio.



## <a name="change-the-service-data-encryption-key"></a>Modificare la chiave DEK del servizio
[!INCLUDE [storage-files-migration-generate-key](../../includes/storage-files-migration-generate-key.md)]

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Operazioni supportate nei dispositivi che eseguono una versione precedente all'aggiornamento 5.0
Nel portale di Azure sono supportati solo i dispositivi StorSimple che eseguono l'aggiornamento 5.0 e versioni successive. I dispositivi che eseguono versioni precedenti hanno un supporto limitato. Dopo avere eseguito la migrazione al portale di Azure, usare la tabella seguente per comprendere le operazioni supportate per i dispositivi con versioni precedenti all'aggiornamento 5.0.

| Operazione                                                                                                                       | Supportato      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Registrare un dispositivo                                                                                                               | Sì            |
| Configurare le impostazioni di dispositivo, ad esempio le caratteristiche generali, la rete e la sicurezza                                                                | Sì            |
| Analizzare, scaricare e installare aggiornamenti                                                                                             | Sì            |
| Disattivare un dispositivo                                                                                                               | Sì            |
| Eliminare un dispositivo                                                                                                                   | Sì            |
| Creare, modificare ed eliminare un contenitore di volumi                                                                                   | No             |
| Creare, modificare ed eliminare un volume                                                                                             | No             |
| Creare, modificare ed eliminare criteri di backup                                                                                      | No             |
| Creazione di un backup manuale                                                                                                            | No             |
| Eseguire un backup pianificato                                                                                                         | Non applicabile |
| Eseguire il ripristino da un set di backup                                                                                                        | No             |
| Eseguire la clonazione in un dispositivo che esegue l'aggiornamento 3.0 e versioni successive <br> Il dispositivo di origine esegue una versione precedente all'aggiornamento 3.0.                                | Sì            |
| Eseguire la clonazione in un dispositivo che esegue una versione precedente all'aggiornamento 3.0.                                                                          | No             |
| Eseguire il failover del dispositivo di origine <br> (da un dispositivo con una versione precedente all'aggiornamento 3.0 a un dispositivo con l'aggiornamento 3.0 e versioni successive)                                                               | Sì            |
| Eseguire il failover del dispositivo di destinazione <br> (a un dispositivo che esegue una versione software precedente all'aggiornamento 3.0)                                                                                   | No             |
| Cancellare un avviso                                                                                                                  | Sì            |
| Visualizzare criteri di backup, catalogo di backup, volumi, contenitori di volumi, grafici di monitoraggio, processi e avvisi creati nel portale classico | Sì            |
| Attivare e disattivare i controller dei dispositivi                                                                                              | Sì            |


## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sul [processo di distribuzione di StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Ulteriori informazioni sulla [gestione dell'account di archiviazione di StorSimple](storsimple-8000-manage-storage-accounts.md).
* Altre informazioni su come [usare il servizio Gestione dispositivi StorSimple per amministrare un dispositivo StorSimple](storsimple-8000-manager-service-administration.md).
