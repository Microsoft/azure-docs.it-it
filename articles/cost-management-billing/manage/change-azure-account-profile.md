---
title: Modificare le informazioni di contatto per un account di fatturazione di Azure
description: Descrive come modificare le informazioni di contatto dell'account di fatturazione di Azure
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: 776c207387ee55b998615131baf77e6c13655b49
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033938"
---
# <a name="change-contact-information-for-an-azure-billing-account"></a>Modificare le informazioni di contatto per un account di fatturazione di Azure

Questo articolo illustra come aggiornare le informazioni di contatto per un *account di fatturazione* nel portale di Azure. Le istruzioni per aggiornare le informazioni di contatto variano in base al tipo di account di fatturazione. Per altre informazioni sugli account di fatturazione e su come identificare il tipo di account di fatturazione, vedere [Visualizzare gli account di fatturazione nel portale di Azure](view-all-accounts.md). L'account di fatturazione di Azure è distinto dall'account utente di Azure e dall'[account Microsoft](https://account.microsoft.com/).

Per aggiornare le informazioni del profilo utente di Azure Active Directory, tenere presente che solo un amministratore utenti può apportare le modifiche. Se non è stato assegnato il ruolo di amministratore utenti, contattare l'amministratore utenti. Per altre informazioni sulla modifica del profilo utente, vedere [Aggiungere o aggiornare informazioni di un profilo utente con Azure Active Directory](../../active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

*Indirizzo acquirente*: corrisponde all'indirizzo e alle informazioni di contatto dell'organizzazione o della persona responsabile di un account di fatturazione. Viene visualizzato in tutte le fatture generate per l'account di fatturazione.

*Indirizzo di fatturazione*: corrisponde all'indirizzo e alle informazioni di contatto dell'organizzazione o della persona responsabile delle fatture generate per un account di fatturazione. Per un account di fatturazione per un Programma Sottoscrizione Microsoft Online è previsto un unico indirizzo di fatturazione, visualizzato in tutte le fatture generate per l'account. Per un account di fatturazione per un Contratto del cliente Microsoft è previsto un indirizzo di fatturazione per ogni profilo di fatturazione, visualizzato nella fattura generata per il profilo di fatturazione.

*Indirizzo di posta elettronica di contatto per i messaggi di marketing e sul servizio*: è possibile specificare un indirizzo di posta elettronica diverso da quello con cui si accede per ricevere importanti notifiche relative a fatturazione, servizio e raccomandazioni sull'account Azure. I messaggi di posta elettronica di notifica del servizio, ad esempio relativi a problemi di sicurezza urgenti, variazioni di prezzo o modifiche che causano un'interruzione dei servizi usati dall'account, vengono sempre inviati all'indirizzo di accesso.

## <a name="update-an-mosp-billing-account-address"></a>Aggiornare l'indirizzo di un account di fatturazione per un Programma Sottoscrizione Microsoft Online

1. Accedere al portale di Azure usando l'indirizzo di posta elettronica con l'autorizzazione di amministratore per l'account.
1. Cercare **Gestione dei costi e fatturazione**.  
    ![Screenshot che illustra dove cercare Gestione dei costi e fatturazione nel portale di Azure](./media/change-azure-account-profile/search-cmb.png)
1. Selezionare **Proprietà** sul lato sinistro.  
    ![Screenshot che illustra le proprietà dell'account di fatturazione del Programma Sottoscrizione Microsoft Online](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. Selezionare **Aggiornare l'indirizzo di fatturazione** per aggiornare gli indirizzi dell'acquirente e di fatturazione. Immettere il nuovo indirizzo e selezionare **Salva**.  
    ![Screenshot che illustra l'indirizzo aggiornato per l'account di fatturazione del Programma Sottoscrizione Microsoft Online](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>Aggiornare l'indirizzo dell'acquirente di un account di fatturazione per un Contratto del cliente Microsoft

1. Accedere al portale di Azure usando l'indirizzo di posta elettronica con un ruolo di proprietario o di collaboratore per l'account di fatturazione per un Contratto del cliente Microsoft.
1. Cercare **Gestione dei costi e fatturazione**.  
    ![Screenshot che illustra dove eseguire la ricerca nel portale di Azure](./media/change-azure-account-profile/search-cmb.png)
1. Selezionare **Proprietà** sul lato sinistro, quindi selezionare **Aggiorna il valore di Venduto a**.  
    ![Screenshot che illustra le proprietà di un account di fatturazione del Contratto del cliente Microsoft in cui è possibile modificare l'indirizzo dell'acquirente](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. Immettere il nuovo indirizzo e selezionare **Salva**.  
    ![Screenshot che illustra l'aggiornamento dell'indirizzo dell'acquirente per un account del Contratto del cliente Microsoft](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > Per alcuni account è necessaria una verifica aggiuntiva prima di poter aggiornare l'indirizzo del destinatario. Se l'account richiede l'approvazione manuale, verrà chiesto di contattare il supporto tecnico di Azure.

## <a name="update-an-mca-billing-account-address"></a>Aggiornare l'indirizzo di un account di fatturazione per un Contratto del cliente Microsoft

1. Accedere al portale di Azure usando l'indirizzo di posta elettronica con un ruolo di proprietario o di collaboratore per un account di fatturazione o un profilo di fatturazione per un Contratto del cliente Microsoft.
1. Cercare **Gestione dei costi e fatturazione**.  
1. Selezionare **Profili di fatturazione** sul lato sinistro.
1. Selezionare un profilo di fatturazione per aggiornare l'indirizzo di fatturazione.  
    ![Screenshot che illustra la pagina Profili di fatturazione in cui è possibile selezionare un profilo di fatturazione](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. Selezionare **Proprietà** sul lato sinistro.
1. Selezionare **Aggiorna l'indirizzo**.  
    ![Screenshot che illustra dove aggiornare l'indirizzo](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. Immettere il nuovo indirizzo e selezionare **Salva**.  
    ![Screenshot che illustra l'aggiornamento dell'indirizzo](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>Messaggi di posta elettronica di servizio e di marketing

Nel portale di Azure viene chiesto di verificare o aggiornare l'indirizzo di posta elettronica ogni 90 giorni. Microsoft invia messaggi di posta elettronica a questo indirizzo con informazioni relative all'account Microsoft per:

- Notifiche del servizio
- Avvisi di sicurezza
- Scopi di fatturazione
- Supporto
- Comunicazioni di marketing
- Procedure consigliate, basate sull'utilizzo di Azure

Immettere l'indirizzo di posta elettronica a cui ricevere le comunicazioni relative all'account. Immettendo un indirizzo di posta elettronica, si acconsente esplicitamente a ricevere comunicazioni da Microsoft.

![Esempio della richiesta di aggiornamento delle informazioni di contatto](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>Cambiare l'indirizzo di posta elettronica di contatto

È possibile cambiare l'indirizzo di posta elettronica di contatto con uno dei metodi seguenti. L'aggiornamento dell'indirizzo di posta elettronica di contatto non comporta l'aggiornamento di quello usato per l'accesso.

1. Se si è un amministratore account per un Programma Sottoscrizione Microsoft Online, seguire le istruzioni riportate in [Aggiornare l'indirizzo di un account di fatturazione per un Programma Sottoscrizione Microsoft Online](#update-an-mosp-billing-account-address) e selezionare **Aggiorna le informazioni di contatto** nell'ultimo passaggio. Immettere quindi il nuovo indirizzo di posta elettronica.
1. Passare all'area [Informazioni contatto](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) nel portale di Azure e immettere il nuovo indirizzo di posta elettronica. 
1. Nel portale di Azure selezionare l'icona con le proprie iniziali o con l'immagine. Quindi selezionare il menu di scelta rapida ( **...** ). Scegliere quindi **Informazioni di contatto** dal menu e immettere il nuovo indirizzo di posta elettronica.

![Esempio di aggiornamento di un indirizzo di posta elettronica in Azure](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>Rifiutare esplicitamente di ricevere messaggi di posta elettronica di marketing

Per rifiutare esplicitamente di ricevere messaggi di posta elettronica di marketing:

1. Aprire il [modulo di richiesta](https://account.microsoft.com/profile/permissions-link-request) per inviare una richiesta tramite l'indirizzo di posta elettronica del profilo. Si riceverà un collegamento tramite posta elettronica per aggiornare le preferenze.
1. Selezionare il collegamento per aprire la pagina di **gestione delle autorizzazioni per le comunicazioni**. Questa pagina visualizza i tipi di comunicazioni di marketing che si è acconsentito esplicitamente di ricevere all'indirizzo di posta elettronica. Deselezionare le voci per cui si vuole rifiutare esplicitamente la ricezione di messaggi, quindi selezionare **Salva**.  
    ![Esempio della pagina per la gestione delle autorizzazioni per le comunicazioni](./media/change-azure-account-profile/manage-communication-permissions.png)

Quando si rifiuta esplicitamente di ricevere comunicazioni di marketing, si continuerà comunque a ricevere notifiche di servizio, in base all'account.

## <a name="update-the-email-address-that-you-sign-in-with"></a>Aggiornare l'indirizzo di posta elettronica con cui si accede

Non è possibile aggiornare l'indirizzo di posta elettronica usato per accedere all'account. Tuttavia, se si ha un account di fatturazione per un Programma Sottoscrizione Microsoft Online è possibile iscriversi per richiedere un altro account con il nuovo indirizzo di posta elettronica e trasferire la proprietà delle sottoscrizioni al nuovo account. Per un account di fatturazione per un Contratto del cliente Microsoft, è possibile assegnare le autorizzazioni per il nuovo indirizzo di posta elettronica per l'account.

## <a name="update-your-credit-card"></a>Aggiornare la carta di credito

Per informazioni su come aggiornare la carta di credito, vedere [Cambiare la carta di credito usata per il pagamento delle sottoscrizioni di Azure](change-credit-card.md).

## <a name="update-your-country-or-region"></a>Aggiornare il paese o l'area geografica

Non è possibile cambiare il paese o l'area geografica per un account esistente. È tuttavia possibile creare un nuovo account in un paese o in un'area diversa e quindi contattare il supporto tecnico di Azure per chiedere il trasferimento della sottoscrizione nel nuovo account.

## <a name="change-the-subscription-name"></a>Cambiare il nome della sottoscrizione

1. Accedere al portale di Azure, selezionare **Sottoscrizione** nel riquadro sinistro e quindi selezionare la sottoscrizione da rinominare.
1. Selezionare **Panoramica** e quindi **Rinomina** sulla barra dei comandi.  
    ![Esempio di ridenominazione della sottoscrizione di Azure](./media/change-azure-account-profile/rename-sub.png)
1. Dopo aver cambiato il nome, selezionare **Salva**.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Visualizzare gli account di fatturazione](view-all-accounts.md)
