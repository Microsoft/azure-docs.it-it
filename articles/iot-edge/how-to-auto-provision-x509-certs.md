---
title: Eseguire automaticamente il provisioning dei dispositivi con DPS usando i certificati X. 509-Azure IoT Edge | Microsoft Docs
description: Usare i certificati X. 509 per testare il provisioning automatico dei dispositivi per Azure IoT Edge con il servizio Device provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 44ea6546eb2099165071fd493ec8f890820c0688
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199825"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Creare ed effettuare il provisioning di un dispositivo IoT Edge usando certificati X. 509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Con il [servizio Device provisioning in hub Azure (DPS)](../iot-dps/index.yml), è possibile effettuare automaticamente il provisioning dei dispositivi IOT Edge usando i certificati X. 509. Se non si ha familiarità con il processo di provisioning automatico, vedere la panoramica sul [provisioning](../iot-dps/about-iot-dps.md#provisioning-process) prima di continuare.

Questo articolo illustra come creare una registrazione del servizio Device provisioning usando i certificati X. 509 in un dispositivo IoT Edge con i passaggi seguenti:

* Generare certificati e chiavi.
* Creare una registrazione singola per un dispositivo o una registrazione di gruppo per un set di dispositivi.
* Installare il runtime di IoT Edge e registrare il dispositivo con l'hub Internet.

L'uso di certificati X.509 come meccanismo di attestazione è un ottimo approccio per ridimensionare la produzione e semplificare il provisioning di dispositivi. I certificati X. 509 vengono in genere disposti in una catena di certificati attendibili. A partire da un certificato radice autofirmato o attendibile, ogni certificato nella catena firma il certificato inferiore successivo. Questo modello crea una catena delegata di trust dal certificato radice attraverso ogni certificato intermedio al certificato "foglia" finale installato in un dispositivo.

## <a name="prerequisites"></a>Prerequisiti

* Un hub IoT attivo.
* Un dispositivo fisico o virtuale da IoT Edge dispositivo.
* È installata la versione più recente di [git](https://git-scm.com/download/) .
* Un'istanza del servizio Device provisioning nell'hub Internet in Azure, collegata all'hub Internet delle cose.
  * Se non si dispone di un'istanza del servizio Device provisioning, seguire le istruzioni riportate in [configurare l'hub Internet delle](../iot-dps/quick-setup-auto-provision.md)cose.
  * Con il servizio Device Provisioning in esecuzione, copiare il valore di **Ambito ID** dalla pagina di panoramica. Questo valore viene usato quando si configura il runtime IoT Edge.

## <a name="generate-device-identity-certificates"></a>Generare certificati di identità del dispositivo

Il certificato di identità del dispositivo è un certificato foglia che si connette tramite una catena di certificati di trust al certificato dell'autorità di certificazione X. 509 principale (CA). Il certificato di identità del dispositivo deve avere il nome comune (CN) impostato sull'ID del dispositivo che si vuole includere nel dispositivo nell'hub Internet.

I certificati di identità del dispositivo vengono usati solo per il provisioning del dispositivo IoT Edge e per l'autenticazione del dispositivo con l'hub Azure. Non firmano certificati, a differenza dei certificati della CA che il dispositivo IoT Edge presenta ai moduli o ai dispositivi foglia per la verifica. Per ulteriori informazioni, vedere [Azure IOT Edge dettagli sull'utilizzo del certificato](iot-edge-certs.md).

Dopo aver creato il certificato di identità del dispositivo, è necessario disporre di due file: un file con estensione cer o PEM che contiene la parte pubblica del certificato e un file con estensione cer o PEM con la chiave privata del certificato. Se si prevede di usare la registrazione di gruppo in DPS, è necessaria anche la parte pubblica di un certificato CA intermedio o radice nella stessa catena di certificati di attendibilità.

Per configurare il provisioning automatico con X. 509 sono necessari i file seguenti:

* Il certificato di identità del dispositivo e il relativo certificato di chiave privata. Il certificato di identità del dispositivo viene caricato in DPS se si crea una registrazione singola. La chiave privata viene passata al runtime IoT Edge.
* Un certificato a catena completa, che deve avere almeno l'identità del dispositivo e i certificati intermedi. Il certificato a catena completa viene passato al runtime IoT Edge.
* Un certificato CA intermedio o radice dalla catena di certificati di attendibilità. Questo certificato viene caricato in DPS se si crea una registrazione di gruppo.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> Attualmente, una limitazione in libiothsm impedisce l'utilizzo di certificati che scadono il 1 ° gennaio 2038 o successivo.

:::moniker-end

### <a name="use-test-certificates-optional"></a>Usa certificati di test (facoltativo)

Se non si dispone di un'autorità di certificazione disponibile per creare nuovi certificati di identità e si vuole provare questo scenario, il Azure IoT Edge repository git contiene gli script che è possibile usare per generare i certificati di test. Questi certificati sono progettati solo per i test di sviluppo e non devono essere utilizzati nell'ambiente di produzione.

Per creare certificati di test, seguire la procedura descritta in [creare certificati demo per testare le funzionalità del dispositivo IOT Edge](how-to-create-test-certificates.md). Completare le due sezioni necessarie per configurare gli script di generazione del certificato e creare un certificato CA radice. Seguire quindi la procedura per creare un certificato di identità del dispositivo. Al termine, è necessario disporre della catena di certificati e della coppia di chiavi seguenti:

Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Sono necessari entrambi i certificati nel dispositivo IoT Edge. Se si prevede di usare la registrazione singola in DPS, si caricherà il file. cert. pem. Se si intende usare la registrazione di gruppo in DPS, è necessario anche un certificato CA intermedio o radice nella stessa catena di certificati di attendibilità da caricare. Se si usano i certificati demo, usare il `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` certificato per la registrazione di gruppo.

## <a name="create-a-dps-individual-enrollment"></a>Creare una registrazione singola DPS

Usare i certificati e le chiavi generati per creare una registrazione singola in DPS per un singolo dispositivo IoT Edge. Le registrazioni singole accettano la parte pubblica del certificato di identità del dispositivo e corrispondono al certificato nel dispositivo.

Se si intende eseguire il provisioning di più dispositivi IoT Edge, seguire i passaggi nella sezione successiva [creare una registrazione del gruppo DPS](#create-a-dps-group-enrollment).

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-at-scale.md).

Per ulteriori informazioni sulle registrazioni nel servizio Device provisioning, vedere [come gestire le registrazioni dei dispositivi](../iot-dps/how-to-manage-enrollments.md).

   > [!TIP]
   > Nell'interfaccia della riga di comando di Azure è possibile creare un gruppo di registrazione o di [registrazione](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) e usare il flag di **Abilitazione Edge** per specificare che un [dispositivo o un](/cli/azure/ext/azure-iot/iot/dps/enrollment) gruppo di dispositivi è un dispositivo IOT Edge.

1. Nella [portale di Azure](https://portal.azure.com)passare all'istanza del servizio Device provisioning in hub Internet.

1. In **le impostazioni** selezionare **Gestisci registrazioni**.

1. Selezionare **Aggiungi registrazione singola**, quindi completare la procedura seguente per configurare la registrazione:  

   * **Meccanismo**: selezionare **X. 509**.

   * **File con estensione PEM o cer del certificato primario**: caricare il file pubblico dal certificato di identità del dispositivo. Se sono stati usati gli script per generare un certificato di prova, scegliere il file seguente:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **ID dispositivo hub** Internet: specificare un ID per il dispositivo, se si vuole. È possibile usare gli ID dispositivo per identificare come destinazione un singolo dispositivo per la distribuzione di moduli. Se non si specifica un ID dispositivo, viene usato il nome comune (CN) nel certificato X. 509.

   * **IOT Edge dispositivo**: selezionare **true** per dichiarare che la registrazione è per un dispositivo IOT Edge.

   * **Selezionare gli hub di Internet delle cose a cui è possibile assegnare il dispositivo**: scegliere l'hub delle cose collegato a cui si vuole connettere il dispositivo. È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di allocazione selezionati.

   * **Stato iniziale del dispositivo gemello**: aggiungere un valore di tag da aggiungere al dispositivo gemello, se lo si desidera. È possibile usare i tag per i gruppi di destinazione dei dispositivi per la distribuzione automatica. Ad esempio:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selezionare **Salva**.

Ora che è presente una registrazione per questo dispositivo, il runtime di IoT Edge può effettuare automaticamente il provisioning del dispositivo durante l'installazione. Passare alla sezione [Install the IOT Edge Runtime](#install-the-iot-edge-runtime) per configurare il dispositivo IOT Edge.

## <a name="create-a-dps-group-enrollment"></a>Creare una registrazione del gruppo DPS

Usare i certificati e le chiavi generati per creare una registrazione di gruppo in DPS per più dispositivi IoT Edge. Le registrazioni di gruppo usano un certificato CA intermedio o radice dalla catena di certificati di attendibilità usata per generare i singoli certificati di identità del dispositivo.

Se invece si vuole eseguire il provisioning di un singolo dispositivo IoT Edge, seguire la procedura descritta nella sezione precedente, [creare una registrazione singola DPS](#create-a-dps-individual-enrollment).

Quando si crea una registrazione nel servizio Device Provisioning, si ha la possibilità di dichiarare un valore di **Stato dispositivo gemello iniziale**. Nel dispositivo gemello è possibile impostare tag per raggruppare i dispositivi in base a una qualsiasi metrica necessaria nella propria soluzione, come l'area, l'ambiente, la località o il tipo di dispositivo. Questi tag vengono usati per creare [distribuzioni automatiche](how-to-deploy-at-scale.md).

### <a name="verify-your-root-certificate"></a>Verificare il certificato radice

Quando si crea un gruppo di registrazione, è possibile usare un certificato verificato. È possibile verificare un certificato con DPS dimostrando di avere la proprietà del certificato radice. Per ulteriori informazioni, vedere [come eseguire la verifica del possesso per i certificati della CA X. 509](../iot-dps/how-to-verify-certificates.md).

1. Nella [portale di Azure](https://portal.azure.com)passare all'istanza del servizio Device provisioning in hub Internet.

1. Selezionare **certificati** dal menu a sinistra.

1. Selezionare **Aggiungi** per aggiungere un nuovo certificato.

1. Immettere un nome descrittivo per il certificato, quindi selezionare il file con estensione cer o PEM che rappresenta la parte pubblica del certificato X. 509.

   Se si usano i certificati demo, caricare il `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certificato.

1. Selezionare **Salva**.

1. Il certificato verrà ora elencato nella pagina **certificati** . Selezionarlo per aprire i dettagli del certificato.

1. Selezionare **genera codice di verifica** e quindi copiare il codice generato.

1. Indipendentemente dal fatto che il certificato della CA sia stato usato o che usi i certificati dimostrativi, è possibile usare lo strumento di verifica disponibile nel repository IoT Edge per verificare la prova del possesso. Lo strumento di verifica usa il certificato della CA per firmare un nuovo certificato con il codice di verifica specificato come nome del soggetto.

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Nella pagina dei dettagli del certificato nella portale di Azure caricare il certificato di verifica appena generato.

1. Selezionare **Verifica**.

### <a name="create-enrollment-group"></a>Crea gruppo di registrazione

Per ulteriori informazioni sulle registrazioni nel servizio Device provisioning, vedere [come gestire le registrazioni dei dispositivi](../iot-dps/how-to-manage-enrollments.md).

1. Nella [portale di Azure](https://portal.azure.com)passare all'istanza del servizio Device provisioning in hub Internet.

1. In **le impostazioni** selezionare **Gestisci registrazioni**.

1. Selezionare **Aggiungi gruppo di registrazione** e quindi completare i passaggi seguenti per configurare la registrazione:

   * **Nome gruppo**: specificare un nome memorabile per la registrazione del gruppo.

   * **Tipo di attestazione**: selezionare **certificato**.

   * **IOT Edge dispositivo**: selezionare **true**. Per la registrazione di un gruppo, tutti i dispositivi devono essere IoT Edge dispositivi o nessuno di essi può essere.

   * **Tipo di certificato**: selezionare **certificato CA** se si dispone di un certificato CA verificato archiviato con DPS o un **certificato intermedio** se si vuole caricare un nuovo file solo per questa registrazione.

   * **Certificato primario**: se si sceglie certificato CA nell'ultima sezione, scegliere il certificato dall'elenco a discesa. Se si sceglie certificato intermedio, caricare il file pubblico da un certificato della CA nella catena di certificati di attendibilità utilizzata per generare i certificati di identità del dispositivo.

   * **Selezionare gli hub di Internet delle cose a cui è possibile assegnare il dispositivo**: scegliere l'hub delle cose collegato a cui si vuole connettere il dispositivo. È possibile scegliere più hub e il dispositivo verrà assegnato a uno di essi in base ai criteri di allocazione selezionati.

   * **Stato iniziale del dispositivo gemello**: aggiungere un valore di tag da aggiungere al dispositivo gemello, se lo si desidera. È possibile usare i tag per i gruppi di destinazione dei dispositivi per la distribuzione automatica. Ad esempio:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. Selezionare **Salva**.

Ora che è presente una registrazione per questo dispositivo, il runtime di IoT Edge può effettuare automaticamente il provisioning del dispositivo durante l'installazione. Passare alla sezione successiva per configurare il dispositivo IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Installare il runtime IoT Edge.

Il runtime di IoT Edge viene distribuito in tutti i dispositivi IoT Edge. I relativi componenti vengono eseguiti in contenitori e consentono di distribuire altri contenitori al dispositivo in modo che sia possibile eseguire codice nella rete perimetrale.

Seguire i passaggi in [installare il runtime di Azure IOT Edge](how-to-install-iot-edge.md), quindi tornare a questo articolo per effettuare il provisioning del dispositivo.

Il provisioning X. 509 con DPS è supportato solo in IoT Edge versione 1.0.9 o successiva.

## <a name="configure-the-device-with-provisioning-information"></a>Configurare il dispositivo con le informazioni di provisioning

Una volta installato il runtime nel dispositivo, configurare il dispositivo con le informazioni usate per connettersi al servizio Device provisioning e all'hub Internet.

Sono disponibili le informazioni seguenti:

* Valore dell' **ambito dell'ID** DPS. È possibile recuperare questo valore dalla pagina Panoramica dell'istanza di DPS nell'portale di Azure.
* File della catena di certificati di identità del dispositivo nel dispositivo.
* Il file di chiave di identità del dispositivo nel dispositivo.
* ID di registrazione facoltativo. Se non viene specificato, l'ID viene estratto dal nome comune nel certificato di identità del dispositivo.

### <a name="linux-device"></a>Dispositivo Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Aprire il file di configurazione nel dispositivo IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Trovare la sezione configurazioni di provisioning del file. Rimuovere il commento dalle righe per il provisioning del certificato X. 509 DPS e verificare che le altre righe di provisioning siano impostate come commento.

   La `provisioning:` riga non deve contenere spazi vuoti precedenti ed è necessario rientrare gli elementi annidati di due spazi.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Aggiornare i valori di `scope_id` , `identity_cert` e `identity_pk` con le informazioni sul dispositivo e sul DPS.

   Quando si aggiungono le informazioni sulla chiave e sul certificato X. 509 al file config. YAML, i percorsi devono essere specificati come URI di file. Ad esempio:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Facoltativamente, specificare un `registration_id` per il dispositivo. In caso contrario, lasciare la riga impostata come commento per registrare il dispositivo con il nome CN del certificato di identità.

1. Facoltativamente, usare le `always_reprovision_on_startup` `dynamic_reprovisioning` linee o per configurare il comportamento del nuovo provisioning del dispositivo. Se un dispositivo è impostato per eseguire un nuovo provisioning all'avvio, tenterà sempre di eseguire il provisioning con DPS prima e quindi di eseguire il fallback al backup del provisioning in caso di errore. Se un dispositivo è impostato in modo da eseguire un nuovo provisioning dinamico, IoT Edge verrà riavviato ed eseguito un nuovo provisioning se viene rilevato un evento di reprovisioning. Per altre informazioni, vedere [concetti relativi al nuovo provisioning dei dispositivi dell'hub](../iot-dps/concepts-device-reprovision.md).

1. Salvare e chiudere il file config. yaml.

1. Riavviare il runtime IoT Edge in modo che accetti tutte le modifiche alla configurazione apportate nel dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Creare un file di configurazione per il dispositivo in base a un file di modello fornito come parte dell'installazione del IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Aprire il file di configurazione nel dispositivo IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Trovare la sezione del **provisioning** del file. Rimuovere il commento dalle righe per il provisioning di DPS con il certificato X. 509 e verificare che tutte le altre righe di provisioning siano impostate come commento.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   # registration_id = "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"

   identity_cert = "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Aggiornare i valori di `id_scope` , `identity_cert` e `identity_pk` con le informazioni sul dispositivo e sul DPS.

   Il valore del certificato di identità può essere fornito come URI di file oppure può essere emesso dinamicamente utilizzando l'autorità di certificazione EST o locale. Rimuovere il commento da una sola riga, in base al formato che si sceglie di usare.

   Il valore della chiave privata Identity può essere fornito come URI di file o PKCS # 11. Rimuovere il commento da una sola riga, in base al formato che si sceglie di usare.

   Se si usano gli URI PKCS # 11, trovare la sezione **PKCS # 11** nel file di configurazione e fornire informazioni sulla configurazione PKCS # 11.

1. Facoltativamente, specificare un `registration_id` per il dispositivo. In caso contrario, lasciare la riga impostata come commento per registrare il dispositivo con il nome comune del certificato di identità.

1. Salvare e chiudere il file.

1. Applicare le modifiche di configurazione apportate al IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Dispositivo Windows

1. Aprire una finestra di PowerShell in modalità amministratore. Assicurarsi di usare una sessione AMD64 di PowerShell durante l'installazione di IoT Edge, non di PowerShell (x86).

1. Il comando **Initialize-IoTEdge** configura il runtime IoT Edge nel computer. Per impostazione predefinita, il comando esegue il provisioning manuale con i contenitori di Windows, quindi usare il `-DpsX509` flag per usare il provisioning automatico con l'autenticazione del certificato X. 509.

   Sostituire i valori segnaposto per `{scope_id}` , `{identity cert chain path}` e `{identity key path}` con i valori appropriati dall'istanza di DPS e i percorsi dei file nel dispositivo.

   Aggiungere la `-RegistrationId {registration_id}` se si vuole impostare l'ID del dispositivo come un valore diverso dal nome CN del certificato di identità.

   Aggiungere il `-ContainerOs Linux` parametro se si usano contenitori Linux in Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Il file di configurazione archivia le informazioni relative al certificato e alla chiave come URI di file. Tuttavia, il comando Initialize-IoTEdge gestisce automaticamente questo passaggio di formattazione, quindi è possibile specificare il percorso assoluto dei file di certificato e di chiave nel dispositivo.

## <a name="verify-successful-installation"></a>Verificare l'esito positivo dell'installazione

Se il runtime è stato avviato correttamente, è possibile passare all'hub IoT e iniziare la distribuzione di moduli IoT Edge nel dispositivo.

È possibile verificare che sia stata usata la registrazione singola creata nel servizio Device provisioning. Passare all'istanza del servizio Device provisioning nel portale di Azure. Aprire i dettagli di registrazione per la registrazione singola creata. Si noti che lo stato della registrazione viene **assegnato** e viene elencato l'ID del dispositivo.

Usare i comandi seguenti sul dispositivo per verificare che il runtime sia stato installato e avviato correttamente.

### <a name="linux-device"></a>Dispositivo Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Verificare lo stato del servizio IoT Edge.

```cmd/sh
systemctl status iotedge
```

Esaminare i log del servizio.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Elencare i moduli in esecuzione.

```cmd/sh
iotedge list
```
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Verificare lo stato del servizio IoT Edge.

```cmd/sh
sudo iotedge system status
```

Esaminare i log del servizio.

```cmd/sh
sudo iotedge system logs
```

Elencare i moduli in esecuzione.

```cmd/sh
sudo iotedge list
```
:::moniker-end

### <a name="windows-device"></a>Dispositivo Windows

Verificare lo stato del servizio IoT Edge.

```powershell
Get-Service iotedge
```

Esaminare i log del servizio.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Elencare i moduli in esecuzione.

```powershell
iotedge list
```

## <a name="next-steps"></a>Passaggi successivi

Il processo di registrazione del servizio Device Provisioning consente di impostare l'ID dispositivo e i tag del dispositivo gemello mentre si effettua il provisioning del nuovo dispositivo. È possibile usare questi valori per identificare come destinazione singoli dispositivi o gruppi di dispositivi usando la gestione automatica dei dispositivi. Informazioni su come [distribuire e monitorare IOT Edge moduli su larga scala usando l'portale di Azure o l'interfaccia della](how-to-deploy-at-scale.md) riga di comando di [Azure](how-to-deploy-cli-at-scale.md).
