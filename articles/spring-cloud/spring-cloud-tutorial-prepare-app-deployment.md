---
title: Come preparare un'applicazione per la distribuzione nel cloud Spring di Azure
description: Informazioni su come preparare un'applicazione per la distribuzione nel cloud Spring di Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 4e9c84efe7b96cf61a69c54e3f5ecbc469ac7d8d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98874143"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Preparare un'applicazione per la distribuzione nel cloud Spring di Azure

::: zone pivot="programming-language-csharp"
Azure Spring cloud offre servizi affidabili per l'hosting, il monitoraggio, la scalabilità e l'aggiornamento di un'app Steeltoe. Questo articolo illustra come preparare un'applicazione Steeltoe esistente per la distribuzione nel cloud Spring di Azure. 

Questo articolo illustra le dipendenze, la configurazione e il codice necessari per eseguire un'app Steeltoe .NET Core nel cloud Spring di Azure. Per informazioni su come distribuire un'applicazione nel cloud Spring di Azure, vedere [distribuire la prima applicazione Azure Spring cloud](spring-cloud-quickstart.md).

>[!Note]
> Il supporto di Steeltoe per Azure Spring Cloud è attualmente disponibile come anteprima pubblica. Le offerte di anteprima pubblica consentono ai clienti di sperimentare le nuove funzionalità prima del rilascio della versione ufficiale.  I servizi e le funzionalità di anteprima pubblica non sono destinati all'uso in produzione.  Per altre informazioni sul supporto durante le anteprime, vedere le [domande frequenti](https://azure.microsoft.com/support/faq/) o inviare una [richiesta di supporto](../azure-portal/supportability/how-to-create-azure-support-request.md).

##  <a name="supported-versions"></a>Versioni supportate

Il cloud Spring di Azure supporta:

* .NET Core 3.1
* Steeltoe 2,4 e 3,0

## <a name="dependencies"></a>Dependencies

Per Steeltoe 2,4, aggiungere il pacchetto [Microsoft. Azure. SpringCloud. client 1. x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) . x più recente al file di progetto:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

Per Steeltoe 3,0, aggiungere il pacchetto [Microsoft. Azure. SpringCloud. client 2](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) . x. x più recente al file di progetto:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Aggiornare Program.cs.

Nel `Program.Main` metodo chiamare il `UseAzureSpringCloudService` metodo.

Per Steeltoe 2.4.4, chiamare `UseAzureSpringCloudService` after `ConfigureWebHostDefaults` e After `AddConfigServer` se viene chiamato:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

Per Steeltoe 3.0.0, chiamare `UseAzureSpringCloudService` prima `ConfigureWebHostDefaults` e prima di qualsiasi codice di configurazione Steeltoe:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>Abilita individuazione del servizio Server Eureka

Nell'origine configurazione che verrà usata quando l'app viene eseguita nel cloud di Azure Spring, impostare sullo `spring.application.name` stesso nome dell'app Azure Spring cloud in cui verrà distribuito il progetto.

Ad esempio, se si distribuisce un progetto .NET denominato `EurekaDataProvider` a un'app cloud di Azure Spring denominata `planet-weather-provider` il *appSettings.jsnel* file deve includere il codice JSON seguente:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Usa individuazione servizio

Per chiamare un servizio usando l'individuazione del servizio Server Eureka, effettuare richieste HTTP a `http://<app_name>` dove `app_name` è il valore di `spring.application.name` dell'app di destinazione. Il codice seguente, ad esempio, chiama il `planet-weather-provider` servizio:

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
Questo argomento illustra come preparare un'applicazione Java Spring esistente per la distribuzione in Azure Spring Cloud. Se correttamente configurato, Azure Spring Cloud offre servizi affidabili per il monitoraggio, il ridimensionamento e l'aggiornamento dell'applicazione Java Spring Cloud.

Prima di eseguire questo esempio, è possibile provare la [guida di avvio rapido di base](spring-cloud-quickstart.md).

Altri esempi illustrano come distribuire un'applicazione in Azure Spring Cloud quando viene configurato il file POM. 
* [Avviare la prima app](spring-cloud-quickstart.md)
* [Compilare ed eseguire microservizi](spring-cloud-quickstart-sample-app-introduction.md)

Questo articolo descrive le dipendenze necessarie e spiega come aggiungerle al file POM.

## <a name="java-runtime-version"></a>Versione del runtime Java

In Azure Spring Cloud è possibile eseguire solo applicazioni Spring/Java.

Azure Spring Cloud supporta Java 8 e 11. L'ambiente di hosting contiene almeno la versione più recente di Azul Zulu OpenJDK per Azure. Per altre informazioni su Azul Zulu OpenJDK per Azure, vedere [Installare JDK](/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versioni di Spring Boot e Spring Cloud

Per preparare un'applicazione Spring Boot esistente per la distribuzione in Azure Spring Cloud, includere le dipendenze di Spring Boot e Spring Cloud nel file POM dell'applicazione come illustrato nelle sezioni seguenti.

Azure Spring Cloud supporta solo le app Spring Boot versione 2.1 o 2.2. Le combinazioni supportate di Spring Boot e Spring Cloud sono indicate nella tabella seguente:

Versione di Spring Boot | Versione di Spring Cloud
---|---
2.2 | Hoxton. SR8
2.3 | Hoxton. SR8
2.4.1 + | 2020.0.0

> [!NOTE]
> È stato identificato un problema con Spring Boot 2.4.0 sull'autenticazione TLS tra le app e Eureka. usare 2.4.1 o versione successiva. Vedere le [domande frequenti](./spring-cloud-faq.md?pivots=programming-language-java#development) per la soluzione alternativa se si insiste sull'uso di 2.4.0.

### <a name="dependencies-for-spring-boot-version-2223"></a>Dipendenze per Spring Boot versione 2.2/2.3

Per Spring Boot versione 2.2 aggiungere le dipendenze seguenti al file POM dell'applicazione.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-24"></a>Dipendenze per Spring Boot versione 2,4

Per Spring Boot versione 2.2 aggiungere le dipendenze seguenti al file POM dell'applicazione.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2020.0.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

> [!WARNING]
> Non specificare `server.port` nella configurazione. Azure Spring cloud Sostituisci questa impostazione a un numero di porta fisso. Osservare anche questa impostazione e non specificare la porta del server nel codice.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Altre dipendenze consigliate per abilitare le funzionalità di Azure Spring Cloud

Per abilitare le funzionalità predefinite di Azure Spring Cloud dal registro dei servizi alla traccia distribuita, è necessario includere anche le dipendenze seguenti nell'applicazione. È possibile eliminare alcune di queste dipendenze se non sono necessarie le funzionalità corrispondenti per le app specifiche.

### <a name="service-registry"></a>Service Registry

Per usare il servizio Registro servizi di Azure gestito, includere la dipendenza `spring-cloud-starter-netflix-eureka-client` nel file pom.xml, come illustrato di seguito:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

L'endpoint del server Service Registry verrà inserito automaticamente come variabile di ambiente nell'app. Le applicazioni potranno quindi registrarsi con il server Service Registry e individuare altri microservizi dipendenti.


#### <a name="enablediscoveryclient-annotation"></a>Annotazione EnableDiscoveryClient

Aggiungere l'annotazione seguente al codice sorgente dell'applicazione.
```java
@EnableDiscoveryClient
```
Vedere ad esempio l'applicazione piggymetrics negli esempi precedenti:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Configurazione distribuita

Per abilitare la configurazione distribuita, includere la dipendenza `spring-cloud-config-client` seguente nella sezione delle dipendenze del file pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Non specificare `spring.cloud.config.enabled=false` nella configurazione bootstrap. In caso contrario, l'applicazione non funzionerà più con Config Server.

### <a name="metrics"></a>Metriche

Includere la dipendenza `spring-boot-starter-actuator` nella sezione delle dipendenze del file pom.xml, come illustrato di seguito:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Viene periodicamente eseguito il pull delle metriche dagli endpoint JMX. È possibile visualizzare le metriche usando il portale di Azure.

 > [!WARNING]
 > Specificare `spring.jmx.enabled=true` nella proprietà di configurazione. In caso contrario, le metriche non possono essere visualizzate in portale di Azure.

### <a name="distributed-tracing"></a>Traccia distribuita

È anche necessario abilitare un'istanza di Azure Application Insights da usare con l'istanza del servizio Azure Spring Cloud. Per informazioni sull'uso di Application Insights con Azure Spring cloud, vedere la [documentazione relativa alla traccia distribuita](spring-cloud-tutorial-distributed-tracing.md).

#### <a name="spring-boot-2223"></a>Spring Boot 2.2/2.3
Includere le dipendenze `spring-cloud-starter-sleuth` e `spring-cloud-starter-zipkin` nella sezione delle dipendenze del file pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

#### <a name="spring-boot-24"></a>Spring Boot 2,4
Includere la `spring-cloud-sleuth-zipkin` dipendenza seguente nella sezione dipendenze del file di pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>Vedere anche
* [Analizzare i log e le metriche dell'applicazione](./diagnostic-services.md)
* [Configurare Config Server](./spring-cloud-tutorial-config-server.md)
* [Usare la traccia distribuita con Azure Spring Cloud](./spring-cloud-tutorial-distributed-tracing.md)
* [Guida di avvio rapido per Spring](https://spring.io/quickstart)
* [Documentazione di Spring Boot](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Passaggi successivi

In questo argomento si è appreso come configurare l'applicazione Java Spring per la distribuzione in Azure Spring Cloud. Per informazioni su come configurare un'istanza del server di configurazione, vedere [configurare un'istanza del server di configurazione](spring-cloud-tutorial-config-server.md).

Altri esempi sono disponibili in GitHub: [Esempi di Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end