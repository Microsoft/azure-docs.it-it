---
title: Azure Service Fabric CLI-telemetria delle impostazioni sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per la configurazione della telemetria di sfctl.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76903022"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Configura le impostazioni di telemetria locali per questa istanza di sfctl.

La telemetria sfctl raccoglie il nome del comando senza i parametri forniti o i relativi valori, la versione di sfctl, il tipo di sistema operativo, la versione di python, l'esito positivo o negativo del comando, il messaggio di errore restituito.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| set-telemetry | Attiva o disattiva la telemetria. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
Attiva o disattiva la telemetria.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --off | Disattiva la telemetria. |
| --on | Attiva la telemetria. Questo è il valore predefinito. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

### <a name="examples"></a>Esempi

Disattiva la telemetria.

```
sfctl settings telemetry set_telemetry --off
```

Attiva la telemetria.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).