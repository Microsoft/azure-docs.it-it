---
title: Opzioni di monitoraggio - Modulo HSM dedicato di Azure | Microsoft Docs
description: Panoramica delle opzioni di monitoraggio e delle responsabilità di monitoraggio per il modulo HSM dedicato di Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 4c3d062d615208177fcb9c7df511f598613bf6a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97092704"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Monitoraggio di HSM dedicato di Azure

Il servizio HSM dedicato di Azure offre un dispositivo fisico ad uso esclusivo del cliente, con controllo amministrativo completo e piena responsabilità di gestione. Il dispositivo reso disponibile è un [modello di modulo di protezione hardware Thales Luna 7 A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms).  Dopo il provisioning al cliente, Microsoft non avrà alcun accesso amministrativo, oltre a una connessione alla porta seriale come ruolo di monitoraggio. Di conseguenza, i clienti sono responsabili delle attività operative comuni, tra cui il monitoraggio completo e l'analisi dei log.
I clienti sono completamente responsabili delle applicazioni che usano HSM e dovrebbero collaborare con Thales per assistenza o consulenza. Vista la portata della proprietà del cliente in termini di igiene operativa, Microsoft non può offrire alcun tipo di garanzia di disponibilità elevata per questo servizio. È responsabilità del cliente assicurarsi che le proprie applicazioni siano configurate correttamente per ottenere la disponibilità elevata. Microsoft monitorerà e gestirà l'integrità del dispositivo e la connettività di rete.

## <a name="microsoft-monitoring"></a>Monitoraggio Microsoft

Il dispositivo del modulo di protezione hardware di Thales Luna 7 in uso ha per impostazione predefinita il protocollo SNMP e la porta seriale come opzioni per il monitoraggio del dispositivo. Microsoft usa la connessione alla porta seriale come mezzo fisico per connettersi al dispositivo e recuperare i dati di telemetria di base sull'integrità. Questo include elementi come la temperatura e lo stato dei componenti, ad esempio alimentatori e ventole.
A tale scopo, Microsoft usa un ruolo di "monitoraggio" non amministrativo configurato nel dispositivo Thales. Questo ruolo offre la possibilità di recuperare i dati di telemetria, ma non concede alcun accesso al dispositivo in termini di attività amministrative o visualizzazione delle informazioni di crittografia. I clienti possono avere la certezza della piena proprietà del dispositivo da gestire, amministrare e usare per l'archiviazione delle chiavi crittografiche sensibili. Nel caso in cui un cliente non fosse soddisfatto di questo accesso minimo per il monitoraggio dell'integrità di base, ha la possibilità di disabilitare l'account di monitoraggio. L'ovvia conseguenza è che Microsoft non disporrà di alcuna informazione e di conseguenza non avrà la possibilità di fornire una notifica tempestiva di eventuali problemi di integrità del dispositivo. In questo caso, il cliente è responsabile dell'integrità del dispositivo.
La funzione di monitoraggio è configurata per eseguire il polling del dispositivo ogni 10 minuti per ottenere i dati di integrità. A causa della natura soggetta a errori della comunicazione seriale, solo dopo la ricezione di più indicatori di integrità negativi in un periodo di un'ora verrà generato un avviso. Questo avviso risulterebbe infine in una comunicazione proattiva al cliente per notificare il problema.
A seconda della natura del problema, sarebbe intrapresa la linea di azione appropriata per ridurre l'impatto e garantire la correzione a basso rischio. Ad esempio, un guasto all'alimentazione è una procedura di swapping a caldo senza eventi di manomissione risultanti, che può essere eseguita con un impatto ridotto e un rischio minimo per le operazioni. Altre procedure possono richiedere l'impostazione su zero e il deprovisioning di un dispositivo per ridurre al minimo i rischi di sicurezza per il cliente. In questo caso il cliente dovrebbe eseguire il provisioning di un dispositivo alternativo e stabilire di nuovo un'associazione a disponibilità elevata, attivando così la sincronizzazione dei dispositivi. In poco tempo, con un'interruzione minima e il minimo rischio per la sicurezza verrà ripristinato il funzionamento normale.  

## <a name="customer-monitoring"></a>Monitoraggio del cliente

Una proposta di valore del servizio HSM dedicato è il controllo che il cliente ottiene sul dispositivo, soprattutto considerando che si tratta di un dispositivo distribuito nel cloud. Una conseguenza di questo controllo è la responsabilità di monitorarlo e gestirne l'integrità. Il dispositivo del modulo di protezione hardware Thales Luna 7 viene fornita con linee guida per l'implementazione di SNMP e syslog. Ai clienti del servizio HSM dedicato si consiglia di usare questi strumenti anche quando l'account di monitoraggio Microsoft rimane attivo. L'uso va considerato obbligatorio se si disattiva l'account di monitoraggio Microsoft.
Entrambe le tecniche disponibili consentono al cliente di identificare i problemi e contattare il supporto tecnico Microsoft per avviare gli interventi correttivi appropriati.

## <a name="next-steps"></a>Passaggi successivi

Prima di eseguire il provisioning di dispositivi o di iniziare la progettazione o distribuzione di applicazioni, è consigliabile aver ben compreso tutti i concetti chiave relativi al servizio, ad esempio disponibilità elevata e sicurezza. Altri argomenti sui concetti:

* [Disponibilità elevata](high-availability.md)
* [Sicurezza fisica](physical-security.md)
* [Rete](networking.md)
* [Supporto](supportability.md)
