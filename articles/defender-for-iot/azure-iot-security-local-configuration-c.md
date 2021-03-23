---
title: Configurazione locale dell'agente di sicurezza (C)
description: Informazioni su Defender per le configurazioni locali dell'agente per C.
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: dc4ea30080711633e52f966b89a16f7246a30673
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779410"
---
# <a name="understanding-the-localconfigurationjson-file---c-agent"></a>Informazioni sul file LocalConfiguration.json - Agente C

Defender for Internet Security Agent usa le configurazioni di un file di configurazione locale.
L'agente di sicurezza legge la configurazione una volta, all'avvio dell'agente.
La configurazione trovata nel file di configurazione locale contiene la configurazione dell'autenticazione e altre configurazioni correlate agli agenti.
Il file contiene le configurazioni nelle coppie chiave-valore nella notazione JSON e le configurazioni vengono popolate quando l'agente è installato.

Per impostazione predefinita, il file si trova in:/var/ASCIoTAgent/LocalConfiguration.json

Le modifiche apportate al file di configurazione vengono applicate quando l'agente viene riavviato.

## <a name="security-agent-configurations-for-c"></a>Configurazioni degli agenti di sicurezza per C

| Nome della configurazione | Valori possibili | Dettagli |
|:-----------|:---------------|:--------|
| AgentId | GUID | Identificatore univoco dell'agente |
| TriggerdEventsInterval | Stringa ISO8601 | Intervallo di utilità di pianificazione per la raccolta di eventi attivati |
| ConnectionTimeout | Stringa ISO8601 | Si è verificato il timeout del periodo di tempo prima della connessione a IoThub |
| Authentication | JsonObject | Configurazione dell'autenticazione. Questo oggetto contiene tutte le informazioni necessarie per l'autenticazione in IoTHub |
| Identità | "DPS", "SecurityModule", "Device" | Identità di autenticazione: DPS se l'autenticazione viene eseguita tramite DPS, SecurityModule se l'autenticazione viene effettuata tramite Defender-Internet o dispositivo se l'autenticazione viene eseguita con le credenziali del dispositivo |
| AuthenticationMethod | "SasToken", "SelfSignedCertificate" | il segreto utente per l'autenticazione-scegliere SasToken se il segreto di utilizzo è una chiave simmetrica, scegliere un certificato autofirmato se il segreto è un certificato autofirmato  |
| FilePath | Percorso del file (stringa) | Percorso del file che contiene il segreto di autenticazione |
| HostName | string | Nome host dell'hub Azure. in genere <My-Hub>. azure-devices.net |
| DeviceId | string | ID del dispositivo, come registrato nell'hub Azure. |
| DPS | JsonObject | Configurazioni correlate a DPS |
| IDScope | string | Ambito ID di DPS |
| ID registrazione | string  | ID registrazione dispositivo DPS |
| Registrazione | JsonObject | Configurazioni correlate al logger agente |
| SystemLoggerMinimumSeverity | 0 <= numero <= 4 | i messaggi di log uguali e superiori a questa gravità verranno registrati in/var/log/syslog (0 è la gravità minima) |
| DiagnosticEventMinimumSeverity | 0 <= numero <= 4 | i messaggi di log uguali e superiori a questa gravità verranno inviati come eventi di diagnostica (0 è la gravità minima) |

## <a name="security-agent-configurations-code-example"></a>Esempio di codice delle configurazioni degli agenti di sicurezza

```json
{
    "Configuration" : {
        "AgentId" : "b97faf0a-0f57-471f-9dab-46a8e1764946",
        "TriggerdEventsInterval" : "PT2M",
        "ConnectionTimeout" : "PT30S",
        "Authentication" : {
            "Identity" : "Device",
            "AuthenticationMethod" : "SasToken",
            "FilePath" : "/path/to/my/SymmetricKey",
            "DeviceId" : "my-device",
            "HostName" : "my-iothub.azure-devices.net",
            "DPS" : {
                "IDScope" : "",
                "RegistrationId" : ""
            }
        },
        "Logging": {
            "SystemLoggerMinimumSeverity": 0,
            "DiagnoticEventMinimumSeverity": 2
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Leggi la [Panoramica](overview.md) del servizio Defender for Internet
- Scopri di più su Defender per l' [architettura dell'it](architecture.md)
- Abilita il Defender per il [servizio](quickstart-onboard-iot-hub.md) Internet
- Leggi le [domande frequenti](resources-frequently-asked-questions.md) sul servizio Defender for Internet
- Informazioni su come accedere a [dati non elaborati sulla sicurezza](how-to-security-data-access.md)
- Informazioni sulle [raccomandazioni](concept-recommendations.md)
- Informazioni sugli [avvisi](concept-security-alerts.md) di sicurezza
