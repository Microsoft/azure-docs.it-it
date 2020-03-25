---
title: Risolvere i problemi di accesso alla sottoscrizione di Azure
description: Questo articolo aiuta a risolvere i problemi che impediscono l'accesso al portale di Azure o al Centro account di Azure.
author: v-miegge
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: v-miegge
ms.openlocfilehash: 40a645fe4b74043d84dc770f15d5e3c3ec907f02
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199484"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Risolvere i problemi di accesso alla sottoscrizione di Azure

Questa guida aiuta a risolvere i problemi che impediscono l'accesso al portale di Azure o al Centro account di Azure.

## <a name="issues"></a>Problemi

### <a name="page-hangs-in-the-loading-status"></a>La pagina resta in stato di caricamento

Se si blocca la pagina del browser Internet, provare ciascuna delle seguenti operazioni fino a quando non è possibile accedere al portale di Azure.

- Aggiornare la pagina.
- Usare un browser Internet diverso.
- Usare la modalità di esplorazione privata per il browser. Per Internet Explorer: Fare clic su **Strumenti** > **Sicurezza** > **InPrivate Browsing**, quindi accedere al [portale di Azure](https://portal.azure.com/) o al [Centro account di Azure](https://account.azure.com/Subscriptions).

### <a name="you-are-automatically-signed-in-as-a-different-user"></a>Viene eseguito l'accesso automaticamente come utente diverso

Questo problema può verificarsi se si usa più di un account utente in un browser Internet.

Per risolvere il problema, utilizzare uno dei seguenti metodi alternativi:

- Svuotare la cache ed eliminare i cookie di Internet. In Internet Explorer fare clic su **Strumenti** > **Opzioni Internet** > **Elimina**. Verificare di aver selezionato sia le caselle di controllo per file temporanei, cookie, password e cronologia esplorazioni, quindi fare clic su Elimina.
- Riconfigurare le impostazioni di Internet Explorer per ripristinare le impostazioni personali definite. Fare clic su **Strumenti** > **Opzioni Internet** > **Avanzate** > selezionare la casella di controllo **Elimina impostazioni personali** > **Ripristina**.
- Usare la modalità di esplorazione privata per il browser. Per Internet Explorer:  Fare clic su **Strumenti** > **Sicurezza** > **InPrivate Browsing**, quindi accedere al [portale di Azure](https://portal.azure.com/) o al [Centro account di Azure](https://account.azure.com/Subscriptions).

### <a name="i-can-sign-in-but-i-see-no-subscriptions-found"></a>È possibile accedere, ma viene visualizzato il messaggio *Non sono state trovate sottoscrizioni*

Questo problema si verifica se è stata effettuata una selezione nella directory errata o se l'account non ha autorizzazioni sufficienti.

**Scenario 1:** Il messaggio di errore appare nel [portale di Azure](https://portal.azure.com/)

Per risolvere il problema:

- Verificare che sia selezionata la directory di Azure corretta facendo clic sull'account in alto a destra.
- Se è selezionata la directory di Azure corretta, ma viene comunque visualizzato il messaggio di errore, richiedere che il proprio account venga [aggiunto come proprietario](add-change-subscription-administrator.md).

**Scenario 2:** Il messaggio di errore appare nel [Centro account di Azure](https://account.windowsazure.com/Subscriptions)

Controllare se l'account usato è l'amministratore account. Per verificare chi è l'amministratore account, seguire questa procedura:

1. Accedere alla [visualizzazione Sottoscrizioni nel portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Scegliere la sottoscrizione da controllare e quindi controllare la sezione **Impostazioni**.

3. Selezionare **Proprietà**. L'amministratore account della sottoscrizione viene visualizzato nella casella **Amministratore account** .

## <a name="additional-help-resources"></a>Risorse della guida aggiuntive

Altri articoli sulla risoluzione dei problemi per la fatturazione e le sottoscrizioni di Azure

- [Carta rifiutata](troubleshoot-declined-card.md)
- [Problemi di iscrizione alla sottoscrizione](troubleshoot-azure-sign-up.md)
- [Non sono state trovate sottoscrizioni](no-subscriptions-found.md)
- [Vista dei costi Enterprise disabilitata](enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Contattare Microsoft per richiedere assistenza

In caso di domande o per assistenza, [creare una richiesta di supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- [Documentazione sulla fatturazione di Azure](../../billing/index.md)
