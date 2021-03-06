---
title: Raccomandazioni basate su agenti
description: Informazioni sul concetto di consigli per la sicurezza e su come vengono usati per i dispositivi Defender for Internet.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: a29957f459edad8b768daea7bc4567d77c80b165
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784986"
---
# <a name="security-recommendations-for-iot-devices"></a>Raccomandazioni sulla sicurezza per i dispositivi Internet

Defender for Internet esegue l'analisi delle risorse di Azure e dei dispositivi Internet e fornisce consigli sulla sicurezza per ridurre la superficie di attacco.
Le raccomandazioni sulla sicurezza sono praticabili e mirano a aiutare i clienti a rispettare le procedure consigliate per la sicurezza.

In questo articolo è disponibile un elenco di raccomandazioni che possono essere attivate sui dispositivi Internet delle cose.

## <a name="agent-based-recommendations"></a>Raccomandazioni basate su agenti

Le raccomandazioni per i dispositivi forniscono informazioni dettagliate e suggerimenti per migliorare il comportamento di sicurezza del dispositivo.

| Gravità | Nome | origine dati | Descrizione |
|--|--|--|--|
| Medio | Aprire le porte nel dispositivo | Defender classico-un micro-agente| Un endpoint di ascolto è stato trovato nel dispositivo. |
| Medio | Sono stati trovati criteri di firewall permissivi in una delle catene. | Defender classico-un micro-agente| Sono stati trovati criteri firewall consentiti (INPUT/OUTPUT). Il criterio firewall deve negare tutto il traffico per impostazione predefinita e definire le regole per consentire la comunicazione necessaria al/dal dispositivo. |
| Medio | È stata trovata una regola permissiva del firewall nella catena di input | Defender classico-un micro-agente| È stata rilevata una regola nel firewall che contiene un modello permissivo per un'ampia gamma di porte o indirizzi IP. |
| Medio | È stata trovata una regola permissiva del firewall nella catena di output | Defender classico-un micro-agente| È stata rilevata una regola nel firewall che contiene un modello permissivo per un'ampia gamma di porte o indirizzi IP. |
| Medio | Convalida della linea di base del sistema operativo non riuscita | Defender classico-un micro-agente| Il dispositivo non è conforme ai [benchmark Linux di CIS](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Raccomandazioni operative basate su agenti

Le indicazioni operative forniscono informazioni dettagliate e suggerimenti per migliorare la configurazione degli agenti di sicurezza.

| Gravità | Nome | origine dati | Descrizione |
|--|--|--|--|
| Basso | Agent invia messaggi non utilizzati | Defender classico-un micro-agente | il 10% o più messaggi di sicurezza sono inferiori a 4 KB durante le ultime 24 ore. |
| Basso | Configurazione del gemello di sicurezza non ottimale | Defender classico-un micro-agente | La configurazione di dispositivi gemelli di sicurezza non è ottimale. |
| Basso | Conflitto di configurazione della sicurezza dei dispositivi gemelli | Defender classico-un micro-agente | Sono stati identificati conflitti nella configurazione dei dispositivi gemelli di sicurezza. |

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica](overview.md) di Defender per il servizio Internet
- Informazioni su come [accedere ai dati di sicurezza](how-to-security-data-access.md)
- Altre informazioni sull' [analisi di un dispositivo](how-to-investigate-device.md)
