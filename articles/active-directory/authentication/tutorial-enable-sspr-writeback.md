---
title: Abilitare il writeback delle password di Azure Active Directory
description: Questa esercitazione illustra come abilitare il writeback della reimpostazione della password self-service di Azure AD usando Azure AD Connect per sincronizzare nuovamente le modifiche in un ambiente Active Directory Domain Services locale.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 444ca19732921b336cae32a9b1eb5755a08e4bd3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028054"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Esercitazione: Abilitare il writeback della reimpostazione della password self-service di Azure Active Directory in un ambiente locale

Con la reimpostazione della password self-service di Azure Active Directory (Azure AD), gli utenti possono reimpostare la password o sbloccare l'account tramite un Web browser. In un ambiente ibrido in cui Azure AD è connesso a un ambiente Active Directory Domain Services (AD DS) locale, possono verificarsi differenze tra le password nelle due directory.

Il writeback delle password consente di sincronizzare le modifiche delle password in Azure AD nell'ambiente Active Directory Domain Services (AD DS) locale. Azure AD Connect fornisce un meccanismo sicuro per inviare le modifiche delle password a una directory locale esistente da Azure AD.

> [!IMPORTANT]
> Questa esercitazione illustra agli amministratori come abilitare la reimpostazione della password self-service in un ambiente locale. Se si è un utente finale già registrato per la reimpostazione della password self-service e si deve ripristinare l'accesso al proprio account, passare a https://aka.ms/sspr.
>
> Se il team IT non ha abilitato la funzionalità per reimpostare la propria password, rivolgersi al supporto tecnico per assistenza aggiuntiva.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare le autorizzazioni necessarie per il writeback delle password
> * Abilitare l'opzione di writeback delle password in Azure AD Connect
> * Abilitare il writeback delle password nella reimpostazione della password self-service di Azure AD

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, sono necessari i privilegi e le risorse seguenti:

* Un tenant di Azure AD funzionante con almeno una licenza di valutazione o di Azure AD Premium P1 abilitata.
    * Se necessario, [crearne uno gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Per altre informazioni, vedere [Requisiti di licenza per la reimpostazione della password self-service di Azure AD](concept-sspr-licensing.md).
* Un account con privilegi di *amministratore globale*.
* Azure AD configurato per la reimpostazione della password self-service.
    * Se necessario, [completare l'esercitazione precedente per abilitare la reimpostazione della password self-service di Azure AD](tutorial-enable-sspr.md).
* Un ambiente Active Directory Domain Services locale esistente configurato con una versione corrente di Azure AD Connect.
    * Se necessario, configurare Azure AD Connect usando la modalità [Rapida](../hybrid/how-to-connect-install-express.md) o [Personalizzata](../hybrid/how-to-connect-install-custom.md).
    * Per usare il writeback delle password, i controller di dominio devono essere Windows Server 2012 o versione successiva.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Configurare le autorizzazioni dell'account per Azure AD Connect

Azure AD Connect consente di sincronizzare utenti, gruppi e credenziali tra un ambiente Active Directory Domain Services locale e Azure AD. In genere si installa Azure AD Connect in un computer Windows Server 2012 o versione successive aggiunto al dominio di Active Directory Domain Services locale.

Per usare correttamente il writeback della reimpostazione della password self-service, per l'account specificato in Azure AD Connect è necessario impostare le autorizzazioni e le opzioni appropriate. Se non si è certi dell'account attualmente in uso, aprire Azure AD Connect e selezionare l'opzione **Visualizza la configurazione corrente**. L'account a cui è necessario aggiungere le autorizzazioni è elencato in **Directory sincronizzate**. Per l'account è necessario impostare le autorizzazioni e le opzioni seguenti:

* **Reimpostazione della password**
* **Autorizzazioni di scrittura** su `lockoutTime`
* **Autorizzazioni di scrittura** su `pwdLastSet`
* **Diritti estesi** per "Password senza scadenza" nell'oggetto radice di *ogni dominio* in tale foresta, se non sono già impostati.

Se non si assegnano tali autorizzazioni, potrebbe sembrare che il writeback sia configurato correttamente, ma gli utenti riscontrano errori quando gestiscono le loro password locali dal cloud. Per visualizzare "Password senza scadenza", è necessario applicare le autorizzazioni a **Questo oggetto e tutti i discendenti**.  

> [!TIP]
>
> Se il writeback delle password non viene eseguito nella directory locale per alcuni account utente, assicurarsi che l'ereditarietà non sia disabilitata per l'account nell'ambiente di Active Directory Domain Services locale. Per la corretta esecuzione della funzionalità, è necessario che le autorizzazioni di scrittura per le password vengano applicate agli oggetti discendenti.

Per impostare le autorizzazioni appropriate per l'esecuzione del writeback delle password, eseguire la procedura seguente:

1. Nell'ambiente Active Directory Domain Services locale aprire **Utenti e computer di Active Directory** con un account con le autorizzazioni di *amministratore di dominio* appropriate.
1. Nel menu **Visualizza** verificare che l'opzione **Funzionalità avanzate** sia attivata.
1. Nel riquadro sinistro fare clic con il pulsante destro del mouse sull'oggetto che rappresenta la radice del dominio e scegliere **Proprietà** > **Sicurezza** > **Avanzate**.
1. Nella scheda **Autorizzazioni** selezionare **Aggiungi**.
1. Per **Entità di sicurezza** selezionare l'account a cui applicare le autorizzazioni, ovvero l'account usato da Azure AD Connect.
1. Nell'elenco a discesa **Applica a** selezionare gli oggetti **Utente discendente**.
1. In *Autorizzazioni* selezionare la casella per l'opzione seguente:
    * **Reimpostazione della password**
1. In *Proprietà* selezionare le caselle per le opzioni seguenti. Scorrere l'elenco per trovare queste opzioni, che potrebbero essere già specificate per impostazione predefinita:
    * **Scrittura di lockoutTime**
    * **Scrittura di pwdLastSet**

    [ ![Impostare le autorizzazioni appropriate in Utenti e computer di Active Directory per l'account usato da Azure AD Connect](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png) ](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Quando si è pronti, selezionare **Applica/OK** per applicare le modifiche e chiudere le finestre di dialogo aperte.

Quando si aggiornano le autorizzazioni, la replica delle autorizzazioni in tutti gli oggetti nella directory potrebbe richiedere fino a un'ora o più.

I criteri delle password nell'ambiente Active Directory Domain Services locale possono impedire la corretta elaborazione delle reimpostazioni delle password. Per il corretto funzionamento del writeback delle password, i criteri di gruppo per *Validità minima della password* devono essere impostati su 0. Questa impostazione è disponibile in **Configurazione computer > Criteri > Impostazioni di Windows > Impostazioni di sicurezza > Criteri account** in `gpedit.msc`.

Se si aggiornano i criteri di gruppo, attendere la replica del criterio aggiornato oppure usare il comando `gpupdate /force`.

> [!Note]
> Per fare in modo che le password vengano cambiate immediatamente, il writeback delle password deve essere impostato su 0. Se tuttavia gli utenti rispettano i criteri locali e la *validità minima della password* è impostata su un valore maggiore di zero, il writeback delle password continuerà a funzionare dopo la valutazione dei criteri locali.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Abilitare il writeback delle password in Azure AD Connect

Una delle opzioni di configurazione in Azure AD Connect riguarda il writeback delle password. Quando questa opzione è abilitata, gli eventi di modifica delle password fanno sì che Azure AD Connect sincronizzi di nuovo le credenziali aggiornate nell'ambiente Active Directory Domain Services locale.

Per abilitare il writeback della reimpostazione della password self-service, è prima necessario abilitare l'opzione corrispondente in Azure AD Connect. Dal server Azure AD Connect completare questa procedura:

1. Accedere al server Azure AD Connect e avviare la configurazione guidata di **Azure AD Connect**.
1. Nella pagina di **benvenuto** selezionare **Configura**.
1. Nella pagina **Attività aggiuntive** selezionare **Personalizzazione delle opzioni di sincronizzazione** e fare clic su **Avanti**.
1. Nella pagina **Connessione ad Azure AD** immettere le credenziali di amministratore globale per il tenant di Azure e selezionare **Avanti**.
1. Nelle pagine di filtro **Connessione delle directory** e **Dominio/unità organizzativa** selezionare **Avanti**.
1. Nella pagina **Funzionalità facoltative** selezionare la casella accanto a **Writeback password** e selezionare **Avanti**.

    ![Configurare Azure AD Connect per il writeback delle password](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Nella pagina **Pronto per la configurazione** fare clic su **Configura** e attendere il completamento del processo.
1. Quando la configurazione termina, selezionare **Esci**.

## <a name="enable-password-writeback-for-sspr"></a>Abilitare il writeback delle password per la reimpostazione della password self-service

Dopo aver abilitato il writeback delle password in Azure AD Connect, configurare la reimpostazione della password self-service di Azure AD per il writeback. Quando si abilita la reimpostazione della password self-service per l'uso del writeback delle password, quando gli utenti modificano o reimpostano la password, la password aggiornata verrà nuovamente sincronizzata con l'ambiente Active Directory Domain Services locale.

Per abilitare il writeback delle password in reimpostazione della password self-service, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore globale.
1. Cercare e selezionare **Azure Active Directory**, selezionare **Reimpostazione password** e quindi scegliere **Integrazione locale**.
1. Impostare l'opzione **Eseguire il writeback delle password nella directory locale?** su *Sì*.
1. Impostare l'opzione **Consentire agli utenti di sbloccare gli account senza reimpostare la password?** su *Sì*.

    ![Abilitare la reimpostazione della password self-service di Azure AD per il writeback delle password](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Al termine, selezionare **Salva**.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si decide di non volere più usare le funzionalità di writeback delle password di reimpostazione della password self-service configurata durante questa esercitazione, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare e selezionare **Azure Active Directory**, selezionare **Reimpostazione password** e quindi scegliere **Integrazione locale**.
1. Impostare l'opzione **Eseguire il writeback delle password nella directory locale?** su *No*.
1. Impostare l'opzione **Consentire agli utenti di sbloccare gli account senza reimpostare la password?** su *No*.

Se non si intende più usare alcuna funzionalità per le password, dal server Azure AD Connect completare questa procedura:

1. Accedere al server Azure AD Connect e avviare la configurazione guidata di **Azure AD Connect**.
1. Nella pagina di **benvenuto** selezionare **Configura**.
1. Nella pagina **Attività aggiuntive** selezionare **Personalizzazione delle opzioni di sincronizzazione** e fare clic su **Avanti**.
1. Nella pagina **Connessione ad Azure AD** immettere le credenziali di amministratore globale per il tenant di Azure e selezionare **Avanti**.
1. Nelle pagine di filtro **Connessione delle directory** e **Dominio/unità organizzativa** selezionare **Avanti**.
1. Nella pagina **Funzionalità facoltative** deselezionare la casella accanto a **Writeback password** e selezionare **Avanti**.
1. Nella pagina **Pronto per la configurazione** fare clic su **Configura** e attendere il completamento del processo.
1. Quando la configurazione termina, selezionare **Esci**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato abilitato il writeback della reimpostazione della password self-service di Azure AD in un ambiente Active Directory Domain Services locale. Si è appreso come:

> [!div class="checklist"]
> * Configurare le autorizzazioni necessarie per il writeback delle password
> * Abilitare l'opzione di writeback delle password in Azure AD Connect
> * Abilitare il writeback delle password nella reimpostazione della password self-service di Azure AD

> [!div class="nextstepaction"]
> [Valutare i rischi all'accesso](tutorial-risk-based-sspr-mfa.md)
