---
title: Creare un'unità organizzativa (OU) in Azure AD Domain Services | Microsoft Docs '
description: Informazioni su come creare e gestire un'unità organizzativa (OU) personalizzata in un Azure AD Domain Services dominio gestito.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fbdfcc23553a27aaa4d7bbd4bff9c4f33cb8fd19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620019"
---
# <a name="create-an-organizational-unit-ou-in-an-azure-active-directory-domain-services-managed-domain"></a>Creare un'unità organizzativa (OU) in un dominio gestito Azure Active Directory Domain Services

Unità organizzative (OU) in un dominio gestito di Active Directory Domain Services (AD DS) consente di raggruppare logicamente oggetti quali account utente, account del servizio o account computer. È quindi possibile assegnare gli amministratori a unità organizzative specifiche e applicare criteri di gruppo per applicare le impostazioni di configurazione di destinazione.

I domini gestiti di Azure AD DS includono le due unità organizzative predefinite seguenti:

* *Computer aaddc Computers* : contiene gli oggetti computer per tutti i computer aggiunti al dominio gestito.
* *Utenti di aaddc Computers* : include utenti e gruppi sincronizzati dal tenant di Azure ad.

Durante la creazione e l'esecuzione di carichi di lavoro che utilizzano Azure AD DS, potrebbe essere necessario creare account del servizio per l'autenticazione delle applicazioni. Per organizzare questi account del servizio, è spesso necessario creare un'unità organizzativa personalizzata nel dominio gestito e quindi creare gli account del servizio all'interno di tale unità organizzativa.

In un ambiente ibrido le unità organizzative create in un ambiente di servizi di dominio Active Directory locale non vengono sincronizzate con il dominio gestito. I domini gestiti usano una struttura di unità organizzativa flat. Tutti gli account utente e i gruppi vengono archiviati nel contenitore *degli utenti di aaddc Computers* , nonostante vengano sincronizzati da domini o foreste locali diversi, anche se è stata configurata una struttura di unità organizzativa gerarchica.

Questo articolo descrive come creare una OU nel dominio gestito.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare le procedure descritte in questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione, sincronizzato con una directory locale o con una directory solo cloud.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Un dominio gestito di Azure Active Directory Domain Services abilitato e configurato nel tenant di Azure AD.
    * Se necessario, completare l'esercitazione per [creare e configurare un Azure Active Directory Domain Services dominio gestito][create-azure-ad-ds-instance].
* Una macchina virtuale di gestione di Windows Server aggiunta al dominio gestito di Azure AD DS.
    * Se necessario, completare l'esercitazione per [creare una macchina virtuale di gestione][tutorial-create-management-vm].
* Un account utente membro del gruppo di *amministratori dei controller di dominio di Azure AD* nel tenant di Azure AD.

## <a name="custom-ou-considerations-and-limitations"></a>Considerazioni e limitazioni personalizzate dell'unità organizzativa

Quando si creano unità organizzative personalizzate in un dominio gestito, si ottiene una maggiore flessibilità di gestione per la gestione degli utenti e l'applicazione di criteri di gruppo. Rispetto a un ambiente di servizi di dominio Active Directory locale, esistono alcune limitazioni e considerazioni per la creazione e la gestione di una struttura di unità organizzativa personalizzata in un dominio gestito:

* Per creare unità organizzative personalizzate, gli utenti devono essere membri del gruppo *AAD DC Administrators* .
* A un utente che crea un'unità organizzativa personalizzata vengono concessi privilegi amministrativi (controllo completo) su tale OU e il proprietario della risorsa.
    * Per impostazione predefinita, il gruppo di *amministratori di AAD DC* dispone anche del controllo completo dell'unità organizzativa personalizzata.
* Viene creata un'unità organizzativa predefinita per *gli utenti di aaddc Computers* che contiene tutti gli account utente sincronizzati dal tenant di Azure ad.
    * Non è possibile spostare gli utenti o i gruppi dalla ou *utenti di aaddc Computers* a unità organizzative personalizzate create dall'utente. Solo gli account utente o le risorse create nel dominio gestito possono essere spostati in unità organizzative personalizzate.
* Gli account utente, i gruppi, gli account di servizio e gli oggetti computer creati in unità organizzative personalizzate non sono disponibili nel tenant di Azure AD.
    * Questi oggetti non vengono visualizzati con l'API Microsoft Graph o nell'interfaccia utente di Azure AD; sono disponibili solo nel dominio gestito.

## <a name="create-a-custom-ou"></a>Creare un'unità organizzativa personalizzata

Per creare un'unità organizzativa personalizzata, usare gli strumenti di amministrazione Active Directory da una macchina virtuale aggiunta a un dominio. Il Centro di amministrazione di Active Directory consente di visualizzare, modificare e creare risorse in un dominio gestito, incluse le unità organizzative.

> [!NOTE]
> Per creare un'unità organizzativa personalizzata in un dominio gestito, è necessario eseguire l'accesso a un account utente membro del gruppo di *amministratori di AAD DC* .

1. Accedere alla macchina virtuale di gestione. Per i passaggi relativi alla modalità di connessione tramite la portale di Azure, vedere [connettersi a una macchina virtuale Windows Server][connect-windows-server-vm].
1. Dalla schermata Start selezionare strumenti di **Amministrazione**. Viene visualizzato un elenco di strumenti di gestione disponibili che sono stati installati nell'esercitazione per [creare una macchina virtuale di gestione][tutorial-create-management-vm].
1. Per creare e gestire le unità organizzative, selezionare **centro di amministrazione di Active Directory** dall'elenco di strumenti di amministrazione.
1. Nel riquadro sinistro scegliere il dominio gestito, ad esempio *aaddscontoso.com*. Viene visualizzato un elenco di unità organizzative e risorse esistenti:

    ![Selezionare il dominio gestito nella Centro di amministrazione di Active Directory](./media/create-ou/create-ou-adac-overview.png)

1. Il riquadro **attività** viene visualizzato sul lato destro del centro di amministrazione di Active Directory. Nel dominio, ad esempio *aaddscontoso.com*, selezionare **nuovo > unità organizzativa**.

    ![Selezionare l'opzione per creare una nuova unità organizzativa nel Centro di amministrazione di Active Directory](./media/create-ou/create-ou-adac-new-ou.png)

1. Nella finestra di dialogo **Crea unità organizzativa** specificare un **nome** per la nuova unità organizzativa, ad esempio *mycustomou "*. Fornire una breve descrizione per l'unità organizzativa, ad esempio un' *unità organizzativa personalizzata per gli account del servizio*. Se lo si desidera, è anche possibile impostare il campo **gestito da** per l'unità organizzativa. Per creare l'unità organizzativa personalizzata, fare clic su **OK**.

    ![Creare un'unità organizzativa personalizzata dalla Centro di amministrazione di Active Directory](./media/create-ou/create-ou-dialog.png)

1. Tornando all'Centro di amministrazione di Active Directory, l'unità organizzativa personalizzata è ora elencata ed è disponibile per l'uso:

    ![Unità organizzativa personalizzata disponibile per l'utilizzo nel Centro di amministrazione di Active Directory](./media/create-ou/create-ou-done.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo degli strumenti di amministrazione o sulla creazione e l'utilizzo di account di servizio, vedere gli articoli seguenti:

* [Centro di amministrazione di Active Directory: Introduzione](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560651(v=ws.10))
* [Guida dettagliata agli account del servizio gestiti](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm