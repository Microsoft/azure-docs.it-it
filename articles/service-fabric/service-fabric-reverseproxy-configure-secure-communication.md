---
title: Comunicazione protetta del proxy inverso di Azure Service Fabric
description: Configurare il proxy inverso per abilitare la comunicazione end-to-end sicura in un'applicazione Service Fabric di Azure.
ms.topic: conceptual
ms.date: 08/10/2017
ms.openlocfilehash: b01ce559b3c790164992d6618149afa9df069466
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86256136"
---
# <a name="connect-to-a-secure-service-with-the-reverse-proxy"></a>Connettersi a un servizio protetto con il proxy inverso

In questo articolo viene spiegato come stabilire una connessione protetta tra il proxy inverso e i servizi, abilitando un canale protetto end-to-end. Per altre informazioni sul proxy inverso, vedere [Proxy inverso in Azure Service Fabric](service-fabric-reverseproxy.md)

> [!IMPORTANT]
> La connessione ai servizi protetti è supportata solo quando il proxy inverso è configurato per l'ascolto su HTTPS. Questo articolo presuppone che questo sia il caso specifico. Per configurare il proxy inverso in Service Fabric, fare riferimento a [Configurare il proxy inverso in Azure Service Fabric](service-fabric-reverseproxy-setup.md).

## <a name="secure-connection-establishment-between-the-reverse-proxy-and-services"></a>Stabilire una connessione protetta tra il proxy inverso e i servizi 

### <a name="reverse-proxy-authenticating-to-services"></a>Autenticazione del proxy inverso per i servizi:
Il proxy inverso si autoidentifica per i servizi usando il relativo certificato. Per i cluster di Azure il certificato viene specificato con la proprietà ***reverseProxyCertificate** _ nella sezione [_ *Microsoft. ServiceFabric* * /Clusters](/azure/templates/microsoft.servicefabric/clusters) del [tipo di risorsa](../azure-resource-manager/templates/template-syntax.md) del modello di gestione risorse. Per i cluster autonomi, il certificato viene specificato con la **proprietà _ReverseProxyCertificate_*_ o _*_ReverseProxyCertificateCommonNames_*_ nella sezione _* Security** di ClusterConfig.json. Per altre informazioni, vedere [Abilitare il proxy inverso nei cluster autonomi](service-fabric-reverseproxy-setup.md#enable-reverse-proxy-on-standalone-clusters). 

I servizi possono implementare la logica per verificare il certificato presentato dal proxy inverso. I servizi possono specificare i dettagli del certificato client accettati come impostazioni di configurazione nel pacchetto di configurazione. Questo può essere letto in fase di runtime e usato per convalidare il certificato presentato dal proxy inverso. Fare riferimento a [Gestire i parametri dell'applicazione](service-fabric-manage-multiple-environment-app-configuration.md) per aggiungere le impostazioni di configurazione. 

### <a name="reverse-proxy-verifying-the-services-identity-via-the-certificate-presented-by-the-service"></a>Verifica dell'identità del servizio da parte del proxy inverso tramite il certificato presentato dal servizio:
Per eseguire la convalida del certificato del server per i certificati presentati dai servizi, il proxy inverso supporta i criteri seguenti: None, ServiceCommonNameAndIssuer e ServiceCertificateThumbprints.
Per selezionare i criteri da usare per il proxy inverso, specificare **ApplicationCertificateValidationPolicy** nella sezione **ApplicationGateway/Http** in [fabricSettings](service-fabric-cluster-fabric-settings.md).

La prossima sezione illustra i dettagli di configurazione per ognuna di queste opzioni.

### <a name="service-certificate-validation-options"></a>Opzioni di convalida dei certificati del servizio 

- **None**: il proxy inverso ignora la verifica del certificato del servizio di proxy e stabilisce la connessione sicura. Comportamento predefinito.
Specificare **ApplicationCertificateValidationPolicy** con il valore **None** nella sezione [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "None"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCommonNameAndIssuer**: il proxy inverso verifica il certificato presentato dal servizio in base al nome comune del certificato e all'identificazione personale immediata dell'autorità di certificazione: specificare **ApplicationCertificateValidationPolicy** con il valore **ServiceCommonNameAndIssuer** nella sezione [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCommonNameAndIssuer"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Per specificare l'elenco di nomi comuni del servizio e di identificazioni personali dell'autorità di certificazione, aggiungere una sezione [**ApplicationGateway/Http/ServiceCommonNameAndIssuer**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttpservicecommonnameandissuer) in **fabricSettings**, come illustrato di seguito. Nella matrice di **parametri** è possibile aggiungere più coppie nome comune del certificato/identificazione personale dell'autorità di certificazione. 

   Se il proxy inverso dell'endpoint si connette a presenta un certificato che è il nome comune e l'identificazione personale dell'autorità di certificazione corrisponde a uno qualsiasi dei valori specificati qui, viene stabilito un canale TLS.
   In caso di errore nella corrispondenza dei dettagli del certificato, il proxy inverso non esegue correttamente la richiesta del client e presenta un codice di stato 502, ovvero Gateway non valido. La riga di stato HTTP conterrà anche la frase "Invalid SSL Certificate" (Certificato SSL non valido). 

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http/ServiceCommonNameAndIssuer",
               "parameters": [
                 {
                   "name": "WinFabric-Test-Certificate-CN1",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 b4 22 11"
                 },
                 {
                   "name": "WinFabric-Test-Certificate-CN2",
                   "value": "b3 44 9b 01 8d 0f 68 39 a2 c5 d6 2b 5b 6c 6a c8 22 11 33 44"
                 }
               ]
             }
           ],
           ...
   }
   ```

- **ServiceCertificateThumbprints**: il proxy inverso consente di verificare il certificato del servizio del proxy in base all'identificazione personale. Se i servizi sono configurati con certificati autofirmati, è possibile scegliere di intraprendere questa strada: specificare **ApplicationCertificateValidationPolicy** con il valore **ServiceCertificateThumbprints** nella sezione [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                 {
                   "name": "ApplicationCertificateValidationPolicy",
                   "value": "ServiceCertificateThumbprints"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Specificare le identificazioni personali anche con una voce **ServiceCertificateThumbprints** nella sezione **ApplicationGateway/Http**. Nel campo del valore è possibile specificare più identificazioni personali come un elenco delimitato da virgole, come illustrato di seguito:

   ```json
   {
   "fabricSettings": [
             ...
             {
               "name": "ApplicationGateway/Http",
               "parameters": [
                   ...
                 {
                   "name": "ServiceCertificateThumbprints",
                   "value": "78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 bf,78 12 20 5a 39 d2 23 76 da a0 37 f0 5a ed e3 60 1a 7e 64 b9"
                 }
               ]
             }
           ],
           ...
   }
   ```

   Se l'identificazione personale del certificato del server è elencata in questa voce di configurazione, il proxy inverso riesce a connettersi alla connessione TLS. In caso contrario, termina la connessione e non esegue correttamente la richiesta del client con un errore 502, ovvero Gateway non valido. La riga di stato HTTP conterrà anche la frase "Invalid SSL Certificate" (Certificato SSL non valido).

## <a name="endpoint-selection-logic-when-services-expose-secure-as-well-as-unsecured-endpoints"></a>Logica di scelta dell'endpoint quando i servizi espongono endpoint sicuri e non sicuri
Service Fabric supporta la configurazione di più endpoint per un servizio. Per altre informazioni, vedere [Specificare le risorse in un manifesto del servizio](service-fabric-service-manifest-resources.md).

Il proxy inverso consente di selezionare uno degli endpoint per l'inoltro della richiesta in base al parametro di query **ListenerName** nell'[URI del servizio](./service-fabric-reverseproxy.md#uri-format-for-addressing-services-by-using-the-reverse-proxy). Se il parametro **ListenerName** non viene specificato, il proxy inverso può scegliere un endpoint qualsiasi dall'elenco degli endpoint. A seconda degli endpoint configurati per il servizio, l'endpoint selezionato può essere un endpoint HTTP o HTTPS. Possono esistere requisiti o scenari in cui si vuole che il proxy inverso operi in "modalità solo endpoint protetti", per evitare che il proxy inverso protetto inoltri richieste a endpoint non protetti. Per impostare la modalità solo endpoint protetti per il proxy inverso, impostare la voce di configurazione **SecureOnlyMode** sul valore **true** nella sezione [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).   

```json
{
"fabricSettings": [
          ...
          {
            "name": "ApplicationGateway/Http",
            "parameters": [
                ...
              {
                "name": "SecureOnlyMode",
                "value": true
              }
            ]
          }
        ],
        ...
}
```

> [!NOTE]
> Se si opera in modalità **SecureOnlyMode**, e un client **ListenerName** corrisponde a un endpoint HTTP (non protetto), il proxy inverso non esegue correttamente la richiesta con un codice di stato HTTP 404 (Non trovato).

## <a name="setting-up-client-certificate-authentication-through-the-reverse-proxy"></a>Configurazione dell'autenticazione del certificato client tramite il proxy inverso
La terminazione TLS viene eseguita nel proxy inverso e tutti i dati del certificato client vengono persi. Perché i servizi eseguano l'autenticazione del certificato client, specificare l'impostazione **ForwardClientCertificate** nella sezione [**ApplicationGateway/Http**](./service-fabric-cluster-fabric-settings.md#applicationgatewayhttp).

1. Quando **ForwardClientCertificate** è impostato su **false**, il proxy inverso non richiede il certificato del client durante l'handshake TLS con il client.
Comportamento predefinito.

2. Quando **ForwardClientCertificate** è impostato su **true**, il proxy inverso richiede il certificato del client durante l'handshake TLS con il client.
I dati del certificato client verranno quindi inviati in un'intestazione HTTP personalizzata denominata **X-Client-Certificate**. Il valore dell'intestazione è la stringa in formato PEM con codifica base64 del certificato del client. Il servizio può eseguire correttamente o meno la richiesta con il codice di stato appropriato dopo aver esaminato i dati del certificato.
Se il client non presenta un certificato, il proxy inverso trasmette un'intestazione vuota e consente al servizio di gestire il caso.

> [!NOTE]
> Il proxy inverso agisce solo come servizio di inoltro. pertanto non esegue alcuna convalida del certificato del client.


## <a name="next-steps"></a>Passaggi successivi
* [Configurare il proxy inverso in un cluster](service-fabric-reverseproxy-setup.md).
* Vedere [configurare il proxy inverso per la connessione ai servizi protetti](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)
* Vedere un esempio di comunicazione HTTP tra i servizi in un [progetto di esempio in GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chiamate di procedura remota con i Reliable Services remoti](service-fabric-reliable-services-communication-remoting.md)
* [Web API che usa OWIN in Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Gestire i certificati cluster](service-fabric-cluster-security-update-certs-azure.md)
