---
title: Viene eseguito il provisioning di un set errato di utenti in un'app della raccolta Azure AD
description: Informazioni su come individuare i motivi per cui è in corso il provisioning di un set di utenti per un'applicazione diverso da quello previsto
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c94388011605da73666e82011bb8ef56d2af8d30
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77522782"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>È in corso il provisioning di un set errato di utenti per un'applicazione della raccolta di Azure AD

Il provisioning degli utenti dell'app è principalmente basato su quali utenti e gruppi sono stati **assegnati** all'applicazione.

Usare le risorse seguenti per informazioni su come verificare quali utenti e gruppi sono stati assegnati a un'applicazione con Azure Active Directory.

## <a name="assign-a-user-directly-as-an-administrator"></a>Assegnare un utente direttamente come amministratore

Per assegnare uno o più utenti direttamente a un'applicazione, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se non viene visualizzata l'applicazione che si vuole visualizzare qui, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni.**

6. Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7. Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di spostamento di sinistra dell'applicazione.

8. Per aprire il riquadro **Aggiungi assegnazione**, fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi**.

9. Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

10. Nella casella di ricerca **Cerca per nome o indirizzo di posta** digitare il **nome completo ** o l'**indirizzo di posta elettronica** dell'utente oggetto dell'assegnazione.

11. Posizionare il puntatore del mouse sull' **utente** nell'elenco per visualizzare una **casella**di controllo. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **Selezionato**.

12. **Facoltativo:** se si vuole **aggiungere più di un utente**, digitare un altro **nome completo** o **indirizzo di posta elettronica** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere l'utente all'elenco **selezionato**.

13. Dopo avere selezionato gli utenti, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per scegliere un ruolo da assegnare agli utenti selezionati.

15. Fare clic sul pulsante **assegna** per assegnare l'applicazione agli utenti selezionati.

Se il provisioning è configurato e già in esecuzione per un'app, il provisioning di nuovi utenti per un'applicazione dovrebbe essere eseguito dopo circa 10 minuti. Controllare i **log di controllo** per informazioni dettagliate.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Assegnare un gruppo direttamente a un'applicazione come amministratore

Per assegnare uno o più gruppi direttamente a un'applicazione, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se non viene visualizzata l'applicazione che si vuole visualizzare qui, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni.**

6. Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7. Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di spostamento di sinistra dell'applicazione.

8. Per aprire il riquadro **Aggiungi assegnazione**, fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi**.

9. Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

10. Nella casella di ricerca **Cerca per nome o indirizzo di posta** digitare il **nome completo del gruppo** a cui si vuole eseguire l'assegnazione.

11. Posizionare il puntatore sul **gruppo** nell'elenco per visualizzare una **casella di controllo**. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo del gruppo per aggiungere il gruppo all'elenco **selezionato**.

12. **Facoltativo:** se si vuole **aggiungere più di un gruppo**, digitare un altro **nome completo di gruppo** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere il gruppo all'elenco **selezionato**.

13. Dopo avere selezionato i gruppi, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per scegliere un ruolo da assegnare ai gruppi selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione ai gruppi selezionati.

Se il provisioning è configurato e già in esecuzione per un'app, il provisioning dei nuovi utenti inclusi nel gruppo dovrebbe essere eseguito per un'applicazione dopo circa 10 minuti. Controllare i **log di controllo** per informazioni dettagliate.

>[!IMPORTANT]
>Eseguire il provisioning dei dettagli del gruppo e del nome del gruppo, oltre ai membri, se supportato per alcune applicazioni. È possibile abilitare o disabilitare questa funzionalità abilitando o disabilitando il **mapping** per gli oggetti di gruppo visualizzati nella scheda **Provisioning**. 
>
>

Se il provisioning di gruppi è abilitato, verificare di controllare i mapping degli attributi per garantire che venga usato un campo appropriato per l'ID di abbinamento. Questo ID di abbinamento può essere il nome visualizzato o l'alias di posta elettronica. Il provisioning di questo gruppo e dei relativi membri non viene effettuato se la proprietà corrispondente è vuota o non popolata per un gruppo in Azure AD.

## <a name="next-steps"></a>Passaggi successivi
[Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](user-provisioning.md)
