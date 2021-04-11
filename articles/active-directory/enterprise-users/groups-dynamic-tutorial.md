---
title: 'Esercitazione: Aggiungere utenti a un gruppo dinamico - Azure AD | Microsoft Docs'
description: In questa esercitazione vengono usati gruppi con regole di appartenenza utente per aggiungere o rimuovere utenti automaticamente
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d498fb4efb3de316981963ee9606255e5b1450a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056964"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Esercitazione: Aggiungere o rimuovere membri di un gruppo automaticamente

In Azure Active Directory (Azure AD) è possibile aggiungere o rimuovere automaticamente utenti in gruppi di sicurezza o gruppi di Microsoft 365, per non doverlo fare sempre manualmente. Ogni volta che una o più proprietà di un utente o un dispositivo cambiano, Azure AD valuta tutte le regole del gruppo dinamico nell'organizzazione di Azure AD per determinare se la modifica comporta l'aggiunta o la rimozione di membri.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Creare un gruppo di utenti guest popolato automaticamente da una società partner
> * Assegnare licenze al gruppo per le funzionalità specifiche del partner per l'accesso degli utenti guess
> * Vantaggio: proteggere il gruppo **Tutti gli utenti** rimuovendo gli utenti guest, ad esempio, in modo da poter concedere agli utenti membro accesso a siti solo interni

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Questa funzionalità richiede una licenza di Azure AD Premium per l'amministratore globale dell'organizzazione. Se non si possiede questa licenza, in Azure AD selezionare **Licenze** > **Prodotti** > **Prova/Acquista**.

Non è necessario assegnare licenze agli utenti perché possano essere membri di gruppi dinamici. Per poter gestire tali utenti, è sufficiente il numero minimo di licenze di Azure AD Premium P1 disponibili nell'organizzazione. 

## <a name="create-a-group-of-guest-users"></a>Creare un gruppo di utenti guest

Prima di tutto, è necessario creare un gruppo per gli utenti guest che fanno tutti parte di un'unica società partner. Poiché per questi utenti sono necessarie licenze speciali, è spesso più pratico creare un gruppo a questo scopo.

1. Accedere al portale di Azure (https://portal.azure.com) ) con un account di amministratore globale per l'organizzazione.
2. Selezionare **Azure Active Directory** > **Gruppi** > **Nuovo gruppo**.
   ![selezionare il comando per avviare un nuovo gruppo](./media/groups-dynamic-tutorial/new-group.png)
3. Nel pannello **Gruppo**:
  
   * Selezionare **Sicurezza** come tipo di gruppo.
   * Immettere `Guest users Contoso` come nome e descrizione per il gruppo.
   * Modificare **Tipo di appartenenza** in **Utente dinamico**.
   
4. Selezionare **Proprietari** e nel pannello **Aggiungi proprietari** cercare i proprietari desiderati. Fare clic sui proprietari desiderati da aggiungere alla selezione.
5. Fare clic su **Seleziona** per chiudere il pannello **Aggiungi proprietari**.  
6. Selezionare **Modifica query dinamica** nella casella **Membri utente dinamico**.
7. Nel pannello **Regole di appartenenza dinamica**:

   * Nel campo **Proprietà** fare clic sul valore esistente e selezionare **userType**. 
   * Verificare che nel campo **Operatore** sia selezionata l'opzione **È uguale a**.  
   * Selezionare il campo **Valore** e immettere **Guest**. 
   * Fare clic sul collegamento ipertestuale **Aggiungi un'espressione** per aggiungere un'altra riga.
   * Nel campo **E/O** selezionare **E**.
   * Nel campo **Proprietà** selezionare **companyName**.
   * Verificare che nel campo **Operatore** sia selezionata l'opzione **È uguale a**.
   * Nel campo **Valore** immettere **Contoso**.
   * Fare clic su **Salva** per chiudere il pannello **Regole di appartenenza dinamica**.
   
8. Nel pannello **Gruppo** selezionare **Crea** per creare il gruppo.

## <a name="assign-licenses"></a>Assegnare licenze

Dopo aver creato il nuovo gruppo, è possibile applicare le licenze necessarie per questi utenti partner.

1. In Azure AD selezionare **Licenze**, selezionare una o più licenze e quindi fare clic su **Assegna**.
2. Selezionare **Utenti e gruppi**, selezionare il gruppo **Guest users Contoso** e quindi salvare le modifiche.
3. **Opzioni di assegnazione** permette di attivare o disattivare i piani di servizio inclusi nelle licenze selezionate. Quando si apporta una modifica, assicurarsi di fare clic su **OK** per salvarla.
4. Per completare l'assegnazione, fare clic su **Assegna** nella parte inferiore del riquadro **Assegna licenza**.

## <a name="remove-guests-from-all-users-group"></a>Rimuovere utenti guest dal gruppo Tutti gli utenti

In alcuni casi, il piano amministrativo finale può prevedere l'assegnazione di tutti gli utenti guest ai rispettivi gruppi per società. È anche possibile modificare il gruppo **Tutti gli utenti** in modo che sia riservato solo agli utenti membro nell'organizzazione. È quindi possibile usarlo per assegnare app e licenze specifiche dell'organizzazione principale.

   ![Modificare il gruppo Tutti gli utenti per limitarlo solo agli utenti membro](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Pulire le risorse

**Per rimuovere il gruppo degli utenti guest**

1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per l'organizzazione.
2. Selezionare **Azure Active Directory** > **Gruppi**. Selezionare il gruppo **Guest users Contoso**, selezionare i puntini di sospensione (...) e quindi **Elimina**. Quando il gruppo viene eliminato, vengono rimosse tutte le licenze assegnate.

**Per ripristinare il gruppo Tutti gli utenti**
1. Selezionare **Azure Active Directory** > **Gruppi**. Selezionare il nome del gruppo **Tutti gli utenti** per aprire il gruppo.
1. Selezionare **Regole di appartenenza dinamica**, cancellare tutto il testo nella regola e selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:
> [!div class="checklist"]
> * Creare un gruppo di utenti guest
> * Assegnare licenze al nuovo gruppo
> * Modificare il gruppo Tutti gli utenti per limitarlo solo agli utenti membro

Passare all'articolo successivo per altre informazioni di base sulle licenze basate su gruppo
> [!div class="nextstepaction"]
> [Nozioni di base sulle licenze basate su gruppo](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



