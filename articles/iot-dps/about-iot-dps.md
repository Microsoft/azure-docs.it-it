---
title: Panoramica del servizio Device Provisioning in hub IoT di Azure | Microsoft Docs
description: Questo articolo descrive il provisioning in Azure con il servizio Device Provisioning e l'hub IoT.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 6b33b866a10ad4a44cef14f3c86d8ca1f40c4750
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965375"
---
# <a name="provisioning-devices-with-azure-iot-hub-device-provisioning-service"></a>Provisioning di dispositivi con il servizio Device Provisioning in hub IoT di Azure
Microsoft Azure include un ampio set di servizi cloud pubblici integrati per tutte le esigenze di soluzioni IoT. Il servizio Device Provisioning in hub IoT è un servizio helper per Hub IoT che consente di effettuare il provisioning JIT automatizzato nell'hub IoT corretto senza alcun intervento dell'utente. Consente di effettuare il provisioning di milioni di dispositivi in modo sicuro e scalabile.

## <a name="when-to-use-device-provisioning-service"></a>Quando usare il servizio Device Provisioning
Esistono molti scenari in cui il servizio Device Provisioning rappresenta una scelta eccellente per connettere i dispositivi e configurarli nell'hub IoT, ad esempio:

* Provisioning automatico a una singola soluzione IoT senza informazioni di connessione hardcoded nell'hub IoT in fabbrica (configurazione iniziale)
* Bilanciamento del carico dei dispositivi tra più hub
* Connessione di dispositivi alla soluzione IoT del proprietario in base ai dati delle transazioni di vendita (multi-tenancy)
* Connessione di dispositivi a una soluzione IoT specifica a seconda del caso di utilizzo (isolamento della soluzione)
* Connessione di un dispositivo all'hub IoT con latenza minima (partizionamento dell'area geografica)
* Nuovo provisioning a seguito di una modifica nel dispositivo
* Distribuzione delle chiavi usate dal dispositivo per connettersi all'hub IoT (quando non usa i certificati X.509 per la connessione)

## <a name="behind-the-scenes"></a>Dietro le quinte
Tutti gli scenari elencati nella sezione precedente possono essere applicati usando il servizio Device Provisioning per effettuare il provisioning automatizzato con lo stesso flusso. Molti passaggi manuali tradizionalmente richiesti per il provisioning vengono automatizzati con il servizio Device Provisioning per ridurre il tempo di distribuzione di dispositivi IoT e il rischio di errori manuali. La sezione seguente descrive le operazioni che vengono eseguite prima del provisioning di un dispositivo. Il primo passaggio è manuale, mentre tutti gli altri sono automatici.

![Flusso di provisioning di base](./media/about-iot-dps/dps-provisioning-flow.png)

1. Il produttore del dispositivo aggiunge le informazioni di registrazione del dispositivo all'elenco di registrazione nel portale di Azure.
2. Il dispositivo contatta l'endpoint del servizio Device Provisioning impostato in fabbrica. Il dispositivo passa al servizio le informazioni di identificazione per dimostrare la propria identità.
3. Il servizio Device Provisioning verifica l'identità del dispositivo convalidando la chiave e l'ID di registrazione rispetto alla voce che trova nell'elenco di registrazione tramite una richiesta di verifica nonce ([Trusted Platform Module](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) o una verifica X.509 standard (X.509).
4. Il servizio Device Provisioning registra il dispositivo con un hub IoT e popola lo [stato del dispositivo gemello desiderato](../iot-hub/iot-hub-devguide-device-twins.md).
5. L'hub IoT restituisce le informazioni sull'ID del dispositivo al servizio.
6. Il servizio Device Provisioning restituisce al dispositivo le informazioni di connessione all'hub IoT. Il dispositivo può ora iniziare a inviare i dati direttamente all'hub IoT.
7. Il dispositivo si connette all'hub IoT.
8. Il dispositivo ottiene lo stato desiderato dal dispositivo gemello nell'hub IoT.

## <a name="provisioning-process"></a>Processo di provisioning
Il processo di distribuzione di un dispositivo a cui partecipa il servizio Device Provisioning prevede due fasi distinte che possono essere svolte in maniera indipendente:

* La **fase di produzione** che riguarda la costruzione e la preparazione in fabbrica del dispositivo.
* La **fase di configurazione del cloud** in cui il servizio Device Provisioning viene configurato per il provisioning automatico.

Entrambe queste fasi rientrano senza soluzione di continuità nei processi di produzione e di distribuzione esistenti. Il servizio Device Provisioning semplifica anche alcuni processi di distribuzione che richiedono molto lavoro manuale per ottenere le informazioni di connessione sul dispositivo.

### <a name="manufacturing-step"></a>Fase di produzione
Questa fase riguarda tutto ciò che accade nella linea di produzione. I ruoli coinvolti in questa fase includono il progettista e il produttore dei componenti al silicio, l'integratore e/o il produttore finale del dispositivo. Questa fase riguarda la creazione dell'hardware.

Il servizio Device Provisioning non introduce un nuovo passaggio nel processo di produzione, piuttosto si unisce al passaggio esistente che installa il software iniziale e (idealmente) il modulo di protezione hardware nel dispositivo. Invece di creare un ID dispositivo in questo passaggio, il dispositivo viene programmato con le informazioni del servizio di provisioning in modo che all'accensione esegua automaticamente la chiamata al servizio di provisioning per ottenere le informazioni di connessione e l'assegnazione della soluzione IoT.

In questa fase il produttore fornisce anche le informazioni della chiave di identificazione al distributore/operatore del dispositivo. Questa operazione può essere semplice quanto confermare che tutti i dispositivi hanno un certificato X.509 generato da un certificato di firma ottenuto dal distributore/operatore del dispositivo o complessa quanto estrarre la parte pubblica di una chiave di verifica autenticità TPM da ogni dispositivo TPM. Questi servizi sono oggi offerti da molti produttori di componenti al silicio.

### <a name="cloud-setup-step"></a>Fase di configurazione del cloud
Questa fase riguarda la configurazione del cloud per un corretto provisioning automatico. In questa fase vengono in genere coinvolti due tipi di utenti: uno che conosce il modo in cui eseguire la configurazione iniziale dei dispositivi (un operatore del dispositivo) e uno che sa come devono essere suddivisi i dispositivi tra gli hub IoT (operatore della soluzione).

La configurazione iniziale del provisioning è un'operazione che avviene una volta sola ed è gestita in genere dall'operatore della soluzione. Dopo che è stato configurato, il servizio di provisioning non deve essere più modificato a meno che non sia necessario modificare il caso d'uso.

Il servizio configurato per il provisioning automatico deve successivamente essere preparato per la registrazione dei dispositivi. Questa operazione viene eseguita dall'operatore del dispositivo, che conosce la configurazione desiderata dei dispositivi e che deve assicurarsi che il servizio di provisioning sia in grado di attestare correttamente l'identità del dispositivo quando questo esegue la ricerca del proprio hub IoT. L'operatore del dispositivo riceve le informazioni relative alla chiave di identificazione dal produttore e le aggiunge all'elenco di registrazione. L'elenco di registrazione può essere soggetto ad aggiornamenti successivi man mano che vengono inserite nuove voci o quelle esistenti vengono aggiornate con le informazioni più recenti sui dispositivi.

## <a name="registration-and-provisioning"></a>Registrazione e provisioning
Il termine *provisioning* implica varie operazioni a seconda del settore in cui viene usato. Nel contesto di provisioning di dispositivi IoT alla relativa soluzione cloud, il provisioning è un processo in due fasi:

1. La prima consiste nello stabilire la connessione iniziale tra il dispositivo e la soluzione IoT tramite la registrazione del dispositivo.
2. La seconda consiste nell'applicare la configurazione adeguata al dispositivo in base ai requisiti specifici della soluzione in cui il dispositivo è stato registrato.

Una volta completati entrambi questi passaggi, il provisioning del dispositivo può considerarsi completato. Alcuni servizi cloud prevedono solo il primo passaggio del processo di provisioning, vale a dire la registrazione dei dispositivi presso l'endpoint della soluzione IoT, ma non la configurazione iniziale. Il servizio Device Provisioning automatizza entrambi i passaggi per semplificare l'esperienza di provisioning dei dispositivi.

## <a name="features-of-the-device-provisioning-service"></a>Funzionalità del servizio Device Provisioning
Il servizio Device Provisioning include molte funzionalità che lo rendono una soluzione ideale per il provisioning dei dispositivi.

* Supporto dell'**attestazione protetta** sia per le entità basate sul certificato X.509 che per quelle basate sul modulo TPM.
* **Elenco di registrazione** contenente il record completo dei dispositivi/gruppi di dispositivi che con il tempo potrebbero registrarsi. L'elenco di registrazione contiene le informazioni sulla configurazione desiderata del dispositivo registrato e può essere aggiornato in qualsiasi momento.
* **Più criteri di allocazione** per controllare la modalità con cui il servizio Device Provisioning assegna i dispositivi agli hub IoT per supportare gli scenari del cliente: latenza più bassa, distribuzione ponderata in modo uniforme (impostazione predefinita) e configurazione statica tramite elenco di registrazione. La latenza viene determinata con lo stesso metodo di [Gestione traffico](../traffic-manager/traffic-manager-routing-methods.md#performance).
* **Monitoraggio e registrazione diagnostica** per assicurarsi che tutto funzioni correttamente.
* **Supporto di più hub** per consentire al servizio Device Provisioning di assegnare dispositivi a più hub IoT. Il servizio Device Provisioning può dialogare con gli hub tramite più sottoscrizioni di Azure.
* **Supporto tra aree** per consentire al servizio Device Provisioning di assegnare i dispositivi a hub IoT in altre aree.
* **Crittografia per i dati inattivi** per crittografare e decrittografare i dati nel servizio Device Provisioning con la crittografia AES a 256 bit, una delle crittografie a blocchi più solide disponibili, conforme a FIPS 140-2.


Per altre informazioni sui concetti e sulle funzionalità del provisioning di dispositivi, vedere l'argomento relativo alla terminologia del [servizio Device Provisioning](concepts-service.md) insieme agli altri argomenti concettuali nella stessa sezione.

## <a name="cross-platform-support"></a>Supporto multipiattaforma
Come tutti i servizi IoT di Azure, il servizio Device Provisioning opera su più piattaforme con vari sistemi operativi. Azure offre SDK open source in un'ampia gamma di [lingue](https://github.com/Azure/azure-iot-sdks) per facilitare la connessione dei dispositivi e la gestione del servizio. Il servizio Device Provisioning supporta i protocolli seguenti per la connessione di dispositivi:

* HTTPS
* AMQP
* AMQP su WebSocket
* MQTT
* MQTT su WebSocket

Il servizio Device Provisioning supporta le connessioni HTTPS solo per le operazioni nel servizio.

## <a name="regions"></a>Regioni
Il servizio Device Provisioning è disponibile in molte aree. L'elenco aggiornato delle aree esistenti e annunciate di recente per i tutti i servizi è disponibile in [Aree di Azure](https://azure.microsoft.com/regions/). È possibile controllare la disponibilità del servizio Device Provisioning nella pagina [Stato di Azure](https://azure.microsoft.com/status/).

> [!NOTE]
> il servizio Device Provisioning è globale e non vincolato a una località. Tuttavia, è necessario specificare un'area in cui risiederanno i metadati associati al profilo del servizio Device Provisioning.

## <a name="availability"></a>Disponibilità
Per il servizio Device Provisioning esiste un Contratto di servizio con il 99,9% di disponibilità, che è possibile [leggere](https://azure.microsoft.com/support/legal/sla/iot-hub/). La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/) descrive la disponibilità garantita di Azure nel suo complesso.

## <a name="quotas"></a>Quote
Ogni sottoscrizione di Azure ha limiti di quota predefiniti che possono influire sull'ambito della soluzione IoT del cliente. Il limite attuale per ogni sottoscrizione è di 10 servizi Device Provisioning per sottoscrizione.

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

Per altre informazioni sui limiti di quota:
* [Limiti del servizio di sottoscrizione di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="related-azure-components"></a>Componenti di Azure correlati
Il servizio Device Provisioning automatizza il provisioning dei dispositivi con l'hub IoT di Azure. Altre informazioni sull'[hub IoT](../iot-hub/index.yml).

## <a name="next-steps"></a>Passaggi successivi
Dopo questa panoramica del provisioning dei dispositivi IoT in Azure, il passaggio successivo consiste nel provare uno scenario di IoT end-to-end.

[Configurare il servizio Device Provisioning in hub IoT con il portale di Azure](quick-setup-auto-provision.md)

[Creare ed effettuare il provisioning di un dispositivo simulato](quick-create-simulated-device.md)

[Configurare il dispositivo per il provisioning](tutorial-set-up-device.md)