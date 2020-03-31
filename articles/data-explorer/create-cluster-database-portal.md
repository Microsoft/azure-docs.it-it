---
title: 'Guida introduttiva: Creare un database e un cluster di Esplora dati di Azure'
description: Questa guida introduttiva descrive come creare un database e un cluster di Esplora dati di Azure e inserire (caricare) i dati.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 07/22/2019
ms.openlocfilehash: ed0c570449a0c21e9eace1273228539db7c208da
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80238646"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Guida introduttiva: Creare un database e un cluster di Esplora dati di Azure

> [!div class="op_single_selector"]
> * [Portale](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Modello ARM](create-cluster-database-resource-manager.md)


Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in un database per poter eseguire query. In questa guida introduttiva vengono creati un cluster e un database.

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-cluster"></a>Creare un cluster

Creare un cluster di Esplora dati di Azure in un gruppo di risorse di Azure con un set definito di risorse di calcolo e archiviazione.

1. Selezionare il pulsante **+ Crea una risorsa** nell'angolo superiore sinistro del portale.

1. Cercare *Esplora dati di Azure*.

   ![Cercare risorse](media/create-cluster-database-portal/search-resources.png)

1. In **Esplora dati di Azure** selezionare **Crea** nella parte inferiore della schermata.

1. Compilare il modulo dei dettagli del cluster di base con le informazioni seguenti.

   ![Modulo per la creazione del cluster](media/create-cluster-database-portal/create-cluster-form2.png)

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per il cluster.|
    | Resource group | Gruppo di risorse in uso | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
    | Nome cluster | Nome univoco del cluster | Scegliere un nome univoco che identifica il cluster. Il nome di dominio *[area].kusto.windows.net* viene accodato al nome del cluster specificato. Il nome può contenere solo lettere minuscole e numeri. Deve avere una lunghezza compresa tra 4 e 22 caratteri.
    | Region | *Stati Uniti occidentali* o *Stati Uniti occidentali 2* | Per questa guida introduttiva selezionare *Stati Uniti occidentali* o *Stati Uniti occidentali 2* (se si usano le zone di disponibilità). Per un sistema di produzione, selezionare l'area più appropriata in base alle esigenze.
    | Zone di disponibilità | *1*, *2* e/o *3* | Inserire le istanze del cluster in diverse zone di disponibilità nella stessa area (facoltativo). Le [zone di disponibilità di Azure](/azure/availability-zones/az-overview) sono località fisiche univoche all'interno della stessa area di Azure. Proteggono un cluster di Esplora dati di Azure e i dati da errori parziali dell'area. Per impostazione predefinita, i nodi del cluster vengono creati nello stesso data center. Selezionando diverse zone di disponibilità è possibile eliminare un singolo punto di guasto e garantire disponibilità elevata. La distribuzione nelle zone di disponibilità è possibile solo durante la creazione del cluster e non può essere modificata in un secondo momento.
    | Specifiche dell'ambiente di calcolo | *D13_v2* | Per questa guida introduttiva, selezionare la specifica con il prezzo più basso. Per un sistema di produzione, selezionare la specifica più appropriata in base alle esigenze.
    | | | |

1. Selezionare **Rivedi e crea** per rivedere i dettagli del cluster e **Crea** per effettuarne il provisioning. Per il provisioning sono in genere necessari circa 10 minuti.

1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.

    ![Vai alla risorsa](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>Creazione di un database

A questo punto si è pronti per il secondo passaggio del processo: la creazione del database.

1. Nella scheda **Panoramica** selezionare **Crea database**.

    ![Passaggio 2: Creare un database](media/create-cluster-database-portal/database-creation.png)

1. Compilare il modulo con le informazioni seguenti.

    ![Modulo per la creazione del database](media/create-cluster-database-portal/create-database.png)

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Nome database | *TestDatabase* | Il nome del database deve essere univoco all'interno del cluster.
    | Periodo di memorizzazione | *3650* | Intervallo di tempo (in giorni) per cui è garantito che i dati rimangano disponibili per le query. L'intervallo di tempo viene misurato dal momento in cui i dati vengono inseriti.
    | Cache period (Periodo cache) | *31* | L'intervallo di tempo (in giorni) per cui mantenere i dati sottoposti frequentemente a query disponibili nell'archiviazione su unità SSD o nella RAM, invece che nell'archiviazione a lungo termine.
    | | | |

1. Selezionare **Crea** per creare il database. Per la creazione è in genere necessario meno di un minuto. Una volta completato il processo, si torna alla scheda **Panoramica** del cluster.

## <a name="run-basic-commands-in-the-database"></a>Eseguire i comandi di base nel database

Dopo aver creato un cluster e un database, è possibile eseguire query e comandi. Nel database non sono ancora presenti dati, ma è comunque possibile vedere come funzionano gli strumenti.

1. Nel cluster selezionare **Query**. Incollare il comando `.show databases` nella finestra di query e quindi selezionare **Esegui**.

    ![Comando di visualizzazione dei database](media/create-cluster-database-portal/show-databases.png)

    Nel set di risultati è presente **TestDatabase**, l'unico database nel cluster.

1. Incollare il comando `.show tables` nella finestra di query e selezionare **Esegui**.

    Questo comando restituisce un set di risultati vuoto perché ancora non sono presenti tabelle. Verrà aggiunta una tabella nel prossimo articolo di questa serie.

## <a name="stop-and-restart-the-cluster"></a>Arrestare e riavviare il cluster

È possibile arrestare e riavviare un cluster in base alle esigenze aziendali.

1. Per arrestare il cluster, nella parte superiore della scheda **Panoramica** selezionare **Arresta**.

    Quando il cluster viene arrestato, i dati non sono disponibili per le query e non è possibile inserire nuovi dati.

1. Per riavviare il cluster, nella parte superiore della scheda **Panoramica** selezionare **Avvia**.

    Quando il cluster viene riavviato, sono necessari circa 10 minuti prima che diventi disponibile, come quando ne è stato effettuato il provisioning per la prima volta. È necessario altro tempo per caricare i dati nella cache ad accesso frequente.  

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di seguire altre guide introduttive ed esercitazioni, non eliminare le risorse create. In caso contrario, pulire il gruppo di risorse per evitare l'addebito di costi.

1. Nel portale di Azure selezionare **Gruppi di risorse** all'estrema sinistra e quindi selezionare il gruppo di risorse che contiene il cluster di Esplora dati.  

1. Selezionare **Elimina gruppo di risorse** per eliminare l'intero gruppo di risorse. Se si usa un gruppo di risorse esistente, è possibile scegliere di eliminare solo il cluster di Esplora dati.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Avvio rapido: Inserire dati dall'hub eventi in Esplora dati di Azure](ingest-data-event-hub.md)


