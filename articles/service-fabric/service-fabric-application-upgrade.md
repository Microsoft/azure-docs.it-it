---
title: Aggiornamento di un'applicazione di infrastruttura di servizi
description: Questo articolo fornisce un'introduzione all'aggiornamento di un'applicazione di Service Fabric, inclusa la scelta delle modalità di aggiornamento e dei controlli di integrità eseguiti.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 9e7a93dd3ef8a1adf6617dcd57887a0ce694c509
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248000"
---
# <a name="service-fabric-application-upgrade"></a>Aggiornamento di un'applicazione di infrastruttura di servizi
Un'applicazione di Azure Service Fabric è una raccolta di servizi. Durante un aggiornamento, Service Fabric confronta il nuovo [manifesto dell'applicazione](service-fabric-application-and-service-manifests.md) con la versione precedente e determina quali servizi dell'applicazione richiedono aggiornamenti. Service Fabric confronta i numeri di versione nel manifesto del servizio con quelli della versione precedente. Se un servizio non è cambiato, non viene aggiornato.

## <a name="rolling-upgrades-overview"></a>Panoramica degli aggiornamenti in sequenza
In un aggiornamento in sequenza di un'applicazione l'operazione viene eseguita in fasi. In ogni fase l'aggiornamento viene applicato a un subset di nodi del cluster denominato dominio di aggiornamento. In questo modo l'applicazione continua a essere disponibile durante l'aggiornamento. È possibile che durante l'aggiornamento il cluster contenga una combinazione di versioni precedenti e nuove.

Per questo motivo è necessario che le due versioni siano compatibili con le versioni precedenti e successive. In caso contrario, l'amministratore di applicazioni deve organizzare un aggiornamento in più fasi per mantenere la disponibilità. In un aggiornamento a più fasi come primo passaggio si esegue l'aggiornamento a una versione intermedia dell'applicazione che sia compatibile con la versione precedente. Come secondo passaggio si esegue l'aggiornamento alla versione finale, non più compatibile con la versione di pre-aggiornamento ma compatibile con la versione intermedia.

I domini di aggiornamento vengono specificati nel manifesto del cluster quando si configura il cluster. I domini di aggiornamento non ricevono gli aggiornamenti in un ordine particolare. Un dominio di aggiornamento è un'unità logica di distribuzione per un'applicazione. I domini di aggiornamento consentono ai servizi di garantire disponibilità elevata durante un aggiornamento.

Gli aggiornamenti non in sequenza sono possibili qualora l'aggiornamento venga applicato a tutti i nodi del cluster, ad esempio nel caso in cui l'applicazione disponga di un solo dominio di aggiornamento. Questo approccio non è consigliato perché durante l'aggiornamento il servizio rimane inattivo e non disponibile. Azure, inoltre, non offre alcuna garanzia per i casi in cui un cluster è configurato con un solo dominio di aggiornamento.

Al termine dell'aggiornamento, tutti i servizi e le repliche (istanze) manterranno la stessa versione. Se l'aggiornamento ha esito positivo, verranno aggiornati alla nuova versione, in caso contrario verrà eseguito il rollback alla versione precedente.

## <a name="health-checks-during-upgrades"></a>Controlli di integrità durante gli aggiornamenti
Per un aggiornamento devono essere impostati criteri di integrità oppure possono essere usati valori predefiniti. Un aggiornamento viene considerato riuscito quando tutti i domini di aggiornamento vengono aggiornati entro i valori di timeout specificati e risultano integri.  Un dominio di aggiornamento integro significa che ha superato tutti i controlli di integrità specificati nei criteri di integrità. Un criterio di integrità, ad esempio, può richiedere che tutti i servizi all'interno di un'istanza dell'applicazione siano *integri*, secondo quanto definito per l'integrità da Service Fabric.

I criteri e i controlli di integrità durante l'aggiornamento da parte dell’infrastruttura di servizi sono indipendenti dai servizi e dalle applicazioni. In altre parole, non vengono eseguiti test specifici per i servizi.  Ad esempio il servizio potrebbe avere un requisito di velocità effettiva, ma Service Fabric non dispone delle informazioni per verificare la velocità effettiva. Per informazioni sui controlli che vengono eseguiti, vedere gli [articoli sull'integrità](service-fabric-health-introduction.md) . I controlli eseguiti durante l'aggiornamento includono test di verifica che il pacchetto dell'applicazione sia stato copiato correttamente, che l'istanza sia stata avviata e così via.

L'integrità dell'applicazione è un'aggregazione delle entità figlio dell'applicazione. In breve, Service Fabric valuta l'integrità dell'applicazione tramite l'integrità segnalata per l'applicazione. In questo modo, può valutare anche l'integrità di tutti i servizi relativi all'applicazione. In alternativa, Service Fabric può valutare l'integrità dei servizi aggregando l'integrità degli elementi figlio, ad esempio la replica dei servizi. Se il criterio di integrità dell'applicazione è soddisfatto, l'aggiornamento può continuare. In caso contrario, l'aggiornamento dell'applicazione ha esito negativo.

## <a name="upgrade-modes"></a>Modalità di aggiornamento
La modalità consigliata per l'aggiornamento dell'applicazione è la modalità monitorata, il metodo più comune. che esegue l'aggiornamento di un dominio di aggiornamento e, se vengono superati tutti i controlli di integrità (in base ai criteri specificati), passa automaticamente al dominio di aggiornamento successivo.  In caso di esito negativo e/o di timeout dei controlli di integrità, viene eseguito il rollback dell'aggiornamento del dominio di aggiornamento oppure la modalità cambia in UnmonitoredManual. È possibile configurare l'aggiornamento in modo che venga scelta una di queste due modalità per gli aggiornamenti non riusciti. 

La modalità UnmonitoredManual richiede l'intervento manuale dopo ogni aggiornamento eseguito in un dominio di aggiornamento per passare al dominio di aggiornamento successivo. Non viene eseguito alcun controllo di integrità su Service Fabric. L'amministratore esegue i controlli di integrità o di stato previsti prima di avviare l'aggiornamento nel dominio di aggiornamento successivo.

## <a name="upgrade-default-services"></a>Aggiornare i servizi predefiniti
Alcuni parametri di servizi predefiniti nel [manifesto dell'applicazione](service-fabric-application-and-service-manifests.md) possono essere aggiornati anche durante un aggiornamento dell'applicazione. Durante un aggiornamento possono essere modificati solo i parametri di servizi che supportano la modifica tramite [Update-ServiceFabricService](/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps). Di seguito viene illustrato il comportamento in caso di modifica dei servizi predefiniti durante l'aggiornamento dell'applicazione:

1. I servizi predefiniti presenti nel nuovo manifesto dell'applicazione che ancora non esistono nel cluster vengono creati.
2. I servizi predefiniti che esistono sia nel manifesto dell'applicazione precedente sia in quello nuovo vengono aggiornati. I parametri del servizio predefinito presente nel nuovo manifesto dell'applicazione sovrascrivono i parametri del servizio esistente. Se si verifica un errore durante l'aggiornamento di un servizio predefinito, verrà eseguito il rollback automatico dell'aggiornamento dell'applicazione.
3. I servizi predefiniti che non esistono nel nuovo manifesto dell'applicazione ma sono presenti nel cluster vengono eliminati. **Si noti che l'eliminazione di un servizio predefinito comporterà l'eliminazione di tutti gli stati del servizio e questa operazione non potrà essere annullata.**

Quando viene eseguito il rollback di un aggiornamento dell'applicazione, vengono ripristinati i valori dei parametri del servizio predefinito precedenti all'avvio dell'aggiornamento. Tuttavia, non è possibile ricreare i servizi eliminati con lo stato precedente.

> [!TIP]
> Il valore dell'impostazione di configurazione del cluster [EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md) deve essere *true* per abilitare le regole 2) e 3) indicate in precedenza (aggiornamento ed eliminazione di un servizio predefinito). Questa funzionalità è supportata in Service Fabric a partire dalla versione 5.5.

## <a name="upgrading-multiple-applications-with-https-endpoints"></a>Aggiornamento di più applicazioni con endpoint HTTPS
Quando si usa HTTP**S**, è necessario prestare attenzione a non usare la **stessa porta** per istanze diverse della stessa applicazione. Ciò è dovuto al fatto che Service Fabric non sarà in grado di aggiornare il certificato per una delle istanze dell'applicazione. Ad esempio, se sia l'applicazione 1 che l'applicazione 2 desiderano eseguire l'aggiornamento del certificato 1 al certificato 2. Al momento dell'aggiornamento, Service Fabric potrebbe avere eliminato la registrazione del certificato 1 con http.sys anche se è ancora utilizzato dall'altra applicazione. Per evitare questo problema, Service Fabric rileva che è già presente un'altra istanza dell'applicazione registrata sulla porta con il certificato (a causa di http.sys) e l'operazione ha esito negativo.

Pertanto Service Fabric non supporta l'aggiornamento di due servizi diversi mediante **la stessa porta** in istanze dell'applicazione diverse. In altre parole, non è possibile utilizzare lo stesso certificato in servizi diversi sulla stessa porta. Se è necessario avere un certificato condiviso sulla stessa porta, occorre verificare che i servizi si trovino su computer diversi con vincoli di posizionamento. In alternativa, utilizzare le porte dinamiche di Service Fabric se possibile per ogni servizio in ogni istanza dell'applicazione. 

Se si verifica un errore di aggiornamento con https, viene visualizzato un avviso indicante che l'API del server Windows HTTP non supporta più certificati per le applicazioni che condividono una porta.

## <a name="application-upgrade-flowchart"></a>Diagramma di flusso di aggiornamento di un'applicazione
Il diagramma di flusso che segue questo paragrafo aiuta a capire il processo di aggiornamento di un'applicazione di Service Fabric. In particolare, il flusso descrive in che modo i valori di timeout, ad esempio *HealthCheckStableDuration*, *HealthCheckRetryTimeout* e *UpgradeHealthCheckInterval*, consentono di verificare se l'aggiornamento in un dominio di aggiornamento viene considerato riuscito o non riuscito.

![Processo di aggiornamento per un'applicazione di Service Fabric][image]

## <a name="next-steps"></a>Passaggi successivi
[Esercitazione sull'aggiornamento di un'applicazione di Service Fabric tramite Visual Studio](service-fabric-application-upgrade-tutorial.md) descrive la procedura di aggiornamento di un'applicazione con Visual Studio.

[Aggiornamento di un'applicazione mediante PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) descrive la procedura di aggiornamento di un'applicazione tramite PowerShell.

Controllare il modo in cui l'applicazione viene aggiornata usando i [parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Rendere compatibili gli aggiornamenti dell'applicazione imparando a usare la [serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Informazioni su come usare le funzionalità avanzate durante l'aggiornamento dell'applicazione facendo riferimento ad [argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Per risolvere i problemi comuni negli aggiornamenti dell'applicazione, fare riferimento ai passaggi descritti in [risoluzione dei problemi relativi agli aggiornamenti dell'applicazione](service-fabric-application-upgrade-troubleshooting.md).

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png
