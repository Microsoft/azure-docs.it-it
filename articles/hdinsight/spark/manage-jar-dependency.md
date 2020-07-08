---
title: Gestire le dipendenze JAR-Azure HDInsight
description: Questo articolo illustra le procedure consigliate per la gestione delle dipendenze di archivio Java (JAR) per le applicazioni HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/05/2020
ms.openlocfilehash: e3807f54de672642bfc6454cf38babbf189c210a
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086076"
---
# <a name="jar-dependency-management-best-practices"></a>Procedure consigliate per la gestione delle dipendenze JAR

I componenti installati nei cluster HDInsight hanno dipendenze da librerie di terze parti. In genere, questi componenti incorporati fanno riferimento a una versione specifica dei moduli comuni come guava. Quando si invia un'applicazione con le relative dipendenze, può verificarsi un conflitto tra versioni diverse dello stesso modulo. Se la versione del componente a cui si fa riferimento prima nel classpath, i componenti predefiniti possono generare eccezioni a causa dell'incompatibilità della versione. Tuttavia, se i componenti predefiniti inseriscono le rispettive dipendenze prima del classpath, l'applicazione potrebbe generare errori come `NoSuchMethod` .

Per evitare conflitti di versione, è consigliabile ombreggiare le dipendenze dell'applicazione.

## <a name="what-does-package-shading-mean"></a>Cosa significa l'ombreggiatura del pacchetto?
L'ombreggiatura fornisce un modo per includere e rinominare le dipendenze. Consente di rilocare le classi e di riscrivere le risorse e il bytecode interessati per creare una copia privata delle dipendenze.

## <a name="how-to-shade-a-package"></a>Come ombreggiare un pacchetto?

### <a name="use-uber-jar"></a>Usare uber-jar
Uber-jar è un singolo file jar che contiene il file jar dell'applicazione e le relative dipendenze. Le dipendenze in uber-jar non sono ombreggiate per impostazione predefinita. In alcuni casi, è possibile che si verifichi un conflitto di versione se altri componenti o applicazioni fanno riferimento a una versione diversa di tali librerie. Per evitare questo problema, è possibile compilare un file uber-jar con alcune o tutte le dipendenze ombreggiate.

### <a name="shade-package-using-maven"></a>Ombreggiatura di un pacchetto con Maven
Maven può compilare applicazioni scritte sia in Java che in scala. Maven-Shade-plugin può aiutarti a creare con facilità un uber-jar ombreggiato.

L'esempio seguente illustra un file `pom.xml` che è stato aggiornato per ombreggiare un pacchetto con Maven-Shade-plugin.  La sezione XML `<relocation>…</relocation>` Sposta le classi dal pacchetto al `com.google.guava` pacchetto `com.google.shaded.guava` spostando le voci del file jar corrispondenti e riscrivendo il bytecode interessato.

Dopo `pom.xml` la modifica, è possibile eseguire `mvn package` per compilare il file uber-jar ombreggiato.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Ombreggiatura di un pacchetto con SBT
SBT è anche uno strumento di compilazione per scala e Java. SBT non dispone di un plug-in Shade come Maven-Shade-plugin. È possibile modificare il `build.sbt` file in modo da ombreggiare i pacchetti. 

Ad esempio, per ombreggiare `com.google.guava` , è possibile aggiungere il comando seguente al `build.sbt` file:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

Quindi, è possibile eseguire `sbt clean` e `sbt assembly` per compilare il file jar ombreggiato. 

## <a name="next-steps"></a>Passaggi successivi

* [Usare gli strumenti IntelliJ di HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Creare un'applicazione scala Maven per Spark in IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
