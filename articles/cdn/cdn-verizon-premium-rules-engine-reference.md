---
title: Riferimento del motore regole della rete CDN di Azure | Documentazione Microsoft
description: Documentazione di riferimento per le funzionalità e condizioni di corrispondenza del motore regole della rete CDN di Azure.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: aab93204c850223756f28a56ea550f912e28e0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69996764"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Guida di riferimento al motore regole di Azure CDN da Verizon PremiumAzure CDN from Verizon Premium rules engine reference

Questo articolo fornisce descrizioni dettagliate delle funzionalità e dalle condizioni di corrispondenza disponibili per il [motore regole](cdn-verizon-premium-rules-engine.md) della rete per la distribuzione di contenuti (rete CDN) di Azure.

Il motore regole è progettato per essere l'autorità finale sul modo in cui i tipi di richieste specifici vengono elaborati dalla rete CDN.

**Usi comuni**:

- Eseguire l'override o definire un criterio della cache personalizzato.
- Proteggere o negare le richieste di contenuti sensibili.
- Reindirizzare le richieste.
- Archiviare i dati di log personalizzati.

## <a name="terminology"></a>Terminologia

Una regola è definita con l'uso di [**espressioni condizionali**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**corrispondenze**](cdn-verizon-premium-rules-engine-reference-match-conditions.md) e [**funzionalità**](cdn-verizon-premium-rules-engine-reference-features.md). Questi elementi vengono evidenziati nella figura seguente:

 ![Condizione di corrispondenza della rete CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Sintassi

Il modo in cui verranno trattati i caratteri speciali varia a seconda del modo in cui una condizione di corrispondenza o funzionalità gestisce i valori di testo. Una condizione di corrispondenza o funzionalità potrebbe interpretare il testo in uno dei modi seguenti:

1. [**Valori letterali**](#literal-values)
2. [**Valori con caratteri jolly**](#wildcard-values)
3. [**Espressioni regolari**](#regular-expressions)

### <a name="literal-values"></a>Valori letterali

Il testo che viene interpretato come valore letterale tratta tutti i caratteri speciali, fatta eccezione per il simbolo %, come parte del valore di cui trovare la corrispondenza. In altre parole, una condizione di corrispondenza letterale impostata su `\'*'\` viene soddisfatta solo se viene trovato tale valore (ad esempio, `\'*'\`).

Per indicare la codifica URL viene usato un simbolo di percentuale (ad esempio, `%20`).

### <a name="wildcard-values"></a>Valori caratteri jolly

Il testo che viene interpretato come un valore carattere jolly assegna un significato aggiuntivo ai caratteri speciali. La tabella di seguito descrive come viene interpretato il set di caratteri seguente:

Carattere | Descrizione
----------|------------
\ | Una barra rovesciata viene utilizzata per eseguire l'escape di ogni carattere specificato in questa tabella. Una barra rovesciata deve essere specificata direttamente prima del carattere speciale che deve essere sottoposto a escape.<br/>La sintassi seguente, ad esempio, ignora un asterisco: `\*`
% | Per indicare la codifica URL viene usato un simbolo di percentuale (ad esempio, `%20`).
\* | L'asterisco è un carattere jolly che rappresenta uno o più caratteri.
Space | Un carattere di spazio indica che una condizione di corrispondenza può essere soddisfatta da uno dei valori o modelli specificati.
"value" | Una virgoletta singola non ha un significato speciale. Tuttavia, un set di virgolette singole viene utilizzato per indicare che un valore deve essere considerato come valore letterale. Può essere usato nei modi seguenti:<br><br/>- Consente una condizione di corrispondenza da soddisfare ogni volta che il valore specificato corrisponde a una parte qualsiasi del valore di confronto.  Ad esempio, `'ma'` corrisponde a una qualsiasi delle seguenti stringhe: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- Consente a un carattere speciale di essere specificato come carattere letterale. Ad esempio, è possibile specificare un carattere spazio letterale racchiudendo uno spazio all'interno di un set di virgolette singole (ad esempio, `' '` o `'sample value'`).<br/>- Consente di specificare un valore vuoto. Specificare un valore vuoto indicando un set di virgolette singole (ovvero '').<br /><br/>**Importante:**<br/>-Se il valore specificato non contiene un carattere jolly, viene automaticamente considerato un valore letterale. Questo significa che non è necessario specificare un set di virgolette singole.<br/>- Se una barra rovesciata non effettua l'escape di un altro carattere in questa tabella, questo viene ignorato se specificato all'interno di un set di virgolette singole.<br/>- Un altro modo per specificare un carattere speciale come un carattere letterale è quello di effettuare l'escape con una barra rovesciata (ovvero `\`).

### <a name="regular-expressions"></a>Espressioni regolari

Le espressioni regolari definiscono un modello che viene cercato all'interno di un valore di testo. La notazione delle espressioni regolari definisce un significato specifico per una varietà di simboli. La tabella seguente indica come vengono trattati i caratteri speciali dalle funzionalità e dalle condizioni di corrispondenza che supportano le espressioni regolari.

Carattere speciale | Descrizione
------------------|------------
\ | Una barra rovesciata esegue l'escape del carattere che la segue. In questo modo il carattere viene trattato come un valore letterale anziché assumere il significato di espressione regolare. La sintassi seguente, ad esempio, ignora un asterisco: `\*`
% | Il significato di un simbolo di percentuale dipende dal relativo utilizzo.<br/><br/> `%{HTTPVariable}`: questa sintassi identifica una variabile HTTP.<br/>`%{HTTPVariable%Pattern}`: questa sintassi usa un simbolo di percentuale per identificare una variabile HTTP e come delimitatore.<br />`\%`: l'escape di un simbolo di percentuale consente di usare tale simbolo come valore letterale o per indicare la codifica URL (ad esempio, `\%20`).
\* | Un asterisco consente al carattere precedente di essere messo in corrispondenza zero o più volte.
Space | Un carattere di spazio in genere è considerato come un carattere letterale.
"value" | Le virgolette singole vengono trattate come caratteri letterali. Un set di virgolette singole non ha un significato speciale.

Le condizioni di corrispondenza e le funzionalità che supportano le espressioni regolari accettano modelli definiti da espressioni regolari compatibili con Perl (PCRE).

## <a name="next-steps"></a>Passaggi successivi

- [Condizioni di corrispondenza del motore regole](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Espressioni condizionali del motore regole](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funzionalità del motore regole](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override del comportamento HTTP predefinito mediante il motore regole](cdn-verizon-premium-rules-engine.md)
- [Panoramica della rete CDN di Azure](cdn-overview.md)
