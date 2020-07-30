---
title: Avvio rapido per l'uso di Microsoft Identity Platform con un'app Web Java | Azure
description: Informazioni su come implementare l'accesso Microsoft in un'app Web Java usando OpenID Connect
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 1474a8ad73f7deb9a7c321f7d7994a7b2b73d624
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87312402"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Avvio rapido: Aggiungere l'accesso con Microsoft a un'app Web Java

Questo argomento di avvio rapido descrive come integrare un'app Web Java con Microsoft Identity Platform. L'app consentirà l'accesso di un utente, otterrà un token di accesso per chiamare l'API Microsoft Graph e creerà una richiesta all'API Microsoft Graph.

Al termine dell'avvio rapido, l'applicazione accetterà accessi di account Microsoft personali (ad esempio outlook.com, live.com e di altro tipo) e di account aziendali o dell'istituto di istruzione di qualsiasi azienda e organizzazione che usa Azure Active Directory. Per un'illustrazione, vedere [Funzionamento dell'esempio](#how-the-sample-works).

## <a name="prerequisites"></a>Prerequisiti

Ecco i prerequisiti per eseguire questo esempio:

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 o versione successiva e [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrare e scaricare l'app della guida introduttiva
> Per avviare l'applicazione della guida di avvio rapido sono disponibili due opzioni: rapida (Opzione 1) o manuale (Opzione 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opzione 1: Registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice
>
> 1. Passare all'esperienza di avvio rapido [Portale di Azure - Registrazioni app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs).
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni nell'esperienza di avvio rapido del portale per scaricare il codice dell'applicazione configurato automaticamente.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opzione 2: Registrare e configurare manualmente l'applicazione e il codice di esempio
>
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
>
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app all'applicazione, seguire questa procedura:
>
> 1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
> 1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
>
> 1. Passare alla pagina [Registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) di Microsoft Identity Platform per sviluppatori.
> 1. Selezionare **Nuova registrazione**.
> 1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione.
>    - Nella sezione **Nome** immettere un nome di applicazione significativo che verrà visualizzato agli utenti dell'app, ad esempio `java-webapp`.
>    - Selezionare **Registra**.
> 1. Nella pagina **Informazioni generali** individuare i valori di **ID applicazione (client)** e **ID della directory (tenant)** dell'applicazione. Copiare questi valori per un utilizzo successivo.
> 1. Selezionare **Autenticazione** dal menu e quindi aggiungere le informazioni seguenti:
>    - Aggiungere la configurazione della piattaforma **Web**.  Aggiungere `https://localhost:8443/msal4jsample/secure/aad` e `https://localhost:8443/msal4jsample/graph/me` come **URI di reindirizzamento**.
>    - Selezionare **Salva**.
> 1. Nel menu a sinistra scegliere **Certificati e segreti** e nella sezione **Segreti client** fare clic su **Nuovo segreto client**:
>
>    - Digitare una descrizione della chiave (ad esempio, segreto dell'app).
>    - Selezionare **Tra 1 anno** per la durata della chiave.
>    - Il valore della chiave viene visualizzato quando si seleziona **Aggiungi**.
>    - Copiare il valore della chiave per un utilizzo successivo. Il valore della chiave non viene visualizzato di nuovo, né può essere recuperato in altro modo, di conseguenza è opportuno prenderne nota non appena viene visualizzato nel portale di Azure.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
>
> Per il corretto funzionamento del codice di esempio di questo argomento di avvio rapido, è necessario:
>
> 1. Aggiungere gli URL di risposta, come `https://localhost:8443/msal4jsample/secure/aad` e `https://localhost:8443/msal4jsample/graph/me`
> 1. Creare un segreto client.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-aspnet-webapp/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-the-code-sample"></a>Passaggio 2: Scaricare il codice di esempio
> [!div renderon="docs"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Scaricare il progetto ed estrarre il file con estensione zip in una cartella locale vicina alla cartella radice, ad esempio **C:\Azure-Samples**
>
> Per usare HTTPS con localhost, compilare le proprietà server.ssl.key. Per generare un certificato autofirmato, usare l'utilità keytool (inclusa in JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Inserire il file dell'archivio chiavi generato nella cartella "resources".

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Passaggio 3: Configurare il codice di esempio
> 1. Estrarre il file ZIP in una cartella locale.
> 1. Se si usa un IDE (Integrated Development Environment), aprire l'esempio al suo interno.
> 1. Aprire il file application.properties, disponibile nella cartella src/main/resources/, e sostituire il valore dei campi *aad.clientId*, *aad.authority* e *aad.secretKey* con i rispettivi valori di **ID applicazione**, **ID tenant** e **Segreto client** come segue:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
> Dove:
>
> - `Enter_the_Application_Id_here` è l'ID applicazione dell'applicazione registrata.
> - `Enter_the_Client_Secret_Here` corrisponde al valore di **Segreto client** creato in **Certificati e segreti** per l'applicazione registrata.
> - `Enter_the_Tenant_Info_Here` - è il valore dell'**ID directory (tenant)** dell'applicazione registrata.
> 1. Per usare HTTPS con localhost, compilare le proprietà server.ssl.key. Per generare un certificato autofirmato, usare l'utilità keytool (inclusa in JRE).
>
>  ```
>   Example:
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Inserire il file dell'archivio chiavi generato nella cartella "resources".


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Passaggio 3: Eseguire il codice di esempio
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Passaggio 4: Eseguire il codice di esempio

Per eseguire il progetto, è possibile effettuare una delle operazioni seguenti:

Eseguirlo direttamente dall'ambiente IDE usando il server Spring Boot incorporato o comprimerlo in un file WAR mediante [maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html) e distribuirlo in una soluzione contenitore J2EE, ad esempio [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-from-ide"></a>Esecuzione dall'ambiente IDE

Se si esegue l'applicazione Web da un ambiente IDE, fare clic su Esegui, quindi passare alla home page del progetto. Per questo esempio, l'URL dell'home page standard è https://localhost:8443

1. Nella pagina iniziale selezionare il pulsante **Login** (Accedi) per essere reindirizzati ad Azure Active Directory e richiedere le credenziali all'utente.

1. Dopo l'autenticazione, l'utente viene reindirizzato a *https://localhost:8443/msal4jsample/secure/aad* . Una volta eseguito l'accesso, nella pagina vengono visualizzate le informazioni sull'account. L'interfaccia utente di esempio presenta i pulsanti seguenti:
    - *Disconnetti*: disconnette l'utente corrente dall'applicazione e lo reindirizza alla home page.
    - *Show User Info* (Mostra informazioni utente): acquisisce un token per Microsoft Graph e chiama Microsoft Graph con una richiesta contenente il token, che restituisce informazioni di base sull'utente che ha eseguito l'accesso.

##### <a name="running-from-tomcat"></a>Esecuzione da Tomcat

Se si vuole distribuire l'esempio Web in Tomcat, sarà necessario apportare alcune modifiche al codice sorgente.

1. Aprire ms-identity-java-webapp/pom.xml
    - In `<name>msal-web-sample</name>` aggiungere `<packaging>war</packaging>`

2. Aprire ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication

    - Eliminare tutto il codice sorgente e sostituirlo con il codice seguente:

   ```Java
    package com.microsoft.azure.msalwebsample;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.boot.builder.SpringApplicationBuilder;
    import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

    @SpringBootApplication
    public class MsalWebSampleApplication extends SpringBootServletInitializer {

     public static void main(String[] args) {
      SpringApplication.run(MsalWebSampleApplication.class, args);
     }

     @Override
     protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
      return builder.sources(MsalWebSampleApplication.class);
     }
    }
   ```

3.   La porta HTTP predefinita di Tomcat è 8080, ma è necessaria una connessione HTTPS sulla porta 8443. Per eseguire questa configurazione:
        - Passare a tomcat/conf/server.xml
        - Cercare il tag `<connector>` e sostituire il connettore esistente con:
        ```
        <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
        ``` 
       
4. Aprire un prompt dei comandi, passare alla cartella radice di questo esempio (in cui si trova il file pom.xml) ed eseguire `mvn package` per compilare il progetto
    - Verrà generato un file `msal-web-sample-0.1.0.war` nella directory /targets.
    - Rinominare questo file in `msal4jsample.war`.
    - Distribuire questo file con estensione war con Tomcat o qualsiasi altra soluzione contenitore J2EE.
        - Per eseguire la distribuzione, copiare il file msal4jsample.war nella directory `/webapps/` dell'installazione di Tomcat e quindi avviare il server Tomcat.

5. Al termine, passare a https://localhost:8443/msal4jsample nel browser


> [!IMPORTANT]
> Questa applicazione della guida introduttiva usa un segreto client per identificarsi come client riservato. Poiché il segreto client viene aggiunto come testo normale ai file di progetto, per motivi di sicurezza è consigliabile usare un certificato anziché un segreto client prima di considerare l'applicazione come applicazione di produzione. Per altre informazioni sull'utilizzo di un certificato, vedere [Credenziali del certificato per l'autenticazione dell'applicazione](https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials).

## <a name="more-information"></a>Ulteriori informazioni

### <a name="how-the-sample-works"></a>Funzionamento dell'esempio
![Mostra come funziona l'app di esempio generata da questo avvio rapido](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="getting-msal"></a>Recupero di MSAL

MSAL per Java (MSAL4J) è la libreria Java usata per far accedere gli utenti e richiedere i token usati per accedere a un'API protetta da Microsoft Identity Platform.

Aggiungere MSAL4J all'applicazione usando Maven o Gradle per gestire le dipendenze apportando le modifiche seguenti al file pom.xml (Maven) o build.gradle (Gradle) dell'applicazione.

In pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

In build.gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

Aggiungere un riferimento a MSAL per Java aggiungendo il codice seguente all'inizio del file in cui verrà usato MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle autorizzazioni e sul consenso:

> [!div class="nextstepaction"]
> [Autorizzazioni e consenso](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

Per altre informazioni sul flusso di autenticazione per questo scenario, vedere il flusso del codice di autorizzazione Oauth 2.0:

> [!div class="nextstepaction"]
> [Flusso Oauth del codice di autorizzazione](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
