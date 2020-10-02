---
title: Autenticazione Azure AD per StorSimple 8000 in Device Manager
description: Spiega come usare l'autenticazione basata su Azure Active Directory per il servizio, generare una nuova chiave di registrazione ed eseguire la registrazione manuale dei dispositivi.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 0e071a20051f047efbd040dfc01a30e3c1381367
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631325"
---
# <a name="use-azuer-active-directory-ad-authentication-for-your-storsimple"></a>Usare l'autenticazione di Azure Active Directory (AD) per StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Panoramica

Il servizio Gestione dispositivi StorSimple viene eseguito in Microsoft Azure e si connette a più dispositivi StorSimple. Finora il servizio Gestione dispositivi di StorSimple ha usato un Servizio di controllo di accesso per autenticare il servizio nel dispositivo StorSimple. Il meccanismo di Servizio di controllo di accesso verrà presto deprecato e sostituito da un'autenticazione di Azure Active Directory. Per altre informazioni, vedere gli annunci seguenti relativi alla deprecazione di ACS e all'uso dell'autenticazione di AAD.

- [The future of Azure ACS is Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/) (Il futuro di Servizio di controllo di accesso di Azure è Azure Active Directory)
- [Upcoming changes to the Microsoft Access Control Service](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/) (Modifiche imminenti al Servizio di controllo di accesso Microsoft)

Questo articolo descrive in dettaglio l'autenticazione di Azure Active Directory, la nuova chiave di registrazione del servizio associata e le modifiche alle regole del firewall applicabili ai dispositivi StorSimple. Le informazioni contenute in questo articolo si applicano solo ai dispositivi StorSimple serie 8000.

L'autenticazione di AAD si verifica nei dispositivi StorSimple serie 8000 che eseguono l'aggiornamento 5 o successivo. A seguito dell'introduzione dell'autenticazione di Azure Active Directory saranno modificati gli elementi seguenti:

- Modelli URL per le regole del firewall.
- Chiave di registrazione del servizio.

Queste modifiche sono descritte dettagliatamente nelle sezioni seguenti.

## <a name="url-changes-for-aad-authentication"></a>Modifiche all'URL per l'autenticazione di Azure Active Directory

Per assicurarsi che il servizio usi l'autenticazione basata su Azure Active Directory, tutti gli utenti devono includere i nuovi URL di autenticazione nelle regole del firewall.

Se si usa StorSimple serie 8000, verificare che l'URL seguente sia incluso nelle regole del firewall:

| Modello URL                         | Cloud | Componente/funzionalità         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |Servizio di autenticazione di Azure Active Directory      |
| `https://login.microsoftonline.us` | US Gov |Servizio di autenticazione di Azure Active Directory      |

Per un elenco completo dei modelli URL per i dispositivi della serie StorSimple 8000, passare a [Modelli URL per le regole del firewall](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Se l'URL di autenticazione non è incluso nelle regole del firewall dopo la data di deprecazione e il dispositivo esegue l'aggiornamento 5, viene visualizzato un avviso relativo all'URL. In questo caso. è necessario includere il nuovo URL di autenticazione. Se il dispositivo esegue una versione precedente all'aggiornamento 5, gli utenti visualizzato un avviso relativo all'heartbeat. In entrambi i casi, il dispositivo StorSimple non può eseguire l'autenticazione con il servizio e il servizio non è in grado di comunicare con il dispositivo.

## <a name="device-version-and-authentication-changes"></a>Versione del dispositivo e modifiche all'autenticazione

Se si usa un dispositivo StorSimple serie 8000, usare la tabella seguente per determinare l'azione da eseguire in base alla versione del software in esecuzione nel dispositivo.

| Versione in esecuzione| Azione da eseguire                                    |
|--------------------------|------------------------|
| Update 5 o versione successiva e dispositivo offline. <br> Viene visualizzato un avviso che indica che l'URL non è approvato.|1. modificare le regole del firewall in modo da includere l'URL di autenticazione. Vedere gli [URL di autenticazione](#url-changes-for-aad-authentication).<br>2. [ottenere la chiave di registrazione di AAD dal servizio](#aad-based-registration-keys).<br>3. [connettersi all'interfaccia di Windows PowerShell del dispositivo StorSimple serie 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. utilizzare `Redo-DeviceRegistration` il cmdlet per registrare il dispositivo tramite Windows PowerShell. Inserire la chiave ottenuta nel passaggio precedente.|
| Update 5 o versione successiva e dispositivo online.| Non è richiesta alcuna azione.                                       |
| Update 4 o versione precedente e dispositivo offline. |1. modificare le regole del firewall in modo da includere l'URL di autenticazione.<br>2. [scaricare l'aggiornamento 5 tramite il server di catalogo](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [applicare l'aggiornamento 5 tramite il metodo hotfix](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [ottenere la chiave di registrazione di AAD dal servizio](#aad-based-registration-keys).<br>5. [connettersi all'interfaccia di Windows PowerShell del dispositivo StorSimple serie 8000](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. utilizzare `Redo-DeviceRegistration` il cmdlet per registrare il dispositivo tramite Windows PowerShell. Inserire la chiave ottenuta nel passaggio precedente.|
| Update 4 o versione precedente e dispositivo online. |Modificare le regole del firewall per includere l'URL di autenticazione.<br> Installare l'aggiornamento 5 tramite il portale di Azure.              |
| Ripristino delle impostazioni predefinite a una versione precedente all'aggiornamento 5.      |Il portale mostra una chiave di registrazione basata su AAD mentre il dispositivo esegue una versione precedenti del software. Seguire i passaggi nello scenario precedente per il caso in cui il dispositivo esegue l'aggiornamento 4 o versioni precedenti.              |

## <a name="aad-based-registration-keys"></a>Chiavi di registrazione basate su Azure Active Directory

A partire dall'aggiornamento 5 per i dispositivi StorSimple serie 8000, vengono usate nuove chiavi di registrazione basate su AAD. Le chiavi di registrazione servono per registrare il servizio Gestione dispositivi di StorSimple con il dispositivo.

Se si usa un dispositivo StorSimple serie 8000 che esegue l'aggiornamento 4 o precedente (o un dispositivo precedente che viene attivato ora), non è possibile usare le nuove chiavi di registrazione del servizio AAD.
In questo scenario, occorre rigenerare la chiave di registrazione del servizio. Dopo averla generata la nuova chiave viene usata per registrare tutti i dispositivi successivi. La chiave precedente non è più valida.

- La nuova chiave di registrazione di Azure Active Directory scade dopo 3 giorni.
- Le chiavi di registrazione di AAD funzionano solo con i dispositivi StorSimple serie 8000 che eseguono l'aggiornamento 5 o versione successiva.
- Le chiavi di registrazione di Azure Active Directory sono più lunghe delle chiavi di registrazione ACS corrispondenti.

Per generare una chiave di registrazione del servizio Azure Active Directory, attenersi alla procedura seguente.

#### <a name="to-generate-the-aad-service-registration-key"></a>Per generare la chiave di registrazione del servizio Azure Active Directory

1. In **Gestione dispositivi StorSimple** passare a **Gestione &gt;** **Chiavi**. È anche possibile usare la barra di ricerca per cercare _chiavi_.
    
2. Fare clic su **Genera chiave**.

    ![Fare clic su Rigenera](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Copiare la nuova chiave. La chiave precedente non funzionerà più.

    ![Confermare la rigenerazione](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Se si sta creando un'appliance cloud StorSimple nel servizio registrato per il dispositivo StorSimple serie 8000, non generare una chiave di registrazione durante la creazione. Attendere il completamento della creazione e quindi generare la chiave di registrazione.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su come distribuire un [dispositivo StorSimple serie 8000](storsimple-8000-deployment-walkthrough-u2.md).
