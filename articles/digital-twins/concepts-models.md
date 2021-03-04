---
title: Modelli DTDL
titleSuffix: Azure Digital Twins
description: Informazioni sul modo in cui i dispositivi gemelli digitali di Azure usano modelli personalizzati per descrivere le entità nell'ambiente.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d33634c08b85ad633fdad3f709b2b2182571d7de
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034762"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Informazioni sui modelli gemelli in Gemelli digitali di Azure

Una caratteristica chiave dei dispositivi gemelli digitali di Azure è la possibilità di definire un vocabolario personalizzato e di creare un grafico a gemelli in termini autonomi dell'azienda. Questa funzionalità viene fornita tramite **modelli** forniti dall'utente. È possibile considerare i modelli come Sostantivi in una descrizione del mondo. 

Un modello è simile a una **classe** in un linguaggio di programmazione orientato a oggetti, definendo una forma dati per un particolare concetto nell'ambiente di lavoro reale. I modelli hanno nomi, ad esempio *room* o *sensore*, e contengono elementi quali proprietà, telemetria/eventi e comandi che descrivono il tipo di entità nell'ambiente. Successivamente, questi modelli vengono usati per creare i dispositivi [**gemelli digitali**](concepts-twins-graph.md) che rappresentano entità specifiche che soddisfano questa descrizione del tipo.

I modelli di dispositivi gemelli digitali di Azure sono rappresentati nel **DTDL (Digital Twin Definition Language)** basato su JSON-LD.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Digital Twin Definition Language (DTDL) per i modelli

I modelli per Gemelli digitali di Azure vengono definiti tramite il linguaggio DTDL (Digital Twins Definition Language). DTDL è basato su JSON-LD ed è indipendente dal linguaggio di programmazione. DTDL non è esclusivo per i dispositivi gemelli digitali di Azure, ma viene usato anche per rappresentare i dati dei dispositivi in altri servizi Internet, ad esempio [plug and Play](../iot-pnp/overview-iot-plug-and-play.md). 

I dispositivi gemelli digitali di Azure usano **DTDL _versione 2_**. Per ulteriori informazioni su questa versione di DTDL, vedere la relativa documentazione specifica in GitHub: [*Digital Gemini Definition Language (DTDL)-versione 2*](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). L'uso di DTDL _versione 1_ con i dispositivi gemelli digitali di Azure è ora deprecato.

> [!NOTE] 
> Non tutti i servizi che usano DTDL implementano esattamente le stesse funzionalità di DTDL. Ad esempio, il Plug and Play Internet delle cose non usa le funzionalità di DTDL per i grafici, mentre i dispositivi gemelli digitali di Azure attualmente non implementano i comandi di DTDL.
>
> Per altre informazioni sulle funzionalità di DTDL specifiche per i dispositivi gemelli digitali di Azure, vedere la sezione più avanti in questo articolo sulle specifiche di implementazione di DTDL per i dispositivi [digitali gemelli di Azure](#azure-digital-twins-dtdl-implementation-specifics).

## <a name="elements-of-a-model"></a>Elementi di un modello

All'interno di una definizione del modello, l'elemento di codice di primo livello è un' **interfaccia**. che incapsula l'intero modello, mentre il resto del modello viene definito all'interno dell'interfaccia. 

Un'interfaccia del modello DTDL può contenere zero, uno o molti dei seguenti campi:
* **Proprietà** -le proprietà sono campi dati che rappresentano lo stato di un'entità (ad esempio le proprietà in molti linguaggi di programmazione orientati a oggetti). Le proprietà dispongono di archiviazione di backup e possono essere lette in qualsiasi momento.
* I campi di **telemetria: i** campi di telemetria rappresentano misurazioni o eventi e vengono spesso usati per descrivere le letture dei sensori del dispositivo. Diversamente dalle proprietà, i dati di telemetria non vengono archiviati in un dispositivo gemello digitale. si tratta di una serie di eventi di dati associati al tempo che devono essere gestiti non appena si verificano. Per altre informazioni sulle differenze tra la proprietà e la telemetria, vedere la sezione [*proprietà e telemetria*](#properties-vs-telemetry) riportata di seguito.
* **Component** -Components consente di compilare l'interfaccia del modello come assembly di altre interfacce, se lo si desidera. Un esempio di componente è costituito da un'interfaccia *frontCamera* (e da un'altra *backcamera* di interfaccia componente) utilizzata per la definizione di un modello per un *telefono*. È necessario innanzitutto definire un'interfaccia per *frontCamera* come se fosse un modello proprio, quindi è possibile farvi riferimento durante la definizione del *telefono*.

    Usare un componente per descrivere una parte integrante della soluzione, ma non è necessaria un'identità separata e non è necessario crearla, eliminarla o riordinarla in modo indipendente. Se si vuole che le entità dispongano di esistenza indipendente nel grafo gemello, rappresentarle come gemelli digitali distinti di modelli diversi, connessi da *relazioni* (vedere il punto successivo).
    
    >[!TIP] 
    >I componenti possono inoltre essere utilizzati per l'organizzazione per raggruppare set di proprietà correlate all'interno di un'interfaccia del modello. In questa situazione è possibile considerare ogni componente come uno spazio dei nomi o una "cartella" all'interno dell'interfaccia.
* **Relazioni: le** relazioni consentono di rappresentare il modo in cui un dispositivo gemello digitale può essere associato ad altri dispositivi gemelli digitali. Le relazioni possono rappresentare significati semantici diversi, ad esempio *Contains* ("Floor Contains room"), *Cools* ("HVAC Cools room"), *isBilledTo* ("commediator is fatturated to User") e così via. Le relazioni consentono alla soluzione di fornire un grafico delle entità correlate.

> [!NOTE]
> La [specifica per DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) definisce anche i **comandi**, che sono metodi che possono essere eseguiti su un dispositivo gemello digitale, ad esempio un comando reset, o un comando per attivare o disattivare un ventilatore. Tuttavia, *i comandi non sono attualmente supportati nei dispositivi gemelli digitali di Azure.*

### <a name="properties-vs-telemetry"></a>Confronto tra proprietà e telemetria

Di seguito sono riportate alcune informazioni aggiuntive sulla distinzione tra i campi della **Proprietà** DTDL e di **telemetria** nei dispositivi gemelli digitali di Azure.

La differenza tra proprietà e telemetria per i modelli di dispositivi gemelli digitali di Azure è la seguente:
* Si prevede che le **Proprietà** dispongano di archiviazione di backup. Ciò significa che è possibile leggere una proprietà in qualsiasi momento e recuperare il relativo valore. Se la proprietà è scrivibile, è anche possibile archiviare un valore nella proprietà.  
* La **telemetria** è più simile a un flusso di eventi; si tratta di un set di messaggi di dati che hanno una durata breve. Se non si configura l'ascolto dell'evento e le azioni da intraprendere quando si verifica, non è presente alcuna traccia dell'evento in un secondo momento. Non è possibile tornare a questa pagina e leggerla in un secondo momento. 
  - Nei termini di C#, la telemetria è come un evento C#. 
  - In termini di utilizzo generale, la telemetria è in genere una singola misurazione inviata da un dispositivo.

I dati di **telemetria** vengono spesso usati con i dispositivi Internet, perché molti dispositivi non sono idonei o interessati a archiviare i valori di misurazione che generano. Li inviano semplicemente come flusso di eventi di telemetria. In questo caso, non è possibile chiedere al dispositivo in qualsiasi momento l'ultimo valore del campo di telemetria. Al contrario, sarà necessario ascoltare i messaggi dal dispositivo e intraprendere le azioni all'arrivo dei messaggi. 

Di conseguenza, quando si progetta un modello in Azure Digital Twins, probabilmente si useranno le **Proprietà** nella maggior parte dei casi per modellare i dispositivi gemelli. In questo modo è possibile avere l'archiviazione di backup e la possibilità di leggere ed eseguire query sui campi dati.

I dati di telemetria e le proprietà spesso interagiscono per gestire i dati in ingresso dai dispositivi. Poiché tutti i dati in ingresso nei dispositivi gemelli digitali di Azure sono tramite [API](how-to-use-apis-sdks.md), in genere si userà la funzione di ingresso per leggere i dati di telemetria o gli eventi delle proprietà dai dispositivi e impostare una proprietà nei dispositivi gemelli digitali di Azure in risposta. 

È anche possibile pubblicare un evento di telemetria dall'API dei dispositivi gemelli digitali di Azure. Come per gli altri dati di telemetria, si tratta di un evento di breve durata che richiede un listener per la gestione.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Specifiche di implementazione DTDL di dispositivi digitali gemelli di Azure

Per garantire la compatibilità di un modello DTDL con i dispositivi gemelli digitali di Azure, è necessario che soddisfi questi requisiti.

* Tutti gli elementi DTDL di primo livello in un modello devono essere di tipo *Interface*. Questo perché le API del modello di dispositivi gemelli digitali di Azure possono ricevere oggetti JSON che rappresentano un'interfaccia o una matrice di interfacce. Di conseguenza, non sono consentiti altri tipi di elementi DTDL al livello principale.
* DTDL per i dispositivi gemelli digitali di Azure non deve definire alcun *comando*.
* I dispositivi gemelli digitali di Azure consentono solo un singolo livello di annidamento dei componenti. Ciò significa che un'interfaccia usata come componente non può avere alcun componente. 
* Le interfacce non possono essere definite inline all'interno di altre interfacce DTDL. devono essere definite come entità di primo livello separate con i rispettivi ID. Quindi, quando un'altra interfaccia vuole includere tale interfaccia come componente o tramite ereditarietà, può fare riferimento al relativo ID.

I dispositivi gemelli digitali di Azure non osservano inoltre l' `writable` attributo per le proprietà o le relazioni. Sebbene possa essere impostata in base alle specifiche DTDL, il valore non viene usato dai dispositivi gemelli digitali di Azure. Al contrario, questi vengono sempre trattati come scrivibili da client esterni che dispongono di autorizzazioni di scrittura generali per il servizio Azure Digital Twins.

## <a name="example-model-code"></a>Esempio di codice del modello

I modelli di tipo gemello possono essere scritti in qualsiasi editor di testo. Il linguaggio DTDL segue la sintassi JSON, quindi è necessario archiviare i modelli con estensione *JSON*. L'uso dell'estensione JSON consentirà a molti editor di testo di programmazione di fornire il controllo della sintassi di base ed evidenziare i documenti DTDL. Per [Visual Studio Code](https://code.visualstudio.com/)è disponibile anche un' [estensione DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) .

Questa sezione contiene un esempio di un modello tipico, scritto come interfaccia DTDL. Il modello descrive i **pianeti**, ognuno con un nome, una massa e una temperatura.
 
Tenere presente che i pianeti possono interagire anche con le **lune** che sono i loro satelliti e possono contenere **crateri**. Nell'esempio seguente il `Planet` modello esprime le connessioni a queste altre entità facendo riferimento a due modelli esterni, `Moon` e `Crater` . Questi modelli sono definiti anche nel codice di esempio riportato di seguito, ma sono conservati molto semplici, in modo da evitare di detrarre dall' `Planet` esempio principale.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

I campi del modello sono:

| Campo | Descrizione |
| --- | --- |
| `@id` | Identificatore per il modello. Deve essere nel formato `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Identifica il tipo di informazioni da descrivere. Per un'interfaccia, il tipo è *Interface*. |
| `@context` | Imposta il [contesto](https://niem.github.io/json/reference/json-ld/context/) per il documento JSON. I modelli devono usare `dtmi:dtdl:context;2` . |
| `displayName` | opzionale Consente di assegnare al modello un nome descrittivo, se lo si desidera. |
| `contents` | Tutti i dati dell'interfaccia rimanenti vengono inseriti qui come una matrice di definizioni di attributo. Ogni attributo deve fornire un `@type` (*Proprietà*, *telemetria*, *comando*, *relazione* o *componente*) per identificare l'ordinamento delle informazioni di interfaccia descritte, quindi un set di proprietà che definiscono l'attributo effettivo (ad esempio, `name` e `schema` per definire una *Proprietà*). |

> [!NOTE]
> Si noti che l'interfaccia componente (*cratere* in questo esempio) è definita nella stessa matrice dell'interfaccia che la utilizza (*Planet*). I componenti devono essere definiti in questo modo nelle chiamate API per poter trovare l'interfaccia.

### <a name="possible-schemas"></a>Schemi possibili

Come per DTDL, lo schema per gli attributi di *Proprietà* e *telemetria* può essere di tipi primitivi standard `integer` , ovvero,, `double` `string` e `Boolean` altri tipi quali `DateTime` e `Duration` . 

Oltre ai tipi primitivi, i campi di *Proprietà* e *telemetria* possono avere questi tipi complessi:
* `Object`
* `Map`
* `Enum`

I campi di *telemetria* supportano anche `Array` .

### <a name="model-inheritance"></a>Ereditarietà del modello

In alcuni casi può essere necessario specializzare ulteriormente un modello. Ad esempio, potrebbe essere utile disporre di una *stanza* del modello generica e di varianti specializzate *conferenceroom* e *Gym*. Per esprimere la specializzazione, DTDL supporta l'ereditarietà: le interfacce possono ereditare da una o più interfacce. 

Nell'esempio seguente viene nuovamente immaginato il modello *Planet* dell'esempio precedente DTDL come sottotipo di un modello *CelestialBody* più grande. Il modello "Parent" viene definito per primo e quindi il modello "Child" si basa su di esso tramite il campo `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

In questo esempio, *CelestialBody* contribuisce con un nome, una massa e una temperatura al *pianeta*. La `extends` sezione è un nome di interfaccia o una matrice di nomi di interfaccia (che consente all'interfaccia di estensione di ereditare da più modelli padre, se lo si desidera).

Una volta applicata l'ereditarietà, l'interfaccia di estensione espone tutte le proprietà dell'intera catena di ereditarietà.

L'interfaccia di estensione non può modificare alcuna definizione delle interfacce padre. può solo aggiungerli. Non è inoltre in grado di ridefinire una funzionalità già definita in una delle interfacce padre (anche se le funzionalità sono definite come uguali). Se, ad esempio, un'interfaccia padre definisce `double` una *massa* di proprietà, l'interfaccia di estensione non può contenere una dichiarazione di *massa*, anche se è anche un oggetto `double` .

## <a name="best-practices-for-designing-models"></a>Procedure consigliate per la progettazione di modelli

Quando si progettano i modelli in modo da riflettere le entità nell'ambiente in uso, può essere utile esaminare e valutare le implicazioni delle [query](concepts-query-language.md) della progettazione. Si consiglia di progettare le proprietà in modo da evitare set di risultati di grandi dimensioni dall'attraversamento del grafo. È inoltre possibile modellare le relazioni per le quali verrà fornita una risposta in una singola query come relazioni a livello singolo.

### <a name="validating-models"></a>Convalida di modelli

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Strumenti per i modelli 

Sono disponibili diversi esempi per semplificare ulteriormente la gestione di modelli e ontologie. Si trovano in questo repository: [Tools for Digital Gemini Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-tools).

In questa sezione viene descritto in modo più dettagliato il set di esempi corrente.

### <a name="model-uploader"></a>Caricatore modello 

_**Per il caricamento di modelli in dispositivi gemelli digitali di Azure**_

Al termine della creazione, dell'estensione o della selezione dei modelli, è possibile caricarli nell'istanza di Azure Digital Twins per renderli disponibili per l'uso nella soluzione. Questa operazione viene eseguita usando le API dei dispositivi [gemelli digitali di Azure](how-to-use-apis-sdks.md), come descritto in [*procedura: gestire i modelli di DTDL*](how-to-manage-model.md#upload-models).

Tuttavia, se si dispone di molti modelli da caricare, o se sono presenti molte interdipendenze che rendono complessa l'ordinamento di caricamenti singoli, è possibile usare questo esempio per caricare molti modelli contemporaneamente: il caricamento del [**modello di Azure Digital Twins**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader). Seguire le istruzioni fornite con l'esempio per configurare e usare questo progetto per caricare i modelli nell'istanza personalizzata.

### <a name="model-visualizer"></a>Visualizzatore modelli 

_**Per la visualizzazione di modelli**_

Dopo aver caricato i modelli nell'istanza di Azure Digital gemelli, è possibile visualizzare i modelli nell'istanza di Azure Digital Twins, incluse eventuali relazioni di ereditarietà e modello, usando il [**Visualizzatore di modelli di Azure Digital gemelli**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer). Questo esempio si trova attualmente in uno stato bozza. Invitiamo la community di sviluppo di Digital gemelli a estendere e contribuire all'esempio. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla creazione di modelli basati su ontologie standard del settore: [ *concetti: che cos'è un'ontologia?*](concepts-ontologies.md)

* Approfondimento sulla gestione dei modelli con le operazioni API: [ *procedura: gestire i modelli DTDL*](how-to-manage-model.md)

* Informazioni sul modo in cui i modelli vengono usati per creare i dispositivi gemelli digitali: [ *concetti: i gemelli digitali e il grafo gemello*](concepts-twins-graph.md)

