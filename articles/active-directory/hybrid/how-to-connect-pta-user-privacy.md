---
title: Privacy dell'utente e autenticazione pass-through di Azure Active Directory | Microsoft Docs
description: Questo articolo riguarda l'autenticazione pass-through di Azure Active Directory (Azure AD) e la conformità al Regolamento generale sulla protezione dei dati (GDPR).
services: active-directory
keywords: Autenticazione pass-through di Azure AD Connect, GDPR, componenti necessari per Azure AD, SSO, Single Sign-On
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0af1c42e7e2c163e7f9e7407d0236e35bfacf8e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76931017"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Privacy dell'utente e autenticazione pass-through di Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Panoramica

L'autenticazione pass-through di Azure AD crea il seguente tipo di log, che può contenere informazioni personali:

- File di log di traccia di Azure AD Connect.
- File di log di traccia dell'agente di autenticazione.
- File di log di eventi di Windows.

Migliorare la privacy dell'utente per l'autenticazione pass-through in due modi:

1.  Qualora richiesto, estrarre i dati per un utente e rimuovere i dati di tale utente dalle installazioni.
2.  Verificare che nessun dato venga conservato per più di 48 ore.

La seconda opzione è consigliabile perché risulta più semplice da implementare e gestire. Vedere le istruzioni seguenti per ogni tipo di log:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Eliminare i file di log di traccia di Azure AD Connect

Controllare il contenuto della cartella **%ProgramData%\AADConnect** ed eliminare il contenuto del log di traccia (file **trace-\*.log**) di questa cartella entro 48 ore dall'installazione o dall'aggiornamento di Azure AD Connect o dalla modifica della configurazione dell'autenticazione pass-through, perché questa azione può creare dati a cui si applica il regolamento GDPR.

>[!IMPORTANT]
>Non eliminare il file **PersistedState.xml** in questa cartella, perché questo file viene usato per mantenere lo stato dell'installazione precedente di Azure AD Connect e viene usato quando viene eseguita un'installazione di aggiornamento. Questo file non conterrà mai dati relativi a un utente e non deve essere mai eliminato.

È possibile esaminare ed eliminare questi file di log di traccia tramite Esplora risorse oppure è possibile usare lo script di PowerShell seguente per eseguire le azioni necessarie:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Salvare lo script in un file con estensione "PS1". Eseguire questo script quando necessario.

Per altre informazioni sui requisiti GDPR per Azure AD Connect correlati, vedere [questo articolo](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Eliminare i log eventi dell'agente di autenticazione

Questo prodotto potrebbe anche creare **log eventi di Windows**. Per altre informazioni, vedere [questo articolo](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx).

Per visualizzare i log correlati all'agente di autenticazione pass-through, aprire l'applicazione **Visualizzatore eventi** nel server e controllare in **Registri applicazioni e servizi\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Eliminare i file di log di traccia dell'agente di autenticazione

È necessario controllare regolarmente il contenuto di **%ProgramData%** Microsoft Azure AD Connect Authentication Agent Trace ed eliminare il contenuto di questa cartella ogni 48 ore. 

>[!IMPORTANT]
>Se il servizio agente di autenticazione è in esecuzione, non sarà possibile eliminare il file di log corrente nella cartella. Arrestare il servizio prima di riprovare. Per evitare errori di accesso dell'utente, l'autenticazione pass-through deve essere già configurata per la [disponibilità elevata](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

È possibile esaminare ed eliminare questi file tramite Esplora risorse oppure è possibile usare lo script seguente per eseguire le azioni necessarie:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Per pianificare l'esecuzione di questo script ogni 48 ore, seguire questa procedura:

1.  Salvare lo script in un file con estensione "PS1".
2.  Aprire **Pannello di controllo** e fare clic su **Sistema e sicurezza**.
3.  Sotto **l'intestazione Strumenti di amministrazione,** fare clic su "**Pianifica attività**".
4.  In **Utilità di pianificazione** fare clic con il pulsante destro del mouse su **Libreria Utilità di pianificazione** e scegliere **Crea attività di base**.
5.  Immettere il nome per la nuova attività e fare clic su **Avanti**.
6.  Selezionare **Ogni giorno** per **Attivazione** e fare clic su **Avanti**.
7.  Impostare la ricorrenza su due giorni e fare clic su **Avanti**.
8.  Selezionare **Avvio programma** come azione e fare clic su **Avanti**.
9.  Digitare "**PowerShell**" nella casella del programma/script e nella casella **"Aggiungi argomenti (facoltativo)**", immettere il percorso completo dello script creato in precedenza, quindi fare clic su **Avanti**.
10. La schermata successiva mostra un riepilogo dell'attività che si sta per creare. Verificare i valori e fare clic su **Fine** per creare l'attività:
 
### <a name="note-about-domain-controller-logs"></a>Nota sui log del controller di dominio

Se è abilitata la registrazione di controllo, questo prodotto può generare log di sicurezza per i controller di dominio. Per altre informazioni sulla configurazione dei criteri di controllo, leggere questo [articolo](https://technet.microsoft.com/library/dd277403.aspx).

## <a name="next-steps"></a>Passaggi successivi
* [Consultare l'Informativa sulla privacy Microsoft nel Trust Center](https://www.microsoft.com/trustcenter)
* [**Risoluzione dei problemi:**](tshoot-connect-pass-through-authentication.md) informazioni su come risolvere i problemi comuni relativi alla funzionalità.
