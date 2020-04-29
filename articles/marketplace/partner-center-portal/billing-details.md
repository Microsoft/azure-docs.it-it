---
title: Fatturazione del Marketplace commerciale | Azure Marketplace
description: Questo articolo illustra gli argomenti correlati al commercio per il Marketplace commerciale.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.topic: guide
ms.date: 12/12/2019
ms.openlocfilehash: a0912e1dca63fabfe6bc546305824a1c582b9a2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80279743"
---
# <a name="commercial-marketplace-billing"></a>Fatturazione del marketplace commerciale

Questo articolo descrive gli argomenti correlati al commercio per il Marketplace commerciale:

- [Opzioni di pubblicazione nel marketplace](#marketplace-publishing-options)
- [Panoramica generale sulle transazioni](#transact-general-overview)
- [Modelli di fatturazione delle transazioni](#transact-billing-models)

## <a name="marketplace-publishing-options"></a>Opzioni di pubblicazione nel marketplace

Marketplace commerciale offre diverse opzioni di pubblicazione per gli editori.

### <a name="list-and-trial-publishing-options"></a>Opzioni di pubblicazione elenco e versione di valutazione

Gli editori possono sfruttare le opzioni di pubblicazione list, Trial e Bring your own License (BYOL) per scopi promozionali e di acquisizione utente. Con queste opzioni, Microsoft non partecipa direttamente alle transazioni di licenza software dell'editore e non è prevista alcuna tariffa per le transazioni. Gli editori sono responsabili del supporto di tutti gli aspetti della transazione della licenza software, inclusi, a titolo esemplificativo: ordine, evasione dell'ordine, misurazione, fatturazione, pagamento e raccolta. Con le opzioni di pubblicazione di inserzioni e valutazioni, gli editori trattengono il 100% dei costi di licenza del software dell'editore pagati dal cliente.

### <a name="transact-publishing-option"></a>Opzione di pubblicazione delle transazioni

Oltre alle opzioni di pubblicazione dell'elenco e della versione di valutazione, l'opzione di pubblicazione Transact è disponibile per i Publisher. Questa opzione sfrutta le funzionalità commerciali disponibili a livello globale di Microsoft e consente a Microsoft di ospitare transazioni del Marketplace cloud per conto dell'editore.

## <a name="transact-general-overview"></a>Panoramica generale sulle transazioni

Quando si usa l'opzione di pubblicazione Transact, Microsoft consente la vendita di software di terze parti e la distribuzione di alcuni tipi di offerta alla sottoscrizione di Azure del cliente. Quando si seleziona un modello di fatturazione e un tipo di offerta, l'autore deve considerare la fatturazione dei costi dell'infrastruttura e i costi di licenza software dell'editore.

L'opzione di pubblicazione Transact è attualmente supportata per i tipi di offerta seguenti: macchine virtuali, applicazioni Azure e applicazioni SaaS.

![Transazioni in Azure Marketplace](./media/transact-amp.png)

### <a name="billing-infrastructure-costs"></a>Fatturazione dei costi per l'infrastruttura

#### <a name="for-virtual-machines-and-azure-applications"></a>Per le macchine virtuali e le applicazioni Azure

Per le macchine virtuali e le applicazioni Azure, le tariffe di utilizzo dell'infrastruttura di Azure vengono fatturate alla sottoscrizione di Azure del cliente. I prezzi per l'utilizzo dell'infrastruttura vengono addebitati e presentati separatamente rispetto ai costi di licenza del provider software per la fattura del cliente.

#### <a name="for-saas-apps"></a>Per le app SaaS

Per le app SaaS, l'editore deve contabilizzare i costi di utilizzo dell'infrastruttura di Azure e i costi di licenza software sotto un'unica voce di costo, Viene rappresentata come una tariffa fissa per il cliente. L'utilizzo dell'infrastruttura di Azure viene gestita e fatturata direttamente al partner. I costi effettivi di utilizzo dell'infrastruttura di Azure non sono visibili al cliente. Gli editori scelgono in genere di accorpare i costi di utilizzo dell'infrastruttura di Azure nei prezzi di licenza software. I costi di licenza software non vengono calcolati in base al consumo.

## <a name="transact-billing-models"></a>Modelli di fatturazione delle transazioni

A seconda dell'opzione di transazione utilizzata, i costi di licenza software dell'editore possono essere presentati come segue:

- *Gratuito*: nessun addebito per le licenze software.
- *Bring your own License (BYOL)*: tutti gli addebiti applicabili per le licenze software vengono gestiti direttamente tra l'editore e il cliente. Microsoft gestisce solo i costi di utilizzo dell'infrastruttura di Azure (solo macchine virtuali e applicazioni Azure).
- *Pagamento in*base al consumo: le tariffe per le licenze software vengono presentate come tariffe per ogni ora, per core (vCPU) in base all'infrastruttura di Azure usata. Questo modello si applica solo alle macchine virtuali e alle applicazioni Azure.
- *Prezzi per le sottoscrizioni*: le tariffe per le licenze software sono presentate come tariffe mensili o annuali, spese ricorrenti fatturate come tariffe forfettarie o per postazione. Questo modello si applica solo alle app SaaS e alle app gestite da Azure.
- *Versione di valutazione software gratuita*: nessun addebito per le licenze software per 30 giorni o 90 giorni.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Prezzi gratuiti e Bring Your Own License (BYOL)

Quando si pubblica un'offerta di transazione gratuita o Bring Your Own License, Microsoft non svolge alcun ruolo nell'agevolazione delle transazioni di vendita per i costi di licenza software. Come per le opzioni di pubblicazione di inserzione e valutazione, l'editore trattiene il 100% dei costi di licenza software.

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Prezzi con pagamento a consumo e delle sottoscrizioni (basati sul sito)

Quando si pubblica un'offerta di transazione con pagamento in base al consumo o sottoscrizione, Microsoft fornisce la tecnologia e i servizi per l'elaborazione di acquisti di licenze software, di restituzione e di addebito. In questo scenario l'editore autorizza Microsoft ad agire in veste di agente per questi scopi. L'editore consente a Microsoft di agevolare le transazioni delle licenze software, mantenendone la designazione di venditore, provider, distributore e licenziante.

Microsoft consente ai clienti di ordinare, concedere in licenza e utilizzare il software di pubblicazione, in base alle condizioni del Marketplace commerciale di Microsoft e del contratto di licenza con l'utente finale dell'editore. Gli editori devono fornire il proprio contratto di licenza con l'utente finale o selezionare il [contratto standard](https://docs.microsoft.com/azure/marketplace/standard-contract) durante la creazione dell'offerta.

### <a name="free-software-trials"></a>Versioni di valutazione gratuite del software

Per gli scenari di pubblicazione a livello di transazione, l'editore può rendere una licenza software disponibile gratuitamente per 30 o 90 giorni. Questa possibilità di sconto non include il costo dell'utilizzo dell'infrastruttura di Azure, che dipende dall'uso della soluzione partner.

### <a name="private-offers"></a>Offerte private

Oltre a usare i tipi di offerta e i modelli di fatturazione per monetizzare un'offerta, gli editori possono eseguire transazioni di un'offerta privata, completate con prezzi negoziati e specifici per le transazioni o configurazioni personalizzate. Le offerte private sono supportate da tutte e 3 le opzioni di pubblicazione delle transazioni.

Questa opzione consente un prezzo superiore o inferiore rispetto all'offerta pubblicamente disponibile. Le offerte private possono essere usate per lo sconto o l'aggiunta di una Premium per un'offerta. Le offerte private possono essere rese disponibili per uno o più clienti aggiungendo all'elenco elementi consentiti la sottoscrizione di Azure a livello di offerta.

### <a name="examples"></a>Esempi

#### <a name="pay-as-you-go"></a>Pagamento in base al consumo

* Se si abilita l'opzione con pagamento in base al consumo, la struttura dei costi è la seguente.

|Costo della licenza  | € 1,00 all'ora  |
|---------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    |   € 0,14 all'ora     |
|*Importo addebitato da Microsoft al cliente*    |  *€ 1,14 all'ora*       |

* In questo scenario Microsoft addebita 1,14 euro all'ora per l'uso dell'immagine di VM pubblicata.

|Microsoft addebita  | € 1,14 all'ora  |
|---------|---------|
|Microsoft paga l'80% del costo della licenza|   € 0,80 all'ora     |
|Microsoft trattiene il 20% del costo della licenza  |  € 0,20 all'ora       |
|Microsoft trattiene il 100% del costo dell'utilizzo di Azure | € 0,14 all'ora |

### <a name="bring-your-own-license-byol"></a>Bring your own License (BYOL)

* Se si abilita l'opzione BYOL, la struttura dei costi è la seguente.

|Costo della licenza  | Il costo della licenza viene concordato e addebitato dall'utente  |
|---------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    |   € 0,14 all'ora     |
|*Importo addebitato da Microsoft al cliente*    |  *€ 0,14 all'ora*       |

* In questo scenario Microsoft addebita 0,14 euro all'ora per l'uso dell'immagine di VM pubblicata.

|Microsoft addebita  | € 0,14 all'ora  |
|---------|---------|
|Microsoft trattiene il costo dell'utilizzo di Azure    |   € 0,14 all'ora     |
|Microsoft trattiene lo 0% del costo della licenza   |  € 0,00 all'ora       |

### <a name="saas-app-subscription"></a>Sottoscrizione app SaaS

Questa opzione deve essere configurata per la vendita tramite Microsoft e può essere addebitata a una tariffa fissa o a un utente su base mensile o annuale.

• Se si Abilita l'opzione Vendi tramite Microsoft per un'offerta SaaS, si avrà la seguente struttura di costo.

|Costo della licenza       | $ 100,00 al mese  |
|--------------|---------|
|Costo dell'utilizzo di Azure (D1/1-Core)    | Costi addebitati direttamente all'editore, non al cliente |
|*Importo addebitato da Microsoft al cliente*    |  *$ 100,00 al mese. Nota: l'editore deve calcolare i costi di infrastruttura sostenuti o trasmessi nei costi di licenza*  |

* In questo scenario Microsoft addebita $ 100,00 per la licenza software e paga $ 80,00 all'editore.
* Ai partner qualificati per la tariffa ridotta del servizio Marketplace verrà visualizzato un importo ridotto delle transazioni sulle offerte SaaS, dal 2019 maggio fino al giugno 2020. In questo scenario Microsoft fattura $100,00 per la licenza software e paga $90,00 all'editore.

|Microsoft addebita  | $ 100,00 al mese  |
|---------|---------|
|Microsoft paga l'80% del costo della licenza <br> \*Microsoft paga il 90% del costo della licenza per qualsiasi app SaaS qualificata   |   $ 80,00 al mese <br> \*$90,00 al mese    |
|Microsoft trattiene il 20% del costo della licenza <br> \*Microsoft mantiene il 10% del costo della licenza per le app SaaS qualificate.  |  $ 20,00 al mese <br> \*$10,00     |

**Costo del servizio Marketplace ridotto:** Per determinati prodotti SaaS pubblicati sul Marketplace commerciale, Microsoft ridurrà il costo del servizio Marketplace dal 20% (come descritto nel contratto Microsoft Publisher) al 10%.  Affinché il prodotto sia idoneo, è necessario che almeno uno dei prodotti sia designato da Microsoft come indirizzo di co-selling IP predisposto o co-selling IP con priorità. Per ricevere la tariffa per il servizio Marketplace ridotta per il mese, è necessario che l'idoneità soddisfi almeno cinque (5) giorni lavorativi prima della fine del mese di calendario precedente. Una tariffa ridotta del servizio Marketplace non si applica alle macchine virtuali, alle app gestite o ad altri prodotti resi disponibili tramite il Marketplace commerciale.  Questa tariffa per il servizio Marketplace ridotta sarà disponibile per le offerte qualificate, con i costi di licenza raccolti da Microsoft tra il 1 ° maggio 2019 e il 30 giugno 2020.  Dopo tale periodo di tempo, la tariffa del servizio Marketplace tornerà al valore normale.
