---
title: Risoluzione dei problemi - Azure IoT Edge | Microsoft Docs
description: Usare questo articolo per acquisire competenze diagnostiche standard per Azure IoT Edge, come il recupero dello stato e dei log dei componenti e la risoluzione di problemi comuni
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 13eab175356ed1ec20caa3263ba00d0563384f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064386"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemi comuni e soluzioni per Azure IoT Edge

Se si verificano problemi durante l'esecuzione di Azure IoT Edge nel proprio ambiente, usare questo articolo come guida per la risoluzione.

## <a name="run-the-iotedge-check-command"></a>Eseguire il comando 'check' iotedge

Il primo passaggio per la risoluzione `check` dei problemi relativi a IoT Edge deve essere l'utilizzo del comando, che esegue una raccolta di test di configurazione e connettività per i problemi comuni. Il `check` comando è disponibile nella [versione 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) e successive.

È possibile `check` eseguire il comando come `--help` segue o includere il flag per visualizzare un elenco completo delle opzioni:

* In Linux:

  ```bash
  sudo iotedge check
  ```

* In Windows:

  ```powershell
  iotedge check
  ```

I tipi di controlli eseguiti dallo strumento possono essere classificati come:

* Controlli di configurazione: esamina i dettagli che potrebbero impedire ai dispositivi Edge di connettersi al cloud, inclusi i problemi con *config.yaml* e il motore contenitore.
* Controlli di connessione: verifica che il runtime di IoT Edge possa accedere alle porte nel dispositivo host e che tutti i componenti IoT Edge possano connettersi all'hub IoT.
* Controlli di conformità della produzione: cerca le procedure consigliate per la produzione, ad esempio lo stato dei certificati dell'autorità di certificazione (CA) del dispositivo e la configurazione del file di registro dei moduli.

Per un elenco completo dei controlli diagnostici, vedere Funzionalità di [risoluzione dei problemi integrata](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>Raccogliere informazioni di debug con il comando 'support-bundle' iotedge

Quando è necessario raccogliere i log da un dispositivo IoT `support-bundle` Edge, il modo più pratico consiste nell'utilizzare il comando. Per impostazione predefinita, questo comando raccoglie modulo, Gestione sicurezza Edge IoT e i log del motore del contenitore, l'output JSON 'controllo iotedge' e altre utili informazioni di debug. Li comprime in un unico file per una facile condivisione. Il `support-bundle` comando è disponibile nella [versione 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) e versioni successive.

Eseguire `support-bundle` il comando `--since` con il flag per specificare per quanto tempo dal passato si desidera ottenere i registri. Ad `6h` esempio, verranno ottenuti `6d` i registri dalle `6m` ultime 6 ore, dagli ultimi 6 giorni, dagli ultimi 6 minuti e così via. Includere `--help` il flag per visualizzare un elenco completo delle opzioni.


* In Linux:

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* In Windows:

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> L'output `support-bundle` del comando può contenere i nomi di host, dispositivi e moduli, le informazioni registrate dai moduli, ecc. Si prega di essere consapevoli di questo se si condivide l'output in un forum pubblico.

## <a name="standard-diagnostic-steps"></a>Procedura di diagnostica standard

Se si verifica un problema, è possibile ottenere ulteriori informazioni sullo stato del dispositivo IoT Edge esaminando i log del contenitore e i messaggi che passano da e verso il dispositivo. Usare i comandi e gli strumenti illustrati in questa sezione per raccogliere informazioni.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Controllare lo stato di IoT Edge Security Manager e dei relativi registri

In Linux:

* Per visualizzare lo stato di IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

* Per visualizzare i log di IoT Edge Security Manager:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Per visualizzare log più dettagliati di IoT Edge Security Manager:

  * Modificare le impostazioni del daemon iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Aggiornare le righe seguenti:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Riavviare il daemon di sicurezza di IoT Edge:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

In Windows:

* Per visualizzare lo stato di IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

* Per visualizzare i log di IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se IoT Edge Security Manager non è in esecuzione, verificare il file di configurazione yaml

> [!WARNING]
> I file YAML non possono contenere tabulazioni come rientro. In alternativa usare due spazi. Gli elementi di primo livello non devono avere spazi iniziali.

In Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Controllare i log dei contenitori per eventuali problemi

Non appena il daemon di sicurezza di IoT Edge è in esecuzione, esaminare i log dei contenitori per rilevare eventuali problemi. Iniziare con i contenitori distribuiti, quindi esaminare i contenitori che costituiscono il runtime di IoT Edge: edgeAgent e edgeHub.Start with your deployed containers, then look at the containers that make up the IoT Edge runtime: edgeAgent and edgeHub. I log dell'agente IoT Edge forniscono in genere informazioni sul ciclo di vita di ogni contenitore. I log dell'hub IoT Edge forniscono informazioni sulla messaggistica e sul routing.

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Visualizzare i messaggi che passano attraverso l'hub IoT Edge

È possibile visualizzare i messaggi che passano attraverso l'hub IoT Edge e raccogliere informazioni dettagliate dai log dettagliati dai contenitori di runtime. Per attivare i log dettagliati su questi contenitori, impostare `RuntimeLogLevel` nel file di configurazione yaml. Per aprire il file:

In Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Per impostazione predefinita, l'elemento `agent` avrà un aspetto simile all'esempio seguente:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Sostituire `env: {}` con:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > I file con estensione yalm non possono contenere tabulazioni per i rientri. In alternativa usare due spazi. Gli elementi di primo livello non possono avere spazi vuoti iniziali.

Salvare il file e riavviare il gestore sicurezza IoT Edge.

È anche possibile verificare i messaggi inviati tra l'hub IoT e i dispositivi IoT Edge. Visualizzare questi messaggi utilizzando [l'estensione Hub IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)di Azure per Visual Studio Code . Per altre informazioni, vedere [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Strumento utile quando si sviluppa con Azure IoT).

### <a name="restart-containers"></a>Riavviare i contenitori

Dopo avere esaminato i log e i messaggi per raccogliere informazioni, è possibile provare a riavviare i contenitori:

```cmd
iotedge restart <container name>
```

Riavviare i contenitori di runtime di IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Riavviare IoT Edge Security Manager

Se il problema persiste, è possibile provare a riavviare IoT Edge Security Manager.

In Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

In Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>L'agente IoT Edge si arresta dopo circa un minuto

Il modulo edgeAgent viene avviato e viene eseguito correttamente per circa un minuto, quindi si arresta. I registri indicano che l'agente IoT Edge tenta di connettersi all'hub IoT tramite AMQP, quindi tenta di connettersi utilizzando AMQP tramite WebSocket. Quando l'operazione non riesce, l'agente IoT Edge viene chiuso.

Registri edgeAgent di esempio:Example edgeAgent logs:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Causa principale**

Una configurazione di rete nella rete host impedisce all'agente IoT Edge di raggiungere la rete. L'agente prova prima a connettersi tramite AMQP (porta 5671). Se la connessione non riesce, prova WebSocket (porta 443).

Il runtime di IoT Edge configura una rete per ognuno dei moduli usati per la comunicazione. In Linux questa rete è una rete di bridge. In Windows si usa la rete NAT. Questo problema è più comune nei dispositivi Windows con contenitori Windows che usano la rete NAT.

**Risoluzione**

Verificare che sia presente un instradamento a Internet per gli indirizzi IP assegnati a questa rete di bridge/NAT. Talvolta una configurazione VPN nell'host esegue l'override della rete di IoT Edge.

## <a name="iot-edge-hub-fails-to-start"></a>L'hub IoT Edge non viene avviato

Il modulo edgeHub non viene avviato e stampa il seguente messaggio nei registri:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Causa principale**

Un altro processo nel computer host è associato alla porta 443. L'hub IoT Edge esegue il mapping delle porte 5671 e 443 per l'utilizzo in scenari gateway. Questo mapping delle porte non riesce se un altro processo è già associato a questa porta.

**Risoluzione**

Individuare e arrestare il processo che usa la porta 443. Questo processo è in genere un server Web.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>L'agente IoT Edge non può accedere all'immagine di un modulo (403)

Un contenitore non viene eseguito e i registri edgeAgent mostrano un errore 403.

**Causa principale**

L'agente IoT Edge non dispone delle autorizzazioni per accedere all'immagine di un modulo.

**Risoluzione**

Verificare che le credenziali del registro siano specificate correttamente nel manifesto di distribuzione

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Se il nome host non è valido, l'esecuzione del daemon di sicurezza di IoT Edge ha esito negativo.

Il comando `sudo journalctl -u iotedge` ha esito negativo e viene stampato il messaggio seguente:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Causa principale**

Il runtime di IoT Edge può supportare solo nomi host contenenti meno di 64 caratteri. I computer fisici non hanno in genere nomi host lunghi, ma il problema è più comune in una macchina virtuale. I nomi host generati automaticamente per le macchine virtuali Windows ospitate in Azure, in particolare, tendono a essere lunghi.

**Risoluzione**

Quando viene visualizzato questo errore, è possibile risolvere il problema configurando il nome DNS della macchina virtuale e quindi impostando il nome DNS come nome host nel comando di configurazione.

1. Nel portale di Azure passare alla pagina di panoramica della macchina virtuale.
2. Selezionare **Configura** sotto Nome DNS. Se la macchina virtuale dispone già di un nome DNS configurato, non è necessario configurarne uno nuovo.

   ![Configurare il nome DNS della macchina virtuale](./media/troubleshoot/configure-dns.png)

3. Specificare un valore per **Etichetta del nome DNS** e selezionare **Salva**.
4. Copiare il nuovo nome DNS, che deve essere nel formato ** \<\>DNSnamelabel\< . vmlocation\>.cloudapp.azure.com**.
5. All'interno della macchina virtuale, usare il comando seguente per configurare il runtime di IoT Edge con il nome DNS:

   * In Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * In Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemi di stabilità nei dispositivi con risorse limitate

È possibile che si verifichino problemi di stabilità nei dispositivi con limiti come Raspberry Pi, in particolare se usati come gateway. I problemi includono eccezioni di memoria insufficiente nel modulo dell'hub Edge, impossibilità di connessione dei dispositivi downstream o interruzione dell'invio dei messaggi dei dati di telemetria da parte del dispositivo dopo alcune ore.

**Causa principale**

L'hub IoT Edge, che fa parte del runtime ioT Edge, è ottimizzato per le prestazioni per impostazione predefinita e tenta di allocare grandi blocchi di memoria. Questa ottimizzazione non è ideale per i dispositivi Edge con limiti e può causare problemi di stabilità.

**Risoluzione**

Per l'hub IoT Edge, impostare una variabile di ambiente **OptimizeForPerformance** **su false**. Esistono due modi per impostare le variabili di ambiente:There are two ways to set environment variables:

Nel portale di Azure:

Nell'hub IoT selezionare il dispositivo IoT Edge e nella pagina dei dettagli del dispositivo e **selezionare Imposta** > **impostazioni**runtime moduli . Creare una variabile di ambiente per il modulo Hub Edge denominata *OptimizeForPerformance* impostata su *false*.

![OptimizeForPerformance impostato su false](./media/troubleshoot/optimizeforperformance-false.png)

**O**

Nel manifesto della distribuzione:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Non è possibile ottenere i log del daemon di IoT Edge su Windows

Se si verifica un'eccezione di tipo EventLogException quando si usa `Get-WinEvent` su Windows, verificare le voci del Registro di sistema.

**Causa principale**

Il comando di PowerShell `Get-WinEvent` si basa su una voce del Registro di sistema che deve essere presente per trovare i log da uno specifico `ProviderName`.

**Risoluzione**

Impostare una voce del Registro di sistema per il daemon di IoT Edge. Creare un file **iotedge.reg** con il contenuto seguente e importarlo nel Registro di sistema di Windows facendovi doppio clic o usando il comando `reg import iotedge.reg`:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Il modulo IoT Edge non riesce a inviare un messaggio a edgeHub con errore 404

Il modulo IoT Edge non riesce a inviare un messaggio a edgeHub con errore `Module not found` 404. Il daemon di IoT Edge stampa il messaggio seguente nei log:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Causa principale**

Per motivi di sicurezza, il daemon di IoT Edge impone l'identificazione del processo a tutti i moduli che si connettono a edgeHub. Verifica che tutti i messaggi inviati da un modulo provengano dall'ID del processo principale del modulo. Se viene inviato un messaggio da un modulo da un ID di processo diverso rispetto a quello inizialmente stabilito, rifiuterà il messaggio con un messaggio di errore 404.

**Risoluzione**

A partire dalla versione 1.0.7, tutti i processi del modulo sono autorizzati a connettersi. Se l'aggiornamento alla 1.0.7 non è possibile, completare i passaggi seguenti. Per ulteriori informazioni, vedere il changelog di [versione 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Verificare che il modulo IoT Edge personalizzato usi sempre lo stesso ID di processo per inviare messaggi a edgeHub. Ad esempio, assicurati `ENTRYPOINT` di `CMD` invece del comando `CMD` nel file Docker, poiché porterà a un ID di processo `ENTRYPOINT` per il modulo e a un altro ID di processo per il comando bash che esegue il programma principale, mentre porterà a un singolo ID processo.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Regole di configurazione di firewall e porte per la distribuzione di IoT Edge

Azure IoT Edge consente la comunicazione da un server locale al cloud di Azure usando i protocolli dell'hub IoT supportati, vedere Scelta di un protocollo di [comunicazione.](../iot-hub/iot-hub-devguide-protocols.md) Per una maggiore sicurezza, i canali di comunicazione tra Azure IoT Edge e hub IoT di Azure sono sempre configurati per essere in uscita. Questa configurazione si basa sul [modello di comunicazione assistita dei servizi](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), che consente di ridurre la superficie di attacco esplorabile da un'entità dannosa. Le comunicazioni in ingresso sono necessarie solo per scenari specifici in cui l'hub IoT di Azure deve eseguire il push dei messaggi al dispositivo Azure IoT Edge. I messaggi da cloud a dispositivo sono protetti tramite canali sicuri TLS e possono essere protetti ulteriormente tramite certificati X.509 e moduli di dispositivo TPM. Il gestore sicurezza di Azure IoT Edge stabilisce come attivare la comunicazione, vedere [Gestore sicurezza di Azure IoT Edge](../iot-edge/iot-edge-security-manager.md).

Sebbene IoT Edge offra una configurazione avanzata per la protezione del runtime e dei moduli distribuiti di Azure IoT Edge, dipende comunque dal computer e dalla configurazione di rete sottostanti. Di conseguenza, è imperativo garantire che siano configurate regole di rete e firewall adeguate per una comunicazione edge-cloud sicura. La tabella seguente può essere usata come linea guida quando è ospitata la regola del firewall di configurazione per i server sottostanti in cui è ospitato il runtime di Azure IoT Edge:The following table can be used as a guideline when configuration firewall rules for the underlying servers where Azure IoT Edge runtime is hosted:

|Protocollo|Porta|In ingresso|In uscita|Materiale sussidiario|
|--|--|--|--|--|
|MQTT|8883|BLOCCATO (impostazione predefinita)|BLOCCATO (impostazione predefinita)|<ul> <li>Configurare i dati in uscita in modo che siano Aperti quando si usa MQTT come protocollo di comunicazione.<li>1883 per MQTT non è supportato da IoT Edge. <li>Le connessioni in ingresso devono essere bloccate.</ul>|
|AMQP|5671|BLOCCATO (impostazione predefinita)|APERTO (impostazione predefinita)|<ul> <li>Protocollo di comunicazione predefinito per IoT Edge. <li> Deve essere configurato per essere Aperto se Azure IoT Edge non è configurato per altri protocolli supportati o AMQP è il protocollo di comunicazione desiderato.<li>5672 per AMQP non è supportato da IoT Edge.<li>Bloccare questa porta quando Azure IoT Edge usa un protocollo supportato dall'hub IoT diverso.<li>Le connessioni in ingresso devono essere bloccate.</ul></ul>|
|HTTPS|443|BLOCCATO (impostazione predefinita)|APERTO (impostazione predefinita)|<ul> <li>Configurare le connessioni in uscita come Aperto sulla porta 443 per il provisioning di IoT Edge. Questa configurazione è necessaria quando si usano script manuali o il servizio Device Provisioning di Azure IoT. <li>La connessione In ingresso deve essere Aperta solo per scenari specifici: <ul> <li>  Se si dispone di un gateway trasparente con dispositivi foglia che possono inviare richieste di metodo. In questo caso, non occorre che la porta 443 sia aperta a reti esterne per connettersi a IotHub o fornire servizi IoTHub tramite Azure IoT Edge. Pertanto, la regola in ingresso potrebbe essere limitata all'apertura solo in ingresso dalla rete interna. <li> Per scenari da client a dispositivo (C2D).</ul><li>80 per HTTP non è supportato da IoT Edge.<li>Se i protocolli non HTTP (ad esempio, AMQP o MQTT) non possono essere configurati nell'azienda. I messaggi possono essere inviati tramite WebSocket. In questo caso, la porta 443 verrà usata per la comunicazione WebSocket.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Il modulo Dell'agente perimetrale segnala continuamente "file di configurazione vuoto" e nessun modulo viene avviato nel dispositivo

Il dispositivo ha problemi ad avviare i moduli definiti nella distribuzione. Solo edgeAgent è in esecuzione ma continuamente segnalando 'file di configurazione vuoto...'.

**Causa principale**

Per impostazione predefinita, IoT Edge avvia i moduli nella propria rete di contenitori isolati. Il dispositivo potrebbe avere problemi con la risoluzione dei nomi DNS all'interno di questa rete privata.

**Risoluzione**

**Opzione 1: Impostare il server DNS nelle impostazioni del motore del contenitoreOption 1: Set DNS server in container engine settings**

Specificare il server DNS per l'ambiente nelle impostazioni del motore contenitore, che verrà applicato a tutti i moduli contenitore avviati dal motore. Creare un `daemon.json` file denominato specificando il server DNS da utilizzare. Ad esempio:

```json
{
    "dns": ["1.1.1.1"]
}
```

Nell'esempio precedente il server DNS viene impostato su un servizio DNS accessibile pubblicamente. Se il dispositivo perimetrale non è in grado di accedere a questo indirizzo IP dal relativo ambiente, sostituirlo con l'indirizzo del server DNS accessibile.

Posizionare `daemon.json` nella posizione corretta per la piattaforma:

| Piattaforma | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| Host Windows con contenitori Windows | `C:\ProgramData\iotedge-moby\config` |

Se il percorso `daemon.json` contiene già un file, aggiungere la chiave **dns** e salvare il file.

Riavviare il motore contenitore per ottenere gli aggiornamenti.

| Piattaforma | Comando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin Powershell) | `Restart-Service iotedge-moby -Force` |

**Opzione 2: Impostare il server DNS nella distribuzione di Edge IoT per moduloOption 2: Set DNS server in IoT Edge deployment per module**

È possibile impostare il server DNS per createOptions di ogni modulo nella distribuzione di IoT Edge.You can set DNS server for each module's *createOptions* in the IoT Edge deployment. Ad esempio:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Assicurarsi di impostare questa configurazione anche per i moduli *edgeAgent* e *edgeHub.*

## <a name="next-steps"></a>Passaggi successivi

Se si ritiene di aver rilevato un bug nella piattaforma di IoT Edge, [Inviare un problema](https://github.com/Azure/iotedge/issues) in modo da poter migliorare l'esperienza.

In caso di altre domande, creare una [Richiesta di supporto](https://portal.azure.com/#create/Microsoft.Support) per assistenza.
