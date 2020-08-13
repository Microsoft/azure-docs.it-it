---
title: Servizi cloud e certificati di gestione | Documentazione Microsoft
description: Informazioni su come creare e distribuire certificati per i servizi cloud e per l'autenticazione con l'API di gestione in Azure.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 08ce69856dd36b6029297109fcb8610b856c8b98
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142367"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Panoramica sui certificati per i servizi cloud di Azure
I [certificati di servizio](#what-are-service-certificates) vengono usati in Azure per i servizi cloud, mentre i [certificati di gestione](#what-are-management-certificates) vengono usati per l'autenticazione con l'API di gestione. Questo argomento offre informazioni generali su entrambi i tipi di certificati, su come [crearli](#create) e come distribuirli in Azure.

Quelli usati in Azure sono certificati x.509 v3 e possono essere firmati da un altro certificato attendibile o essere autofirmati. Un certificato autofirmato viene firmato dall'autore e pertanto non è attendibile per impostazione predefinita. La maggior parte dei browser può ignorare questo problema. È consigliabile utilizzare i certificati autofirmati solo quando si sviluppano e si testano servizi cloud. 

I certificati usati da Azure possono contenere una chiave pubblica. I certificati hanno un'identificazione personale che consente di identificarli in modo non ambiguo. Questa identificazione personale viene usata nel [file di configurazione](cloud-services-configure-ssl-certificate-portal.md) di Azure per identificare il certificato che deve essere usato da un servizio cloud. 

>[!Note]
>I servizi Cloud di Azure non accettano il certificato crittografato AES256-SHA256.

## <a name="what-are-service-certificates"></a>Cosa sono i certificati di servizio?
I certificati di servizio sono associati ai servizi cloud e consentono la comunicazione sicura da e verso il servizio. Ad esempio, se si distribuisse un ruolo Web, si fornirebbe un certificato per autenticare un endpoint HTTPS esposto. I certificati di servizio, definiti nella definizione del servizio, vengono automaticamente distribuiti nella macchina virtuale che esegue un'istanza del ruolo. 

È possibile caricare i certificati di servizio nel portale di Azure tramite il portale di Azure o usando il modello di distribuzione classico. I certificati di servizio sono associati a uno specifico servizio cloud. Vengono assegnati a una distribuzione nel file di definizione del servizio.

I certificati di servizio possono essere gestiti separatamente dai servizi e da persone diverse. Ad esempio, uno sviluppatore può caricare un pacchetto del servizio che fa riferimento a un certificato caricato in precedenza in Azure da un responsabile IT. Un responsabile IT può gestire e rinnovare tale certificato, modificando la configurazione del servizio, senza dover caricare un nuovo pacchetto del servizio. Questa operazione è possibile poiché il nome logico, il nome di archivio e il percorso vengono specificati nel file di definizione del servizio, mentre l'identificazione personale del certificato viene specificata nel file di configurazione del servizio. Per aggiornare il certificato, è sufficiente caricare un nuovo certificato e modificare il valore dell'identificazione personale nel file di configurazione del servizio.

>[!Note]
>L'articolo [domande frequenti sui servizi cloud-configurazione e gestione](cloud-services-configuration-and-management-faq.md) contiene alcune informazioni utili sui certificati.

## <a name="what-are-management-certificates"></a>Cosa sono i certificati di gestione?
I certificati di gestione consentono di eseguire l'autenticazione con il modello di distribuzione classico. Numerosi programmi e strumenti (ad esempio Visual Studio o Azure SDK) usano questi certificati per automatizzare la configurazione e la distribuzione di vari servizi di Azure. Questi non sono realmente correlati ai servizi cloud. 

> [!WARNING]
> Fare attenzione. Questi tipi di certificati consentono a chiunque esegua l'autenticazione di gestire la sottoscrizione a cui sono associati. 
> 
> 

### <a name="limitations"></a>Limitazioni
È previsto un limite di 100 certificati di gestione per ogni sottoscrizione. È anche previsto un limite di 100 certificati di gestione per tutte le sottoscrizioni che fanno riferimento all'ID utente di un amministratore del servizio specifico. Se l'ID utente per l'amministratore dell'account è già stato usato per aggiungere 100 certificati di gestione e ne sono necessari altri, è possibile aggiungere un coamministratore per aumentare il numero di certificati. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Creare un nuovo certificato autofirmato
È possibile usare qualsiasi strumento disponibile per creare un certificato autofirmato purché rispetti queste impostazioni:

* Un certificato X.509.
* Contiene una chiave pubblica.
* Viene creato per lo scambio di chiave (file PFX).
* Il nome del soggetto deve corrispondere al dominio usato per accedere al servizio cloud.

    > Non è possibile acquisire un certificato TLS/SSL per cloudapp.net (o per qualsiasi dominio relativo ad Azure); il nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato usato per accedere all'applicazione. Ad esempio **contoso.net**, non **contoso.cloudapp.net**.

* Crittografia minima a 2048 bit.
* **Solo certificato di servizio**: il certificato lato client deve trovarsi nell'archivio certificati *Personale* .

Sono disponibili due semplici modi per creare un certificato in Windows: con l'utilità `makecert.exe` o con IIS.

### <a name="makecertexe"></a>Makecert.exe
Questa utilità è stata deprecata e di seguito non è più disponibile la relativa documentazione. Per altre informazioni, vedere [questo articolo di MSDN](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Se si vuole usare il certificato con un indirizzo IP anziché un dominio, usare l'indirizzo IP nel parametro -DnsName.


Se si desidera usare questo [certificato con il portale di gestione](../azure-api-management-certs.md), esportarlo in un file con estensione **cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
Su Internet sono disponibili molte pagine che spiegano come eseguire questa operazione con IIS. [Qui](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) ce n’è uno ottimo che ho trovato che ritengo che spieghi bene. 

### <a name="linux"></a>Linux
[Questo](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) articolo descrive come creare certificati tramite SSH.

## <a name="next-steps"></a>Passaggi successivi
[Caricare il certificato di servizio nel portale di Azure](cloud-services-configure-ssl-certificate-portal.md).

Caricare il [certificato dell'API di gestione](../azure-api-management-certs.md) nel portale di Azure.




