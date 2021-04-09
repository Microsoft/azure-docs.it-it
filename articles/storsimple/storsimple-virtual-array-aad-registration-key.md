---
title: Nuova autenticazione per gli array virtuali StorSimple
description: Informazioni sull'autenticazione di AAD, la nuova chiave di registrazione del servizio associata e le modifiche alle regole del firewall applicabili ai dispositivi StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 75332498ac59dc46a7a079eff4c25e02b2a6cb9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98986938"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Usare la nuova autenticazione per StorSimple

## <a name="overview"></a>Panoramica

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Il servizio Gestione dispositivi StorSimple viene eseguito in Microsoft Azure ed è connesso a più array virtuali StorSimple. Finora il servizio Gestione dispositivi di StorSimple ha usato un Servizio di controllo di accesso per autenticare il servizio nel dispositivo StorSimple. Il meccanismo di Servizio di controllo di accesso verrà presto deprecato e sostituito da un'autenticazione di Azure Active Directory.

Le informazioni contenute in questo articolo si applicano solo a entrambi gli arrays virtuali StorSimple serie 1200. Questo articolo descrive in dettaglio l'autenticazione di Azure Active Directory, la nuova chiave di registrazione del servizio associata e le modifiche alle regole del firewall applicabili ai dispositivi StorSimple.

L'autenticazione di Azure Active Directory si verifica negli array virtuali StorSimple modello 1200 che eseguono l'aggiornamento 1 o una versione successiva.

A seguito dell'introduzione dell'autenticazione di Azure Active Directory saranno modificati gli elementi seguenti:

- Modelli URL per le regole del firewall.
- Chiave di registrazione del servizio.

Queste modifiche sono descritte dettagliatamente nelle sezioni seguenti.

## <a name="url-changes-for-aad-authentication"></a>Modifiche all'URL per l'autenticazione di Azure Active Directory

Per assicurarsi che il servizio usi l'autenticazione basata su Azure Active Directory, tutti gli utenti devono includere i nuovi URL di autenticazione nelle regole del firewall.

Se si usa l'array virtuale StorSimple, verificare che l'URL seguente sia incluso nelle regole del firewall:

| Modello URL                         | Cloud | Componente/funzionalità         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |Servizio di autenticazione di Azure Active Directory      |
| `https://login.microsoftonline.us` | US Gov |Servizio di autenticazione di Azure Active Directory      |

Per un elenco completo dei modelli URL per gli array virtuali StorSimple, passare a [Modelli URL per le regole del firewall](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Se l'URL di autenticazione non viene incluso nelle regole del firewall oltre la data di deprecazione, viene visualizzato un avviso critico per informare l'utente che non è stato possibile autenticare il dispositivo StorSimple con il servizio. Il servizio non sarà in grado di comunicare con il dispositivo. Se viene visualizzato questo avviso, è necessario includere il nuovo URL di autenticazione. Per altre informazioni sull'avviso, visitare [Usare gli avvisi per monitorare il dispositivo StorSimple](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Versione del dispositivo e modifiche all'autenticazione

Se si usa un array virtuale StorSimple, usare la tabella seguente per determinare l'azione da eseguire in base alla versione del software in esecuzione sul dispositivo.

| Versione in esecuzione  | Azione da eseguire                                    |
|----------------------------|--------------------------------------------------------------|
| Aggiornamento 1.0 o versione successiva e dispositivo offline. <br> Viene visualizzato un avviso che indica che l'URL non è allowlisted.| 1. modificare le regole del firewall in modo da includere l'URL di autenticazione. Vedere gli [URL di autenticazione](#url-changes-for-aad-authentication). <br> 2. [ottenere la chiave di registrazione di AAD dal servizio](#aad-based-registration-keys). <br> 3. eseguire i passaggi 1-5 per [connettersi all'interfaccia di Windows PowerShell dell'array virtuale](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. utilizzare `Invoke-HcsReRegister` il cmdlet per registrare il dispositivo tramite Windows PowerShell. Inserire la chiave ottenuta nel passaggio precedente.|
| Aggiornamento 1.0 o versione successiva e dispositivo online.| Non è richiesta alcuna azione.                                       |
| Aggiornamento 0.6 o versione precedente e dispositivo offline. | 1. [scaricare l'aggiornamento 1,0 tramite il server di catalogo](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [applicare l'aggiornamento 1,0 tramite l'interfaccia utente Web locale](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [ottenere la chiave di registrazione di AAD dal servizio](#aad-based-registration-keys). <br>4. eseguire i passaggi 1-5 per [connettersi all'interfaccia di Windows PowerShell dell'array virtuale](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. utilizzare `Invoke-HcsReRegister` il cmdlet per registrare il dispositivo tramite Windows PowerShell. Inserire la chiave ottenuta nel passaggio precedente.|
| Aggiornamento 0.6 o versione precedente e dispositivo online | Modificare le regole del firewall per includere l'URL di autenticazione.<br> Installare l'aggiornamento 1.0 tramite il portale di Azure. |

## <a name="aad-based-registration-keys"></a>Chiavi di registrazione basate su Azure Active Directory

A partire dall'aggiornamento 1.0 per gli array virtuali StorSimple, vengono usate nuove chiavi di registrazione basate su Azure Active Directory. Le chiavi di registrazione servono per registrare il servizio Gestione dispositivi di StorSimple con il dispositivo.

Se si usa un array virtuale StorSimple che esegue l'aggiornamento 0.6 o una versione precedente, non è possibile usare le nuove chiavi di registrazione del servizio Azure Active Directory. È necessario rigenerare la chiave di registrazione del servizio. Dopo averla generata la nuova chiave viene usata per registrare tutti i dispositivi successivi. La chiave precedente non è più valida.

- La nuova chiave di registrazione di Azure Active Directory scade dopo 3 giorni.
- Le chiavi di registrazione di Azure Active Directory funzionano solo con gli array virtuali StorSimple serie 1200 che eseguono l'aggiornamento 1 o una versione successiva. La chiave di registrazione di Azure Active Directory di un dispositivo StorSimple serie 8000 non funzionerà.
- Le chiavi di registrazione di Azure Active Directory sono più lunghe delle chiavi di registrazione ACS corrispondenti.

Per generare una chiave di registrazione del servizio Azure Active Directory, attenersi alla procedura seguente.

#### <a name="to-generate-the-aad-service-registration-key"></a>Per generare la chiave di registrazione del servizio Azure Active Directory

1. In **Gestione dispositivi StorSimple** passare a **Gestione &gt;** **Chiavi**.
    
    ![Passare alle chiavi](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Fare clic su **Genera chiave**.

    ![Fare clic su Rigenera](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Copiare la nuova chiave. La chiave precedente non funziona più.

    ![Confermare la rigenerazione](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni su come distribuire l'[array virtuale StorSimple](storsimple-virtual-array-deploy1-portal-prep.md)
