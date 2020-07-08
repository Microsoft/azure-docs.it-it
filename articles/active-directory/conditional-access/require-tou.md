---
title: Condizioni per l'utilizzo richieste per l'accesso condizionale - Azure Active Directory
description: Questa guida di avvio rapido illustra come richiedere l'accettazione delle condizioni per l'utilizzo prima di concedere l'accesso alle app cloud selezionate dall'accesso condizionale di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34cc8154660db8afcd5859d45ccfd622bc7ca88f
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86039824"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Avvio rapido: Richiedere l'accettazione di condizioni per l'utilizzo prima dell'accesso alle app cloud

Prima che gli utenti accedano ad app cloud specifiche all'interno dell'ambiente in uso, è consigliabile ottenere il consenso di questi sotto forma di accettazione delle condizioni per l'utilizzo. L'accesso condizionale di Azure Active Directory (Azure AD) offre:

- Un metodo semplice per la configurazione delle condizioni per l'utilizzo
- La possibilità di richiedere l'accettazione delle condizioni per l'utilizzo tramite criteri di accesso condizionale  

Questa guida di avvio rapido illustra come configurare [criteri di accesso condizionale di Azure AD](../active-directory-conditional-access-azure-portal.md) che richiedono l'accettazione di condizioni per l'utilizzo per un'app cloud selezionata all'interno dell'ambiente.

![Crea criteri](./media/require-tou/5555.png)

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare lo scenario in questa guida introduttiva, sono necessari gli elementi seguenti:

- **Accesso a un'edizione Azure AD Premium**: l'accesso condizionale di Azure AD è una funzionalità di Azure AD Premium.
- **Account di test denominato Isabella Simonsen**: se non si conosce la procedura per creare un account di test, vedere [Aggiungere gli utenti basati su cloud](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Verificare il proprio accesso

L'obiettivo di questo passaggio è farsi un'idea dell'esperienza di accesso senza un criterio di accesso condizionale.

**Per verificare il proprio accesso:**

1. Accedere al [portale di Azure](https://portal.azure.com/) come Isabella Simonsen.
1. Uscire,

## <a name="create-your-terms-of-use"></a>Creare le condizioni per l'utilizzo

Questa sezione illustra i passaggi necessari per creare un esempio di condizioni per l'utilizzo. Quando si creano condizioni per l'utilizzo, si seleziona un valore per **Applica con i modelli di criteri di accesso condizionale**. Se si seleziona **Criteri personalizzati**, subito dopo la creazione delle condizioni per l'utilizzo viene visualizzata la finestra di dialogo per la creazione di un nuovo criterio di accesso condizionale.

**Per creare condizioni per l'utilizzo:**

1. In Microsoft Word creare un nuovo documento.
1. Digitare **My terms of use** (Condizioni per l'utilizzo personali) e quindi salvare il documento con il nome **mytou.pdf**.
1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Sulla barra di spostamento a sinistra nel portale di Azure fare clic su **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Nella sezione **Sicurezza** della pagina **Azure Active Directory** fare clic su **Accesso condizionale**.

   ![Accesso condizionale](./media/require-tou/03.png)

1. Nella sezione **Gestisci** fare clic su **Condizioni per l'utilizzo**.

   ![Condizioni per l'utilizzo](./media/require-tou/04.png)

1. Nel menu in alto fare clic su **Nuove condizioni**.

   ![Condizioni per l'utilizzo](./media/require-tou/05.png)

1. Nella pagina **Nuove condizioni d'uso**:

   ![Condizioni per l'utilizzo](./media/require-tou/112.png)

   1. Nella casella di testo **Nome** digitare **My TOU** (Condizioni per l'utilizzo personali).
   1. Nella casella di testo **Nome visualizzato** digitare **My TOU** (Condizioni per l'utilizzo personali).
   1. Caricare il file PDF delle condizioni per l'utilizzo.
   1. Per **Lingua**, selezionare **Inglese**.
   1. Per **Richiedi agli utenti di espandere le Condizioni d'uso** selezionare **Attivata**.
   1. Per **Applica con i modelli di criteri di accesso condizionale** selezionare **Criteri personalizzati**.
   1. Fare clic su **Crea**.

## <a name="create-your-conditional-access-policy"></a>Creare i criteri di accesso condizionale

Questa sezione illustra come creare i criteri di accesso condizionale necessari. Lo scenario di questa guida introduttiva usa:

- Portale di Azure come segnaposto per un'app cloud che richiede l'accettazione delle condizioni per l'utilizzo. 
- Utente di esempio per testare i criteri di accesso condizionale.  

Nei criteri, impostare:

| Impostazione | valore |
| --- | --- |
| Utenti e gruppi | Isabella Simonsen |
| App cloud | Gestione di Microsoft Azure |
| Concedere l'accesso | My TOU (Condizioni per l'utilizzo personali) |

![Crea criteri](./media/require-tou/1234.png)

**Per configurare i criteri di accesso condizionale:**

1. Nella casella di testo **Nome** della pagina **Nuovo** digitare **Require TOU for Isabella** (Richiedi condizioni per l'utilizzo per Isabella).

   ![Nome](./media/require-tou/71.png)

1. Nella sezione **Assegnazioni** fare clic su **Utenti e gruppi**.

   ![Utenti e gruppi](./media/require-tou/06.png)

1. Nella pagina **Utenti e gruppi**:

   ![Utenti e gruppi](./media/require-tou/24.png)

   1. Fare clic su **Seleziona utenti e gruppi** e quindi selezionare **Utenti e gruppi**.
   1. Fare clic su **Seleziona**.
   1. Nella pagina **Seleziona** scegliere **Isabella Simonsen** e quindi fare clic su **Seleziona**.
   1. Nella pagina **Utenti e gruppi** fare clic su **Fatto**.
1. Fare clic su **App cloud**.

   ![App cloud](./media/require-tou/08.png)

1. Nella pagina **App cloud**:

   ![Selezionare App cloud](./media/require-tou/26.png)

   1. Fare clic su **Selezionare le app**.
   1. Fare clic su **Seleziona**.
   1. Nella pagina **Seleziona** selezionare **Gestione di Microsoft Azure** e quindi fare clic su **Seleziona**.
   1. Nella pagina **App cloud** fare clic su **Fatto**.
1. Nella sezione **Controlli di accesso** fare clic su **Concedi**.

   ![Controlli di accesso](./media/require-tou/10.png)

1. Nella pagina **Concedi**:

   ![Concedi](./media/require-tou/111.png)

   1. Selezionare **Concedi accesso**.
   1. Selezionare **My TOU** (Condizioni per l'utilizzo personali).
   1. Fare clic su **Seleziona**.
1. Nella sezione **Attiva criterio** fare clic su **Sì**.

   ![Abilitare i criteri](./media/require-tou/18.png)

1. Fare clic su **Crea**.

## <a name="evaluate-a-simulated-sign-in"></a>Valutare un accesso simulato

Ora che sono stati configurati i criteri di accesso condizionale, è possibile sapere se funzionano come previsto. Come primo passaggio, usare lo strumento per i criteri What If dell'accesso condizionale per simulare un accesso dell'utente di test. La simulazione valuta l'impatto di questo accesso sui criteri e genera un report di simulazione.  

Per inizializzare lo strumento di valutazione dei criteri **What If**, impostare:

- **Isabella Simonsen** come utente
- **Gestione di Microsoft Azure** come app cloud

Fare clic su **What If** per creare un report di simulazione che indica:

- **Require TOU for Isabella** (Richiedi condizioni per l'utilizzo per Isabella) in **Criteri applicabili**
- **My TOU** (Condizioni per l'utilizzo personali) per **Controlli di concessione**.

![Strumento per i criteri What If](./media/require-tou/79.png)

**Per valutare i criteri di accesso condizionale:**

1. Nella pagina [Accesso condizionale - Criteri](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) fare clic su **What If** nel menu in alto.  

   ![What If](./media/require-tou/14.png)

1. Fare clic su **Utenti**, scegliere **Isabella Simonsen** e quindi fare clic su **Seleziona**.

   ![Utente](./media/require-tou/15.png)

1. Per selezionare un'app cloud:

   ![App cloud](./media/require-tou/16.png)

   1. Fare clic su **App cloud**.
   1. Nella pagina **App cloud** fare clic su **Selezionare le app**.
   1. Fare clic su **Seleziona**.
   1. Nella pagina **Seleziona** selezionare **Gestione di Microsoft Azure** e quindi fare clic su **Seleziona**.
   1. Nella pagina App cloud fare clic su **Fine**.
1. Fare clic su **What If**.

## <a name="test-your-conditional-access-policy"></a>Testare i criteri di accesso condizionale

Nella sezione precedente si è appreso come valutare un accesso simulato. Oltre a una simulazione, è anche consigliabile testare i criteri di accesso condizionale per assicurarsi che funzionino come previsto.

Per testare i criteri, provare ad accedere al [portale di Azure](https://portal.azure.com) usando l'account di test **Isabella Simonsen**. Verrà visualizzata una finestra di dialogo che richiede di accettare le condizioni per l'utilizzo.

![Condizioni per l'utilizzo](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, eliminare l'utente di test e i criteri di accesso condizionale:

- Se non si conosce la procedura per eliminare un utente di Azure AD, vedere [Eliminare gli utenti da Azure Active Directory](../fundamentals/add-users-azure-active-directory.md#delete-a-user).
- Per eliminare i criteri, selezionarli e quindi fare clic su **Elimina** nella barra di accesso rapido.

    ![Autenticazione a più fattori](./media/require-tou/33.png)

- Per eliminare le condizioni per l'utilizzo, selezionarle e quindi fare clic su **Elimina le condizioni** sulla barra degli strumenti nella parte superiore.

    ![Autenticazione a più fattori](./media/require-tou/29.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Richiedere Multi-Factor Authentication per app specifiche](app-based-mfa.md)
