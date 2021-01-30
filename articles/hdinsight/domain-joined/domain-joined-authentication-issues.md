---
title: Problemi di autenticazione in Azure HDInsight
description: Problemi di autenticazione in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.openlocfilehash: b5a55b3b5f9affcd9f34e1c0d4c439a7ada8c0b9
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095125"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Problemi di autenticazione in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

Nei cluster sicuri supportati da Azure Data Lake (Gen1 o Gen2), quando gli utenti del dominio accedono ai servizi cluster tramite il gateway HDI, ad esempio l'accesso al portale Apache Ambari, il gateway HDI tenterà di ottenere un token OAuth da Azure Active Directory (Azure AD) prima di ottenere un ticket Kerberos da Azure AD DS. L'autenticazione può avere esito negativo in una di queste fasi. Questo articolo ha lo scopo di eseguire il debug di alcuni di questi problemi.

Quando l'autenticazione ha esito negativo, vengono richieste le credenziali. Se si annulla questa finestra di dialogo, verrà visualizzato il messaggio di errore. Di seguito sono riportati alcuni dei messaggi di errore comuni:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant o unauthorized_client, 50126

### <a name="issue"></a>Problema

L'accesso non riesce per gli utenti federati con codice di errore 50126 (l'accesso ha esito positivo per gli utenti del cloud). Il messaggio di errore è simile al seguente:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Causa

Azure AD codice di errore 50126 indica che il `AllowCloudPasswordValidation` criterio non è stato impostato dal tenant.

### <a name="resolution"></a>Soluzione

L'amministratore globale del tenant di Azure AD deve consentire Azure AD di utilizzare gli hash delle password per gli utenti supportati da ADFS.  Applicare il `AllowCloudPasswordValidationPolicy` come illustrato nell'articolo [usare Enterprise Security Package in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant o unauthorized_client, 50034

### <a name="issue"></a>Problema

L'accesso ha esito negativo con codice di errore 50034. Il messaggio di errore è simile al seguente:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Causa

Nome utente errato (inesistente). L'utente non usa lo stesso nome utente usato in portale di Azure.

### <a name="resolution"></a>Soluzione

Usare lo stesso nome utente che funziona in tale portale.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant o unauthorized_client, 50053

### <a name="issue"></a>Problema

L'account utente è bloccato, codice di errore 50053. Il messaggio di errore è simile al seguente:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Causa

Troppi tentativi di accesso con una password non corretta.

### <a name="resolution"></a>Soluzione

Attendere 30 minuti, quindi arrestare tutte le applicazioni che potrebbero tentare di eseguire l'autenticazione.

---

## <a name="invalid_grant-or-unauthorized_client-50053-2"></a>invalid_grant o unauthorized_client, 50053 (#2)

### <a name="issue"></a>Problema

Password scaduta, codice errore 50053. Il messaggio di errore è simile al seguente:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Causa

La password è scaduta.

### <a name="resolution"></a>Soluzione

Modificare la password nel portale di Azure (nel sistema locale) e attendere 30 minuti per l'aggiornamento della sincronizzazione.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Problema

Messaggio di errore di ricezione `interaction_required` .

### <a name="cause"></a>Causa

L'autenticazione a più fattori o i criteri di accesso condizionale vengono applicati all'utente. Poiché l'autenticazione interattiva non è ancora supportata, l'utente o il cluster deve essere esentato dall'accesso condizionale/con autenticazione a più fattori. Se si sceglie di esentare il cluster (criterio di esenzione basato su indirizzi IP), verificare che Active Directory `ServiceEndpoints` sia abilitato per tale vnet.

### <a name="resolution"></a>Soluzione

Usare i criteri di accesso condizionale ed esentare i cluster HDInsight da multi-factor authentication, come illustrato nella pagina relativa alla [configurazione di un cluster HDInsight con Enterprise Security Package tramite Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

---

## <a name="sign-in-denied"></a>Accesso negato

### <a name="issue"></a>Problema

L'accesso è stato negato.

### <a name="cause"></a>Causa

Per ottenere questa fase, l'autenticazione OAuth non è un problema, ma l'autenticazione Kerberos è. Se il cluster è supportato da ADLS, l'accesso OAuth ha avuto esito positivo prima del tentativo di autenticazione Kerberos. Nei cluster WASB non è stato effettuato un tentativo di accesso OAuth. Potrebbero esserci diversi motivi per cui gli hash delle password di tipo errore Kerberos non sono sincronizzati, l'account utente è stato bloccato in Azure AD DS e così via. Gli hash delle password vengono sincronizzati solo quando l'utente cambia la password. Quando si crea l'istanza di Azure AD DS, viene avviata la sincronizzazione delle password che vengono modificate dopo la creazione. La sincronizzazione delle password impostate prima del suo inizio non verrà sincronizzata in maniera retroattiva.

### <a name="resolution"></a>Soluzione

Se si ritiene che le password potrebbero non essere sincronizzate, provare a modificare la password e attendere alcuni minuti per la sincronizzazione.

Provare a eseguire SSH in a. è necessario provare a eseguire l'autenticazione (kinit) con le stesse credenziali utente da un computer aggiunto al dominio. Connettersi tramite SSH al nodo Head/Edge con un utente locale ed eseguire kinit.

---

## <a name="kinit-fails"></a>kinit ha esito negativo

### <a name="issue"></a>Problema

Kinit ha esito negativo.

### <a name="cause"></a>Causa

Variabile.

### <a name="resolution"></a>Soluzione

Per la riuscita di kinit, è necessario conoscerla `sAMAccountName` (si tratta del nome dell'account breve senza l'area di autenticazione). `sAMAccountName` è in genere il prefisso dell'account (ad esempio, Bob in `bob@contoso.com` ). Per alcuni utenti, può essere diverso. È necessario avere la possibilità di sfogliare o cercare la directory per apprendere il `sAMAccountName` .

Modi per trovare `sAMAccountName` :

* Se è possibile accedere a Ambari usando l'amministratore di Ambari locale, esaminare l'elenco di utenti.

* Se si dispone di un [computer Windows aggiunto](../../active-directory-domain-services/tutorial-create-management-vm.md)a un dominio, è possibile usare gli strumenti standard di Windows ad per esplorare. Questa operazione richiede un account di lavoro nel dominio.

* Dal nodo Head è possibile usare i comandi SAMBA per eseguire la ricerca. Questa operazione richiede una sessione Kerberos valida (kinit riuscito). ricerca NET ads "(userPrincipalName = Bob *)"

    I risultati di ricerca/esplorazione dovrebbero visualizzare l' `sAMAccountName` attributo. Inoltre, è possibile esaminare altri attributi `pwdLastSet` , ad esempio, `badPasswordTime` e `userPrincipalName` così via, per verificare se tali proprietà corrispondono a quanto previsto.

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit ha esito negativo con errore di preautenticazione

### <a name="issue"></a>Problema

Kinit ha esito negativo con `Preauthentication` errori.

### <a name="cause"></a>Causa

Nome utente o password non corretta.

### <a name="resolution"></a>Soluzione

Verificare il nome utente e la password. Controllare anche le altre proprietà descritte in precedenza. Per abilitare il debug dettagliato, eseguire `export KRB5_TRACE=/tmp/krb.log` dalla sessione prima di provare kinit.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>Il comando job/HDFS ha esito negativo a causa di TokenNotFoundException

### <a name="issue"></a>Problema

Il comando job/HDFS ha esito negativo a causa di `TokenNotFoundException` .

### <a name="cause"></a>Causa

Il token di accesso OAuth richiesto non è stato trovato perché il processo o il comando abbia esito positivo. Il driver ADLS/ABFS tenterà di recuperare il token di accesso OAuth dal servizio credenziali prima di eseguire le richieste di archiviazione. Questo token viene registrato quando si accede al portale di Ambari usando lo stesso utente.

### <a name="resolution"></a>Soluzione

Assicurarsi di aver effettuato l'accesso al portale di Ambari una volta tramite il nome utente la cui identità viene utilizzata per eseguire il processo.

---

## <a name="error-fetching-access-token"></a>Errore durante il recupero del token di accesso

### <a name="issue"></a>Problema

Messaggio di errore ricevuto dall'utente `Error fetching access token` .

### <a name="cause"></a>Causa

Questo errore si verifica in modo intermittente quando gli utenti provano ad accedere al ADLS Gen2 usando gli ACL e il token Kerberos è scaduto.

### <a name="resolution"></a>Soluzione

* Per Azure Data Lake Storage Gen1, pulire la cache del browser e accedere di nuovo a Ambari.

* Per Azure Data Lake Storage Gen2, eseguire `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>` per l'utente a cui l'utente sta tentando di accedere

---

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]