---
title: Visualizzare le prenotazioni per le risorse di Azure | Microsoft Docs
description: Informazioni su come visualizzare le prenotazioni per Azure nel portale di Azure.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 5c9d9074e4b8d0d9e36417daee4d58c1d9b28b64
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199246"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Visualizzare le prenotazioni di Azure nel portale di Azure

A seconda del tipo di sottoscrizione e di autorizzazioni, è possibile visualizzare le prenotazioni per Azure in due modi.

## <a name="view-purchased-reservations"></a>Visualizzare le prenotazioni acquistate

Per impostazione predefinita, quando si acquista una prenotazione, l'utente e l'amministratore dell'account possono visualizzare la prenotazione. L'utente e l'amministratore account ottengono automaticamente il ruolo di proprietario per l'ordine di prenotazione e la prenotazione. Per consentire ad altri utenti di visualizzare la prenotazione, è necessario aggiungerli con il ruolo di **Proprietario** o **Lettore** all'ordine di prenotazione o alla prenotazione.

Per altre informazioni vedere [Aggiungere o modificare gli utenti che possono gestire una prenotazione](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Per visualizzare una prenotazione come proprietario o lettore,

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Cercare **Prenotazioni**.
    ![Screenshot che mostra una ricerca nel portale di Azure](./media/view-reservations/portal-reservation-search.png)  
3. L'elenco mostra tutte le prenotazioni per le quali si ha il ruolo di Proprietario o Lettore. Ogni prenotazione mostra l'ultima percentuale di utilizzo nota.  
    ![Esempio di elenco di prenotazioni](./media/view-reservations/view-reservations.png)
4. Selezionare una prenotazione e visualizzare la tendenza di utilizzo per gli ultimi cinque giorni.  
    ![Esempio di tendenza di utilizzo di una prenotazione](./media/view-reservations/reservation-utilization.png)
5. Si possono ottenere informazioni sull'[utilizzo delle prenotazioni](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) anche tramite l'API Utilizzo istanze riservate e il [pacchetto di contenuto Informazioni dettagliate sul consumo di Microsoft Azure per Power BI](/power-bi/service-connect-to-azure-consumption-insights).

Se è necessario modificare l'ambito di una prenotazione, dividere una prenotazione, oppure modificare chi può gestire una prenotazione, vedere [gestire le prenotazioni Azure](manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Visualizzare le transazioni di prenotazione per le registrazioni Enterprise

 Se si dispone di una registrazione Enterprise gestita dal partner, visualizzare le prenotazioni in **Report** nel portale EA. Per altre registrazioni Enterprise, è possibile visualizzare le prenotazioni nel portale EA e nel portale di Azure. È necessario essere un amministratore EA per visualizzare le transazioni di prenotazione.

Per visualizzare le transazioni di prenotazione nel portale di Azure,

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **Gestione dei costi e fatturazione**.

    ![Screenshot che mostra una ricerca nel portale di Azure](./media/view-reservations/portal-cm-billing-search.png)

1. Selezionare **Transazioni di prenotazione**.
1. Per filtrare i risultati, selezionare **Intervallo di tempo**, **Tipo**, o **Descrizione**.
1. Selezionare **Applica**.

    ![Screenshot che mostra i risultati delle transazioni di prenotazione](./media/view-reservations/portal-billing-reservation-transaction-results.png)

Per ottenere i dati usando un'API, vedere [Get Reserved Instance transaction charges for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) (Ottenere le tariffe di transazione di istanza riservata per i clienti Enterprise).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
- [Gestire le prenotazioni per Azure](manage-reserved-vm-instance.md)

Acquistare un piano di servizio:

- [Pagare in anticipo la capacità riservata di Azure Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Pagare in anticipo le risorse di calcolo del database SQL con capacità riservata del database SQL di Azure](../../sql-database/sql-database-reserved-capacity.md)
- [Pagare in anticipo le macchine virtuali tramite le istanze di macchina virtuale riservate di Azure](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

Acquistare un piano software:

- [Pagare in anticipo i piani software Red Hat dalle prenotazioni di Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Pagare in anticipo per i piani software SUSE dalle prenotazioni di Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)

Comprendere l'utilizzo:

- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)
- [Informazioni sull'utilizzo della prenotazione per sottoscrizioni CSP](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
