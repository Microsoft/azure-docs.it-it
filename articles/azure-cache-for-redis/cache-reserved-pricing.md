---
title: Pagamento anticipato per il calcolo con capacità riservata - Cache di Azure per Redis
description: Pagamento anticipato per la cache di Azure per le risorse di calcolo Redis con capacità riservata
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: aded023c9f4c045f612e33d32c1e3ac71afddf02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77530302"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Pagamento anticipato per la cache di Azure per le risorse di calcolo Redis con capacità riservata

La cache di Azure per Redis ora consente di risparmiare denaro con il pagamento anticipato per le risorse di calcolo rispetto ai prezzi con pagamento in base al costo. Con la cache di Azure per la capacità riservata Redis, si assume un impegno iniziale nella cache per un periodo di uno o tre anni per ottenere uno sconto significativo sui costi di calcolo. Per acquistare la cache di Azure per la capacità riservata Redis, è necessario specificare l'area di Azure, il livello di servizio e il termine.

Non è necessario assegnare la prenotazione a una cache di Azure specifica per le istanze Redis.You do not need to assign the reservation to specific Azure Cache for Redis instances. Una cache di Azure già in esecuzione per Redis o quelli che vengono appena distribuiti otterrà automaticamente il vantaggio dei prezzi riservati, fino alla dimensione della cache riservata. Tramite l'acquisto di una prenotazione, si pagano in anticipo i costi di calcolo per un periodo di uno o tre anni. Non appena si acquista una prenotazione, gli addebiti di calcolo della cache di Azure per Redis che corrispondono agli attributi della prenotazione non vengono più addebitati alle tariffe con pagamento in base al costo. Una prenotazione non copre i costi di rete o di archiviazione associati alla cache. Alla fine del periodo di prenotazione, il vantaggio di fatturazione scade e la cache di Azure per Redis viene fatturata al prezzo con pagamento in base al costo. Le prenotazioni non vengono rinnovate automaticamente. Per informazioni sui prezzi, vedere Cache di [Azure per Redis capacità riservata offerta](https://azure.microsoft.com/pricing/details/cache).

È possibile acquistare la cache di Azure per la capacità riservata Redis nel portale di [Azure.](https://portal.azure.com/) Per acquistare la capacità riservata:

* È necessario essere nel ruolo di proprietario per almeno un Enterprise o una singola sottoscrizione con tariffe con pagamento in base al costo.
* Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare la cache di Azure per la capacità riservata Redis.For Cloud Solution Provider (CSP) program, only the admin agents or sales agents can purchase Azure Cache for Redis reserved capacity.

Per informazioni dettagliate su come vengono addebitati gli acquisti di prenotazioni da parte dei clienti aziendali e dei clienti con pagamento in base al consumo, vedere [Informazioni sull'utilizzo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) della prenotazione di Azure per la registrazione Enterprise e [Informazioni sull'utilizzo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo.


## <a name="determine-the-right-cache-size-before-purchase"></a>Determinare la dimensione corretta della cache prima dell'acquisto

La dimensione della prenotazione deve essere basata sulla quantità totale di calcolo utilizzata dalla cache esistente o presto distribuita all'interno di un'area specifica e utilizzando lo stesso livello di servizio.

Si supponga, ad esempio, di eseguire uno scopo generale, Gen5 – 32 vCore cache e due cache ottimizzata per la memoria, Gen5 – 16 vCore. Inoltre, si suppone che si prevede di distribuire entro il mese successivo uno scopo generale aggiuntivo, Gen5 – 32 server di database vCore e una memoria ottimizzata, Gen5 – 16 server di database vCore. Supponiamo che tu sappia che queste risorse ti serviranno per almeno 1 anno. In questo caso, è necessario acquistare un 64 (2x32) vCores, 1 anno di prenotazione per singolo database generico - Gen5 e un 48 (2x16 - 16) vCore 1 prenotazione anno per la memoria del database singolo ottimizzato - Gen5


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Acquistare la cache di Azure per la capacità riservata RedisBuy Azure Cache for Redis reserved capacity

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. Selezionare Tutte le**prenotazioni** **dei servizi** > .
3. Selezionare **Aggiungi** e quindi nel riquadro Prenotazioni acquisti selezionare Cache di **Azure per Redis per** acquistare una nuova prenotazione per le cache.
4. Compilare i campi obbligatori. Database nuovi o esistenti che corrispondono agli attributi selezionati sono idonei per ottenere lo sconto sulla capacità riservata. Il numero effettivo della cache di Azure per le istanze Redis che ottengono lo sconto dipende dall'ambito e dalla quantità selezionati.


![Panoramica dei prezzi riservati](media/cache-reserved-pricing/cache-reserved-price.png)


Nella tabella seguente vengono descritti i campi obbligatori.

| Campo | Descrizione |
| :------------ | :------- |
| Subscription   | Sottoscrizione usata per pagare la cache di Azure per la prenotazione della capacità riservata Redis.The subscription used to pay for the Azure Cache for Redis reserved capacity reservation. Al metodo di pagamento nella sottoscrizione vengono addebitati i costi iniziali per la cache di Azure per la prenotazione della capacità riservata Redis.The payment method on the subscription is charged the upfront costs for the Azure Cache for Redis reserved capacity reservation. Il tipo di sottoscrizione deve essere un contratto Enterprise (numeri di offerta: MS-A-R-0017P o MS-A-R-0148P) o un contratto individuale con prezzi con pagamento in base al consumo (numeri di offerta: MS-A-R-0003P o MS-A-R-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Per un abbonamento individuale con prezzi con pagamento in base al consumo, gli addebiti vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura nell'abbonamento.
| Scope | L'ambito della prenotazione può coprire una o più sottoscrizioni (ambito condiviso). Se si seleziona: </br></br> **Condiviso,** lo sconto sulla prenotazione viene applicato alla cache di Azure per le istanze Redis in esecuzione in qualsiasi sottoscrizione all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</br></br> **Sottoscrizione singola,** lo sconto di prenotazione viene applicato alla cache di Azure per le istanze Redis in questa sottoscrizione. </br></br> **Singolo gruppo di risorse,** lo sconto sulla prenotazione viene applicato alla cache di Azure per le istanze Redis nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione.
| Region | Area di Azure coperta dalla cache di Azure per la prenotazione della capacità riservata Redis.The Azure region that's covered by the Azure Cache for Redis reserved capacity reservation.
| Piano tariffario | Livello di servizio per la cache di Azure per i server Redis.The service tier for the Azure Cache for Redis servers.
| Termine | Un anno o tre anni
| Quantità | Quantità di risorse di calcolo acquistate nella cache di Azure per la prenotazione della capacità riservata Redis. La quantità è un numero di cache nell'area di Azure e nel livello di servizio selezionati che vengono prenotate e ottengono lo sconto sulla fatturazione. Ad esempio, se si esegue o si prevede di eseguire una cache di Azure per i server Redis con la capacità totale della cache di 26 GB nell'area Stati Uniti orientali, è necessario specificare la quantità come 26 per ottimizzare il vantaggio per tutte le cache.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [Scambi e rimborsi self-service per le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="cache-size-flexibility"></a>Flessibilità delle dimensioni della cache

La flessibilità delle dimensioni della cache consente di aumentare o ridurre le prestazioni all'interno di un livello di servizio e di un'area, senza perdere il vantaggio della capacità riservata.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

Se hai domande o hai bisogno di aiuto, [crea una richiesta](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)di supporto .

## <a name="next-steps"></a>Passaggi successivi

Lo sconto prenotazione viene applicato automaticamente alla cache di Azure per le istanze Redis che corrispondono all'ambito e agli attributi della prenotazione. È possibile aggiornare l'ambito della prenotazione tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure o l'API.

*  Per informazioni su come vengono applicati gli sconti sulla capacità riservata alla cache di Azure per Redis, vedere [Informazioni sullo sconto per prenotazione di AzureTo](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md) learn how reserved capacity discounts are applied to Azure Cache for Redis, see Understand the Azure reservation discount

* Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

    * [Informazioni sulle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
    * [Gestire le prenotazioni di AzureManage Azure Reservations](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
    * [Comprendere lo sconto sulle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
    * [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
    * [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
    * [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

