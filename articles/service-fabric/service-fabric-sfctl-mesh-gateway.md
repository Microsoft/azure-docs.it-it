---
title: INTERFACCIA della riga di comando di Azure Service Fabric-gateway mesh sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per il recupero e l'eliminazione di Service Fabric risorse del gateway mesh.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9b6766137dd88a5a780dcca7b6eab7c6c3f9bbf4
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260397"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Consente di ottenere ed eliminare le risorse gateway mesh.

## <a name="commands"></a>Comandi

|Comando|Descrizione|
| --- | --- |
| eliminazione | Elimina la risorsa del gateway. |
| list | Elenca tutte le risorse del gateway. |
| show | Ottiene la risorsa del gateway con il nome specificato. |

## <a name="sfctl-mesh-gateway-delete"></a>sfctl mesh gateway delete
Elimina la risorsa del gateway.

Elimina la risorsa del gateway identificata dal nome.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name -n [Obbligatorio] | Nome della risorsa del gateway. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-gateway-list"></a>sfctl mesh gateway list
Elenca tutte le risorse del gateway.

Ottiene le informazioni su tutte le risorse del gateway in un determinato gruppo di risorse. Le informazioni includono la descrizione e altre proprietà del gateway.

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-gateway-show"></a>sfctl mesh gateway show
Ottiene la risorsa del gateway con il nome specificato.

Ottiene le informazioni sulla risorsa del gateway con il nome specificato. Le informazioni includono la descrizione e altre proprietà del gateway.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name -n [Obbligatorio] | Nome della risorsa del gateway. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](./scripts/sfctl-upgrade-application.md).
