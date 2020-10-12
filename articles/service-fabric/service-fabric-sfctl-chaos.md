---
title: INTERFACCIA della riga di comando di Azure Service Fabric-Chaos sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per la gestione di Chaos.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f59eb3296c27e64eb6a4644b2f455e3704381f49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260837"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Consente di avviare, arrestare e creare report nel servizio di test chaos.

## <a name="subgroups"></a>Sottogruppi
|Sottogruppo|Descrizione|
| --- | --- |
| [pianificazione](service-fabric-sfctl-chaos-schedule.md) | Ottiene e imposta la pianificazione di chaos. |
## <a name="commands"></a>Comandi

|Comando|Descrizione|
| --- | --- |
| eventi | Recupera il segmento successivo degli eventi di Chaos in base al token di continuazione o all'intervallo di tempo. |
| get | Ottiene lo stato di Chaos. |
| Avvio | Avvia Chaos nel cluster. |
| stop | Arresta Chaos se è in esecuzione nel cluster e imposta lo stato della pianificazione di Chaos come arrestata. |

## <a name="sfctl-chaos-events"></a>sfctl chaos events
Recupera il segmento successivo degli eventi di Chaos in base al token di continuazione o all'intervallo di tempo.

Per ottenere il segmento successivo degli eventi di Chaos, è possibile specificare ContinuationToken. Per ottenere l'inizio di un nuovo segmento degli eventi di Chaos, è possibile specificare l'intervallo di tempo tramite StartTimeUtc ed EndTimeUtc. Non è possibile specificare sia ContinuationToken che l'intervallo di tempo nella stessa chiamata. Quando sono presenti più di 100 eventi di Chaos, tali eventi vengono restituiti in più segmenti. Un segmento non contiene più di 100 eventi di Chaos e, per ottenere il segmento successivo, si effettua una chiamata a questa API con il token di continuazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --end-time-utc | Ora di file di Windows che rappresenta l'ora di fine dell'intervallo di tempo per cui verrà generato un report di Chaos. Per informazioni dettagliate, vedere [Metodo DateTime.ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx). |
| --max-results | Il numero massimo di risultati che devono essere restituiti come parte delle query di paging. Questo parametro definisce il limite massimo di risultati restituiti. Se non rientrano nel messaggio in base ai limiti di dimensione massima per i messaggi definiti nella configurazione, il numero dei risultati restituiti può essere inferiore al numero massimo di risultati specificato. Se questo parametro è uguale a zero o non specificato, le query di paging includono il numero massimo di risultati possibili che rientrano nel messaggio restituito. |
| --start-time-utc | Ora di file di Windows che rappresenta l'ora di inizio dell'intervallo di tempo per cui verrà generato un report di Chaos. Per informazioni dettagliate, vedere [Metodo DateTime.ToFileTimeUtc](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx). |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-chaos-get"></a>sfctl chaos get
Ottiene lo stato di Chaos.

Ottiene lo stato di Chaos che indica se Chaos è in esecuzione o meno, i parametri di Chaos usati per l'esecuzione di Chaos e lo stato della pianificazione di Chaos.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Avvia Chaos nel cluster.

Se Chaos non è già in esecuzione nel cluster, avvia Chaos con i parametri di Chaos passati. Se Chaos è già in esecuzione quando viene effettuata la chiamata, la chiamata ha esito negativo con codice di errore FABRIC_E_CHAOS_ALREADY_RUNNING. Per altri dettagli, vedere l'articolo [indurre Chaos controllato nei cluster Service Fabric](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos) .

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --app-type-health-policy-map | Matrice con codifica JSON di voci di dizionario (chiave/valore) con percentuale massima di applicazioni non integre per tipi di applicazione specifici. Ogni voce del dizionario specifica come chiave il nome del tipo di applicazione e un numero intero per valore che rappresenta la percentuale MaxPercentUnhealthyApplications usata per valutare le applicazioni del tipo di applicazione specificato. <br><br> Definisce una mappa con una percentuale massima di applicazioni non integre per tipi di applicazione specifici. La mappa dei criteri di integrità del tipo di applicazione può essere utilizzata durante la valutazione dell'integrità del cluster per descrivere i singoli tipi di applicazioni. I tipi di applicazioni inclusi nella mappa vengono valutati rispetto alla percentuale specificata nella mappa, non con il valore MaxPercentUnhealthyApplications globale definito nei criteri di integrità del cluster. Le applicazioni dei tipi specificati nella mappa non vengono conteggiate nel pool globale di applicazioni. Ad esempio, se alcune applicazioni di un tipo sono critiche, l'amministratore del cluster può aggiungere una voce alla mappa per quel tipo di applicazione e assegnarle un valore pari a 0% (non tollerare eventuali errori). Tutte le altre applicazioni possono essere valutate con MaxPercentUnhealthyApplications impostato sul 20%, in modo da tollerare alcuni errori dalle migliaia di istanze dell'applicazione. La mappa dei criteri di integrità dei tipi di applicazioni viene usata solo se il manifesto del cluster consente la valutazione dell'integrità dei tipi di applicazioni usando la voce di configurazione per HealthManager/EnableApplicationTypeHealthEvaluation. <br><br> Stringa con codifica JSON di esempio: [{ \" Key \" : \" Fabric:/vote \" , \" value \" : \" 0 \" }] |
| --chaos-target-filter | Dizionario con codifica JSON con due chiavi di tipo stringa. Le due chiavi sono NodeTypeInclusionList e ApplicationInclusionList. I valori per entrambe queste chiavi sono un elenco di stringhe. chaos_target_filter definisce tutti i filtri per gli errori Chaos di destinazione, ad esempio generando errori solo per alcuni tipi di nodi o solo per determinate applicazioni. <br><br> Se non si usa chaos_target_filter, gli errori Chaos interessano tutte le entità del cluster. Se si usa chaos_target_filter, gli errori Chaos interessano solo le entità che soddisfano le specifiche di chaos_target_filter. NodeTypeInclusionList e ApplicationInclusionList consentono solo una semantica di unione. Non è possibile specificare un'intersezione tra NodeTypeInclusionList e ApplicationInclusionList. Non è ad esempio possibile specificare "genera un errore in questa applicazione solo quando si trova in quel tipo di nodo". Dopo che un'entità è stata inclusa in NodeTypeInclusionList o in ApplicationInclusionList, tale entità non può essere esclusa tramite ChaosTargetFilter. Anche se in ApplicationInclusionList non compare applicationX, in alcune iterazioni di Chaos applicationX può presentare un errore, perché si trova per caso in un nodo di tipo nodeTypeY incluso in NodeTypeInclusionList. Se NodeTypeInclusionList e ApplicationInclusionList sono entrambi vuoti, viene generata un'eccezione ArgumentException. Tutti i tipi di errore (riavvio di nodo, riavvio di pacchetto di codice, rimozione di replica, riavvio di replica, spostamento di replica primaria e spostamento di replica secondaria) sono abilitati per i nodi di questi tipi di nodo. Se un tipo di nodo (ad esempio NodeTypeX) non compare in NodeTypeInclusionList, gli errori a livello di nodo (ad esempio NodeRestart) non verranno mai abilitati per i nodi di tipo NodeTypeX. Gli errori di pacchetto di codice e di replica, tuttavia, possono comunque essere abilitati per NodeTypeX se un'applicazione in ApplicationInclusionList si trova in un nodo di tipo NodeTypeX. In questo elenco possono essere inclusi al massimo 100 tipi di nodo. Per aumentare questo numero, è necessario un aggiornamento della configurazione di MaxNumberOfNodeTypesInChaosEntityFilter. Tutte le repliche appartenenti ai servizi di queste applicazioni sono soggette a errori di replica (riavvio di replica, rimozione di replica, spostamento di replica primaria, spostamento di replica secondaria) generati da Chaos. Chaos può riavviare un pacchetto di codice solo se quest'ultimo ospita solo repliche di queste applicazioni. Se un'applicazione non compare in questo elenco, può comunque presentare errori in alcune iterazioni di Chaos se l'applicazione finisce per trovarsi in un nodo di un tipo incluso in NodeTypeInclusionList. Se tuttavia applicationX è associata a nodeTypeY tramite vincoli di posizionamento, applicationX non è presente in ApplicationInclusionList e nodeTypeY non è presente in NodeTypeInclusionList, applicationX non presenterà mai errori. In questo elenco possono essere inclusi al massimo 1000 nomi di applicazione. Per aumentare questo numero, è necessario un aggiornamento della configurazione di MaxNumberOfApplicationsInChaosEntityFilter. |
| --context | Mappa con codifica JSON di coppie chiave-valore di tipo (string, string). La mappa può essere usata per registrare le informazioni sull'esecuzione di Chaos. Non possono esserci più di 100 coppie di questo tipo e ogni stringa (chiave o valore) può essere costituita da un massimo di 4095 caratteri. Questa mappa viene impostata dalla funzione di avvio dell'esecuzione di Chaos per l'archiviazione facoltativa del contesto dell'esecuzione specifica. |
| --disable-move-replica-faults | Disabilita lo spostamento primario e sposta gli errori secondari. |
| --max-cluster-stabilization | Tempo di attesa massimo perché tutte le entità del cluster diventino stabili e integre.  Impostazione predefinita\: 60. <br><br> Chaos viene eseguito secondo iterazioni e all'inizio di ogni iterazione convalida l'integrità delle entità del cluster. Se durante la convalida un'entità del cluster non risulta stabile e integra entro il periodo di tempo specificato da MaxClusterStabilizationTimeoutInSeconds, Chaos genera un evento di convalida non riuscita. |
| --max-concurrent-faults | Il numero massimo di errori simultanei indotti per ogni iterazione. Chaos viene eseguito secondo iterazioni e due iterazioni consecutive sono separate da una fase di convalida. Maggiore è la concorrenza, più aggressivo è l'inserimento degli errori, generando serie più complesse di stati per il rilevamento dei bug. È consigliabile iniziare con un valore di 2 o 3 e prestare attenzione durante l'incremento del valore.  Impostazione predefinita\: 1. |
| --max-percent-unhealthy-apps | Quando si esegue la valutazione dell'integrità del cluster durante l'esecuzione di Chaos, indica la percentuale massima consentita di applicazioni non integre prima che venga segnalato un errore. <br><br> Percentuale massima consentita di applicazioni non integre prima che venga segnalato un errore. Ad esempio, per consentire il 10% di applicazioni non integre, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di applicazioni che possono risultare non integre prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un'applicazione non integra, l'integrità viene valutata come Avviso. Questa viene calcolata dividendo il numero di applicazioni non integre rispetto al numero totale di istanze dell'applicazione nel cluster, escluse le applicazioni di tipi inclusi nel parametro ApplicationTypeHealthPolicyMap. Il calcolo viene arrotondato per eccesso per tollerare un errore su un numero limitato di applicazioni. La percentuale predefinita è zero. |
| --max-percent-unhealthy-nodes | Quando si esegue la valutazione dell'integrità del cluster durante l'esecuzione di Chaos, indica la percentuale massima consentita di nodi non integri prima che venga segnalato un errore. <br><br> Percentuale massima consentita di nodi non integri prima che venga segnalato un errore. Ad esempio, per consentire il 10% di nodi non integri, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di nodi che possono risultare non integri prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un nodo non integro, l'integrità viene valutata come Avviso. Tale percentuale viene calcolata dividendo il numero dei nodi non integri per il numero totale di nodi nel cluster. Il calcolo viene arrotondato per eccesso per tollerare un errore su un numero limitato di nodi. La percentuale predefinita è zero. Questa percentuale dovrà essere configurata in modo da tenere conto del fatto che in cluster di grandi dimensioni sono sempre presenti nodi inattivi o in fase di riparazione. |
| --time-to-run | Tempo totale, in secondi, in cui Chaos verrà eseguito prima di interrompersi automaticamente. Il valore massimo consentito è 4.294.967.295 (System.UInt32.MaxValue).  Impostazione predefinita\: 4294967295. |
| --timeout -t | Impostazione predefinita\: 60. |
| --wait-time-between-faults | Tempo di attesa, in secondi, tra errori consecutivi all'interno di una singola iterazione.  Impostazione predefinita\: 20. <br><br> Maggiore è il valore, minore è la sovrapposizione tra gli errori e più semplice la sequenza delle transizioni di stato che attraversa il cluster. È consigliabile iniziare con un valore compreso tra 1 e 5 e prestare attenzione durante l'incremento del valore. |
| --wait-time-between-iterations | Intervallo di tempo, in secondi, tra due iterazioni consecutive di Chaos. Maggiore è il valore, minore è la frequenza di inserimento degli errori.  Impostazione predefinita\: 30. |
| --warning-as-error | Indica se gli avvisi vengono considerati con lo stesso livello di gravità degli errori. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
Arresta Chaos se è in esecuzione nel cluster e imposta lo stato della pianificazione di Chaos come arrestata.

Impedisce a Chaos di eseguire nuovi errori. Gli errori in elaborazione continueranno a essere eseguiti fino al completamento. Lo stato della pianificazione di Chaos corrente viene impostato come arrestato. Dopo che una pianificazione è stata arrestata, rimarrà nello stato arrestato e non verrà usata per nuove esecuzioni di Chaos. È necessario impostare una nuova pianificazione di Chaos per riprendere la pianificazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](./scripts/sfctl-upgrade-application.md).
