---
title: Creare un tenant in Desktop virtuale Windows (versione classica) - Azure
description: Descrive come configurare i tenant di Desktop virtuale Windows (versione classica) in Azure Active Directory.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f41584194f8f5e8afde630405116b8b169cb5656
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91542037"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-classic"></a>Esercitazione: Creare un tenant in Desktop virtuale Windows (versione classica)

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows (versione classica), che non supporta gli oggetti Azure Resource Manager di Desktop virtuale Windows.

La creazione di un tenant di Desktop virtuale Windows è il primo passaggio per lo sviluppo di una soluzione di virtualizzazione desktop. Un tenant è un gruppo di uno o più pool di host. Ogni pool di host è costituito da più host di sessione, in esecuzione come macchine virtuali in Azure e registrati nel servizio Desktop virtuale Windows. Ogni pool di host è inoltre costituito da uno o più gruppi di app usati per pubblicare le risorse delle applicazioni desktop e remote per gli utenti. Con un tenant è possibile creare pool di host e gruppi di app, assegnare utenti e stabilire connessioni tramite il servizio.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Concedere le autorizzazioni di Azure Active Directory al servizio Desktop virtuale Windows.
> * Assegnare il ruolo applicazione TenantCreator a un utente nel tenant di Azure Active Directory.
> * Creare un tenant in Desktop virtuale Windows.

## <a name="what-you-need-to-set-up-a-tenant"></a>Requisiti per la configurazione di un tenant

Prima di iniziare la configurazione del tenant di Desktop virtuale Windows, assicurarsi di avere a disposizione quanto segue:

* L'ID tenant di [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) per gli utenti di Desktop virtuale Windows.
* Un account di amministratore globale all'interno del tenant di Azure Active Directory.
   * Questo requisito si applica anche alle organizzazioni CSP (Cloud Solution Provider, provider di soluzioni cloud) che creano un tenant in Desktop virtuale Windows per i loro clienti. Un'organizzazione CSP deve essere in grado di accedere come amministratore globale alle istanze di Azure Active Directory dei clienti.
   * L'account amministratore deve provenire dal tenant di Azure Active Directory in cui si sta provando a creare il tenant di Desktop virtuale Windows. Questa procedura non supporta gli account di Azure Active Directory B2B (guest).
   * L'account amministratore deve essere un account aziendale o dell'istituto di istruzione.
* Una sottoscrizione di Azure.

Per la corretta esecuzione della procedura descritta in questa esercitazione, è necessario avere a disposizione l'ID tenant, l'account amministratore globale e la sottoscrizione.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Concedere le autorizzazioni a Desktop virtuale Windows

Se le autorizzazioni per Desktop virtuale Windows per questa istanza di Azure Active Directory sono già state concesse, ignorare questa sezione.

La concessione di autorizzazioni per il servizio Desktop virtuale Windows consente di eseguire query in Azure Active Directory per le attività amministrative e degli utenti finali.

Per concedere le autorizzazioni al servizio:

1. Aprire un browser e avviare il flusso di consenso amministratore per l'[app server Desktop virtuale Windows](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Se si gestisce un cliente ed è necessario concedere il consenso amministratore per la relativa directory, immettere l'URL seguente nel browser sostituendo {tenant} con il nome del dominio di Azure AD del cliente. Se l'organizzazione del cliente ha registrato contoso.onmicrosoft.com come nome di dominio di Azure AD, ad esempio, sostituire {tenant} con contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Accedere alla pagina di consenso di Desktop virtuale Windows con un account di amministratore globale. Ad esempio, nell'organizzazione Contoso l'account potrebbe essere admin@contoso.com o admin@contoso.onmicrosoft.com.
3. Selezionare **Accetto**.
4. Attendere un minuto affinché Azure AD possa registrare il consenso.
5. Aprire un browser e avviare il flusso di consenso amministratore per l'[app client Desktop virtuale Windows](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Se si gestisce un cliente ed è necessario concedere il consenso amministratore per la relativa directory, immettere l'URL seguente nel browser sostituendo {tenant} con il nome del dominio di Azure AD del cliente. Se l'organizzazione del cliente ha registrato contoso.onmicrosoft.com come nome di dominio di Azure AD, ad esempio, sostituire {tenant} con contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Accedere alla pagina di consenso di Desktop virtuale Windows come amministratore globale, come nel passaggio 2.
7. Selezionare **Accetto**.

## <a name="assign-the-tenantcreator-application-role"></a>Assegnare il ruolo applicazione TenantCreator

L'assegnazione del ruolo applicazione TenantCreator a un utente di Azure Active Directory consente a quest'ultimo di creare un tenant in Desktop virtuale Windows associato all'istanza di Azure Active Directory. Per assegnare il ruolo TenantCreator, è necessario usare l'account di amministratore globale.

Per assegnare il ruolo applicazione TenantCreator:

1. Accedere al [portale di Azure](https://portal.azure.com) per gestire il ruolo applicazione TenantCreator. Cercare e selezionare **Applicazioni aziendali**. Se si usano più tenant di Azure Active Directory, la procedura consigliata è di aprire una sessione privata del browser e di copiare e incollare gli URL nella barra degli indirizzi.

   > [!div class="mx-imgBorder"]
   > ![Screenshot della ricerca di applicazioni aziendali nel portale di Azure](../media/azure-portal-enterprise-applications.png)

2. All'interno di **Applicazioni aziendali** cercare **Desktop virtuale Windows**. Verranno visualizzate le due applicazioni per cui è stato fornito il consenso nella sezione precedente. Tra queste due app, selezionare **Desktop virtuale Windows**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot dei risultati della ricerca visualizzati per "Windows Virtual Desktop" ("Desktop virtuale Windows") in "Enterprise applications" ("Applicazioni aziendali"). L'app denominata "Desktop virtuale Windows" è evidenziata.](../media/tenant-enterprise-app.png)

3. Selezionare **Utenti e gruppi**. Come si può vedere, l'amministratore che ha concesso il consenso per l'applicazione è già presente nell'elenco con il ruolo **Accesso predefinito**. Questo ruolo non è sufficiente per creare un tenant di Desktop virtuale Windows. Continuare a seguire queste istruzioni per aggiungere il ruolo **TenantCreator** a un utente.

   > [!div class="mx-imgBorder"]
   > ![Screenshot degli utenti e dei gruppi assegnati per gestire l'applicazione aziendale "Desktop virtuale Windows". Lo screenshot illustra solo un'assegnazione, relativa ad "Accesso predefinito".](../media/tenant-default-access.png)

4. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella scheda **Aggiungi assegnazione**.
5. Cercare un account utente che creerà il tenant di Desktop virtuale Windows. Per semplicità, può essere l'account di amministratore globale.
   - Se si usa un provider di identità Microsoft, ad esempio contosoadmin@live.com o contosoadmin@outlook.com, potrebbe non essere possibile accedere a Desktop virtuale Windows. È consigliabile usare invece un account specifico del dominio come admin@contoso.com o admin@contoso.onmicrosoft.com.

   > [!div class="mx-imgBorder"]
   > ![Screenshot della selezione di un utente da aggiungere come "TenantCreator".](../media/tenant-assign-user.png)

   > [!NOTE]
   > È necessario selezionare un utente (o un gruppo contenente un utente) che derivi da questa istanza di Azure Active Directory. Non è possibile scegliere un utente guest (B2B) o un'entità servizio.

6. Selezionare l'account utente, scegliere il pulsante **Seleziona** e quindi selezionare **Assegna**.
7. Nella pagina **Desktop virtuale Windows - Utenti e gruppi** verificare se viene visualizzata una nuova voce con il ruolo **TenantCreator** assegnato all'utente che creerà il tenant di Desktop virtuale Windows.

   > [!div class="mx-imgBorder"]
   > ![Screenshot degli utenti e dei gruppi assegnati per gestire l'applicazione aziendale "Desktop virtuale Windows". Lo screenshot ora include una seconda voce di un utente assegnato al ruolo "TenantCreator".](../media/tenant-tenant-creator-added.png)

Prima di continuare con la creazione del tenant di Desktop virtuale Windows, sono necessarie due informazioni:

   - ID tenant (o **ID directory**) di Azure Active Directory
   - L'ID sottoscrizione di Azure

Per trovare l'ID tenant (o **ID directory**) di Azure Active Directory:
1. Nella stessa sessione del [portale di Azure](https://portal.azure.com) cercare e selezionare **Azure Active Directory**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot dei risultati della ricerca di "Azure Active Directory" nel portale di Azure. Il risultato nella categoria "Servizi" è evidenziato.](../media/tenant-search-azure-active-directory.png)

2. Scorrere in basso fino a trovare la voce **Proprietà** e quindi selezionarla.
3. Cercare **ID directory** e quindi selezionare l'icona degli Appunti. Incollare l'ID in una posizione da cui sia possibile usarlo in seguito come valore **AadTenantId**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot delle proprietà di Azure Active Directory. Il puntatore del mouse passa sull'icona degli Appunti per copiare e incollare "ID directory".](../media/tenant-directory-id.png)

Per trovare l'ID sottoscrizione di Azure:
1. Nella stessa sessione del [portale di Azure](https://portal.azure.com) cercare e selezionare **Sottoscrizioni**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot dei risultati della ricerca di "Azure Active Directory" nel portale di Azure. Il risultato della ricerca di "Servizi" è evidenziato.](../media/tenant-search-subscription.png)

2. Selezionare la sottoscrizione di Azure da usare per ricevere le notifiche del servizio Desktop virtuale Windows.
3. Cercare **ID sottoscrizione** e quindi passare il puntatore del mouse sul valore fino a visualizzare l'icona degli Appunti. Selezionare l'icona degli Appunti e incollare l'ID in una posizione da cui sia possibile usarlo in seguito come valore **AzureSubscriptionId**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot delle proprietà della sottoscrizione di Azure. Il puntatore del mouse passa sull'icona degli Appunti per copiare e incollare "ID sottoscrizione".](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Creare un tenant di Desktop virtuale Windows

Dopo aver concesso le autorizzazioni al servizio Desktop virtuale Windows per eseguire query su Azure Active Directory e aver assegnato il ruolo TenantCreator a un account utente, è possibile creare il tenant di Desktop virtuale Windows.

Prima di tutto, [scaricare e importare il modulo Desktop virtuale Windows](/powershell/windows-virtual-desktop/overview/) da usare nella sessione di PowerShell, se non è già stato fatto.

Accedere a Desktop virtuale Windows usando l'account utente TenantCreator con questo cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Creare quindi un nuovo tenant di Desktop virtuale Windows associato al tenant di Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Sostituire i valori tra parentesi con i valori appropriati per l'organizzazione e per il tenant. Il nome scelto per il nuovo tenant di Desktop virtuale Windows deve essere un nome univoco globale. Ad esempio, per l'utente TenantCreator di Desktop virtuale Windows nell'organizzazione Contoso, il cmdlet da eseguire sarà come questo:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

È consigliabile assegnare l'accesso amministrativo a un secondo utente nel caso in cui il proprio account risulti bloccato oppure se si va in ferie e serve qualcuno che agisca da amministratore del tenant in propria assenza. Per assegnare l'accesso amministrativo a un secondo utente, eseguire il cmdlet seguente sostituendo `<TenantName>` e `<Upn>` con il nome del tenant e con il nome UPN del secondo utente.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato il tenant, sarà necessario creare un'entità servizio in Azure Active Directory a cui assegnare un ruolo in Desktop virtuale Windows. L'entità servizio consentirà di distribuire l'offerta di Azure Marketplace di Desktop virtuale Windows per creare un pool di host. Per altre informazioni sui pool di host, continuare con l'esercitazione per la creazione di pool di host in Desktop virtuale Windows.

> [!div class="nextstepaction"]
> [Creare entità servizio e assegnazioni di ruolo con PowerShell](create-service-principal-role-powershell.md)
