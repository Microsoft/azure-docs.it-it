---
title: Risolvere i problemi di iscrizione a un nuovo account nel portale di Azure o nel Centro account di Azure
description: Risoluzione di un problema che si verifica quando si prova a effettuare l'iscrizione a un nuovo account nel portale di Microsoft Azure o nel Centro account di Azure.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 7bcf3197ae218c469983200814cecf551dc42a6b
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133176"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Risolvere i problemi di iscrizione a un nuovo account nel portale di Azure o nel Centro account di Azure

Si verifica un problema quando si prova a effettuare l'iscrizione a un nuovo account nel portale di Microsoft Azure o nel Centro account di Azure. Prima di risolvere il problema, effettuare prima le verifiche seguenti:

- Le informazioni fornite per il profilo dell'account Azure, ad esempio indirizzo di posta elettronica di contatto, indirizzo e numero di telefono, sono corrette.
- Le informazioni della carta di credito sono corrette.
- Non è già presente un account Microsoft con le stesse informazioni.

> [!NOTE]
> Se si ha già un account esistente e si cercano indicazioni per la risoluzione dei problemi di accesso, vedere [Risolvere i problemi di accesso alla sottoscrizione di Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/troubleshoot-sign-in-issue).

## <a name="resolutions"></a>Soluzioni

Per risolvere eventuali errori, selezionare il problema che si verifica quando si prova a eseguire l'iscrizione ad Azure.

### <a name="error-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>Errore: *Non è possibile procedere con l'iscrizione a causa di un problema con l'account. Contattare il supporto tecnico.*

Per risolvere il problema, seguire questa procedura:

1. Accedere al [Centro account di Azure](https://account.azure.com/Profile) usando le credenziali di amministratore account.

2. Selezionare **Modifica dettagli**.

3. Assicurarsi che tutti i campi relativi all'indirizzo siano completati e validi.

4. Quando si esegue l'iscrizione per la sottoscrizione di Azure, assicurarsi che l'indirizzo di fatturazione relativo alla registrazione della carta di credito corrisponda ai dati bancari.

Se si continua a ricevere il messaggio di errore, provare a usare un browser diverso per l'iscrizione.

### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>L'indicatore di stato si blocca nella sezione *Verifica dell'identità mediante smart card*.

Per completare la verifica dell'identità mediante smart card, è necessario che il browser consenta i cookie di terze parti.

![Verifica dell'identità mediante smart card](./media/troubleshoot-azure-sign-up/identify-verification-by-card.png)

Seguire questa procedura per aggiornare le impostazioni relative ai cookie del browser.

1. Se si usa Chrome, selezionare **Impostazioni** > **Mostra impostazioni avanzate** > **Privacy** > **Impostazioni contenuti**. Deselezionare **Blocca cookie di terze parti e dati dei siti**.

2. Se si usa Microsoft Edge, selezionare **Impostazioni** > **Visualizza impostazioni avanzate** > **Cookie** > **Non bloccare i cookie**.

3. Aggiornare la pagina di iscrizione ad Azure e controllare se il problema è risolto.

4. Se il problema persiste, chiudere e riavviare il browser, quindi riprovare.

### <a name="credit-card-form-doesnt-support-my-billing-address"></a>Il modulo della carta di credito non supporta il mio indirizzo di fatturazione

L'indirizzo di fatturazione deve trovarsi nel paese selezionato nella sezione **Info sull'utente**. Assicurarsi di selezionare il paese corretto.

### <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>Assenza di messaggi di testo o chiamate durante la verifica dell'account di sottoscrizione

Anche se il processo è in genere rapido, il recapito del codice di verifica potrebbe richiedere un massimo di quattro minuti. Il numero di telefono immesso per la verifica non viene archiviato come numero di contatto per l'account.

Altri suggerimenti:

- Non è possibile usare un numero di telefono VoIP (Voice-over-IP) per il processo di verifica tramite telefono.
- Verificare il numero di telefono immesso, incluso il prefisso internazionale selezionato nel menu a discesa.
- Se il telefono non può ricevere SMS, provare l'opzione **Chiama**.
- Assicurarsi che il telefono possa ricevere chiamate o messaggi SMS da un numero proveniente dagli Stati Uniti.

Quando si riceve l'SMS o la chiamata, immettere il codice ricevuto nella casella di testo.

### <a name="credit-card-declined-or-not-accepted"></a>Carta di credito rifiutata o non accettata

Le carte di credito o debito virtuali o prepagate non sono accettate come opzione di pagamento per le sottoscrizioni di Azure. Per informazioni sugli altri motivi per cui la carta può essere rifiutata, vedere [La carta di debito o credito viene rifiutata al momento dell'iscrizione ad Azure](https://support.microsoft.com/help/4042960).

### <a name="free-trial-is-not-available"></a>La versione di valutazione gratuita non è disponibile

È stata usata una sottoscrizione di Azure in passato? Le condizioni per l'utilizzo di Azure prevedono un limite di una sola attivazione della versione di valutazione gratuita per un utente che non ha mai usato Azure in precedenza. Se quindi si è già usato un qualsiasi altro tipo di sottoscrizione di Azure, non è possibile attivare una versione di valutazione gratuita. Considerare l'iscrizione per una [sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/).

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Ho visto un addebito sul mio account della versione di valutazione gratuita

Dopo l'iscrizione sulla carta di credito potrebbe essere addebitato un importo a scopo di verifica. Questo importo viene rimosso entro tre-cinque giorni. Se si è preoccupati della gestione dei costi, leggere altre informazioni per [evitare costi imprevisti](getting-started.md).

### <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>Non è possibile attivare un piano con vantaggi di Azure, ad esempio MSDN, BizSpark, BizSparkPlus o MPN

Assicurarsi di usare le credenziali di accesso corrette. Controllare quindi il programma dei vantaggi per assicurarsi di averne diritto.

- MSDN
  - Verificare lo stato di idoneità nella [pagina dell'account MSDN](https://msdn.microsoft.com/subscriptions/manage/default.aspx).
  - Se non è possibile verificare lo stato, contattare i [centri del Servizio clienti per abbonamenti MSDN](https://msdn.microsoft.com/library/aa493452.aspx).
- Microsoft for Startups
  - Accedere al [portale di Microsoft for Startups](https://startups.microsoft.com/#start-two) per verificare il proprio stato di idoneità per Microsoft for Startups.
  - Se non è possibile verificare lo stato, è possibile ottenere assistenza nei [forum di Microsoft for Startups](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Accedere al [portale di MPN](https://mspartner.microsoft.com/Pages/Locale.aspx) per verificare lo stato di idoneità. È possibile usufruire di vantaggi aggiuntivi se si hanno le [competenze Cloud Platform](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx)appropriate.
  - Se non è possibile verificare lo stato, contattare il [supporto tecnico di MPN](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Non è possibile attivare una nuova sottoscrizione di Azure in Open

Per creare una sottoscrizione di Azure in Open, è necessaria una chiave di attivazione del servizio online (OSA, Online Service Activation) valida a cui è associato almeno un token di Azure in Open. Se non è disponibile una chiave di attivazione del servizio online, contattare uno dei partner Microsoft elencati in [Microsoft Pinpoint](https://pinpoint.microsoft.com/).

### <a name="error-you-are-not-eligible-for-an-azure-subscription"></a>Errore: *Non si è idonei per una sottoscrizione di Azure*

Per risolvere questo problema, effettuare le verifiche seguenti:

- Le informazioni fornite per il profilo dell'account di Azure, ad esempio indirizzo di posta elettronica di contatto, indirizzo e numero di telefono, sono corrette.
- Le informazioni della carta di credito sono corrette.
- Non è già presente un account Microsoft che usa le stesse informazioni.

### <a name="error-your-current-account-type-is-not-supported"></a>Errore: *Il tipo di account corrente non è supportato*

Questo problema può verificarsi se l'account è registrato in una [directory di Azure AD non gestita](../../active-directory/users-groups-roles/directory-self-service-signup.md) e non è presente nella directory di Azure AD dell'organizzazione.

Per risolvere questo problema, iscrivere l'account Azure usando un altro account o assumere la proprietà della directory di Azure AD non gestita. Per altre informazioni, vedere [Acquisire la proprietà di una directory non gestita come amministratore in Azure Active Directory](../../active-directory/users-groups-roles/domains-admin-takeover.md).

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
