---
title: Sicurezza della comunicazione per la Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Informazioni sulla mitigazione per le minacce alla sicurezza della comunicazione esposte nella Threat Modeling Tool. Vedere le informazioni di mitigazione e gli esempi di codice di visualizzazione.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: 3d795d30e3ad420e0fed002baddf37469ddcf995
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004563"
---
# <a name="security-frame-communication-security--mitigations"></a>Infrastruttura di sicurezza: sicurezza della comunicazione - Procedure di mitigazione 
| Prodotto o servizio | Articolo |
| --------------- | ------- |
| **Hub eventi di Azure** | <ul><li>[Proteggere la comunicazione con l'hub eventi con SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Verificare i privilegi dell'account del servizio e controllare che le pagine ASP.NET o i servizi personalizzati rispettino la sicurezza di CRM](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Usare gateway di gestione dati durante la connessione di SQL Server locali a Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Identity Server** | <ul><li>[Verificare che tutto il traffico verso il server di identità superi la connessione HTTPS](#identity-https)</li></ul> |
| **Applicazione Web** | <ul><li>[Verificare i certificati X.509 usati per autenticare le connessioni SSL, TLS e DTLS](#x509-ssltls)</li><li>[Configurare il certificato TLS/SSL per un dominio personalizzato nel servizio app Azure](#ssl-appservice)</li><li>[Forzare tutto il traffico verso il servizio app di Azure su una connessione HTTPS](#appservice-https)</li><li>[Abilitare HTTP Strict Transport Security (HSTS)](#http-hsts)</li></ul> |
| **Database** | <ul><li>[Verificare la crittografia della connessione SQL Server e la convalida del certificato](#sqlserver-validation)</li><li>[Forzare la comunicazione crittografata con SQL Server](#encrypted-sqlserver)</li></ul> |
| **Archiviazione di Azure** | <ul><li>[Verificare che per la comunicazione con Archiviazione di Azure venga usato HTTPS](#comm-storage)</li><li>[Convalidare l'hash MD5 dopo il download di BLOB se non è possibile abilitare HTTPS](#md5-https)</li><li>[Usare il client compatibile con SMB 3,0 per garantire la crittografia dei dati in transito per le condivisioni file di Azure](#smb-shares)</li></ul> |
| **Client per dispositivi mobili** | <ul><li>[Implementa blocco certificati](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Abilitare HTTPS: canale di trasporto sicuro](#https-transport)</li><li>[WCF: impostare il livello di protezione per la sicurezza dei messaggi su EncryptAndSign](#message-protection)</li><li>[WCF: usare un account con privilegi minimi per eseguire il servizio WCF](#least-account-wcf)</li></ul> |
| **API Web** | <ul><li>[Forzare tutto il traffico verso le API Web su una connessione HTTPS](#webapi-https)</li></ul> |
| **Cache Redis di Azure** | <ul><li>[Assicurarsi che la comunicazione con cache di Azure per Redis sia su TLS](#redis-ssl)</li></ul> |
| **Gateway IoT sul campo** | <ul><li>[Proteggere la comunicazione da dispositivo a gateway sul campo](#device-field)</li></ul> |
| **Gateway IoT cloud** | <ul><li>[Proteggere la comunicazione da dispositivo a gateway nel cloud con SSL/TLS](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Proteggere la comunicazione con l'hub eventi con SSL/TLS

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Hub eventi di Azure | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Panoramica del modello di sicurezza e autenticazione di Hub eventi](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Passaggi** | Proteggere le connessioni AMQP o HTTP con l'hub eventi usando SSL/TLS |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Verificare i privilegi dell'account del servizio e controllare che le pagine ASP.NET o i servizi personalizzati rispettino la sicurezza di CRM

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Dynamics CRM | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Verificare i privilegi dell'account del servizio e controllare che le pagine ASP.NET o i servizi personalizzati rispettino la sicurezza di CRM |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Usare gateway di gestione dati durante la connessione di SQL Server locali a Azure Data Factory

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Azure Data Factory | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | Tipi di servizi collegati: Azure e locale |
| **Riferimenti**              |[Trasferimento dei dati tra il gateway di](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway) [Gestione dati](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) locale e Azure Data Factory |
| **Passaggi** | <p>Lo strumento Gateway di gestione dati è necessario per la connessione a origini dati protette da un firewall o dalla rete aziendale.</p><ol><li>Bloccando il computer si isola lo strumento Gateway di gestione dati impedendo a programmi che non funzionano correttamente di danneggiare o analizzare il computer dell'origine dati. Ad esempio, in caso di installazione degli ultimi aggiornamenti, abilitazione delle porte necessarie minime, provisioning degli account controllati, abilitazione del controllo e della crittografia dei dischi e così via.</li><li>È necessario eseguire la rotazione della chiave del gateway dati a intervalli frequenti o a ogni rinnovo della password dell'account del servizio Gateway di gestione dati.</li><li>I transiti di dati attraverso il servizio di collegamento devono essere crittografati.</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Verificare che tutto il traffico verso Identity Server venga gestito su connessione HTTPS

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Identity Server | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [IdentityServer3 - Keys, Signatures and Cryptography](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html) (IdentityServer3 - Chiavi, firme e crittografia), [IdentityServer3 - Deployment](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) (IdentityServer3 - Distribuzione) |
| **Passaggi** | Per impostazione predefinita, IdentityServer richiede che tutte le connessioni in ingresso vengano effettuate tramite HTTPS. È assolutamente obbligatorio che per la comunicazione con IdentityServer vengano usati esclusivamente trasporti protetti. Esistono alcuni scenari di distribuzione, ad esempio l'offload TLS, in cui questo requisito può essere rilassato. Per altre informazioni, vedere la pagina relativa alla distribuzione di Identity Server indicata nei riferimenti. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Verificare i certificati X.509 usati per autenticare le connessioni SSL, TLS e DTLS

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | <p>Le applicazioni che usano SSL, TLS o DTLS devono eseguire una verifica completa dei certificati X.509 delle entità a cui si connettono. Ciò include la verifica dei certificati in relazione a:</p><ul><li>Nome di dominio</li><li>Date di validità (date sia di inizio che di scadenza)</li><li>Stato di revoca</li><li>Utilizzo (ad esempio, autenticazione server per i server o autenticazione client per i client)</li><li>Catena di certificati. I certificati devono essere concatenati a un'autorità di certificazione radice (CA) considerata attendibile dalla piattaforma o configurata in modo esplicito dall'amministratore</li><li>La lunghezza della chiave pubblica del certificato deve essere superiore a 2048 bit</li><li>L'algoritmo di hash deve essere SHA256 o versione superiore |

## <a name="configure-tlsssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Configurare il certificato TLS/SSL per un dominio personalizzato nel servizio app Azure

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | Tipo di ambiente: Azure |
| **Riferimenti**              | [Abilitare HTTPS per un'app in Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Passaggi** | Per impostazione predefinita, Azure abilita già HTTPS per ogni app con un certificato con caratteri jolly per il dominio *.azurewebsites.net. Come tutti i domini con caratteri jolly, tuttavia, non è sicuro quanto un dominio personalizzato con un proprio certificato ([riferimento](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/)). È consigliabile abilitare TLS per il dominio personalizzato a cui si accederà l'app distribuita|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Forzare tutto il traffico verso il servizio app di Azure su una connessione HTTPS

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | Tipo di ambiente: Azure |
| **Riferimenti**              | [Imporre HTTPS nel servizio app di Azure](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Passaggi** | <p>Nonostante Azure abiliti già HTTPS per i servizi app di Azure con un certificato con caratteri jolly per il dominio *.azurewebsites.net, non impone HTTPS. I visitatori possono comunque accedere all'app usando HTTP e questo potrebbe compromettere la sicurezza dell'app. È quindi necessario imporre HTTPS in modo esplicito. Le applicazioni ASP.NET MVC dovranno usare il [filtro RequireHttps](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) che forza il nuovo invio di una richiesta HTTP non protetta su HTTPS.</p><p>In alternativa, per imporre HTTPS è possibile usare il modulo URL Rewrite incluso con Servizio app di Azure. Il modulo URL Rewrite consente agli sviluppatori di definire le regole applicate alle richieste in ingresso prima che queste vengano passate all'applicazione. Le regole di URL Rewrite sono definite in un file web.config archiviato nella radice dell'applicazione.</p>|

### <a name="example"></a>Esempio
L'esempio seguente contiene una regola di base di URL Rewrite che forza l'uso di HTTPS per tutto il traffico in ingresso.
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Il funzionamento di questa regola prevede la restituzione di un codice di stato HTTP 301 (reindirizzamento permanente) quando l'utente richiede una pagina mediante HTTP. Il codice 301 reindirizza la richiesta allo stesso URL richiesto dal visitatore, ma sostituisce la parte HTTP della richiesta con HTTPS. Ad esempio, `HTTP://contoso.com` viene reindirizzato a `HTTPS://contoso.com`. 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Abilitare HTTP Strict Transport Security (HSTS)

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Applicazione Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Foglio informativo di OWASP su HTTP Strict Transport Security](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) |
| **Passaggi** | <p>HTTP Strict Transport Security (HSTS) è un miglioramento della sicurezza con consenso esplicito che viene specificato da un'applicazione Web usando una speciale intestazione della risposta. Quando un browser supportato riceve questa intestazione, impedisce l'invio tramite HTTP di qualsiasi comunicazione al dominio specificato e invia tutte le comunicazioni tramite HTTPS. Impedisce anche i messaggi di richiesta con click-through HTTPS nei browser.</p><p>Per implementare HSTS, è necessario configurare l'intestazione della risposta seguente per un sito Web a livello globale, nel codice o nella configurazione. Strict-Transport-Security: max-age = 300; includeSubDomains HSTS risolve le minacce seguenti:</p><ul><li>L'utente imposta come segnalibro o digita manualmente `https://example.com` ed è soggetto a un attacco man-in-the-middle: HSTS reindirizza automaticamente le richieste HTTP a HTTPS per il dominio di destinazione</li><li>Un'applicazione Web che dovrebbe essere esclusivamente HTTPS inavvertitamente contiene collegamenti HTTP o fornisce contenuti tramite HTTP: HSTS reindirizza automaticamente le richieste HTTP a HTTPS per il dominio di destinazione</li><li>Un utente malintenzionato tenta con un attacco man-in-the-middle di intercettare il traffico da un utente vittima con un certificato non valido sperando che l'utente accetti tale certificato: HSTS non consente a un utente di eseguire l'override del messaggio di certificato non valido</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Verificare la crittografia della connessione e la convalida dei certificati di SQL Server

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | SQL Azure  |
| **Attributes (Attributi)**              | Versione SQL: 12 |
| **Riferimenti**              | [Best Practices on Writing Secure Connection Strings for SQL Database](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) (Procedure consigliate per la scrittura di stringhe di connessione sicure per il database SQL) |
| **Passaggi** | <p>Tutte le comunicazioni tra il database SQL e un'applicazione client vengono crittografate con Transport Layer Security (TLS), precedentemente noto come Secure Sockets Layer (SSL), in qualsiasi momento. Il database SQL non supporta connessioni non crittografate. Per convalidare i certificati con gli strumenti o il codice dell'applicazione, richiedere una connessione crittografata in modo esplicito e non considerare attendibili i certificati del server. Se il codice dell'applicazione o gli strumenti non richiedono una connessione crittografata, riceveranno comunque connessioni crittografate.</p><p>Tuttavia, potrebbero non convalidare i certificati del server e pertanto saranno soggetti ad attacchi "man in the middle". Per convalidare i certificati con il codice dell'applicazione ADO.NET, impostare `Encrypt=True` e `TrustServerCertificate=False` nella stringa di connessione del database. Per convalidare i certificati tramite SQL Server Management Studio, aprire la finestra di dialogo Connetti al server. Fare clic su Crittografa connessione nella scheda Proprietà connessione.</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Forzare la comunicazione crittografata con SQL Server

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Database | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Locale |
| **Attributes (Attributi)**              | Versione SQL: MsSQL2016, MsSQL2012, MsSQL2014 |
| **Riferimenti**              | [Abilitare connessioni crittografate al motore di database](https://msdn.microsoft.com/library/ms191192)  |
| **Passaggi** | L'abilitazione della crittografia TLS aumenta la protezione dei dati trasmessi tra le istanze di SQL Server e delle applicazioni. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Verificare che per la comunicazione con Archiviazione di Azure venga usato HTTPS

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Archiviazione di Azure | 
| **Fase SDL**               | Distribuzione |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Crittografia a livello di trasporto in Archiviazione di Azure: uso di HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Passaggi** | Per garantire la sicurezza dei dati di Archiviazione di Azure in transito, quando si chiamano le API REST o si accede a oggetti nelle risorse di archiviazione usare sempre il protocollo HTTPS. Le firme di accesso condiviso, che possono essere usate per delegare l'accesso a oggetti di Archiviazione di Azure, includono la possibilità di specificare che quando si usano firme di accesso condiviso può essere usato solo il protocollo HTTPS, in modo da garantire che chiunque invii collegamenti con token di firma di accesso condiviso usi il protocollo corretto.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Convalidare l'hash MD5 dopo il download di BLOB se non è possibile abilitare HTTPS

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Archiviazione di Azure | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | Tipo di archiviazione: BLOB |
| **Riferimenti**              | [Windows Azure Blob MD5 Overview](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) (Panoramica di MD5 nel servizio BLOB di Microsoft Azure) |
| **Passaggi** | <p>Il servizio BLOB di Microsoft Azure offre meccanismi per garantire l'integrità dei dati sia al livello dell'applicazione che al livello trasporto. Se per qualsiasi motivo è necessario usare HTTP anziché HTTPS e si usano BLOB in blocchi, è possibile usare il controllo MD5 per verificare l'integrità dei BLOB in fase di trasferimento.</p><p>Ciò contribuisce a proteggere dagli errori a livello di rete/trasporto, ma non necessariamente dalle violazioni. Se è possibile utilizzare HTTPS, che fornisce la protezione a livello di trasporto, il controllo MD5 è ridondante e superfluo.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Usare il client compatibile con SMB 3,0 per garantire la crittografia dei dati in transito per le condivisioni file di Azure

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Client per dispositivi mobili | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | Tipo di archiviazione: file |
| **Riferimenti**              | [Archiviazione file di Azure](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Supporto di SMB in Archiviazione file di Azure per client Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Passaggi** | Il servizio di archiviazione file di Azure supporta HTTPS quando si usa l'API REST, ma è più comunemente usato come una condivisione file SMB collegata a una VM. SMB 2.1 non supporta la crittografia, quindi le connessioni sono consentite solo all'interno della stessa area in Azure. Tuttavia, SMB 3.0 supporta la crittografia e può essere usato con Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, consentendo l'accesso tra aree e anche l'accesso sul desktop. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Implementare l'associazione del certificato

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Archiviazione di Azure | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico, Windows Phone |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Associazione del certificato e della chiave pubblica](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) |
| **Passaggi** | <p>L'associazione del certificato protegge da attacchi man-in-the-middle. L'associazione è il processo con cui un host viene associato alla chiave pubblica o al certificato X509 previsto. Quando per un host è noto o visibile un certificato o una chiave pubblica, questo viene associato all'host. </p><p>Pertanto, quando un antagonista tenta di eseguire un attacco TLS MITM, durante l'handshake TLS la chiave del server dell'utente malintenzionato sarà diversa dalla chiave del certificato aggiunto e la richiesta verrà ignorata, impedendo il blocco del certificato MITM mediante l'implementazione del delegato di ServicePointManager `ServerCertificateValidationCallback` .</p>|

### <a name="example"></a>Esempio
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>Abilitare HTTPS: canale di trasporto sicuro

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | .NET Framework 3 |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Passaggi** | La configurazione dell'applicazione deve garantire l'uso di HTTPS per tutti gli accessi a informazioni riservate.<ul><li>**SPIEGAZIONE:** se un'applicazione che gestisce informazioni riservate non usa la crittografia a livello di messaggio, all'applicazione deve essere consentita la comunicazione solo tramite un canale di trasporto crittografato.</li><li>**RACCOMANDAZIONI:** verificare che il trasporto HTTP sia disabilitato e abilitare invece il trasporto HTTPS. Ad esempio, sostituire il tag `<httpTransport/>` con il tag `<httpsTransport/>`. Non basarsi su una configurazione di rete (firewall) per garantire che l'applicazione sia accessibile solo tramite un canale sicuro. Da un punto di vista teorico, l'applicazione non deve dipendere dalla rete per la sicurezza.</li></ul><p>Da un punto di vista pratico, le persone responsabili della protezione della rete non sempre tengono traccia dell'evoluzione dei requisiti di sicurezza dell'applicazione.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: impostare il livello di protezione per la sicurezza dei messaggi su EncryptAndSign

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | .NET Framework 3 |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Passaggi** | <ul><li>**SPIEGAZIONE:** quando il livello di protezione è impostato su "None", la protezione dei messaggi verrà disabilitata. La riservatezza e l'integrità si ottengono con il livello di impostazione appropriato.</li><li>**RACCOMANDAZIONI**<ul><li>Quando `Mode=None`, la protezione dei messaggi è disabilitata</li><li>Quando `Mode=Sign`, il messaggio viene firmato ma non crittografato. Questa impostazione deve essere usata quando è importante l'integrità dei dati</li><li>Quando `Mode=EncryptAndSign`, il messaggio viene firmato e crittografato</li></ul></li></ul><p>Quando è sufficiente convalidare l'integrità delle informazioni senza problemi di riservatezza, valutare la possibilità di disattivare la crittografia e limitarsi alla firma del messaggio. Questo può risultare utile per contratti di operazione o di servizio in cui è necessario convalidare il mittente originale ma non vengono trasmessi dati sensibili. Quando si riduce il livello di protezione, prestare attenzione che il messaggio non contenga dati personali.</p>|

### <a name="example"></a>Esempio
Gli esempi seguenti illustrano la configurazione del servizio e dell'operazione per la sola firma del messaggio. Esempio di contratto di servizio con `ProtectionLevel.Sign`: di seguito è riportato un esempio dell'uso di ProtectionLevel.Sign a livello di contratto di servizio. 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Esempio
Esempio di contratto di operazione con `ProtectionLevel.Sign` (per un controllo granulare): di seguito è riportato un esempio dell'uso di `ProtectionLevel.Sign` a livello di contratto di operazione.

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: usare un account con privilegi minimi per eseguire il servizio WCF

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | .NET Framework 3 |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Passaggi** | <ul><li>**SPIEGAZIONE:** non eseguire i servizi WCF con un account amministratore o con privilegi elevati, che comporterebbe un alto impatto in caso di compromissione dei servizi.</li><li>**RACCOMANDAZIONI:** per ospitare il servizio WCF usare un account con privilegi minimi, in modo da ridurre la superficie di attacco dell'applicazione e il potenziale danno in caso di attacco. Se l'account del servizio richiede diritti di accesso aggiuntivi per risorse dell'infrastruttura come MSMQ, il registro eventi, i contatori delle prestazioni e il file system, è necessario concedere autorizzazioni appropriate a tali risorse in modo da consentire la corretta esecuzione del servizio WCF.</li></ul><p>Se il servizio deve accedere a risorse specifiche per conto del chiamante originale, usare la rappresentazione e la delega per trasferire l'identità del chiamante per un controllo di autorizzazione downstream. In uno scenario di sviluppo, usare l'account del servizio di rete locale, ovvero un account predefinito speciale con privilegi ridotti. In uno scenario di produzione, creare un account di servizio del dominio personalizzato con privilegi minimi.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Forzare tutto il traffico verso le API Web su una connessione HTTPS

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | MVC 5, MVC 6 |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Imporre SSL in un controller di API Web](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Passaggi** | Se un'applicazione include sia un'associazione HTTPS che un'associazione HTTP, i client possono comunque usare HTTP per accedere al sito. Per impedirlo, usare un filtro azioni per garantire che per le richieste per le API protette venga sempre usato HTTPS.|

### <a name="example"></a>Esempio 
Il codice seguente illustra un filtro di autenticazione dell'API Web che verifica la presenza di TLS: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Aggiungere questo filtro a tutte le azioni dell'API Web che richiedono TLS: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-tls"></a><a id="redis-ssl"></a>Assicurarsi che la comunicazione con cache di Azure per Redis sia su TLS

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Cache Redis di Azure | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Azure Redis TLS-supporto](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Passaggi** | Il server Redis non supporta la TLS predefinita, ma la cache di Azure per Redis. Se ci si connette a Cache Redis di Azure e il client supporta TLS, ad esempio StackExchange.Redis, è consigliabile usare TLS. Per impostazione predefinita, la porta non TLS è disabilitata per la nuova cache di Azure per le istanze di Redis. Assicurarsi che le impostazioni predefinite sicure non vengano modificate a meno che non esista una dipendenza dal supporto TLS per i client Redis. |

Si noti che Redis è progettato per essere accessibile da client attendibili all'interno di ambienti attendibili. Di conseguenza, non è solitamente opportuno esporre l'istanza di Redis direttamente a Internet o, in generale, a un ambiente in cui client non attendibili possono accedere direttamente al socket UNIX o alla porta TCP di Redis. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Proteggere la comunicazione da dispositivo a gateway sul campo

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Gateway IoT sul campo | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | N/D  |
| **Passaggi** | Per i dispositivi basati su IP, il protocollo di comunicazione può in genere essere incapsulato in un canale SSL/TLS per proteggere i dati in transito. Per altri protocolli che non supportano SSL/TLS, verificare se sono disponibili versioni sicure del protocollo che offrono sicurezza a livello di trasporto o di messaggio. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Proteggere la comunicazione da dispositivo a gateway nel cloud con SSL/TLS

| Titolo                   | Dettagli      |
| ----------------------- | ------------ |
| **Componente**               | Gateway IoT cloud | 
| **Fase SDL**               | Compilare |  
| **Tecnologie applicabili** | Generico |
| **Attributes (Attributi)**              | N/D  |
| **Riferimenti**              | [Scegliere il protocollo di comunicazione](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Passaggi** | Proteggere i protocolli HTTP/AMQP e MQTT con SSL/TLS. |
