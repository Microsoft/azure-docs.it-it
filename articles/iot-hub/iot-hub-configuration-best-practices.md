---
title: Procedure consigliate per la configurazione di dispositivi per l'hub IoT di Azure | Microsoft Docs
description: Informazioni sulle procedure consigliate per l'uso della gestione automatica dei dispositivi per ridurre al minimo le attività ripetitive e complesse necessarie per gestire i dispositivi Internet su larga scala.
author: robinsh
ms.author: robinsh
ms.date: 06/28/2019
ms.topic: conceptual
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 8a39c2b06ca8a0f852891acb60ba199fc2c6db5c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024130"
---
# <a name="best-practices-for-device-configuration-within-an-iot-solution"></a>Procedure consigliate per la configurazione di dispositivi in una soluzione IoT

La gestione automatica dei dispositivi nell'hub IoT di Azure consente di automatizzare molte attività ripetitive e complesse per la gestione di un numero elevato di dispositivi nel loro intero ciclo di vita. Questo articolo definisce molte delle procedure consigliate per i vari ruoli coinvolti nello sviluppo e nel funzionamento di una soluzione IoT.

* **Produttore/integratore di hardware IoT**: questo ruolo include i produttori di hardware IoT, gli integratori che si occupano dell'assemblaggio di hardware da produttori diversi e i fornitori di hardware per una distribuzione IoT prodotta o integrata da altri fornitori. Coloro che svolgono questo ruolo sono coinvolti nello sviluppo e nell'integrazione di firmware, sistemi operativi incorporati e software incorporato.

* **Sviluppatore di soluzioni IoT**: una soluzione IoT viene in genere realizzata da uno sviluppatore di soluzioni che può far parte di un team interno o essere un integratore di sistemi specializzato in questa attività. Lo sviluppatore di soluzioni IoT può realizzare vari componenti della soluzione IoT partendo da zero, integrare vari componenti standard o open source oppure personalizzare un [acceleratore di soluzioni IoT](../iot-accelerators/index.yml).

* **Operatore di soluzioni IoT**: una volta distribuita, la soluzione IoT richiede attività di gestione, monitoraggio, aggiornamento e manutenzione a lungo termine. Queste operazioni possono essere eseguite da un team interno costituito da esperti di tecnologie informatiche, da team che svolgono operazioni hardware e attività di manutenzione, nonché da specialisti di dominio che monitorano il corretto funzionamento dell'intera infrastruttura IoT.

## <a name="understand-automatic-device-management-for-configuring-iot-devices-at-scale"></a>Informazioni sulla gestione automatica dei dispositivi per la configurazione di dispositivi IoT su larga scala

La gestione automatica dei dispositivi include i numerosi vantaggi offerti dai [dispositivi gemelli](iot-hub-devguide-device-twins.md) e dai [moduli gemelli](iot-hub-devguide-module-twins.md) per sincronizzare gli stati desiderati e segnalati tra il cloud e i dispositivi. Le [configurazioni automatiche dei dispositivi](./iot-hub-automatic-device-management.md) aggiornano automaticamente grandi set di dispositivi gemelli e riepilogano lo stato e la conformità. I seguenti passaggi generali descrivono come viene sviluppata e usata la gestione automatica dei dispositivi:

* Il **produttore/integratore di hardware IoT** implementa le funzionalità di gestione dei dispositivi all'interno di un'applicazione incorporata usando [dispositivi gemelli](iot-hub-devguide-device-twins.md). Queste funzionalità possono includere gli aggiornamenti del firmware, l'installazione e l'aggiornamento del software e la gestione delle impostazioni.

* Lo **sviluppatore di soluzioni IoT** implementa il livello di gestione per le operazioni di gestione dei dispositivi usando [dispositivi gemelli](iot-hub-devguide-device-twins.md) e [configurazioni automatiche dei dispositivi](./iot-hub-automatic-device-management.md). La soluzione deve includere la definizione di un'interfaccia per operatore per eseguire le attività di gestione dei dispositivi.

* L'**operatore di soluzioni IoT** usa la soluzione IoT per eseguire attività di gestione dei dispositivi, in particolare per raggruppare i dispositivi, avviare modifiche di configurazione, ad esempio aggiornamenti al firmware, monitorare lo stato di avanzamento e risolvere i problemi che si verificano.

## <a name="iot-hardware-manufacturerintegrator"></a>Produttore/integratore di hardware IoT

Di seguito sono presentate le procedure consigliate per i produttori e gli integratori di hardware che si occupano dello sviluppo di software incorporato:

* **Implementare [dispositivi gemelli](iot-hub-devguide-device-twins.md):** i dispositivi gemelli consentono di sincronizzare dal cloud la configurazione desiderata e di segnalare le informazioni sulla configurazione corrente e le proprietà dei dispositivi. Il modo migliore per implementare dispositivi gemelli all'interno di applicazioni incorporate consiste nell'uso degli [SDK per IoT di Azure](https://github.com/Azure/azure-iot-sdks). I dispositivi gemelli sono particolarmente adatti alla configurazione per i motivi seguenti:

    * Supportano le comunicazioni bidirezionali.
    * Consentono lo stato connesso e disconnesso dei dispositivi.
    * Seguono il principio della coerenza finale.
    * Sono completamente disponibili per query nel cloud.

* **Strutturare il dispositivo gemello per la gestione dei dispositivi**: il dispositivo gemello deve essere strutturato in modo che le proprietà di gestione siano raggruppate logicamente in sezioni. In questo modo, le modifiche di configurazione possono essere isolate senza alcun impatto sulle altre sezioni del dispositivo gemello. È ad esempio possibile creare una sezione all'interno delle proprietà desiderate per il firmware, un'altra sezione per il software e una terza sezione per le impostazioni di rete. 

* **Segnalare gli attributi utili per la gestione dei dispositivi**: gli attributi come il modello e il formato del dispositivo fisico, il firmware, il sistema operativo, il numero di serie e altri elementi identificativi sono utili per segnalare le informazioni sui parametri relativi alle modifiche della configurazione di destinazione.

* **Definire gli stati principali per la segnalazione dello stato di avanzamento**: gli stati principali devono essere enumerati in modo da poter essere segnalati all'operatore. Un aggiornamento del firmware può ad esempio essere segnalato come corrente, in fase di download, in fase di applicazione, in corso e in stato di errore. Definire campi aggiuntivi per altre informazioni su ogni stato.

## <a name="iot-solution-developer"></a>Sviluppatore di soluzioni IoT

Di seguito sono presentate le procedure consigliate per gli sviluppatori di soluzioni IoT che creano sistemi basati su Azure:

* **Implementare [dispositivi gemelli](iot-hub-devguide-device-twins.md):** i dispositivi gemelli consentono di sincronizzare dal cloud la configurazione desiderata e di segnalare le informazioni sulla configurazione corrente e le proprietà dei dispositivi. Il modo migliore per implementare dispositivi gemelli all'interno di applicazioni di soluzioni cloud consiste nell'uso degli [SDK per IoT di Azure](https://github.com/Azure/azure-iot-sdks). I dispositivi gemelli sono particolarmente adatti alla configurazione per i motivi seguenti:

    * Supportano le comunicazioni bidirezionali.
    * Consentono lo stato connesso e disconnesso dei dispositivi.
    * Seguono il principio della coerenza finale.
    * Sono completamente disponibili per query nel cloud.

* **Organizzare i dispositivi usando i tag dei dispositivi gemelli**: la soluzione deve consentire all'operatore di definire anelli di qualità o altri set di dispositivi in base alle varie strategie di distribuzione, ad esempio canary. L'organizzazione dei dispositivi può essere implementata all'interno della soluzione usando tag dei dispositivi gemelli e [query](iot-hub-devguide-query-language.md). Questa organizzazione è necessaria per un'implementazione precisa e sicura delle configurazioni.

* **Implementare le [configurazioni automatiche dei dispositivi](./iot-hub-automatic-device-management.md):** queste configurazioni distribuiscono e monitorano le modifiche di configurazione per grandi set di dispositivi IoT tramite dispositivi gemelli.

   Le configurazioni automatiche dei dispositivi vengono assegnate a set di dispositivi gemelli tramite la **condizione di destinazione**, che consiste in una query sulle proprietà segnalate o sui tag dei dispositivi gemelli. Il **contenuto di destinazione** è il set di proprietà desiderate che verrà impostato all'interno dei dispositivi gemelli di destinazione. Il contenuto di destinazione deve essere allineato con la struttura dei dispositivi gemelli definita dal produttore/integratore dell'hardware IoT. Le **metriche** sono query sulle proprietà segnalate dal dispositivo gemello e devono anche essere allineate con la struttura del dispositivo gemello definita dal produttore o dall'integratore di hardware.

   Le configurazioni automatiche dei dispositivi vengono eseguite per la prima volta poco dopo la creazione della configurazione e quindi a intervalli di cinque minuti. Traggono anche vantaggio dall'hub delle cose che eseguono operazioni di dispositivo gemello a una velocità che non supererà mai i [limiti di limitazione](iot-hub-devguide-quotas-throttling.md) per le letture e gli aggiornamenti dei dispositivi gemelli.

* **Usare il [servizio Device Provisioning](../iot-dps/how-to-manage-enrollments.md):** gli sviluppatori di soluzioni devono usare il servizio Device Provisioning per assegnare tag dei dispositivi gemelli a nuovi dispositivi, in modo che vengano configurati automaticamente dalle **configurazioni automatiche dei dispositivi** assegnate ai dispositivi gemelli con tali tag. 

## <a name="iot-solution-operator"></a>Operatore di soluzioni IoT

Di seguito sono presentate le procedure consigliate per gli operatori di soluzioni IoT che usano una soluzione IoT basata su Azure:

* **Organizzare i dispositivi per la gestione**: la soluzione IoT deve definire o consentire la creazione di anelli di qualità o altri set di dispositivi in base alle varie strategie di distribuzione, ad esempio canary. I set di dispositivi verranno usati per implementare le modifiche di configurazione ed eseguire altre operazioni di gestione dei dispositivi su larga scala.

* **Eseguire modifiche di configurazione tramite un'implementazione graduale**: un'implementazione graduale è un processo in base al quale un operatore distribuisce le modifiche a un set più ampio di dispositivi IoT. L'obiettivo è apportare le modifiche gradualmente per ridurre il rischio di introdurre modifiche che causano disservizi su larga scala.  L'operatore deve usare l'interfaccia della soluzione per creare una [configurazione automatica dei dispositivi](./iot-hub-automatic-device-management.md) e la condizione di destinazione deve essere assegnata a un set iniziale di dispositivi, ad esempio un gruppo canary. L'operatore deve quindi convalidare la modifica della configurazione nel set iniziale di dispositivi.

   Al termine della convalida, l'operatore aggiornerà la configurazione automatica dei dispositivi in modo da includere un set di dispositivi più ampio. L'operatore deve inoltre impostare la priorità per la configurazione in modo che sia superiore alle altre configurazioni attualmente assegnate a tali dispositivi. L'implementazione può essere monitorata tramite le metriche definite dalla configurazione automatica dei dispositivi.

* **Eseguire operazioni di rollback in caso di errori o problemi di configurazione**: è possibile eseguire il rollback di una configurazione automatica dei dispositivi che causa errori o problemi di configurazione modificando la **condizione di destinazione** in modo che non risulti più soddisfatta dai dispositivi. Assicurarsi che un'altra configurazione automatica dei dispositivi con priorità inferiore rimanga assegnata a tali dispositivi. Verificare che il rollback abbia esito positivo visualizzando le metriche: la configurazione di cui è stato eseguito il rollback non deve più mostrare lo stato dei dispositivi a cui non è assegnata e le metriche della seconda configurazione devono ora includere i conteggi per i dispositivi a cui questa è ancora assegnata.

## <a name="next-steps"></a>Passaggi successivi

* Leggere altre informazioni sull'implementazione di dispositivi gemelli in [Comprendere e usare dispositivi gemelli nell'hub IoT](iot-hub-devguide-device-twins.md).

* Eseguire i passaggi per creare, aggiornare o eliminare una configurazione automatica dei dispositivi in [Configurare e monitorare i dispositivi IoT su larga scala](./iot-hub-automatic-device-management.md).

* Implementare un modello di aggiornamento del firmware usando dispositivi gemelli e configurazioni automatiche dei dispositivi in [Esercitazione: Implementare un processo di aggiornamento del firmware del dispositivo](tutorial-firmware-update.md).