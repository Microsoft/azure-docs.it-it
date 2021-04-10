---
title: Domande frequenti su Azure Spring cloud | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti su Azure Spring cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: cee71557e60be9fdd94099453d9b8617c4b3b5ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878572"
---
# <a name="azure-spring-cloud-faq"></a>Domande frequenti su Azure Spring cloud

Questo articolo fornisce le risposte alle domande frequenti su Azure Spring cloud.

## <a name="general"></a>Generale

### <a name="why-azure-spring-cloud"></a>Perché scegliere Azure Spring Cloud?

Azure Spring cloud offre una piattaforma distribuita come servizio (PaaS) per gli sviluppatori di Spring cloud. Azure Spring cloud gestisce l'infrastruttura dell'applicazione in modo da potersi concentrare sul codice dell'applicazione e sulla logica di business. Le funzionalità di base integrate nel cloud Spring di Azure includono Eureka, server di configurazione, server del registro di sistema del servizio, servizio di compilazione pivotal, distribuzione Blue-Green e altro ancora. Questo servizio consente inoltre agli sviluppatori di associare le proprie applicazioni ad altri servizi di Azure, ad esempio Azure Cosmos DB, database di Azure per MySQL e cache di Azure per Redis.

Azure Spring cloud migliora l'esperienza di diagnostica delle applicazioni per gli sviluppatori e gli operatori integrando monitoraggio di Azure, Application Insights e Log Analytics.

### <a name="how-secure-is-azure-spring-cloud"></a>Qual è il livello di sicurezza di Azure Spring Cloud?

Sicurezza e privacy sono tra le priorità principali per i clienti di Azure e Azure Spring cloud. Azure contribuisce a garantire che solo i clienti abbiano accesso a dati, log o configurazioni dell'applicazione crittografando in modo sicuro tutti questi dati. 

* Le istanze del servizio in Azure Spring cloud sono isolate l'una dall'altra.
* Azure Spring cloud offre funzionalità complete di gestione dei certificati e TLS/SSL.
* Le patch critiche di sicurezza per i runtime OpenJDK e Spring Cloud vengono applicate prima possibile ad Azure Spring Cloud.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>In quali aree è disponibile Azure Spring cloud?

Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Europa occidentale, Europa settentrionale, Regno Unito meridionale, Asia sudorientale, Australia orientale, Canada centrale, Emirati Arabi Uniti settentrionali, India centrale, Corea centrale, Asia orientale e Cina orientale 2 (Mooncake). [Altre informazioni](https://azure.microsoft.com/global-infrastructure/services/?products=spring-cloud)

### <a name="is-any-customer-data-stored-outside-of-the-specified-region"></a>I dati dei clienti vengono archiviati al di fuori dell'area specificata?

Azure Spring cloud è un servizio a livello di area. Tutti i dati dei clienti nel cloud Spring di Azure vengono archiviati in più aree all'interno della stessa area geografica dell'area specificata per la ridondanza. Per altre informazioni su Geo e Region, vedere la pagina relativa alla [residenza dei dati in Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quali sono le limitazioni note di Azure Spring cloud?

Azure Spring cloud presenta le limitazioni note seguenti:
    
* `spring.application.name` verrà eseguito l'override del nome dell'applicazione usato per creare ogni applicazione.
* `server.port` il valore predefinito è la porta 1025. Se viene applicato un altro valore, ne verrà eseguito l'override. Osservare anche questa impostazione e non specificare la porta del server nel codice.
* I modelli portale di Azure e Azure Resource Manager non supportano il caricamento di pacchetti dell'applicazione. È possibile caricare i pacchetti dell'applicazione solo distribuendo l'applicazione tramite l'interfaccia della riga di comando di Azure.

### <a name="what-pricing-tiers-are-available"></a>Quali sono i piani tariffari disponibili? 
Quale si deve usare e quali sono i limiti all'interno di ogni livello?
* Azure Spring cloud offre due piani tariffari: Basic e standard. Il livello Basic è destinato a sviluppo/test e a provare Azure Spring cloud. Il livello standard è ottimizzato per eseguire il traffico di produzione per utilizzo generico. Vedere [i dettagli sui prezzi di Azure Spring cloud](https://azure.microsoft.com/pricing/details/spring-cloud/) per i limiti e il confronto a livello di funzionalità.

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Come è possibile fornire feedback e segnalare problemi?

Se si verificano problemi con Azure Spring cloud, creare una [richiesta di supporto di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). Per inviare una richiesta di funzionalità o fornire commenti e suggerimenti, passare a [commenti e suggerimenti su Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Sviluppo

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Sono uno sviluppatore di Spring cloud, ma è una novità di Azure. Qual è il modo più rapido per imparare a sviluppare un'applicazione Azure Spring cloud?

Per il modo più rapido per iniziare a usare Azure Spring cloud, seguire le istruzioni riportate nella [Guida introduttiva: avviare un'applicazione Azure Spring cloud usando il portale di Azure](spring-cloud-quickstart.md).

::: zone pivot="programming-language-java"
### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Quale runtime Java supporta Azure Spring Cloud?

Azure Spring Cloud supporta Java 8 e 11. Vedere [le versioni del runtime e del sistema operativo Java](#java-runtime-and-os-versions)

### <a name="is-spring-boot-24x-supported"></a>Spring Boot 2.4. x è supportato?
È stato identificato un problema con Spring Boot 2,4 che attualmente collabora con la community di Spring per risolverlo. Nel frattempo, includere queste due dipendenze per abilitare l'autenticazione TLS tra le app e Eureka.

```xml
<dependency> 
    <groupId>com.sun.jersey</groupId>
    <artifactId>jersey-client</artifactId>
    <version>1.19.4</version>
</dependency>
<dependency>
    <groupId>com.sun.jersey.contribs</groupId>
    <artifactId>jersey-apache-client4</artifactId>
    <version>1.19.4</version>
</dependency>
```

::: zone-end

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Dove è possibile visualizzare i log e le metriche dell'applicazione Spring cloud?

Le metriche sono disponibili nella scheda di panoramica dell'app e nella scheda [Monitoraggio di Azure](../azure-monitor/essentials/data-platform-metrics.md#metrics-explorer).

Il cloud Spring di Azure supporta l'esportazione di metriche e log applicazioni di Spring cloud in archiviazione di Azure, EventHub e [log Analytics](../azure-monitor/logs/data-platform-logs.md). Il nome della tabella in Log Analytics è *AppPlatformLogsforSpring*. Per informazioni su come abilitarla, vedere [servizi di diagnostica](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud supporta la traccia distribuita?

Sì. Per altre informazioni, vedere [esercitazione: usare la traccia distribuita con Azure Spring cloud](spring-cloud-howto-distributed-tracing.md).

::: zone pivot="programming-language-java"
### <a name="what-resource-types-does-service-binding-support"></a>Quali tipi di risorse supporta il binding di servizi?

Attualmente sono supportati tre servizi:
* Azure Cosmos DB
* Database di Azure per MySQL
* Cache Redis di Azure.
::: zone-end

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>È possibile visualizzare, aggiungere o spostare volumi permanenti dall'interno delle applicazioni?

Sì.

### <a name="how-many-outbound-public-ip-addresses-does-an-azure-spring-cloud-instance-have"></a>Quanti indirizzi IP pubblici in uscita sono presenti in un'istanza di Azure Spring cloud?

Il numero di indirizzi IP pubblici in uscita può variare in base ai livelli e ad altri fattori. 

| Tipo di istanza cloud Spring di Azure | Numero predefinito di indirizzi IP pubblici in uscita |
| -------------------------------- | ---------------------------------------------- |
| Istanze del livello Basic             | 1                                              |
| Istanze del livello standard          | 2                                              |
| Istanze di VNet Injection         | 1                                              |


### <a name="can-i-increase-the-number-of-outbound-public-ip-addresses"></a>È possibile aumentare il numero di indirizzi IP pubblici in uscita?

Sì, è possibile aprire un [ticket di supporto](https://azure.microsoft.com/support/faq/)  per richiedere altri indirizzi IP pubblici in uscita.

### <a name="when-i-deletemove-an-azure-spring-cloud-service-instance-will-its-extension-resources-be-deletedmoved-as-well"></a>Quando si elimina o si sposta un'istanza del servizio cloud di Azure Spring, le risorse di estensione verranno eliminate o spostate?

Dipende dalla logica dei provider di risorse che possiedono le risorse di estensione. Le risorse di estensione di un' `Microsoft.AppPlatform` istanza non appartengono allo stesso spazio dei nomi, quindi il comportamento varia a seconda del provider di risorse. Ad esempio, l'operazione di eliminazione/spostamento non produrrà a catena le risorse delle **impostazioni di diagnostica** . Se viene eseguito il provisioning di una nuova istanza di Azure Spring cloud con lo stesso ID di risorsa di quello eliminato o se viene spostata di nuovo l'istanza di Azure Spring cloud precedente, le risorse delle **impostazioni di diagnostica** precedenti continuano a estenderlo.

È possibile eliminare le impostazioni di diagnostica di Spring cloud usando l'interfaccia della riga di comando di Azure:

```azurecli
 az monitor diagnostic-settings delete --name $diagnosticSettingName --resource $azureSpringCloudResourceId
```

::: zone pivot="programming-language-java"
## <a name="java-runtime-and-os-versions"></a>Runtime Java e versioni del sistema operativo

### <a name="which-versions-of-java-runtime-are-supported-in-azure-spring-cloud"></a>Quali versioni di Java Runtime sono supportate nel cloud Spring di Azure?

Azure Spring cloud supporta le versioni Java LTS con le build più recenti, attualmente il 2020 giugno, Java 8 e Java 11 sono supportate. Vedere [Install the JDK for Azure and Azure stack](/azure/developer/java/fundamentals/java-jdk-install)

### <a name="who-built-these-java-runtimes"></a>Chi ha creato questi runtime Java?

Sistemi Azul. Le build di JDK Azul Zulu per Azure - Enterprise Edition sono distribuzioni di OpenJDK gratuite, multipiattaforma e pronte per la produzione per Azure e Azure Stack, supportate da Microsoft e Azul Systems. Contengono tutti i componenti necessari per compilare ed eseguire applicazioni Java SE.

### <a name="how-often-will-java-runtimes-get-updated"></a>Con quale frequenza vengono aggiornati i runtime Java?

Le versioni di JDK con supporto a medio e lungo termine includono aggiornamenti trimestrali di sicurezza e correzioni di bug, oltre a eventuali patch e aggiornamenti critici straordinari necessari. Il supporto include il backporting a Java 7 e 8 degli aggiornamenti di sicurezza e delle correzioni di bug applicate alle versioni più recenti di Java, come Java 11,

### <a name="how-long-will-java-8-and-java-11-lts-versions-be-supported"></a>Per quanto tempo saranno supportate le versioni Java 8 e Java 11 LTS?

Vedere [supporto a lungo termine Java per Azure e Azure stack](/azure/developer/java/fundamentals/java-jdk-long-term-support).

* Java 8 LTS sarà supportato fino al 2030 dicembre.
* Java 11 LTS sarà supportato fino al 2027 settembre.

### <a name="how-can-i-download-a-supported-java-runtime-for-local-development"></a>Come è possibile scaricare un runtime Java supportato per lo sviluppo locale?

Vedere [Install the JDK for Azure and Azure stack](/azure/developer/java/fundamentals/java-jdk-install).

### <a name="what-is-the-retire-policy-for-older-java-runtimes"></a>Quali sono i criteri di ritiro per i runtime Java precedenti?

Il preavviso pubblico verrà inviato a 12 mesi prima del ritiro di qualsiasi versione precedente del runtime. La migrazione a una versione successiva avrà 12 mesi.

* Gli amministratori della sottoscrizione riceveranno una notifica tramite posta elettronica quando la versione di Java viene ritirata.
* Le informazioni di ritiro verranno pubblicate nella documentazione.

### <a name="how-can-i-get-support-for-issues-at-the-java-runtime-level"></a>Come è possibile ottenere supporto per i problemi a livello di runtime Java?

È possibile aprire un ticket di supporto con il supporto tecnico di Azure.  Vedere [come creare una richiesta di supporto di Azure](../azure-portal/supportability/how-to-create-azure-support-request.md).

### <a name="what-is-the-operation-system-to-run-my-apps"></a>Qual è il sistema operativo per l'esecuzione di app personali?

Viene usata la versione più recente di Ubuntu LTS, attualmente [ubuntu 20,04 LTS (fossa focale)](https://releases.ubuntu.com/focal/) è il sistema operativo predefinito.

### <a name="how-often-are-os-security-patches-applied"></a>Con quale frequenza vengono applicate le patch di sicurezza del sistema operativo?

Le patch di sicurezza applicabili al cloud Spring di Azure vengono implementate in produzione su base mensile.
Le patch di sicurezza critiche (il Punteggio CVE >= 9) applicabili al cloud Spring di Azure vengono implementate appena possibile.
::: zone-end

## <a name="deployment"></a>Distribuzione

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring cloud supporta la distribuzione Blue-Green?
Sì. Per ulteriori informazioni, vedere la pagina relativa alla [configurazione di un ambiente di gestione temporanea](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>È possibile accedere a Kubernetes per manipolare i contenitori dell'applicazione?

No.  Azure Spring Cloud astrae lo sviluppatore dall'architettura sottostante, consentendogli di concentrarsi sul codice dell'applicazione e sulla logica di business.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud supporta la creazione di contenitori dall'origine?

Sì. Per altre informazioni, vedere [avviare l'applicazione Spring cloud dal codice sorgente](spring-cloud-quickstart.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud supporta il ridimensionamento automatico nelle istanze di app?

Sì.  Per altre informazioni, vedere [configurare la scalabilità](spring-cloud-tutorial-setup-autoscale.md)automatica.

::: zone pivot="programming-language-java"
### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Quali sono le procedure consigliate per la migrazione di microservizi cloud primaverili esistenti al cloud Spring di Azure?

Quando si esegue la migrazione di microservizi cloud primaverili esistenti al cloud Spring di Azure, è consigliabile osservare le procedure consigliate seguenti:
* È necessario risolvere tutte le dipendenze dell'applicazione.
* Preparare le voci di configurazione, le variabili di ambiente e i parametri JVM in modo da consentirne il confronto con la distribuzione nel cloud Spring di Azure.
* Se si vuole usare l'associazione al servizio, esaminare i servizi di Azure e assicurarsi di avere impostato le autorizzazioni di accesso appropriate.
* Si consiglia di rimuovere o disabilitare i servizi incorporati che potrebbero essere in conflitto con i servizi gestiti da Azure Spring cloud, ad esempio il servizio di individuazione dei servizi, il server di configurazione e così via.
* Si consiglia di usare le librerie di primavera pivotal stabili e ufficiali. Le versioni non ufficiale, beta o con fork delle librerie di Spring pivot non hanno supporto per il contratto di servizio (SLA).

Dopo la migrazione, monitorare le metriche della CPU/RAM e il traffico di rete per assicurarsi che le istanze dell'applicazione vengano ridimensionate in modo appropriato.
::: zone-end

::: zone pivot="programming-language-csharp"
## <a name="net-core-versions"></a>Versioni di .NET Core

### <a name="which-net-core-versions-are-supported"></a>Quali versioni di .NET Core sono supportate?

.NET Core 3,1 e versioni successive.

### <a name="how-long-will-net-core-31-be-supported"></a>Per quanto tempo sarà supportato .NET Core 3,1?

Fino al 3 dicembre 2022. Vedere [criteri di supporto di .NET Core](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).
::: zone-end


## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="what-are-the-impacts-of-service-registry-rarely-unavailable"></a>Quali sono gli effetti del registro di sistema del servizio raramente non disponibili?

In alcuni casi, è possibile che vengano visualizzati alcuni errori come 
```
RetryableEurekaHttpClient: Request execution failure with status code 401; retrying on another server if available
```
dai log delle applicazioni. Questo problema è stato introdotto da Spring Framework con frequenza molto bassa a causa di problemi di rete instabili o di altro genere. 

Non dovrebbe esserci alcun effetto sull'esperienza utente. il client Eureka dispone di criteri di heartbeat e di ripetizione dei tentativi per gestire questo problema. È possibile considerarlo come un errore temporaneo e ignorarlo in modo sicuro.

Questa parte verrà migliorata ed evitare questo errore dalle applicazioni degli utenti in breve futuro.


## <a name="next-steps"></a>Passaggi successivi

Per altre domande, vedere la guida alla [risoluzione dei problemi di Azure Spring cloud](spring-cloud-troubleshoot.md).
