---
title: 'Esercitazione: Creare un progetto pilota di provisioning cloud di Azure AD Connect per una foresta di AD sincronizzata esistente'
description: Informazioni su come creare un progetto pilota di provisioning cloud per una foresta di Active Directory di test già sincronizzata tramite il servizio di sincronizzazione di Azure Active Directory (Azure AD) Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e771a988faca98d009b97b1e705ddac7110a255f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91266497"
---
# <a name="pilot-cloud-provisioning-for-an-existing-synced-ad-forest"></a>Creare un progetto pilota per il provisioning cloud di una foresta di AD sincronizzata esistente 

Questa esercitazione illustra come creare un progetto pilota di provisioning cloud per una foresta di Active Directory di test già sincronizzata tramite il servizio di sincronizzazione di Azure Active Directory (Azure AD) Connect.

![Creazione](media/tutorial-migrate-aadc-aadccp/diagram.png)

## <a name="considerations"></a>Considerazioni
Prima di provare questa esercitazione, considerare quanto segue:
1. Assicurarsi di avere familiarità con le nozioni di base del provisioning cloud. 
2. Verificare che sia in esecuzione la versione 1.4.32.0 o successiva del servizio di sincronizzazione di Azure AD Connect e di aver configurato le regole di sincronizzazione come documentato. Quando si crea il progetto pilota, si rimuoverà un'unità organizzativa o un gruppo di test dall'ambito di sincronizzazione di Azure AD Connect. Gli oggetti spostati dall'ambito verranno eliminati da Azure AD. Nel caso degli oggetti utente, l'eliminazione da Azure AD sarà temporanea e sarà possibile ripristinarli. Nel caso degli oggetti gruppo, l'eliminazione da Azure AD sarà permanente e non sarà possibile ripristinarli. Nel servizio di sincronizzazione di Azure AD Connect è stato introdotto un tipo di collegamento che impedisce l'eliminazione nel caso di scenari pilota. 
3. Verificare che gli oggetti nell'ambito del progetto pilota abbiano il valore ms-ds-consistencyGUID popolato, in modo che corrispondano a livello hardware con il provisioning cloud. 

   > [!NOTE]
   > Il servizio di sincronizzazione di Azure AD Connect non popola *ms-ds-consistencyGUID* per impostazione predefinita.

4. Si tratta di uno scenario avanzato. Assicurarsi di seguire esattamente i passaggi descritti in questa esercitazione.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione sono necessari i requisiti seguenti
- Un ambiente di test con il servizio di sincronizzazione di Azure AD Connect versione 1.4.32.0 o successiva
- Un'unità organizzativa o un gruppo incluso nell'ambito di sincronizzazione e che è possibile usare nel progetto pilota. È consigliabile iniziare con un piccolo set di oggetti.
- Un server che esegue Windows Server 2012 R2 o versione successiva che ospiterà l'agente di provisioning.  Non può essere lo stesso server di Azure AD Connect.
- L'ancoraggio di origine per il servizio di sincronizzazione di Azure AD Connect deve essere *objectGuid* o *ms-ds-consistencyGUID*

## <a name="update-azure-ad-connect"></a>Aggiornare Azure AD Connect

Come minimo, è necessario avere [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.4.32.0. Per aggiornare il servizio di sincronizzazione di Azure AD Connect, completare i passaggi elencati in [Azure AD Connect: Eseguire l'aggiornamento alla versione più recente](../hybrid/how-to-upgrade-previous-version.md).  

## <a name="stop-the-scheduler"></a>Arrestare l'utilità di pianificazione
Il servizio di sincronizzazione di Azure AD Connect sincronizza le modifiche rilevate nella directory locale usando un'utilità di pianificazione. Per modificare e aggiungere regole personalizzate, è consigliabile disabilitare l'utilità di pianificazione in modo che nel frattempo non vengano eseguite sincronizzazioni.  Eseguire la procedura descritta di seguito:

1.  Nel server che esegue il servizio di sincronizzazione di Azure AD Connect aprire PowerShell con privilegi di amministratore.
2.  Eseguire `Stop-ADSyncSyncCycle`.  Premere INVIO.
3.  Eseguire `Set-ADSyncScheduler -SyncCycleEnabled $false`.

>[!NOTE] 
>Se si esegue l'utilità di pianificazione personalizzata per il servizio di sincronizzazione di Azure AD Connect, disabilitarla. 

## <a name="create-custom-user-inbound-rule"></a>Creare una regola utente in ingresso personalizzata

 1. Avviare l'editor di sincronizzazione dal menu dell'applicazione sul desktop, come illustrato di seguito:</br>
 ![Menu dell'editor delle regole di sincronizzazione](media/how-to-cloud-custom-user-rule/user8.png)</br>
 
 2. Selezionare **In ingresso** nell'elenco a discesa Direzione e fare clic su **Aggiungi nuova regola**.
 ![Screenshot che mostra la finestra per la visualizzazione e la gestione delle regole di sincronizzazione, con l'opzione "In ingresso" e il pulsante per l'aggiunta di una nuova regola selezionati.](media/how-to-cloud-custom-user-rule/user1.png)</br>
 
 3. Nella pagina **Descrizione** immettere quanto segue e fare clic su **Avanti**:

    **Nome:** assegnare un nome significativo alla regola<br>
    **Descrizione:** aggiungere una descrizione significativa<br>
    **Connected System** (Sistema connesso): scegliere il connettore AD per cui si sta scrivendo la regola di sincronizzazione personalizzata<br>
    **Connected System Object Type** (Tipo di oggetto sistema connesso): Utente<br>
    **Metaverse Object Type** (Tipo di oggetto metaverse): Persona<br>
    **Link Type** (Tipo di collegamento): Join<br>
    **Precedence** (Precedenza): specificare un valore univoco nel sistema<br>
    **Tag**: lasciare vuoto questo campo<br>
    ![Screenshot che mostra la pagina relativa alla descrizione nella finestra per la creazione di una regola di sincronizzazione in ingresso, con i valori specificati.](media/how-to-cloud-custom-user-rule/user2.png)</br>
 
 4. Nella pagina **Scoping filter** (Filtro di ambito) immettere l'unità organizzativa o il gruppo di sicurezza in base a cui creare il progetto pilota.  Per filtrare in base a unità organizzativa, aggiungere la parte OU del nome distinto. Questa regola verrà applicata a tutti gli utenti inclusi in tale unità organizzativa.  Quindi, se il nome distinto termina con "OU=CPUsers,DC=contoso,DC=com, aggiungere questo filtro.  Quindi fare clic su **Next**. 

    |Regola|Attributo|Operatore|valore|
    |-----|----|----|-----|
    |Scoping OU|DN|ENDSWITH|Nome distinto dell'unità organizzativa.|
    |Scoping group||ISMEMBEROF|Nome distinto del gruppo di sicurezza.|

    ![Screenshot che mostra la pagina relativa al filtro dell'ambito nella finestra per la creazione di una regola di sincronizzazione in ingresso, con un valore di filtro dell'ambito specificato.](media/how-to-cloud-custom-user-rule/user3.png)</br>
 
 5. Nella pagina **Join Rules** (Regole di unione) fare clic su **Avanti**.
 6. Nella pagina **Transformations** (Trasformazioni) aggiungere una trasformazione costante: impostare su True l'attributo cloudNoFlow. Scegliere **Aggiungi**.
 ![Screenshot che mostra la pagina relativa alle trasformazioni nella finestra per la creazione di una regola di sincronizzazione in ingresso, con un flusso di trasformazione costante aggiunto.](media/how-to-cloud-custom-user-rule/user4.png)</br>

È necessario seguire la stessa procedura per tutti i tipi di oggetto (utente, gruppo e contatto). Ripetere i passaggi per ogni connettore AD e per ogni foresta AD. 

## <a name="create-custom-user-outbound-rule"></a>Creare una regola utente in uscita personalizzata

 1. Selezionare **In uscita** nell'elenco a discesa Direzione e fare clic su **Aggiungi regola**.
 ![Screenshot che mostra la direzione "In uscita" selezionata e il pulsante per l'aggiunta di una nuova regola evidenziato.](media/how-to-cloud-custom-user-rule/user5.png)</br>
 
 2. Nella pagina **Descrizione** immettere quanto segue e fare clic su **Avanti**:

    **Nome:** assegnare un nome significativo alla regola<br>
    **Descrizione:** aggiungere una descrizione significativa<br>
    **Connected System** (Sistema connesso): scegliere il connettore di Azure AD per cui si sta scrivendo la regola di sincronizzazione personalizzata<br>
    **Connected System Object Type** (Tipo di oggetto sistema connesso): Utente<br>
    **Metaverse Object Type** (Tipo di oggetto metaverse): Persona<br>
    **Link Type** (Tipo di collegamento): JoinNoFlow<br>
    **Precedence** (Precedenza): specificare un valore univoco nel sistema<br>
    **Tag**: lasciare vuoto questo campo<br>
    
    ![Screenshot che mostra la pagina relativa alla descrizione con le proprietà specificate.](media/how-to-cloud-custom-user-rule/user6.png)</br>
 
 3. Nella pagina **Scoping filter** (Filtro di ambito) scegliere **cloudNoFlow** uguale **True**. Quindi fare clic su **Next**.
 ![Regola personalizzata](media/how-to-cloud-custom-user-rule/user7.png)</br>
 
 4. Nella pagina **Join Rules** (Regole di unione) fare clic su **Avanti**.
 5. Nella pagina **Transformations** (Trasformazioni) fare clic su **Add** (Aggiungi).

È necessario seguire la stessa procedura per tutti i tipi di oggetto (utente, gruppo e contatto).

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installare l'agente di provisioning cloud di Azure AD Connect
1. Accedere al server che si userà con le autorizzazioni di amministratore dell'organizzazione.  Se si usa l'esercitazione [Ambiente di AD e Azure di base](tutorial-basic-ad-azure.md), sarà CP1.
2. Scaricare l'agente di provisioning cloud di Azure AD Connect usando la procedura descritta [qui](how-to-install.md#install-the-agent).
3. Eseguire il provisioning cloud di Azure AD Connect (AADConnectProvisioningAgent.Installer)
3. Nella schermata iniziale **accettare** le condizioni di licenza e fare clic su **Installa**.</br>
![Screenshot che mostra la schermata iniziale dell'agente di provisioning di Microsoft Azure AD Connect.](media/how-to-install/install1.png)</br>

4. Al termine dell'operazione verrà avviata la configurazione guidata.  Accedere con l'account amministratore globale di Azure AD.
5. Nella schermata **Connect Active Directory** (Connetti Active Directory) fare clic su **Aggiungi directory** quindi accedere con l'account amministratore di Active Directory.  Questa operazione aggiungerà la directory locale.  Fare clic su **Avanti**.</br>
![Screenshot che mostra la schermata per la connessione di Active Directory con un valore di directory specificato.](media/how-to-install/install3.png)</br>

6. Nella schermata **Configurazione completata** fare clic su **Conferma**.  Questa operazione registrerà e riavvierà l'agente.</br>
![Screenshot che mostra la schermata di completamento della configurazione con il pulsante per la conferma selezionato.](media/how-to-install/install4.png)</br>

7. Al termine dell'operazione verrà visualizzato l'avviso indicante che **la verifica è stata completata correttamente**.  È possibile fare clic su **Esci**.</br>
![Schermata iniziale](media/how-to-install/install5.png)</br>
8. Se è ancora visualizzata la schermata iniziale, fare clic su **Chiudi**.

## <a name="verify-agent-installation"></a>Verificare l'installazione dell'agente
La verifica dell'agente si esegue nel portale di Azure e nel server locale che esegue l'agente.

### <a name="azure-portal-agent-verification"></a>Verifica dell'agente nel portale di Azure
Per verificare se l'agente viene visto da Azure, seguire questa procedura:

1. Accedere al portale di Azure.
2. A sinistra selezionare **Azure Active Directory**, fare clic su **Azure AD Connect** e al centro selezionare **Gestione del provisioning (anteprima)** .</br>
![Azure portal](media/how-to-install/install6.png)</br>

3.  Nella schermata **Provisioning di Azure AD (anteprima)** fare clic su **Verifica tutti gli agenti**.
![Provisioning di Azure AD](media/how-to-install/install7.png)</br>
 
4. Nella **schermata degli agenti di provisioning locali** verranno visualizzati gli agenti installati.  Verificare che l'agente in questione sia presente e contrassegnato come **Disabilitato**.  L'agente è disabilitato per impostazione predefinita ![Agenti di provisioning](media/how-to-install/verify1.png)</br>

### <a name="on-the-local-server"></a>Nel server locale
Per verificare se l'agente è in esecuzione, seguire questa procedura:

1.  Accedere al server con un account amministratore
2.  Aprire **Servizi** passando all'opzione oppure tramite Start/Esegui/Services.msc.
3.  In **Servizi** assicurarsi che siano presenti i servizi **Microsoft Azure AD Connect Agent Updater** e **Microsoft Azure AD Connect Provisioning Agent** e che il relativo stato sia **In esecuzione**.
![Services](media/how-to-troubleshoot/troubleshoot1.png)

## <a name="configure-azure-ad-connect-cloud-provisioning"></a>Configurare il provisioning cloud di Azure AD Connect
Per configurare il provisioning, seguire questa procedura:

 1. Accedere al portale di Azure AD.
 2. Fare clic su **Azure Active Directory**
 3. Fare clic su **Azure AD Connect**
 4. Selezionare **Gestione del provisioning (anteprima)** 
 ![Screenshot che mostra il collegamento "Gestione del provisioning (anteprima)".](media/how-to-configure/manage1.png)</br>
 5.  Fare clic su **Nuova configurazione**
 ![Screenshot della schermata Provisioning di Azure AD (anteprima) con il collegamento "Nuova configurazione" evidenziato.](media/tutorial-single-forest/configure1.png)</br>
 6.  Nella schermata di configurazione, immettere un valore per **Messaggio di posta elettronica di notifica**, spostare il selettore su **Abilita** e fare clic su **Salva**.
 ![Screenshot della schermata di configurazione con il messaggio di posta elettronica di notifica compilato e l'opzione Abilita selezionata.](media/tutorial-single-forest/configure2.png)</br>
 7. In **Configura** selezionare **Tutti gli utenti** per cambiare l'ambito della regola di configurazione.
 ![Screenshot della schermata di configurazione con l'opzione "Tutti gli utenti" evidenziata accanto a "Definisci l'ambito per gli utenti".](media/how-to-configure/scope2.png)</br>
 8. A destra cambiare l'ambito per includere la specifica unità organizzativa creata, "OU=CPUsers,DC=contoso,DC=com".
 ![Screenshot della schermata Definisci l'ambito per gli utenti in cui è evidenziato l'ambito modificato e impostato sull'unità organizzativa creata.](media/tutorial-existing-forest/scope2.png)</br>
 9.  Fare clic su **Fine** e su **Salva**.
 10. L'ambito dovrebbe ora essere impostato su una unità organizzativa. 
 ![Screenshot della schermata di configurazione con l'opzione "1 unità organizzativa" evidenziata accanto a "Definisci l'ambito per gli utenti".](media/tutorial-existing-forest/scope3.png)</br>
 

## <a name="verify-users-are-provisioned-by-cloud-provisioning"></a>Verificare che il provisioning cloud venga applicato agli utenti
A questo punto verificare che gli utenti presenti nella directory locale siano stati sincronizzati e che ora siano inclusi nel tenant di Azure AD.  Per essere completata, questa operazione può richiedere alcune ore.  Per verificare se il provisioning cloud è stato applicato agli utenti, seguire questa procedura:

1. Selezionare il [portale di Azure](https://portal.azure.com) e accedere con un account che dispone di una sottoscrizione di Azure.
2. A sinistra selezionare **Azure Active Directory**
3. Fare clic su **Azure AD Connect**
4. Fare clic su **Gestione del provisioning (anteprima)**
5. Fare clic sul pulsante **Log**
6. Cercare un nome utente per verificare che sia stato incluso nel provisioning cloud

È inoltre possibile verificare che l'utente e il gruppo esistano in Azure AD.

## <a name="start-the-scheduler"></a>Avviare l'utilità di pianificazione
Il servizio di sincronizzazione di Azure AD Connect sincronizza le modifiche rilevate nella directory locale usando un'utilità di pianificazione. Ora che sono state modificate le regole, è possibile riavviare l'utilità di pianificazione.  Eseguire la procedura descritta di seguito:

1.  Nel server che esegue il servizio di sincronizzazione di Azure AD Connect aprire PowerShell con privilegi di amministratore
2.  Eseguire `Set-ADSyncScheduler -SyncCycleEnabled $true`.
3.  Eseguire `Start-ADSyncSyncCycle`.  Premere INVIO.  

>[!NOTE] 
>Se si esegue l'utilità di pianificazione personalizzata per il servizio di sincronizzazione di Azure AD Connect, abilitarla. 

Una volta abilitata l'utilità di pianificazione, Azure AD Connect arresterà l'esportazione di eventuali modifiche negli oggetti con `cloudNoFlow=true` nel metaverse, a meno che non vi siano attributi di riferimento (ad esempio manager) in corso di aggiornamento. Nel caso in cui sia presente un aggiornamento dell'attributo di riferimento per l'oggetto, Azure AD Connect ignorerà il segnale di `cloudNoFlow` ed esporterà tutti gli aggiornamenti nell'oggetto.

## <a name="something-went-wrong"></a>Si è verificato un errore
Nel caso il progetto pilota non funzioni come previsto, è possibile tornare nella configurazione del servizio di sincronizzazione di Azure AD Connect seguendo questa procedura:
1.  Disabilitare la configurazione del provisioning nel portale di Azure. 
2.  Disabilitare tutte le regole di sincronizzazione personalizzate create per il provisioning cloud usando l'editor delle regole di sincronizzazione. La disabilitazione genera una sincronizzazione completa in tutti i connettori.

## <a name="configure-azure-ad-connect-sync-to-exclude-the-pilot-ou"></a>Configurare il servizio di sincronizzazione di Azure AD Connect per escludere l'unità organizzativa pilota
Dopo aver verificato che gli utenti dell'unità organizzativa pilota vengono gestiti correttamente dal provisioning cloud, è possibile riconfigurare Azure AD Connect per escludere l'unità organizzativa pilota creata in precedenza.  L'agente di provisioning cloud gestirà la sincronizzazione di questi utenti in futuro.  Usare la procedura seguente per definire l'ambito di Azure AD Connect.

 1. Nel server che esegue Azure AD Connect fare doppio clic sull'icona di Azure AD Connect.
 2. Fare clic su **Configura**.
 3. Selezionare **Personalizzazione delle opzioni di sincronizzazione** e quindi fare clic su Avanti.
 4. Accedere a Azure AD e fare clic su **Avanti**.
 5. Nella schermata **Connessione delle directory** fare clic su **Avanti**.
 6. Nella schermata **Filtro di domini e unità organizzative** selezionare **Sincronizza le unità organizzative e i domini selezionati**.
 7. Espandere il dominio e **deselezionare** l'unità organizzativa **CPUsers**.  Fare clic su **Avanti**.
![ambito](media/tutorial-existing-forest/scope1.png)</br>
 9. Nella schermata **Funzionalità facoltative** fare clic su **Avanti**.
 10. Nella schermata **Ready to configure** (Pronto per la configurazione) fare clic su **Configure** (Configura).
 11. Al termine, fare clic su **Esci**. 

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)

