---
title: Gestire i segreti dell'applicazione Service Fabric di Azure
description: Informazioni su come proteggere i valori dei segreti in un'applicazione di Service Fabric (indipendente dalla piattaforma).
ms.topic: conceptual
ms.date: 01/04/2019
ms.openlocfilehash: af82a55d41c48eebcbcbd1581ec5096a89c49bea
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248119"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Gestire i segreti crittografati nelle applicazioni di Service Fabric
Questa guida descrive la procedura di gestione dei segreti in un'applicazione di Service Fabric. I segreti possono essere informazioni riservate, ad esempio le stringhe di connessione di archiviazione, le password o altri valori che non devono essere gestiti in testo normale.

Per usare i segreti crittografati in un'applicazione di Service Fabric è necessario completare tre passaggi:
* Configurare un certificato di crittografia e crittografare i segreti.
* Specificare i segreti crittografati in un'applicazione.
* Decrittografare i segreti crittografati dal codice del servizio.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Configurare un certificato di crittografia e crittografare i segreti
La configurazione di un certificato di crittografia e il suo utilizzo per crittografare i segreti sono operazioni che vengono eseguite diversamente in Windows rispetto a Linux.
* [Configurare un certificato di crittografia e crittografare segreti in cluster Windows.][secret-management-windows-specific-link]
* [Configurare un certificato di crittografia e crittografare segreti in cluster Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Specificare i segreti crittografati in un'applicazione
Il passaggio precedente illustra come crittografare un segreto con un certificato e produrre una stringa con codifica Base 64 da usare in un'applicazione. Questa stringa con codifica Base 64 può essere specificata come [parametro][parameters-link] crittografato nel file Settings.xml di un servizio o come [variabile di ambiente][environment-variables-link] nel file ServiceManifest.xml di un servizio.

Specificare un [parametro][parameters-link] crittografato nel file di configurazione Settings.xml del servizio con l'attributo `IsEncrypted` impostato su `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Specificare una [variabile di ambiente][environment-variables-link] crittografata nel file ServiceManifest.xml del servizio con l'attributo `Type` impostato su `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

I segreti devono anche essere inclusi nell'applicazione Service Fabric specificando un certificato nel manifesto dell'applicazione. Aggiungere un elemento **SecretsCertificate** a **ApplicationManifest.xml** e includere l'identificazione digitale del certificato desiderato.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> Quando si attiva un'applicazione che specifica un SecretsCertificate, Service Fabric troverà il certificato corrispondente e si concede all'identità che l'applicazione viene eseguita con le autorizzazioni complete per la chiave privata del certificato. Service Fabric monitorerà inoltre il certificato per le modifiche e riapplicare le autorizzazioni di conseguenza. Per rilevare le modifiche per i certificati dichiarati dal nome comune, Service Fabric esegue un'attività periodica che trova tutti i certificati corrispondenti e li confronta con un elenco di identificazioni personali memorizzato nella cache. Quando viene rilevata una nuova identificazione digitale, significa che un certificato da tale soggetto è stato rinnovato. L'attività viene eseguita una volta al minuto in ogni nodo del cluster.
>
> Mentre SecretsCertificate consente dichiarazioni basate sul soggetto, tenere presente che le impostazioni crittografate sono associate alla coppia di chiavi usata per crittografare l'impostazione nel client. È necessario assicurarsi che il certificato di crittografia originale (o equivalente) corrisponda alla dichiarazione basata sul soggetto e che sia installato, inclusa la corrispondente chiave privata, in ogni nodo del cluster che potrebbe ospitare l'applicazione. Tutti i certificati temporali che corrispondono alla dichiarazione basata sul soggetto e compilati dalla stessa coppia di chiavi del certificato di crittografia originale sono considerati equivalenti.
>

### <a name="inject-application-secrets-into-application-instances"></a>Inserire i segreti dell'applicazione nelle istanze dell'applicazione
Idealmente, la distribuzione in ambienti diversi dovrebbe essere il più possibile automatizzata. Questo può essere ottenuto tramite l'esecuzione della crittografia dei segreti in un ambiente di compilazione e l'uso dei segreti crittografati come parametri durante la creazione delle istanze dell'applicazione.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Usare parametri sostituibili in Settings.xml
Il file di configurazione Settings.xml consente di usare parametri sostituibili che possono essere forniti al momento della creazione dell'applicazione. Usare l'attributo `MustOverride` invece di fornire un valore per un parametro:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Per sostituire i valori in Settings.xml, dichiarare un parametro di sostituzione per il servizio in ApplicationManifest.xml:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

A questo punto, il valore può essere specificato come un *parametro dell'applicazione* quando si crea un'istanza dell'applicazione. La creazione di un'istanza dell'applicazione può generare uno script con PowerShell o essere scritta in C#, per semplificare l'integrazione in un processo di compilazione.

Tramite PowerShell, viene fornito il parametro del comando `New-ServiceFabricApplication` come [tabella hash](/previous-versions/windows/it-pro/windows-powershell-1.0/ee692803(v=technet.10)):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Tramite C#, i parametri dell'applicazione vengono specificati in `ApplicationDescription` come `NameValueCollection`:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Decrittografare i segreti crittografati dal codice del servizio
Le API per l'accesso a [parametri][parameters-link] e [variabili di ambiente][environment-variables-link] consentono di decrittografare facilmente i valori crittografati. Poiché la stringa crittografata contiene informazioni sul certificato usato per la crittografia, non è necessario specificare manualmente il certificato. Il certificato deve essere solo installato sul nodo su cui è in esecuzione il servizio.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Passaggi successivi
* [Archivio dei segreti](service-fabric-application-secret-store.md) Service Fabric 
* Altre informazioni sulla [sicurezza dell'applicazione e del servizio](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md
