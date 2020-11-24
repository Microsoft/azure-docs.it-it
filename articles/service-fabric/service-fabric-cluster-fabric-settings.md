---
title: Modificare le impostazioni di un cluster di Azure Service Fabric
description: Questo articolo descrive le impostazioni dell'infrastruttura e i criteri di aggiornamento dell'infrastruttura che è possibile personalizzare.
ms.topic: reference
ms.date: 08/30/2019
ms.openlocfilehash: 1f16e89dd1131f6aea64e5e72a342b3b737f3728
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542644"
---
# <a name="customize-service-fabric-cluster-settings"></a>Personalizzare le impostazioni di un cluster di Service Fabric
Questo articolo illustra le varie impostazioni dell'infrastruttura per il cluster di Service Fabric che è possibile personalizzare. Per i cluster ospitati in Azure, è possibile personalizzare le impostazioni tramite il [portale di Azure](https://portal.azure.com) o con un modello di Azure Resource Manager. Per altre informazioni, vedere [Upgrade the configuration of an Azure cluster](service-fabric-cluster-config-upgrade-azure.md) (Aggiornare la configurazione di un cluster Azure). Per i cluster autonomi è possibile personalizzare le impostazioni aggiornando il file *ClusterConfig.json* ed eseguendo un aggiornamento della configurazione nel cluster. Per altre informazioni, vedere [Aggiornare la configurazione di un cluster autonomo](service-fabric-cluster-config-upgrade-windows-server.md).

Esistono tre diversi criteri di aggiornamento:

- **Dinamici**: le modifiche apportate a una configurazione dinamica non causano il riavvio né dei processi di Service Fabric, né dei processi host del servizio. 
- **Statici**: le modifiche apportate a una configurazione statica comportano il riavvio del nodo di Service Fabric per poter applicare le modifiche. I servizi sui nodi verranno riavviati.
- **Non consentiti**: queste impostazioni non possono essere modificate. Per modificare queste impostazioni è necessario eliminare il cluster e crearne uno nuovo. 

Di seguito è riportato un elenco di impostazioni dell'infrastruttura che è possibile personalizzare, organizzate per sezione.

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ApplicationCertificateValidationPolicy|stringa, il valore predefinito è "None"|Statico| Ciò non convalida il certificato del server; esito positivo della richiesta. Fare riferimento alla configurazione ServiceCertificateThumbprints per un elenco delimitato da virgole di identificazioni personali dei certificati remoti che il proxy inverso può considerare attendibili. Fare riferimento alla configurazione ServiceCommonNameAndIssuer per il nome soggetto e l'identificazione personale dell'autorità di certificazione dei certificati remoti che il proxy inverso può considerare attendibili. Per altre informazioni, vedere [Connessione protetta del proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |
|BodyChunkSize |Uint, valore predefinito: 16384 |Dinamico| Indica la dimensione in byte per il blocco usato per leggere il corpo. |
|CrlCheckingFlag|uint, valore predefinito: 0x40000000 |Dinamico| Flag per la convalida della catena di certificati di applicazione/servizio. Ad esempio i controlli di CRL 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY L'impostazione 0 disabilita il controllo di CRL L'elenco completo dei valori supportati è documentato da dwFlags di CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx  |
|DefaultHttpRequestTimeout |Tempo in secondi, Il valore predefinito è 120 |Dinamico|Specificare l'intervallo di tempo in secondi.  Indica il timeout di richiesta predefinito per le richieste http in elaborazione nel gateway applicazione http. |
|ForwardClientCertificate|bool, valore predefinito: FALSE|Dinamico|Quando impostato su false, il proxy inverso non richiederà il certificato client. Quando impostato su true, il proxy inverso richiede il certificato client durante l'handshake TLS e inoltra la stringa in formato PEM con codifica base64 al servizio in un'intestazione denominata X-Client-Certificate. Il servizio può respingere la richiesta con un codice di stato appropriato dopo aver esaminato i dati del certificato. Se è impostato su true e il client non presenta un certificato, il proxy inverso inoltra un'intestazione vuota e il caso viene gestito dal servizio. Un livello trasparente fungerà da proxy inverso. Per altre informazioni, vedere [Configurare l'autenticazione con certificato client](service-fabric-reverseproxy-configure-secure-communication.md#setting-up-client-certificate-authentication-through-the-reverse-proxy). |
|GatewayAuthCredentialType |stringa, il valore predefinito è "None" |Statico| Indica il tipo di credenziali di sicurezza da usare nell'endpoint del gateway applicazione http. I valori validi sono None/X509. |
|GatewayX509CertificateFindType |stringa, valore predefinito è "FindByThumbprint" |Dinamico| Indica la modalità di ricerca del certificato nell'archivio specificato da GatewayX509CertificateStoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | stringa, il valore predefinito è "" |Dinamico| Valore del filtro di ricerca usato per individuare il certificato del gateway applicazione http. Il certificato è configurato nell'endpoint https e può essere usato anche per verificare l'identità dell'app se richiesto dai servizi. Viene prima cercato FindValue. Se non esiste, viene cercato FindValueSecondary. |
|GatewayX509CertificateFindValueSecondary | stringa, il valore predefinito è "" |Dinamico|Valore del filtro di ricerca usato per individuare il certificato del gateway applicazione http. Il certificato è configurato nell'endpoint https e può essere usato anche per verificare l'identità dell'app se richiesto dai servizi. Viene prima cercato FindValue. Se non esiste, viene cercato FindValueSecondary.|
|GatewayX509CertificateStoreName |stringa, il valore predefinito è "My" |Dinamico| Nome dell'archivio certificati X.509 che contiene il certificato per il gateway applicazione http. |
|HttpRequestConnectTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(5)|Dinamico|Specificare l'intervallo di tempo in secondi.  Indica il timeout di connessione per le richieste HTTP inviate dal gateway applicazione HTTP.  |
|IgnoreCrlOfflineError|bool, valore predefinito: TRUE|Dinamico|Indica se ignorare l'errore di CRL offline per la verifica del certificato di applicazioni/servizi. |
|IsEnabled |Bool, valore predefinito: false |Statico| Abilita/disabilita HttpApplicationGateway. HttpApplicationGateway è disabilitato per impostazione predefinita. È necessario impostare questa configurazione per abilitarlo. |
|NumberOfParallelOperations | Uint, valore predefinito: 5000 |Statico|Numero di letture da pubblicare nella coda del server http. Consente di controllare il numero di richieste simultanee che possono essere espletate da HttpGateway. |
|RemoveServiceResponseHeaders|string, valore predefinito: "Date; Server"|Statico|Elenco delimitato da punti e virgola/virgole delle intestazioni di risposta che verranno rimosse dalla risposta del servizio prima di inoltrarla al client. Se è impostato su una stringa vuota, tutte le intestazioni restituite dal servizio vengono passate così come sono, vale a dire non vengono sovrascritti i valori Date e Server. |
|ResolveServiceBackoffInterval |Tempo in secondi, valore predefinito: 5 |Dinamico|Specificare l'intervallo di tempo in secondi.  Indica l'intervallo di backoff predefinito prima di ritentare l'operazione di risoluzione del servizio. |
|SecureOnlyMode|bool, valore predefinito: FALSE|Dinamico| SecureOnlyMode: true: il proxy inverso eseguirà l'inoltro solo ai servizi che pubblicano endpoint sicuri. false: il proxy inverso può inoltrare le richieste a endpoint sicuri/non sicuri. Per altre informazioni, vedere [Logica di selezione dell'endpoint del proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md#endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints).  |
|ServiceCertificateThumbprints|stringa, il valore predefinito è ""|Dinamico|L'elenco delimitato da virgole di identificazioni personali dei certificati remoti che il proxy inverso può considerare attendibili. Per altre informazioni, vedere [Connessione protetta del proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="applicationgatewayhttpservicecommonnameandissuer"></a>ApplicationGateway/Http/ServiceCommonNameAndIssuer

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valore predefinito: None|Dinamico| Nome soggetto e identificazione personale dell'autorità di certificazione dei certificati remoti che il proxy inverso può considerare attendibili. Per altre informazioni, vedere [Connessione protetta del proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md#secure-connection-establishment-between-the-reverse-proxy-and-services). |

## <a name="backuprestoreservice"></a>BackupRestoreService

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, valore predefinito: 0|Statico|MinReplicaSetSize per BackupRestoreService |
|PlacementConstraints|stringa, il valore predefinito è    ""|Statico|    PlacementConstraints per BackupRestoreService |
|SecretEncryptionCertThumbprint|stringa, il valore predefinito è    ""|Dinamico|Identificazione personale del certificato X509 di crittografia segreta |
|SecretEncryptionCertX509StoreName|stringa, il valore consigliato è "My" (nessun valore predefinito) |    Dinamico|    Indica il certificato da usare per la crittografia e la decrittografia del nome credenziali dell'archivio certificati X.509 usato per crittografare e decrittografare le credenziali dell'archivio impiegate dal servizio di backup e ripristino |
|TargetReplicaSetSize|int, valore predefinito: 0|Statico| TargetReplicaSetSize per BackupRestoreService |

## <a name="clustermanager"></a>ClusterManager

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|AllowCustomUpgradeSortPolicies | Bool, valore predefinito: false |Dinamico|Indica se i criteri di ordinamento dell'aggiornamento personalizzato sono consentiti o meno. Viene usato per eseguire l'aggiornamento a 2 fasi che abilita questa funzionalità. Service Fabric 6.5 aggiunge il supporto per specificare i criteri di ordinamento per i domini di aggiornamento durante gli aggiornamenti del cluster o dell'applicazione. I criteri supportati sono Numeric, Lexicographical, ReverseNumeric e ReverseLexicographical. Il criterio predefinito è Numeric. Per usare questa funzionalità, l'impostazione del manifesto del cluster ClusterManager/AllowCustomUpgradeSortPolicies deve essere impostata su True come secondo passaggio di aggiornamento della configurazione dopo l'aggiornamento del codice di SF 6.5. È importante eseguire questa operazione in due fasi, in caso contrario durante l'aggiornamento del codice l'ordine del primo aggiornamento potrebbe non essere riconosciuto.|
|EnableDefaultServicesUpgrade | Bool, valore predefinito: false |Dinamico|Abilita l'aggiornamento dei servizi predefiniti durante l'aggiornamento dell'applicazione. Le descrizioni dei servizi predefiniti vengono sovrascritte in seguito all'aggiornamento. |
|FabricUpgradeHealthCheckInterval |Tempo in secondi, il valore predefinito è 60 |Dinamico|Frequenza dei controlli sullo stato di integrità durante un aggiornamento di un'infrastruttura monitorata |
|FabricUpgradeStatusPollInterval |Tempo in secondi, il valore predefinito è 60 |Dinamico|Frequenza di polling dello stato di aggiornamento dell'infrastruttura. Questo valore determina la frequenza di aggiornamento di qualsiasi chiamata GetFabricUpgradeProgress |
|ImageBuilderTimeoutBuffer |Tempo in secondi, valore predefinito: 3 |Dinamico|Specificare l'intervallo di tempo in secondi. Il tempo per consentire la restituzione al client degli errori di timeout specifici di Image Builder. Se il buffer è troppo piccolo, il client giunge a un timeout prima del server e riceve un errore di timeout generico. |
|InfrastructureTaskHealthCheckRetryTimeout | Tempo in secondi, il valore predefinito è 60 |Dinamico|Specificare l'intervallo di tempo in secondi. Il tempo impiegato per eseguire nuovamente i controlli di integrità negativi durante la post-elaborazione di un'attività di infrastruttura. In caso di un controllo di integrità positivo, il timer verrà reimpostato. |
|InfrastructureTaskHealthCheckStableDuration | Tempo in secondi, valore predefinito: 0|Dinamico| Specificare l'intervallo di tempo in secondi. Il tempo in cui osservare controlli di integrità positivi e consecutivi prima del completamento positivo della post-elaborazione di un'attività di infrastruttura. In caso di un controllo di integrità negativo, il timer verrà reimpostato. |
|InfrastructureTaskHealthCheckWaitDuration |Tempo in secondi, valore predefinito: 0|Dinamico| Specificare l'intervallo di tempo in secondi. Il tempo di attesa prima di avviare controlli di integrità dopo la post-elaborazione di un'attività di infrastruttura. |
|InfrastructureTaskProcessingInterval | Tempo in secondi, il valore predefinito è 10 |Dinamico|Specificare l'intervallo di tempo in secondi. L'intervallo di elaborazione usato dal computer dello stato di elaborazione dell'attività di infrastruttura. |
|MaxCommunicationTimeout |Tempo in secondi, valore predefinito: 600 |Dinamico|Specificare l'intervallo di tempo in secondi. Timeout massimo per le comunicazioni interne tra ClusterManager e altri servizi di sistema, ad esempio Naming Service, Gestione failover e così via. Questo timeout deve essere inferiore a MaxOperationTimeout globale, poiché potrebbero avvenire più comunicazioni tra i componenti di sistema per ciascuna operazione client. |
|MaxDataMigrationTimeout |Tempo in secondi, valore predefinito: 600 |Dinamico|Specificare l'intervallo di tempo in secondi. Timeout massimo per le operazioni di ripristino di migrazione di dati dopo un aggiornamento di Service Fabric. |
|MaxOperationRetryDelay |Tempo in secondi, valore predefinito: 5|Dinamico| Specificare l'intervallo di tempo in secondi. Ritardo massimo per i tentativi interni quando vengono rilevati errori. |
|MaxOperationTimeout |Tempo in secondi, valore predefinito: MaxValue |Dinamico| Specificare l'intervallo di tempo in secondi. Il timeout massimo globale per l'elaborazione interna delle operazioni in ClusterManager. |
|MaxTimeoutRetryBuffer | Tempo in secondi, valore predefinito: 600 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout massimo dell'operazione durante i tentativi interni dovuti a timeout è `<Original Time out> + <MaxTimeoutRetryBuffer>`. Viene aggiunto timeout aggiuntivo in incrementi di MinOperationTimeout. |
|MinOperationTimeout | Tempo in secondi, il valore predefinito è 60 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout minimo globale per l'elaborazione interna delle operazioni in ClusterManager. |
|MinReplicaSetSize |Int, valore predefinito: 3 |Non consentito|MinReplicaSetSize per ClusterManager. |
|PlacementConstraints | stringa, il valore predefinito è "" |Non consentito|PlacementConstraints per ClusterManager. |
|QuorumLossWaitDuration |Tempo in secondi, valore predefinito: MaxValue |Non consentito| Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per ClusterManager. |
|ReplicaRestartWaitDuration |Tempo in secondi, il valore predefinito è (60.0 \* 30)|Non consentito|Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per ClusterManager. |
|ReplicaSetCheckTimeoutRollbackOverride |Tempo in secondi, valore predefinito: 1200 |Dinamico| Specificare l'intervallo di tempo in secondi. Se ReplicaSetCheckTimeout è impostato sul valore massimo di DWORD, questo verrà sovrascritto dal valore di questa configurazione ai fini del rollback. Il valore usato per il roll forward non viene mai sovrascritto. |
|SkipRollbackUpdateDefaultService | Bool, valore predefinito: false |Dinamico|CM non riporterà allo stato precedente i servizi predefiniti aggiornati durante il rollback dell'aggiornamento dell'applicazione. |
|StandByReplicaKeepDuration | Tempo in secondi, il valore predefinito è (3600.0 \* 2)|Non consentito|Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per ClusterManager. |
|TargetReplicaSetSize |Int, valore predefinito: 7 |Non consentito|TargetReplicaSetSize per ClusterManager. |
|UpgradeHealthCheckInterval |Tempo in secondi, il valore predefinito è 60 |Dinamico|Frequenza dei controlli sullo stato di integrità durante gli aggiornamenti di un'applicazione monitorata |
|UpgradeStatusPollInterval |Tempo in secondi, il valore predefinito è 60 |Dinamico|Frequenza di polling sullo stato di aggiornamento dell'applicazione. Questo valore determina la frequenza di aggiornamento di qualsiasi chiamata GetApplicationUpgradeProgress |
|CompleteClientRequest | Bool, valore predefinito: false |Dinamico| Completa la richiesta client quando viene accettata da CM. |

## <a name="common"></a>Comuni

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PerfMonitorInterval |Tempo in secondi, valore predefinito: 1 |Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo del monitoraggio delle prestazioni. Con valore 0 o negativo, il monitoraggio è disabilitato. |

## <a name="defragmentationemptynodedistributionpolicy"></a>DefragmentationEmptyNodeDistributionPolicy
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, valore predefinito: None|Dinamico|Specifica i criteri per la deframmentazione da eseguire dopo lo svuotamento dei nodi. Per una determinata metrica, 0 indica che Service Fabric deve provare a deframmentare i nodi in modo uniforme tra i domini di aggiornamento e i domini di errore. 1 indica solo che i nodi devono essere deframmentati. |

## <a name="defragmentationmetrics"></a>DefragmentationMetrics
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyBoolValueMap, valore predefinito: None|Dinamico|Determina il set di metriche che deve essere usato per la deframmentazione e non per il bilanciamento del carico. |

## <a name="defragmentationmetricspercentornumberofemptynodestriggeringthreshold"></a>DefragmentationMetricsPercentOrNumberOfEmptyNodesTriggeringThreshold
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, valore predefinito: None|Dinamico|Determina il numero di nodi liberi necessari per considerare il cluster deframmentato specificando un intervallo in percentuale [0,0-1,0] o il numero di nodi vuoti > = 1,0 |

## <a name="diagnostics"></a>Diagnostica

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|AdminOnlyHttpAudit |Bool, valore predefinito: true | Dinamico | Escludere le richieste HTTP che non influiscono sullo stato del cluster dal controllo. Attualmente sono escluse solo le richieste di tipo "GET", ma questo scenario è soggetto a modifiche. |
|AppDiagnosticStoreAccessRequiresImpersonation |Bool, valore predefinito: true | Dinamico |Indica se è richiesta la rappresentazione quando si accede a un archivio di diagnostica per conto dell'applicazione. |
|AppEtwTraceDeletionAgeInDays |Int, valore predefinito: 3 | Dinamico |Il numero di giorni dopo il quale i file ETL meno recenti contenenti tracce ETW di applicazioni vengono eliminati. |
|ApplicationLogsFormatVersion |Int, valore predefinito: 0 | Dinamico |Versione per il formato dei log applicazioni. I valori supportati sono 0 e 1. La versione 1 include più campi dal record di eventi ETW rispetto alla versione 0. |
|AuditHttpRequests |Bool, valore predefinito: false | Dinamico | Attivare o disattivare il controllo HTTP. Lo scopo del controllo è rilevare le attività eseguite nel cluster, incluso l'utente che ha avviato la richiesta. Si noti che si tratta di un tentativo di registrazione e che potrebbe verificarsi una perdita di traccia. Le richieste HTTP con autenticazione "User" non vengono registrate. |
|CaptureHttpTelemetry|Bool, valore predefinito: true | Dinamico | Attivare o disattivare la telemetria HTTP. Lo scopo della telemetria è permettere a Service Fabric di acquisire i dati di telemetria per pianificare il lavoro futuro e identificare le aree problematiche. La telemetria non registra dati personali o il corpo della richiesta. La telemetria acquisisce tutte le richieste HTTP se non diversamente configurata. |
|ClusterId |string | Dinamico |L'ID univoco del cluster. Viene generato quando viene creato il cluster. |
|ConsumerInstances |string | Dinamico |L'elenco delle istanze di consumer DCA. |
|DiskFullSafetySpaceInMB |Int, valore predefinito: 1024 | Dinamico |Spazio su disco rimanente in MB per la protezione dall'uso di DCA. |
|EnableCircularTraceSession |Bool, valore predefinito: false | Statico |Il flag indica se devono essere usate le sessioni di traccia circolari. |
|EnablePlatformEventsFileSink |Bool, valore predefinito: false | Statico |Abilita/Disabilita la scrittura degli eventi della piattaforma su disco |
|EnableTelemetry |Bool, valore predefinito: true | Dinamico |Parametro per abilitare o disabilitare la telemetria. |
|FailuresOnlyHttpTelemetry | Bool, valore predefinito: false | Dinamico | Se è abilitata l'acquisizione della telemetria HTTP. Vengono acquisite solo le richieste non riuscite. Ciò consente di ridurre il numero di eventi generati per la telemetria. |
|HttpTelemetryCapturePercentage | int, valore predefinito: 50 | Dinamico | Se è abilitata l'acquisizione della telemetria HTTP. Viene acquisita solo una percentuale casuale di richieste. Ciò consente di ridurre il numero di eventi generati per la telemetria. |
|MaxDiskQuotaInMB |Int, valore predefinito: 65536 | Dinamico |Quota del disco in MB per i file di log di Windows Fabric. |
|ProducerInstances |string | Dinamico |L'elenco delle istanze di producer DCA. |

## <a name="dnsservice"></a>DnsService
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|EnablePartitionedQuery|bool, valore predefinito: FALSE|Statico|Flag per abilitare il supporto per le query DNS per i servizi partizionati. La funzionalità è disattivata per impostazione predefinita. Per altre informazioni, vedere [Servizio DNS di Service Fabric.](service-fabric-dnsservice.md)|
|InstanceCount|int, valore predefinito: -1|Statico|Il valore predefinito è -1, il che significa che DnsService è in esecuzione su ogni nodo. OneBox deve essere impostata su 1 perché DnsService usa la porta nota 53, pertanto non può avere più istanze sullo stesso computer.|
|IsEnabled|bool, valore predefinito: FALSE|Statico|Abilita/Disabilita DnsService. DnsService è disabilitato per impostazione predefinita. È necessario impostare questa configurazione per abilitarlo. |
|PartitionPrefix|string, valore predefinito "--"|Statico|Controlla il valore della stringa prefisso di partizione nelle query DNS per i servizi partizionati. Il valore: <ul><li>Deve essere conforme a RFC poiché sarà parte di una query DNS.</li><li>Non deve contenere un punto '.', poiché il punto interferisce con il comportamento del suffisso DNS.</li><li>Non può contenere più di 5 caratteri.</li><li>Non può essere una stringa vuota.</li><li>Se viene eseguito l'override dell'impostazione PartitionPrefix, PartitionSuffix deve essere sottoposto a override e viceversa.</li></ul>Per altre informazioni, vedere [Servizio DNS di Service Fabric](service-fabric-dnsservice.md).|
|PartitionSuffix|stringa, il valore predefinito è ""|Statico|Controlla il valore della stringa suffisso di partizione nelle query DNS per i servizi partizionati. Il valore: <ul><li>Deve essere conforme a RFC poiché sarà parte di una query DNS.</li><li>Non deve contenere un punto '.', poiché il punto interferisce con il comportamento del suffisso DNS.</li><li>Non può contenere più di 5 caratteri.</li><li>Se viene eseguito l'override dell'impostazione PartitionPrefix, PartitionSuffix deve essere sottoposto a override e viceversa.</li></ul>Per altre informazioni, vedere [Servizio DNS di Service Fabric](service-fabric-dnsservice.md). |
|RetryTransientFabricErrors|Bool, valore predefinito: true|Statico|L'impostazione controlla le funzionalità di ripetizione dei tentativi durante la chiamata di Service Fabric API da DnsService. Quando è abilitata, ritenta fino a tre volte se si verifica un errore temporaneo.|

## <a name="eventstoreservice"></a>EventStoreService

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|MinReplicaSetSize|int, valore predefinito: 0|Statico|MinReplicaSetSize per il servizio EventStore |
|PlacementConstraints|stringa, il valore predefinito è    ""|Statico|    PlacementConstraints per il servizio EventStore |
|TargetReplicaSetSize|int, valore predefinito: 0|Statico| TargetReplicaSetSize per il servizio EventStore |

## <a name="fabricclient"></a>FabricClient

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ConnectionInitializationTimeout |Tempo in secondi, valore predefinito: 2 |Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di timeout di connessione per ogni tentativo di apertura di connessione del client al gateway.|
|HealthOperationTimeout |Tempo in secondi, valore predefinito: 120 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout per un messaggio di report inviato a Health Manager. |
|HealthReportRetrySendInterval |Tempo in secondi, valore predefinito: 30 minuti, valore minimo: 1 |Dinamico|Specificare l'intervallo di tempo in secondi. L'intervallo dopo il quale il componente di report invia nuovamente i report sull'integrità accumulati a Health Manager. |
|HealthReportSendInterval |Tempo in secondi, il valore predefinito è 30 |Dinamico|Specificare l'intervallo di tempo in secondi. L'intervallo dopo il quale il componente di report invia i report sull'integrità accumulati a Health Manager. |
|KeepAliveIntervalInSeconds |Int, valore predefinito: 20 |Statico|L'intervallo con cui il trasporto FabricClient invia messaggi keep-alive al gateway. Se il valore è 0, keepAlive è disabilitato. Deve essere un valore positivo. |
|MaxFileSenderThreads |Uint, valore predefinito: 10 |Statico|Il numero massimo di file trasferiti in parallelo. |
|NodeAddresses |stringa, il valore predefinito è "" |Statico|Una collezione di indirizzi (stringhe di connessione) su diversi nodi che possono essere usati per comunicare con Naming Service. Inizialmente, il client si connette scegliendo casualmente uno degli indirizzi. Se viene specificata più di una stringa e una connessione non riesce a causa di un errore di comunicazione o timeout, il client usa l'indirizzo successivo in maniera sequenziale. Per i dettagli di semantica sui nuovi tentativi, vedere la sezione relativa del Naming Service. |
|PartitionLocationCacheLimit |Int, valore predefinito: 100000 |Statico|Numero di partizioni memorizzate nella cache per la risoluzione di servizio. Impostarlo su 0 per non avere limiti. |
|RetryBackoffInterval |Tempo in secondi, valore predefinito: 3 |Dinamico|Specificare l'intervallo di tempo in secondi. L'intervallo di backoff prima di ritentare l'operazione. |
|ServiceChangePollInterval |Tempo in secondi, valore predefinito: 120 |Dinamico|Specificare l'intervallo di tempo in secondi. L'intervallo tra i polling consecutivi per le modifiche di servizio dal client al gateway, per i callback di notifica in caso di modifiche al servizio registrato. |

## <a name="fabrichost"></a>FabricHost

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Int, valore predefinito: 10 |Dinamico|Conteggio massimo di tentativi di attivazione da parte del sistema prima di interrompersi. |
|ActivationMaxRetryInterval |Tempo in secondi, il valore predefinito è 300 |Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo massimo tra i tentativi di attivazione. In casi di errori periodici, l'intervallo tra i tentativi viene calcolato come segue: Min( ActivationMaxRetryInterval; conteggio errori continui * ActivationRetryBackoffInterval). |
|ActivationRetryBackoffInterval |Tempo in secondi, valore predefinito: 5 |Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di backoff per ogni errore di attivazione; in caso di errore di attivazione continua, il sistema ritenta l'attivazione fino al massimo valore definito in MaxActivationFailureCount. L'intervallo tra tentativi è il prodotto dell'errore di attivazione continua e dell'intervallo di backoff di attivazione. |
|EnableRestartManagement |Bool, valore predefinito: false |Dinamico|Parametro per abilitare il riavvio del server. |
|EnableServiceFabricAutomaticUpdates |Bool, valore predefinito: false |Dinamico|Parametro per abilitare l'aggiornamento automatico di Service Fabric tramite Windows Update. |
|EnableServiceFabricBaseUpgrade |Bool, valore predefinito: false |Dinamico|Parametro per abilitare l'aggiornamento base del server. |
|FailureReportingExpeditedReportingIntervalEnabled | Bool, valore predefinito: true | Statico | Consente un maggiore velocità di caricamento in DCA quando FabricHost è in modalità di segnalazione errori. |
|FailureReportingTimeout | TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(60) | Statico |Specificare l'intervallo di tempo in secondi. Timeout per la segnalazione di errori DCA nel caso in cui FabricHost riscontri un errore di avvio in fase iniziale. | 
|RunDCAOnStartupFailure | Bool, valore predefinito: true | Statico |Determina se avviare DCA per caricare i log quando si verificano problemi di avvio in FabricHost. | 
|StartTimeout |Tempo in secondi, il valore predefinito è 300 |Dinamico|Specificare l'intervallo di tempo in secondi. Timeout per l'avvio di fabricactivationmanager. |
|StopTimeout |Tempo in secondi, il valore predefinito è 300 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout per l'attivazione, la disattivazione e l'aggiornamento del servizio ospitato. |

## <a name="fabricnode"></a>FabricNode

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ClientAuthX509FindType |stringa, valore predefinito è "FindByThumbprint" |Dinamico|Indica la modalità di ricerca del certificato nell'archivio specificato da ClientAuthX509StoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
|ClientAuthX509FindValue |stringa, il valore predefinito è "" | Dinamico|Valore di filtro di ricerca usato per individuare un certificato per il ruolo di amministratore predefinito FabricClient. |
|ClientAuthX509FindValueSecondary |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare un certificato per il ruolo di amministratore predefinito FabricClient. |
|ClientAuthX509StoreName |stringa, il valore predefinito è "My" |Dinamico|Nome dell'archivio certificati X.509 che contiene un certificato per il ruolo di amministratore predefinito FabricClient. |
|ClusterX509FindType |stringa, valore predefinito è "FindByThumbprint" |Dinamico|Indica la modalità di ricerca del certificato del cluster nell'archivio specificato da ClusterX509StoreName. Valori supportati: "FindByThumbprint"; "FindBySubjectName" con "FindBySubjectName"; quando sono presenti più corrispondenze; viene usata quella con la scadenza più lontana. |
|ClusterX509FindValue |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare il certificato del cluster. |
|ClusterX509FindValueSecondary |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare il certificato del cluster. |
|ClusterX509StoreName |stringa, il valore predefinito è "My" |Dinamico|Nome dell'archivio certificati X.509 che contiene il certificato del cluster per proteggere la comunicazione all'interno del cluster. |
|EndApplicationPortRange |Int, valore predefinito: 0 |Statico|Termine (non inclusivo) delle porte di applicazione gestite dal sottosistema di hosting. Obbligatorio se EndpointFilteringEnabled è impostato su true in Hosting. |
|ServerAuthX509FindType |stringa, valore predefinito è "FindByThumbprint" |Dinamico|Indica la modalità di ricerca del certificato del server nell'archivio specificato da ServerAuthX509StoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
|ServerAuthX509FindValue |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare il certificato del server. |
|ServerAuthX509FindValueSecondary |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare il certificato del server. |
|ServerAuthX509StoreName |stringa, il valore predefinito è "My" |Dinamico|Nome dell'archivio certificati X.509 che contiene il certificato server per il servizio di entrata. |
|StartApplicationPortRange |Int, valore predefinito: 0 |Statico|Avvio delle porte di applicazione gestite dal sottosistema di hosting. Obbligatorio se EndpointFilteringEnabled è impostato su true in Hosting. |
|StateTraceInterval |Tempo in secondi, il valore predefinito è 300 |Statico|Specificare l'intervallo di tempo in secondi. L'intervallo per la traccia dello stato di nodo in ogni nodo e dei nodi in FM/FMM. |
|UserRoleClientX509FindType |stringa, valore predefinito è "FindByThumbprint" |Dinamico|Indica la modalità di ricerca del certificato del server nell'archivio specificato da ServerAuthX509StoreName. Valori supportati: FindByThumbprint; FindBySubjectName. |
|UserRoleClientX509FindValue |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare un certificato per il ruolo di utente predefinito FabricClient. |
|UserRoleClientX509FindValueSecondary |stringa, il valore predefinito è "" |Dinamico|Valore di filtro di ricerca usato per individuare un certificato per il ruolo di utente predefinito FabricClient. |
|UserRoleClientX509StoreName |stringa, il valore predefinito è "My" |Dinamico|Nome dell'archivio certificati X.509 che contiene un certificato per il ruolo di utente predefinito FabricClient. |

## <a name="failovermanager"></a>FailoverManager

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|AllowNodeStateRemovedForSeedNode|Bool, valore predefinito: false |Dinamico|Flag per indicare se è consentito rimuovere lo stato del nodo per un nodo di inizializzazione |
|BuildReplicaTimeLimit|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(3600)|Dinamico|Specificare l'intervallo di tempo in secondi. Limite di tempo per la creazione di una replica con stato, dopo il quale verrà avviato un report sull'integrità di avviso. |
|ClusterPauseThreshold|int, valore predefinito: 1|Dinamico|Se il numero di nodi nel sistema scende al di sotto di questo valore, la selezione, il bilanciamento del carico e il failover vengono interrotti. |
|CreateInstanceTimeLimit|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(300)|Dinamico|Specificare l'intervallo di tempo in secondi. Limite di tempo per la creazione di un'istanza senza stato, dopo il quale verrà avviato un report sull'integrità di avviso. |
|ExpectedClusterSize|int, valore predefinito: 1|Dinamico|Durante l'avvio iniziale del cluster, il servizio FM aspetterà che questo numero di nodi segnali lo stato di attivato prima di iniziare a selezionare altri servizi, inclusi i servizi di sistema come quello di denominazione. Aumentando questo valore aumenta il tempo necessario per l'avvio di un cluster, ma si evitano sovraccarichi per i primi nodi, oltre agli ulteriori spostamenti che risulterebbero necessari man mano che vengono portati online altri nodi. Questo valore deve essere impostato in genere su una piccola frazione della dimensione iniziale del cluster. |
|ExpectedNodeDeactivationDuration|Timespan, valore predefinito: Common::TimeSpan::FromSeconds(60.0 \* 30)|Dinamico|Specificare l'intervallo di tempo in secondi. Si tratta della durata prevista per il completamento della disattivazione in un nodo. |
|ExpectedNodeFabricUpgradeDuration|Timespan, valore predefinito: Common::TimeSpan::FromSeconds(60.0 \* 30)|Dinamico|Specificare l'intervallo di tempo in secondi. Si tratta della durata prevista per l'aggiornamento di un nodo durante l'aggiornamento di Windows Fabric. |
|ExpectedReplicaUpgradeDuration|Timespan, valore predefinito: Common::TimeSpan::FromSeconds(60.0 \* 30)|Dinamico|Specificare l'intervallo di tempo in secondi. Si tratta della durata prevista per l'aggiornamento di tutte le repliche in un nodo durante l'aggiornamento dell'applicazione. |
|IsSingletonReplicaMoveAllowedDuringUpgrade|bool, valore predefinito: TRUE|Dinamico|Se impostato su true, sarà consentito lo spostamento delle repliche con una dimensione del set di repliche di destinazione pari a 1 durante l'aggiornamento. |
|MinReplicaSetSize|int, valore predefinito: 3|Non consentito|Questa è la dimensione del set di repliche minima per il servizio FM. Se il numero di repliche attive di FM scende al di sotto di questo valore, il servizio FM rifiuterà le modifiche al cluster almeno fino a quando non viene recuperato il numero minimo di repliche. |
|PlacementConstraints|stringa, il valore predefinito è ""|Non consentito|Eventuali vincoli di selezione per le repliche di Gestione failover. |
|PlacementTimeLimit|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(600)|Dinamico|Specificare l'intervallo di tempo in secondi. Limite di tempo per il raggiungimento del numero di repliche di destinazione, dopo il quale verrà avviato un report sull'integrità di avviso. |
|QuorumLossWaitDuration |Tempo in secondi, valore predefinito: MaxValue |Dinamico|Specificare l'intervallo di tempo in secondi. Durata massima per cui una partizione può essere in stato di perdita del quorum. Se la partizione si trova ancora in questo stato passato questo intervallo di tempo, viene ripristinata considerando come perse le repliche offline. Ciò può comportare una potenziale perdita di dati. |
|ReconfigurationTimeLimit|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(300)|Dinamico|Specificare l'intervallo di tempo in secondi. Limite di tempo per la riconfigurazione, dopo il quale verrà avviato un report sull'integrità di avviso. |
|ReplicaRestartWaitDuration|Timespan, valore predefinito: Common::TimeSpan::FromSeconds(60.0 \* 30)|Non consentito|Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per FMService |
| SeedNodeQuorumAdditionalBufferNodes | int, valore predefinito: 0 | Dinamico | Buffer dei nodi di inizializzazione necessari per l'uso (insieme al quorum dei nodi di inizializzazione) FM consente massimo (totalNumSeedNodes - (seedNodeQuorum + SeedNodeQuorumAdditionalBufferNodes)) nodi di inizializzazione prima di essere portato offline. |
|StandByReplicaKeepDuration|Timespan, valore predefinito: Common::TimeSpan::FromSeconds(3600.0 \* 24 \* 7)|Non consentito|Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per FMService |
|TargetReplicaSetSize|int, valore predefinito: 7|Non consentito|Questo è il numero di destinazione delle repliche di FM che manterrà Windows Fabric. Un numero maggiore comporta una maggiore affidabilità dei dati di FM, con effetti negativi limitati sulle prestazioni. |
|UserMaxStandByReplicaCount |Int, valore predefinito: 1 |Dinamico|Il numero massimo predefinito di repliche in standby che il sistema conserva per i servizi utente. |
|UserReplicaRestartWaitDuration |Tempo in secondi, il valore predefinito è 60.0 \* 30 |Dinamico|Specificare l'intervallo di tempo in secondi. Quando una replica persistente va offline, Windows Fabric attende che torni online per questo intervallo di tempo, passato il quale crea nuove repliche sostitutive, che richiederanno una copia dello stato. |
|UserStandByReplicaKeepDuration |Tempo in secondi, valore predefinito: 3600.0 \* 24 \* 7 |Dinamico|Specificare l'intervallo di tempo in secondi. Quando una replica persistente torna online, potrebbe già essere stata sostituita. Questo timer determina per quanto tempo FM manterrà la replica in standby prima di rimuoverla. |

## <a name="faultanalysisservice"></a>FaultAnalysisService

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|CompletedActionKeepDurationInSeconds | Int, valore predefinito: 604800 |Statico| Tempo di conservazione approssimativo delle azioni in stato terminale. Dipende inoltre StoredActionCleanupIntervalInSeconds, poiché l'operazione di pulizia è eseguita solo in quell'intervallo. 604800 equivale a 7 giorni. |
|DataLossCheckPollIntervalInSeconds|int, valore predefinito: 5|Statico|Tempo che intercorre tra i controlli eseguiti dal sistema durante l'attesa del verificarsi di una perdita di dati. Il numero di controlli del numero di perdite di dati per ogni iterazione interna è dato da DataLossCheckWaitDurationInSeconds diviso per questo parametro. |
|DataLossCheckWaitDurationInSeconds|int, valore predefinito: 25|Statico|La quantità totale di tempo, in secondi, per cui il sistema attenderà che si verifichi una perdita di dati. Questo parametro viene usato internamente quando viene chiamata l'API StartPartitionDataLossAsync(). |
|MinReplicaSetSize |Int, valore predefinito: 0 |Statico|MinReplicaSetSize per FaultAnalysisService. |
|PlacementConstraints | stringa, il valore predefinito è ""|Statico| PlacementConstraints per FaultAnalysisService. |
|QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue |Statico|Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per FaultAnalysisService. |
|ReplicaDropWaitDurationInSeconds|int, valore predefinito: 600|Statico|Questo parametro viene usato quando viene chiamata l'API per la perdita di dati. Controlla il tempo di attesa del sistema per l'eliminazione di una replica dopo una chiamata interna di rimozione della replica. |
|ReplicaRestartWaitDuration |Tempo in secondi, valore predefinito: 60 minuti|Statico|Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per FaultAnalysisService. |
|StandByReplicaKeepDuration| Tempo in secondi, valore predefinito: (60 *24* 7) minuti |Statico|Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per FaultAnalysisService. |
|StoredActionCleanupIntervalInSeconds | Int, valore predefinito: 3600 |Statico|Indica la frequenza di pulizia dell'archivio. Verranno rimosse solo le azioni in stato terminale e completate da un numero di secondi pari almeno a CompletedActionKeepDurationInSeconds. |
|StoredChaosEventCleanupIntervalInSeconds | Int, valore predefinito: 3600 |Statico|Frequenza con cui l'archivio verrà controllato per eseguire eventualmente la pulizia. Se il numero di eventi è superiore a 30.000, la pulizia verrà avviata. |
|TargetReplicaSetSize |Int, valore predefinito: 0 |Statico|NOT_PLATFORM_UNIX_START TargetReplicaSetSize per FaultAnalysisService. |

## <a name="federation"></a>Federazione

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|LeaseDuration |Tempo in secondi, il valore predefinito è 30 |Dinamico|Durata di un lease tra un nodo e gli elementi adiacenti. |
|LeaseDurationAcrossFaultDomain |Tempo in secondi, il valore predefinito è 30 |Dinamico|Durata di un lease tra un nodo e gli elementi adiacenti nei domini di errore. |

## <a name="filestoreservice"></a>FileStoreService

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|AcceptChunkUpload|Bool, valore predefinito: TRUE|Dinamico|Configurazione per determinare se il servizio di archiviazione di file accetta o meno il caricamento di file basato su blocco durante la copia del pacchetto di applicazione. |
|AnonymousAccessEnabled | Bool, valore predefinito: true |Statico|Abilita o disabilita l'accesso anonimo alle condivisioni di FileStoreService. |
|CommonName1Ntlmx509CommonName|stringa, il valore predefinito è ""|Statico| Nome comune dell'archivio del certificato X509 usato per generare HMAC in CommonName1NtlmPasswordSecret quando si usa l'autenticazione NTLM |
|CommonName1Ntlmx509StoreLocation|stringa, il valore predefinito è "LocalMachine"|Statico|Percorso dell'archivio del certificato X509 usato per generare HMAC in CommonName1NtlmPasswordSecret quando si usa l'autenticazione NTLM |
|CommonName1Ntlmx509StoreName|stringa, il valore predefinito è "MY"| Statico|Nome dell'archivio del certificato X509 usato per generare HMAC in CommonName1NtlmPasswordSecret quando si usa l'autenticazione NTLM |
|CommonName2Ntlmx509CommonName|stringa, il valore predefinito è ""|Statico|Nome comune dell'archivio del certificato X509 usato per generare HMAC in CommonName2NtlmPasswordSecret quando si usa l'autenticazione NTLM |
|CommonName2Ntlmx509StoreLocation|stringa, il valore predefinito è "LocalMachine"| Statico|Percorso dell'archivio del certificato X509 usato per generare HMAC in CommonName2NtlmPasswordSecret quando si usa l'autenticazione NTLM |
|CommonName2Ntlmx509StoreName|stringa, il valore predefinito è "MY"|Statico| Nome dell'archivio del certificato X509 usato per generare HMAC in CommonName2NtlmPasswordSecret quando si usa l'autenticazione NTLM |
|CommonNameNtlmPasswordSecret|SecureString, valore predefinito: Common::SecureString("")| Statico|Segreto della password usato come valore di inizializzazione per generare la stessa password quando si usa l'autenticazione NTLM. |
|DiskSpaceHealthReportingIntervalWhenCloseToOutOfDiskSpace |TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(5)|Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di tempo tra il controllo dello spazio su disco per la segnalazione di un evento di integrità quando lo spazio del disco è quasi esaurito. |
|DiskSpaceHealthReportingIntervalWhenEnoughDiskSpace |TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(15)|Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di tempo tra il controllo dello spazio su disco per la segnalazione di un evento di integrità quando lo spazio del disco è sufficiente. |
|EnableImageStoreHealthReporting |bool, valore predefinito: TRUE    |Statico|Configurazione per determinare se il servizio Archivio file deve segnalare la propria integrità. |
|FreeDiskSpaceNotificationSizeInKB|int64, il valore predefinito è 25\*1024 |Dinamico|Dimensioni dello spazio libero su disco al di sotto delle quali può generarsi un avviso di integrità. Il valore minimo di questa configurazione e della configurazione FreeDiskSpaceNotificationThresholdPercentage vengono usati per determinare l'invio di avvisi di integrità. |
|FreeDiskSpaceNotificationThresholdPercentage|double, valore predefinito: 0,02 |Dinamico|Percentuale di spazio disponibile su disco al di sotto della quale può generarsi un avviso di integrità. Il valore minimo di questa configurazione e della configurazione FreeDiskSpaceNotificationInMB vengono usati per determinare l'invio di avvisi di integrità. |
|GenerateV1CommonNameAccount| bool, valore predefinito: TRUE|Statico|Specifica se generare un account con l'algoritmo di generazione V1 del nome utente. A partire dalla versione 6.1 di Service Fabric viene sempre creato un account con generazione V2. L'account V1 è necessario per gli aggiornamenti da/a versioni che non supportano la generazione V2 (prima della versione 6.1).|
|MaxCopyOperationThreads | Uint, valore predefinito: 0 |Dinamico| Il numero massimo di file paralleli che il Replicator secondario può copiare da quello primario. '0' == numero di memorie centrali. |
|MaxFileOperationThreads | Uint, valore predefinito: 100 |Statico| Numero massimo di thread paralleli che possono eseguire FileOperations (copia o spostamento) nel Replicator primario. '0' == numero di memorie centrali. |
|MaxRequestProcessingThreads | Uint, valore predefinito: 200 |Statico|Numero massimo di thread paralleli che possono elaborare richieste nel Replicator primario. '0' == numero di memorie centrali. |
|MaxSecondaryFileCopyFailureThreshold | Uint, valore predefinito: 25|Dinamico|Numero massimo di tentativi di copia di file nel Replicator secondario prima di interrompere l'operazione. |
|MaxStoreOperations | Uint, valore predefinito: 4096 |Statico|Numero massimo di transazioni di archivi paralleli eseguibili nel Replicator primario. '0' == numero di memorie centrali. |
|NamingOperationTimeout |Tempo in secondi, il valore predefinito è 60 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout per eseguire un'operazione di denominazione. |
|PrimaryAccountNTLMPasswordSecret | SecureString, valore predefinito: vuoto |Statico| Il segreto della password usato come valore di inizializzazione per generare la stessa password quando si usa l'autenticazione NTLM. |
|PrimaryAccountNTLMX509StoreLocation | stringa, il valore predefinito è "LocalMachine"|Statico| Il percorso dell'archivio del certificato X509 usato per generare HMAC in PrimaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
|PrimaryAccountNTLMX509StoreName | stringa, il valore predefinito è "MY"|Statico| Il nome dell'archivio del certificato X509 usato per generare HMAC in PrimaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
|PrimaryAccountNTLMX509Thumbprint | stringa, il valore predefinito è ""|Statico|L'identificazione personale dell'archivio del certificato X509 usata per generare HMAC in PrimaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
|PrimaryAccountType | stringa, il valore predefinito è "" |Statico|L'AccountType primario dell'entità per l'ACL delle condivisioni di FileStoreService. |
|PrimaryAccountUserName | stringa, il valore predefinito è "" |Statico|Il nome utente dell'account primario dell'entità per l'ACL delle condivisioni di FileStoreService. |
|PrimaryAccountUserPassword | SecureString, valore predefinito: vuoto |Statico|La password dell'account primario dell'entità per l'ACL delle condivisioni di FileStoreService. |
|QueryOperationTimeout | Tempo in secondi, il valore predefinito è 60 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout per eseguire un'operazione di query. |
|SecondaryAccountNTLMPasswordSecret | SecureString, valore predefinito: vuoto |Statico| Il segreto della password usato come valore di inizializzazione per generare la stessa password quando si usa l'autenticazione NTLM. |
|SecondaryAccountNTLMX509StoreLocation | stringa, il valore predefinito è "LocalMachine" |Statico|Il percorso dell'archivio del certificato X509 usato per generare HMAC in SecondaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
|SecondaryAccountNTLMX509StoreName | stringa, il valore predefinito è "MY" |Statico|Il nome dell'archivio del certificato X509 usato per generare HMAC in SecondaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
|SecondaryAccountNTLMX509Thumbprint | stringa, il valore predefinito è ""| Statico|L'identificazione personale dell'archivio del certificato X509 usata per generare HMAC in SecondaryAccountNTLMPasswordSecret quando si usa l'autenticazione NTLM. |
|SecondaryAccountType | stringa, il valore predefinito è ""|Statico| L'AccountType secondario dell'entità per l'ACL delle condivisioni di FileStoreService. |
|SecondaryAccountUserName | stringa, il valore predefinito è ""| Statico|Il nome utente dell'account secondario dell'entità per l'ACL delle condivisioni di FileStoreService. |
|SecondaryAccountUserPassword | SecureString, valore predefinito: vuoto |Statico|La password dell'account secondario dell'entità per l'ACL delle condivisioni di FileStoreService. |
|SecondaryFileCopyRetryDelayMilliseconds|uint, valore predefinito: 500|Dinamico|Ritardo tra i tentativi di copia del file (in millisecondi).|
|UseChunkContentInTransportMessage|bool, valore predefinito: TRUE|Dinamico|Flag per l'uso della nuova versione del protocollo di caricamento introdotta nella versione 6.4. Questa versione del protocollo usa il trasporto di Service Fabric per caricare file nell'archivio immagini, offrendo prestazioni migliori rispetto al protocollo SMB usato nelle versioni precedenti. |

## <a name="healthmanager"></a>HealthManager

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|EnableApplicationTypeHealthEvaluation |Bool, valore predefinito: false |Statico|Criteri di valutazione dell'integrità del cluster: abilitare il parametro per la valutazione dell'integrità del tipo di applicazione. |
|MaxSuggestedNumberOfEntityHealthReports|Int, valore predefinito: 100 |Dinamico|Numero massimo di report sull'integrità che un'entità può avere prima di generare problemi relativi alla logica di segnalazione dell'integrità del watchdog. Ogni entità di integrità deve avere un numero relativamente ridotto di report sull'integrità. Se il conteggio dei report supera questo numero, è possibile che ci siano problemi con l'implementazione del watchdog. Quando viene valutata, un'entità con un numero eccessivo di report viene contrassegnata tramite un rapporto di integrità di avviso. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ConsiderWarningAsError |Bool, valore predefinito: false |Statico|Criteri di valutazione dell'integrità del cluster: gli avvisi vengano considerati come errori. |
|MaxPercentUnhealthyApplications | Int, valore predefinito: 0 |Statico|Criteri di valutazione dell'integrità del cluster: la percentuale massima di applicazioni non integre consentita per indicare come integro il cluster stesso. |
|MaxPercentUnhealthyNodes | Int, valore predefinito: 0 |Statico|Criteri di valutazione dell'integrità del cluster: la percentuale massima di nodi non integri consentita per indicare come integro il cluster stesso. |

## <a name="healthmanagerclusterupgradehealthpolicy"></a>HealthManager/ClusterUpgradeHealthPolicy

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|MaxPercentDeltaUnhealthyNodes|int, valore predefinito: 10|Statico|Criteri di valutazione dell'integrità dell'aggiornamento del cluster: la percentuale massima di nodi non integri differenziali consentita per considerare integro il cluster stesso. |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes|int, valore predefinito: 15|Statico|Criteri di valutazione dell'integrità dell'aggiornamento del cluster: la percentuale massima di nodi non integri differenziali consentita in un dominio di aggiornamento per considerare integro il cluster stesso. |

## <a name="hosting"></a>Hosting

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ActivationMaxFailureCount |Numero intero, il valore predefinito è 10 |Dinamico|Numero di volte che il sistema ritenta l'attivazione non riuscita prima di rinunciare |
|ActivationMaxRetryInterval |Tempo in secondi, il valore predefinito è 300 |Dinamico|In ogni caso di errore di attivazione continua, il sistema ritenta l'attivazione fino al massimo valore definito in ActivationMaxFailureCount. ActivationMaxRetryInterval specifica l'intervallo di tempo di attesa prima di un nuovo tentativo dopo ogni errore di attivazione |
|ActivationRetryBackoffInterval |Tempo in secondi, il valore predefinito è 5 |Dinamico|Intervallo di backoff per ogni errore di attivazione; in caso di errore di attivazione continua, il sistema ritenta l'attivazione fino al massimo valore definito in MaxActivationFailureCount. L'intervallo tra tentativi è il prodotto dell'errore di attivazione continua e dell'intervallo di backoff di attivazione. |
|ActivationTimeout| TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(180)|Dinamico| Specificare l'intervallo di tempo in secondi. Timeout per l'attivazione, la disattivazione e l'aggiornamento dell'applicazione. |
|ApplicationHostCloseTimeout| TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico| Specificare l'intervallo di tempo in secondi. Quando viene rilevata l'uscita dall'infrastruttura in processi ad attivazione automatica, FabricRuntime chiude tutte le repliche nel processo host (applicationhost) dell'utente. Questo è il timeout per l'operazione di chiusura. |
| CnsNetworkPluginCnmUrlPort | wstring, valore predefinito: L"48080" | Statico | Porta URL dell'API CNM di Azure |
| CnsNetworkPluginCnsUrlPort | wstring, valore predefinito: L"10090" | Statico | Porta URL di Azure CNS |
|ContainerServiceArguments|stringa, valore predefinito: "-H localhost:2375 -H npipe://"|Statico|Service Fabric gestisce il daemon Docker (tranne che nei computer client Windows come Windows 10). Questa configurazione consente all'utente di specificare argomenti personalizzati che devono essere passati al daemon Docker all'avvio. Quando vengono specificati argomenti personalizzati, Service Fabric non passa altri argomenti al motore Docker, ad eccezione dell'argomento "--pidfile". Di conseguenza, gli utenti non devono specificare l'argomento "-pidfile" come parte dei propri argomenti personalizzati. Gli argomenti personalizzati devono inoltre garantire che il daemon Docker sia in ascolto sulla named pipe predefinita in Windows (o sul socket di dominio Unix in Linux) perché Service Fabric possa comunicare con il daemon.|
|ContainerServiceLogFileMaxSizeInKb|int, valore predefinito: 32768|Statico|Dimensioni massime del file di log generato dai contenitori Docker.  Solo Windows.|
|ContainerImageDownloadTimeout|int, numero di secondi, valore predefinito: 1200 (20 minuti)|Dinamico|Numero di secondi prima del timeout del download dell'immagine.|
|ContainerImagesToSkip|stringa, nomi di immagine separati dal carattere di linea verticale, il valore predefinito è ""|Statico|Nome di una o più immagini del contenitore che non devono essere eliminate.  Usato con il parametro PruneContainerImages.|
|ContainerServiceLogFileNamePrefix|string, valore predefinito: "sfcontainerlogs"|Statico|Prefisso del nome per i file di log generati dai contenitori Docker.  Solo Windows.|
|ContainerServiceLogFileRetentionCount|int, valore predefinito: 10|Statico|Numero di file di log generati dai contenitori Docker prima che i file di log vengano sovrascritti.  Solo Windows.|
|CreateFabricRuntimeTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico| Specificare l'intervallo di tempo in secondi. Valore di timeout per la chiamata di FabricCreateRuntime. |
|DefaultContainerRepositoryAccountName|stringa, il valore predefinito è ""|Statico|Credenziali predefinite usate al posto delle credenziali specificate nel file ApplicationManifest.xml |
|DefaultContainerRepositoryPassword|stringa, il valore predefinito è ""|Statico|Credenziali predefinite della password usate al posto delle credenziali specificate nel file ApplicationManifest.xml|
|DefaultContainerRepositoryPasswordType|stringa, il valore predefinito è ""|Statico|Quando non è una stringa vuota, il valore può essere "Encrypted" o "SecretsStoreRef".|
|DefaultDnsSearchSuffixEmpty|bool, valore predefinito: FALSE|Statico|Per impostazione predefinita, il nome del servizio viene aggiunto al nome DNS SF per i servizi contenitore. Questa funzionalità arresta questo comportamento in modo che per impostazione predefinita non venga aggiunto nulla al nome DNS SF nel percorso di risoluzione.|
|DeploymentMaxFailureCount|int, valore predefinito: 20| Dinamico|Verranno eseguiti altri tentativi di distribuzione dell'applicazione per le volte specificate in DeploymentMaxFailureCount prima di considerare non riuscita la distribuzione dell'applicazione nel nodo.| 
|DeploymentMaxRetryInterval| TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(3600)|Dinamico| Specificare l'intervallo di tempo in secondi. Intervallo tra tentativi massimo per la distribuzione. In caso di errori continui, l'intervallo tra i tentativi viene calcolato come segue: Min( DeploymentMaxRetryInterval; conteggio errori continui * DeploymentRetryBackoffInterval). |
|DeploymentRetryBackoffInterval| TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(10)|Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di backoff per l'errore di distribuzione. Per ogni errore di distribuzione continuo, il sistema ritenterà la distribuzione per il massimo di volte specificato in MaxDeploymentFailureCount. L'intervallo tra tentativi è un prodotto degli errori di distribuzione continui e dell'intervallo di backoff della distribuzione. |
|DisableContainers|bool, valore predefinito: FALSE|Statico|Configurazione per la disabilitazione di contenitori - usata al posto di DisableContainerServiceStartOnContainerActivatorOpen, che è una configurazione deprecata |
|DisableDockerRequestRetry|bool, valore predefinito: FALSE |Dinamico| Per impostazione predefinita, Service Fabric comunica con DD (Docker Daemon) con un timeout pari a DockerRequestTimeout per ogni richiesta HTTP inviata al daemon. Se DD non risponde entro questo periodo di tempo e l'operazione di più alto livello ha ancora tempo rimanente, Service Fabric invia nuovamente la richiesta.  Con un contenitore Hyper-V, DD richiede talvolta molto più tempo per attivare il contenitore o per disattivarlo. In casi come questo si verifica il timeout della richiesta DD dalla prospettiva di Service Fabric e Service Fabric ritenta l'operazione. Talvolta si ha l'impressione che venga esercitata una maggiore pressione su DD. Questa configurazione consente di disabilitare la ripetizione del tentativo e attendere la risposta di DD. |
|DnsServerListTwoIps | Bool, valore predefinito: false | Statico | Questi flag aggiungono due volte il server DNS locale per attenuare i problemi di risoluzione intermittenti. |
| DoNotInjectLocalDnsServer | bool, valore predefinito: FALSE | Statico | Impedisce al runtime di inserire l'indirizzo IP locale come server DNS per i contenitori. |
|EnableActivateNoWindow| bool, valore predefinito: FALSE|Dinamico| Il processo attivato viene creato in background senza alcuna console. |
|EnableContainerServiceDebugMode|bool, valore predefinito: TRUE|Statico|Abilita/disabilita la registrazione per i contenitori Docker.  Solo Windows.|
|EnableDockerHealthCheckIntegration|bool, valore predefinito: TRUE|Statico|Abilita l'integrazione di eventi di docker HEALTHCHECK nel report relativo all'integrità del sistema di Service Fabric. |
|EnableProcessDebugging|bool, valore predefinito: FALSE|Dinamico| Abilita l'avvio degli host dell'applicazione nel debugger. |
|EndpointProviderEnabled| bool, valore predefinito: FALSE|Statico| Abilita la gestione delle risorse degli endpoint tramite l'infrastruttura. Richiede la specifica di un intervallo di porte dell'applicazione iniziale e finale in FabricNode. |
|FabricContainerAppsEnabled| bool, valore predefinito: FALSE|Statico| |
|FirewallPolicyEnabled|bool, valore predefinito: FALSE|Statico| Abilita l'apertura delle porte del firewall per le risorse degli endpoint con porte esplicite specificate in ServiceManifest. |
|GetCodePackageActivationContextTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico|Specificare l'intervallo di tempo in secondi. Valore di timeout per le chiamate CodePackageActivationContext. Non applicabile ai servizi ad hoc. |
|GovernOnlyMainMemoryForProcesses|bool, valore predefinito: FALSE|Statico|Il comportamento predefinito di governance delle risorse consiste nell'applicare il limite specificato in MemoryInMB per la quantità di memoria totale (RAM + swapping) usata dal processo. Se viene superato il limite, il processo riceverà l'eccezione OutOfMemory. Se questo parametro è impostato su true, il limite verrà applicato solo alla quantità di memoria RAM che verrà usata da un processo. Se questo limite viene superato e questa impostazione è true, il sistema operativo eseguirà lo swapping della memoria principale su disco. |
|IPProviderEnabled|bool, valore predefinito: FALSE|Statico|Abilita la gestione degli indirizzi IP. |
|IsDefaultContainerRepositoryPasswordEncrypted|bool, valore predefinito: FALSE|Statico|Indica se DefaultContainerRepositoryPassword è crittografata o meno.|
|LinuxExternalExecutablePath|stringa, valore predefinito: "/usr/bin/" |Statico|La directory principale dei comandi eseguibili esterni nel nodo.|
|NTLMAuthenticationEnabled|bool, valore predefinito: FALSE|Statico| Abilita il supporto per l'uso di NTLM da parte dei pacchetti di codice eseguiti con account di altri utenti, in modo che i processi tra computer diversi possano comunicare in modo sicuro. |
|NTLMAuthenticationPasswordSecret|SecureString, valore predefinito: Common::SecureString("")|Statico|Hash crittografato usato per generare la password per gli utenti NTLM. Deve essere impostato se NTLMAuthenticationEnabled è true. Convalidato dal deployer. |
|NTLMSecurityUsersByX509CommonNamesRefreshInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(3)|Dinamico|Specificare l'intervallo di tempo in secondi. Impostazioni specifiche dell'ambiente. Intervallo periodico in base al quale l'hosting cerca nuovi certificati da usare per la configurazione di NTLM per FileStoreService. |
|NTLMSecurityUsersByX509CommonNamesRefreshTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(4)|Dinamico| Specificare l'intervallo di tempo in secondi. Timeout per la configurazione di utenti NTLM usando nomi comuni del certificato. Gli utenti NTLM sono necessari per le condivisioni FileStoreService. |
|PruneContainerImages|bool, valore predefinito: FALSE|Dinamico| Rimuovere dai nodi le immagini del contenitore dell'applicazione inutilizzate. Quando viene annullata la registrazione di un'istanza di ApplicationType dal cluster di Service Fabric, le immagini del contenitore che sono state usate dall'applicazione verranno rimosse dai nodi in cui sono state scaricate da Service Fabric. L'eliminazione viene eseguita ogni ora e pertanto la rimozione delle immagini dal cluster può richiedere fino a un'ora, oltre al tempo necessario per eliminare l'immagine.<br>Service Fabric non scarica né rimuove mai le immagini che non sono correlate a un'applicazione.  Le immagini non correlate che sono state scaricate manualmente o in altro modo devono essere rimosse in modo esplicito.<br>Le immagini che non devono essere eliminate possono essere specificate nel parametro ContainerImagesToSkip.| 
|RegisterCodePackageHostTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico| Specificare l'intervallo di tempo in secondi. Valore di timeout per la chiamata di sincronizzazione di FabricRegisterCodePackageHost. Applicabile solo per gli host di applicazioni con più pacchetti di codice come FWP. |
|RequestTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(30)|Dinamico| Specificare l'intervallo di tempo in secondi. Rappresenta il timeout per la comunicazione tra l'host dell'applicazione dell'utente e il processo di Fabric per varie operazioni correlate all'hosting, come la registrazione di factory e la registrazione di runtime. |
|RunAsPolicyEnabled| bool, valore predefinito: FALSE|Statico| Abilita l'esecuzione di pacchetti di codice con l'account utente locale, anziché con l'account utente usato per l'esecuzione del processo dell'infrastruttura. Per abilitare questo criterio è necessario che Fabric sia eseguito con l'account SYSTEM o con un account utente con SeAssignPrimaryTokenPrivilege. |
|ServiceFactoryRegistrationTimeout| TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico|Specificare l'intervallo di tempo in secondi. Valore di timeout per la chiamata Register(Stateless/Stateful)ServiceFactory di sincronizzazione |
|ServiceTypeDisableFailureThreshold |Numero intero, il valore predefinito è 1 |Dinamico|Si tratta della soglia per il conteggio degli errori superata la quale a FailoverManager (FM) viene notificato di disabilitare il tipo di servizio sul nodo e di provare il posizionamento su un altro nodo. |
|ServiceTypeDisableGraceInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(30)|Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di tempo dopo il quale il tipo di servizio può essere disabilitato |
|ServiceTypeRegistrationTimeout |Tempo in secondi, il valore predefinito è 300 |Dinamico|Tempo massimo consentito per la registrazione di ServiceType nell'infrastruttura |
|UseContainerServiceArguments|bool, valore predefinito: TRUE|Statico|Questa configurazione indica all'host di ignorare il passaggio di argomenti (specificato nella configurazione ContainerServiceArguments) al daemon Docker.|

## <a name="httpgateway"></a>HttpGateway

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ActiveListeners |Uint, valore predefinito: 50 |Statico| Numero di letture da pubblicare nella coda del server http. Consente di controllare il numero di richieste simultanee che possono essere espletate da HttpGateway. |
|HttpGatewayHealthReportSendInterval |Tempo in secondi, il valore predefinito è 30 |Statico|Specificare l'intervallo di tempo in secondi. Intervallo dopo il quale il gateway HTTP invia i report sull'integrità accumulati a Health Manager. |
|HttpStrictTransportSecurityHeader|String, valore predefinito: ""|Dinamico| Specificare il valore dell'intestazione HTTP Strict Transport Security da includere in ogni risposta inviata da HttpGateway. Quando è impostata su una stringa vuota, questa intestazione non verrà inclusa nella risposta del gateway.|
|IsEnabled|Bool, valore predefinito: false |Statico| Abilita/disabilita HttpGateway. HttpGateway è disabilitato per impostazione predefinita. |
|MaxEntityBodySize |Uint, valore predefinito: 4194304 |Dinamico|Indica la dimensione massima del corpo prevista da una richiesta HTTP. Il valore predefinito è 4 MB. Httpgateway non porterà a termine una richiesta se questa ha un corpo di dimensioni superiori al valore indicato. La dimensione minima dei blocchi di lettura è di 4096 byte. Il valore deve pertanto essere >= 4096. |

## <a name="imagestoreservice"></a>ImageStoreService

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|Attivato |Bool, valore predefinito: false |Statico|Flag Enabled per ImageStoreService. Valore predefinito: false |
|MinReplicaSetSize | Int, valore predefinito: 3 |Statico|MinReplicaSetSize per ImageStoreService. |
|PlacementConstraints | stringa, il valore predefinito è "" |Statico| PlacementConstraints per ImageStoreService. |
|QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue |Statico| Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per ImageStoreService. |
|ReplicaRestartWaitDuration | Tempo in secondi, il valore predefinito è 60.0 \* 30 |Statico|Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per ImageStoreService. |
|StandByReplicaKeepDuration | Tempo in secondi, il valore predefinito è 3600.0 \* 2 |Statico| Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per ImageStoreService. |
|TargetReplicaSetSize | Int, valore predefinito: 7 |Statico|TargetReplicaSetSize per ImageStoreService. |

## <a name="ktllogger"></a>KtlLogger

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|AutomaticMemoryConfiguration |Int, valore predefinito: 1 |Dinamico|Flag che indica se le impostazioni di memoria devono essere configurate in modo dinamico e automatico. Se il valore è 0, le impostazioni vengono usate direttamente e non vengono modificate in base alle condizioni di sistema. Se il valore è 1, le impostazioni vengono configurate automaticamente e possono cambiare in base alle condizioni di sistema. |
|MaximumDestagingWriteOutstandingInKB | Int, valore predefinito: 0 |Dinamico|Il numero di KB per cui il log condiviso può superare in dimensioni il log dedicato. Usare 0 per non avere limiti.
|SharedLogId |stringa, il valore predefinito è "" |Statico|GUID univoco per il contenitore di log condivisi. Usare "" se si usa il percorso predefinito nella radice dati di Service Fabric. |
|SharedLogPath |stringa, il valore predefinito è "" |Statico|Percorso e nome del file in cui inserire il contenitore del log condiviso. Usare "" per indicare il percorso predefinito nella radice dati di Service Fabric. |
|SharedLogSizeInMB |Int, valore predefinito: 8192 |Statico|Il numero di MB da allocare nel contenitore di log condivisi. |
|SharedLogThrottleLimitInPercentUsed|int, valore predefinito: 0 | Statico | Percentuale di utilizzo del log condiviso che causerà una limitazione delle richieste. Il valore deve essere compreso tra 0 e 100. Un valore pari a 0 implica l'uso del valore percentuale predefinito. Un valore pari a 100 non implica alcuna limitazione delle richieste. Un valore compreso tra 1 e 99 specifica la percentuale di utilizzo del log oltre la quale si verificherà la limitazione delle richieste. Se ad esempio il log condiviso ha una dimensione di 10 GB e il valore è 90, la limitazione delle richieste avverrà non appena saranno in uso 9 GB. È consigliabile usare il valore predefinito.|
|WriteBufferMemoryPoolMaximumInKB | Int, valore predefinito: 0 |Dinamico|Il numero di KB raggiungibile dal pool di memoria buffer in scrittura. Usare 0 per non avere limiti. |
|WriteBufferMemoryPoolMinimumInKB |Int, valore predefinito: 8388608 |Dinamico|Il numero di KB da allocare inizialmente per il pool di memoria buffer in scrittura. Usare 0 per non avere limiti. L'impostazione predefinita deve essere coerente con il parametro SharedLogSizeInMB di seguito. |

## <a name="managedidentitytokenservice"></a>ManagedIdentityTokenService
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|IsEnabled|bool, valore predefinito: FALSE|Statico|Flag che controlla la presenza e lo stato del servizio token di identità gestita nel cluster. Si tratta di un prerequisito per l'utilizzo della funzionalità di identità gestita delle applicazioni Service Fabric.|

## <a name="management"></a>Gestione

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|AutomaticUnprovisionInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(5)|Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di pulizia consentito per l'annullamento della registrazione del tipo di applicazione durante la pulizia automatica dei tipi di applicazione.|
|AzureStorageMaxConnections | Int, valore predefinito: 5000 |Dinamico|Il numero massimo di connessioni simultanee all'archiviazione di Azure. |
|AzureStorageMaxWorkerThreads | Int, valore predefinito: 25 |Dinamico|Il numero massimo di thread di ruoli di lavoro in parallelo. |
|AzureStorageOperationTimeout | Tempo in secondi, valore predefinito: 6000 |Dinamico|Specificare l'intervallo di tempo in secondi. Timeout per il completamento dell'operazione xstore. |
|CleanupApplicationPackageOnProvisionSuccess|bool, valore predefinito: true |Dinamico|Abilita o disabilita la pulizia automatica del pacchetto dell'applicazione quando il provisioning ha esito positivo.
|CleanupUnusedApplicationTypes|Bool, valore predefinito: false |Dinamico|Questa configurazione, se abilitata, consente di annullare automaticamente la registrazione delle versioni dei tipi di applicazioni inutilizzate ignorando le ultime tre versioni inutilizzate, riducendo così lo spazio su disco occupato dall'archivio immagini. La pulizia automatica verrà attivata alla fine di un provisioning corretto per il tipo di app specifico e verrà eseguita periodicamente una volta al giorno per tutti i tipi di applicazione. Il numero di versioni non usate da ignorare è configurabile con il parametro "MaxUnusedAppTypeVersionsToKeep". <br/> *La procedura consigliata consiste nell'usare `true` .*
|DisableChecksumValidation | Bool, valore predefinito: false |Statico| Questa configurazione consente di abilitare o disabilitare la convalida di checksum durante il provisioning dell'applicazione. |
|DisableServerSideCopy | Bool, valore predefinito: false |Statico|Questa configurazione consente di abilitare o disabilitare la copia sul lato server del pacchetto dell'applicazione in ImageStore durante il provisioning dell'applicazione. |
|ImageCachingEnabled | Bool, valore predefinito: true |Statico|Questa configurazione consente di abilitare o disabilitare la memorizzazione nella cache. |
|ImageStoreConnectionString |SecureString |Statico|Stringa di connessione per la radice di ImageStore. |
|ImageStoreMinimumTransferBPS | Int, valore predefinito: 1024 |Dinamico|La velocità di trasferimento minima tra il cluster e ImageStore. Questo valore viene usato per determinare il timeout durante l'accesso a ImageStore esterno. Modificarlo solo in caso di latenza elevata tra il cluster e ImageStore per dare più tempo al cluster di scaricare da ImageStore esterno. |
|MaxUnusedAppTypeVersionsToKeep | Int, valore predefinito: 3 |Dinamico|Questa configurazione definisce il numero di versioni dei tipi di applicazioni non utilizzate da ignorare per la pulizia. Questo parametro è applicabile solo se il parametro CleanupUnusedApplicationTypes è abilitato. <br/>*La procedura consigliata generale consiste nell'usare il valore predefinito ( `3` ). I valori minori di 1 non sono validi.*|


## <a name="metricactivitythresholds"></a>MetricActivityThresholds
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyIntegerValueMap, valore predefinito: None|Dinamico|Determina il set di MetricActivityThresholds per le metriche nel cluster. Il bilanciamento funzionerà se maxNodeLoad è maggiore di MetricActivityThresholds. Per le metriche di deframmentazione definisce la quantità di carico raggiunta la quale o al di sotto della quale Service Fabric considererà il nodo vuoto. |

## <a name="metricbalancingthresholds"></a>MetricActivityThresholds
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, valore predefinito: None|Dinamico|Determina il set di MetricBalancingThresholds per le metriche nel cluster. Il bilanciamento funzionerà se maxNodeLoad/minNodeLoad è maggiore di MetricBalancingThresholds. La deframmentazione funzionerà se maxNodeLoad/minNodeLoad in almeno un dominio di errore o dominio di aggiornamento è minore di MetricBalancingThresholds. |

## <a name="metricloadstickinessforswap"></a>MetricLoadStickinessForSwap
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, valore predefinito: None|Dinamico|Determina la parte del carico che rimane associata alla replica quando viene scambiata, accetta un valore compreso tra 0 (il carico non è associato alla replica) e 1 (il carico rimane associato alla replica, impostazione predefinita) |

## <a name="namingservice"></a>NamingService

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|GatewayServiceDescriptionCacheLimit |Int, valore predefinito: 0 |Statico|Numero massimo di voci conservate nella cache della descrizione del servizio LRU nell'archivio di Naming Gateway. Impostarlo su 0 per non avere limiti. |
|MaxClientConnections |Int, valore predefinito: 1000 |Dinamico|Numero massimo di connessioni client consentito per gateway. |
|MaxFileOperationTimeout |Tempo in secondi, il valore predefinito è 30 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout massimo consentito per l'operazione del servizio di un archivio file. Le richieste che specificano un timeout maggiore verranno respinte. |
|MaxIndexedEmptyPartitions |Int, valore predefinito: 1000 |Dinamico|Numero massimo di partizioni vuote che rimarranno indicizzate nella cache di notifica per la sincronizzazione dei client in fase di riconnessione. Tutte le partizioni vuote oltre questo numero verranno rimosse dall'indice in ordine crescente di versione. Riconnettendo i client sarà comunque possibile sincronizzare e ricevere gli aggiornamenti saltati delle partizioni vuote, ma il protocollo di sincronizzazione sarà più oneroso. |
|MaxMessageSize |Int, valore predefinito: 4\*1024\*1024 |Statico|Dimensioni massime dei messaggi di comunicazioni del nodo client quando si usa la denominazione. Attenuazione di attacchi DOS, valore predefinito: 4 MB. |
|MaxNamingServiceHealthReports | Int, valore predefinito: 10 |Dinamico|Numero massimo di operazioni lente non integre di cui il servizio Naming Service esegue globalmente il report. Se il valore è 0, verranno incluse tutte le operazioni lente. |
|MaxOperationTimeout |Tempo in secondi, valore predefinito: 600 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout massimo consentito per le operazioni client. Le richieste che specificano un timeout maggiore verranno respinte. |
|MaxOutstandingNotificationsPerClient |Int, valore predefinito: 1000 |Dinamico|Numero massimo di notifiche in sospeso prima della chiusura forzata di una registrazione cliente da parte del gateway. |
|MinReplicaSetSize | Int, valore predefinito: 3 |Non consentito| Il numero minimo di repliche di Naming Service in cui scrivere per completare un aggiornamento. Se nel sistema sono presenti meno repliche rispetto a questo valore, Reliability System non effettua aggiornamenti dell'archivio Naming Service fino a quando le repliche non vengono ripristinate. Questo valore non deve mai essere maggiore di TargetReplicaSetSize. |
|PartitionCount |Int, valore predefinito: 3 |Non consentito|Numero di partizioni dell'archivio Naming Service da creare. Ogni partizione possiede una chiave di partizione singola che corrisponde al relativo indice. Esistono quindi chiavi di partizione [0; PartitionCount]. Aumentando il numero di partizioni Naming Service, verrà aumentata anche la scalabilità delle relative operazioni riducendo la quantità media di dati presenti in un dato set di repliche di riserva. Tutto questo comporterà però un maggiore uso di risorse, dato che devono essere conservate repliche di servizio pari a PartitionCount*ReplicaSetSize.|
|PlacementConstraints | stringa, il valore predefinito è "" |Non consentito| Vincolo di posizionamento per il Naming Service. |
|QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue |Non consentito| Specificare l'intervallo di tempo in secondi. Quando un Naming Service entra in uno stato di perdita del quorum, si avvia questo timer. Una volta scaduto, FM considererà perse le repliche offline e tenterà di ripristinare il quorum. Questa azione può comportare la perdita di dati. |
|RepairInterval | Tempo in secondi, valore predefinito: 5 |Statico| Specificare l'intervallo di tempo in secondi. Intervallo in cui verrà avviata la correzione delle incoerenze di denominazione tra il proprietario dell'autorità e il proprietario del nome. |
|ReplicaRestartWaitDuration | Tempo in secondi, valore predefinito: (60.0 * 30)|Non consentito| Specificare l'intervallo di tempo in secondi. Quando una replica di Naming Service si arresta, si avvia questo timer. Alla scadenza FM inizierà a sostituire le repliche non attive; queste non vengono ancora considerate perse. |
|ServiceDescriptionCacheLimit | Int, valore predefinito: 0 |Statico| Numero massimo di voci conservate nella cache della descrizione del servizio LRU nell'archivio di Naming Service. Impostarlo su 0 per non avere limiti. |
|ServiceNotificationTimeout |Tempo in secondi, il valore predefinito è 30 |Dinamico|Specificare l'intervallo di tempo in secondi. Il timeout usato per il recapito delle notifiche di servizio al client. |
|StandByReplicaKeepDuration | Tempo in secondi, valore predefinito: è 3600.0 * 2 |Non consentito| Specificare l'intervallo di tempo in secondi. Quando una replica per Naming Service torna online, potrebbe già essere stata sostituita. Questo timer determina per quanto tempo FM manterrà la replica in standby prima di rimuoverla. |
|TargetReplicaSetSize |Int, valore predefinito: 7 |Non consentito|Il numero di set di repliche per ogni partizione dell'archivio di Naming Service. L'aumento del numero di set di repliche rafforzerà l'affidabilità delle informazioni nell'archivio di Naming Service, riducendo i rischi di perdita di tali informazioni in caso di errori nei nodi. Tutto ciò comporterà tuttavia un carico maggiore per Windows Fabric, nonché un aumento del tempo necessario per aggiornare i dati di denominazione.|

## <a name="nodebufferpercentage"></a>NodeBufferPercentage
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|KeyDoubleValueMap, valore predefinito: None|Dinamico|Percentuale di capacità del nodo per ogni nome di metrica. Usato come buffer per mantenere spazio libero in un nodo per il caso di failover. |

## <a name="nodecapacities"></a>NodeCapacities

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup |NodeCapacityCollectionMap |Statico|Una raccolta di capacità dei nodi per diverse metriche. |

## <a name="nodedomainids"></a>NodeDomainIds

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup |NodeFaultDomainIdCollection |Statico|Descrive il dominio di errore a cui appartiene un nodo. Il dominio di errore viene definito tramite un URI che descrive la posizione del nodo nel data center.  Gli URI dei domini di errore sono nel formato fd:/fd/ seguito da un segmento di percorso URI.|
|UpgradeDomainId |stringa, il valore predefinito è "" |Statico|Descrivi il dominio di aggiornamento a cui appartiene un nodo. |

## <a name="nodeproperties"></a>NodeProperties

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup |NodePropertyCollectionMap |Statico|Una raccolta di coppie di stringhe chiave-valore per le proprietà del nodo. |

## <a name="paas"></a>PaaS

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ClusterId |stringa, il valore predefinito è "" |Non consentito|Archivio certificati X.509 usato da Service Fabric per la protezione della configurazione. |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|Counters |string | Dinamico |Elenco delimitato da virgole dei contatori delle prestazioni da raccogliere. |
|IsEnabled |Bool, valore predefinito: true | Dinamico |Il flag indica se è abilitata la raccolta dei contatori delle prestazioni nel nodo locale. |
|MaxCounterBinaryFileSizeInMB |Int, valore predefinito: 1 | Dinamico |Dimensione massima (in MB) per ogni file binario del contatore delle prestazioni. |
|NewCounterBinaryFileCreationIntervalInMinutes |Int, valore predefinito: 10 | Dinamico |Intervallo massimo (in secondi) dopo il quale viene creato un nuovo file binario di contatore delle prestazioni. |
|SamplingIntervalInSeconds |Int, valore predefinito: 60 | Dinamico |Intervallo di campionamento per i contatori delle prestazioni che vengono raccolti. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|AffinityConstraintPriority | Int, valore predefinito: 0 | Dinamico|Determina la priorità del vincolo di affinità: 0: alta; 1: bassa; numero negativo: ignorare. |
|ApplicationCapacityConstraintPriority | Int, valore predefinito: 0 | Dinamico|Determina la priorità del vincolo di capacità: 0: alta; 1: bassa; numero negativo: ignorare. |
|AutoDetectAvailableResources|bool, valore predefinito: TRUE|Statico|Questa configurazione attiverà il rilevamento automatico delle risorse disponibili nel nodo (CPU e memoria). Quando questa configurazione è impostata su true, verranno lette le capacità reali che verranno corrette se l'utente ha specificato capacità del nodo non corrette o non le ha definite affatto. Se questo parametro di configurazione viene impostato su false, si terrà traccia di un avviso relativo alla specifica di capacità del nodo non corrette da parte dell'utente, ma tali capacità non verranno corrette. Questo significa che verranno presupposte capacità illimitate se l'utente vuole specificare capacità maggiori di quelle effettivamente disponibili oppure se le capacità non vengono definite. |
|BalancingDelayAfterNewNode | Tempo in secondi, valore predefinito: 120 |Dinamico|Specificare l'intervallo di tempo in secondi. Non avviare attività di bilanciamento in questo periodo dopo aver aggiunto un nuovo nodo. |
|BalancingDelayAfterNodeDown | Tempo in secondi, valore predefinito: 120 |Dinamico|Specificare l'intervallo di tempo in secondi. Non avviare attività di bilanciamento in questo periodo se un nodo va offline. |
|BlockNodeInUpgradeConstraintPriority | Int, valore predefinito: 0 |Dinamico|Determina la priorità del vincolo di capacità: 0: difficile; 1: Soft; negativo: ignora  |
|CapacityConstraintPriority | Int, valore predefinito: 0 | Dinamico|Determina la priorità del vincolo di capacità: 0: alta; 1: bassa; numero negativo: ignorare. |
|ConsecutiveDroppedMovementsHealthReportLimit | Int, valore predefinito: 20 | Dinamico|Definisce il numero di volte consecutive in cui i movimenti inviati da ResourceBalancer devono essere eliminati prima di eseguire la diagnostica e creare avvisi di integrità. Negativo: non vengono emessi avvisi in questa condizione. |
|ConstraintFixPartialDelayAfterNewNode | Tempo in secondi, valore predefinito: 120 |Dinamico| Specificare l'intervallo di tempo in secondi. Non risolvere violazioni di vincoli FaultDomain e UpgradeDomain in questo periodo dopo aver aggiunto un nuovo nodo. |
|ConstraintFixPartialDelayAfterNodeDown | Tempo in secondi, valore predefinito: 120 |Dinamico| Specificare l'intervallo di tempo in secondi. Non risolvere violazioni di vincoli FaultDomain e UpgradeDomain in questo periodo dopo che un nodo va offline. |
|ConstraintViolationHealthReportLimit | Int, valore predefinito: 50 |Dinamico| Definisce il numero di violazioni di vincoli di tempo non risolte da parte di una replica prima di eseguire la diagnostica e di creare report di integrità a riguardo. |
|DetailedConstraintViolationHealthReportLimit | Int, valore predefinito: 200 |Dinamico| Definisce il numero di violazioni di vincoli di tempo non risolte da parte di una replica prima di eseguire la diagnostica e di creare report di integrità dettagliati a riguardo. |
|DetailedDiagnosticsInfoListLimit | Int, valore predefinito: 15 |Dinamico| Definisce il numero di voci di diagnostica di un vincolo (con informazioni dettagliate) da includere prima del troncamento della diagnostica.|
|DetailedNodeListLimit | Int, valore predefinito: 15 |Dinamico| Definisce il numero di nodi per vincolo da includere prima del troncamento nei report sulle repliche non spostate. |
|DetailedPartitionListLimit | Int, valore predefinito: 15 |Dinamico| Definisce il numero di partizioni per ciascuna voce diagnostica di un vincolo da includere prima del troncamento della diagnostica. |
|DetailedVerboseHealthReportLimit | Int, valore predefinito: 200 | Dinamico|Definisce il numero di posizionamenti errati continui di una replica prima di creare report di integrità dettagliati. |
|EnforceUserServiceMetricCapacities|bool, valore predefinito: FALSE | Statico |Abilita la protezione dei servizi di infrastruttura. Tutti i servizi utente si trovano in un unico oggetto/cgroup di processo e sono limitati a una quantità specificata di risorse. Questa impostazione deve essere statica (richiede il riavvio di FabricHost) poiché la creazione/rimozione di un oggetto processo utente e l'impostazione dei limiti vengono effettuate durante l'apertura di Fabric Host. |
|FaultDomainConstraintPriority | Int, valore predefinito: 0 |Dinamico| Determina la priorità del vincolo di dominio di errore: 0: alta; 1: bassa; numero negativo: ignorare. |
|GlobalMovementThrottleCountingInterval | Tempo in secondi, valore predefinito: 600 |Statico| Specificare l'intervallo di tempo in secondi. Indicare la lunghezza dell'intervallo precedente per cui si desidera tenere traccia dei movimenti di replica di dominio. Usato insieme a GlobalMovementThrottleThreshold. È possibile impostarlo su 0 per ignorare del tutto la limitazione globale. |
|GlobalMovementThrottleThreshold | Uint, valore predefinito: 1000 |Dinamico| Numero massimo di movimenti consentiti nella fase di bilanciamento nel precedente intervallo indicato in GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForBalancing | Uint, valore predefinito: 0 | Dinamico|Numero massimo di movimenti consentiti nella fase di bilanciamento nel precedente intervallo indicato in GlobalMovementThrottleCountingInterval. Impostarlo su 0 per non avere limiti. |
|GlobalMovementThrottleThresholdForPlacement | Uint, valore predefinito: 0 |Dinamico| Numero massimo di movimenti consentiti nella fase di posizionamento nel precedente intervallo indicato in GlobalMovementThrottleCountingInterval. Impostarlo su 0 per non avere limiti.|
|GlobalMovementThrottleThresholdPercentage|double, valore predefinito: 0|Dinamico|Numero massimo di spostamenti totali consentiti nelle fasi di bilanciamento e selezione (espresso come percentuale del numero totale di repliche nel cluster) nell'intervallo trascorso indicato da GlobalMovementThrottleCountingInterval. Impostarlo su 0 per non avere limiti. Se vengono specificati sia questa impostazione che GlobalMovementThrottleThreshold, viene usato il limite più conservativo.|
|GlobalMovementThrottleThresholdPercentageForBalancing|double, valore predefinito: 0|Dinamico|Numero massimo di spostamenti consentiti nella fase di bilanciamento (espresso come percentuale del numero totale di repliche in PLB) nell'intervallo trascorso indicato da GlobalMovementThrottleCountingInterval. Impostarlo su 0 per non avere limiti. Se vengono specificati sia questa impostazione che GlobalMovementThrottleThresholdForBalancing, viene usato il limite più conservativo.|
|InBuildThrottlingAssociatedMetric | stringa, il valore predefinito è "" |Statico| Il nome della metrica associato a questa limitazione. |
|InBuildThrottlingEnabled | Bool, valore predefinito: false |Dinamico| Determina se la limitazione in build è abilitata. |
|InBuildThrottlingGlobalMaxValue | Int, valore predefinito: 0 |Dinamico|Il numero massimo di repliche in build consentite a livello globale. |
|InterruptBalancingForAllFailoverUnitUpdates | Bool, valore predefinito: false | Dinamico|Determina se qualsiasi tipo di aggiornamento di un'unità di failover deve interrompere un'esecuzione di un bilanciamento rapido o lento. Specificare "false" per interrompere l'esecuzione del bilanciamento se FailoverUnit: viene creata/eliminata; ha repliche mancanti; ha modificato il percorso di replica primario o il numero di repliche. L'esecuzione del bilanciamento NON verrà interrotta in altri casi, ossia se FailoverUnit: ha repliche extra; ha modificato flag della replica; ha modificato solo la versione della partizione e tutti gli altri casi. |
|MinConstraintCheckInterval | Tempo in secondi, valore predefinito: 1 |Dinamico| Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che avvengano due cicli di controllo dei vincoli consecutivi. |
|MinLoadBalancingInterval | Tempo in secondi, valore predefinito: 5 |Dinamico| Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che avvengano due cicli di bilanciamento consecutivi. |
|MinPlacementInterval | Tempo in secondi, valore predefinito: 1 |Dinamico| Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che avvengano due cicli di posizionamento consecutivi. |
|MoveExistingReplicaForPlacement | Bool, valore predefinito: true |Dinamico|Impostazione che determina se spostare le repliche esistenti durante il posizionamento. |
|MovementPerPartitionThrottleCountingInterval | Tempo in secondi, valore predefinito: 600 |Statico| Specificare l'intervallo di tempo in secondi. Indicare la lunghezza dell'intervallo precedente per cui si desidera tenere traccia dei movimenti di replica per ciascuna partizione. Usato insieme a MovementPerPartitionThrottleThreshold. |
|MovementPerPartitionThrottleThreshold | Uint, valore predefinito: 50 |Dinamico| Non verranno eseguiti spostamenti correlati al bilanciamento per una partizione se il numero di tali spostamenti per le repliche della partizione ha raggiunto o superato MovementPerFailoverUnitThrottleThreshold nell'intervallo precedente indicato in MovementPerPartitionThrottleCountingInterval. |
|MoveParentToFixAffinityViolation | Bool, valore predefinito: false |Dinamico| Impostazione che determina se le repliche padre possono essere spostate per correggere i vincoli di affinità.|
|PartiallyPlaceServices | Bool, valore predefinito: true |Dinamico| Determina se tutte le repliche servizio nel cluster verranno posizionate in modo "tutto o niente" in caso di nodi appropriati limitati.|
|PlaceChildWithoutParent | Bool, valore predefinito: true | Dinamico|Impostazione che determina se è possibile posizionare una replica di servizio figlia in caso non sia presente una replica padre. |
|PlacementConstraintPriority | Int, valore predefinito: 0 | Dinamico|Determina la priorità del vincolo di posizionamento: 0: alta; 1: bassa; numero negativo: ignorare. |
|PlacementConstraintValidationCacheSize | Int, valore predefinito: 10000 |Dinamico| Limita le dimensioni della tabella usata per la convalida e la memorizzazione nella cache rapide delle espressioni dei vincoli di posizionamento. |
|PlacementSearchTimeout | Tempo in secondi, valore predefinito: 0.5 |Dinamico| Specificare l'intervallo di tempo in secondi. Tempo massimo di ricerca durante il posizionamento dei servizi prima di restituire un risultato. |
|PLBRefreshGap | Tempo in secondi, valore predefinito: 1 |Dinamico| Specificare l'intervallo di tempo in secondi. Definisce il tempo minimo che deve passare prima che PLB aggiorni lo stato. |
|PreferredLocationConstraintPriority | Int, valore predefinito: 2| Dinamico|Determina la priorità del vincolo di posizione preferita: 0: alta; 1: bassa; 2: ottimizzazione; numero negativo: Ignora |
|PreferredPrimaryDomainsConstraintPriority| Int, valore predefinito: 1 | Dinamico| Determina la priorità del vincolo di dominio primario preferito: 0: alta; 1: bassa; numero negativo: Ignora |
|PreferUpgradedUDs|Bool, valore predefinito: FALSE|Dinamico|Attiva e disattiva la logica che preferisce lo spostamento in domini di aggiornamento già aggiornati. A partire da SF 7.0, il valore predefinito per questo parametro viene modificato da TRUE a FALSE.|
|PreventTransientOvercommit | Bool, valore predefinito: false | Dinamico|Determina se PLB deve conteggiare immediatamente le risorse che saranno liberate dagli spostamenti avviati. Per impostazione predefinita, PLB può avviare gli spostamenti in entrata e in uscita nello stesso nodo che può creare un overcommit temporaneo. Impostando il parametro su true sarà possibile prevenire overcommit simili e disabilitare le deframmentazioni su richiesta, note anche come placementWithMove. |
|ScaleoutCountConstraintPriority | Int, valore predefinito: 0 |Dinamico| Determina la priorità del vincolo del conteggio di scalabilità orizzontale: 0: alta; 1: bassa; numero negativo: ignorare. |
|SubclusteringEnabled|Bool, valore predefinito: false | Dinamico |Riconoscimento del sottoclustering durante il calcolo della deviazione standard per il bilanciamento |
|SubclusteringReportingPolicy| Int, valore predefinito: 1 |Dinamico|Definisce come e se vengono inviati i report sull'integrità del sottoclustering: 0: Non segnalare; 1: Avviso; 2: OK |
|SwapPrimaryThrottlingAssociatedMetric | stringa, il valore predefinito è ""|Statico| Il nome della metrica associato a questa limitazione. |
|SwapPrimaryThrottlingEnabled | Bool, valore predefinito: false|Dinamico| Determina se la limitazione swap-primary è abilitata. |
|SwapPrimaryThrottlingGlobalMaxValue | Int, valore predefinito: 0 |Dinamico| Il numero massimo di repliche swap-primary consentite a livello globale. |
|TraceCRMReasons |Bool, valore predefinito: true |Dinamico|Specifica se tracciare i motivi dei movimenti indicati da CRM al canale degli eventi operativi. |
|UpgradeDomainConstraintPriority | Int, valore predefinito: 1| Dinamico|Determina la priorità del vincolo di dominio di aggiornamento: 0: alta; 1: bassa; numero negativo: ignorare. |
|UseMoveCostReports | Bool, valore predefinito: false | Dinamico|Indica al servizio di bilanciamento del carico di ignorare l'elemento di costo della funzione di assegnazione dei punteggio. Comporterà potenzialmente un numero elevato di spostamenti per un posizionamento più bilanciato. |
|UseSeparateSecondaryLoad | Bool, valore predefinito: true | Dinamico|Impostazione che determina se usare un carico separato per le repliche secondarie. |
|UseSeparateSecondaryMoveCost | Bool, valore predefinito: false | Dinamico|Impostazione che determina se usare un carico di spostamento separato per le repliche secondarie. |
|ValidatePlacementConstraint | Bool, valore predefinito: true |Dinamico| Specifica se l'espressione PlacementConstraint per un servizio viene convalidata quando viene aggiornato il parametro ServiceDescription di un servizio. |
|ValidatePrimaryPlacementConstraintOnPromote| Bool, valore predefinito: TRUE |Dinamico|Specifica se l'espressione PlacementConstraint per un servizio viene valutata per le preferenze primarie al failover. |
|VerboseHealthReportLimit | Int, valore predefinito: 20 | Dinamico|Definisce il numero di posizionamenti non riusciti di una replica prima di inviare un avviso di integrità a riguardo (se è abilitata la creazione di report di integrità dettagliati). |
|NodeLoadsOperationalTracingEnabled | Bool, valore predefinito: true |Dinamico|Configurazione che abilita la traccia strutturale operativa del carico del nodo nell'archivio eventi. |
|NodeLoadsOperationalTracingInterval | TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(20) | Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo con cui tracciare i carichi dei nodi nell'archivio eventi per ogni dominio del servizio. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ApplicationUpgradeMaxReplicaCloseDuration | Tempo in secondi, valore predefinito: 900 |Dinamico|Specificare l'intervallo di tempo in secondi. Durata dell'attesa massima del sistema prima di arrestare gli host di servizi che dispongono di repliche bloccate nello stato di chiusura durante l'aggiornamento dell'applicazione.|
|FabricUpgradeMaxReplicaCloseDuration | Tempo in secondi, valore predefinito: 900 |Dinamico| Specificare l'intervallo di tempo in secondi. Durata dell'attesa massima del sistema prima di arrestare gli host di servizi che dispongono di repliche bloccate nello stato di chiusura durante l'aggiornamento dell'infrastruttura. |
|GracefulReplicaShutdownMaxDuration|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(120)|Dinamico|Specificare l'intervallo di tempo in secondi. La durata dell'attesa massima del sistema prima di arrestare gli host di servizi che dispongono di repliche bloccate nello stato di chiusura. Se questo valore è impostato su 0, le repliche non verranno avviate alla chiusura.|
|NodeDeactivationMaxReplicaCloseDuration | Tempo in secondi, valore predefinito: 900 |Dinamico|Specificare l'intervallo di tempo in secondi. Durata dell'attesa massima del sistema prima di arrestare gli host di servizi che dispongono di repliche bloccate nello stato di chiusura durante la disattivazione del nodo. |
|PeriodicApiSlowTraceInterval | Tempo in secondi, valore predefinito: 5 minuti |Dinamico| Specificare l'intervallo di tempo in secondi. PeriodicApiSlowTraceInterval definisce l'intervallo in cui le chiamate API lente verranno ritracciate dal monitoraggio API. |
|ReplicaChangeRoleFailureRestartThreshold|int, valore predefinito: 10|Dinamico| Integer. Specificare il numero di errori API durante la promozione della replica primaria dopo il quale verrà applicata l'azione di mitigazione automatica (riavvio della replica). |
|ReplicaChangeRoleFailureWarningReportThreshold|int, valore predefinito: 2147483647|Dinamico| Integer. Specificare il numero di errori API durante la promozione della replica primaria dopo il quale verrà generato il rapporto di integrità di avviso.|
|ServiceApiHealthDuration | Tempo in secondi, valore predefinito: 30 minuti |Dinamico| Specificare l'intervallo di tempo in secondi. ServiceApiHealthDuration definisce l'attesa massima per l'esecuzione di un'API prima che questa venga indicata come non integra. |
|ServiceReconfigurationApiHealthDuration | Tempo in secondi, il valore predefinito è 30 |Dinamico| Specificare l'intervallo di tempo in secondi. ServiceReconfigurationApiHealthDuration definisce l'attesa massima per l'esecuzione di un'API prima che questa venga indicata come non integra. Si applica alle chiamate API con impatto sulla disponibilità.|

## <a name="replication"></a>Replica
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromMilliseconds(15)|Statico|Specificare l'intervallo di tempo in secondi. Periodo di tempo di attesa del Replicator dopo aver ricevuto un'operazione prima di inviare un acknowledgement in risposta. Altre operazioni ricevuti durante questo periodo di tempo comporteranno un invio dei vari acknowledgement in un unico messaggio, riducendo il traffico di rete ma anche potenzialmente la velocità effettiva del Replicator.|
|MaxCopyQueueSize|uint, valore predefinito: 1024|Statico|Valore massimo che definisce la dimensione iniziale per la coda che gestisce le operazioni di replica. Si noti che deve essere una potenza di 2. Se durante il runtime la coda raggiunge tale dimensione, l'operazione verrà limitata tra i replicatori primario e secondario.|
|MaxPrimaryReplicationQueueMemorySize|uint, valore predefinito: 0|Statico|Valore massimo della coda di replica primaria in byte.|
|MaxPrimaryReplicationQueueSize|uint, valore predefinito: 1024|Statico|Numero massimo di operazioni che possono essere presenti nella coda di replica primaria. Si noti che deve essere una potenza di 2.|
|MaxReplicationMessageSize|uint, valore predefinito: 52428800|Statico|Dimensioni massime dei messaggi delle operazioni di replica. Il valore predefinito è 50 MB.|
|MaxSecondaryReplicationQueueMemorySize|uint, valore predefinito: 0|Statico|Valore massimo della coda di replica secondaria in byte.|
|MaxSecondaryReplicationQueueSize|uint, valore predefinito: 2048|Statico|Numero massimo di operazioni che possono essere presenti nella coda di replica secondaria. Si noti che deve essere una potenza di 2.|
|QueueHealthMonitoringInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(30)|Statico|Specificare l'intervallo di tempo in secondi. Questo valore determina il periodo di tempo impiegato dal Replicator per monitorare gli eventi di integrità di avviso/errore nelle code delle operazioni di replica. Il valore '0' disabilita il monitoraggio dell'integrità |
|QueueHealthWarningAtUsagePercent|uint, valore predefinito: 80|Statico|Questo valore determina l'utilizzo della coda di replica (in percentuale) dopo il quale verrà generato un avviso per segnalare un utilizzo elevato della coda. La segnalazione avviene dopo un intervallo di tolleranza corrispondente a QueueHealthMonitoringInterval. Se l'utilizzo della coda scende sotto questa percentuale nell'intervallo di tolleranza.|
|ReplicatorAddress|stringa, il valore predefinito è "localhost:0"|Statico|L'endpoint in forma di stringa "IP:Port" usato dal Replicator di Windows Fabric per stabilire connessioni con altre repliche per inviare/ricevere operazioni.|
|ReplicatorListenAddress|stringa, il valore predefinito è "localhost:0"|Statico|Endpoint in forma di stringa -'IP:Porta' usato dal Replicator di Windows Fabric per ricevere operazioni da altre repliche.|
|ReplicatorPublishAddress|stringa, il valore predefinito è "localhost:0"|Statico|Endpoint in forma di stringa -'IP:Porta' usato dal Replicator di Windows Fabric per inviare operazioni ad altre repliche.|
|RetryInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(5)|Statico|Specificare l'intervallo di tempo in secondi. Quando un'operazione viene persa o rifiutata, questo timer determina la frequenza con cui il replicatore ritenterà l'operazione di invio.|

## <a name="resourcemonitorservice"></a>ResourceMonitorService
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|IsEnabled|bool, valore predefinito: FALSE |Statico|Controlla se il servizio è abilitato o meno nel cluster. |

## <a name="runas"></a>RunAs

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|RunAsAccountName |stringa, il valore predefinito è "" |Dinamico|Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Dinamico|Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "DomainUser/NetworkService/ManagedServiceAccount/LocalSystem".|
|RunAsPassword|stringa, il valore predefinito è "" |Dinamico|Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

## <a name="runas_dca"></a>RunAs_DCA

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|RunAsAccountName |stringa, il valore predefinito è "" |Dinamico|Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Dinamico|Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|stringa, il valore predefinito è "" |Dinamico|Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

## <a name="runas_fabric"></a>RunAs_Fabric

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|RunAsAccountName |stringa, il valore predefinito è "" |Dinamico|Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Dinamico|Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|stringa, il valore predefinito è "" |Dinamico|Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

## <a name="runas_httpgateway"></a>RunAs_HttpGateway

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|RunAsAccountName |stringa, il valore predefinito è "" |Dinamico|Indica il nome dell'account RunAs. Parametro necessario solo per i tipi di account "DomainUser" o "ManagedServiceAccount". I valori validi sono "dominio\utente" o "user@domain". |
|RunAsAccountType|stringa, il valore predefinito è "" |Dinamico|Indica il tipo di account RunAs. Parametro necessario per ogni sezione RunAs. I valori validi sono: "LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem". |
|RunAsPassword|stringa, il valore predefinito è "" |Dinamico|Indica la password dell'account RunAs. Parametro necessario solo per il tipo di account "DomainUser". |

## <a name="security"></a>Sicurezza
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento**| **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|AADCertEndpointFormat|stringa, il valore predefinito è ""|Statico|Formato dell'endpoint dei certificati di AAD, Azure Commercial per impostazione predefinita, specificato per un ambiente non predefinito, ad esempio Azure per enti pubblici "https:\//login.microsoftonline.us/{0}/federationmetadata/2007-06/federationmetadata.xml" |
|AADClientApplication|stringa, il valore predefinito è ""|Statico|Nome dell'applicazione client nativa o ID che rappresenta i client dell'infrastruttura. |
|AADClusterApplication|stringa, il valore predefinito è ""|Statico|Nome dell'applicazione API Web o ID che rappresenta il cluster. |
|AADLoginEndpoint|stringa, il valore predefinito è ""|Statico|Endpoint di accesso di AAD, Azure Commercial per impostazione predefinita, specificato per un ambiente non predefinito, ad esempio Azure per enti pubblici "https:\//login.microsoftonline.us" |
|AADTenantId|stringa, il valore predefinito è ""|Statico|ID del tenant (GUID) |
|AcceptExpiredPinnedClusterCertificate|bool, valore predefinito: FALSE|Dinamico|Flag che indica se accettare i certificati del cluster scaduti dichiarati dall'identificazione personale. Si applica solo ai certificati del cluster per garantire che sia attivo. |
|AdminClientCertThumbprints|stringa, il valore predefinito è ""|Dinamico|Identificazioni personali dei certificati usati dai client nel ruolo di amministratore. Elenco di nomi delimitati da virgole. |
|AADTokenEndpointFormat|stringa, il valore predefinito è ""|Statico|Endpoint del token di AAD, Azure Commercial per impostazione predefinita, specificato per un ambiente non predefinito, ad esempio Azure per enti pubblici "https:\//login.microsoftonline.us/{0}" |
|AdminClientClaims|stringa, il valore predefinito è ""|Dinamico|Tutte le attestazioni possibili previste dai client di amministrazione. Stesso formato di ClientClaims. Questo elenco viene aggiunto internamente a ClientClaims, pertanto non occorre aggiungere le stesse voci a ClientClaims. |
|AdminClientIdentities|stringa, il valore predefinito è ""|Dinamico|Identità di Windows dei client dell'infrastruttura nel ruolo di amministratore. Questo parametro viene usato per autorizzare operazioni sull'infrastruttura con privilegi. Si tratta di un elenco delimitato da virgole, in cui ogni voce è un nome di account di dominio o un nome di gruppo. Per motivi di praticità, all'account che esegue fabric.exe viene assegnato automaticamente il ruolo di amministratore, così come al gruppo ServiceFabricAdministrators. |
|AppRunAsAccountGroupX509Folder|String, valore predefinito: /home/sfuser/sfusercerts |Statico|Cartella in cui vengono posizionati i certificati AppRunAsAccountGroup X509 e le chiavi private |
|CertificateExpirySafetyMargin|TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(43200)|Statico|Specificare l'intervallo di tempo in secondi. Margine di sicurezza per la scadenza del certificato. Lo stato del report di integrità del certificato passa da OK ad avviso quando la scadenza è inferiore a questo margine. Il valore predefinito è 30 giorni. |
|CertificateHealthReportingInterval|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(3600 * 8)|Statico|Specificare l'intervallo di tempo in secondi. Specificare l'intervallo per il report di integrità del certificato. Il valore predefinito è 8 ore. L'impostazione del valore 0 disabilita i report sull'integrità dei certificati. |
|ClientCertThumbprints|stringa, il valore predefinito è ""|Dinamico|Identificazioni personali dei certificati usati dai client per comunicare con il cluster. Il cluster usa questi valori per autorizzare la connessione in ingresso. Elenco di nomi delimitati da virgole. |
|ClientClaimAuthEnabled|bool, valore predefinito: FALSE|Statico|Indica se l'autenticazione basata sulle attestazioni è abilitata nei client. L'impostazione di questo parametro su true comporta l'impostazione implicita di ClientRoleEnabled. |
|ClientClaims|stringa, il valore predefinito è ""|Dinamico|Tutte le attestazioni possibili previste dai client per la connessione al gateway. Questo è un elenco "OR": ClaimsEntry \|\| ClaimsEntry \|\| ClaimsEntry ... ogni ClaimsEntry è un elenco "AND": ClaimType=ClaimValue && ClaimType=ClaimValue && ClaimType=ClaimValue ... |
|ClientIdentities|stringa, il valore predefinito è ""|Dinamico|Identità di Windows di FabricClient. Il gateway di denominazione usa questi valori per autorizzare le connessioni in ingresso. Si tratta di un elenco delimitato da virgole, in cui ogni voce è un nome di account di dominio o un nome di gruppo. Per motivi di praticità, l'account che esegue fabric.exe viene autorizzato automaticamente, così come i gruppi ServiceFabricAllowedUsers e ServiceFabricAdministrators. |
|ClientRoleEnabled|bool, valore predefinito: FALSE|Statico|Indica se il ruolo di client è abilitato. Se impostato su true, ai client vengono assegnati i ruoli in base alle rispettive identità. Nella versione 2, l'abilitazione di questo parametro indica che un client non incluso in AdminClientCommonNames/AdminClientIdentities può solo eseguire operazioni di sola lettura. |
|ClusterCertThumbprints|stringa, il valore predefinito è ""|Dinamico|Identificazioni personali dei certificati per cui è consentita l'aggiunta al cluster. Elenco di nomi separati da virgole. |
|ClusterCredentialType|stringa, il valore predefinito è "None"|Non consentito|Indica il tipo di credenziali di sicurezza da usare per proteggere il cluster. I valori validi sono "None/X509/Windows" |
|ClusterIdentities|stringa, il valore predefinito è ""|Dinamico|Identità di Windows dei nodi del cluster, usata per l'autorizzazione delle appartenenze al cluster. Si tratta di un elenco delimitato da virgole, in cui ogni voce è un nome di account di dominio o un nome di gruppo. |
|ClusterSpn|stringa, il valore predefinito è ""|Non consentito|Nome dell'entità servizio del cluster quando l'infrastruttura viene eseguita come singolo utente di dominio (gMSA/account utente di dominio). Nome dell'entità servizio dei listener di lease e dei listener in fabric.exe: listener di federazione; listener di replica interna; listener del servizio di runtime e listener del gateway di denominazione. Questo parametro deve essere lasciato vuoto quando l'infrastruttura viene eseguita con account computer, connettendo in questo caso il nome dell'entità servizio del listener di calcolo dall'indirizzo di trasporto del listener. |
|CrlCheckingFlag|uint, valore predefinito: 0x40000000|Dinamico|Flag di convalida della catena di certificati predefinito. Può essere sovrascritto dal flag specifico del componente. Ad esempio Federation/X509CertChainFlags 0x10000000 CERT_CHAIN_REVOCATION_CHECK_END_CERT 0x20000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN 0x40000000 CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT 0x80000000 CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY L'impostazione 0 disabilita il controllo di CRL L'elenco completo dei valori supportati è documentato da dwFlags di CertGetCertificateChain: https://msdn.microsoft.com/library/windows/desktop/aa376078(v=vs.85).aspx |
|CrlDisablePeriod|TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(15)|Dinamico|Specificare l'intervallo di tempo in secondi. Per quanto tempo viene disabilitato il controllo di CRL dopo il rilevamento di un errore di offline. Se l'errore di CRL offline può essere ignorato. |
|CrlOfflineHealthReportTtl|TimeSpan, valore predefinito: Common::TimeSpan::FromMinutes(1440)|Dinamico|Specificare l'intervallo di tempo in secondi. |
|DisableFirewallRuleForDomainProfile| bool, valore predefinito: TRUE |Statico| Indica se la regola del firewall non deve essere abilitata per un profilo di dominio. |
|DisableFirewallRuleForPrivateProfile| bool, valore predefinito: TRUE |Statico| Indica se la regola del firewall non deve essere abilitata per un profilo privato. | 
|DisableFirewallRuleForPublicProfile| bool, valore predefinito: TRUE | Statico|Indica se la regola del firewall non deve essere abilitata per un profilo pubblico. |
| EnforceLinuxMinTlsVersion | bool, valore predefinito: FALSE | Statico | Se impostato su true, è supportata solo la versione 1.2+ di TLS.  Se impostato su false, sono supportate le versioni di TLS precedenti. Si applica solo a Linux |
| EnforcePrevalidationOnSecurityChanges | bool, valore predefinito: FALSE| Dinamico | Flag che controlla il comportamento dell'aggiornamento del cluster quando vengono rilevate modifiche alle impostazioni di sicurezza. Se il valore è impostato su "true", l'aggiornamento del cluster tenterà di garantire che almeno uno dei certificati corrispondenti a una delle regole di presentazione possa superare una regola di convalida corrispondente. La pre-convalida viene eseguita prima che le nuove impostazioni vengano applicate a un nodo, ma solo sul nodo che ospita la replica primaria del servizio Gestione cluster al momento dell'avvio dell'aggiornamento. Il valore predefinito è attualmente impostato su "false"; a partire dalla versione 7.1, il valore verrà modificato in "true" per i nuovi cluster di Azure Service Fabric.|
|FabricHostSpn| stringa, il valore predefinito è "" |Statico| Nome dell'entità servizio di FabricHost, quando l'infrastruttura viene eseguita come singolo utente di dominio (gMSA/account utente di dominio) e FabricHost viene eseguito con l'account computer. Nome dell'entità servizio del listener IPC per FabricHost, che deve essere lasciato vuoto per impostazione predefinita perché FabricHost viene eseguito con l'account computer. |
|IgnoreCrlOfflineError|bool, valore predefinito: FALSE|Dinamico|Se ignorare l'errore di CRL offline quando il lato server verifica i certificati client in ingresso. |
|IgnoreSvrCrlOfflineError|bool, valore predefinito: TRUE|Dinamico|Se ignorare l'errore di CRL offline quando il lato client verifica i certificati server in ingresso. Il valore predefinito è true. Gli attacchi basati su certificati server revocati richiedono di compromettere il sistema DNS, ovvero sono più complessi rispetto a quelli basati sui certificati client revocati. |
|ServerAuthCredentialType|stringa, il valore predefinito è "None"|Statico|Indica il tipo di credenziali di sicurezza da usare per proteggere la comunicazione tra FabricClient e il cluster. I valori validi sono "None/X509/Windows" |
|ServerCertThumbprints|stringa, il valore predefinito è ""|Dinamico|Identificazioni personali dei certificati del server usati dal cluster per le comunicazioni con i client. I client usano questi valori per autenticare il cluster. Elenco di nomi delimitati da virgole. |
|SettingsX509StoreName| stringa, il valore predefinito è "MY"| Dinamico|Archivio certificati X509 usato dall'infrastruttura per la protezione della configurazione. |
|UseClusterCertForIpcServerTlsSecurity|bool, valore predefinito: FALSE|Statico|Indica se usare il certificato del cluster per proteggere l'unità di trasporto TLS del server IPC |
|X509Folder|string, valore predefinito: /var/lib/waagent|Statico|Cartella in cui si trovano i certificati e le chiavi private X509. |
|TLS1_2_CipherList| string| Statico|Se impostato su una stringa non vuota, sostituisce l'elenco di crittografia supportato per TLS 1.2 e versioni precedenti. Vedere la documentazione "openssl-ciphers" per recuperare l'elenco di crittografia supportato e il formato dell'elenco. Esempio di un elenco di crittografia robusto per TLS 1.2: "ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES-128-GCM-SHA256:ECDHE-ECDSA-AES256-CBC-SHA384:ECDHE-ECDSA-AES128-CBC-SHA256:ECDHE-RSA-AES256-CBC-SHA384:ECDHE-RSA-AES128-CBC-SHA256" Si applica solo a Linux. |

## <a name="securityadminclientx509names"></a>Security/AdminClientX509Names

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valore predefinito: None|Dinamico|Elenco di coppie "Nome" e "Valore". Ogni "Nome" rappresenta il nome comune del soggetto o il DnsName dei certificati X509 autorizzati per le operazioni del client di amministrazione. Per un "Nome" specifico, "Valore" è un elenco separato da virgole di identificazioni personali del certificato per l'aggiunta dell'autorità di certificazione. Se non è vuoto, l'autorità di certificazione diretta dei certificati client di amministrazione deve essere inclusa nell'elenco. |

## <a name="securityclientaccess"></a>Security/ClientAccess

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ActivateNode |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per attivare un nodo. |
|CancelTestCommand |stringa, il valore predefinito è "Admin" |Dinamico| Annulla un TestCommand specifico, se è in corso. |
|CodePackageControl |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per riavviare i pacchetti di codice. |
|CreateApplication |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per creare un'applicazione. |
|CreateComposeDeployment|stringa, il valore predefinito è "Admin"| Dinamico|Crea una distribuzione Compose descritta dai file Compose. |
|CreateGatewayResource|stringa, il valore predefinito è "Admin"| Dinamico|Creazione di una risorsa per il gateway |
|CreateName |stringa, il valore predefinito è "Admin" |Dinamico|Configurazione di sicurezza per creare un URI di denominazione. |
|CreateNetwork|stringa, il valore predefinito è "Admin" |Dinamico|Crea una rete di contenitori |
|CreateService |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per creare un servizio. |
|CreateServiceFromTemplate |stringa, il valore predefinito è "Admin" |Dinamico|Configurazione di sicurezza per creare un servizio da un modello. |
|CreateVolume|stringa, il valore predefinito è "Admin"|Dinamico|Crea un volume |
|DeactivateNode |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per disattivare un nodo. |
|DeactivateNodesBatch |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per disattivare più nodi. |
|Delete |stringa, il valore predefinito è "Admin" |Dinamico| Configurazioni di sicurezza per l'operazione di eliminazione del client dell'archivio immagini. |
|DeleteApplication |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per eliminare un'applicazione. |
|DeleteComposeDeployment|stringa, il valore predefinito è "Admin"| Dinamico|Elimina la distribuzione Compose. |
|DeleteGatewayResource|stringa, il valore predefinito è "Admin"| Dinamico|Elimina una risorsa del gateway |
|DeleteName |stringa, il valore predefinito è "Admin" |Dinamico|Configurazione di sicurezza per eliminare un URI di denominazione. |
|DeleteNetwork|stringa, il valore predefinito è "Admin" |Dinamico|Elimina una rete di contenitori |
|DeleteService |stringa, il valore predefinito è "Admin" |Dinamico|Configurazione di sicurezza per eliminare un servizio. |
|DeleteVolume|stringa, il valore predefinito è "Admin"|Dinamico|Elimina un volume.| 
|EnumerateProperties |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per l'enumerazione delle proprietà di denominazione. |
|EnumerateSubnames |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per l'enumerazione degli URI di denominazione. |
|FileContent |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il trasferimento del file del client dell'archivio immagini (esterno al cluster). |
|FileDownload |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per l'avvio del download del file del client dell'archivio immagini (esterno al cluster). |
|FinishInfrastructureTask |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per terminare le attività di infrastruttura. |
|GetChaosReport | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Recupera lo stato di Chaos all'interno di un intervallo di tempo specificato. |
|GetClusterConfiguration | stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Provoca GetClusterConfiguration in una partizione. |
|GetClusterConfigurationUpgradeStatus | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Provoca GetClusterConfigurationUpgradeStatus in una partizione. |
|GetFabricUpgradeStatus |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per il polling dello stato degli aggiornamenti del cluster. |
|GetFolderSize |stringa, il valore predefinito è "Admin" |Dinamico|Configurazione della sicurezza per il recupero delle dimensioni della cartella di FileStoreService |
|GetNodeDeactivationStatus |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per controllare lo stato di disattivazione. |
|GetNodeTransitionProgress | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per recuperare lo stato di avanzamento di un comando di transizione nodo. |
|GetPartitionDataLossProgress | stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Recupera lo stato di avanzamento per una chiamata API di richiamo di perdita dei dati. |
|GetPartitionQuorumLossProgress | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Recupera lo stato di avanzamento per una chiamata API di richiamo di perdita del quorum. |
|GetPartitionRestartProgress | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Recupera lo stato di avanzamento di una chiamata API di riavvio di una partizione. |
|GetSecrets|stringa, il valore predefinito è "Admin"|Dinamico|Ottenere i valori dei segreti |
|GetServiceDescription |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per le notifiche del servizio di long polling e lettura delle descrizioni dei servizi. |
|GetStagingLocation |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il recupero del percorso di gestione temporanea del client dell'archivio immagini. |
|GetStoreLocation |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il recupero del percorso dell'archivio del client dell'archivio immagini. |
|GetUpgradeOrchestrationServiceState|stringa, il valore predefinito è "Admin"| Dinamico|Attiva GetUpgradeOrchestrationServiceState su una partizione. |
|GetUpgradesPendingApproval |stringa, il valore predefinito è "Admin" |Dinamico| Provoca GetUpgradesPendingApproval in una partizione. |
|GetUpgradeStatus |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per il polling dello stato degli aggiornamenti dell'applicazione. |
|InternalList |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per l'operazione di elenco del file del client dell'archivio immagini (interno). |
|InvokeContainerApi|stringa, il valore predefinito è "Admin"|Dinamico|Richiama l'API del contenitore |
|InvokeInfrastructureCommand |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di protezione per i comandi di gestione delle attività di infrastruttura. |
|InvokeInfrastructureQuery |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per eseguire query sulle attività di infrastruttura. |
|Elenco |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per l'operazione di elenco del file del client dell'archivio immagini. |
|MoveNextFabricUpgradeDomain |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per riprendere gli aggiornamenti del cluster con un dominio di aggiornamento esplicito. |
|MoveNextUpgradeDomain |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per riprendere gli aggiornamenti dell'applicazione con un dominio di aggiornamento esplicito. |
|MoveReplicaControl |stringa, il valore predefinito è "Admin" | Dinamico|Spostamento di repliche. |
|NameExists |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per i controlli sulla presenza di URI di denominazione. |
|NodeControl |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per avviare, arrestare e riavviare i nodi. |
|NodeStateRemoved |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per i report sulla rimozione dello stato di un nodo. |
|Ping |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per i ping di client. |
|PredeployPackageToNode |stringa, il valore predefinito è "Admin" |Dinamico| API per la pre-distribuzione. |
|PrefixResolveService |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per la risoluzione dei prefissi di basati sui reclami. |
|PropertyReadBatch |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per le operazioni di lettura delle proprietà di denominazione. |
|PropertyWriteBatch |stringa, il valore predefinito è "Admin" |Dinamico|Configurazioni di sicurezza per le operazioni di scrittura di proprietà di denominazione. |
|ProvisionApplicationType |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il provisioning dei tipi di applicazione. |
|ProvisionFabric |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il provisioning del manifesto del cluster e/o del file con estensione msi. |
|Query |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per le query. |
|RecoverPartition |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per ripristinare una partizione. |
|RecoverPartitions |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per ripristinare più partizioni. |
|RecoverServicePartitions |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per ripristinare partizioni di servizio. |
|RecoverSystemPartitions |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per ripristinare partizioni di servizio di sistema. |
|RemoveNodeDeactivations |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per annullare la disattivazione di più nodi. |
|ReportFabricUpgradeHealth |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per riprendere gli aggiornamenti del cluster con lo stato di avanzamento corrente. |
|ReportFault |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per i report di errori. |
|ReportHealth |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per i report di integrità. |
|ReportUpgradeHealth |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per riprendere gli aggiornamenti dell'applicazione con lo stato di avanzamento corrente. |
|ResetPartitionLoad |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per reimpostare il carico per failoverUnit. |
|ResolveNameOwner |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per la risoluzione dei proprietari degli URI di denominazione. |
|ResolvePartition |stringa, il valore predefinito è "Admin\|\|User" | Dinamico|Configurazione di sicurezza per la risoluzione dei servizi di sistema. |
|ResolveService |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per la risoluzione di servizi in base a reclami. |
|ResolveSystemService|stringa, il valore predefinito è "Admin\|\|User"|Dinamico| Configurazione di sicurezza per la risoluzione dei servizi di sistema. |
|RollbackApplicationUpgrade |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per eseguire il rollback degli aggiornamenti dell'applicazione. |
|RollbackFabricUpgrade |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per il rollback degli aggiornamenti del cluster. |
|ServiceNotifications |stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per le notifiche di servizi basati su eventi. |
|SetUpgradeOrchestrationServiceState|stringa, il valore predefinito è "Admin"| Dinamico|Attiva SetUpgradeOrchestrationServiceState su una partizione. |
|StartApprovedUpgrades |stringa, il valore predefinito è "Admin" |Dinamico| Provoca StartApprovedUpgrades in una partizione. |
|StartChaos |stringa, il valore predefinito è "Admin" |Dinamico| Avvia Chaos, se non è già avviato. |
|StartClusterConfigurationUpgrade |stringa, il valore predefinito è "Admin" |Dinamico| Provoca StartClusterConfigurationUpgrade in una partizione. |
|StartInfrastructureTask |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per avviare le attività di infrastruttura. |
|StartNodeTransition |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per avviare la transizione di un nodo. |
|StartPartitionDataLoss |stringa, il valore predefinito è "Admin" |Dinamico| Provoca la perdita di dati in una partizione. |
|StartPartitionQuorumLoss |stringa, il valore predefinito è "Admin" |Dinamico| Provoca la perdita di quorum in una partizione. |
|StartPartitionRestart |stringa, il valore predefinito è "Admin" |Dinamico| Riavvia contemporaneamente alcune o tutte le repliche di una partizione. |
|StopChaos |stringa, il valore predefinito è "Admin" |Dinamico| Arresta Chaos, se è stato avviato. |
|ToggleVerboseServicePlacementHealthReporting | stringa, il valore predefinito è "Admin\|\|User" |Dinamico| Configurazione di sicurezza per attivare o disattivare ServicePlacement HealthReporting dettagliati. |
|UnprovisionApplicationType |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per annullare il provisioning dei tipi di applicazione. |
|UnprovisionFabric |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per l'annullamento del provisioning del manifesto del cluster e/o del file con estensione msi. |
|UnreliableTransportControl |stringa, il valore predefinito è "Admin" |Dinamico| Trasporto non affidabile per l'aggiunta e la rimozione di comportamenti. |
|UpdateService |stringa, il valore predefinito è "Admin" |Dinamico|Configurazione di sicurezza per gli aggiornamenti dei servizi. |
|UpgradeApplication |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per avviare o interrompere gli aggiornamenti dell'applicazione. |
|UpgradeComposeDeployment|stringa, il valore predefinito è "Admin"| Dinamico|Aggiorna la distribuzione Compose. |
|UpgradeFabric |stringa, il valore predefinito è "Admin" |Dinamico| Configurazione di sicurezza per avviare gli aggiornamenti del cluster. |
|Caricamento |stringa, il valore predefinito è "Admin" | Dinamico|Configurazione di sicurezza per l'operazione di copia del client dell'archivio immagini. |

## <a name="securityclientcertificateissuerstores"></a>Security/ClientCertificateIssuerStores

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, valore predefinito: None |Dinamico|Archivi certificati dell'autorità di certificazione X509 per i certificati client; Nome = clientIssuerCN; Valore = elenco di archivi delimitato da virgole |

## <a name="securityclientx509names"></a>Security/ClientX509Names

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valore predefinito: None|Dinamico|Elenco di coppie "Nome" e "Valore". Ogni "Nome" rappresenta il nome comune del soggetto o il DnsName dei certificati X509 autorizzati per le operazioni del client. Per un "Nome" specifico, "Valore" è un elenco separato da virgole di identificazioni personali del certificato per l'aggiunta dell'autorità di certificazione. Se non è vuoto, l'autorità di certificazione diretta dei certificati client deve essere inclusa nell'elenco.|

## <a name="securityclustercertificateissuerstores"></a>Security/ClusterCertificateIssuerStores

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, valore predefinito: None |Dinamico|Archivi certificati dell'autorità di certificazione X509 per i certificati cluster; Nome = clusterIssuerCN; Valore = elenco di archivi delimitato da virgole |

## <a name="securityclusterx509names"></a>Security/ClusterX509Names

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valore predefinito: None|Dinamico|Elenco di coppie "Nome" e "Valore". Ogni "Nome" rappresenta il nome comune del soggetto o il DnsName dei certificati X509 autorizzati per le operazioni del cluster. Per un "Nome" specifico, "Valore" è un elenco separato da virgole di identificazioni personali del certificato per l'aggiunta dell'autorità di certificazione. Se non è vuoto, l'autorità di certificazione diretta dei certificati cluster deve essere inclusa nell'elenco.|

## <a name="securityservercertificateissuerstores"></a>Security/ServerCertificateIssuerStores

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|IssuerStoreKeyValueMap, valore predefinito: None |Dinamico|Archivi certificati dell'autorità di certificazione X509 per i certificati server; Nome = serverIssuerCN; Valore = elenco di archivi delimitato da virgole |

## <a name="securityserverx509names"></a>Security/ServerX509Names

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup|X509NameMap, valore predefinito: None|Dinamico|Elenco di coppie "Nome" e "Valore". Ogni "Nome" rappresenta il nome comune del soggetto o il DnsName dei certificati X509 autorizzati per le operazioni del server. Per un "Nome" specifico, "Valore" è un elenco separato da virgole di identificazioni personali del certificato per l'aggiunta dell'autorità di certificazione. Se non è vuoto, l'autorità di certificazione diretta dei certificati server deve essere inclusa nell'elenco.|

## <a name="setup"></a>Configurazione

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ContainerNetworkName|stringa, il valore predefinito è ""| Statico |Nome di rete da usare quando si configura una rete di contenitori.|
|ContainerNetworkSetup|bool, valore predefinito: FALSE (Linux) e TRUE (Windows)| Statico |Se configurare una rete di contenitori.|
|FabricDataRoot |string | Non consentito |La directory radice dei dati di Service Fabric. L'impostazione predefinita per Azure è d:\svcfab |
|FabricLogRoot |string | Non consentito |La directory radice dei log di Service Fabric. Si tratta della posizione in cui vengono collocate le tracce e i log di Service Fabric. |
|NodesToBeRemoved|stringa, il valore predefinito è ""| Dinamico |Nodi che devono essere rimossi come parte dell'aggiornamento della configurazione. (Solo per le distribuzioni autonome)|
|ServiceRunAsAccountName |string | Non consentito |Il nome dell'account con cui eseguire il servizio host infrastruttura. |
|SkipContainerNetworkResetOnReboot|bool, valore predefinito: FALSE|NotAllowed|Se di desidera ignorare la reimpostazione di rete del contenitore al riavvio del sistema.|
|SkipFirewallConfiguration |Bool, valore predefinito: false | Non consentito |Specifica se le impostazioni del firewall devono essere impostate dal sistema. Si applica solo se si usa Windows Firewall. Se si usano firewall di terze parti, è necessario aprire le porte per il sistema e le applicazioni da usare |

## <a name="tokenvalidationservice"></a>TokenValidationService

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|Providers |stringa, il valore predefinito è "DSTS" |Statico|Elenco separato da virgole dei provider di convalida dei token per l'abilitazione. I provider validi sono: DSTS; AAD. Attualmente è possibile abilitare un singolo provider alla volta. |

## <a name="traceetw"></a>Traccia/Etw

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|Level |Int, valore predefinito: 4 | Dinamico |Il livello di traccia ETW può accettare valori 1, 2, 3 e 4. Per assicurare il supporto è necessario mantenere il livello di traccia a 4 |

## <a name="transactionalreplicator"></a>TransactionalReplicator

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|BatchAcknowledgementInterval | Tempo in secondi, valore predefinito: 0,015 | Statico | Specificare l'intervallo di tempo in secondi. Periodo di tempo di attesa del Replicator dopo aver ricevuto un'operazione prima di inviare un acknowledgement in risposta. Altre operazioni ricevuti durante questo periodo di tempo comporteranno un invio dei vari acknowledgement in un unico messaggio, riducendo il traffico di rete ma anche potenzialmente la velocità effettiva del Replicator. |
|MaxCopyQueueSize |Uint, valore predefinito: 16384 | Statico |Valore massimo che definisce la dimensione iniziale per la coda che gestisce le operazioni di replica. Si noti che deve essere una potenza di 2. Se durante il runtime la coda raggiunge tale dimensione, l'operazione verrà limitata tra i replicatori primario e secondario. |
|MaxPrimaryReplicationQueueMemorySize |Uint, valore predefinito: 0 | Statico |Valore massimo della coda di replica primaria in byte. |
|MaxPrimaryReplicationQueueSize |Uint, valore predefinito: 8192 | Statico |Numero massimo di operazioni che possono essere presenti nella coda di replica primaria. Si noti che deve essere una potenza di 2. |
|MaxReplicationMessageSize |Uint, valore predefinito: 52428800 | Statico | Dimensioni massime dei messaggi delle operazioni di replica. Il valore predefinito è 50 MB. |
|MaxSecondaryReplicationQueueMemorySize |Uint, valore predefinito: 0 | Statico |Valore massimo della coda di replica secondaria in byte. |
|MaxSecondaryReplicationQueueSize |Uint, valore predefinito: 16384 | Statico |Numero massimo di operazioni che possono essere presenti nella coda di replica secondaria. Si noti che deve essere una potenza di 2. |
|ReplicatorAddress |stringa, il valore predefinito è "localhost:0" | Statico | L'endpoint in forma di stringa "IP:Port" usato dal Replicator di Windows Fabric per stabilire connessioni con altre repliche per inviare/ricevere operazioni. |

## <a name="transport"></a>Trasporto
| **Parametro** | **Valori consentiti** |**Criteri di aggiornamento** |**Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|ConnectionOpenTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(60)|Statico|Specificare l'intervallo di tempo in secondi. Timeout per la configurazione della connessione sul lato in ingresso e di accettazione (inclusa la negoziazione di sicurezza in modalità protetta) |
|FrameHeaderErrorCheckingEnabled|bool, valore predefinito: TRUE|Statico|Impostazione predefinita per controllare la presenza di errori nell'intestazione del fotogramma in modalità non protetta; l'impostazione del componente ne esegue l'override. |
|MessageErrorCheckingEnabled|Bool, valore predefinito: FALSE|Statico|Impostazione predefinita per controllare la presenza di errori nell'intestazione e nel corpo del messaggio in modalità non protetta; l'impostazione del componente ne esegue l'override. |
|ResolveOption|stringa, il valore predefinito è "unspecified"|Statico|Determina la modalità di risoluzione del nome di dominio completo.  I valori validi sono "unspecified/ipv4/ipv6". |
|SendTimeout|TimeSpan, valore predefinito: Common::TimeSpan::FromSeconds(300)|Dinamico|Specificare l'intervallo di tempo in secondi. Timeout di invio per il rilevamento di una connessione bloccata. I rapporti di errore TCP non sono affidabili in alcuni ambienti. Potrebbe essere necessario regolarlo in base alla larghezza di banda disponibile e alla dimensione dei dati in uscita (\*MaxMessageSiz\/\*SendQueueSizeLimit). |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|AutoupgradeEnabled | Bool, valore predefinito: true |Statico| Polling e aggiornamenti automatici in base a un file di stato obiettivo. |
|AutoupgradeInstallEnabled|Bool, valore predefinito: false|Statico|Polling, provisioning e installazione automatici di un'azione di aggiornamento del codice in base a un file di stato obiettivo.|
|GoalStateExpirationReminderInDays|Int, valore predefinito: 30|Statico|Imposta il numero di giorni rimanenti dopo il quale deve essere visualizzato il promemoria di stato obiettivo.|
|MinReplicaSetSize |Int, valore predefinito: 0 |Statico |MinReplicaSetSize per UpgradeOrchestrationService.|
|PlacementConstraints | stringa, il valore predefinito è "" |Statico| PlacementConstraints per UpgradeOrchestrationService. |
|QuorumLossWaitDuration | Tempo in secondi, valore predefinito: MaxValue |Statico| Specificare l'intervallo di tempo in secondi. QuorumLossWaitDuration per UpgradeOrchestrationService. |
|ReplicaRestartWaitDuration | Tempo in secondi, valore predefinito: 60 minuti|Statico| Specificare l'intervallo di tempo in secondi. ReplicaRestartWaitDuration per UpgradeOrchestrationService. |
|StandByReplicaKeepDuration | Tempo in secondi, valore predefinito: 60 *24* 7 minuti |Statico| Specificare l'intervallo di tempo in secondi. StandByReplicaKeepDuration per UpgradeOrchestrationService. |
|TargetReplicaSetSize |Int, valore predefinito: 0 |Statico |TargetReplicaSetSize per UpgradeOrchestrationService. |
|UpgradeApprovalRequired | Bool, valore predefinito: false | Statico|Impostazione per richiedere l'approvazione di un amministratore prima di procedere con l'aggiornamento del codice. |

## <a name="upgradeservice"></a>UpgradeService

| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|BaseUrl | stringa, il valore predefinito è "" |Statico|BaseUrl per UpgradeService. |
|ClusterId | stringa, il valore predefinito è "" |Statico|ClusterId per UpgradeService. |
|CoordinatorType | stringa, il valore predefinito è "WUTest"|Non consentito|CoordinatorType per UpgradeService. |
|MinReplicaSetSize | Int, valore predefinito: 2 |Non consentito| MinReplicaSetSize per UpgradeService. |
|OnlyBaseUpgrade | Bool, valore predefinito: false |Dinamico|OnlyBaseUpgrade per UpgradeService. |
|PlacementConstraints |stringa, il valore predefinito è "" |Non consentito|PlacementConstraints per UpgradeService. |
|PollIntervalInSeconds|Timespan, valore predefinito: Common::TimeSpan::FromSeconds(60) |Dinamico|Specificare l'intervallo di tempo in secondi. Intervallo di polling UpgradeService per operazioni di gestione ARM. |
|TargetReplicaSetSize | Int, valore predefinito: 3 |Non consentito| TargetReplicaSetSize per UpgradeService. |
|TestCabFolder | stringa, il valore predefinito è "" |Statico| TestCabFolder per UpgradeService. |
|X509FindType | stringa, il valore predefinito è ""|Dinamico| X509FindType per UpgradeService. |
|X509FindValue | stringa, il valore predefinito è "" |Dinamico| X509FindValue per UpgradeService. |
|X509SecondaryFindValue | stringa, il valore predefinito è "" |Dinamico| X509SecondaryFindValue per UpgradeService. |
|X509StoreLocation | stringa, il valore predefinito è "" |Dinamico| X509StoreLocation per UpgradeService. |
|X509StoreName | stringa, il valore predefinito è "My"|Dinamico|X509StoreName per UpgradeService. |

## <a name="userservicemetriccapacities"></a>UserServiceMetricCapacities
| **Parametro** | **Valori consentiti** | **Criteri di aggiornamento** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
|PropertyGroup| UserServiceMetricCapacitiesMap, valore predefinito: None | Statico | Una raccolta di limiti di governance delle risorse dei servizi utente. Deve essere statica perché influisce sulla logica di rilevamento automatico |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [Aggiornare la configurazione di un cluster di Azure](service-fabric-cluster-config-upgrade-azure.md) e [Aggiornare la configurazione di un cluster autonomo](service-fabric-cluster-config-upgrade-windows-server.md).
