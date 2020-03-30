---
title: Gestire gli utenti esclusi dai criteri di accesso condizionale - Azure ADManage users excluded from Conditional Access policies - Azure AD
description: Informazioni su come usare le verifiche di accesso ad Azure Active Directory (Azure AD) per gestire gli utenti esclusi dai criteri di accesso condizionale
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d7bce940f2b614c239e8b5e5719d96da10a6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422702"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Usare le verifiche di accesso di Azure AD per gestire gli utenti esclusi dai criteri di accesso condizionaleUse Azure AD access reviews to manage users excluded from Conditional Access policies

In una situazione ideale, tutti gli utenti seguono gli stessi criteri di accesso per proteggere l'accesso alle risorse dell'organizzazione. Esistono tuttavia casi aziendali che richiedono di introdurre eccezioni. Questo articolo descrive alcuni esempi in cui potrebbero essere necessarie delle esclusioni e come può l'amministratore IT gestire questa esigenza, evitare sviste nelle eccezioni dei criteri e offrire ai revisori una prova della revisione regolare di tali eccezioni tramite le verifiche di accesso di Azure Active Directory (Azure AD).

> [!NOTE]
> Per usare le verifiche di accesso di Azure AD è necessaria una licenza di valutazione o una licenza a pagamento Azure Premium P2 o Enterprise Mobility + Security E5 valida. Per altre informazioni, vedere Edizioni di Azure Active Directory .For more information, see [Azure Active Directory editions](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Motivi per escludere utenti dai criteri

In qualità di amministratore IT, è possibile usare [l'accesso condizionale](../conditional-access/overview.md) di Azure AD per richiedere agli utenti di eseguire l'autenticazione tramite l'autenticazione a più fattori o l'accesso da una rete o un dispositivo attendibile. Durante la pianificazione della distribuzione, è possibile scoprire che alcuni di questi requisiti non possono essere soddisfatti da tutti gli utenti. Ad esempio, esistono utenti che lavorano da un ufficio remoto che non fa parte della rete interna o un dirigente che usa un telefono meno recente non supportato. L'azienda richiede che questi utenti siano autorizzati ad accedere e svolgere il proprio lavoro, pertanto sono esclusi dai criteri di accesso condizionale.

Come altro esempio, è possibile usare [percorsi denominati](../conditional-access/location-condition.md) in Accesso condizionale per configurare un set di contee e aree da cui non si vuole consentire agli utenti di accedere al tenant.

![Percorsi denominati in Accesso condizionaleNamed locations in Conditional Access](./media/conditional-access-exclusion/named-locations.png)

Tuttavia, in alcuni casi, gli utenti potrebbero avere un motivo legittimo per accedere da questi paesi/aree geografiche bloccate. Ad esempio, potrebbero essere in viaggio in uno di questi paesi per motivi di lavoro o personali. In questo esempio, il criterio di accesso condizionale per bloccare questi paesi potrebbe disporre di un gruppo di sicurezza cloud dedicato per gli utenti esclusi dal criterio. Gli utenti che devono avere l'accesso anche in viaggio, possono aggiungersi al gruppo usando la [gestione gruppi self-service di Azure AD](../users-groups-roles/groups-self-service-management.md).

Un altro esempio potrebbe essere che si dispone di un criterio di accesso condizionale che [blocca l'autenticazione legacy per la maggior parte degli utenti.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/) Microsoft consiglia di bloccare l'uso di protocolli legacy nel tenant per migliorare il comportamento di sicurezza. Tuttavia, in presenza di utenti che devono assolutamente usare metodi di autenticazione legacy per accedere alle risorse tramite Office 2010 o client basati su SMTP/IMAP o POP, è possibile escludere gli utenti dai criteri che bloccano i metodi di autenticazione legacy.

## <a name="why-are-exclusions-challenging"></a>Perché le esclusioni sono complesse?

In Azure AD è possibile definire l'ambito di criteri di accesso condizionale a un set di utenti. È anche possibile escludere alcuni di questi utenti selezionando ruoli di Azure AD, singoli utenti o ospiti degli utenti. È importante ricordare che quando vengono configurate queste esclusioni, la finalità dei criteri non può essere applicata a questi utenti. Se le esclusioni venissero configurate come un elenco di singoli utenti o tramite un gruppo di sicurezza locale legacy, risulterebbero limitati la visibilità di questo elenco di esclusioni (gli utenti potrebbero non sapere della sua esistenza) e il controllo dell'amministratore IT (gli utenti possono richiedere l'inserimento nel gruppo di sicurezza per evitare i criteri). Inoltre, gli utenti che risultano qualificati per l'esclusione una volta, potrebbero non averne più bisogno o non essere più idonei.

All'inizio di un'esclusione, l'elenco di utenti che possono ignorare i criteri è in genere breve. Con il passare del tempo, il numero degli utenti esclusi cresce, così come l'elenco. A un certo punto, diventa necessario verificare l'elenco e accertarsi che ognuno di questi utenti debba continuare a essere escluso. La gestione dell'elenco da un punto di vista tecnico può essere relativamente semplice, ma chi prende le decisioni aziendali e come ci si può assicurare che tutto ciò sia controllabile?

Tuttavia, se si configura l'esclusione ai criteri di accesso condizionale usando un gruppo di Azure AD, è possibile usare le verifiche di accesso come controllo di compensazione, per aumentare la visibilità e ridurre il numero di utenti che presentano un'eccezione.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Come creare un gruppo di esclusione in un criterio di accesso condizionaleHow to create an exclusion group in a Conditional Access policy

Seguire questi passaggi per creare un nuovo gruppo di Azure AD e un criterio di accesso condizionale che non si applica a tale gruppo.

### <a name="create-an-exclusion-group"></a>Creare un gruppo di esclusione

1. Accedere al portale di Azure.

1. Nel menu di spostamento a sinistra fare clic su **Azure Active Directory** e quindi su **Gruppi**.

1. Nel menu in alto fare clic su **Nuovo gruppo** per aprire il riquadro del gruppo.

1. Selezionare **Sicurezza** nell'elenco **Tipo gruppo**. Specificare un nome e una descrizione.

1. Assicurarsi di impostare il tipo di **Appartenenza** su **Assegnato**.

1. Selezionare gli utenti che devono far parte di questo gruppo di esclusione e quindi fare clic su **Crea**.

    ![Riquadro Nuovo gruppo in Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Creare criteri di accesso condizionale che escludano il gruppo

A questo punto è possibile creare un criterio di accesso condizionale che utilizza questo gruppo di esclusione.

1. Nel riquadro di spostamento sinistro fare clic su **Azure Active Directory** e quindi su Accesso condizionale per aprire il pannello Criteri.In the left navigation, click Azure Active Directory and then click **Conditional Access** to open the **Policies** blade.

1. Fare clic su **Nuovi criteri** per aprire il riquadro **Nuovo**.

1. Specificare un nome.

1. In Assegnazioni fare clic su **Utenti e gruppi**.

1. Nella scheda **Includi** selezionare **Tutti gli utenti**.

1. Nella scheda **Escludi** aggiungere un segno di spunta per **Utenti e gruppi** e quindi fare clic su **Selezionare gli utenti esclusi**.

1. Selezionare il gruppo di esclusione creato.

    > [!NOTE]
    > Come procedura consigliata, è consigliabile escludere almeno un account amministratore dai criteri durante i test per assicurarsi di non rimanere bloccati fuori dal tenant.

1. Continuare con l'impostazione dei criteri di accesso condizionale in base ai requisiti dell'organizzazione.

    ![Selezionare il riquadro Utenti esclusi in Accesso condizionaleSelect excluded users pane in Conditional Access](./media/conditional-access-exclusion/select-excluded-users.png)

Esaminiamo due esempi in cui è possibile utilizzare le verifiche di accesso per gestire le esclusioni nei criteri di accesso condizionale.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Esempio 1: Verifica dell'accesso per gli utenti che accedono da paesi/aree geografiche bloccatiExample 1: Access review for users accessing from blocked countries/regions

Si supponga di disporre di un criterio di accesso condizionale che blocca l'accesso da determinati paesi.Let's say you have a Conditional Access policy that blocks access from certain countries/regions. I criteri includono un gruppo escluso dai criteri. Ecco una verifica di accesso consigliata per controllare i membri del gruppo.

> [!NOTE]
> Per creare le verifiche di accesso, è necessario un ruolo Amministratore globale o Amministratore utente.

1. La verifica verrà eseguita ogni settimana.

2. Non è prevista la fine, per assicurarsi di mantenere sempre aggiornato questo gruppo di esclusione.

3. Tutti i membri di questo gruppo saranno inclusi nell'ambito per la revisione.

4. Ogni utente dovrà auto-attestare che hanno ancora bisogno di avere accesso da questi paesi/aree bloccate, quindi hanno ancora bisogno di essere un membro del gruppo.

5. Se l'utente non risponde alla richiesta di revisione, verrà rimosso automaticamente dal gruppo e pertanto non potrà più accedere al tenant mentre è in viaggio verso questi paesi/aree geografiche.

6. Abilitare le notifiche tramite posta elettronica, in modo che gli utenti ricevano notifica dell'inizio e del completamento della verifica di accesso.

    ![Creare un riquadro di verifica dell'accesso per l'esempio 1Create an access review pane for example 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Esempio 2: Verifica di accesso per gli utenti che accedono con l'autenticazione legacy

Si supponga di disporre di un criterio di accesso condizionale che blocca l'accesso per gli utenti che utilizzano l'autenticazione legacy e le versioni client precedenti. I criteri includono un gruppo escluso dai criteri. Ecco una verifica di accesso consigliata per controllare i membri del gruppo.

1. Questa verifica dovrà essere periodica.

2. Tutti i membri del gruppo dovranno essere verificati.

3. Può essere configurata in modo da elencare i responsabili delle unità aziendali come revisori selezionati.

4. Applicare automaticamente i risultati e rimuovere gli utenti non approvati per continuare a usare i metodi di autenticazione legacy.

5. Potrebbe essere utile abilitare le raccomandazioni, in modo che i revisori di gruppi di grandi dimensioni possano prendere facilmente le decisioni necessarie.

6. Abilitare le notifiche tramite posta elettronica, in modo che gli utenti ricevano notifica dell'inizio e del completamento della verifica di accesso.

    ![Creare un riquadro di verifica dell'accesso per l'esempio 2Create an access review pane for example 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Suggerimento avanzato**: se esistono molti gruppi di esclusione e pertanto è necessario creare più verifiche di accesso, è ora disponibile un'API nell'endpoint beta di Microsoft Graph che consente di crearli e gestirli a livello di codice. Per iniziare, vedere le [informazioni di riferimento per l'API delle verifiche di accesso di Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) e [Example of retrieving Azure AD access reviews via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096) (Esempio di recupero delle verifiche di accesso di Azure AD tramite Microsoft Graph).

## <a name="access-review-results-and-audit-logs"></a>Risultati delle verifiche di accesso e log di controllo

Ora che hai tutto a posto, gruppo, criteri di accesso condizionale e verifiche di accesso, è il momento di monitorare e tenere traccia dei risultati di queste revisioni.

1. Nel portale di Azure aprire il pannello **Verifiche di accesso**.

1. Aprire il controllo e il programma creati per gestire il gruppo di esclusione.

1. Fare clic su **Risultati** per vedere chi è stato approvato per rimanere nell'elenco e chi è stato rimosso.

    ![I risultati delle verifiche di accesso mostrano chi è stato approvato](./media/conditional-access-exclusion/access-reviews-results.png)

1. Fare quindi clic su **Log di controllo** per visualizzare le azioni eseguite durante la verifica.

    ![Recensioni di accesso ai registri di controllo che elencano le azioni](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Gli amministratori IT sanno che gestire gruppi di esclusione per i criteri è talvolta inevitabile. Tuttavia, la manutenzione di questi gruppi, la loro verifica a intervalli regolari da parte del titolare dell'azienda o degli utenti stessi, così come il controllo di queste modifiche possono essere semplificati grazie alle verifiche di accesso di Azure AD.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una verifica di accesso di gruppi o applicazioniCreate an access review of groups or applications](create-access-review.md)
- [Che cos'è l'accesso condizionale in Azure Active Directory?](../conditional-access/overview.md)
