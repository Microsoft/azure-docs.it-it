---
title: Pagamento anticipato per il calcolo con capacità riservata-database di Azure per MySQL
description: Pagamento anticipato per le risorse di calcolo per database di Azure per MySQL con capacità riservata
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 78c8750de7189bad33e9bbc766a3d7543a646f6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159354"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>Pagamento anticipato per le risorse di calcolo per database di Azure per MySQL con capacità riservata

Database di Azure per MySQL consente ora di risparmiare denaro prepagando le risorse di calcolo rispetto ai prezzi con pagamento in base al consumo. Con la capacità riservata di database di Azure per MySQL, è possibile effettuare un impegno iniziale sul server MySQL per un periodo di uno o tre anni per ottenere uno sconto significativo sui costi di calcolo. Per acquistare la capacità riservata di database di Azure per MySQL, è necessario specificare l'area di Azure, il tipo di distribuzione, il livello di prestazioni e il termine. </br>

Non è necessario assegnare la prenotazione a specifici server di database di Azure per MySQL. Un database di Azure già in esecuzione per MySQL o uno appena distribuito, otterrà automaticamente il vantaggio dei prezzi riservati. Tramite l'acquisto di una prenotazione, si pagano in anticipo i costi di calcolo per un periodo di uno o tre anni. Non appena si acquista una prenotazione, per i costi di calcolo di database di Azure per MySQL che corrispondono agli attributi di prenotazione non vengono più addebitate le tariffe a consumo. Una prenotazione non copre il software, la rete o gli addebiti di archiviazione associati al server di database MySQL. Al termine del periodo di prenotazione, il vantaggio di fatturazione scadrà e il database di Azure per MySQL verrà fatturato in base al prezzo con pagamento in base al consumo. Le prenotazioni non vengono rinnovate automaticamente. Per informazioni sui prezzi, vedere l' [offerta di capacità riservata per database di Azure per MySQL](https://azure.microsoft.com/pricing/details/mysql/). </br>

È possibile acquistare la capacità riservata di database di Azure per MySQL nell' [portale di Azure](https://portal.azure.com/). Per acquistare la capacità riservata:

* È necessario avere il ruolo di proprietario per almeno una sottoscrizione Enterprise o singola con tariffe con pagamento in base al consumo.
* Per le sottoscrizioni Enterprise, **Aggiungi istanze riservate** deve essere abilitata nel [portale EA](https://ea.azure.com/). In alternativa, se tale impostazione è disabilitata, è necessario essere un amministratore della sottoscrizione con contratto Enterprise.
* Per il programma Cloud Solution Provider (CSP), solo gli agenti di amministrazione o gli agenti di vendita possono acquistare la capacità riservata di database di Azure per MySQL. </br>

Per informazioni dettagliate sul modo in cui i clienti aziendali e i clienti con pagamento in base al consumo vengono addebitati sugli acquisti di prenotazione, vedere informazioni [sull'utilizzo delle prenotazioni di Azure per la registrazione Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) e [informazioni sull'utilizzo della prenotazione di Azure per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage).


## <a name="determine-the-right-database-size-before-purchase"></a>Determinare le dimensioni del database corrette prima dell'acquisto

Le dimensioni della prenotazione devono essere basate sulla quantità totale di risorse di calcolo usate dal server esistente o presto distribuito in un'area specifica e usando lo stesso livello di prestazioni e la stessa generazione dell'hardware.</br>

Si supponga, ad esempio, di eseguire un database MySQL quinta generazione-32 vCore e due database MySQL con ottimizzazione per la memoria, quinta generazione-16 vCore. Si supponga, inoltre, di pianificare la distribuzione entro il prossimo mese con un server di database vCore, quinta generazione – 32 e un server di database con ottimizzazione per la memoria, quinta generazione – 16 vCore. Supponiamo che tu sappia che ti serviranno queste risorse per almeno un anno. In questo caso, è necessario acquistare una prenotazione di 64 (2x32) Vcore, 1 anno per utilizzo generico per database singolo-quinta generazione e una prenotazione 48 (2x16 + 16) vCore 1 anno per l'ottimizzazione della memoria per database singolo-quinta generazione


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>Acquistare la capacità riservata per database di Azure per MySQL

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **tutte le prenotazioni dei servizi** > **Reservations**.
3. Selezionare **Aggiungi** e quindi nel riquadro prenotazioni acquisti selezionare database di **Azure per MySQL** per acquistare una nuova prenotazione per i database MySQL.
4. Compilare i campi obbligatori. I database nuovi o esistenti che corrispondono agli attributi selezionati sono idonei per ottenere lo sconto per la capacità riservata. Il numero effettivo del database di Azure per i server MySQL che ottengono lo sconto dipende dall'ambito e dalla quantità selezionati.


![Panoramica dei prezzi riservati](media/concepts-reserved-pricing/mysql-reserved-price.png)


Nella tabella seguente vengono descritti i campi obbligatori.

| Campo | Descrizione |
| :------------ | :------- |
| Subscription   | Sottoscrizione usata per pagare la prenotazione di capacità riservata per database di Azure per MySQL. Il metodo di pagamento per la sottoscrizione viene addebitato ai costi iniziali per la prenotazione di capacità riservata del database di Azure per MySQL. Il tipo di sottoscrizione deve essere un contratto Enterprise Agreement (numero offerta: MS-AZR-0017P o MS-AZR-0148P) o un contratto singolo con prezzi con pagamento in base al consumo (numeri di offerta: MS-AZR-0003P o MS-AZR-0023P). Se si dispone di una sottoscrizione Enterprise, il costo delle istanze riservate viene sottratto dal saldo dell'impegno monetario prescelto. Per una singola sottoscrizione con prezzi con pagamento in base al consumo, i costi vengono addebitati sulla carta di credito o sul metodo di pagamento della fattura per la sottoscrizione.
| Scope | L'ambito della prenotazione vCore può coprire una sottoscrizione o più sottoscrizioni (ambito condiviso). Se si seleziona: </br></br> **Condiviso**, lo sconto di prenotazione vCore viene applicato al database di Azure per i server MySQL in esecuzione in tutte le sottoscrizioni all'interno del contesto di fatturazione. Per i clienti aziendali, l'ambito condiviso è la registrazione e include tutte le sottoscrizioni all'interno della registrazione. Per i clienti con pagamento in base al consumo, l'ambito condiviso copre tutte le sottoscrizioni con pagamento in base al consumo create dall'amministratore dell'account.</br></br> **Sottoscrizione singola**, lo sconto di prenotazione vCore viene applicato al database di Azure per i server MySQL in questa sottoscrizione. </br></br> **Gruppo di risorse singolo**, lo sconto relativo alla prenotazione viene applicato al database di Azure per i server MySQL nella sottoscrizione selezionata e al gruppo di risorse selezionato all'interno di tale sottoscrizione.
| Region | Area di Azure coperta dalla prenotazione di capacità riservata del database di Azure per MySQL.
| Tipo di distribuzione | Il tipo di risorsa database di Azure per MySQL per cui si vuole acquistare la prenotazione.
| Livello di prestazioni | Livello di servizio per il database di Azure per i server MySQL.
| Termine | Un anno
| Quantità | Quantità di risorse di calcolo acquistate nella riserva di capacità riservata del database di Azure per MySQL. La quantità è un numero di Vcore nell'area di Azure selezionata e il livello di prestazioni che vengono riservati e otterranno lo sconto per la fatturazione. Ad esempio, se si esegue o si prevede di eseguire un database di Azure per i server MySQL con la capacità di calcolo totale di Quinta generazione 16 Vcore nell'area Stati Uniti orientali, è necessario specificare Quantity come 16 per ottimizzare il vantaggio per tutti i server.

## <a name="cancel-exchange-or-refund-reservations"></a>Annullare o scambiare le prenotazioni oppure chiedere il rimborso

È possibile annullare o scambiare le prenotazioni oppure chiederne il rimborso con determinate limitazioni. Per altre informazioni, vedere [scambi e rimborsi self-service per le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>Flessibilità delle dimensioni vCore

La flessibilità delle dimensioni vCore consente di aumentare o ridurre la quantità di risorse all'interno di un livello di prestazioni e di un'area, senza perdere il vantaggio della capacità riservata. 

## <a name="need-help--contact-us"></a>Serve aiuto? Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

Lo sconto per la prenotazione di vCore viene applicato automaticamente al numero di server di database di Azure per MySQL che corrispondono agli attributi e all'ambito di prenotazione della capacità riservata di database di Azure per MySQL. È possibile aggiornare l'ambito della prenotazione di capacità riservata del database di Azure per MySQL tramite portale di Azure, PowerShell, l'interfaccia della riga di comando o tramite l'API. </br></br>
Per informazioni su come gestire la capacità riservata del database di Azure per MySQL, vedere gestire la capacità riservata di database di Azure per MySQL.

Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:

* [Che cosa sono le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Gestire le prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Comprendere lo sconto sulle prenotazioni di Azure](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
* [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Prenotazioni di Azure nel programma Cloud Solution Provider (CSP) del Centro per i partner](https://docs.microsoft.com/partner-center/azure-reservations)

