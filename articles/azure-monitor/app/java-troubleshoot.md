---
title: Risoluzione dei problemi di Application Insights in un progetto Web Java
description: Guida per la risoluzione dei problemi - monitoraggio di app Java live con Application Insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: ecc9a298d122919138683b48527574a1ff3e5edc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84484787"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Risoluzione dei problemi e domande e risposte relative ad Application Insights per Java
Domande o problemi relativi ad [Azure Application Insights in Java][java]? Ecco alcuni suggerimenti.

## <a name="build-errors"></a>Errori di compilazione
**In Eclipse o Intellij Idea quando si aggiunge l'SDK di Application Insights in Eclipse i in Intellij Idea tramite Maven o Gradle, vengono visualizzati errori di convalida relativi a build o checksum.**

* Se l'elemento di dipendenza `<version>` usa un criterio di ricerca con caratteri jolly, ad esempio (Maven) `<version>[2.0,)</version>` o (Gradle) `version:'2.0.+'`, provare a indicare una versione specifica, come `2.0.1`. Vedere le [note sulla versione](https://github.com/Microsoft/ApplicationInsights-Java/releases) per la versione più recente.

## <a name="no-data"></a>Nessun dato
**Dopo avere aggiunto Application Insights correttamente ed avere eseguito l'app, nel portale non vengono visualizzati dati.**

* Attendere un minuto, quindi fare clic su Aggiorna. I grafici si aggiornano autonomamente periodicamente, ma è anche possibile aggiornare manualmente. L'intervallo di aggiornamento dipende dall'intervallo di tempo del grafico.
* Verificare di disporre di una chiave di strumentazione definita nel file ApplicationInsights.xml, presente nella cartella delle risorse del progetto, o configurata come variabile di ambiente.
* Verificare che non vi siano nodi `<DisableTelemetry>true</DisableTelemetry>` nel file XML.
* Nel firewall, potrebbe essere necessario aprire le porte TCP 80 e 443 per il traffico in uscita verso dc.services.visualstudio.com. Vedere l' [elenco completo delle eccezioni del firewall](../../azure-monitor/app/ip-addresses.md)
* Nella schermata iniziale di Microsoft Azure osservare la mappa dello stato dei servizi. Se ci sono indicazioni di avviso, attendere che tornino alla normalità, quindi chiudere e riaprire il pannello dell'applicazione di Application Insights.
* [Attivare la registrazione](#debug-data-from-the-sdk) aggiungendo un `<SDKLogger />` elemento sotto il nodo radice nel file di ApplicationInsights.xml (nella cartella risorse del progetto) e cercare le voci precedute da intelligenza artificiale: informazioni/avvisi/errori per eventuali log sospetti. 
* Per verificare che Java SDK abbia caricato il file ApplicationInsights.xml corretto, esaminare i messaggi di output della console e cercare il messaggio "Configuration file has been successfully found".
* Se il file di configurazione non è stato trovato, verificare i percorsi di ricerca di questo file nei messaggi di output e assicurarsi che il file ApplicationInsights.xml sia memorizzato in uno di tali percorsi. In linea di massima, il file di configurazione può essere salvato accanto ai file JAR di Application Insights SDK. In Tomcat, ad esempio, questo percorso corrisponde alla cartella WEB-INF/classes. Durante lo sviluppo è possibile inserire ApplicationInsights.xml nella cartella delle risorse del progetto Web.
* Esaminare anche la [pagina dei problemi di GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues) per i problemi noti con l'SDK.
* Assicurarsi di usare la stessa versione degli appender di core, Web, agente e registrazione di Application Insights per evitare eventuali problemi di conflitto di versione.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Non vengono più visualizzati i dati disponibili in precedenza
* È stata raggiunta la quota mensile relativa ai punti dati? Aprire Impostazioni/quota e prezzi per individuarli. In tal caso, è possibile aggiornare il piano o pagare per la capacità aggiuntiva. Vedere lo [schema dei prezzi](https://azure.microsoft.com/pricing/details/application-insights/).
* L'SDK è stato aggiornato di recente? Verificare che all'interno della directory del progetto siano presenti solo file con estensione JAR dell'SDK univoco. Non devono essere presenti due versioni diverse dell'SDK.
* È stata considerata la risorsa AI corretta? Confrontare la chiave di strumentazione dell'applicazione con la risorsa in cui sono previsti i dati di telemetria. I valori devono corrispondere.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Non sono presenti tutti i dati previsti
* Aprire la pagina Utilizzo e costi stimati e controllare se il [campionamento](../../azure-monitor/app/sampling.md) è in esecuzione. (100% di trasmissione significa che il campionamento non è in esecuzione). Il servizio Application Insights può essere impostato in modo da accettare solo una frazione dei dati di telemetria che arrivano dall'app. Ciò permette di evitare il superamento della quota mensile.
* Il campionamento dell'SDK è attivo? In caso affermativo, i dati potrebbero essere campionati alla frequenza specificata per tutti i tipi applicabili.
* Si esegue una versione precedente dell'SDK di Java? A partire dalla versione 2.0.1 è stato introdotto il meccanismo di tolleranza di errore per gestire i problemi di intermittenza della rete e gli errori di back-end, oltre alla persistenza dei dati sulle unità locali.
* Si è limitati a causa di un numero eccessivo di dati di telemetria? Se attiva la registrazione delle informazioni, verrà visualizzato un messaggio del log "App is throttled" (L'app è limitata). Il limite corrente è 32.000 elementi di telemetria/secondo.

### <a name="java-agent-cannot-capture-dependency-data"></a>L'agente Java non riesce ad acquisire i dati di dipendenza
* L'agente Java è stato configurato seguendo l'articolo sulla [configurazione dell'agente Java](java-agent.md)?
* Verificare che sia il file con estensione JAR dell'agente Java che il file AI-Agent.xml si trovino nella stessa cartella.
* Assicurarsi che la dipendenza che si sta tentando di raccogliere automaticamente sia supportata per la raccolta automatica. Attualmente sono supportate solo le raccolte delle dipendenze di MySQL, MsSQL, Oracle DB e Cache Redis di Azure.

## <a name="no-usage-data"></a>Dati di utilizzo non presenti
**I dati relativi a richieste e tempi di risposta vengono visualizzati, ma non sono presenti dati relativi alla visualizzazione di pagine, ai browser o dati relativi agli utenti.**

L'app è stata correttamente configurata per inviare dati di telemetria dal server. Il passaggio successivo consiste nel [configurare le pagine Web per inviare dati di telemetria dal Web browser][usage].

In alternativa, se il client è un'app in un [telefono o altro dispositivo][platforms], è possibile inviare i dati di telemetria da tali dispositivi.

Usare la chiave di strumentazione per impostare la telemetria sia sul client che sul server. I dati verranno visualizzati nella stessa risorsa di Application Insights e sarà possibile correlare eventi dal client e dal server.


## <a name="disabling-telemetry"></a>Disabilitazione della telemetria
**In che modo è possibile disabilitare la raccolta di dati di telemetria?**

Nel codice:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**O**

Aggiornare ApplicationInsights.xml (nella cartella resources del progetto). Aggiungere la riga seguente sotto il nodo radice:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Usando il metodo XML, dopo la modifica di questo valore sarà necessario riavviare l'applicazione.

## <a name="changing-the-target"></a>Modifica della destinazione
**Come è possibile modificare la risorsa di Azure a cui il progetto invia i dati?**

* [Ottenere la chiave di strumentazione della nuova risorsa][java].
* Se si è aggiunto Application Insights al progetto usando Azure Toolkit for Eclipse, fare clic con il pulsante destro del mouse sul progetto Web, selezionare **Azure**, **Configura Application Insights**, quindi modificare la chiave.
* Se la chiave di strumentazione è stata configurata come variabile di ambiente aggiornare il valore della variabile di ambiente con la nuova chiave di strumentazione.
* Oppure, aggiornare la chiave nel file ApplicationInsights.xml presente nella cartella resources del progetto.

## <a name="debug-data-from-the-sdk"></a>Debug dei dati dall'SDK

**Come ottenere informazioni sulle attività dell'SDK**

Per ottenere altre informazioni su quello che accade nell'API, aggiungere `<SDKLogger/>` sotto il nodo radice del file di configurazione ApplicationInsights.xml.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

È anche possibile impostare il logger perché fornisca un file di output:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Starter Spring boot

Per abilitare la registrazione dell'SDK con le app Spring boot con Application Insights Spring boot Starter, aggiungere il codice seguente al `application.properties` file:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

o per stampare in errore standard:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agente Java

Per abilitare la registrazione dell'agente JVM, aggiornare il [file diAI-Agent.xml](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Proprietà della riga di comando Java
_Dalla versione 2.4.0_

Per abilitare la registrazione usando le opzioni della riga di comando, senza modificare i file di configurazione:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

o per stampare in errore standard:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Schermata iniziale di Azure
**Sto esaminando [il portale di Azure](https://portal.azure.com). La mappa mi dice qualcosa sull'app?**

No, la mappa visualizza lo stato di integrità dei server Azure nelle varie parti del mondo.

*Dalla schermata iniziale (home) di Azure, in che modo è possibile trovare i dati relativi all'app?*

Supponendo che [l'app sia stata impostata per Application Insights][java], fare clic su Esplora, selezionare Application Insights, quindi selezionare la risorsa dell'app creata per l'app specifica. Per raggiungere questa area più velocemente in futuro, è possibile aggiungere l'app alla schermata iniziale.

## <a name="intranet-servers"></a>Server Intranet
**È possibile monitorare un server nella Intranet?**

Sì, purché il server possa inviare dati di telemetria al portale di Application Insights tramite la rete Internet pubblica.

Nel firewall, potrebbe essere necessario aprire le porte TCP 80 e 443 per il traffico in uscita verso dc.services.visualstudio.com e f5.services.visualstudio.com.

## <a name="data-retention"></a>Conservazione dei dati
**Per quanto tempo vengono conservati i dati nel portale? Tale conservazione è sicura?**

Vedere [Conservazione dei dati e privacy][data].

## <a name="debug-logging"></a>Registrazione del debug
Application Insights usa `org.apache.http`. Questo è stato spostato all'interno dei file con estensione jar core di Application Insights nello spazio dei nomi `com.microsoft.applicationinsights.core.dependencies.http`. In questo modo Application Insights può gestire gli scenari in cui diverse versioni dello stesso `org.apache.http` coesistono in una base codice.

>[!NOTE]
>Se si abilita la registrazione a livello di DEBUG per tutti gli spazi dei nomi nell'app, verrà rispettata da tutti i moduli in esecuzione incluso `org.apache.http` rinominato come `com.microsoft.applicationinsights.core.dependencies.http`. Application Insights non sarà in grado di applicare il filtro per queste chiamate perché la chiamata del registro è stata effettuata dalla libreria di Apache. La registrazione a livello di DEBUG produce una considerevole quantità di dati del registro e non è consigliata per le istanze di produzione in tempo reale.


## <a name="next-steps"></a>Passaggi successivi
**Application Insights è stato correttamente impostato per l'app server Java. Cos'altro è possibile fare?**

* [Monitorare la disponibilità delle pagine Web][availability]
* [Monitorare l'utilizzo delle pagine Web][usage]
* [Tenere traccia dell'utilizzo delle app per dispositivi e diagnosticarne i problemi][platforms]
* [Scrivere codice per tenere traccia dell'utilizzo dell'app][track]
* [Acquisire i log di diagnostica][javalogs]

## <a name="get-help"></a>Ottieni supporto
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Registrare un problema su GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

