---
title: Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure per il Contratto Microsoft Partner
description: Informazioni su come richiedere la proprietà della fatturazione delle sottoscrizioni di Azure da altri utenti per il Contratto Microsoft Partner.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/19/2020
ms.author: banders
ms.openlocfilehash: 0c5ecb61b5b34864aa89c1f0e760e96c383a269f
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091489"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Ottenere la proprietà della fatturazione delle sottoscrizioni di Azure dall'account del contratto Microsoft Partner

Per fornire una singola fattura combinata per i servizi gestiti e l'utilizzo di Azure, il provider CSP (Cloud Solution Provider) può acquisire la proprietà della fatturazione delle sottoscrizioni di Azure dai clienti con contratti Enterprise diretti.

Questa funzionalità è disponibile solo per i partner con fatturazione diretta CSP, certificati come [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp). È soggetta alla governance e ai criteri di Microsoft e potrebbe richiedere la revisione e l'approvazione per determinati clienti.

Per richiedere la proprietà della fatturazione, è necessario disporre del ruolo **Amministratore globale** o **Agenti amministratori**. Per altre informazioni, vedere [Centro per i partner - Assegnare autorizzazioni e ruoli utente](/partner-center/permissions-overview).

Le informazioni in questo articolo si applicano agli account di fatturazione per i contratti Microsoft Partner. Questi account vengono creati per i provider CSP e consentire loro di gestire la fatturazione per conto dei clienti nella nuova esperienza Commerce. La nuova esperienza è disponibile solo per i partner che hanno almeno un cliente che ha accettato un Contratto del cliente Microsoft con un piano di Azure. [Verificare di avere accesso a un Contratto Microsoft Partner](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Prerequisiti

1. Stabilire una [relazione come rivenditore](/partner-center/request-a-relationship-with-a-customer) con il cliente. Controllare la [panoramica delle autorizzazioni a livello di area per CSP](/partner-center/regional-authorization-overview) per assicurarsi che il tenant del cliente e il tenant del partner risiedano nelle stesse aree autorizzate.
1. [Confermare l'accettazione del cliente del Contratto del cliente Microsoft](/partner-center/confirm-customer-agreement).
1. Configurare un [piano di Azure](/partner-center/purchase-azure-plan) per il cliente. Se il cliente acquista attraverso più rivenditori, è necessario configurare un piano di Azure per ogni combinazione di cliente e rivenditore.

## <a name="request-billing-ownership"></a>Richiedere la proprietà della fatturazione

1. Accedere al [portale di Azure](https://portal.azure.com) mediante le credenziali di agente amministratore CSP nel tenant CSP.
1. Cercare **Gestione dei costi e fatturazione**.  
    ![Screenshot che illustra la ricerca di Gestione dei costi e fatturazione nel portale di Azure per richiedere la proprietà della fatturazione.](./media/mpa-request-ownership/search-cmb.png)
1. Selezionare **Clienti** sul lato sinistro e quindi selezionare un cliente dall'elenco.  
    [![Screenshot che mostra la selezione dei clienti](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. Selezionare **Richieste di trasferimento** sul lato sinistro e quindi **Aggiungi una nuova richiesta**.  
    [![Screenshot che mostra la selezione delle richieste di trasferimento](./media/mpa-request-ownership/mpa-select-transfer-requests.png)](./media/mpa-request-ownership/mpa-select-transfer-requests.png#lightbox)
1. Immettere l'indirizzo di posta elettronica dell'utente nell'organizzazione cliente che accetterà la richiesta di trasferimento. L'utente deve essere un proprietario dell'account con un contratto Enterprise. Selezionare **Invia la richiesta di trasferimento**.  
    [![Screenshot che mostra l'invio di una richiesta di trasferimento](./media/mpa-request-ownership/mpa-send-transfer-requests.png)](./media/mpa-request-ownership/mpa-send-transfer-requests.png#lightbox)
1. L'utente riceve un messaggio di posta elettronica con le istruzioni per esaminare la richiesta di trasferimento.  
    ![Screenshot che mostra il messaggio di posta elettronica per l'esame della richiesta di trasferimento](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)
1. Per approvare la richiesta di trasferimento, l'utente seleziona il collegamento nel messaggio di posta elettronica e segue le istruzioni.  
    [![Screenshot che mostra la verifica della richiesta di trasferimento](./media/mpa-request-ownership/review-transfer-requests.png)](./media/mpa-request-ownership/review-transfer-requests.png#lightbox) L'utente può selezionare l'account di fatturazione da cui vuole trasferire i prodotti di Azure. Dopo la selezione, vengono visualizzati i prodotti idonei che possono essere trasferiti. **Nota:** le sottoscrizioni disabilitate non possono essere trasferite e verranno mostrate nell'elenco "Non-transferrable Azure Products" (Prodotti di Azure non trasferibili), se applicabile. Dopo la selezione dei prodotti di Azure da trasferire, selezionare **Convalida**.
1. L'area **Transfer Validation Result** (Risultato della convalida del trasferimento) mostrerà l'impatto dei prodotti di Azure che verranno trasferiti. Ecco gli stati possibili:
    * **Superata** -La convalida per questo prodotto di Azure è stata superata ed è possibile trasferirlo.
    * **Avviso** - È presente un avviso per il prodotto di Azure selezionato. Benché sia comunque possibile trasferire il prodotto, il trasferimento avrà impatti di cui l'utente deve essere consapevole nel caso in cui voglia eseguire azioni di mitigazione. La sottoscrizione di Azure da trasferire sfrutta ad esempio i vantaggi di un'istanza riservata. Dopo il trasferimento la sottoscrizione non riceverà più tale vantaggio. Per massimizzare i risparmi, assicurarsi che l'istanza riservata sia associata a un'altra sottoscrizione che può usare i rispettivi vantaggi. L'utente può anche scegliere invece di tornare alla pagina di selezione e deselezionare questa sottoscrizione di Azure.
    * **Non riuscita** - Il prodotto di Azure selezionato non può essere trasferito a causa di un errore. L'utente dovrà tornare alla pagina di selezione e deselezionare il prodotto per trasferire gli altri prodotti di Azure selezionati.  
    ![Screenshot che mostra l'esperienza di convalida](./media/mpa-request-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Verificare lo stato della richiesta di trasferimento

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare **Gestione dei costi e fatturazione**.  
    ![Screenshot che illustra la ricerca di Gestione dei costi e fatturazione nel portale di Azure per richiedere lo stato del trasferimento.](./media/mpa-request-ownership/billing-search-cost-management-billing.png)
1. Selezionare **Clienti** sul lato sinistro.  
    [![Screenshot che mostra la selezione dei clienti](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. Selezionare il cliente nell'elenco per cui è stata inviata la richiesta di trasferimento.
1. Selezionare **Richieste di trasferimento** sul lato sinistro. Nella pagina Richieste di trasferimento vengono visualizzate le informazioni seguenti: [![Screenshot che mostra l'elenco delle richieste di trasferimento](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png#lightbox)

   |Colonna|Definizione|
   |---------|---------|
   |Data richiesta|Data di invio della richiesta di trasferimento|
   |Recipient|Indirizzo di posta elettronica dell'utente che ha inviato la richiesta di trasferimento della proprietà della fatturazione|
   |Expiration date|Data di scadenza della richiesta|
   |Stato|Stato della richiesta di trasferimento|

    Alla richiesta di trasferimento può essere associato uno degli stati seguenti:

   |Stato|Definizione|
   |---------|---------|
   |In corso|L'utente non ha accettato la richiesta di trasferimento|
   |Elaborazione in corso|L'utente ha approvato la richiesta di trasferimento. La fatturazione per le sottoscrizioni che l'utente ha selezionato verrà trasferita al proprio account|
   |Completi| La fatturazione per le sottoscrizioni che l'utente ha selezionato verrà trasferita al proprio account|
   |Completato con errori|La richiesta è stata completata ma non è stato possibile trasferire la fatturazione per alcune sottoscrizioni selezionate dall'utente|
   |Scaduto|L'utente non ha accettato la richiesta in tempo e la richiesta è scaduta|
   |Cancellati|Un utente con accesso alla richiesta di trasferimento ha annullato la richiesta|
   |Rifiutata|L'utente ha rifiutato la richiesta di trasferimento|

1. Selezionare una richiesta di trasferimento per visualizzare i dettagli. Nella pagina Dettagli del trasferimento vengono visualizzate le informazioni seguenti: [![Screenshot che mostra l'elenco delle sottoscrizioni trasferite](./media/mpa-request-ownership/mpa-transfer-completed.png)](./media/mpa-request-ownership/mpa-transfer-completed.png#lightbox)

   |Colonna  |Definizione|
   |---------|---------|
   |ID della richiesta di trasferimento|ID univoco della richiesta di trasferimento. Se si invia una richiesta di supporto, condividere l'ID con il supporto tecnico di Azure per velocizzarne la gestione|
   |Data della richiesta di trasferimento|Data di invio della richiesta di trasferimento|
   |Autore della richiesta di trasferimento|Indirizzo di posta elettronica dell'utente che ha inviato la richiesta di trasferimento|
   |Data di scadenza della richiesta di trasferimento| Data di scadenza della richiesta di trasferimento|
   |Indirizzo di posta elettronica del destinatario|Indirizzo di posta elettronica dell'utente che ha inviato la richiesta di trasferimento della proprietà della fatturazione|
   |Collegamento di trasferimento inviato al destinatario|URL inviato all'utente per esaminare la richiesta di trasferimento|

## <a name="supported-subscription-types"></a>Tipi di sottoscrizioni supportati

È possibile richiedere la proprietà della fatturazione dei tipi di sottoscrizione elencati di seguito.

* [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
* [Contratto Enterprise Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* È necessario convertire una sottoscrizione di sviluppo/test in un'offerta EA Enterprise tramite un ticket di supporto. Una sottoscrizione di sviluppo/test Enterprise viene fatturata in base a una tariffa con pagamento in base al consumo dopo il trasferimento. Qualsiasi sconto offerto tramite l'offerta Sviluppo/test Enterprise mediante il contratto Enterprise del cliente non sarà disponibile per il partner CSP.

## <a name="additional-information"></a>Informazioni aggiuntive

Nella sezione seguente sono disponibili informazioni aggiuntive sul trasferimento delle sottoscrizioni.

### <a name="no-service-downtime"></a>Non sono previsti tempi di inattività del servizio

L'esecuzione dei servizi di Azure della sottoscrizione continua senza interruzioni. Viene eseguita la transizione solo della relazione di fatturazione per le sottoscrizioni di Azure che l'utente sceglie di trasferire.

### <a name="disabled-subscriptions"></a>Sottoscrizioni disabilitate

Non è possibile trasferire le sottoscrizioni disabilitate. Per trasferire la proprietà della fatturazione, le sottoscrizioni devono trovarsi nello stato attivo.

### <a name="azure-resources-transfer"></a>Trasferimento delle risorse di Azure

Vengono trasferite tutte le risorse delle sottoscrizioni, come macchine virtuali, dischi e siti Web. Con il trasferimento, gli ID sottoscrizione e gli ID risorsa vengono preservati. 

### <a name="azure-marketplace-products-transfer"></a>Trasferimento di prodotti di Azure Marketplace

I prodotti di Azure Marketplace disponibili per le sottoscrizioni gestite tramite i partner CSP vengono trasferiti insieme alle sottoscrizioni corrispondenti. Le sottoscrizioni contenenti prodotti di Azure Marketplace non abilitati per i partner CSP non possono essere trasferite.

### <a name="azure-reservations-transfer"></a>Trasferimento di prenotazioni di Azure

Le prenotazioni di Azure non vengono spostate automaticamente con le sottoscrizioni. È possibile mantenere la prenotazione nel contratto Enterprise per altre sottoscrizioni o [annullare la prenotazione](../reservations/exchange-and-refund-azure-reservations.md) e consentire al partner di riacquistarla nel programma CSP.

### <a name="access-to-azure-services"></a>Accesso ai servizi di Azure

La transizione non influisce sull'accesso per utenti, gruppi o entità servizio esistenti assegnati con [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Il partner non otterrà alcun nuovo accesso con Controllo degli accessi in base al ruolo di Azure alle sottoscrizioni.

I partner devono collaborare con il cliente per ottenere l'accesso alle sottoscrizioni. I partner devono ottenere l'accesso [Amministra per conto terzi](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) o [Azure Lighthouse](../../lighthouse/concepts/cloud-solution-provider.md) per aprire ticket di supporto.

### <a name="azure-support-plan"></a>Piano di supporto tecnico di Azure

Il supporto tecnico di Azure non viene trasferito con le sottoscrizioni. Se l'utente trasferisce tutte le sottoscrizioni di Azure, chiedere di annullare il relativo piano di supporto tecnico. Dopo il trasferimento, il partner CSP è responsabile del supporto tecnico. Il cliente deve collaborare con il partner CSP per qualsiasi richiesta di supporto.  

### <a name="charges-for-transferred-subscription"></a>Addebiti per la sottoscrizione trasferita

Il proprietario originale della fatturazione delle sottoscrizioni è responsabile degli eventuali addebiti segnalati fino al completamento del trasferimento. L'utente è responsabile per gli addebiti registrati a partire dal momento del trasferimento. Potrebbero esserci addebiti relativi a un periodo precedente al trasferimento, ma che sono stati segnalati successivamente. Questi addebiti verranno visualizzati nella fattura.

### <a name="cancel-a-transfer-request"></a>Annullare una richiesta di trasferimento

È possibile annullare la richiesta di trasferimento finché la richiesta non viene approvata o rifiutata. Per annullare la richiesta di trasferimento, passare alla [pagina Dettagli del trasferimento](#check-the-transfer-request-status) e selezionare Annulla nella parte inferiore della pagina.

### <a name="software-as-a-service-saas-transfer"></a>Trasferimento di prodotti SaaS

I prodotti SaaS non vengono trasferiti con le sottoscrizioni. Chiedere all'utente di [contattare il supporto tecnico di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per trasferire la proprietà della fatturazione di prodotti SaaS. Unitamente alla proprietà della fatturazione, l'utente può trasferire anche la proprietà delle risorse. La proprietà delle risorse consente di eseguire operazioni di gestione come l'eliminazione e la visualizzazione dei dettagli del prodotto. Per trasferire la proprietà delle risorse, l'utente deve essere un proprietario delle risorse nel prodotto SaaS.

### <a name="additional-approval-for-certain-customers"></a>Approvazione aggiuntiva per determinati clienti

Alcune richieste di transizione dei clienti possono richiedere un processo di revisione aggiuntiva con Microsoft a causa della natura della struttura di registrazione Enterprise corrente del cliente. Il partner riceverà una notifica in merito a tali requisiti quando prova a inviare un invito ai clienti. I partner devono collaborare con il responsabile dello sviluppo dei partner e con il team dell'account del cliente per completare il processo di revisione.

### <a name="azure-subscription-directory"></a>Directory della sottoscrizione di Azure

La directory delle sottoscrizioni di Azure da trasferire deve corrispondere alla directory del cliente che è stata selezionata durante la creazione della relazione CSP.

Se queste due directory non corrispondono, non è possibile trasferire le sottoscrizioni. È necessario stabilire una nuova relazione rivenditore CSP con il cliente selezionando la directory delle sottoscrizioni di Azure o modificando la directory delle sottoscrizioni di Azure in modo che corrisponda alla directory della relazione CSP del cliente. Per altre informazioni, vedere [Associare una sottoscrizione esistente alla directory di Azure AD](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory).

### <a name="ea-subscription-in-the-non-organization-directory"></a>Sottoscrizione con contratto Enterprise nella directory non dell'organizzazione

Le sottoscrizioni con Contratto Enterprise da directory non dell'organizzazione possono essere trasferite purché per la directory sia disponibile una relazione come rivenditore con il programma CSP. Se per la directory non è disponibile una relazione come rivenditore, è necessario assicurarsi che l'utente dell'organizzazione sia presente nella directory come *amministratore globale* che può accettare la relazione come partner. La parte relativa al nome di dominio del nome utente deve essere il nome di dominio predefinito iniziale "[nome dominio]. onmicrosoft.com" o un nome di dominio personalizzato non federato verificato, ad esempio "contoso.com".  

Per aggiungere un nuovo utente alla directory, vedere [Avvio rapido: Aggiungere nuovi utenti ad Azure Active Directory per aggiungere il nuovo utente alla directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Verificare l'accesso a un Contratto Microsoft Partner

[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Richiesta di assistenza Contattare il supporto tecnico

Se si necessita assistenza, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per ottenere una rapida risoluzione del problema.

## <a name="next-steps"></a>Passaggi successivi

* La proprietà della fatturazione delle sottoscrizioni di Azure è stata trasferita all'utente. È possibile tenere traccia degli addebiti per queste sottoscrizioni nel [portale di Azure](https://portal.azure.com).
* Collaborare con il cliente per ottenere l'accesso alle sottoscrizioni di Azure trasferite. [Assegnare i ruoli di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md).