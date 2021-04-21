---
title: Usare un certificato TLS/SSL nel codice
description: Informazioni su come usare i certificati client nel codice. Eseguire l'autenticazione con risorse remote con un certificato client o eseguire attività di crittografia con esse.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 294587fde846a3774f7d74f64029e0bca00e9c08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829405"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Usare un certificato TLS/SSL nel codice nel Servizio app di Azure

Nel codice dell'applicazione è possibile accedere [ai certificati pubblici o privati aggiunti al servizio app.](configure-ssl-certificate.md) Il codice dell'app può fungere da client e accedere a un servizio esterno che richiede l'autenticazione del certificato oppure potrebbe dover eseguire attività di crittografia. Questa guida dettagliata illustra come usare certificati pubblici o privati nel codice dell'applicazione.

Questo approccio all'uso dei certificati nel codice usa la funzionalità TLS nel servizio app, che richiede che l'app sia nel **livello Basic** o superiore. Se l'app è nel **livello Gratuito** **o Condiviso,** è possibile [includere il file del certificato nel repository dell'app.](#load-certificate-from-file)

Quando si consente al servizio app di gestire i certificati TLS/SSL, è possibile gestire separatamente i certificati e il codice dell'applicazione e proteggere i dati sensibili.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida pratica:

- [Creare un'app del Servizio app](./index.yml)
- [Aggiungere un certificato all'app](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Trovare l'identificazione personale

Nel menu a sinistra del <a href="https://portal.azure.com" target="_blank">portale di Azure</a> scegliere **Servizi app** >  **\<app-name>** .

Nel riquadro di spostamento a sinistra dell'app selezionare Impostazioni **TLS/SSL,** quindi selezionare Certificati a chiave privata (con estensione **pfx)** o Certificati a chiave **pubblica (.cer).**

Trovare il certificato da usare e copiare l'identificazione personale.

![Copiare l'identificazione personale del certificato](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Rendere accessibile il certificato

Per accedere a un certificato nel codice dell'app, aggiungere l'identificazione personale all'impostazione `WEBSITE_LOAD_CERTIFICATES` dell'app eseguendo il comando seguente nel <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Per rendere accessibili tutti i certificati, impostare il valore su `*` .

## <a name="load-certificate-in-windows-apps"></a>Caricare il certificato nelle app di Windows

L'impostazione dell'app rende accessibili i certificati specificati all'app ospitata `WEBSITE_LOAD_CERTIFICATES` in Windows nell'archivio certificati di Windows, in Utente [corrente\My.](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)

Nel codice C# si accede al certificato tramite l'identificazione personale del certificato. Il codice seguente carica un certificato con l'identificazione personale `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

Nel codice Java si accede al certificato dall'archivio "Windows-MY" usando il campo Nome comune soggetto (vedere [Certificato di chiave pubblica](https://en.wikipedia.org/wiki/Public_key_certificate)). Il codice seguente illustra come caricare un certificato di chiave privata:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Per le lingue che non supportano o non offrono supporto insufficiente per l'archivio certificati di Windows, vedere [Caricare il certificato dal file](#load-certificate-from-file).

## <a name="load-certificate-from-file"></a>Caricare il certificato dal file

Se è necessario caricare manualmente un file di certificato caricato, è meglio caricare il certificato usando [FTPS](deploy-ftp.md) anziché [Git,](deploy-local-git.md)ad esempio . È consigliabile mantenere i dati sensibili, ad esempio un certificato privato, fuori dal controllo del codice sorgente.

> [!NOTE]
> ASP.NET e ASP.NET Core in Windows devono accedere all'archivio certificati anche se si carica un certificato da un file. Per caricare un file di certificato in un'app Windows .NET, caricare il profilo utente corrente con il comando seguente <a target="_blank" href="https://shell.azure.com" >nel</a>Cloud Shell :
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Questo approccio all'uso dei certificati nel codice usa la funzionalità TLS nel servizio app, che richiede che l'app sia di livello **Basic** o superiore.

L'esempio C# seguente carica un certificato pubblico da un percorso relativo nell'app:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Per informazioni su come caricare un certificato TLS/SSL da un file in Node.js, PHP, Python, Java o Ruby, vedere la documentazione relativa al linguaggio o alla piattaforma Web corrispondente.

## <a name="load-certificate-in-linuxwindows-containers"></a>Caricare il certificato in contenitori Linux/Windows

Le impostazioni dell'app rendono i certificati specificati accessibili alle app contenitore Windows o `WEBSITE_LOAD_CERTIFICATES` Linux (inclusi i contenitori Linux predefiniti) come file. I file si trovano nelle directory seguenti:

| Piattaforma contenitore | Certificati pubblici | Certificati privati |
| - | - | - |
| Contenitore Windows | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Contenitore Linux | `/var/ssl/certs` | `/var/ssl/private` |

I nomi dei file di certificato sono le identificazioni personale del certificato. 

> [!NOTE]
> Il servizio app inserisce i percorsi dei certificati nei contenitori di Windows come variabili di ambiente `WEBSITE_PRIVATE_CERTS_PATH` `WEBSITE_INTERMEDIATE_CERTS_PATH` , , e `WEBSITE_PUBLIC_CERTS_PATH` `WEBSITE_ROOT_CERTS_PATH` . È meglio fare riferimento al percorso del certificato con le variabili di ambiente anziché impostare come hardcoding il percorso del certificato, nel caso in cui i percorsi del certificato cambino in futuro.
>

Inoltre, i [contenitori di Windows Server Core](configure-custom-container.md#supported-parent-images) caricano automaticamente i certificati nell'archivio certificati, in **LocalMachine\My**. Per caricare i certificati, seguire lo stesso modello di [Carica certificato nelle app di Windows.](#load-certificate-in-windows-apps) Per i contenitori basati su Windows Nano, usare i percorsi di file indicati in precedenza per [caricare il certificato direttamente dal file](#load-certificate-from-file).

Il codice C# seguente illustra come caricare un certificato pubblico in un'app Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Il codice C# seguente illustra come caricare un certificato privato in un'app Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;
...
var bytes = File.ReadAllBytes("/var/ssl/private/<thumbprint>.p12");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Per informazioni su come caricare un certificato TLS/SSL da un file in Node.js, PHP, Python, Java o Ruby, vedere la documentazione relativa al linguaggio o alla piattaforma Web corrispondente.

## <a name="more-resources"></a>Altre risorse

* [Proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel Servizio app di Azure](configure-ssl-bindings.md)
* [Applicare HTTPS](configure-ssl-bindings.md#enforce-https)
* [Applicare TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ: App Service Certificates](./faq-configuration-and-management.md) (Domande frequenti: certificati del servizio app)
