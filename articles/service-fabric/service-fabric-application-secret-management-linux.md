---
title: Configurare un certificato di crittografia nei cluster Linux
description: Informazioni su come configurare un certificato di crittografia e crittografare segreti in cluster Linux.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78969023"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Configurare un certificato di crittografia e crittografare segreti in cluster Linux
Questo articolo illustra come configurare un certificato di crittografia e usarlo per crittografare segreti in cluster Linux. Per i cluster Windows, vedere [Configurare un certificato di crittografia e crittografare segreti in cluster Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Ottenere un certificato di crittografia dei dati
Il certificato di crittografia dei dati viene usato esclusivamente per la crittografia e decrittografia dei [parametri][parameters-link] del file Settings.xml di un servizio e delle [variabili di ambiente][environment-variables-link] del file ServiceManifest.xml di un servizio. Non viene usato per l'autenticazione o per la firma di testo crittografato. Il certificato deve soddisfare i requisiti seguenti:

* Il certificato deve includere una chiave privata.
* L'uso delle chiavi del certificato deve includere la crittografia dei dati (10) e non deve includere l'autenticazione del server o del client.

  Ad esempio, i comandi seguenti possono essere usati per generare il certificato necessario tramite OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Installare il certificato nel cluster
Il certificato deve essere installato in ogni nodo del cluster nel percorso `/var/lib/sfcerts`. L'account utente in cui viene eseguito il servizio (sfuser per impostazione predefinita) **deve avere accesso in lettura** al certificato installato (ossia `/var/lib/sfcerts/TestCert.pem` per l'esempio corrente).

## <a name="encrypt-secrets"></a>Crittografare i segreti
Per crittografare un segreto è possibile usare il frammento di codice seguente. Questo frammento di codice si limita a crittografare il valore e **non** firma il testo crittografato. **È necessario usare** lo stesso certificato di crittografia installato nel cluster per produrre il testo crittografato per i valori del segreto.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
L'output della stringa con codifica Base 64 risultante in encrypted.txt contiene sia il testo crittografato del segreto sia le informazioni relative al certificato usato per crittografarlo. È possibile verificarne la validità decrittografandolo con OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Passaggi successivi
Vedere le informazioni su come [specificare i segreti crittografati in un'applicazione][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
