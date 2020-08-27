---
title: Comprendere la fattura di Azure
description: Informazioni su come leggere e comprendere l'utilizzo e la fattura per la sottoscrizione di Azure
author: bandersmsft
ms.reviewer: jureid
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: b7b115f909c57470bfbdab9cc44e6a258f85b8f1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685919"
---
# <a name="understand-terms-on-your-microsoft-azure-invoice"></a>Comprendere i termini sulla fattura di Microsoft Azure

La fattura presenta un riepilogo degli addebiti e fornisce le istruzioni per il pagamento. È disponibile per il download in formato PDF (Portable Document Format) dal [Portale di Azure](https://portal.azure.com/). In alternativa, è possibile riceverla tramite posta elettronica. Per altre informazioni, vedere [How to get your Azure billing invoice and daily usage data](../manage/download-azure-invoice-daily-usage-date.md) (Come ottenere la fattura e i dati di utilizzo giornalieri di Azure).

Alcuni punti di cui tenere conto:

-   Se si sta usando una sottoscrizione di prova gratuita, è possibile ottenere le informazioni dettagliate sull'utilizzo dal Portale di Azure, ma la fattura non viene emessa.

-   Nella fattura corrente possono essere presenti fino a 24 ore di utilizzo riferite alla fine del periodo di fatturazione precedente.

-   Gli addebiti elencati negli estratti conto per i clienti internazionali sono solo a scopo di valutazione. Le banche possono avere costi diversi per i tassi di conversione.

>[!VIDEO https://www.youtube.com/embed/jWG1lyJe3Mg]

## <a name="detailed-terms-and-descriptions-of-your-invoice"></a>Dettagli sui termini e sulle descrizioni nella fattura
Le sezioni seguenti riportano i termini importanti presenti sulle fatture e le descrizioni per ogni termine.

### <a name="account-information"></a>Informazioni sull'account

La sezione della fattura relativa alle informazioni sull'account, nella parte superiore della prima pagina, presenta le informazioni sul profilo e sulla sottoscrizione.

![Sezione della fattura relativa alle informazioni sull'account](./media/understand-invoice/account-information-section.png)

| Termine | Descrizione |
| --- | --- |
| Numero d'ordine d'acquisto |Numero d'ordine d'acquisto, facoltativo, assegnato dall'utente per il monitoraggio |
| N. fattura |Numero di fattura univoco, generato automaticamente da Microsoft, usato a scopo di monitoraggio |
| Ciclo di fatturazione |Periodo a cui la fattura si riferisce |
| Data fattura |Data di generazione della fattura, di solito il giorno dopo la fine del ciclo di fatturazione |
| Metodo di pagamento |Tipo di pagamento usato per l'account, ovvero addebito con fattura o carta di credito. |
| Indirizzo di fatturazione |Indirizzo di fatturazione indicato per l'account |
| Offerta di sottoscrizione, con pagamento in base al consumo |Tipo di offerta di sottoscrizione che è stato acquistato, ossia con pagamento in base al consumo, BizSpark Plus, Azure Pass e così via. Per altre informazioni, vedere [Tipi di offerte per Azure](https://azure.microsoft.com/support/legal/offer-details/). |
| Indirizzo di posta elettronica proprietario account | Indirizzo di posta elettronica dell'account associato all'account Microsoft Azure. <br /><br />Per cambiare l'indirizzo di posta elettronica, vedere [Modificare le informazioni sul profilo dell'account di Azure, ad esempio indirizzo di posta elettronica, indirizzo e numero di telefono di contatto](../manage/change-azure-account-profile.md). |

### <a name="understand-the-invoice-summary"></a>Comprendere il riepilogo della fattura
La sezione di **riepilogo della fattura**elenca gli importi totali delle transazioni avvenute dall'ultimo periodo di fatturazione e gli addebiti per l'utilizzo corrente.

![Sezione di riepilogo della fattura](./media/understand-invoice/invoice-summary-section.png)

Il nome della sottoscrizione ("Production Storage") è il nome della sottoscrizione per questa fattura.

#### <a name="understand-the-previous-charges"></a>Comprendere gli addebiti precedenti
La sezione della fattura relativa a saldo precedente, pagamenti e saldo inevaso riepiloga le transazioni avvenute dall'ultimo periodo di fatturazione.

| Termine | Descrizione |
| --- | --- |
| Saldo precedente |Importo totale dovuto dall'ultimo periodo di fatturazione |
| Pagamenti |Pagamenti e crediti totali applicati all'ultimo periodo di fatturazione |
| Saldo in sospeso (dal ciclo di fatturazione precedente) |Eventuali crediti o eventuale saldo residuo per l'account dall'ultimo periodo di fatturazione |

#### <a name="understand-the-current-charges"></a>Comprendere gli addebiti attuali
La sezione Addebiti correnti della fattura riporta informazioni dettagliate sugli addebiti mensili per il periodo di fatturazione corrente.

| Termine | Descrizione |
| --- | --- |
| Addebiti per utilizzo |Gli addebiti per utilizzo sono gli addebiti mensili totali per una sottoscrizione per il periodo di fatturazione corrente|
| Sconti |Sconti sui servizi applicati al periodo di fatturazione corrente|
| Rettifiche |Crediti vari (utilizzo gratuito, crediti e così via) o addebiti in sospeso applicati al periodo di fatturazione corrente.<br/><br/>Ad esempio, se è stata attivata l'offerta Visual Studio Enterprise con MSDN, sarà indicato un credito mensile. Se si annulla la sottoscrizione, vengono riportati tutti gli addebiti di utilizzo mensile che superano il credito mensile ottenuto con l'offerta di sottoscrizione. Gli addebiti vengono conteggiati dall'inizio del periodo di fatturazione corrente fino alla data di annullamento della sottoscrizione. |

#### <a name="sold-to-and-payment-instructions"></a>Istruzioni "venduto a" e di pagamento

La tabella seguente descrive le istruzioni "venduto a" e di pagamento riportate nella seconda pagina della fattura.

| Termine |Descrizione |
| --- | --- |
| Venduto a |Indirizzo del profilo nell'account. <br/><br/>Se è necessario cambiare l'indirizzo, vedere [Modificare le informazioni sul profilo dell'account di Azure, ad esempio indirizzo di posta elettronica, indirizzo e numero di telefono di contatto](../manage/change-azure-account-profile.md).|
| Istruzioni per il pagamento |Istruzioni per l'esecuzione del pagamento in base al metodo di pagamento, ad esempio carta di credito o contro fattura. |

#### <a name="usage-charges"></a>Addebiti per utilizzo

La sezione della fattura relativa agli addebiti per utilizzo riporta le informazioni sugli addebiti a livello di contatore.

![Sezione degli addebiti per utilizzo](./media/understand-invoice/usage-charges-section.png)

La tabella seguente descrive le intestazioni delle colonne relative agli addebiti per utilizzo presenti nella fattura.

| Termine |Descrizione |
| --- | --- |
| Nome |Identifica il servizio di primo livello a cui si riferisce l'utilizzo |
| Type |Definisce il tipo di servizio di Azure che può influire sulla tariffa |
| Risorsa |Identifica l'unità di misura del contatore utilizzato |
| Region |Identifica la posizione del centro dati per i servizi il cui prezzo dipende dalla posizione stessa del centro dati |
| Consumato |Contiene la quantità riportata dal contatore usata durante il periodo di fatturazione |
| Incluso |Contiene la quantità riportata dal contatore inclusa gratuitamente nel periodo di fatturazione corrente |
| Fatturabile |Mostra la differenza tra la quantità utilizzata e la quantità inclusa. Sarà addebitato questo importo. Per le offerte di pagamento in base al consumo senza alcuna quantità inclusa, questo totale corrisponde a quello della quantità consumata |
| Tariffa |Indica la tariffa addebitata per unità fatturabile |
| valore |Indica il risultato della moltiplicazione della colonna Quantità in eccesso per la colonna Tariffa. Se la quantità consumata non supera la quantità inclusa, non è indicato alcun addebito in questa colonna. |
| Subtotale |Somma di tutti gli addebiti al lordo dell'imposta per il periodo di fatturazione |
| Grand Total |Somma di tutti gli addebiti al netto dell'imposta per il periodo di fatturazione |

## <a name="how-do-i-make-sure-that-the-charges-in-my-invoice-are-correct"></a>Come posso assicurarmi che gli addebiti nella fattura siano corretti?
Se nella fattura è presente un addebito su cui si vogliono ricevere altre informazioni, vedere [Comprendere la fattura per Microsoft Azure](review-individual-bill.md)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
