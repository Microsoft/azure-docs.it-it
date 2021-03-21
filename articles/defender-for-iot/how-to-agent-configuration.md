---
title: Configurare gli agenti di sicurezza
description: Informazioni su come configurare Defender per gli agenti di sicurezza Internet per l'uso con Defender per il servizio di sicurezza Internet.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: b59d9079b6d0fe0ab3fe61eacf43f3a99eb46686
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495777"
---
# <a name="tutorial-configure-security-agents"></a>Esercitazione: configurare gli agenti di sicurezza

Questo articolo illustra il Defender per gli agenti di sicurezza Internet e illustra come modificarli e configurarli.

> [!div class="checklist"]
> * Configurare gli agenti di sicurezza
> * Modificare il comportamento dell'agente modificando le proprietà gemelle
> * Individua configurazione predefinita

## <a name="agents"></a>Agenti

Defender per gli agenti di sicurezza Internet raccoglie i dati dai dispositivi Internet e esegue azioni di sicurezza per attenuare le vulnerabilità rilevate. La configurazione dell'agente di sicurezza è controllabile usando un set di proprietà dei moduli gemelli che è possibile personalizzare. In generale, gli aggiornamenti secondari a queste proprietà non sono frequenti.

L'oggetto di configurazione di Defender per l'agente di sicurezza dell'agente di sicurezza di Internet è un oggetto formato JSON. L'oggetto di configurazione è un set di proprietà controllabili che è possibile definire per controllare il comportamento dell'agente.

Queste configurazioni consentono di personalizzare l'agente per ogni scenario necessario. Se ad esempio si escludono automaticamente alcuni eventi o se il consumo di energia a un livello minimo è possibile, è possibile configurare queste proprietà.

Usare lo [schema](https://aka.ms/iot-security-github-module-schema) di configurazione di Defender per l'agente Security per apportare modifiche.

## <a name="configuration-objects"></a>Oggetti di configurazione

Le proprietà correlate a tutti i Defender per l'agente di sicurezza di Internet si trovano nell'oggetto di configurazione dell'agente, all'interno della sezione delle proprietà desiderate, del modulo **azureiotsecurity** .

Per modificare la configurazione, creare e modificare questo oggetto all'interno dell'identità **azureiotsecurity** del modulo gemello.

Se l'oggetto di configurazione dell'agente non è presente nel modulo gemello di **azureiotsecurity** , tutti i valori delle proprietà dell'agente di sicurezza sono impostati su predefinito.

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  }
}
```

## <a name="configuration-schema-and-validation"></a>Schema di configurazione e convalida

Assicurarsi di convalidare la configurazione dell'agente rispetto a questo [schema](https://aka.ms/iot-security-github-module-schema). Un agente non viene avviato se l'oggetto di configurazione non corrisponde allo schema.

Se, mentre l'agente è in esecuzione, l'oggetto di configurazione viene modificato in una configurazione non valida (la configurazione non corrisponde allo schema), l'agente ignorerà la configurazione non valida e continuerà a utilizzare la configurazione corrente.

### <a name="configuration-validation"></a>Convalida configurazione

Defender for Internet Security Agent segnala la configurazione corrente all'interno della sezione delle proprietà segnalate dell'identità del dispositivo gemello del modulo **azureiotsecurity** .
L'agente segnala tutte le proprietà disponibili, se una proprietà non è stata impostata dall'utente, l'agente segnala la configurazione predefinita.

Per convalidare la configurazione, confrontare i valori impostati nella sezione desiderata con i valori riportati nella sezione segnalata.

Se si verifica una mancata corrispondenza tra le proprietà desiderate e segnalate, l'agente non è stato in grado di analizzare la configurazione.

Convalidare le proprietà desiderate rispetto allo [schema](https://aka.ms/iot-security-github-module-schema), correggere gli errori e impostare di nuovo le proprietà desiderate.

> [!NOTE]
> Verrà generato un avviso di errore di configurazione dall'agente nel caso in cui l'agente non sia stato in grado di analizzare la configurazione desiderata.
> Confrontare la sezione segnalata e desiderata per determinare se l'avviso è ancora applicabile

## <a name="editing-a-property"></a>Modifica di una proprietà

Tutte le proprietà personalizzate devono essere impostate all'interno dell'oggetto di configurazione dell'agente all'interno del modulo gemello **azureiotsecurity** .
Per usare un valore di proprietà predefinito, rimuovere la proprietà dall'oggetto di configurazione.

### <a name="setting-a-property"></a>Impostazione di una proprietà

1. Nell'hub Internet delle cose individuare e selezionare il dispositivo che si vuole modificare.

1. Fare clic sul dispositivo e quindi sul modulo **azureiotsecurity** .

1. Fare clic su **Module Identity gemelle**.

1. Modificare le proprietà che si desidera modificare nella pagina Defender-Internet-micro-Agent.

   Ad esempio, per configurare gli eventi di connessione come priorità alta e raccogliere gli eventi con priorità alta ogni 7 minuti, utilizzare la configurazione seguente.

    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Fare clic su **Salva**.

### <a name="using-a-default-value"></a>Uso di un valore predefinito

Per usare un valore di proprietà predefinito, rimuovere la proprietà dall'oggetto di configurazione.

## <a name="default-properties"></a>Proprietà predefinite

La tabella seguente contiene le proprietà controllabili di Defender per gli agenti di sicurezza.

I valori predefiniti sono disponibili nello schema appropriato in [GitHub](https\://aka.ms/iot-security-module-default).

| Nome| Stato | Valori validi| Valori predefiniti| Descrizione |
|----------|--------|--|-------|----|
|highPriorityMessageFrequency|Obbligatorio: false |Valori validi: durata nel formato ISO 8601 |Valore predefinito: PT7M |Intervallo di tempo massimo prima dell'invio dei messaggi con priorità alta.|
|lowPriorityMessageFrequency |Obbligatorio: false|Valori validi: durata nel formato ISO 8601 |Valore predefinito: PT5H |Tempo massimo prima dell'invio dei messaggi con priorità bassa.|
|snapshotFrequency |Richiedi: false|Valori validi: durata nel formato ISO 8601 |Valore predefinito PT13H |Intervallo di tempo per la creazione di snapshot dello stato del dispositivo.|
|maxLocalCacheSizeInBytes |Obbligatorio: false |Valori validi: |Valore predefinito: 2560000, maggiore di 8192 | Archiviazione massima (in byte) consentita per la cache dei messaggi di un agente. Quantità massima di spazio consentito per l'archiviazione dei messaggi nel dispositivo prima dell'invio dei messaggi.|
|maxMessageSizeInBytes |Obbligatorio: false |Valori validi: un numero positivo maggiore di 8192, minore di 262144 |Valore predefinito: 204800 |Dimensione massima consentita di un agente per il messaggio cloud. Questa impostazione consente di controllare la quantità massima di dati inviati in ogni messaggio. |
|eventPriority $ {EventName} |Obbligatorio: false |Valori validi: High, low, off |Valori predefiniti: |Priorità di ogni evento generato dall'agente |

### <a name="supported-security-events"></a>Eventi di sicurezza supportati

|Nome evento| PropertyName | Valore predefinito| Evento snapshot| Stato dettagli  |
|----------|-|---------|----|----|
|Evento di diagnostica|eventPriorityDiagnostic| Off| Falso| Eventi di diagnostica correlati agli agenti. Utilizzare questo evento per la registrazione dettagliata.|
|Errore di configurazione |eventPriorityConfigurationError |Basso |Falso |L'agente non è riuscito ad analizzare la configurazione. Verificare la configurazione in base allo schema.|
|Statistiche eventi eliminati |eventPriorityDroppedEventsStatistics |Basso |Vero|Statistiche evento correlate agli agenti. |
|Hardware connesso|eventPriorityConnectedHardware |Basso |Vero |Snapshot di tutti i componenti hardware connessi al dispositivo.|
|Porte in ascolto|eventPriorityListeningPorts |Alto |Vero |Snapshot di tutte le porte di ascolto aperte sul dispositivo.|
|Creazione processo |eventPriorityProcessCreate |Basso |Falso |Controlla la creazione del processo nel dispositivo.|
|Terminazione processo|eventPriorityProcessTerminate |Basso |Falso |Controlla la terminazione del processo nel dispositivo.|
|Informazioni di sistema |eventPrioritySystemInformation |Basso |Vero |Uno snapshot delle informazioni di sistema (ad esempio, sistema operativo o CPU).|
|Utenti locali| eventPriorityLocalUsers |Alto |Vero|Snapshot degli utenti locali registrati nel sistema. |
|Accedi|  eventPriorityLogin |Alto|Falso|Controllare gli eventi di accesso al dispositivo (account di accesso locali e remoti).|
|Creazione della connessione |eventPriorityConnectionCreate|Basso|Falso|Controlla le connessioni TCP create da e verso il dispositivo. |
|Configurazione del firewall| eventPriorityFirewallConfiguration|Basso|Vero|Snapshot della configurazione del firewall del dispositivo (regole del firewall). |
|Baseline del sistema operativo| eventPriorityOSBaseline| Basso|Vero|Snapshot del controllo della linea di base del sistema operativo del dispositivo.|
|

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle raccomandazioni di Defender per l'it](concept-recommendations.md)
- [Esplora Defender per gli avvisi](concept-security-alerts.md)
- [Accedere a dati non elaborati sulla sicurezza](how-to-security-data-access.md)
