---
title: Configurare la sicurezza per il gruppo di server PostgreSQL Hyperscale abilitato per Azure Arc
description: Configurare la sicurezza per il gruppo di server PostgreSQL Hyperscale abilitato per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d6e27fddceb69efbb2c1697c09ee9b61d7f38ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687975"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Configurare la sicurezza per il gruppo di server PostgreSQL Hyperscale abilitato per Azure Arc

In questo documento vengono descritti i vari aspetti correlati alla sicurezza del gruppo di server:
- Crittografia di dati inattivi
- Gestione degli utenti
   - Prospettive generali
   - Modificare la password dell'utente amministratore _Postgres_
- Audit

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Crittografia di dati inattivi
È possibile implementare la crittografia dei dati inattivi crittografando i dischi in cui vengono archiviati i database e/o usando le funzioni di database per crittografare i dati inseriti o aggiornati.

### <a name="hardware-linux-host-volume-encryption"></a>Hardware: crittografia del volume host Linux
Implementare la crittografia dei dati di sistema per proteggere tutti i dati che risiedono nei dischi usati dall'installazione di Azure Arc Enabled Data Services. Per altre informazioni su questo argomento, vedere:
- [Crittografia dei dati](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) inattivi in Linux in generale 
- Crittografia del disco con `cryptsetup` comando LUKS Encrypt (Linux) ( https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) in particolare poiché Azure Arc Enabled Data Services viene eseguito nell'infrastruttura fisica fornita dall'utente, l'utente è responsabile della protezione dell'infrastruttura.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Software: usare l' `pgcrypto` estensione PostgreSQL nel gruppo di server
Oltre a crittografare i dischi usati per ospitare la configurazione di Azure Arc, è possibile configurare il gruppo di server di scalabilità PostgreSQL abilitato per Azure Arc per esporre i meccanismi che le applicazioni possono usare per crittografare i dati nei database. L' `pgcrypto` estensione fa parte delle `contrib` estensioni di Postgres ed è disponibile nel gruppo di server di iperscalabilità di PostgreSQL abilitato per Azure Arc. Per informazioni dettagliate sull' `pgcrypto` estensione, vedere [qui](https://www.postgresql.org/docs/current/pgcrypto.html).
In breve, con i comandi seguenti, si Abilita l'estensione, la si crea e si usa:


#### <a name="create-the-pgcrypto-extension"></a>Creare l' `pgcrypto` estensione
Connettersi al gruppo di server con lo strumento client desiderato ed eseguire la query standard PostgreSQL:
```console
CREATE EXTENSION pgcrypto;
```

> [Qui](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md) sono disponibili informazioni dettagliate su come connettersi.

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Verificare l'elenco delle estensioni pronte per l'uso nel gruppo di server
È possibile verificare che l' `pgcrypto` estensione sia pronta per l'uso elencando le estensioni disponibili nel gruppo di server.
Connettersi al gruppo di server con lo strumento client desiderato ed eseguire la query standard PostgreSQL:
```console
select * from pg_extension;
```
Dovrebbe essere visualizzato `pgcrypto` se è stato abilitato e creato con i comandi indicati in precedenza.

#### <a name="use-the-pgcrypto-extension"></a>Utilizzare l' `pgcrypto` estensione
A questo punto è possibile modificare il codice delle applicazioni in modo che usino le funzioni offerte da `pgcrypto` :
- Funzioni di hashing generali
- Funzioni di hashing delle password
- Funzioni di crittografia PGP
- Funzioni di crittografia non elaborate
- Funzioni di dati casuali

Ad esempio, per generare valori hash. Eseguire il comando:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

Restituisce l'hash seguente:

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Oppure, ad esempio:

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

Restituisce l'hash seguente:

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Oppure, ad esempio, per archiviare i dati crittografati come una password:

Si supponga che l'applicazione archivi i segreti nella tabella seguente:

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

E crittografare la password durante la creazione di un utente:

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

A questo punto, si noterà che la password è crittografata:

```console
select * from mysecrets;
```

Output:

- USERid: 1
- Nome utente: me
- USERpassword: $1 $ Uc7jzZOp $ NTfcGo7F10zGOkXOwjHy31

Quando ci si connette con l'applicazione e si passa una password, la ricerca viene visualizzata nella `mysecrets` tabella e restituisce il nome dell'utente in caso di corrispondenza tra la password fornita all'applicazione e le password archiviate nella tabella. Ad esempio:

- Viene passata la password errata:
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Output 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- Viene passata la password corretta:

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   Output:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

Questo piccolo esempio dimostra che è possibile crittografare i dati inattivi (archiviare i dati crittografati) in Azure Arc abilitata per l'iperscalabilità di PostgreSQL usando l'estensione Postgres `pgcrypto` e le applicazioni possono usare funzioni offerte da `pgcrypto` per modificare i dati crittografati.

## <a name="user-management"></a>Gestione degli utenti
### <a name="general-perspectives"></a>Prospettive generali
È possibile usare il metodo Postgres Standard per creare utenti o ruoli. Tuttavia, in tal caso, gli artefatti saranno disponibili solo nel ruolo di coordinatore. In anteprima, gli utenti o i ruoli non potranno ancora accedere ai dati distribuiti all'esterno del nodo coordinatore e nei nodi di lavoro del gruppo di server. Il motivo è che in anteprima la definizione dell'utente non viene replicata nei nodi di lavoro.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>Modificare la password dell'utente amministratore _Postgres_
L'iperscalabilità di PostgreSQL abilitata per Azure Arc viene fornita con l'utente amministratore _Postgres Standard_ per il quale si imposta la password quando si crea il gruppo di server.
Il formato generale del comando per modificare la password è:
```console
azdata arc postgres server edit --name <server group name> --admin-password
```

Dove `--admin-password` è un valore booleano che si riferisce alla presenza di un valore nella variabile di ambiente AZDATA_PASSWORD **sessione** .
Se la variabile di ambiente della **sessione** AZDATA_PASSWORD esiste e contiene un valore, l'esecuzione del comando precedente imposterà la password dell'utente postgres sul valore di questa variabile di ambiente.

Se la variabile di ambiente della **sessione** AZDATA_PASSWORD esiste ma non ha valore o la variabile di ambiente della **sessione** di AZDATA_PASSWORD non esiste, l'esecuzione del comando precedente richiederà all'utente di immettere una password in modo interattivo

#### <a name="change-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>Modificare la password dell'utente amministratore Postgres in modo interattivo

1. Elimina la variabile di ambiente della **sessione** di AZDATA_PASSWORD o ne elimina il valore
2. Eseguire il comando:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Ad esempio:
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   Verrà richiesto di immettere la password e di confermarla:
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   Quando la password viene aggiornata, l'output del comando Mostra:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="change-the-password-of-the-postgres-administrative-user-using-the-azdata_password-session-environment-variable"></a>Modificare la password dell'utente amministratore Postgres usando la variabile di ambiente AZDATA_PASSWORD **sessione** :
1. Impostare il valore della variabile di ambiente della **sessione** di AZDATA_PASSWORD su ciò che si desidera impostare come password.
2. Eseguire il comando:
   ```console
   azdata arc postgres server edit --name <server group name> --admin-password
   ```
   Ad esempio:
   ```console
   azdata arc postgres server edit -n postgres01 --admin-password
   ```
   
   Quando la password viene aggiornata, l'output del comando Mostra:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> Per verificare se la variabile di ambiente della sessione di AZDATA_PASSWORD esiste e il valore che possiede, eseguire:
> - In un client Linux:
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - In un client Windows con PowerShell:
> ```console
> echo $env:AZDATA_PASSWORD
> ```

## <a name="audit"></a>Audit

Per gli scenari di controllo, configurare il gruppo di server per l'uso delle `pgaudit` estensioni di postgres. Per altre informazioni `pgaudit` , vedere il [ `pgAudit` progetto GitHub](https://github.com/pgaudit/pgaudit/blob/master/README.md). Per abilitare l' `pgaudit` estensione nel gruppo di server, leggere [usare le estensioni PostgreSQL](using-extensions-in-postgresql-hyperscale-server-group.md).


## <a name="next-steps"></a>Passaggi successivi
- Vedere l' [ `pgcrypto` estensione](https://www.postgresql.org/docs/current/pgcrypto.html)
- Vedere [usare le estensioni PostgreSQL](using-extensions-in-postgresql-hyperscale-server-group.md)

