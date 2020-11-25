---
title: Monitoraggio delle prestazioni delle app Web Java-applicazione Azure Insights
description: Estendere il monitoraggio di prestazioni e utilizzo del sito Web Java con Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
ms.custom: devx-track-java
ms.openlocfilehash: e91e0252ac2af02157c30fe025e75b6cfee7de6e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002018"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorare le dipendenze, le eccezioni intercettate e i tempi di esecuzione del metodo nelle app Web Java

> [!IMPORTANT]
> L'approccio consigliato per il monitoraggio delle applicazioni Java consiste nell'usare la strumentazione automatica senza modificare il codice. Seguire le linee guida per [Application Insights agente Java 3,0](./java-in-process-agent.md).

Se l' [app Web Java è stata instrumentata con Application Insights SDK][java], è possibile usare l'agente Java per ottenere informazioni più dettagliate, senza apportare modifiche al codice:

* **Dipendenze:** dati sulle chiamate effettuate dall'applicazione ad altri componenti, tra cui:
  * Le **chiamate http in uscita** effettuate tramite Apache HttpClient, OkHttp e `java.net.HttpURLConnection` vengono acquisite.
  * Vengono acquisite le **chiamate Redis** effettuate tramite il client JEDIS.
  * **Query JDBC** : per MySQL e PostgreSQL, se la chiamata richiede più di 10 secondi, l'agente segnala il piano di query.

* **Registrazione dell'applicazione:** Acquisire e correlare i log delle applicazioni con le richieste HTTP e altri dati di telemetria
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Denominazione delle operazioni migliore:** (usata per l'aggregazione di richieste nel portale)
  * **Spring** in base a `@RequestMapping` .
  * **JAX-RS** in base a `@Path` . 

Per usare l'agente Java, installarlo nel server. Le app Web devono essere instrumentate con [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Installare l'agente di Application Insights per Java
1. [Scaricare l'agente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)sul computer che esegue il server Java. Assicurarsi di scaricare la stessa versione dell'agente Java e dei pacchetti core e Web dell'SDK per Java di Application Insights.
2. Modificare lo script di avvio del server applicazioni e aggiungere l'argomento JVM seguente:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Ad esempio, in Tomcat su un computer Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Riavviare il server applicazioni.

## <a name="configure-the-agent"></a>Configurare l'agente
Creare un file detto `AI-Agent.xml` e inserirlo nella stessa cartella che include il file JAR dell'agente.

Configurare il contenuto del file XML. Modificare l'esempio seguente in modo da includere o escludere le funzionalità desiderate.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Configurazione aggiuntiva (Spring boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Per app Azure Services, eseguire le operazioni seguenti:

* Selezionare Impostazioni > Impostazioni applicazione
* In Impostazioni app aggiungere una nuova coppia chiave-valore:

Chiave: `JAVA_OPTS` valore: `-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Per la versione più recente dell'agente Java, vedere [qui](https://github.com/Microsoft/ApplicationInsights-Java/releases
)le versioni. 

L'agente deve essere incluso nel pacchetto come risorsa nel progetto, in modo che finisca nella directory D:/Home/site/wwwroot/. È possibile verificare che l'agente si trovi nella directory del servizio app corretta passando a **strumenti di sviluppo**  >  **avanzati strumenti**  >  **console di debug** ed esaminando il contenuto della directory del sito.    

* Salvare le impostazioni scegliendo Salva e quindi fare clic su Riavvia per riavviare l'app. Questi passaggi si applicano solo ai servizi app in esecuzione in Windows.

> [!NOTE]
> AI-Agent.xml e il file agente JAR devono essere nella stessa cartella. Vengono spesso inseriti insieme nella cartella `/resources` del progetto.  

#### <a name="enable-w3c-distributed-tracing"></a>Abilitare l'analisi distribuita W3C

Aggiungere il codice seguente al file AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> La modalità di compatibilità con le versioni precedenti è abilitata per impostazione predefinita. Il parametro enableW3CBackCompat è facoltativo e deve essere utilizzato solo quando si desidera disattivare tale funzionalità. 

Ad esempio, quando tutti i servizi sono stati aggiornati alla versione più recente degli SDK che supportano il protocollo W3C. Si raccomanda di passare alla versione più recente degli SDK con supporto W3C il prima possibile.

Assicurarsi che **entrambe le configurazioni [in ingresso](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) e in uscita (agente)** siano identiche.

## <a name="view-the-data"></a>Visualizzare i dati
Nella risorsa Application Insights, le dipendenze Remote aggregate e i tempi di esecuzione dei metodi vengono visualizzati nel [riquadro prestazioni][metrics].

Per cercare singole istanze di dipendenze, eccezioni e report sui metodi, aprire [Ricerca][diagnostic].

[Diagnosi dei problemi di dipendenza - ulteriori informazioni](./asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Domande? Problemi?
* Dati non visualizzati [Impostare le eccezioni del firewall](./ip-addresses.md)
* [Risoluzione dei problemi Java](java-troubleshoot.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#track-exception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../platform/metrics-charts.md

