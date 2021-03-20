---
title: 'Azure AD Connect: introduzione alle impostazioni rapide | Documentazione Microsoft'
description: Informazioni su come scaricare, installare e configurare la procedura guidata per Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.assetid: b6ce45fd-554d-4f4d-95d1-47996d561c9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a655f355bb77d937f4daff2f8987769416ebd8c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279670"
---
# <a name="getting-started-with-azure-ad-connect-using-express-settings"></a>Introduzione alle impostazioni rapide per Azure AD Connect
La funzione **Impostazioni rapide** di Azure AD Connect viene usata quando è presente una topologia a singola foresta e si usa la [sincronizzazione dell'hash delle password](how-to-connect-password-hash-synchronization.md) per l'autenticazione. **Impostazioni rapide** è l'opzione predefinita e viene usata nello scenario distribuito più comune. Questa opzione richiede solo pochi clic per estendere la directory locale nel cloud.

Prima di avviare l'installazione di Azure AD Connect, assicurarsi di [scaricare Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) e completare i passaggi obbligatori illustrati in [Azure AD Connect: hardware e prerequisiti](how-to-connect-install-prerequisites.md).

Se le impostazioni rapide non corrispondono alla topologia, vedere la [documentazione correlata](#related-documentation) per altri scenari.

## <a name="express-installation-of-azure-ad-connect"></a>Installazione rapida di Azure AD Connect
È possibile visualizzare questi passaggi in azione nella sezione [Video](#videos) .

1. Accedere come amministratore locale al server in cui si vuole installare Azure AD Connect. Questa operazione deve essere eseguita sul server da impostare come server di sincronizzazione.
2. Trovare il file **AzureADConnect.msi** e farvi doppio clic.
3. Nella schermata iniziale, selezionare la casella che consente di accettare le condizioni di licenza e fare clic su **Continua**.  
4. Nella schermata Impostazioni rapide fare clic su **Usa impostazioni rapide**.  
   ![Avvio di Azure AD Connect](./media/how-to-connect-install-express/express.png)
5. Nella schermata Connessione ad Azure AD immettere il nome utente e la password di un amministratore globale per Azure AD. Fare clic su **Avanti**.  
   ![Connessione ad Azure AD](./media/how-to-connect-install-express/connectaad.png)  
   Se viene visualizzato un errore e si hanno problemi di connettività, vedere [Risolvere i problemi di connettività](tshoot-connect-connectivity.md).
6. Nella schermata Connessione a Servizi di dominio Active Directory immettere il nome utente e la password di un account amministratore dell'organizzazione. È possibile immettere la parte relativa al dominio in formato NetBios o FQDN, ad esempio FABRIKAM\administrator o fabrikam.com\administrator. Fare clic su **Avanti**.  
   ![Connessione ad AD DS](./media/how-to-connect-install-express/connectad.png)
7. La pagina [**Configurazione dell'accesso ad Azure AD**](plan-connect-user-signin.md#azure-ad-sign-in-configuration) viene visualizzata solo se non è stata completata la [verifica dei domini](../fundamentals/add-custom-domain.md) nei [prerequisiti](how-to-connect-install-prerequisites.md).
   ![Domini non verificati](./media/how-to-connect-install-express/unverifieddomain.png)  
   Se viene visualizzata questa pagina, verificare ogni dominio contrassegnato come **Non aggiunto** e **Non verificato**. Assicurarsi che i domini usati siano stati verificati in Azure AD. Fare clic sul simbolo Aggiorna dopo avere verificato tutti i domini.
8. Nella schermata Pronto per la configurazione fare clic su **Installa**.
   * Nella pagina Pronto per la configurazione è possibile deselezionare la casella di controllo **Start the synchronization process as soon as configuration completes** (Avvia il processo di sincronizzazione non appena viene completata la configurazione). È necessario deselezionare questa casella di controllo per eseguire una configurazione aggiuntiva, ad esempio il [filtro](how-to-connect-sync-configure-filtering.md). Se si deseleziona questa opzione, la procedura guidata configura la sincronizzazione ma lascia disabilitata l'utilità di pianificazione, che non viene eseguita finché non la si abilita manualmente [eseguendo una seconda volta l'installazione guidata](how-to-connect-installation-wizard.md).
   * Lasciando la casella di controllo **Avvia il processo di sincronizzazione al termine della configurazione** selezionata, verrà immediatamente attivata una sincronizzazione completa con Azure AD di tutti gli utenti, i gruppi e i contatti.
   * Se Exchange è presente nell'istanza locale di Active Directory, è disponibile anche un'opzione per abilitare la [**distribuzione ibrida di Exchange**](/exchange/exchange-hybrid). Abilitare questa opzione se si prevede di avere cassette postali di Exchange contemporaneamente nel cloud e in locale.
     ![Pronto per la configurazione di Azure AD Connect](./media/how-to-connect-install-express/readytoconfigure.png)
9. Al termine dell'installazione, fare clic su **Esci**.
10. Al termine dell'installazione, disconnettersi e accedere nuovamente prima di usare Synchronization Service Manager o Synchronization Rules Editor.

## <a name="videos"></a>Video
Per un video sull'uso dell'installazione rapida, vedere:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Express-Settings/player]
>
>

## <a name="next-steps"></a>Passaggi successivi
Dopo aver installato Azure AD Connect è possibile [verificare l'installazione e assegnare le licenze](how-to-connect-post-installation.md).

Altre informazioni su queste funzionalità che sono state abilitate con l'installazione: [Aggiornamento automatico](how-to-connect-install-automatic-upgrade.md), [Impedire eliminazioni accidentali](how-to-connect-sync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](how-to-connect-health-sync.md).

Altre informazioni su questi argomenti comuni: [utilità di pianificazione e come attivare la sincronizzazione](how-to-connect-sync-feature-scheduler.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).

## <a name="related-documentation"></a>Documentazione correlata

| Argomento | Collegamento |
| --- | --- |
| Panoramica di Azure AD Connect | [Integrare le directory locali con Azure Active Directory](whatis-hybrid-identity.md)
| Eseguire l'installazione con le impostazioni personalizzate | [Installazione personalizzata di Azure AD Connect](how-to-connect-install-custom.md) |
| Aggiornamento da DirSync | [Aggiornamento dallo strumento di sincronizzazione di Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md)|
| Account usati per l'installazione | [Ulteriori informazioni sulle credenziale e le autorizzazioni di Azure AD Connect](reference-connect-accounts-permissions.md) |