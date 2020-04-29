---
title: Introduzione ai certificati di Key Vault
description: Gli scenari seguenti illustrano diversi utilizzi primari del servizio di gestione dei certificati di Key Vault, includendo i passaggi aggiuntivi necessari per creare il primo certificato in un insieme di credenziali delle chiavi.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 5881314f0d3c62e7d6181ebd7bb27a5e0e87729a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431943"
---
# <a name="get-started-with-key-vault-certificates"></a>Introduzione ai certificati di Key Vault
Gli scenari seguenti illustrano diversi utilizzi primari del servizio di gestione dei certificati di Key Vault, includendo i passaggi aggiuntivi necessari per creare il primo certificato in un insieme di credenziali delle chiavi.

Vengono illustrate le procedure seguenti:
- Creazione del primo certificato di Key Vault
- Creazione di un certificato con un'autorità di certificazione partner di Key Vault
- Creazione di un certificato con un'autorità di certificazione non partner di Key Vault
- Importazione di un certificato

## <a name="certificates-are-complex-objects"></a>Certificati come oggetti complessi
I certificati sono costituiti da tre risorse correlate che collegate tra loro formano un certificato di Key Vault: i metadati del certificato, una chiave e un segreto.


![Certificati complessi](../media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Creazione del primo certificato di Key Vault  
 Prima di poter creare un certificato in un'istanza di Key Vault, è necessario completare i passaggi preliminari 1 e 2 e deve essere presente un insieme di credenziali delle chiavi per l'utente/organizzazione.  

**Passaggio 1**. Provider autorità di certificazione (CA)  
-   L'onboarding come amministratore IT o PKI o altro utente responsabile della gestione degli account con le CA per una determinata azienda (ad esempio, Contoso) è un prerequisito per usare i certificati di Key Vault.  
    Le CA seguenti sono gli attuali provider partner di Key Vault.  
    -   DigiCert-Key Vault offre certificati di protezione/SSL OV con DigiCert.  
    -   GlobalSign-Key Vault offre certificati di protezione/SSL OV con GlobalSign.  

**Passaggio 2** : un amministratore dell'account per un provider di autorità di certificazione crea le credenziali che devono essere usate da Key Vault per registrare, rinnovare e usare i certificati TLS/SSL tramite Key Vault.

**Passaggio 3**. A seconda dell'autorità di certificazione, un amministratore di Contoso e un dipendente di Contoso (utente di Key Vault) proprietario di certificati possono ottenere un certificato dall'amministratore o direttamente dall'account con la CA.  

- Avviare un'operazione di aggiunta di credenziali in un insieme di credenziali delle chiavi [ impostando una risorsa autorità di certificazione](/rest/api/keyvault/setcertificateissuer/setcertificateissuer). Un'autorità di certificazione è un'entità rappresentata in Azure Key Vault come risorsa CertificateIssuer. Viene usata per rendere disponibili informazioni sull'origine di un certificato di un insieme di credenziali delle chiavi: nome dell'autorità di certificazione, provider, credenziali e altri dettagli amministrativi.
  - Ad esempio, MyDigiCertIssuer  
    -   Provider  
    -   Credenziali: credenziali dell'account CA. Ogni autorità di certificazione ha dati specifici.  

    Per altre informazioni sulla creazione di account con i provider CA, vedere il relativo post nel [blog su Key Vault](https://aka.ms/kvcertsblog).  

**Passaggio 3,1** : configurare i [contatti dei certificati](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) per le notifiche. Questi costituiscono il contatto per l'utente di Key Vault. Questo passaggio non viene applicato da Key Vault.  

Nota: questo processo, fino al passaggio 3.1, è un'operazione una tantum.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Creazione di un certificato con una CA partner di Key Vault

![Creare un certificato con un'autorità di certificazione partner di Key Vault](../media/certificate-authority-2.png)

**Passaggio 4**. Le descrizioni seguenti corrispondono ai passaggi contrassegnati con un numero in verde nel diagramma precedente.  
  (1) Nel diagramma precedente, l'applicazione crea un certificato, operazione che internamente inizia con la creazione di una chiave nell'insieme di credenziali delle chiavi.  
  (2)-Key Vault invia una richiesta di certificato TLS/SSL alla CA.  
  (3) L'applicazione esegue il polling di Key Vault, in un processo di ciclo e attesa, per il completamento del certificato. La creazione del certificato è completa quando Key Vault riceve la risposta della CA con il certificato X.509.  
  (4)-la CA risponde alla richiesta di certificato TLS/SSL di Key Vault con un certificato TLS/SSL X509.  
  (5) La creazione del nuovo certificato viene completata con l'unione del certificato X509 per la CA.  

  L'utente di Key Vault crea un certificato specificando i criteri.

  -   Ripetere in base alle esigenze.  
  -   Vincoli dei criteri  
      -   Proprietà X509  
      -   Proprietà della chiave  
      -   Informazioni di riferimento per il provider, ad esempio MyDigiCertIssure  
      -   Informazioni relative al rinnovo, ad esempio 90 giorni prima della scadenza  

  - Il processo di creazione di un certificato è in genere asincrono e prevede il polling dell'insieme di credenziali delle chiavi per lo stato dell'operazione di creazione del certificato.  
[Operazione di recupero del certificato](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Stato: operazione completata, non riuscita con informazioni sull'errore o annullata  
      -   A causa del ritardo nella creazione può essere avviata un'operazione di annullamento. L'annullamento può essere applicato o meno.  

## <a name="import-a-certificate"></a>Importazione di un certificato  
 In alternativa, è possibile importare un certificato, PFX o PEM, in Key Vault.  

 Per importare un certificato, è necessario un file PEM o PFX su disco con una chiave privata. 
-   È necessario specificare il nome dell'insieme di credenziali e il nome del certificato. I criteri sono facoltativi.

-   I file PEM/PFX contengono attributi che possono essere analizzati da Key Vault per popolare i criteri dei certificati. Se i criteri dei certificati sono già specificati, Key Vault proverà a trovare una corrispondenza con i dati del file PFX/PEM.  

-   Al termine dell'importazione, le operazioni successive useranno i nuovi criteri, ossia le nuove versioni.  

-   Se non vengono eseguite altre operazioni, Key Vault per prima cosa invierà un avviso di scadenza. 

-   L'utente, inoltre, può modificare i criteri, che sono funzionali al momento dell'importazione ma contengono impostazioni predefinite nel caso in cui non vengano specificate informazioni durante l'importazione, Ad esempio, se non sono presenti informazioni sull'autorità di certificazione.  

### <a name="formats-of-import-we-support"></a>Formati di importazione supportati
Sono supportati i seguenti tipi di importazione per il formato di file PEM. Un singolo certificato con codifica PEM insieme a una chiave codificata PKCS # 8 e non crittografata con la seguente

-----BEGIN CERTIFICATE----------END CERTIFICATE-----

-----INIZIO CHIAVE PRIVATA----------CHIAVE PRIVATA FINALE-----

Nell'Unione del certificato sono supportati 2 formati basati su PEM. È possibile unire un solo certificato con codifica PKCS # 8 o un file P7B con codifica Base64. -----BEGIN CERTIFICATE----------END CERTIFICATE-----

Attualmente non sono supportate le chiavi EC nel formato PEM.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Creazione di un certificato con una CA non partner di Key Vault  
 Questo metodo consente di usare CA diverse dai provider partner di Key Vault, in modo che l'organizzazione possa avvalersi di una CA di propria scelta.  

![Creare un certificato con un'autorità di certificazione personalizzata](../media/certificate-authority-1.png)  

 Le descrizioni dei passaggi seguenti corrispondono ai passaggi contrassegnati con un numero in verde nel diagramma precedente.  

  (1) Nel diagramma precedente, l'applicazione crea un certificato, operazione che internamente inizia con la creazione di una chiave nell'insieme di credenziali delle chiavi.  

  (2) Key Vault restituisce all'applicazione una richiesta di firma del certificato.  

  (3) L'applicazione passa la richiesta di firma del certificato alla CA scelta.  

  (4) La CA scelta risponde con un certificato X509.  

  (5) L'applicazione completa la creazione del nuovo certificato con l'unione del certificato X509 proveniente dalla CA.

