---
title: Correzioni per l'errore "non è possibile ottenere da qui"-Azure AD
description: Individuare le possibili correzioni per i motivi per cui si riceve il messaggio di errore "non è possibile ottenerlo da qui".
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 2484de4e554d16ba049d206981a44654ede28a3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77190033"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Possibili correzioni per il messaggio di errore "non è possibile ottenere da qui"

In fase di accesso ai servizi o alle app Web interne dell'organizzazione potrebbe essere visualizzato il messaggio d'errore **Non è possibile accedervi da qui**. Questo messaggio significa che l'organizzazione ha applicato criteri che impediscono al dispositivo di accedere alle risorse dell'organizzazione. Di seguito sono riportate alcune operazioni che è possibile provare, anche se probabilmente si dovrà contattare il supporto tecnico per risolvere il problema.

## <a name="make-sure-youre-using-a-supported-browser"></a>Assicurarsi di usare un browser supportato
Se viene visualizzato il messaggio d'errore **Non è possibile accedervi da qui**, indicante che si sta tentando di accedere ai siti dell'organizzazione da un browser non supportato, controllare quale browser si sta usando.

![Messaggio d'errore relativo al supporto browser](media/user-help-device-remediation/browser-version.png)

Per risolvere il problema è necessario installare ed eseguire un browser supportato, in base al sistema operativo. Se si usa Windows 10, fra i browser supportati ci sono Microsoft Edge, Internet Explorer e Google Chrome. Se si usa un sistema operativo diverso, controllare l'elenco completo dei [browser supportati](../conditional-access/concept-conditional-access-conditions.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Assicurarsi che il sistema operativo in uso sia supportato
Assicurarsi che la versione del sistema operativo in uso sia supportata. Fra le versioni supportate ci sono le seguenti:

- **Client Windows.** Windows 7 o versione successiva.

- **Windows Server.** Windows Server 2008 R2 o versione successiva.

- **MacOS.** macOS X o versione successiva

- **Android e iOS.** Versione più recente dei sistemi operativi per dispositivi mobili Android e iOS

Per risolvere il problema è necessario installare ed eseguire un sistema operativo supportato.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Assicurarsi che il dispositivo sia collegato alla rete
Se viene visualizzato il messaggio d'errore **Non è possibile accedervi da qui**, indicante che il dispositivo non è conforme ai criteri di accesso dell'organizzazione, assicurarsi che il dispositivo sia aggiunto alla rete dell'organizzazione.

![Messaggio d'errore relativo alla presenza o meno sulla rete](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Per controllare se il dispositivo è aggiunto alla rete
1. Eseguire l'accesso a Windows usando l'account aziendale o dell'istituto di istruzione. Ad esempio: alain@contoso.com.

2. Connettersi alla rete dell'organizzazione tramite una rete privata virtuale (VPN) o DirectAccess.

3. Dopo aver stabilito la connessione, premere il **tasto WINDOWS+L** per bloccare il dispositivo.

4. Sbloccare il dispositivo usando l'account aziendale o dell'istituto di istruzione e quindi tentare di nuovo di accedere all'app o al servizio problematico.

    Se viene visualizzato di nuovo il messaggio d'errore **Non è possibile accedervi da qui**, selezionare il collegamento **Altri dettagli** e quindi contattare il supporto tecnico con tutte le informazioni a portata di mano.

### <a name="to-join-your-device-to-your-network"></a>Per aggiungere il dispositivo alla rete
Se il dispositivo non è stato aggiunto alla rete dell'organizzazione, è possibile fare una delle due cose seguenti:

- **Aggiungere il dispositivo di lavoro.** Aggiungere il dispositivo Windows 10 di proprietà dell'azienda alla rete dell'organizzazione in modo da poter accedere a risorse potenzialmente limitate. Per altre informazioni e istruzioni dettagliate, vedere [Aggiungere il dispositivo aziendale alla rete dell'organizzazione](user-help-join-device-on-network.md).

- **Registrare il dispositivo personale per il lavoro.** Registrare il dispositivo personale, in genere un telefono o tablet, nella rete dell'organizzazione. Dopo aver registrato il dispositivo, esso potrà accedere alle risorse limitate della propria organizzazione. Per altre informazioni e istruzioni dettagliate, vedere [Registrare il dispositivo personale nella rete dell'organizzazione](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Passaggi successivi
- [Che cos'è il portale App personali?](active-directory-saas-access-panel-introduction.md)

- [Accedere con il telefono, non con la password](user-help-auth-app-sign-in.md)
