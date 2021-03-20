---
title: accesso azcopy | Microsoft Docs
description: Questo articolo contiene informazioni di riferimento per il comando azcopy login.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: e4740870dd2d9748aad55150ce1946e3eb666619
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878359"
---
# <a name="azcopy-login"></a>azcopy login

Accede a Azure Active Directory per accedere alle risorse di archiviazione di Azure.

## <a name="synopsis"></a>Riepilogo

Accedere a Azure Active Directory per accedere alle risorse di archiviazione di Azure.

Per essere autorizzati all'account di archiviazione di Azure, è necessario assegnare il ruolo di **collaboratore dati BLOB di archiviazione** all'account utente nel contesto dell'account di archiviazione, del gruppo di risorse padre o della sottoscrizione padre.

Questo comando memorizza nella cache le informazioni di accesso crittografate per l'utente corrente usando i meccanismi predefiniti del sistema operativo.

> [!IMPORTANT]
> Se si imposta una variabile di ambiente tramite la riga di comando, tale variabile sarà leggibile nella cronologia della riga di comando. Si consiglia di cancellare le variabili che contengono credenziali dalla cronologia della riga di comando. Per evitare che le variabili vengano visualizzate nella cronologia, è possibile utilizzare uno script per richiedere le credenziali dell'utente e per impostare la variabile di ambiente.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Articoli concettuali correlati

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)
- [Trasferire i dati con AzCopy e l'archiviazione BLOB](./storage-use-azcopy-v10.md#transfer-data)
- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)
- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)

## <a name="examples"></a>Esempio

Accedere in modo interattivo con l'ID tenant di AAD predefinito impostato su comune:

```azcopy
azcopy login
```

Accedere in modo interattivo con un ID tenant specificato:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Accedere usando l'identità assegnata dal sistema di una macchina virtuale (VM):

```azcopy
azcopy login --identity
```

Accedere usando l'identità assegnata dall'utente di una macchina virtuale e un ID client dell'identità del servizio:
  
```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```
 
Accedere usando l'identità assegnata dall'utente di una macchina virtuale e un ID oggetto dell'identità del servizio:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Accedere usando l'identità assegnata dall'utente di una macchina virtuale e un ID risorsa dell'identità del servizio:
 
```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Accedere come entità servizio usando un segreto client: impostare la variabile di ambiente AZCOPY_SPA_CLIENT_SECRET al segreto client per l'autenticazione dell'entità servizio basata su segreto.

```azcopy
azcopy login --service-principal --application-id <your service principal's application ID>
```

Accedere come entità servizio usando un certificato e la relativa password:

Impostare la variabile di ambiente AZCOPY_SPA_CERT_PASSWORD sulla password del certificato per l'autenticazione dell'entità servizio basata su certificato:

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert --application-id <your service principal's application ID>
```

Trattare `/path/to/my/cert` come un percorso di un file PEM o Pkcs12. AzCopy non raggiunge l'archivio certificati di sistema per ottenere il certificato.

`--certificate-path` è obbligatorio quando si esegue l'autenticazione dell'entità servizio basata su certificato.

## <a name="options"></a>Opzioni

**--AAD-endpoint** stringa l'endpoint Azure Active Directory da usare. Il valore predefinito https://login.microsoftonline.com) è corretto per il cloud di Azure globale. Impostare questo parametro quando si esegue l'autenticazione in un cloud nazionale. Non necessario per identità del servizio gestita.

**--Application-ID** stringa ID applicazione dell'identità assegnata dall'utente. Obbligatorio per l'autenticazione dell'entità servizio.

**--Certificate-percorso** stringa percorso del certificato per l'autenticazione del nome SPN. Obbligatorio per l'autenticazione dell'entità servizio basata su certificato.

**--**   guida per il `azcopy login` comando.

**--Identity**   Accedere usando l'identità della macchina virtuale, nota anche come identità del servizio gestito.

**--Identity-client-ID** stringa ID client dell'identità assegnata dall'utente.

**--Identity-Object-ID** oggetto stringa ID dell'identità assegnata dall'utente.

**--Identity-Resource-ID** stringa ID risorsa dell'identità assegnata dall'utente.

**--Service-Principal**   Accedere tramite un nome dell'entità servizio (SPN) utilizzando un certificato o un segreto. Il segreto client o la password del certificato deve essere inserita nella variabile di ambiente appropriata. Digitare AzCopy ENV per visualizzare i nomi e le descrizioni delle variabili di ambiente.

**--Tenant-ID** stringa il Azure Active Directory ID tenant da usare per l'accesso interattivo al dispositivo OAuth.

## <a name="options-inherited-from-parent-commands"></a>Opzioni ereditate dai comandi padre

|Opzione|Descrizione|
|---|---|
|--Cap-Mbps float|Viene riversata la velocità di trasferimento, in megabit al secondo. Una velocità effettiva momentanea potrebbe variare leggermente rispetto al limite. Se questa opzione è impostata su zero o viene omessa, la velocità effettiva non è limitata.|
|--output-tipo stringa|Formato dell'output del comando. Le scelte includono: text, JSON. Il valore predefinito è "Text".|
|--trusted-Microsoft-suffissi stringa   |Specifica i suffissi di dominio aggiuntivi in cui è possibile inviare i token di accesso Azure Active Directory.  Il valore predefinito è'*. Core.Windows.NET;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net ". Tutti gli elencati qui vengono aggiunti al valore predefinito. Per la sicurezza, è consigliabile inserire qui solo Microsoft Azure domini. Separare più voci con un punto e virgola.|

## <a name="see-also"></a>Vedi anche

- [azcopy](storage-ref-azcopy.md)
