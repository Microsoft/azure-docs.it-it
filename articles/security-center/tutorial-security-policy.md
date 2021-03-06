---
title: Uso dei criteri di sicurezza | Microsoft Docs
description: Questo articolo descrive come usare i criteri di sicurezza nel centro sicurezza di Azure.
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 6ecedc20cf6924a82b6b4640d3caa75bc5958de0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101325"
---
# <a name="manage-security-policies"></a>Gestire i criteri di sicurezza

Questo articolo illustra come vengono configurati i criteri di sicurezza e come visualizzarli nel Centro sicurezza. 

## <a name="who-can-edit-security-policies"></a>Utenti che possono modificare i criteri di sicurezza

È possibile modificare i criteri di sicurezza tramite il portale Criteri di Azure, l'API REST o Windows PowerShell.

Il Centro sicurezza usa il controllo degli accessi in base al ruolo di Azure (RBAC di Azure), che fornisce ruoli predefiniti che è possibile assegnare a utenti, gruppi e servizi di Azure. Quando gli utenti aprono il Centro sicurezza, visualizzano solo le informazioni correlate alle risorse a cui possono accedere. Ciò significa che agli utenti viene assegnato il ruolo di *proprietario*, *collaboratore* o *lettore* alla sottoscrizione della risorsa. Sono inoltre disponibili due ruoli specifici del Centro sicurezza:

- **Lettura** per la sicurezza: dispone dei diritti per visualizzare gli elementi del Centro sicurezza, ad esempio raccomandazioni, avvisi, criteri e integrità. Non è possibile apportare modifiche.
- **Amministratore della sicurezza**: ha gli stessi diritti di visualizzazione del *lettore di sicurezza*. Può anche aggiornare i criteri di sicurezza e ignorare gli avvisi.

## <a name="manage-your-security-policies"></a>Gestire i criteri di sicurezza

Per visualizzare i criteri di sicurezza nel Centro sicurezza:

1. Nel dashboard del **Centro sicurezza** selezionare **Criteri di sicurezza**.

    :::image type="content" source="./media/security-center-policies/security-center-policy-mgt.png" alt-text="Pagina di gestione dei criteri":::

   Nella schermata **Gestione dei criteri** è possibile visualizzare il numero dei gruppi di gestione, delle sottoscrizioni e delle aree di lavoro, oltre alla struttura dei gruppi di gestione.

1. Selezionare la sottoscrizione o il gruppo di gestione di cui si vogliono visualizzare i criteri.

1. Verrà visualizzata la pagina Criteri di sicurezza per la sottoscrizione o il gruppo di gestione. Mostra i criteri disponibili e assegnati.

    :::image type="content" source="./media/tutorial-security-policy/security-policy-page.png" alt-text="Pagina dei criteri di sicurezza del Centro sicurezza" lightbox="./media/tutorial-security-policy/security-policy-page.png":::

    > [!NOTE]
    > Se è presente un'etichetta "MG ereditato" insieme ai criteri predefiniti, significa che il criterio è stato assegnato a un gruppo di gestione e ereditato dalla sottoscrizione che si sta visualizzando.

1. Scegliere tra le opzioni disponibili in questa pagina:

    1. Per lavorare con gli standard del settore, selezionare **Aggiungi altri standard**. Per altre informazioni, vedere [personalizzare il set di standard nel dashboard conformità normativa](update-regulatory-compliance-packages.md).

    1. Per assegnare e gestire le iniziative personalizzate, selezionare **Aggiungi iniziative personalizzate**. Per altre informazioni, vedere [uso di iniziative e criteri di sicurezza personalizzati](custom-security-policies.md).

    1. Per visualizzare e modificare l'iniziativa predefinita, selezionare **Visualizza criterio effettivo** e procedere come descritto di seguito. 

        :::image type="content" source="./media/security-center-policies/policy-screen.png" alt-text="Schermata dei criteri effettivi":::

       Questa schermata dei **criteri di sicurezza** riflette l'azione eseguita dai criteri assegnati alla sottoscrizione o al gruppo di gestione selezionato.
       
       * Usare i collegamenti nella parte superiore per aprire un' **assegnazione** dei criteri che si applica alla sottoscrizione o al gruppo di gestione. Questi collegamenti consentono di accedere all'assegnazione e modificare o disabilitare i criteri. Se, ad esempio, si nota che un'assegnazione di criteri specifica nega effettivamente Endpoint Protection, utilizzare il collegamento per modificare o disabilitare i criteri.
       
       * Nell'elenco dei criteri è possibile visualizzare l'applicazione effettiva del criterio nella sottoscrizione o nel gruppo di gestione. Vengono prese in considerazione le impostazioni di ogni criterio applicato all'ambito e viene visualizzato il risultato cumulativo delle azioni eseguite dal criterio. Se, ad esempio, in un'assegnazione dei criteri è disabilitata, ma in un'altra è impostata su AuditIfNotExist, l'effetto cumulativo si applica a AuditIfNotExist. L'effetto più attivo ha sempre la precedenza.
       
       * L'effetto dei criteri può essere: Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Per altre informazioni su come vengono applicati gli effetti, vedere [Informazioni sugli effetti di Criteri](../governance/policy/concepts/effects.md).

       > [!NOTE]
       > Quando si visualizzano i criteri assegnati, è possibile visualizzare più assegnazioni e anche come è configurata ogni singola assegnazione.


## <a name="disable-security-policies-and-disable-recommendations"></a>Disabilitare i criteri di sicurezza e disabilitare le raccomandazioni

Quando l'iniziativa di sicurezza attiva una raccomandazione irrilevante per l'ambiente in uso, è possibile impedire la visualizzazione di tale raccomandazione. Per disabilitare una raccomandazione, disabilitare i criteri specifici che generano la raccomandazione.

La raccomandazione che si vuole disabilitare verrà comunque visualizzata se è necessaria per uno standard normativo applicato agli strumenti di conformità normativi del Centro sicurezza. Anche se è stato disabilitato un criterio nell'iniziativa predefinita, i criteri nell'iniziativa dello standard normativo attiveranno comunque la raccomandazione se è necessaria per la conformità. Non è possibile disabilitare i criteri dalle iniziative standard normative.

Per ulteriori informazioni sulle raccomandazioni, vedere [gestione delle raccomandazioni sulla sicurezza](security-center-recommendations.md).

1. Nella sezione **policy & Compliance** del Centro sicurezza selezionare criteri di **sicurezza**.

    :::image type="content" source="./media/tutorial-security-policy/policy-management.png" alt-text="Avvio del processo di gestione dei criteri nel centro sicurezza di Azure":::

2. Selezionare la sottoscrizione o il gruppo di gestione per il quale si desidera disabilitare la raccomandazione.

   > [!NOTE]
   > Tenere presente che un gruppo di gestione applica i propri criteri alle relative sottoscrizioni. Pertanto, se si disabilita un criterio di una sottoscrizione e la sottoscrizione appartiene a un gruppo di gestione che usa ancora lo stesso criterio, si continuerà a ricevere le raccomandazioni relative al criterio in questione. Il criterio verrà comunque applicato dal livello di gestione e le raccomandazioni continueranno a essere generate.

1. Selezionare **Visualizza criteri validi**.

    :::image type="content" source="./media/tutorial-security-policy/view-effective-policy.png" alt-text="Come aprire i criteri validi assegnati alla sottoscrizione":::

1. Selezionare i criteri assegnati.

   ![Seleziona criteri](./media/tutorial-security-policy/security-policy.png)

1. Nella sezione **parametri** cercare i criteri che richiamano la raccomandazione che si desidera disabilitare e nell'elenco a discesa selezionare **disabilitato**

   ![disabilitare i criteri](./media/tutorial-security-policy/disable-policy.png)

1. Selezionare **Salva**.

   > [!NOTE]
   > Per rendere effettive le modifiche ai criteri di disabilitazione possono essere necessarie fino a 12 ore.

## <a name="next-steps"></a>Passaggi successivi
In questa pagina sono stati illustrati i criteri di sicurezza. Per informazioni correlate, vedere le pagine seguenti:

- [Informazioni su come impostare i criteri tramite PowerShell](../governance/policy/assign-policy-powershell.md)
- [Informazioni su come modificare i criteri di sicurezza in criteri di Azure](../governance/policy/tutorials/create-and-manage.md)
- [Informazioni su come impostare un criterio tra le sottoscrizioni o i gruppi di gestione usando criteri di Azure](../governance/policy/overview.md)
- [Informazioni su come abilitare il Centro sicurezza in tutte le sottoscrizioni in un gruppo di gestione](onboard-management-group.md)