---
title: Pubblicare l'offerta dell'applicazione Azure - Azure Marketplace
description: Descrive il processo e la procedura per la pubblicazione di un'offerta per un'applicazione Azure in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: dsindona
ms.openlocfilehash: 9f89e31c2d17ef74971d2057ba58e9572e92184c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280100"
---
# <a name="publish-azure-application-offer"></a>Pubblicare un'offerta di applicazione Azure

Dopo avere creato un'offerta specificando le informazioni nella pagina **Nuova offerta**, è possibile pubblicarla. Selezionare **Pubblica** per avviare il processo di pubblicazione.

Il diagramma seguente illustra i passaggi principali del processo di pubblicazione che rende un'offerta disponibile in Azure Marketplace.

![Procedura di pubblicazione dell'offerta](./media/offer-publishing-steps.png)


## <a name="detailed-description-of-publishing-steps"></a>Descrizione dettagliata dei passaggi per la pubblicazione

La tabella seguente elenca e illustra ogni passaggio della pubblicazione e indica una stima dei tempi per completare ogni passaggio.  Le stime espresse in "giorni" si intendono in giorni lavorativi, che non comprendono i fine settimana e i giorni festivi.

|  **Passaggio di pubblicazione**           | **Tempo**    | **Descrizione**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Convalida dei prerequisiti         | < 15 min    | Vengono convalidate le informazioni e le impostazioni dell'offerta.                        |
| Convalida delle impostazioni dei ricavi influenzate | < 15 min  | Viene verificata l'attribuzione dell'utilizzo delle risorse di Azure per l'offerta.             |
| Certificazione                  | < 1 giorno     | L'offerta viene analizzata dal team delle certificazioni di Azure. Viene eseguita l'analisi dell'offerta per individuare eventuali virus, malware, conformità alle normative sulla sicurezza e problemi di sicurezza. L'offerta viene controllata per verificare che soddisfi tutti i criteri di idoneità. Per altre informazioni, vedere la sezione [Prerequisiti](./cpp-prerequisites.md). Se viene rilevato un problema, viene fornito un feedback. |
| Convalida del test drive          | < 2 ore   | (Facoltativo) Se è presente un test drive, Microsoft verifica che possa essere distribuito e replicato.  |
| Creazione di pacchetti e registrazione per la generazione di clienti potenziali | < 1 ora  | Le risorse tecniche dell'offerta sono confezionate per l'utilizzo da parte dei clienti e i sistemi di piombo vengono configurati e distribuiti. |
|  Approvazione del server di pubblicazione             |  manual    | Revisione e conferma finali dell'editore prima che l'offerta diventi disponibile in Azure Marketplace. L'offerta è ora disponibile in anteprima.  È possibile distribuire l'offerta nelle sottoscrizioni selezionate (nei passaggi relativi alle informazioni sull'offerta) per verificare che soddisfi tutti i requisiti.  Dopo aver verificato l'offerta, selezionare **Go Live** in modo che l'offerta passi alla fase successiva. |
| Revisione Microsoft                | 7 - 14 giorni | Microsoft esamina interamente l'applicazione Azure e, in caso di problemi, invia un messaggio di posta elettronica.  La durata di questo passaggio dipende dalla complessità dell'applicazione, dai problemi rilevati e dalla rapidità con cui si reagisce.  |
| Live                           | < 1 giorno | L'offerta viene rilasciata, replicata nelle aree specificate e resa disponibile al pubblico. |
|   |&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|   |

È possibile monitorare il processo di pubblicazione nella scheda **Stato** per l'offerta nel portale Cloud Partner.

![Scheda Stato per l'offerta di un'app Azure](./media/offer-status-tab.png)

Al termine del processo di pubblicazione, l'offerta sarà disponibile nell'elenco della [categoria dell'applicazione di Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/).

>[!Note]
>L'opt-in del canale per i partner Cloud Solution Provider (CSP) è ora disponibile.  Per ulteriori informazioni sul marketing dell'offerta tramite i canali partner Microsoft CSP, consulta [Cloud Solution Provider.](../../cloud-solution-providers.md)

## <a name="errors-and-review-feedback"></a>Errori e feedback della revisione

Oltre a visualizzare lo stato della pubblicazione dell'offerta, la scheda **Stato** visualizza i messaggi di errore e il feedback da eventuali passaggi della pubblicazione in cui viene riscontrato un problema.  Se il problema è critico, la pubblicazione viene annullata.  È necessario risolvere i problemi segnalati e ripubblicare l'offerta.  Poiché il passaggio **Revisione Microsoft** rappresenta una revisione estesa dell'offerta e dei relativi asset tecnici associati (soprattutto il modello di Azure Resource Manager), i problemi vengono in genere visualizzati come collegamenti richiesta pull.  Per una spiegazione su come visualizzare e rispondere a queste richieste pull, vedere [Gestione del feedback di revisione](./cpp-handling-review-feedback.md).


## <a name="next-steps"></a>Passaggi successivi

Se si verificano errori in uno o più passaggi di pubblicazione, è necessario correggerli e ripubblicare l'offerta.  Se vengono rilevati problemi critici nel passaggio **Revisione Microsoft**, è necessario [gestire il feedback della revisione](./cpp-handling-review-feedback.md) accedendo al repository Azure DevOps del team di revisione Microsoft.

Dopo la pubblicazione di un'app Azure, è possibile [aggiornare l'offerta esistente](./cpp-update-existing-offer.md) in modo da riflettere le modifiche di requisiti tecnici o aziendali. 
