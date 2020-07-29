---
title: Esplorare i log di traccia Java in applicazione Azure Insights
description: Eseguire la ricerca di tracce Log4J o Logback in Application Insights
ms.topic: conceptual
ms.date: 05/18/2019
ms.openlocfilehash: 417462da431d8fe2ba878c93c6f4b467bdde2670
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324166"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Esplorare i log di traccia Java in Application Insights
Se si usa Logback o Log4J (v1.2 o v2.0) per la traccia, è possibile inviare automaticamente i log di traccia ad Application Insights dove è possibile esplorarli e eseguirvi ricerche.

> [!TIP]
> È necessario impostare la chiave di strumentazione Application Insights una sola volta per l'applicazione. Se si usa un Framework come Java Spring, è possibile che la chiave sia già stata registrata in un'altra posizione nella configurazione dell'app.

## <a name="using-the-application-insights-java-agent"></a>Uso dell'agente Java Application Insights

Per impostazione predefinita, l'agente Java Application Insights acquisisce automaticamente la registrazione eseguita a `WARN` livello e superiore.

È possibile modificare la soglia di registrazione acquisita usando il `AI-Agent.xml` file:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

È possibile disabilitare l'acquisizione della registrazione dell'agente Java usando il `AI-Agent.xml` file:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>In alternativa, invece di usare l'agente Java, è possibile seguire le istruzioni riportate di seguito.

### <a name="install-the-java-sdk"></a>Installare Java SDK

Seguire le istruzioni per installare [Application Insights SDK per Java][java], se questa operazione non è già stata eseguita.

### <a name="add-logging-libraries-to-your-project"></a>Aggiungere le librerie di registrazione al progetto
*Scegliere il modo più appropriato per il progetto.*

#### <a name="if-youre-using-maven"></a>Se si usa Maven...
Se il progetto è già stato configurato per usare Maven per la compilazione, aggiungere uno dei frammenti di codice seguenti nel file pom.xml.

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Se si usa Gradle...
Se il progetto è già configurato per usare Gradle per la compilazione, aggiungere una delle righe seguenti al gruppo `dependencies` nel file build.gradle:

Aggiornare quindi le dipendenze progetto per fare in modo che i file binari vengano scaricati.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>In caso contrario...
Seguire le linee guida per installare manualmente Application Insights SDK per Java, scaricare il file jar (dopo aver raggiunto la pagina del repository centrale Maven, fare clic sul collegamento "jar" nella sezione di download) per l'appender appropriato e aggiungere il file jar dell'appender scaricato al progetto.

| Logger | Download | Libreria |
| --- | --- | --- |
| Logback |[Jar dell'appender logback](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Jar dell'appender Log4J versione 2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[Jar dell'appender Log4J versione 1.2](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>Aggiungere l'appender per il framework di registrazione
Per iniziare la raccolta di tracce, unire il frammento di codice rilevante al file di configurazione Log4J o Logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

È possibile fare riferimento agli appender di Application Insights da qualsiasi logger configurato e non necessariamente dal logger principale (come illustrato negli esempi di codice riportati sopra).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Esplorare le tracce nel portale Application Insights.
Ora che è stato configurato il progetto per inviare tracce in Application Insights, è possibile visualizzare e cercare queste tracce nel portale di Application Insights nel pannello [Ricerca][diagnostic].

Le eccezioni inviate tramite logger vengono visualizzate nel portale come telemetria delle eccezioni.

![Nel portale di Application Insights, aprire Ricerca diagnostica](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Passaggi successivi
[Ricerca diagnostica][diagnostic]

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[java]: java-get-started.md

