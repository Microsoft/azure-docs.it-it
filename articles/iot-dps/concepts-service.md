---
title: Concetti relativi al servizio Azure Device Provisioning in hub IoT | Microsoft Docs
description: Descrive i concetti relativi al provisioning del servizio specifici dei dispositivi con il servizio Device provisioning (DPS) e l'hub Internet
author: nberdy
ms.author: nberdy
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: f42502ac4db12a060af5906243d3f8e7584c5df3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79285214"
---
# <a name="iot-hub-device-provisioning-service-concepts"></a>Concetti relativi al servizio Device Provisioning in hub IoT

Il servizio Device Provisioning in hub IoT è un servizio helper per l'hub IoT che consente di configurare il provisioning completamente automatico dei dispositivi in un hub IoT specifico. Con il servizio Device Provisioning è possibile effettuare il [provisioning automatico](concepts-auto-provisioning.md) di milioni di dispositivi in modo sicuro e scalabile.

Il provisioning dei dispositivi è un processo in due parti. La prima parte consiste nello stabilire la connessione iniziale tra il dispositivo e la soluzione Internet delle cose mediante la *registrazione* del dispositivo. La seconda parte consiste nell'applicare la *configurazione* corretta al dispositivo in base ai requisiti specifici della soluzione. Una volta completati entrambi questi passaggi, il *provisioning* del dispositivo può considerarsi completato. Il servizio Device Provisioning consente di automatizzare entrambi i passaggi per semplificare l'esperienza di provisioning dei dispositivi.

Questo articolo fornisce una panoramica dei concetti relativi al provisioning in relazione alla gestione del *servizio*. L'articolo è di particolare interesse per le persone coinvolte nella [fase di impostazione cloud](about-iot-dps.md#cloud-setup-step) in preparazione alla distribuzione di un dispositivo.

## <a name="service-operations-endpoint"></a>Endpoint delle operazioni del servizio

L'endpoint delle operazioni del servizio è l'endpoint per la gestione delle impostazioni del servizio e la manutenzione dell'elenco di registrazione. Questo endpoint viene usato solo dall'amministratore del servizio e non dai dispositivi.

## <a name="device-provisioning-endpoint"></a>Endpoint di provisioning dei dispositivi

L'endpoint di provisioning dei dispositivi è il singolo endpoint che tutti i dispositivi usano per il provisioning automatico. L'URL è lo stesso per tutte le istanze del servizio di provisioning, per eliminare la necessità di sovrascrivere la memoria dei dispositivi con nuove informazioni di connessione in scenari di supply chain. L'ambito ID garantisce l'isolamento dei tenant.

## <a name="linked-iot-hubs"></a>Hub IoT collegati

Il servizio Device Provisioning può effettuare il provisioning dei dispositivi solo negli hub IoT che sono stati collegati. Il collegamento di un hub IoT a un'istanza del servizio Device Provisioning fornisce al servizio le autorizzazioni di lettura/scrittura nel registro dei dispositivi dell'hub IoT. Con il collegamento, il servizio Device Provisioning può registrare un ID dispositivo e impostare la configurazione iniziale nel dispositivo gemello. Gli hub IoT collegati possono trovarsi in qualsiasi area di Azure. È possibile collegare hub di altre sottoscrizioni al servizio di provisioning.

## <a name="allocation-policy"></a>Criteri di allocazione

Impostazione a livello di servizio che determina in che modo il servizio Device Provisioning assegna i dispositivi a un hub IoT. Sono supportati tre criteri di allocazione:

* **Distribuzione ponderata uniforme**: gli hub IoT collegati hanno le stesse probabilità di essere scelti per il provisioning dei dispositivi. Si tratta dell'impostazione predefinita. Se si esegue il provisioning dei dispositivi in un solo hub IoT, è possibile mantenere questa impostazione.

* **Latenza minima**: il provisioning dei dispositivi viene effettuato in un hub IoT con latenza minima per il dispositivo. Se più hub IoT collegati forniscono la stessa latenza minima, il servizio di provisioning suddivide i dispositivi tra gli hub

* **Configurazione statica tramite l'elenco di registrazione**: la specifica dell'hub IoT desiderato nell'elenco di registrazione ha la priorità rispetto ai criteri di allocazione a livello di servizio.

## <a name="enrollment"></a>Registrazione

Una registrazione è un record di dispositivi o gruppi di dispositivi che possono registrarsi tramite il provisioning automatico. Il record di registrazione contiene informazioni sul dispositivo o sul gruppo di dispositivi, tra cui:
- il [meccanismo di attestazione](concepts-security.md#attestation-mechanism) usato dal dispositivo
- la configurazione iniziale facoltativa desiderata
- l'hub IoT desiderato
- l'ID dispositivo desiderato

Il servizio Device Provisioning supporta due tipi di registrazione:

### <a name="enrollment-group"></a>Gruppo di registrazione

Un gruppo di registrazione è un gruppo di dispositivi che condividono un meccanismo di attestazione specifico. I gruppi di registrazione supportano sia X. 509 sia simmetrico. Tutti i dispositivi del gruppo di registrazione X. 509 presentano certificati X. 509 che sono stati firmati dalla stessa autorità di certificazione radice o intermedia (CA). Ogni dispositivo nel gruppo di registrazione di chiavi simmetriche presenta token SAS derivati dalla chiave simmetrica del gruppo. Il nome del gruppo di registrazione e il nome di certificato devono essere alfanumerici, in caratteri minuscoli e possono contenere segni meno.

> [!TIP]
> È consigliabile usare un gruppo di registrazione per un numero elevato di dispositivi che condividono una configurazione iniziale desiderata o per i dispositivi destinati allo stesso tenant.

### <a name="individual-enrollment"></a>Registrazione individuale

Una registrazione individuale è una voce per un singolo dispositivo che esegue la registrazione. Le registrazioni individuali possono usare certificati foglia X.509 o token di firma di accesso condiviso (in un TPM fisico o virtuale) come meccanismo di attestazione. L'ID di registrazione in una singola registrazione è alfanumerico, con caratteri minuscoli e può contenere segni meno. In caso di registrazione individuale è possibile specificare l'ID dispositivo hub IoT desiderato.

> [!TIP]
> È consigliabile usare le registrazioni individuali per i dispositivi che richiedono configurazioni iniziali univoche oppure per i dispositivi che possono autenticare solo usando token di firma di accesso condiviso tramite l'attestazione TPM.

## <a name="registration"></a>Registrazione

Una registrazione è il record di un dispositivo che esegue correttamente la registrazione/il provisioning in un hub IoT tramite il servizio Device Provisioning. I record di registrazione vengono creati automaticamente. Possono essere eliminati, ma non aggiornati.

## <a name="operations"></a>Operazioni

Le operazioni rappresentano l'unità di fatturazione del servizio Device Provisioning. Un'operazione si riferisce al corretto completamento di un'istruzione nel servizio. Le operazioni includono le registrazioni e le ripetizioni delle registrazioni dei dispositivi, oltre alle modifiche sul lato del servizio, come l'aggiunta di voci all'elenco di registrazione e l'aggiornamento delle voci dell'elenco di registrazione.
