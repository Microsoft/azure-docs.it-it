---
title: Preparare la distribuzione della soluzione nell'ambiente di produzione Azure IoT Edge
description: Informazioni su come portare la soluzione Azure IoT Edge dallo sviluppo alla produzione, inclusa la configurazione dei dispositivi con i certificati appropriati e la creazione di un piano di distribuzione per gli aggiornamenti del codice futuri.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: fda69d582f26b0c9189898bb5c8b0004a1e47360
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722770"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>Preparare la distribuzione della soluzione IoT Edge alla produzione

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Una volta pronti a portare la soluzione IoT Edge dallo sviluppo alla produzione, assicurarsi che sia configurata per prestazioni costanti.

Le informazioni fornite in questo articolo non sono tutte uguali. Per assicurarsi di definire le priorità, ogni sezione inizia con elenchi che dividono il lavoro in due sezioni: **importante** da completare prima di passare all'ambiente di produzione, o **utile** da sapere.

## <a name="device-configuration"></a>Configurazione del dispositivo

I dispositivi IoT Edge possono essere qualsiasi cosa, da un dispositivo Raspberry Pi a un computer portatile, a una macchina virtuale in esecuzione in un server. È possibile accedere al dispositivo fisicamente o tramite una connessione virtuale, oppure può essere isolato per lunghi periodi di tempo. In entrambi i casi, è necessario assicurarsi che sia configurato per funzionare in modo appropriato.

* **Importante**
  * Installare i certificati di produzione
  * Disporre di un piano di gestione dei dispositivi
  * Usare Moby come motore del contenitore

* **Utile**
  * Scegliere il protocollo di upstream

### <a name="install-production-certificates"></a>Installare i certificati di produzione

Ogni dispositivo IoT Edge nell'ambiente di produzione richiede un certificato di autorità di certificazione (CA) del dispositivo installato. Il certificato della CA viene quindi dichiarato nel runtime IoT Edge nel file di configurazione. Per gli scenari di sviluppo e test, il runtime di IoT Edge crea certificati temporanei se non sono stati dichiarati certificati nel file config. Tuttavia, questi certificati temporanei scadono dopo tre mesi e non sono sicuri per gli scenari di produzione. Per gli scenari di produzione, è necessario fornire il proprio certificato CA del dispositivo, da un'autorità di certificazione autofirmata o acquistata da un'autorità di certificazione commerciale.

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Attualmente, una limitazione in libiothsm impedisce l'utilizzo di certificati che scadono il 1 ° gennaio 2038 o successivo.

:::moniker-end

Per comprendere il ruolo del certificato della CA del dispositivo, vedere [Come Azure IoT Edge usa i certificati](iot-edge-certs.md).

Per altre informazioni su come installare i certificati in un dispositivo IoT Edge e farvi riferimento dal file di configurazione, vedere [gestire un certificato in un dispositivo IOT Edge](how-to-manage-device-certificates.md).

### <a name="have-a-device-management-plan"></a>Disporre di un piano di gestione dei dispositivi

Prima di rendere disponibili tutti i dispositivi nell'ambiente di produzione è necessario sapere come si intende gestire gli aggiornamenti futuri. Per un dispositivo IoT Edge, l'elenco dei componenti da aggiornare può includere:

* Il firmware del dispositivo
* Le librerie del sistema operativo
* Motore del contenitore, ad esempio Moby
* IoT Edge
* Certificati di CA

Per ulteriori informazioni, vedere [la pagina relativa all'aggiornamento del runtime IOT Edge](how-to-update-iot-edge.md). I metodi correnti per l'aggiornamento di IoT Edge richiedono l'accesso fisico o SSH al dispositivo IoT Edge. Se è necessario aggiornare molti dispositivi, è consigliabile aggiungere i passaggi di aggiornamento a uno script o usare uno strumento di automazione come Ansible.

### <a name="use-moby-as-the-container-engine"></a>Usare Moby come motore del contenitore

Un motore di contenitori è un prerequisito per qualsiasi dispositivo IoT Edge. Solo il motore moby è supportato nell'ambiente di produzione. Gli altri motori del contenitore, come Docker, funzionano con IoT Edge ed è comunque possibile usare questi motori per lo sviluppo. Il motore moby può essere ridistribuito quando usato con Azure IoT Edge e Microsoft fornisce assistenza per questo motore.

### <a name="choose-upstream-protocol"></a>Scegliere il protocollo di upstream

È possibile configurare il protocollo (che determina la porta usata) per la comunicazione upstream con l'hub Internet per l'agente IoT Edge e l'hub IoT Edge. Il protocollo predefinito è AMQP, ma è possibile modificarlo a seconda della configurazione di rete.

I due moduli runtime hanno una variabile di ambiente **UpstreamProtocol**. I valori validi per la variabile sono:

* MQTT
* AMQP
* MQTTWS
* AMQPWS

Configurare la variabile UpstreamProtocol per l'agente di IoT Edge nel file di configurazione nel dispositivo stesso. Se, ad esempio, il dispositivo IoT Edge si trova dietro un server proxy che blocca le porte AMQP, potrebbe essere necessario configurare l'agente IoT Edge per l'uso di AMQP su WebSocket (AMQPWS) per stabilire la connessione iniziale all'hub.

Una volta che il dispositivo IoT Edge si connette, assicurarsi di continuare a configurare la variabile UpstreamProtocol per entrambi i moduli runtime nelle distribuzioni future. È possibile trovare un esempio di questo processo in [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

## <a name="deployment"></a>Distribuzione

* **Utile**
  * Essere coerenti con il protocollo upstream
  * Configurare l'archiviazione host per i moduli di sistema
  * Ridurre lo spazio di memoria usato dall'hub IoT Edge
  * Non usare le versioni di debug delle immagini del modulo

### <a name="be-consistent-with-upstream-protocol"></a>Essere coerenti con il protocollo upstream

Se l'agente di IoT Edge è stato configurato nel dispositivo IoT Edge per usare un protocollo diverso da quello predefinito, è necessario dichiarare lo stesso protocollo in tutte le distribuzioni future (AMQP). Ad esempio, se il dispositivo IoT Edge si trova dietro un server proxy che blocca le porte AMQP,è probabile che il dispositivo sia stato configurato per connettersi tramite AMQP su WebSocket (AMQPWS). Quando si distribuiscono moduli al dispositivo, configurare lo stesso protocollo AMQPWS per l'agente di IoT Edge e l'hub IoT Edge. in caso contrario, il valore predefinito di AMQP sostituirà le impostazioni e impedirà la connessione.

È necessario configurare solo la variabile di ambiente UpstreamProtocol per l'agente IoT Edge e i moduli Hub IoT Edge. Tutti i moduli aggiuntivi adottano qualsiasi protocollo impostato nei moduli del runtime.

È possibile trovare un esempio di questo processo in [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

### <a name="set-up-host-storage-for-system-modules"></a>Configurare l'archiviazione host per i moduli di sistema

I moduli Hub IoT Edge e agente usano l'archiviazione locale per mantenere lo stato e abilitare la messaggistica tra i moduli, i dispositivi e il cloud. Per migliorare l'affidabilità e le prestazioni, configurare i moduli di sistema per l'uso dell'archiviazione nel file System host.

Per altre informazioni, vedere [archiviazione host per i moduli di sistema](how-to-access-host-storage-from-module.md).

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>Ridurre lo spazio di memoria usato dall'hub IoT Edge

Se si distribuiscono dispositivi vincolati con memoria limitata disponibile, è possibile configurare IoT Edge Hub per l'esecuzione in una capacità più semplificata e usare meno spazio su disco. Queste configurazioni limitano tuttavia le prestazioni dell'hub IoT Edge, quindi è possibile trovare il giusto equilibrio che funziona per la soluzione.

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>Non ottimizzare le prestazioni su dispositivi vincolati

Per impostazione predefinita, l'hub IoT Edge è ottimizzato per le prestazioni, quindi tenta di allocare blocchi di memoria di grandi dimensioni. Questa configurazione può causare problemi di stabilità in dispositivi più piccoli come Raspberry Pi. Se si distribuiscono dispositivi con risorse vincolate, potrebbe essere necessario impostare la variabile di ambiente **OptimizeForPerformance** su **false** nell'hub IOT Edge.

Quando **OptimizeForPerformance** è impostato su **true**, l'intestazione del protocollo MQTT usa il PooledByteBufferAllocator, che offre prestazioni migliori ma alloca una maggiore quantità di memoria. L'allocatore non funziona bene nei sistemi operativi a 32 bit o nei dispositivi con memoria insufficiente. Inoltre, quando viene ottimizzato per le prestazioni, RocksDb alloca più memoria per il proprio ruolo come provider di archiviazione locale.

Per altre informazioni, vedere [problemi di stabilità nei dispositivi più piccoli](troubleshoot-common-errors.md#stability-issues-on-smaller-devices).

#### <a name="disable-unused-protocols"></a>Disabilitare i protocolli non usati

Un altro modo per ottimizzare le prestazioni dell'hub IoT Edge e ridurre l'utilizzo della memoria consiste nel disattivare le intestazioni di protocollo per tutti i protocolli che non si stanno usando nella soluzione.

Le intestazioni di protocollo vengono configurate impostando variabili di ambiente booleane per il modulo Hub IoT Edge nei manifesti della distribuzione. Le tre variabili sono:

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

Tutte le tre variabili hanno *due caratteri di sottolineatura* e possono essere impostate su true o false.

#### <a name="reduce-storage-time-for-messages"></a>Ridurre il tempo di archiviazione per i messaggi

Il modulo Hub IoT Edge archivia temporaneamente i messaggi se non possono essere recapitati all'hub Internet per qualsiasi motivo. È possibile configurare il periodo di tempo in cui l'hub IoT Edge mantenga i messaggi non recapitati prima di lasciarli scadere. In caso di problemi di memoria sul dispositivo, è possibile ridurre il valore di **timeToLiveSecs** nel dispositivo gemello del modulo Hub IOT Edge.

Il valore predefinito del parametro timeToLiveSecs è 7200 secondi, che equivale a due ore.

### <a name="do-not-use-debug-versions-of-module-images"></a>Non usare le versioni di debug delle immagini del modulo

Quando si passa da scenari di test a scenari di produzione, ricordarsi di rimuovere le configurazioni di debug dai manifesti di distribuzione. Verificare che nessuna delle immagini del modulo nei manifesti di distribuzione disponga del suffisso **\. debug** . Se sono state aggiunte opzioni di creazione per esporre le porte nei moduli per il debug, rimuovere anche queste opzioni di creazione.

## <a name="container-management"></a>Gestione di contenitori

* **Importante**
  * Gestire l'accesso nel registro contenitori
  * Usare tag per gestire le versioni
* **Utile**
  * Archiviare i contenitori di runtime nel registro privato

### <a name="manage-access-to-your-container-registry"></a>Gestire l'accesso nel registro contenitori

Prima di distribuire moduli nei dispositivi IoT Edge di produzione, assicurarsi di controllare l'accesso al registro contenitori in modo che gli utenti esterni non possano accedere o apportare modifiche alle immagini del contenitore. Usare un registro contenitori privato, non pubblico, per gestire le immagini del contenitore.

Nelle esercitazioni e in altri documenti, viene indicato di usare nel dispositivo IoT Edge le stesse credenziali del registro contenitori usate nel computer di sviluppo. Queste istruzioni sono destinate solamente a facilitare la configurazione degli ambienti di test e di sviluppo e non devono essere seguite in uno scenario di produzione.

Per un accesso più protetto al registro di sistema, è possibile scegliere tra le [Opzioni di autenticazione](../container-registry/container-registry-authentication.md). Un'autenticazione più diffusa e consigliata consiste nell'usare un'entità servizio Active Directory particolarmente adatta per le applicazioni o i servizi per eseguire il pull delle immagini del contenitore in modo automatico o altrimenti automatico (senza titolo), come IoT Edge i dispositivi.

Per creare un'entità servizio, eseguire i due script come descritto in [creare un'entità servizio](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal). Questi script eseguono le attività seguenti:

* Il primo script crea l'entità servizio. Restituisce l'ID dell'entità servizio e la password dell'entità servizio. Archiviare i valori in modo sicuro nei record.

* Il secondo script crea le assegnazioni di ruolo da concedere all'entità servizio, che può essere eseguita successivamente, se necessario. Si consiglia di applicare il ruolo utente **acrPull** per il `role` parametro. Per un elenco dei ruoli, vedere [autorizzazioni e ruoli di container Registry di Azure](../container-registry/container-registry-roles.md).

Per eseguire l'autenticazione tramite un'entità servizio, specificare l'ID e la password dell'entità servizio ottenuti dal primo script. Specificare queste credenziali nel manifesto della distribuzione.

* Per nome utente o ID client specificare l'ID dell'entità servizio.

* Per la password o il segreto client, specificare la password dell'entità servizio.

> [!NOTE]
> Dopo l'implementazione di un'autenticazione di sicurezza avanzata, disabilitare l'impostazione **utente amministratore** in modo che l'accesso predefinito di nome utente/password non sia più disponibile. Nel registro contenitori del portale di Azure, dal menu riquadro a sinistra in **Impostazioni** selezionare **chiavi di accesso**.

### <a name="use-tags-to-manage-versions"></a>Usare tag per gestire le versioni

Un tag è un concetto di Docker che è possibile usare per distinguere le versioni dei contenitori docker. I tag sono suffissi come **1.0** che vanno alla fine di un repository del contenitore. Ad esempio, **mcr.microsoft.com/azureiotedge-agent:1.0**. I tag sono modificabili e possono essere modificati in modo da puntare a un altro contenitore in qualsiasi momento, in modo che il team debba concordare una convenzione da seguire quando si aggiornano le immagini del modulo.

I tag consentono inoltre di applicare gli aggiornamenti sui dispositivi IoT Edge. Quando si esegue il push di una versione aggiornata di un modulo nel registro contenitori, incrementare il tag. Eseguire quindi il push di una nuova distribuzione per i dispositivi con il tag incrementato. Il motore contenitore riconoscerà il tag incrementato come una nuova versione ed eseguirà il pull della versione più recente del modulo nel dispositivo.

#### <a name="tags-for-the-iot-edge-runtime"></a>Tag per il runtime di IoT Edge

Le immagini dell'agente IoT Edge e dell'hub di IoT Edge vengono contrassegnate con la versione di IoT Edge a cui sono associate. Esistono due diversi modi per usare i tag con le immagini di runtime:

* **Tag di versioni**. Vengono usati solo i primi due valori del numero di versione per ottenere l'immagine più recente corrispondente a tali cifre. Ad esempio, 1,1 viene aggiornato ogni volta che è presente una nuova versione per puntare alla versione 1.1. x più recente. Se il runtime del contenitore nel dispositivo IoT Edge esegue nuovamente il pull dell'immagine, i moduli del runtime vengono aggiornati alla versione più recente. Nelle distribuzioni dal portale di Azure vengono usati i tag di versioni per impostazione predefinita. *Questo approccio è consigliato per finalità di sviluppo.*

* **Tag specifici**. Vengono usati tutti e tre i valori del numero di versione per impostare in modo esplicito la versione dell'immagine. Ad esempio, 1.1.0 non cambierà dopo la versione iniziale. Quando si è pronti per l'aggiornamento, è possibile dichiarare un nuovo numero di versione nel manifesto della distribuzione. *Questo approccio è consigliato per finalità di produzione.*

### <a name="store-runtime-containers-in-your-private-registry"></a>Archiviare i contenitori di runtime nel registro privato

Si sa come archiviare le immagini del contenitore per i moduli di codice personalizzati nel registro di sistema privato di Azure, ma è anche possibile usarlo per archiviare le immagini del contenitore pubblico, ad esempio per i moduli di runtime edgeAgent e edgHub. Questa operazione potrebbe essere necessaria se si dispone di restrizioni del firewall molto rigide, perché questi contenitori di runtime vengono archiviati in Microsoft Container Registry (().

Ottenere le immagini con il comando Docker pull da inserire nel registro di sistema privato. Tenere presente che sarà necessario aggiornare le immagini con ogni nuova versione di IoT Edge Runtime.

| Contenitore IoT Edge Runtime | Comando pull di Docker |
| --- | --- |
| [Agente di Azure IoT Edge](https://hub.docker.com/_/microsoft-azureiotedge-agent) | `docker pull mcr.microsoft.com/azureiotedge-agent` |
| [Hub Azure IoT Edge](https://hub.docker.com/_/microsoft-azureiotedge-hub) | `docker pull mcr.microsoft.com/azureiotedge-hub` |

Assicurarsi quindi di aggiornare i riferimenti all'immagine nel file deployment.template.jsper i moduli di sistema edgeAgent e edgeHub. Sostituire `mcr.microsoft.com` con il nome del registro di sistema e il server per entrambi i moduli.

* EdgeAgent

    `"image": "<registry name and server>/azureiotedge-agent:1.1",`

* EdgeHub

    `"image": "<registry name and server>/azureiotedge-hub:1.1",`

## <a name="networking"></a>Rete

* **Utile**
  * Verificare la configurazione in uscita e in entrata
  * Consenti connessioni da dispositivi IoT Edge
  * Configurare la comunicazione tramite un proxy

### <a name="review-outboundinbound-configuration"></a>Verificare la configurazione in uscita e in entrata

I canali di comunicazione tra Azure IoT Edge e hub IoT di Azure sono sempre configurati per essere in uscita. Per la maggior parte degli scenari di IoT Edge, sono necessarie solo tre connessioni. Il motore del contenitore deve connettersi con il registro contenitori (o i registri) che contiene le immagini del modulo. Il runtime IoT Edge deve connettersi all'hub IoT per recuperare le informazioni di configurazione del dispositivo e per inviare i messaggi e i dati di telemetria. Se si usa il provisioning automatico, IoT Edge necessario connettersi al servizio Device provisioning. Per altre informazioni, vedere [Regole di configurazione del firewall e delle porte](troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="allow-connections-from-iot-edge-devices"></a>Consenti connessioni da dispositivi IoT Edge

Se per la configurazione della rete è necessario consentire esplicitamente le connessioni effettuate da IoT Edge dispositivi, esaminare l'elenco seguente di componenti IoT Edge:

* **Agente IoT Edge** apre una connessione MQTT/AMQP permanente all'hub IoT, possibilmente tramite WebSockets.
* **Hub di IoT Edge** apre una singola connessione AMQP permanente o più connessioni MQTT all'hub IoT, probabilmente tramite WebSockets.
* **IOT Edge servizio** esegue chiamate HTTPS intermittenti all'hub Internet.

In tutti i tre casi, il nome DNS dovrebbe corrisponde al modello \*.azure-devices.net.

Inoltre, il **motore del contenitore** effettua chiamate ai registri contenitori tramite HTTPS. Per recuperare le immagini del contenitore del runtime IoT Edge, il nome DNS è mcr.microsoft.com. Il motore del contenitore si connette ad altri registri in base alla configurazione della distribuzione.

Questo elenco di controllo è un punto di partenza per le regole del firewall:

   | URL (\* = carattere jolly) | Porte TCP in uscita | Utilizzo |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Registro Container Microsoft |
   | global.azure-devices-provisioning.net  | 443 | Accesso DPS (facoltativo) |
   | \*.azurecr.io | 443 | Registri contenitori personali e di terze parti |
   | \*.blob.core.windows.net | 443 | Scaricare Delta di immagini Container Registry di Azure dall'archiviazione BLOB |
   | \*.azure-devices.net | 5671, 8883, 443 | Accesso hub IoT |
   | \*.docker.io  | 443 | Accesso all'hub Docker (facoltativo) |

Alcune di queste regole del firewall vengono ereditate da Azure Container Registry. Per altre informazioni, vedere [configurare le regole per accedere a un registro contenitori di Azure dietro un firewall](../container-registry/container-registry-firewall-access-rules.md).

> [!NOTE]
> Per fornire un nome di dominio completo tra gli endpoint REST e i dati, a partire dal **15 giugno 2020,** l'endpoint dati Microsoft container Registry cambierà da `*.cdn.mscr.io` a `*.data.mcr.microsoft.com`  
> Per ulteriori informazioni, vedere [configurazione delle regole del firewall di Microsoft container Registry client](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)

Se non si vuole configurare il firewall per consentire l'accesso ai registri dei contenitori pubblici, è possibile archiviare le immagini nel registro contenitori privato, come descritto in [archiviare i contenitori di runtime nel registro](#store-runtime-containers-in-your-private-registry)di sistema privato.

### <a name="configure-communication-through-a-proxy"></a>Configurare la comunicazione tramite un proxy

Se i dispositivi vengono distribuiti su una rete che utilizza un server proxy, devono essere in grado di comunicare attraverso il proxy per raggiungere l'hub IoT e i registri contenitori. Per altre informazioni, vedere [Configurare un dispositivo IoT Edge per comunicare tramite un server proxy](how-to-configure-proxy-support.md).

## <a name="solution-management"></a>Gestione della soluzione

* **Utile**
  * Impostare i log e la diagnostica
  * Prendere in considerazione i test e le pipeline CI/CD

### <a name="set-up-logs-and-diagnostics"></a>Impostare i log e la diagnostica

In Linux il daemon IoT Edge usa i Journal come driver di registrazione predefinito. È possibile usare lo strumento della riga di comando `journalctl` per eseguire una query dei log daemon.

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

A partire dalla versione 1,2, IoT Edge si basa su più daemon. Mentre i log di ogni daemon possono essere sottoposti a query singolarmente `journalctl` , i `iotedge system` comandi rappresentano un modo pratico per eseguire query sui log combinati.

* `iotedge`Comando consolidato:

  ```bash
  sudo iotedge system logs
  ```

* `journalctl`Comando equivalente:

  ```bash
  journalctl -u aziot-edge -u aziot-identityd -u aziot-keyd -u aziot-certd -u aziot-tpmd
  ```

:::moniker-end

In Windows, il daemon di IoT Edge usa la diagnostica di PowerShell. Usare `Get-IoTEdgeLog` per eseguire query dei log da daemon. I moduli IoT Edge usano il driver JSON per la registrazione, che è l'impostazione predefinita.  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Quando si esegue il test di una distribuzione di IoT Edge, è generalmente possibile accedere ai dispositivi per recuperare i log e risolvere i problemi. In uno scenario di distribuzione, quell'opzione potrebbe non essere disponibile. Considerare in che modo raccogliere informazioni sui dispositivi in produzione. Una possibilità consiste nell'usare un modulo di registrazione che raccoglie le informazioni da altri moduli e le invia al cloud. Un esempio di un modulo di registrazione [logspout-loganalytics](https://github.com/veyalla/logspout-loganalytics), oppure è possibile progettare il proprio.

### <a name="place-limits-on-log-size"></a>Limiti per le dimensioni del log

Per impostazione predefinita, il motore di contenitori di Moby non imposta limiti per le dimensioni del log del contenitore. Nel corso del tempo questo può compromettere il riempimento del dispositivo con i log e l'esaurimento dello spazio su disco. Per evitare questo problema, prendere in considerazione le opzioni seguenti:

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>Opzione: impostare i limiti globali che si applicano a tutti i moduli contenitore

È possibile limitare le dimensioni di tutti i file di log del contenitore nelle opzioni di log del motore di contenitori. Nell'esempio seguente il driver di log viene impostato su `json-file` (scelta consigliata) con limiti per le dimensioni e il numero di file:

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

Aggiungere (o accodare) queste informazioni a un file denominato `daemon.json` e posizionarlo nella posizione corretta per la piattaforma del dispositivo.

| Piattaforma | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

Per rendere effettive le modifiche, è necessario riavviare il motore del contenitore.

#### <a name="option-adjust-log-settings-for-each-container-module"></a>Opzione: regolare le impostazioni del log per ogni modulo contenitore

Questa operazione può essere eseguita nella **createOptions** di ogni modulo. Ad esempio:

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

#### <a name="additional-options-on-linux-systems"></a>Opzioni aggiuntive nei sistemi Linux

* Configurare il motore di contenitori per inviare i log al `systemd` [Journal](https://docs.docker.com/config/containers/logging/journald/) impostando `journald` come driver di registrazione predefinito.

* Rimuovere periodicamente i log precedenti dal dispositivo installando uno strumento logrotate. Usare la specifica del file seguente:

   ```txt
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>Prendere in considerazione i test e le pipeline CI/CD

Per uno scenario di distribuzione di IoT Edge più efficiente, provare a integrare la distribuzione di produzione nel test e nelle pipeline CI/CD. Azure IoT Edge supporta più piattaforme di integrazione CI/CD, tra cui Azure DevOps. Per altre informazioni, vedere [Integrazione e distribuzione continue in Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [distribuzione automatica di IoT Edge](module-deployment-monitoring.md).
* Vedere come IoT Edge supporta l'[integrazione e la distribuzione continue](how-to-continuous-integration-continuous-deployment.md).
