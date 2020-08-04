---
title: Creare un'app di monitoraggio pazienti continuo con Azure IoT Central | Microsoft Docs
description: Informazioni su come creare un'applicazione di monitoraggio pazienti continuo usando i modelli di applicazione di Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 704c56745ad89e9ed2f79e8a863f1d0bc9845bf9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001826"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Esercitazione: Distribuire ed esplorare un modello di app di monitoraggio pazienti continuo



Questa esercitazione illustra ai creatori di soluzioni come iniziare a distribuire un modello di applicazione di monitoraggio pazienti continuo IoT Central. Si apprenderà come distribuire il modello, quali sono gli elementi inclusi per impostazione predefinita e quali operazioni è possibile eseguire successivamente.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare un modello di applicazione
> * Esplorare il modello di applicazione

## <a name="create-an-application-template"></a>Creare un modello di applicazione

Passare al [sito Web di gestione applicazioni Azure IoT Central](https://apps.azureiotcentral.com/). Selezionare **Compila** dalla barra di spostamento a sinistra e quindi fare clic sulla scheda **Servizi sanitari**. 

>[!div class="mx-imgBorder"] 
>![Gestione app - Servizi sanitari](media/app-manager-health.png)

Fare clic sul pulsante **Crea app** per iniziare a creare l'applicazione e quindi accedere con un account Microsoft personale oppure aziendale o dell'istituto di istruzione. Verrà visualizzata la pagina **Nuova applicazione**.

![Creare l'applicazione - Servizi sanitari](media/app-manager-health-create.png)

![Creare l'applicazione Servizi sanitari - Info di fatturazione](media/app-manager-health-create-billinginfo.png)

Per creare l'applicazione, seguire questa procedura:

1. Azure IoT Central suggerisce automaticamente un nome per l'applicazione in base al modello selezionato. È possibile accettare tale nome oppure immettere il nome descrittivo desiderato per l'applicazione, ad esempio **Continuous patient monitoring**. Azure IoT Central genera automaticamente anche un prefisso URL univoco in base al nome dell'applicazione. È possibile modificare questo prefisso URL in modo da renderlo più facile da ricordare.

2. È possibile specificare se si vuole creare l'applicazione con il piano tariffario *gratuito* o con uno dei piani tariffari *standard*. Le applicazioni create con il piano tariffario gratuito sono gratuite per sette giorni prima della scadenza e consentono l'uso di un massimo di cinque dispositivi gratuiti. È possibile spostare un'applicazione dal piano gratuito a un piano tariffario standard in qualsiasi momento prima della scadenza. Se si sceglie il piano gratuito, è necessario immettere le informazioni di contatto e specificare se si vogliono ricevere informazioni e suggerimenti da Microsoft. Le applicazioni create con un piano standard supportano fino a due dispositivi gratuiti e richiedono l'immissione delle informazioni relative alla sottoscrizione di Azure ai fini della fatturazione.

3. Fare clic su **Crea** nella parte inferiore della pagina per distribuire l'applicazione.

## <a name="walk-through-the-application-template"></a>Esplorare il modello di applicazione

### <a name="dashboards"></a>Dashboard

Dopo aver distribuito il modello di app, prima di tutto si passerà a **Lamna in-patient monitoring dashboard** (Dashboard di monitoraggio dei pazienti degenti Lamna). Lamna Healthcare è un sistema ospedaliero fittizio che include due strutture: Woodgrove Hospital e Burkville Hospital. In questo dashboard dell'operatore relativo a Woodgrove Hospital verranno visualizzate le informazioni e i dati di telemetria sui dispositivi in questo modello insieme a un set di comandi, processi e azioni che è possibile eseguire. Dal dashboard è possibile:

* Visualizzare i dati di telemetria e le proprietà del dispositivo, ad esempio il **livello della batteria** o lo stato di **connettività**.

* Visualizzare la **planimetria piano** e la posizione del dispositivo patch intelligente per i segni vitali.

* **Effettuare di nuovo il provisioning** della patch intelligente per i segni vitali per un nuovo paziente.

* Vedere un esempio di **dashboard del provider** che un team di assistenza ospedaliera potrebbe visualizzare per monitorare i pazienti.

* Modificare lo **stato paziente** del dispositivo per indicare se quest'ultimo è in uso per uno scenario con pazienti degenti o in modalità remota.

>[!div class="mx-imgBorder"] 
>![Pazienti degenti Lamna](media/lamna-in-patient.png)

È anche possibile fare clic su **Go to remote patient dashboard** (Vai al dashboard dei pazienti remoti) per visualizzare il secondo dashboard dell'operatore usato per Burkville Hospital. Tale dashboard contiene un set analogo di azioni, dati di telemetria e informazioni. È inoltre possibile visualizzare più dispositivi in uso e **aggiornare il firmware** di ognuno.

>[!div class="mx-imgBorder"] 
>![Pazienti remoti Lamna](media/lamna-remote.png)

In entrambi i dashboard è sempre possibile tornare a questa documentazione.

### <a name="device-templates"></a>Modelli di dispositivo

Se si fa clic sulla scheda **Modelli di dispositivo**, si noterà che fanno parte del modello due tipi diversi di dispositivi:

* **Smart Vitals Patch** (Patch intelligente per i segni vitali): Questo dispositivo rappresenta una patch che misura tipi diversi di segni vitali. Può essere usato per monitorare i pazienti degenti in ospedale e quelli esterni. Se si fa clic sul modello, si vedrà che la patch, oltre a inviare dati del dispositivo come il livello della batteria e la temperatura, invia dati relativi allo stato di salute del paziente, ad esempio la frequenza respiratoria e la pressione sanguigna.

* **Smart Knee Brace** (Tutore intelligente per il ginocchio): questo dispositivo rappresenta un tutore per il ginocchio che i pazienti potrebbero usare durante il recupero da un intervento di protesi al ginocchio. Se si fa clic su questo modello, si visualizzeranno ad esempio informazioni relative al raggio di azione del movimento articolare e all'accelerazione, oltre ai dati del dispositivo.

>[!div class="mx-imgBorder"] 
>![Modello di dispositivo patch intelligente per i segni vitali](media/smart-vitals-device-template.png)

### <a name="device-groups"></a>Gruppi di dispositivi 
I gruppi di dispositivi consentono di raggruppare logicamente un set di dispositivi per eseguire query o operazioni in blocco su di essi. 

Selezionando la scheda Device groups (Gruppi di dispositivi), si noterà che sono stati creati alcuni gruppi di dispositivi predefiniti per ognuno dei modelli di dispositivo nell'applicazione. Si noterà che sono stati creati anche altri due gruppi di dispositivi di esempio, denominati "Provision devices" (Dispositivi per il provisioning) e "Devices with outdated firmware" (Dispositivi con firmware obsoleto). Questi gruppi di dispositivi di esempio verranno usati come input per eseguire alcuni [processi](#jobs).

### <a name="rules"></a>Regole

Se si passa alla scheda Regole, si noterà che nel modello di applicazione sono presenti tre regole:

* **Brace temperature high** (Temperatura tutore elevata): questa regola viene attivata quando la temperatura del tutore intelligente per il ginocchio supera i 35 &deg;C in un arco temporale di 5 minuti. È possibile usare questa regola per avvisare il paziente e il team di assistenza e raffreddare il dispositivo in modalità remota.

* **Fall detected** (Rilevata caduta): questa regola viene attivata se viene rilevata una caduta del paziente. È possibile usare questa regola per configurare un'azione allo scopo di dislocare un team operativo per assistere il paziente che è caduto.

* **Patch battery low** (Livello batteria basso): questa regola viene attivata quando il livello della batteria del dispositivo scende al di sotto del 10%. È possibile usare questa regola per attivare una notifica che segnali al paziente di caricare il dispositivo.

>[!div class="mx-imgBorder"] 
>![Regola per temperatura tutore elevata](media/brace-temp-rule.png)

### <a name="jobs"></a>Processi

I processi consentono di eseguire operazioni in blocco su un set di dispositivi, usando i [gruppi di dispositivi](#device-groups) come input. Il modello di applicazione è stato sottoposto a seeding con due processi di esempio che un operatore di soluzioni potrebbe dover eseguire in un determinato momento del ciclo di vita dei dispositivi:
* **Aggiornare il firmware del tutore per il ginocchio**: questo processo troverà i dispositivi nel gruppo di dispositivi "Devices with outdated firmware" (Dispositivi con firmware obsoleto) ed eseguirà un comando per aggiornare questi dispositivi alla versione più recente del firmware del tutore per il ginocchio. Questo processo di esempio presuppone che i dispositivi dispongano di funzionalità per ricevere un comando di aggiornamento e recuperare i file del firmware direttamente dal cloud.  

* **Eseguire nuovamente il provisioning dei dispositivi**: se si dispone di un set di dispositivi recentemente restituiti all'ospedale ed è necessario eseguirne nuovamente il provisioning per il set successivo di pazienti, è possibile eseguire questo processo per aggiornare in blocco i dispositivi per il provisioning. In questo caso, vengono rilevati tutti i dispositivi da un gruppo di dispositivi denominato "Provision devices" (Dispositivi per il provisioning) e viene avviato un comando per eseguire nuovamente il provisioning di questi dispositivi. 

### <a name="devices"></a>Dispositivi

Fare clic sulla scheda **Dispositivi** e quindi selezionare un'istanza di **Smart Knee Brace** (Tutore intelligente per il ginocchio). Si noterà che sono disponibili tre viste per esplorare le informazioni relative allo specifico dispositivo selezionato. Queste viste vengono create e pubblicate al momento della compilazione del modello di dispositivo per il dispositivo, quindi saranno coerenti tra tutti i dispositivi connessi o simulati.

La vista **Dashboard** offre una panoramica dei dati di telemetria e delle proprietà, inviati dal dispositivo, che sono destinati all'operatore.

La scheda **Proprietà** consentirà di modificare le proprietà cloud e le proprietà di lettura/scrittura del dispositivo.

La scheda **Comandi** consentirà di eseguire comandi modellati come parte del modello di dispositivo.

>[!div class="mx-imgBorder"] 
>![Viste del tutore per il ginocchio](media/knee-brace-dashboard.png)

### <a name="data-export"></a>Esportazione dati

L'esportazione dei dati consente di esportare i dati del dispositivo IoT Central continuamente in altri servizi di Azure, tra cui l'[API di Azure per FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir).

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminarla scegliendo **Amministrazione > Impostazioni applicazione** e facendo clic su **Elimina**.

>[!div class="mx-imgBorder"] 
>![Eliminare l'app](media/admin-delete.png)

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per imparare a creare un dashboard del provider che si connetta all'applicazione IoT Central.

> [!div class="nextstepaction"]
> [Creare un dashboard del provider](howto-health-data-triage.md)