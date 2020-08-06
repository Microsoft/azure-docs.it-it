---
title: Operazioni di Azure Active Directory Connect Health
description: Questo articolo descrive le operazioni aggiuntive che possono essere eseguite dopo avere distribuito Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e72e3cb3ccc6273a9b698475c5e5aa0bb87b8b7
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87828796"
---
# <a name="azure-active-directory-connect-health-operations"></a>Operazioni di Azure Active Directory Connect Health
Questo argomento descrive le varie operazioni che è possibile eseguire tramite Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Abilitare le notifiche di posta elettronica
È possibile configurare il servizio Azure AD Connect Health per l'invio di notifiche di posta elettronica quando vengono generati avvisi indicanti che l'infrastruttura di gestione delle identità non è integra. Questo errore si verifica quando viene generato un avviso e quando viene risolto.

![Schermata delle impostazioni delle notifiche di posta elettronica di Azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> Le notifiche di posta elettronica sono abilitate per impostazione predefinita.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Per abilitare le notifiche di posta elettronica di Azure AD Connect Health
1. Aprire il pannello **Avvisi** per il servizio per cui si desidera ricevere una notifica di posta elettronica.
2. Nella barra delle azioni fare clic su **Impostazioni di notifica**.
3. Impostare l'opzione di notifica di posta elettronica su **ON**.
4. Selezionare la casella di controllo per fare in modo che tutti gli amministratori globali ricevano notifiche di posta elettronica.
5. Se si desidera ricevere notifiche di posta elettronica ad altri indirizzi di posta elettronica, specificarli nella casella **Destinatari di posta elettronica aggiuntivi**. Per rimuovere un indirizzo di posta elettronica da questo elenco, fare clic con il pulsante destro del mouse sulla voce interessata e scegliere **Elimina**.
6. Per finalizzare le modifiche, fare clic su **Salva**. Le modifiche diventano effettive dopo il salvataggio.

>[!NOTE] 
> Quando si verificano problemi durante l'elaborazione delle richieste di sincronizzazione nel servizio back-end, questo servizio invia un messaggio di posta elettronica di notifica con i dettagli dell'errore agli indirizzi di posta elettronica dei contatti amministrativi del tenant. I clienti hanno inviato commenti e suggerimenti che, in alcuni casi, il volume di questi messaggi è eccessivamente grande, quindi si sta cambiando il modo in cui vengono inviati i messaggi. 
>
> Anziché inviare un messaggio per ogni errore di sincronizzazione ogni volta che si verifica, verrà inviato un digest giornaliero di tutti gli errori restituiti dal servizio back-end. Questo consente ai clienti di elaborare questi errori in modo più efficiente e di ridurre il numero di messaggi di errore duplicati.
>
> Si prevede che questa modifica venga implementata il 15 gennaio 2020.

## <a name="delete-a-server-or-service-instance"></a>Eliminare un server o un'istanza del servizio

>[!NOTE] 
> Per i passaggi di eliminazione, è richiesta la licenza Azure AD Premium.

In alcuni casi può essere utile fare in modo che un server non venga più monitorato. Ecco cosa si deve sapere per rimuovere un server dal servizio Azure AD Connect Health.

Quando si elimina un server, tenere presente quanto segue:

* Questa azione interrompe la raccolta di altri dati da tale server. Questo server viene rimosso dal servizio di monitoraggio. Dopo questa azione non sarà possibile visualizzare nuovi avvisi né dati di monitoraggio o di analisi di utilizzo per questo server.
* Questa azione non disinstalla o rimuove l'agente per l'integrità dal server. Se l'agente per l'integrità non è stato disinstallato prima di eseguire questo passaggio, potrebbero venire visualizzati errori correlati all'agente per l'integrità nel server.
* Questa azione non elimina i dati già raccolti da questo server. I dati vengono eliminati in conformità con i criteri di conservazione dei dati di Azure.
* Dopo avere eseguito questa azione, per avviare di nuovo il monitoraggio dello stesso server, disinstallare e reinstallare l'agente per l'integrità.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Eliminare un server dal servizio Azure AD Connect Health

>[!NOTE] 
> Per i passaggi di eliminazione, è richiesta la licenza Azure AD Premium.

Azure AD Connect Health per Active Directory Federation Services (AD FS) e Azure AD Connect (sincronizzazione):

1. Aprire il pannello **Server** dal pannello **Elenco server** selezionando il nome del server da rimuovere.
2. Nella barra delle azioni del pannello **Server** fare clic su **Elimina**.
![Schermata di eliminazione del server di Azure AD Connect Health](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Confermare il nome del server digitandolo nella casella di conferma.
4. Fare clic su **Elimina**.

Azure AD Connect Health per Azure Active Directory Domain Services:

1. Aprire il dashboard **Controller di dominio**.
2. Selezionare il controller di dominio da rimuovere.
3. Nella barra delle azioni fare clic su **Elimina elemento selezionato**.
4. Confermare l'azione per eliminare il server.
5. Fare clic su **Elimina**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Eliminare un'istanza del servizio dal servizio Azure AD Connect Health
In alcuni casi può essere utile rimuovere un'istanza del servizio. Ecco cosa si deve sapere per rimuovere un'istanza del servizio dal servizio Azure AD Connect Health.

Quando si elimina un'istanza del servizio, tenere presente quanto segue:

* Questa azione rimuove l'istanza corrente del servizio dal servizio di monitoraggio.
* Questa azione non disinstalla o rimuove l'agente per l'integrità da alcun server monitorato come parte di questa istanza del servizio. Se l'agente per l'integrità non è stato disinstallato prima di eseguire questo passaggio, potrebbero venire visualizzati errori correlati all'agente per l'integrità nei server.
* Tutti i dati di questa istanza del servizio verranno eliminati secondo i criteri di conservazione dati di Azure.
* Dopo avere eseguito questa azione, per avviare il monitoraggio del servizio, disinstallare e reinstallare gli agenti per l'integrità in tutti i server. Dopo avere eseguito questa azione, per avviare di nuovo il monitoraggio dello stesso server, disinstallare, reinstallare e registrare l'agente per l'integrità in quel server.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Per eliminare un'istanza del servizio dal servizio Azure AD Connect Health
1. Aprire il pannello **Servizio** dal pannello **Elenco servizi** selezionando l'identificatore del servizio (nome farm) che si desidera rimuovere. 
2. Nella barra delle azioni del pannello **Servizio** fare clic su **Elimina**. 
![Schermata di eliminazione del servizio di Azure AD Connect Health](./media/how-to-connect-health-operations/DeleteServer.png)
3. Confermare il nome del servizio digitandolo nella casella di conferma (ad esempio: sts.contoso.com).
4. Fare clic su **Elimina**.
   <br><br>

[//]: # (Inizio della sezione Controllo degli accessi in base al ruolo)
## <a name="manage-access-with-role-based-access-control"></a>Gestire l'accesso con il controllo degli accessi in base al ruolo
Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/role-assignments-portal.md) per Azure ad Connect Health fornisce accesso a utenti e gruppi diversi dagli amministratori globali. Questa funzionalità assegna ruoli agli utenti e ai gruppi previsti e fornisce un meccanismo per limitare gli amministratori globali all'interno di una directory.

### <a name="roles"></a>Ruoli
Azure AD Connect Health supporta i ruoli predefiniti seguenti:

| Ruolo | Autorizzazioni |
| --- | --- |
| Proprietario |I proprietari possono *gestire l'accesso* (ad esempio assegnare ruoli a un utente o gruppo), *visualizzare tutte le informazioni* (ad esempio visualizzare gli avvisi) dal portale e *modificare le impostazioni* (ad esempio le notifiche di posta elettronica) all'interno di Azure AD Connect Health. <br>Per impostazione predefinita, gli amministratori globali di Azure AD vengono assegnati a questo ruolo e questa assegnazione non è modificabile. |
| Collaboratore |I collaboratori possono *visualizzare tutte le informazioni* (ad esempio visualizzare gli avvisi) dal portale e *modificare le impostazioni* (ad esempio le notifiche di posta elettronica) all'interno di Azure AD Connect Health. |
| Lettore |I lettori possono *visualizzare tutte le informazioni* (ad esempio visualizzare gli avvisi) dal portale all'interno di Azure AD Connect Health. |

Tutti gli altri ruoli, ad esempio gli amministratori dell'accesso utente o gli utenti DevTest Labs, anche se disponibili nell'esperienza del portale, non influiscono sull'accesso all'interno di Azure AD Connect Health.

### <a name="access-scope"></a>Ambito di accesso
Azure AD Connect Health supporta la gestione dell'accesso a due livelli:

* **Tutte le istanze del servizio**: è il percorso consigliato nella maggior parte dei casi. Controlla l'accesso per tutte le istanze del servizio (ad esempio, una farm AD FS) in tutti i tipi di ruolo che vengono monitorati da Azure AD Connect Health.
* **Istanza del servizio**: in alcuni casi potrebbe essere necessario separare l'accesso in base ai tipi di ruolo o in base a un'istanza del servizio. In questo caso, è possibile gestire l'accesso a livello di istanza del servizio.  

L'autorizzazione viene concessa se un utente finale ha accesso a livello di directory o a livello di istanza del servizio.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Consentire l'accesso a utenti o gruppi ad Azure AD Connect Health
I passaggi che seguono spiegano come eseguire questa operazione.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Passaggio 1: Selezionare l'ambito di accesso appropriato
Per consentire a un utente l'accesso a livello di *tutte le istanze del servizio* dall'interno di Azure AD Connect Health, aprire il pannello principale in Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Passaggio 2: Aggiungere utenti e gruppi e assegnare ruoli
1. Nella sezione **Configura** fare clic su **Utenti**.<br>
   ![Schermata della barra laterale delle risorse di Azure AD Connect Health](./media/how-to-connect-health-operations/startRBAC.png)
2. Selezionare **Aggiungi**.
3. Nel riquadro **selezionare un ruolo** selezionare un ruolo, ad esempio **Proprietario**.<br>
   ![Schermata della finestra Utenti del Controllo degli accessi in base al ruolo di Azure AD Connect Health](./media/how-to-connect-health-operations/RBAC_add.png)
4. Digitare il nome o l'identificatore dell'utente o del gruppo. È possibile selezionare uno o più utenti o gruppi contemporaneamente. Fare clic su **Seleziona**.
   ![Schermata della finestra Utenti del Controllo degli accessi in base al ruolo di Azure AD Connect Health](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Selezionare **OK**.<br>
6. Dopo avere completato l'assegnazione dei ruoli, gli utenti e i gruppi vengono visualizzati nell'elenco.<br>
   ![Schermata della finestra Utenti del Controllo degli accessi in base al ruolo di Azure AD Connect Health con nuovi utenti evidenziati](./media/how-to-connect-health-operations/RBAC_user_list.png)

Ora gli utenti e i gruppi elencati hanno accesso, in base ai relativi ruoli assegnati.

> [!NOTE]
> * Gli amministratori globali hanno sempre accesso completo a tutte le operazioni, ma gli account Global Administrator non saranno presenti nell'elenco precedente.
> * La funzionalità Invita utenti non è supportata in Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Passaggio 3: Condividere il percorso del pannello con utenti o gruppi
1. Dopo che le autorizzazioni sono state assegnate, un utente può accedere ad Azure AD Connect Health da [qui](https://aka.ms/aadconnecthealth).
2. Nel pannello l'utente può aggiungere il pannello o parti di esso al dashboard. Fare semplicemente clic sull'icona **Aggiungi a dashboard**.<br>
   ![Schermata del pannello di aggiunta del Controllo degli accessi in base al ruolo di Azure AD Connect Health con l'icona di aggiunta evidenziata](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Un utente con il ruolo di lettore non può recuperare l'estensione Azure AD Connect Health da Azure Marketplace. L'utente non può eseguire l'operazione di creazione necessaria a tale compito. Potrà comunque ancora accedere al pannello tramite il collegamento sopra riportato. Per gli utilizzi successivi, l'utente può aggiungere il pannello al dashboard.
>
>

### <a name="remove-users-or-groups"></a>Rimuovere utenti o gruppi
È possibile rimuovere un utente o un gruppo aggiunto al Controllo degli accessi in base al ruolo di Azure AD Connect Health. Fare semplicemente clic con il pulsante destro del mouse sull'utente o sul gruppo e selezionare **Rimuovi**.<br>
![Schermata della finestra Utenti del Controllo degli accessi in base al ruolo di Azure AD Connect Health con Rimuovi evidenziato](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Fine della sezione Controllo degli accessi in base al ruolo)

## <a name="next-steps"></a>Passaggi successivi
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Installazione agente Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Uso di Azure AD Connect Health con AD FS](how-to-connect-health-adfs.md)
* [Uso di Azure AD Connect Health per la sincronizzazione](how-to-connect-health-sync.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](how-to-connect-health-adds.md)
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
* [Cronologia delle versioni di Azure AD Connect Health](reference-connect-health-version-history.md)
