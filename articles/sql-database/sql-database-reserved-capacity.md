---
title: Risparmio sui costi
description: Informazioni su come acquistare capacità di riserva del database SQL di Azure per risparmiare sui costi per i servizi di calcolo.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979981"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Risparmia sui costi per le risorse di calcolo del database SQL con la capacità riservata del database SQL di AzureSave costs for SQL Database compute resources with Azure SQL Database reserved capacity

Risparmia denaro con il database SQL di Azure eseguendo il commit in una prenotazione per le risorse di calcolo rispetto ai prezzi con pagamento in base al costo. Con la capacità riservata del database SQL di Azure, si impegna l'uso del database SQL per un periodo di uno o tre anni per ottenere uno sconto significativo sui costi di calcolo. Per acquistare capacità di riserva del database SQL, è necessario specificare l'area di Azure, il tipo di distribuzione, il livello di prestazioni e il periodo.


Non è necessario assegnare la prenotazione a istanze del database SQL specifiche (database singoli, pool elastici o istanze gestite). Il vantaggio verrà assegnato automaticamente alle istanze di database SQL corrispondenti, già in esecuzione o appena distribuite. Acquistando una prenotazione, ci si impegna a utilizzare i costi di calcolo per un periodo di uno o tre anni. Non appena si acquista una prenotazione, i costi di calcolo del database SQL che corrispondono agli attributi della prenotazione stessa non vengono più addebitati in base alle tariffe relative al pagamento in base al consumo. Una prenotazione non copre i costi di software, rete o archiviazione associati all'istanza di database SQL. La scadenza del periodo di prenotazione comporta anche la scadenza del vantaggio sulla fatturazione. Dopo la scadenza, i database SQL vengono fatturati in base alla tariffa relativa al pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente. Per informazioni sui prezzi, vedere l'[offerta di capacità di riserva del database SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).

È possibile acquistare capacità di riserva del database SQL di Azure nel [portale di Azure](https://portal.azure.com). Usare [pagamenti anticipati o mensili](../cost-management-billing/reservations/monthly-payments-reservations.md) per acquistare la prenotazione. Per acquistare capacità di riserva del database SQL:

- È necessario essere nel ruolo di proprietario per almeno un Enterprise o una singola sottoscrizione con tariffe con pagamento in base al costo.
- Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
- Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o di vendita possono acquistare capacità di riserva del database SQL.

I dettagli su come vengono addebitati gli acquisti di prenotazioni per i clienti aziendali e i clienti con pagamento in base al consumo, vedere [Comprendere l'utilizzo](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) della prenotazione di Azure per la registrazione Enterprise e l'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al [consumo.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-sql-size-before-purchase"></a>Determinare la dimensione corretta del database SQL prima dell'acquisto

La dimensione della prenotazione deve basarsi sulla quantità totale di calcolo usata dai database singoli, dai pool elastici o dalle istanze gestite esistenti o di cui è prevista la distribuzione a breve all'interno di un'area specifica e deve essere calcolata usando lo stesso livello di prestazioni e hardware della stessa generazione.

Si supponga, ad esempio, che siano in esecuzione un pool elastico di quinta generazione a 16 vCore a utilizzo generico e due database singoli di quinta generazione a 4 vCore critici. Si supponga anche che entro un mese sia prevista la distribuzione di un altro pool elastico di quinta generazione a 16 vCore a utilizzo generico e di un pool elastico di quinta generazione a 32 vCore critico. Si supponga poi di sapere che queste risorse saranno necessarie per almeno 1 anno. In questo caso è necessario acquistare una prenotazione di 1 anno per 32 vCore (2x16) per database singolo/pool elastico di quinta generazione per utilizzo generico di calcolo e una prenotazione di 1 anno per 40 vCore (4x2+32) per database singolo SQL/pool elastico di quinta generazione Business Critical.

## <a name="buy-sql-database-reserved-capacity"></a>Acquistare capacità di riserva del database SQL

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Selezionare Tutte le**prenotazioni** **dei servizi** > .
3. Selezionare **Aggiungi,** quindi nel riquadro Prenotazioni acquisti selezionare **Database SQL** per acquistare una nuova prenotazione per il database SQL.
4. Compilare i campi obbligatori. Possono usufruire dello sconto per la capacità di riserva i database singoli, le istanze gestite o i pool elastici nuovi o esistenti che soddisfano gli attributi selezionati. Il numero di istanze di database SQL a cui viene applicato lo sconto dipende dall'ambito e dalla quantità selezionati.
    ![Screenshot subito prima dell'acquisto della capacità di riserva del database SQL](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

Nella tabella seguente vengono descritti i campi obbligatori.

| Campo      | Descrizione|
|------------|--------------|
|Subscription|Sottoscrizione usata per pagare la prenotazione della capacità di riserva del database SQL. L'acquisto della prenotazione di capacità di riserva del database SQL viene addebitato in base al metodo di pagamento associato alla sottoscrizione. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri di offerta: MS-A-R-0017P o MS-A-R-0148P) o un contratto individuale con prezzi con pagamento in base al consumo (numeri di offerta: MS-A-R-0003P o MS-A-R-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Per un abbonamento individuale con prezzi con pagamento in base al consumo, gli addebiti vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura nell'abbonamento.|
|Scope       |L'ambito della prenotazione di vCore può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: <br/><br/>**Condiviso**, lo sconto prenotazione vCore viene applicato alle istanze del database SQL in esecuzione in qualsiasi sottoscrizione all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.<br/><br/>**Sottoscrizione singola**, lo sconto di prenotazione vCore viene applicato alle istanze del database SQL in questa sottoscrizione. <br/><br/>**Singolo gruppo di risorse,** lo sconto di prenotazione viene applicato alle istanze del database SQL nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione.|
|Region      |Area di Azure coperta dalla prenotazione di capacità di riserva del database SQL.|
|Tipo di distribuzione|Tipo di risorsa di SQL per cui si vuole acquistare la prenotazione.|
|Livello di prestazioni|Livello di servizio per le istanze di database SQL.
|Termine        |Un anno o tre anni.|
|Quantità    |Quantità di risorse di calcolo acquistate all'interno della prenotazione della capacità riservata del database SQL. La quantità è un numero di vCore nell'area di Azure e nel livello prestazioni selezionati che vengono prenotati e ottengono lo sconto sulla fatturazione. Ad esempio, se si esegue o si prevede di eseguire istanze di database SQL con la capacità di calcolo totale di Gen5 16 vCore nell'area Stati Uniti orientali, è necessario specificare la quantità come 16 per ottimizzare il vantaggio per tutte le istanze. |

1. Rivedere il costo della prenotazione di capacità di riserva del database SQL nella sezione **Costi**.
1. Selezionare **Acquista**.
1. Selezionare **Visualizza questa prenotazione** per vedere lo stato dell'acquisto.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flessibilità delle dimensioni vCore

La flessibilità delle dimensioni vCore consente di aumentare o ridurre la quantità di risorse all'interno di un livello di prestazioni e di un'area, senza perdere il vantaggio della capacità riservata. La capacità riservata del database SQL offre anche la possibilità di spostare temporaneamente i database ad accesso frequente tra pool e database singoli come parte delle normali operazioni (entro la stessa area e lo stesso livello di prestazioni) senza perdere il vantaggio della capacità riservata. Mantenendo un buffer di risorse non applicate nella prenotazione, è possibile gestire in modo efficace i picchi di prestazioni senza superare il budget.

## <a name="limitation"></a>Limitazione

Non è possibile riservare database SQL basati su DTU (base, standard o Premium).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Se hai domande o hai bisogno di aiuto, [crea una richiesta](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)di supporto .

## <a name="next-steps"></a>Passaggi successivi

Lo sconto sulla prenotazione di vCore si applica automaticamente al numero di istanze di database SQL corrispondenti all'ambito di prenotazione e agli attributi della capacità di riserva del database SQL. È possibile aggiornare l'ambito della prenotazione di capacità di riserva del database SQL nel [portale di Azure](https://portal.azure.com), in PowerShell, nell'interfaccia della riga di comando o tramite l'API.

Per informazioni su come gestire la prenotazione di capacità di riserva del database SQL, vedere [Gestire la capacità di riserva del database SQL](../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

- [Informazioni sulle prenotazioni di Azure](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Gestire le prenotazioni di AzureManage Azure Reservations](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Comprendere lo sconto sulle prenotazioni di Azure](../cost-management-billing/reservations/understand-reservation-charges.md)
- [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)
