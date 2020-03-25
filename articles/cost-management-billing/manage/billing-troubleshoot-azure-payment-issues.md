---
title: Risolvere i problemi di pagamento di Azure
description: Risoluzione di un problema durante l'aggiornamento delle informazioni di pagamento nell'account nel portale di Microsoft Azure o nel Centro account.
author: v-miegge
ms.reviewerr: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 03/06/2020
ms.author: jaserano
ms.openlocfilehash: 3374fc948c2ed3c75108e49eb6129f3976f8c956
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202965"
---
# <a name="troubleshoot-azure-payment-issues"></a>Risolvere i problemi di pagamento di Azure

Si verifica un problema o un errore quando si prova ad aggiornare le informazioni di pagamento di un account nel portale di Microsoft Azure o nel Centro account di Azure.

Per risolvere il problema, selezionare l'argomento riportato di seguito che rispecchia maggiormente l'errore verificatosi.

## <a name="my-credit-card-was-declined-when-i-tried-to-sign-up-for-azure"></a>La carta di credito è stata rifiutata quando si è tentato di iscriversi ad Azure

Per risolvere i problemi riguardanti una carta rifiutata, vedere [Risolvere un problema di carta rifiutata all'iscrizione ad Azure](troubleshoot-declined-card.md).

## <a name="unable-to-see-subscriptions-under-my-account-to-update-the-payment-method"></a>Impossibile visualizzare le sottoscrizioni nell'account per aggiornare il metodo di pagamento

È possibile che si stia usando un ID di posta elettronica diverso da quello usato per le sottoscrizioni.

Per risolvere questo problema, vedere [Errore di accesso Non sono state trovate sottoscrizioni per il portale di Azure o il Centro account di Azure](no-subscriptions-found.md).

## <a name="unable-to-use-a-virtual-or-prepaid-credit-or-debit-card-as-a-payment-method"></a>Non è possibile usare una carta di credito o di debito prepagata o virtuale come metodo di pagamento.

*   Le carte di credito prepagate o virtuali non sono accettate come metodo di pagamento per le sottoscrizioni di Azure.
*   Le carte di debito non sono accettate come metodo di pagamento per le sottoscrizioni di Azure.

Per altre informazioni, vedere [Risolvere un problema di carta rifiutata all'iscrizione ad Azure](troubleshoot-declined-card.md).

## <a name="unable-to-remove-a-credit-card-from-a-saved-billing-payment-method"></a>Impossibile rimuovere una carta di credito da un metodo di pagamento salvato

Per impostazione predefinita non è possibile rimuovere una carta di credito dalla sottoscrizione attiva.

Se è necessario eliminare una carta esistente, è necessario aggiungerne una nuova alla sottoscrizione in modo che lo strumento di pagamento precedente possa essere eliminato correttamente oppure è necessario annullare la sottoscrizione. In tal modo la sottoscrizione viene eliminata definitivamente e la carta rimossa.

## <a name="unable-to-delete-an-old-payment-method-after-adding-a-new-payment-method"></a>Impossibile eliminare un vecchio metodo di pagamento dopo averne aggiunto uno nuovo

Il nuovo strumento di pagamento potrebbe non essere associato alla sottoscrizione. Per associare lo strumento di pagamento alla sottoscrizione, vedere [Aggiungere, aggiornare o rimuovere una carta di credito per Azure](change-credit-card.md).

## <a name="unable-to-delete-a-payment-method-because-of-cannot-delete-payment-method-error"></a>Impossibile eliminare un metodo di pagamento a causa del messaggio di errore *Impossibile eliminare il metodo di pagamento*

Ciò si verifica perché c'è un saldo scoperto. Risolvere eventuali saldi scoperti prima di eliminare il metodo di pagamento.

## <a name="unable-to-make-payment-for-a-subscription"></a>Impossibile effettuare il pagamento di una sottoscrizione

Se viene visualizzato il messaggio di errore seguente: *Il pagamento è scaduto. Si è verificato un problema con il metodo di pagamento* o *Siamo spiacenti, non è possibile salvare le informazioni. Chiudere il browser e riprovare.* , potrebbe esserci un pagamento in sospeso sulla carta, perché quest'ultima è stata rifiutata dall'ente finanziario.

Verificare che la carta di credito disponga di un saldo sufficiente per effettuare un pagamento. In caso contrario, usare un'altra carta per effettuare il pagamento o rivolgersi all'istituto finanziario per risolvere il problema.

Contattare la banca per i seguenti problemi:

- Le transazioni internazionali non sono abilitate.
- La carta ha un limite di credito e il saldo deve essere regolato.
- Sulla carta è abilitato un pagamento ricorrente.

## <a name="unable-to-change-payment-method-because-of-browser-issues-browser-does-not-respond-does-not-load-and-so-on"></a>Impossibile modificare il metodo di pagamento a causa di problemi del browser (il browser non risponde, non carica, e così via)

Disconnettersi da tutte le sessioni di Azure attive e quindi seguire la procedura descritta nell'articolo [Esplorare InPrivate in Microsoft Edge](https://support.microsoft.com/help/4026200/microsoft-edge-browse-inprivate) per avviare una sessione InPrivate in Microsoft Edge o Internet Explorer.

Nella sessione privata, seguire la procedura descritta in [come cambiare una carta di credito](change-credit-card.md) per aggiornare o modificare le informazioni sulla carta di credito.

È anche possibile provare a eseguire le operazioni seguenti:

- Aggiornare il browser
- Usare un altro browser
- Eliminare i cookie memorizzati nella cache

## <a name="my-subscription-is-still-disabled-after-updating-the-payment-method"></a>La sottoscrizione è ancora disabilitata dopo aver aggiornato il metodo di pagamento.

Questo problema si verifica perché c'è un saldo scoperto. Risolvere eventuali saldi scoperti prima di eliminare il metodo di pagamento.

## <a name="unable-to-change-payment-method-because-of-an-xml-error-response-page"></a>Impossibile modificare il metodo di pagamento a causa di un errore XML nella pagina di risposta

Si riceve questo messaggio se si usa il [portale di Azure](https://portal.azure.com/) per aggiungere una nuova carta di credito.

Per aggiungere i dati della carta, accedere al portale degli account di Azure con l'indirizzo di posta elettronica dell'amministratore dell'account.

## <a name="additional-help-resources"></a>Risorse della guida aggiuntive

Altri articoli sulla risoluzione dei problemi per la fatturazione e le sottoscrizioni di Azure

- [Carta rifiutata](troubleshoot-declined-card.md)
- [Problemi di accesso alla sottoscrizione](troubleshoot-sign-in-issue.md)
- [Non sono state trovate sottoscrizioni](no-subscriptions-found.md)
- [Vista dei costi Enterprise disabilitata](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contattare Microsoft per richiedere assistenza

In caso di domande o per assistenza, [creare una richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione sulla fatturazione di Azure](../../billing/index.md)
