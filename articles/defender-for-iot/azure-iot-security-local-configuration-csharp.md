---
title: Configurazione locale dell'agente di sicurezza di Defender per Internet (C#)
description: Altre informazioni su Defender per il servizio di sicurezza Internet, il file di configurazione locale dell'agente di sicurezza per C#.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 14237894a38725ee07deca3b5f46cbe0ecfce4cd
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493221"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>Informazioni sul file di configurazione locale (agente C#)

Defender for Internet Security Agent usa le configurazioni di un file di configurazione locale.

L'agente di sicurezza legge il file di configurazione una volta all'avvio dell'esecuzione dell'agente. Le configurazioni disponibili nel file di configurazione locale contengono sia la configurazione di autenticazione che altre configurazioni correlate agli agenti.

L'agente di sicurezza C# usa più file di configurazione:

- Configurazioni correlate a **General.config** -Agent.
- **Authentication.config** la configurazione correlata all'autenticazione (inclusi i dettagli di autenticazione).
- Configurazioni correlate **SecurityIotInterface.config** -Internet.

I file di configurazione contengono la configurazione predefinita. La configurazione dell'autenticazione viene popolata durante l'installazione dell'agente e le modifiche apportate al file di configurazione vengono eseguite quando l'agente viene riavviato.

## <a name="configuration-file-location"></a>Percorso del file di configurazione

Per Linux:

- I file di configurazione del sistema operativo si trovano in `/var/ASCIoTAgent` .

Per Windows:

- I file di configurazione del sistema operativo si trovano all'interno della directory dell'agente sicurezza.

### <a name="generalconfig-configurations"></a>Configurazioni di General.config

| Nome della configurazione | Valori possibili | Dettagli |
|:-----------|:---------------|:--------|
| agentId | GUID | Identificatore univoco agente |
| readRemoteConfigurationTimeout | TimeSpan | Periodo di tempo per il recupero della configurazione remota dall'hub Internet. Se l'agente non è in grado di recuperare la configurazione entro il tempo specificato, l'operazione si timeout.|
| schedulerInterval | TimeSpan | Intervallo di pianificazione interno. |
| producerInterval | TimeSpan | Intervallo di lavoro Producer di eventi. |
| consumerInterval | TimeSpan | Intervallo di lavoro consumer evento. |
| highPriorityQueueSizePercentage | 0 < numero < 1 | Parte della cache totale dedicata per i messaggi con priorità alta. |
| logLevel | "Off", "Fatal", "Error", "Warning", "Information", "debug"  | I messaggi di log uguali e superiori a questa gravità vengono registrati nella console di debug (syslog in Linux). |
| fileLogLevel |  "Off", "Fatal", "Error", "Warning", "Information", "debug"| I messaggi di log uguali e superiori a questa gravità vengono registrati nel file (syslog in Linux). |
| diagnosticVerbosityLevel | "None", "some", "All", | Livello di dettaglio degli eventi di diagnostica. None: gli eventi di diagnostica non vengono inviati. Vengono inviati solo alcuni eventi di diagnostica con priorità alta. All-tutti i log vengono inviati anche come eventi di diagnostica. |
| logFilePath | Percorso del file | Se fileLogLevel > off, i log vengono scritti in questo file. |
| defaultEventPriority | "High", "low", "off" | Priorità evento predefinita. |

### <a name="generalconfig-example"></a>Esempio di General.config

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Nome configurazione | Valori possibili | Dettagli |
|:-----------|:---------------|:--------|
| moduleName | string | Nome dell'identità Defender-are-micro-Agent. Questo nome deve corrispondere al nome dell'identità del modulo nel dispositivo. |
| deviceId | string | ID del dispositivo, registrato nell'hub Azure. |
| schedulerInterval | Stringa TimeSpan | Intervallo di pianificazione interno. |
| gatewayHostname | string | Nome host dell'hub Azure. In genere <My-Hub>. azure-devices.net |
| filePath | stringa-percorso del file | Percorso del file che contiene il segreto di autenticazione.|
| tipo | "SelfSignedCertificate" | Il segreto utente per l'autenticazione. Scegliere dise il segreto utente è una chiave simmetrica, scegliere *certificato autofirmato* *se il* segreto è un certificato autofirmato. |
| identity | "DPS", "Module", "Device" | Identity di autenticazione: DPS se l'autenticazione viene eseguita tramite DPS, modulo se l'autenticazione viene eseguita usando le credenziali del modulo o il dispositivo se l'autenticazione viene eseguita usando le credenziali del dispositivo.
| certificateLocationKind |  "LocalFile", "Store" | LocalFile se il certificato è archiviato in un file, archiviare se il certificato si trova in un archivio certificati. |
| idScope | string | Ambito ID di DPS |
| registrationId | string  | ID registrazione dispositivo DPS. |
|

### <a name="authenticationconfig-example"></a>Esempio di Authentication.config

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Nome della configurazione | Valori possibili | Dettagli |
|:-----------|:---------------|:--------|
| transportType | "Ampq" "MQTT" | Tipo trasporto hub Internet. |
|

### <a name="securityiotinterfaceconfig-example"></a>Esempio di SecurityIotInterface.config

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Passaggi successivi

- Leggi la [Panoramica](overview.md) del servizio Defender for Internet
- Scopri di più su Defender per l' [architettura dell'it](architecture.md)
- Abilita il Defender per il [servizio](quickstart-onboard-iot-hub.md) Internet
- Leggi le [domande frequenti](resources-frequently-asked-questions.md) sul servizio Defender for Internet
- Informazioni su come accedere a [dati non elaborati sulla sicurezza](how-to-security-data-access.md)
- Informazioni sulle [raccomandazioni](concept-recommendations.md)
- Informazioni sugli [avvisi](concept-security-alerts.md) di sicurezza
