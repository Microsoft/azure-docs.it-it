---
title: File di inclusione
description: File di inclusione
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: d791c4ba46587ac5709d72cb31bc76f087118b03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67476268"
---
## <a name="create-one-or-more-access-reviews"></a>Creare una o più verifiche di accesso

1. Fare clic su **nuovo** per creare una nuova verifica di accesso.

1. Assegnare un nome alla verifica di accesso. Facoltativamente è possibile assegnare una descrizione alla verifica. Il nome e la descrizione vengono visualizzati dai revisori.

    ![Creare una verifica di accesso - Nome della verifica e descrizione](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Impostare un valore per **Data di inizio**. Per impostazione predefinita, la verifica di accesso viene eseguita una sola volta, viene avviata lo stesso giorno in cui viene creata e termina entro un mese. È possibile cambiare le date di inizio e di fine per iniziare la verifica di accesso in futuro e impostare la durata sul numero di giorni desiderato.

    ![Data di inizio, frequenza, durata, fine, numero di volte e data di fine](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Per fare in modo che la verifica di accesso ricorra, modificare l'impostazione della **frequenza** da **una volta a una** **settimana**, ogni **mese**, **trimestrale**, **annuale**o **semestrale**. Usare il dispositivo di scorrimento **durata** o la casella di testo per definire il numero di giorni per cui ogni revisione della serie ricorrente verrà aperta per l'input dai revisori. La durata massima che è possibile impostare per una verifica mensile, ad esempio, è di 27 giorni, per evitare la sovrapposizione delle verifiche.

1. Usare l'impostazione **Fine** per specificare come terminare la serie di verifiche di accesso ricorrenti. La serie può terminare in tre modi: può essere eseguita in modo continuo per avviare le verifiche per un periodo illimitato o fino a una data specifica oppure terminare dopo che è stato completato un numero definito di occorrenze. Un altro amministratore utente o un altro amministratore globale può arrestare la serie dopo la creazione modificando la data in **Impostazioni**, in modo che termini a tale data.

1. Nella sezione **utenti** selezionare uno o più ruoli di cui si desidera verificare l'appartenenza.

    ![Ambito degli utenti per verificare l'appartenenza ai ruoli](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Se si seleziona più di un ruolo, vengono create più verifiche di accesso. Se ad esempio si selezionano cinque ruoli, vengono create cinque verifiche di accesso separate.

    Se si sta creando una verifica di accesso dei ruoli di Azure AD, di seguito viene illustrato un esempio dell'elenco di appartenenza alla verifica.

    ![Elencare il riquadro delle appartenenze Azure AD i ruoli che è possibile selezionare](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Se si sta creando una verifica di accesso dei ruoli delle risorse di Azure, di seguito viene illustrato un esempio dell'elenco di appartenenza alla verifica.

    ![Riquadro Verifica appartenenza che elenca i ruoli delle risorse di Azure che è possibile selezionare](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Nella sezione **revisori** selezionare una o più persone per esaminare tutti gli utenti. In alternativa è possibile fare in modo che i membri verifichino il proprio accesso.

    ![Elenco di revisori di utenti o membri selezionati (self)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Utenti selezionati** : usare questa opzione quando non si è certi di chi necessita di accesso. Con questa opzione è possibile assegnare l'esecuzione della revisione a un proprietario delle risorse o a un gestore del gruppo.
    - **Membri (self)** : usare questa opzione per fare in modo che gli utenti esamino le proprie assegnazioni di ruolo.

### <a name="upon-completion-settings"></a>Impostazioni al completamento

1. Per specificare cosa succede dopo il completamento di una verifica, espandere la sezione **Impostazioni al completamento**.

    ![Al termine delle impostazioni per l'applicazione automatica e la revisione non risponde](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Se si vuole rimuovere automaticamente l'accesso per gli utenti rifiutati, impostare l'opzione **Applica automaticamente i risultati alla risorsa** su **Abilita**. Per applicare manualmente i risultati al termine della verifica, impostare l'opzione su **Disabilita**.

1. Usare l'elenco **In caso di mancata risposta del revisore** per specificare cosa accade per gli utenti non sottoposti a verifica dal revisore entro il periodo di verifica. Questa impostazione non ha alcun impatto sugli utenti che sono stati sottoposti a verifica manualmente dai revisori. Se la decisione finale del revisore è il rifiuto, l'accesso dell'utente verrà rimosso.

    - **Nessuna modifica**: non viene apportata alcuna modifica all'accesso dell'utente
    - **Rimuovi accesso**: l'accesso dell'utente viene rimosso
    - **Approva accesso**: l'accesso dell'utente viene approvato
    - **Accetta i consigli**: vengono applicati i consigli del sistema per rifiutare o approvare l'accesso continuo dell'utente

### <a name="advanced-settings"></a>Impostazioni avanzate

1. Per specificare impostazioni aggiuntive, espandere la sezione **Impostazioni avanzate**.

    ![Impostazioni avanzate per Mostra raccomandazioni, Richiedi motivo all'approvazione, notifiche di posta elettronica e promemoria](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Impostare **Mostra i consigli** su **Abilita** per mostrare ai revisori i consigli del sistema basati sulle informazioni di accesso dell'utente.

1. Impostare **Richiedi il motivo all'approvazione** su **Abilita** per richiedere al revisore di specificare un motivo per l'approvazione.

1. Impostare **Notifiche tramite posta elettronica** su **Abilita** per fare in modo che Azure AD invii notifiche tramite posta elettronica ai revisori all'avvio di una verifica di accesso e agli amministratori al completamento di una verifica.

1. Impostare **Promemoria** su **Abilita** per fare in modo che Azure AD invii promemoria delle verifiche di accesso in corso ai revisori che non hanno completato la verifica.
