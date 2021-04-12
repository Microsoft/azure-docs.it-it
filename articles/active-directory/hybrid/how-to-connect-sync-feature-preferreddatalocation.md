---
title: 'Azure AD Connect: configurare il percorso dati preferito per le risorse Microsoft 365'
description: Viene descritto come posizionare le risorse utente Microsoft 365 vicino all'utente con Azure Active Directory Connect sincronizzazione.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/11/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c456e7788280b7ca5328342e1cd848ba3a583a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95972760"
---
# <a name="azure-active-directory-connect-sync-configure-preferred-data-location-for-microsoft-365-resources"></a>Sincronizzazione Azure Active Directory Connect: configurare il percorso dati preferito per le risorse Microsoft 365
Lo scopo di questo argomento è illustrare come configurare l'attributo per la posizione dei dati preferita in Azure Active Directory (Azure AD) Connect Sync. Quando un utente usa funzionalità multigeo in Microsoft 365, questo attributo viene usato per definire la posizione geografica dei dati Microsoft 365 dell'utente. I termini *area* e *area geografica* vengono usati in modo intercambiabile.

## <a name="enable-synchronization-of-preferred-data-location"></a>Abilitare la sincronizzazione del percorso dati preferito
Per impostazione predefinita, le risorse Microsoft 365 per gli utenti si trovano nella stessa area geografica del tenant di Azure AD. Ad esempio, se il tenant si trova in America del Nord, anche le cassette postali di Exchange degli utenti si trovano in America del Nord. In un'organizzazione multinazionale questo aspetto può creare problemi.

Impostando l'attributo **preferredDataLocation** è possibile definire l'area geografica dell'utente. È possibile avere le risorse di Microsoft 365 dell'utente, ad esempio la cassetta postale e OneDrive, nella stessa area geografica dell'utente e avere ancora un tenant per l'intera organizzazione.

> [!IMPORTANT]
> La funzionalità multigeo è attualmente disponibile per i clienti con un Contratto Enterprise attivo e un minimo di 250 sottoscrizioni di servizi Microsoft 365. Per informazioni dettagliate, contattare il rappresentante Microsoft.
>
>

Un elenco di tutti i GEOS per Microsoft 365 è disponibile in [dove si trovano i dati?](/microsoft-365/enterprise/o365-data-locations).

GEOS in Microsoft 365 disponibile per la funzionalità multigeo:

| Area geografica | Valore preferredDataLocation |
| --- | --- |
| Asia Pacifico | APC |
| Australia | AUS |
| Canada | CAN |
| Unione Europa | EUR |
| Francia | FRA |
| India | IND |
| Giappone | JPN |
| Corea del Sud | KOR |
| Sudafrica | ZAF |
| Svizzera | CHE |
| Emirati Arabi Uniti | ARE |
| Regno Unito | GBR |
| Stati Uniti | NAM |

* Se un'area geografica non è elencata nella tabella, ad esempio l'America del Sud, significa che non è possibile usarla per Multi-Geo Capabilities.

* Non tutti i carichi di lavoro di Microsoft 365 supportano l'impostazione dell'area geografica di un utente.

### <a name="azure-ad-connect-support-for-synchronization"></a>Supporto di Azure AD Connect per la sincronizzazione

Azure AD Connect supporta la sincronizzazione dell'attributo **preferredDataLocation** per gli oggetti **Utente** nella versione 1.1.524.0 e successive. In particolare:

* Lo schema del tipo di oggetto **Utente** in Azure AD Connector è stato esteso per poter includere l'attributo **preferredDataLocation**. L'attributo è di tipo stringa a valore singolo.
* Lo schema del tipo di oggetto **Persona** nel metaverse è stato esteso per poter includere l'attributo **preferredDataLocation**. L'attributo è di tipo stringa a valore singolo.

Per impostazione predefinita, **preferredDataLocation** non è abilitato per la sincronizzazione. Questa funzionalità è destinata alle organizzazioni di grandi dimensioni. Lo schema Active Directory in Windows Server 2019 dispone di un attributo **msDS-preferredDataLocation** da usare a questo scopo. Se lo schema di Active Directory non è stato aggiornato e non è possibile eseguire questa operazione, è necessario identificare un attributo per contenere l'area geografica Microsoft 365 per gli utenti. Si tratta di un attributo diverso per ogni organizzazione.

> [!IMPORTANT]
> Azure AD consente la configurazione diretta dell'attributo **preferredDataLocation** sugli **oggetti Utente cloud** con Azure AD PowerShell. Per configurare questo attributo sugli **oggetti Utente sincronizzati**, è necessario usare Azure AD Connect.

Prima di abilitare la sincronizzazione:

* Se lo schema di Active Directory non è stato aggiornato a 2019, decidere quale attributo Active Directory locale usare come attributo di origine. Deve essere di tipo **stringa a valore singolo**.
* Se in precedenza è stato configurato l'attributo **preferredDataLocation** sugli **oggetti Utente sincronizzati** esistenti in Azure AD con Azure AD PowerShell, è necessario eseguire il backporting dei valori dell'attributo per gli oggetti **Utente** corrispondenti in Active Directory locale.

    > [!IMPORTANT]
    > Se non si esegue il backporting di questi valori, Azure AD Connect rimuove i valori dell'attributo esistente in Azure AD quando è abilitata la sincronizzazione per l'attributo **preferredDataLocation**.

* Configurare ora l'attributo di origine in almeno due oggetti Utente di Active Directory locale. Lo si potrà usare più avanti per la verifica.

Le sezioni seguenti illustrano la procedura per abilitare la sincronizzazione dell'attributo **preferredDataLocation**.

> [!NOTE]
> I passaggi sono descritti nel contesto di una distribuzione di Azure AD con topologia a foresta singola e senza regole di sincronizzazione personalizzate. Se sono stati configurati una topologia a più foreste e regole di sincronizzazione personalizzate o si dispone di un server di gestione temporanea, modificare i passaggi di conseguenza.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>Passaggio 1: Disabilitare l'utilità di pianificazione della sincronizzazione e verificare che non ci sia alcuna sincronizzazione in corso
Per evitare l'esportazione di modifiche accidentali in Azure AD, verificare che non venga eseguita alcuna sincronizzazione durante l'aggiornamento delle regole di sincronizzazione. Per disabilitare l'utilità di pianificazione della sincronizzazione predefinita:

1. Avviare una sessione di PowerShell nel server di Azure AD Connect.
2. Disabilitare la sincronizzazione pianificata eseguendo questo cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. Avviare **Synchronization Service Manager** passando a **START** > **Synchronization Service** (Servizio di sincronizzazione).
4. Selezionare scheda **Operazioni** e verificare che per nessuna operazione lo stato sia *In corso*.

![Screenshot di Synchronization Service Manager](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-refresh-the-schema-for-active-directory"></a>Passaggio 2: aggiornare lo schema per Active Directory
Se lo schema di Active Directory è stato aggiornato a 2019 e Connect è stato installato prima dell'estensione dello schema, la cache dello schema Connect non dispone dello schema aggiornato. Quindi, è necessario aggiornare lo schema dalla procedura guidata affinché venga visualizzato nell'interfaccia utente.

1. Avviare la procedura guidata Azure AD Connect dal desktop.
2. Selezionare l'opzione **Aggiorna schema directory** e fare clic su **Avanti**.
3. Immettere le credenziali Azure AD e fare clic su **Avanti**.
4. Nella pagina **Aggiorna schema directory** assicurarsi che tutte le foreste siano selezionate e fare clic su **Avanti**.
5. Al termine, chiudere la procedura guidata.

![Screenshot dello schema di aggiornamento della directory nella procedura guidata Connetti](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-refreshschema.png)

## <a name="step-3-add-the-source-attribute-to-the-on-premises-active-directory-connector-schema"></a>Passaggio 3: aggiungere l'attributo di origine allo schema di Active Directory Connector locale
**Questo passaggio è necessario solo se si esegue Connect versione 1.3.21 o precedente. Se si è in 1.4.18 o versione successiva, andare al passaggio 5.**  
Non tutti gli attributi di Azure AD vengono importati nello spazio connettore Active Directory locale. Se si è scelto di usare un attributo non sincronizzato per impostazione predefinita, è necessario importarlo. Per aggiungere l'attributo di origine all'elenco degli attributi importati:

1. Selezionare la scheda **Connettori** in Synchronization Service Manager.
2. Fare clic con il pulsante destro del mouse sull'istanza di Active Directory Connector locale e scegliere **Proprietà**.
3. Nella finestra di dialogo popup passare alla scheda **Seleziona attributi**.
4. Verificare che l'attributo di origine che si è scelto di usare sia selezionato nell'elenco degli attributi. Se l'attributo non viene visualizzato, selezionare la casella di controllo **Mostra tutto**.
5. Per salvare, selezionare **OK**.

![Screenshot che mostra la finestra di dialogo Synchronization Service Manager e proprietà con l'elenco "attributi" evidenziato.](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-4-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>Passaggio 4: aggiungere **preferredDataLocation** allo schema del connettore Azure ad
**Questo passaggio è necessario solo se si esegue Connect versione 1.3.21 o precedente. Se si è in 1.4.18 o versione successiva, andare al passaggio 5.**  
Per impostazione predefinita, l'attributo **preferredDataLocation** non viene importato nello spazio connettore Azure ad. Per aggiungerlo all'elenco di attributi importati:

1. Selezionare la scheda **Connettori** in Synchronization Service Manager.
2. Fare clic con il pulsante destro del mouse sul connettore Azure AD e selezionare **Proprietà**.
3. Nella finestra di dialogo popup passare alla scheda **Seleziona attributi**.
4. Selezionare l'attributo **preferredDataLocation** nell'elenco.
5. Per salvare, selezionare **OK**.

![Screenshot di Synchronization Service Manager e della finestra di dialogo Proprietà](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-5-create-an-inbound-synchronization-rule"></a>Passaggio 5: creare una regola di sincronizzazione in ingresso
La regola di sincronizzazione in ingresso consente la trasmissione del valore dell'attributo dall'attributo di origine di Active Directory locale al metaverse.

1. Avviare **Synchronization Rules Editor** (Editor delle regole di sincronizzazione) passando a **START** > **Synchronization Rules Editor** (Editor delle regole di sincronizzazione).
2. Impostare il filtro di ricerca **Direzione** su **In arrivo**.
3. Per creare una nuova regola in entrata, fare clic sul pulsante **Aggiungi nuova regola**.
4. Nella scheda **Descrizione** inserire la configurazione seguente:

    | Attributo | Valore | Dettagli |
    | --- | --- | --- |
    | Nome | *Specificare un nome* | Ad esempio, "In entrata da AD - Utente PreferredDataLocation" |
    | Descrizione | *Fornire una descrizione personalizzata* |  |
    | Connected System | *Selezionare l'istanza di Active Directory Connector locale* |  |
    | Connected System Object Type | **Utente** |  |
    | Metaverse Object Type | **Person** |  |
    | Tipo di collegamento | **Join** |  |
    | Precedenza | *Scegliere un numero compreso tra 1 e 99* | I numeri compresi tra 1 e 99 sono riservati alle regole di sincronizzazione personalizzate. Non scegliere un valore usato da un'altra regola di sincronizzazione. |

5. Lasciare vuoto il **filtro di ambito** per includere tutti gli oggetti. Potrebbe essere necessario perfezionare il filtro di ambito in base alla distribuzione di Azure AD Connect.
6. Passare alla **scheda trasformazione** e implementare la regola di trasformazione seguente:

    | Tipo di flusso | Attributo di destinazione | Source (Sorgente) | Applicare una sola volta | Tipi di unione |
    | --- | --- | --- | --- | --- |
    |Connessione diretta | preferredDataLocation | Selezionare l'attributo di origine | Non selezionato | Aggiornamento |

7. Per creare la regola in entrata, selezionare **Aggiungi**.

![Screenshot della pagina di creazione regola di sincronizzazione in ingresso](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-6-create-an-outbound-synchronization-rule"></a>Passaggio 6: creare una regola di sincronizzazione in uscita
La regola di sincronizzazione in uscita consente il flusso del valore dell'attributo dal metaverse all'attributo **preferredDataLocation** in Azure ad:

1. Passare all' **Editor delle regole di sincronizzazione**.
2. Impostare il filtro di ricerca **Direzione** da **In uscita**.
3. Selezionare **Aggiungi nuova regola**.
4. Nella scheda **Descrizione** inserire la configurazione seguente:

    | Attributo | Valore | Dettagli |
    | ----- | ------ | --- |
    | Nome | *Specificare un nome* | Ad esempio, "In uscita verso AAD - Utente PreferredDataLocation" |
    | Descrizione | *Inserire una descrizione* ||
    | Connected System | *Selezionare il connettore di Azure AD* ||
    | Connected System Object Type | **Utente** ||
    | Metaverse Object Type | **Person** ||
    | Tipo di collegamento | **Join** ||
    | Precedenza | *Scegliere un numero compreso tra 1 e 99* | I numeri compresi tra 1 e 99 sono riservati alle regole di sincronizzazione personalizzate. Non scegliere un valore usato da un'altra regola di sincronizzazione. |

5. Passare alla scheda **filtro ambito** e aggiungere un singolo gruppo di filtri di ambito con due clausole:

    | Attributo | Operatore | valore |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | Utente |
    | cloudMastered | NOTEQUAL | True |

    L'ambito del filtro determina a quali oggetti di Azure AD viene applicata la regola di sincronizzazione in uscita. In questo esempio viene usato lo stesso filtro di ambito dalla regola di sincronizzazione "out to Azure AD – User Identity" OOB (out-of-box). Impedisce che la regola di sincronizzazione venga applicata agli oggetti **utente** non sincronizzati da un Active Directory locale. Potrebbe essere necessario perfezionare il filtro di ambito in base alla distribuzione di Azure AD Connect.

6. Passare alla scheda **trasformazione** e implementare la regola di trasformazione seguente:

    | Tipo di flusso | Attributo di destinazione | Source (Sorgente) | Applicare una sola volta | Tipi di unione |
    | --- | --- | --- | --- | --- |
    | Connessione diretta | preferredDataLocation | preferredDataLocation | Non selezionato | Aggiornamento |

7. Fare clic su **Aggiungi** per creare la regola in uscita.

![Screenshot della pagina di creazione regola di sincronizzazione in uscita](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-7-run-full-synchronization-cycle"></a>Passaggio 7: eseguire il ciclo di sincronizzazione completa
In generale, un ciclo di sincronizzazione completo è necessario. Il motivo è che sono stati aggiunti nuovi attributi per gli schemi di Active Directory e Azure AD Connector e sono state introdotte regole di sincronizzazione personalizzate. Verificare le modifiche prima di esportarle in Azure AD. È possibile usare la procedura seguente per controllare le modifiche, eseguendo al contempo manualmente i passaggi che compongono il ciclo di sincronizzazione completo.

1. Eseguire un'**importazione completa** nell'istanza di Active Directory Connector locale:

   1. Passare alla scheda **Operazioni** in Synchronization Service Manager.
   2. Fare clic con il pulsante destro del mouse sull'istanza di **Active Directory Connector locale** e scegliere **Esegui**.
   3. Nella finestra di dialogo selezionare **Importazione completa** e fare clic su **OK**.
   4. Attendere il completamento dell'operazione.

      > [!NOTE]
      > È possibile ignorare l'importazione completa in Active Directory Connector locale se l'attributo di origine è già incluso nell'elenco degli attributi importati. In altre parole, non è necessario apportare modifiche durante il passaggio 2 in precedenza in questo articolo.

2. Eseguire un'**importazione completa** in Azure AD Connector:

   1. Fare clic con il pulsante destro del mouse sul **connettore Azure ad** e scegliere **Esegui**.
   2. Nella finestra di dialogo selezionare **Importazione completa** e fare clic su **OK**.
   3. Attendere il completamento dell'operazione.

3. Verificare le modifiche alle regole di sincronizzazione in un oggetto **utente** esistente.

   L'attributo di origine di Active Directory locale e **preferredDataLocation** di Azure AD sono stati importati nel rispettivo spazio connettore. Prima di procedere con il passaggio di sincronizzazione completa, è consigliabile eseguire un'operazione di anteprima su un oggetto **Utente** esistente nello spazio Active Directory Connector locale. L'attributo di origine dell'oggetto selezionato deve essere popolato. Un'anteprima corretta che mostra **preferredDataLocation** popolato nel metaverse indica che le regole di sincronizzazione sono state configurate correttamente. Per informazioni sull'esecuzione dell'anteprima, vedere [Verificare la modifica](how-to-connect-sync-change-the-configuration.md#verify-the-change).

4. Eseguire una **sincronizzazione completa** nell'istanza di Active Directory Connector locale:

   1. Fare clic con il pulsante destro del mouse sull'istanza di **Active Directory Connector locale** e scegliere **Esegui**.
   2. Nella finestra di dialogo selezionare **Sincronizzazione completa** e fare clic su **OK**.
   3. Attendere il completamento dell'operazione.

5. Verificare le **esportazioni in sospeso** per Azure AD:

   1. Fare clic con il pulsante destro del mouse sul **connettore Azure ad** e scegliere **Cerca spazio connettore**.
   2. Nella finestra di dialogo **Search Connector Space** (Cerca spazio connettore:

        a. Impostare **Ambito** su **Pending Export** (Esportazione in sospeso).<br>
        b. Selezionare tutte e tre le caselle di controllo, tra cui **Aggiungi, Modifica ed Elimina**.<br>
        c. Selezionare **Ricerca** per ottenere l'elenco degli oggetti con le modifiche da esportare. Per esaminare le modifiche per un determinato oggetto, fare doppio clic sull'oggetto.<br>
        d. Verificare le modifiche previste.

6. Eseguire l' **esportazione** sul **connettore Azure ad**

   1. Fare clic con il pulsante destro del mouse sul **connettore Azure ad** e scegliere **Esegui**.
   2. Nella finestra di dialogo **Run Connector** (Esegui connettore) selezionare **Esporta** e fare clic su **OK**.
   3. Attendere il completamento dell'operazione.

> [!NOTE]
> Come si può notare, la procedura non include il passaggio di sincronizzazione completa in Azure AD Connector e quello di esportazione in Active Directory Connector. Questi passaggi non sono necessari perché i valori degli attributi si trasmettono solo da Active Directory locale ad Azure AD.

## <a name="step-8-re-enable-sync-scheduler"></a>Passaggio 8: riabilitare l'utilità di pianificazione della sincronizzazione
Riabilitare l'utilità di pianificazione della sincronizzazione predefinita:

1. Avviare una sessione di PowerShell.
2. Riabilitare la sincronizzazione pianificata eseguendo questo cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-9-verify-the-result"></a>Passaggio 9: verificare il risultato
A questo punto è necessario verificare la configurazione e abilitarla per gli utenti.

1. Aggiungere l'area all'attributo selezionato per un utente. L'elenco di aree geografiche disponibili è indicato in questa tabella.  
![Screenshot dell'attributo AD aggiunto a un utente](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. Attendere che l'attributo sia sincronizzato con Azure AD.
3. Usare il servizio PowerShell di Exchange Online per verificare che l'area della cassetta postale sia stata impostata correttamente.  
![Screenshot di PowerShell per Exchange Online](./media/how-to-connect-sync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
Supponendo che il tenant sia stato contrassegnato come in grado di usare questa funzionalità, la cassetta postale viene spostata nell'area geografica corretta. Per verificare che ciò avvenga, esaminare il nome del server in cui si trova la cassetta postale.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sulle aree geografiche in Microsoft 365:

* [Sessioni su Multi-Geo Capabilities in Ignite](https://aka.ms/MultiGeoIgnite)
* [Multi-Geo Capabilities in OneDrive](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)
* [Multi-Geo Capabilities in SharePoint Online](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365)

Altre informazioni sul modello di configurazione nel motore di sincronizzazione:

* Per altre informazioni sul modello di configurazione, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Per altre informazioni sul linguaggio delle espressioni, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sulle espressioni di provisioning dichiarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).

Argomenti generali:

* [Servizio di sincronizzazione Azure AD Connect: comprendere e personalizzare la sincronizzazione](how-to-connect-sync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)