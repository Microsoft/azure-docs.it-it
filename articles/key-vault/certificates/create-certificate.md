---
title: Metodi di creazione dei certificati
description: Informazioni sulle diverse opzioni per creare o importare un certificato di Key Vault in Azure Key Vault. Esistono diversi modi per creare un certificato Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 71d8961af372f927fab909073daa715766542f87
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606673"
---
# <a name="certificate-creation-methods"></a>Metodi di creazione dei certificati

 Un certificato di Key Vault può essere creato o importato in un insieme di credenziali delle chiavi. Quando viene creato un certificato KV la chiave privata è creata all'interno dell'insieme di credenziali delle chiavi e mai esposta al proprietario del certificato. È possibile creare un certificato in Key Vault nei modi seguenti.  

-   **Creare un certificato autofirmato:** viene creata una coppia di chiavi pubblica e privata che viene associata con un certificato. Il certificato viene firmato dalla propria chiave.  

-    **Creare un nuovo certificato manualmente:** viene creata una coppia di chiavi pubblica e privata e viene generata un richiesta di firma del certificato X.509. La richiesta di firma può essere firmata dall'autorità di registrazione o di certificazione. Per completare il certificato KV in Key Vault, il certificato X.509 firmato può essere unito con la coppia di chiavi in sospeso. Anche se questo metodo richiede più passaggi, offre maggiore sicurezza perché la chiave privata viene creata e limitata a Key Vault. Il concetto è illustrato nel diagramma seguente.  

![Creare un certificato con un'autorità di certificazione personalizzata](../media/certificate-authority-1.png)  

Le descrizioni seguenti corrispondono ai passaggi contrassegnati con un numero in verde nel diagramma precedente.

1. Nel diagramma precedente l'applicazione crea un certificato, operazione che internamente inizia con la creazione di una chiave nell'insieme di credenziali delle chiavi.
2. Key Vault restituisce all'applicazione una richiesta di firma del certificato.
3. L'applicazione passa la richiesta di firma del certificato alla CA selezionata.
4. La CA scelta risponde con un certificato X509.
5. L'applicazione completa la creazione del certificato con l'unione del certificato X.509 della CA.

-   **Creare un certificato con un provider autorità di certificazione noto:** questo metodo richiede un'attività una tantum di creazione di un oggetto autorità di certificazione. Dopo che è stato creato un oggetto autorità di certificazione nell'insieme di credenziali delle chiavi, è possibile fare riferimento al nome di tale oggetto nei criteri del certificato di Key Vault. La richiesta di creazione di tale certificato KV creerà una coppia di chiavi nell'insieme di credenziali e comunicherà con il servizio di provider di autorità di certificazione utilizzando le informazioni nell'oggetto autorità di certificazione a cui si fa riferimento per ottenere un certificato X.509. Il certificato X.509 viene recuperato dal servizio autorità di certificazione e viene unito con la coppia di chiavi per completare la creazione del certificato KV.  

![Creare un certificato con un'autorità di certificazione partner di Key Vault](../media/certificate-authority-2.png)  

Le descrizioni seguenti corrispondono ai passaggi contrassegnati con un numero in verde nel diagramma precedente.

1. Nel diagramma precedente l'applicazione crea un certificato, operazione che internamente inizia con la creazione di una chiave nell'insieme di credenziali delle chiavi.
2. Key Vault invia alla CA una richiesta di certificato TLS/SSL.
3. L'applicazione esegue il polling, con un processo di ciclo e attesa, per Key Vault per il completamento del certificato. La creazione del certificato è completata quando Key Vault riceve la risposta della CA con il certificato X509.
4. La CA risponde alla richiesta di certificato TLS/SSL di Key Vault con un certificato X. 509 TLS/SSL.
5. La creazione del nuovo certificato viene completata con la fusione del certificato X. 509 TLS/SSL per l'autorità di certificazione.

## <a name="asynchronous-process"></a>Processo asincrono
La creazione dei certificati di Key Vault è un processo asincrono. Questa operazione crea una richiesta di certificato KV e restituisce il codice di stato http 202 (accettato). Lo stato della richiesta può essere monitorato eseguendo il polling dell'oggetto in sospeso creato dall'operazione. Nell'intestazione LOCATION viene restituito l'URI completo dell'oggetto in sospeso.  

Quando viene completata una richiesta per creare un certificato KV, lo stato dell'oggetto in sospeso cambia da "inprogress" in "completed" e viene creata una nuova versione del certificato KV. che diventa la versione corrente.  

## <a name="first-creation"></a>Prima creazione
 Quando si crea un certificato KV per la prima volta, vengono creati anche una chiave indirizzabile e un segreto con lo stesso nome del certificato. Se il nome è già in uso, l'operazione avrà esito negativo con il codice di stato http 409 (conflitto).
La chiave e il segreto indirizzabili ottengono i propri attributi dagli attributi del certificato di Key Vault. La chiave e il segreto indirizzabili creati in questo modo vengono contrassegnati come chiavi e segreti gestiti, la cui durata è gestita da Key Vault. Le chiavi gestite e i segreti sono di sola lettura. Nota: se un certificato di Key Vault scade o viene disabilitato, la chiave e il segreto corrispondenti diventano inutilizzabili.  

 Se si tratta della prima operazione di creazione di un certificato KV, sono necessari criteri.  È possibile fornire criteri anche con le successive operazioni di creazione per sostituire la risorsa criteri. Se non si specificano criteri, viene usata la risorsa criteri del servizio per creare una versione successiva del certificato KV. Si noti che quando è in corso una richiesta per la creazione di una versione successiva, il certificato di Key Vault corrente e la chiave e il segreto indirizzabili corrispondenti rimangono invariati.  

## <a name="self-issued-certificate"></a>Autocertificazione
 Per creare un'autocertificazione, impostare il nome dell'autorità di certificazione su "Self" nei criteri di certificato, come illustrato nel frammento seguente dei criteri di certificazione.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Se il nome dell'autorità di certificazione non è specificato, viene impostato su "Unknown". Quando l'autorità di certificazione è "Unknown", il proprietario del certificato dovrà ottenere manualmente un certificato X.509 dall'autorità di certificazione di sua scelta, quindi unire il certificato X.509 pubblico con l'oggetto certificato in sospeso dell'insieme di credenziali delle chiavi per completare la creazione del certificato.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Provider CA partner
La creazione del certificato può essere completata manualmente oppure usando un'autorità di certificazione "Self". Key Vault collabora con alcuni provider di autorità di certificazione per semplificare la creazione dei certificati. Presso questi provider autorità di certificazione partner è possibile ordinare i tipi seguenti di certificati per l'insieme di credenziali delle chiavi.  

|Provider|Tipo di certificato|Configurazione configurazione  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault offre certificati SSL OV o EV con DigiCert| [Guida all'integrazione](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)
|GlobalSign|Key Vault offre certificati SSL OV o EV con GlobalSign| [Guida all'integrazione](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 Un'autorità di certificazione è un'entità rappresentata in Azure Key Vault come risorsa CertificateIssuer. Viene usata per rendere disponibili informazioni sull'origine di un certificato di un insieme di credenziali delle chiavi: nome dell'autorità di certificazione, provider, credenziali e altri dettagli amministrativi.

Si noti che quando viene passato un ordine al provider di autorità di certificazione, può rispettare o ignorare le estensioni e il periodo di validità del certificato X.509 in base al tipo di certificato.  

 Autorizzazione: richiede l'autorizzazione di creazione certificati.

## <a name="see-also"></a>Vedere anche

 - [Monitorare e gestire la creazione dei certificati](create-certificate-scenarios.md)
