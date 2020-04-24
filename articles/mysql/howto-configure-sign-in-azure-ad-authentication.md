---
title: Usare Azure Active Directory-database di Azure per MySQL
description: Informazioni su come configurare Azure Active Directory (Azure AD) per l'autenticazione con database di Azure per MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 0403edadd491609c2c88d5b5ac6980d97163f8d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299006"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Usare Azure Active Directory per l'autenticazione con MySQL

Questo articolo illustra i passaggi per configurare Azure Active Directory l'accesso con database di Azure per MySQL e come connettersi usando un token di Azure AD.

> [!IMPORTANT]
> L'autenticazione Azure AD per database di Azure per MySQL è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="setting-the-azure-ad-admin-user"></a>Impostazione dell'utente amministratore di Azure AD

Solo un utente amministratore Azure AD può creare o abilitare gli utenti per l'autenticazione basata su Azure AD. Per creare e Azure AD utente amministratore, attenersi alla procedura seguente:

1. Nella portale di Azure selezionare l'istanza del database di Azure per MySQL che si vuole abilitare per l'Azure AD.
2. In impostazioni selezionare Active Directory amministratore:

![imposta amministratore di Azure ad][2]

3. Selezionare un utente Azure AD valido nel tenant del cliente da Azure AD amministratore.

> [!IMPORTANT]
> Quando si imposta l'amministratore, viene aggiunto un nuovo utente al database di Azure per il server MySQL con autorizzazioni di amministratore completo.

Per ogni server MySQL è possibile creare solo un Azure AD amministratore. la selezione di un'altra operazione sovrascriverà l'amministratore di Azure AD esistente configurato per il server.

In una versione futura verrà supportata la specifica di un gruppo di Azure AD anziché di un singolo utente per avere più amministratori, tuttavia questa operazione non è ancora supportata.

Dopo aver configurato l'amministratore, è ora possibile accedere:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Connessione al database di Azure per MySQL con Azure AD

Il diagramma generale seguente riepiloga il flusso di lavoro di uso dell'autenticazione Azure AD con database di Azure per MySQL:

![flusso di autenticazione][1]

Abbiamo progettato l'integrazione Azure AD per lavorare con gli strumenti di MySQL comuni, come l'interfaccia della riga di comando di MySQL, che non sono Azure AD consapevoli e supportano solo la specifica di nome utente e password quando ci si connette a MySQL. Viene passato il token di Azure AD come password, come illustrato nella figura precedente.

Attualmente sono stati testati i client seguenti:

- MySQLWorkbench 
- INTERFACCIA della riga di comando di MySQL

Sono stati anche testati i driver dell'applicazione più comuni. è possibile visualizzare i dettagli alla fine di questa pagina.

Questi sono i passaggi necessari per eseguire l'autenticazione di un utente o un'applicazione con Azure AD descritto di seguito:

### <a name="step-1-authenticate-with-azure-ad"></a>Passaggio 1: eseguire l'autenticazione con Azure AD

Assicurarsi che sia installata l' [interfaccia](/cli/azure/install-azure-cli)della riga di comando di Azure.

Richiamare lo strumento dell'interfaccia della riga di comando di Azure per l'autenticazione con Azure AD. È necessario fornire il Azure AD ID utente e la password.

```
az login
```

Questo comando avvierà una finestra del browser nella pagina di autenticazione del Azure AD.

> [!NOTE]
> È inoltre possibile utilizzare Azure Cloud Shell per eseguire questi passaggi.
> Tenere presente che quando si recupera Azure AD token di accesso nel Azure Cloud Shell sarà necessario chiamare `az login` e accedere di nuovo in modo esplicito (nella finestra separata con un codice). Dopo l'accesso, il `get-access-token` comando funzionerà come previsto.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Passaggio 2: recuperare Azure AD token di accesso

Richiamare lo strumento dell'interfaccia della riga di comando di Azure per acquisire un token di accesso per l'utente autenticato Azure AD dal passaggio 1 per accedere a database di Azure per MySQL.

Esempio (per il cloud pubblico):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

Il valore della risorsa precedente deve essere specificato esattamente come illustrato. Per gli altri cloud, il valore della risorsa può essere cercato usando:

```shell
az cloud show
```

Per l'interfaccia della riga di comando di Azure versione 2.0.71 e successive, il comando può essere specificato nella versione più comoda seguente per tutti i cloud:

```shell
az account get-access-token --resource-type oss-rdbms
```

Una volta completata l'autenticazione, Azure AD restituirà un token di accesso:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Il token è una stringa di base 64 che codifica tutte le informazioni sull'utente autenticato e che è destinata al servizio database di Azure per MySQL.

> [!NOTE]
> La validità del token di accesso è qualsiasi tra 5 minuti e 60 minuti. Si consiglia di ottenere il token di accesso appena prima di avviare l'accesso a database di Azure per MySQL.

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Passaggio 3: usare il token come password per l'accesso con MySQL

Quando ci si connette, è necessario usare il token di accesso come password utente MySQL. Quando si usano client GUI come MySQLWorkbench, è possibile usare il metodo precedente per recuperare il token. 

Quando si usa l'interfaccia della riga di comando, è possibile usare questa breve mano per connettersi: 

**Esempio (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```

Si noti l'impostazione "Enable-non crittografato-plugin": è necessario usare una configurazione simile con altri client per assicurarsi che il token venga inviato al server senza che venga eseguito l'hashing.

A questo punto è stata eseguita l'autenticazione al server MySQL usando l'autenticazione Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Creazione di Azure AD utenti nel database di Azure per MySQL

Per aggiungere un utente Azure AD al database di Azure per il database MySQL, seguire questa procedura dopo la connessione. vedere la sezione successiva relativa alla modalità di connessione:

1. Assicurarsi prima di tutto che l' `<user>@yourtenant.onmicrosoft.com` utente Azure ad sia un utente valido in Azure ad tenant.
2. Accedere all'istanza del database di Azure per MySQL come utente amministratore Azure AD.
3. Creare un `<user>@yourtenant.onmicrosoft.com` utente nel database di Azure per MySQL.

**Esempio:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Per i nomi utente che superano 32 caratteri, è consigliabile usare invece un alias, da usare per la connessione: 

Esempio:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```

> [!NOTE]
> L'autenticazione di un utente tramite Azure AD non concede all'utente alcuna autorizzazione per accedere agli oggetti nel database di Azure per il database MySQL. È necessario concedere manualmente all'utente le autorizzazioni necessarie.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Creazione di gruppi di Azure AD nel database di Azure per MySQL

Per abilitare un gruppo di Azure AD per l'accesso al database, usare lo stesso meccanismo utilizzato per gli utenti, ma specificare invece il nome del gruppo:

**Esempio:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Quando si esegue l'accesso, i membri del gruppo utilizzeranno i propri token di accesso personali, ma firmeranno con il nome del gruppo specificato come nome utente.

## <a name="token-validation"></a>Convalida di token

Azure AD autenticazione in database di Azure per MySQL garantisce che l'utente esista nel server MySQL e controlla la validità del token convalidando il contenuto del token. Vengono eseguiti i seguenti passaggi di convalida del token:

-   Il token è firmato da Azure AD e non è stato alterato
-   Il token è stato emesso da Azure AD per il tenant associato al server
-   Il token non è scaduto
-   Il token è per la risorsa database di Azure per MySQL (e non per un'altra risorsa di Azure)

## <a name="compatibility-with-application-drivers"></a>Compatibilità con i driver dell'applicazione

La maggior parte dei driver è supportata, ma assicurarsi di usare le impostazioni per l'invio della password in testo non crittografato, in modo che il token venga inviato senza modifiche.

* C/C++
  * libmysqlclient: supportato
  * MySQL-Connector-c + +: supportato
* Java
  * Connettore/J (MySQL-Connector-Java): supportato. è necessario usare `useSSL` l'impostazione
* Python
  * Connettore/Python: supportato
* Ruby
  * mysql2: supportato
* .NET
  * MySQL-Connector-Net: supportato, è necessario aggiungere un plug-in per mysql_clear_password
  * MySQL-NET/MySqlConnector: supportato
* Node.js
  * mysqljs: non supportato (non invia il token in testo non crittografato senza patch)
  * node-mysql2: supportato
* Perl
  * DBD:: MySQL: supportato
  * NET:: MySQL: non supportato
* Go
  * go-SQL-driver: supportato, Aggiungi `?tls=true&allowCleartextPasswords=true` a stringa di connessione

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i concetti generali per [l'autenticazione Azure Active Directory con database di Azure per MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
