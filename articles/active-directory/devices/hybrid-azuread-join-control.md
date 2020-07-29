---
title: Convalida controllata del join di Azure AD ibrido-Azure AD
description: Informazioni su come eseguire una convalida controllata del join ibrido di Azure AD prima di abilitarlo in tutta l'organizzazione contemporaneamente
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66b216e5e511d2d80378ee7e2d124dccbc7abcb7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85252713"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Convalida controllata dell'aggiunta ad Azure AD ibrido

Quando tutti i prerequisiti sono soddisfatti, i dispositivi Windows verranno registrati automaticamente come dispositivi nel tenant del Azure AD. Lo stato di queste identità del dispositivo in Azure AD viene definito ibrido Azure AD join. Altre informazioni sui concetti trattati in questo articolo sono disponibili negli articoli [Introduzione alla gestione dei dispositivi in Azure Active Directory](overview.md) e [pianificare l'implementazione di Azure Active Directory join ibrida](hybrid-azuread-join-plan.md).

È possibile che le organizzazioni desiderino eseguire una convalida controllata del join ibrido di Azure AD prima di abilitarlo nell'intera organizzazione. In questo articolo viene illustrato come eseguire una convalida controllata del join di Azure AD ibrido.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Convalida controllata del join Azure AD ibrido nei dispositivi Windows correnti

Per i dispositivi che eseguono il sistema operativo desktop Windows, è supportata la versione 1607 (Aggiornamento dell'anniversario di Windows 10) o successiva. Come procedura consigliata, eseguire l'aggiornamento alla versione più recente di Windows 10.

Per eseguire una convalida controllata del join Azure AD ibrido nei dispositivi Windows correnti, è necessario:

1. Cancellare la voce del punto di connessione del servizio (SCP) da Active Directory (AD) se esistente
1. Configurare l'impostazione del registro di sistema sul lato client per SCP nei computer aggiunti a un dominio usando un oggetto Criteri di gruppo (GPO)
1. Se si utilizza AD FS, è inoltre necessario configurare l'impostazione del registro di sistema sul lato client per SCP nel server AD FS utilizzando un oggetto Criteri di gruppo  
1. Potrebbe anche essere necessario [personalizzare le opzioni di sincronizzazione](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) in Azure ad Connect per abilitare la sincronizzazione dei dispositivi. 


### <a name="clear-the-scp-from-ad"></a>Cancellare SCP da AD

Utilizzare l'editor interfacce Active Directory Services (ADSI Edit) per modificare gli oggetti SCP in Active Directory.

1. Avviare l'applicazione desktop **ADSI Edit** da e dalla workstation amministrativa o da un controller di dominio come amministratore dell'organizzazione.
1. Connettersi al **contesto dei nomi di configurazione** del dominio.
1. Passare a **CN = Configuration, DC = contoso, DC = com**  >  **CN = Services**  >  **CN = Device Registration Configuration**
1. Fare clic con il pulsante destro del mouse sull'oggetto foglia **CN = 62a0ff2e-97b9-4513-943F-0d221bd30080** e scegliere **Proprietà** .
   1. Selezionare **parole chiave** dalla finestra **Editor attributi** e fare clic su **modifica** .
   1. Selezionare i valori di **azureADId** e **azureADName** (uno alla volta) e fare clic su **Rimuovi** .
1. Chiudi **ADSI Edit**


### <a name="configure-client-side-registry-setting-for-scp"></a>Configurare l'impostazione del registro di sistema sul lato client per SCP

Usare l'esempio seguente per creare un oggetto Criteri di gruppo (GPO) per distribuire un'impostazione del registro di sistema configurando una voce SCP nel registro dei dispositivi.

1. Aprire una console di gestione di Criteri di gruppo e creare un nuovo oggetto Criteri di gruppo nel dominio.
   1. Specificare un nome per l'oggetto Criteri di gruppo appena creato, ad esempio ClientSideSCP.
1. Modificare l'oggetto Criteri di gruppo e individuare il percorso seguente: preferenze **Configurazione computer**  >  **Preferences**  >  **Windows Settings**  >  **Registro di sistema** impostazioni di Windows
1. Fare clic con il pulsante destro del mouse sul Registro di sistema e selezionare **nuovo**  >  **elemento del registro**
   1. Nella scheda **generale** configurare quanto segue:
      1. Azione: **aggiornamento**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Percorso della chiave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome valore: **TenantId**
      1. Tipo di valore: **REG_SZ**
      1. Dati valore: GUID o **ID directory** dell'istanza di Azure ad (questo valore è disponibile nell' **Azure portal**  >  **Azure Active Directory**  >  **Properties**  >  **ID directory**delle proprietà portale di Azure Azure Active Directory)
   1. Fare clic su **OK**.
1. Fare clic con il pulsante destro del mouse sul Registro di sistema e selezionare **nuovo**  >  **elemento del registro**
   1. Nella scheda **generale** configurare quanto segue:
      1. Azione: **aggiornamento**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Percorso della chiave: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Nome valore: **TenantName**
      1. Tipo di valore: **REG_SZ**
      1. Dati valore: il **nome di dominio** verificato se si usa un ambiente federato, ad esempio ad FS. Il **nome di dominio** verificato o il nome di dominio onmicrosoft.com, ad esempio, `contoso.onmicrosoft.com` se si usa l'ambiente gestito
   1. Fare clic su **OK**.
1. Chiudere l'editor per l'oggetto Criteri di gruppo appena creato
1. Collegare l'oggetto Criteri di gruppo appena creato all'unità organizzativa desiderata contenente i computer aggiunti a un dominio che appartengono al popolamento di implementazione controllato

### <a name="configure-ad-fs-settings"></a>Configurare le impostazioni di AD FS

Se si utilizza AD FS, prima di tutto è necessario configurare SCP sul lato client attenendosi alle istruzioni sopra riportate mediante il collegamento dell'oggetto Criteri di gruppo ai server di AD FS. L'oggetto SCP definisce l'origine dell'autorità per gli oggetti dispositivo. Può essere locale o Azure AD. Quando SCP sul lato client è configurato per AD FS, l'origine per gli oggetti dispositivo viene stabilita come Azure AD.

> [!NOTE]
> Se non è stato possibile configurare SCP sul lato client nei server AD FS, l'origine per le identità del dispositivo verrebbe considerata come in locale. ADFS avvierà quindi l'eliminazione degli oggetti dispositivo dalla directory locale dopo il periodo stabilito definito nell'attributo "MaximumInactiveDays" della registrazione del dispositivo ADFS. È possibile trovare gli oggetti di registrazione del dispositivo ADFS usando il [cmdlet Get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Convalida controllata del join di Azure AD ibrido nei dispositivi Windows di livello inferiore

Per registrare i dispositivi Windows di livello inferiore, le organizzazioni devono installare [Microsoft Workplace Join per computer non Windows 10](https://www.microsoft.com/download/details.aspx?id=53554), disponibile nell'Area download Microsoft.

È possibile distribuire il pacchetto usando un sistema di distribuzione software come  [Microsoft Endpoint Configuration Manager](/configmgr/). Il pacchetto supporta le opzioni standard di installazione invisibile all'utente con il parametro quiet. Configuration Manager Current Branch offre vantaggi aggiuntivi rispetto alle versioni precedenti, come la possibilità di tenere traccia delle registrazioni completate.

Il programma di installazione crea nel sistema un'attività pianificata che viene eseguita nel contesto utente. e attivata nel momento in cui l'utente accede a Windows. L'attività aggiunge automaticamente il dispositivo con Azure AD con le credenziali dell'utente dopo l'autenticazione con Azure AD.

Per controllare la registrazione del dispositivo, è necessario distribuire il pacchetto di Windows Installer al gruppo selezionato di dispositivi Windows di livello inferiore.

> [!NOTE]
> Se una SCP non è configurata in Active Directory, è necessario seguire lo stesso approccio descritto in [configurare l'impostazione del registro di sistema sul lato client per SCP](#configure-client-side-registry-setting-for-scp)sui computer aggiunti a un dominio usando un oggetto Criteri di gruppo (GPO).


Dopo aver verificato che tutto funzioni come previsto, è possibile registrare automaticamente il resto dei dispositivi Windows correnti e di livello inferiore con Azure AD [configurando SCP usando Azure ad Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Passaggi successivi

[Pianificare l'implementazione dell'aggiunta ad Azure Active Directory ibrido](hybrid-azuread-join-plan.md)
