---
title: "Azure AD Connect: Eseguire l'aggiornamento da una versione precedente | Documentazione Microsoft"
description: Illustra i diversi metodi per eseguire l'aggiornamento alla versione più recente di Azure Active Directory Connect, tra cui l'aggiornamento sul posto e la migrazione swing.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65fc0e84582c005c5796ceac86ee28fc46b2e1d8
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094217"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Eseguire l'aggiornamento da una versione precedente alla versione più recente
Questo argomento descrive i diversi metodi che è possibile usare per aggiornare l'installazione di Azure Active Directory (Azure AD) Connect alla versione più recente.  È anche possibile usare le procedure illustrate nella sezione [Migrazione swing](#swing-migration), che consentono di apportare modifiche significative alla configurazione.

>[!NOTE]
> È importante che i server continuino a essere aggiornati con le versioni più recenti di Azure AD Connect. Vengono costantemente apportati aggiornamenti a AADConnect. questi aggiornamenti includono correzioni per problemi di sicurezza e bug, oltre a miglioramenti di manutenzione, prestazioni e scalabilità. Per visualizzare la versione più recente e per sapere quali modifiche sono state apportate tra le versioni, consultare la [cronologia delle versioni di rilascio](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history)

>[!NOTE]
> Attualmente è supportato l'aggiornamento da qualsiasi versione di Azure AD Connect alla versione corrente. Gli aggiornamenti sul posto di DirSync o ADSync non sono supportati ed è necessaria una migrazione swing.  Se si vuole eseguire l'aggiornamento da DirSync, vedere [eseguire l'aggiornamento dallo strumento di sincronizzazione Azure ad (dirsync)](how-to-dirsync-upgrade-get-started.md) o dalla sezione [migrazione swing](#swing-migration) .  </br>In pratica, i clienti con versioni molto vecchie potrebbero riscontrare problemi non direttamente correlati a Azure AD Connect. I server che sono stati in produzione per diversi anni, in genere hanno applicato diverse patch e non tutti questi possono essere considerati.  In genere, i clienti che non hanno eseguito l'aggiornamento in 12-18 mesi devono considerare un aggiornamento swing, in quanto si tratta dell'opzione più conservativa e meno rischiosa.

Se si vuole eseguire l'aggiornamento da DirSync, vedere invece l'argomento relativo all'[aggiornamento dallo strumento di sincronizzazione di Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md) .

Esistono diverse strategie per eseguire l'aggiornamento di Azure AD Connect.

| Metodo | Descrizione |
| --- | --- |
| [aggiornamento automatico](how-to-connect-install-automatic-upgrade.md) |Si tratta del metodo più semplice per i clienti con installazione rapida. |
| [Aggiornamento sul posto](#in-place-upgrade) |Se si ha un singolo server, è possibile eseguire un aggiornamento sul posto dell'installazione nello stesso server. |
| [Migrazione swing](#swing-migration) |Se si usano due server, è possibile preparare uno dei due con la nuova versione o configurazione e, quando si è pronti, modificare il server attivo. |

Per informazioni sulle autorizzazioni, vedere le [autorizzazioni necessarie per un aggiornamento](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Dopo aver abilitato il nuovo server Azure AD Connect per avviare la sincronizzazione delle modifiche Azure AD, non è necessario eseguire il rollback all'uso di DirSync o Azure AD Sync. Il downgrade da Azure AD Connect ai client legacy, inclusi DirSync e Azure AD Sync, non è supportato e può causare problemi come la perdita di dati in Azure AD.

## <a name="in-place-upgrade"></a>Aggiornamento sul posto
Un aggiornamento sul posto è indicato per il passaggio da Azure AD Sync o Azure AD Connect. Non è possibile usarlo per lo spostamento da DirSync o per una soluzione con Forefront Identity Manager (FIM) e Azure AD Connect.

Questo metodo è preferibile quando sono presenti un singolo server e meno di 100.000 oggetti. Se sono state apportate modifiche alle regole di sincronizzazione predefinite, vengono eseguite un'importazione e una sincronizzazione complete. Questo metodo si assicura che la nuova configurazione venga applicata a tutti gli oggetti presenti nel sistema. L'esecuzione può richiedere alcune ore, a seconda del numero di oggetti nell'ambito del motore di sincronizzazione. Viene sospesa la normale sincronizzazione differenziale pianificata, per impostazione predefinita ogni 30 minuti, mentre continua la sincronizzazione delle password. È consigliabile eseguire l'aggiornamento sul posto durante il fine settimana. Se sono state apportate modifiche alla configurazione predefinita con la nuova versione di Azure AD Connect, verrà avviata un'importazione/una sincronizzazione differenziale normale.  
![Aggiornamento sul posto](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Se sono state apportate modifiche alle regole di sincronizzazione predefinite, queste regole verranno reimpostate sui valori predefiniti della configurazione al momento dell'aggiornamento. Per assicurarsi che la configurazione venga mantenuta tra un aggiornamento e l'altro, verificare che le modifiche vengano apportate come descritto in [Procedure consigliate per modificare la configurazione predefinita](how-to-connect-sync-best-practices-changing-default-configuration.md).

Durante l'aggiornamento sul posto, è possibile che vengano introdotte modifiche che richiedono l'esecuzione di specifiche attività di sincronizzazione (ad esempio, i passaggi di importazione completa e di sincronizzazione completa) al termine dell'aggiornamento. Per rinviare queste attività, fare riferimento alla sezione [Come rinviare la sincronizzazione completa dopo l'aggiornamento](#how-to-defer-full-synchronization-after-upgrade).

Se si usa Azure AD Connect con un connettore non standard (ad esempio il connettore Generic LDAP e il connettore Generic SQL), è necessario aggiornare la configurazione del connettore corrispondente nel [Synchronization Service Manager](./how-to-connect-sync-service-manager-ui-connectors.md) dopo l'aggiornamento sul posto. Per informazioni dettagliate su come aggiornare la configurazione del connettore, consultare la sezione dell'articolo [Cronologia di rilascio delle versioni dei connettori - Troubleshooting (Risoluzione dei problemi)](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#troubleshooting). Se non si aggiorna la configurazione, i passaggi di importazione ed esportazione non funzioneranno correttamente per il connettore. Viene visualizzato l'errore seguente nel registro eventi dell'applicazione con il messaggio *"Assembly version in AAD Connector configuration ("X.X.XXX.X") is earlier than the actual version ("X.X.XXX.X") of "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll" (La versione dell'assembly nella configurazione di AAD Connector ("X.X.XXX.X") è precedente rispetto alla versione corrente ("X.X.XXX.X") di "C:\Programmi\Microsoft Azure AD Sync\Estensioni\Microsoft.IAM.Connector.GenericLdap.dll).*

## <a name="swing-migration"></a>Migrazione swing
Se è presente una distribuzione complessa o molti oggetti, può essere impraticabile eseguire un aggiornamento sul posto nel sistema attivo. Per alcuni clienti questa procedura potrebbe richiedere più giorni, durante i quali non verranno elaborate modifiche differenziali. È possibile usare questo metodo anche quando si pianificano modifiche sostanziali della configurazione e si vogliono provare tali modifiche prima di effettuarne il push nel cloud.

Il metodo consigliato per questi scenari è una migrazione swing. Sono necessari almeno due server, uno attivo e uno di staging. Il server attivo (linee blu continue nell'immagine seguente) è responsabile del carico di produzione attivo. Il server di staging (linee tratteggiate viola) viene preparato con la nuova versione o configurazione. Quando la preparazione è completa, il server viene reso attivo. Il server precedentemente attivo, su cui ora è installata la versione o configurazione precedente, diventa il server di staging e viene aggiornato.

I due server possono usare versioni diverse. Ad esempio, il server attivo per il quale è stata pianificata la rimozione delle autorizzazioni può usare Azure AD Sync, mentre il nuovo server di staging può usare Azure AD Connect. Se si usa la migrazione swing per sviluppare una nuova configurazione, è consigliabile usare la stessa versione sui due server.  
![server di gestione temporanea](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Alcuni clienti preferiscono avere tre o quattro server per questo scenario. Quando il server di staging viene aggiornato, in caso di [ripristino di emergenza](how-to-connect-sync-staging-server.md#disaster-recovery) non è disponibile un server di backup. Con tre o quattro server è possibile preparare un set di server primari o di standby con la nuova versione, per assicurare che sia sempre presente un server di staging pronto a sostituire il server attivo.

Questa procedura è indicata anche per il passaggio da Azure AD Sync o da una soluzione con FIM e Azure AD Connector. Questa procedura non è indicata per DirSync, ma nell'argomento [Aggiornare il servizio di sincronizzazione di Microsoft Azure Active Directory (DirSync)](how-to-dirsync-upgrade-get-started.md) è disponibile lo stesso metodo con migrazione swing, chiamato anche distribuzione parallela, con i passaggi per DirSync.

### <a name="use-a-swing-migration-to-upgrade"></a>Usare una migrazione swing per l'aggiornamento
1. Se si usa Azure AD Connect in entrambi i server e si pianifica di eseguire soltanto una modifica della configurazione, assicurarsi che il server attivo e il server di staging usino entrambi la stessa versione. Questo accorgimento rende più facile un successivo confronto delle differenze. Se si esegue l'aggiornamento da Azure AD Sync, questi server hanno versioni diverse. Se si esegue l'aggiornamento da una versione precedente di Azure AD Connect, è consigliabile che i due server usino la stessa versione. Non si tratta tuttavia di un requisito obbligatorio.
2. Se è stata eseguita una configurazione personalizzata che non è presente nel server di staging, seguire la procedura descritta in [Spostare la configurazione personalizzata dal server attivo al server di staging](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Se si esegue l'aggiornamento da una versione precedente di Azure AD Connect, aggiornare il server di staging alla versione più recente. Se si esegue lo spostamento da Azure AD Sync, installare Azure AD Connect nel server di staging.
4. Consentire al motore di sincronizzazione di eseguire un'importazione completa e una sincronizzazione completa nel server di gestione temporanea.
5. Verificare che la nuova configurazione non abbia causato modifiche impreviste seguendo la procedura descritta nella sezione "Verificare" in [Verificare la configurazione di un server](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). In caso di imprevisti, correggere la configurazione, eseguire l'importazione e la sincronizzazione, quindi verificare i dati fino a ottenere il risultato necessario, seguendo la procedura.
6. Impostare il server di gestione temporanea come server attivo. Questo è il passaggio finale, dal titolo "Cambiare il server attivo", di [Verificare la configurazione di un server](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Se si esegue l'aggiornamento di Azure AD Connect, eseguire ora l'aggiornamento del server in modalità di staging alla versione più recente. Seguire gli stessi passaggi descritti in precedenza per aggiornare i dati e la configurazione. Se si esegue l'aggiornamento da Azure AD Sync, è possibile disattivare e rimuovere le autorizzazioni dal server precedente.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Spostare una configurazione personalizzata dal server attivo al server di staging
Se sono state apportate modifiche di configurazione al server attivo, è necessario assicurarsi che le stesse modifiche vengano applicate al server di staging. Per facilitare lo spostamento, è possibile usare l'[analizzatore di configurazione Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

È possibile spostare le regole di sincronizzazione personalizzate create usando PowerShell. È necessario applicare altre modifiche allo stesso modo in entrambi i sistemi e non è possibile eseguire la migrazione delle modifiche. L'[analizzatore di configurazione](https://github.com/Microsoft/AADConnectConfigDocumenter) consente di confrontare i due sistemi per assicurarsi che siano identici. Lo strumento è anche utile per automatizzare la procedura illustrata in questa sezione.

È necessario configurare gli elementi seguenti nello stesso modo in entrambi i server:

* Connessione alle stesse foreste
* Domini e filtri unità organizzativa
* Stesse funzionalità facoltative, come la sincronizzazione e il writeback delle password

**Spostare le regole di sincronizzazione personalizzate**  
Per spostare le regole di sincronizzazione personalizzate, seguire questa procedura:

1. Aprire l' **editor delle regole di sincronizzazione** nel server attivo.
2. Selezionare una regola personalizzata. Fare clic su **esportare**. Viene visualizzata una finestra del Blocco note. Salvare il file temporaneo con estensione ps1. In questo modo diventa uno script PowerShell. Copiare il file con estensione PS1 nel server di staging.  
   ![Esportazione delle regole di sincronizzazione](./media/how-to-upgrade-previous-version/exportrule.png)
3. Il GUID del connettore è diverso nel server di staging e deve essere modificato. Per ottenere il GUID, avviare l'**editor di sincronizzazione delle regole**, selezionare una delle regole predefinite che rappresentano lo stesso sistema connesso e fare clic sul pulsante per l'**esportazione**. Sostituire il GUID nel file con estensione ps1 con il GUID del server di gestione temporanea.
4. In un prompt dei comandi di PowerShell eseguire il file con estensione ps1. In questo modo viene creata la regola di sincronizzazione personalizzata nel server di staging.
5. Ripetere la procedura per tutte le regole personalizzate.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Come rinviare la sincronizzazione completa dopo l'aggiornamento
Durante l'aggiornamento sul posto, è possibile che vengano introdotte modifiche che richiedono l'esecuzione di specifiche attività di sincronizzazione (ad esempio, i passaggi di importazione completa e di sincronizzazione completa). In caso di modifiche allo schema del connettore, ad esempio, è necessario eseguire sui connettori interessati l'**importazione completa**, mentre in caso di modifiche alle regole di sincronizzazione è necessario eseguire la **sincronizzazione completa**. Durante l'aggiornamento, Azure AD Connect determina le attività di sincronizzazione necessarie e le registra come *sostituzioni*. Nel ciclo di sincronizzazione successivo, l'utilità di pianificazione della sincronizzazione preleva queste sostituzioni e le esegue. Dopo essere state eseguite, le sostituzioni vengono rimosse.

È possibile che, in alcuni casi, si preferisca non eseguire queste sostituzioni subito dopo l'aggiornamento. In presenza di numerosi oggetti sincronizzati, ad esempio, può essere opportuno eseguire la procedura di sincronizzazione dopo l'orario lavorativo. Per rimuovere queste sostituzioni:

1. Durante l'aggiornamento, **deselezionare** l'opzione **Avvia il processo di sincronizzazione al termine della configurazione**. In questo modo si disabilita l'utilità di pianificazione della sincronizzazione e si impedisce l'esecuzione automatica del ciclo di sincronizzazione prima che vengano rimosse le sostituzioni.

   ![Screenshot che evidenzia l'opzione avvia il processo di sincronizzazione al termine della configurazione che è necessario cancellare.](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Al termine dell'aggiornamento, eseguire il cmdlet seguente per scoprire quali sostituzioni sono state aggiunte: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > Le sostituzioni sono specifiche di ogni connettore. Nell'esempio seguente, i passaggi di importazione completa e sincronizzazione completa sono stati aggiunti sia al connettore di Active Directory locale sia al connettore di Azure Active Directory.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Annotare le sostituzioni esistenti che sono state aggiunte.
   
4. Per rimuovere le sostituzioni per l'importazione completa e per la sincronizzazione completa in un connettore arbitrario, eseguire il cmdlet seguente: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Per rimuovere le sostituzioni in tutti i connettori, eseguire lo script PowerShell seguente:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Per riabilitare l'utilità di pianificazione, eseguire il cmdlet seguente: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Non dimenticare di eseguire i passaggi di sincronizzazione necessari appena possibile. È possibile eseguire questi passaggi manualmente tramite Synchronization Service Manager oppure aggiungere nuovamente le sostituzioni usando il cmdlet Set-ADSyncSchedulerConnectorOverride.

Per aggiungere le sostituzioni per l'importazione completa e per la sincronizzazione completa in un connettore arbitrario, eseguire il cmdlet seguente: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Risoluzione dei problemi
La sezione seguente contiene informazioni sulla risoluzione dei problemi che possono essere utili se si verifica un problema durante l'aggiornamento di Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Errore di assenza del connettore di Azure Active Directory durante l'aggiornamento di AD Azure Connect

Quando si esegue l'aggiornamento di Azure AD Connect da una versione precedente, si potrebbe ottenere l'errore seguente all'inizio dell'aggiornamento 

![Errore](./media/how-to-upgrade-previous-version/error1.png)

Questo errore si verifica perché il connettore di Azure Active Directory con l'identificatore, b891884f-051e-4a83-95af-2544101c9083, non esiste nella configurazione corrente di Azure AD Connect. Per verificare questa eventualità, aprire una finestra di PowerShell, eseguire il cmdlet `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

Il cmdlet di PowerShell segnala che **non è stato possibile trovare l'MA specificato**.

Il motivo di questo errore è che la configurazione corrente di Azure AD Connect non è supportata per l'aggiornamento. 

Se si desidera installare una versione più recente di Azure AD Connect: chiudere la procedura guidata di Azure AD Connect, disinstallare Azure AD Connect esistente ed eseguire un'installazione pulita di Azure AD Connect più recente.



## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sull' [integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
