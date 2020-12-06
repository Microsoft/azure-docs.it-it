---
title: Metodi di autenticazione utilizzo & Insights-Azure Active Directory
description: Creazione di report su Azure AD la reimpostazione della password self-service e Multi-Factor Authentication utilizzo del metodo di autenticazione
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8633ae0d23650eb12ee2e1fdcd355864dfd8b6c3
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743633"
---
# <a name="authentication-methods-usage--insights-preview"></a>Metodi di autenticazione utilizzo & Insights (anteprima)

Usage & Insights consente di comprendere in che modo i metodi di autenticazione per funzionalità quali Azure AD Multi-Factor Authentication e la reimpostazione della password self-service sono in esecuzione nell'organizzazione. Questa funzionalità di creazione di report consente all'organizzazione di individuare i metodi registrati e il modo in cui vengono usati.

## <a name="permissions-and-licenses"></a>Autorizzazioni e licenze

I ruoli seguenti possono accedere all'utilizzo e alle informazioni dettagliate:

- Amministratore globale
- Ruolo con autorizzazioni di lettura per la sicurezza
- Amministratore della protezione
- Lettore di report

Non è necessaria alcuna licenza aggiuntiva per accedere all'utilizzo e alle informazioni dettagliate. Azure AD Multi-Factor Authentication e le informazioni sulle licenze per la reimpostazione della password self-service (SSPR) sono disponibili nel [Azure Active Directory sito dei prezzi](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Funzionamento

Per accedere all'utilizzo del metodo di autenticazione e alle informazioni dettagliate:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare a **Azure Active Directory**  >  uso della **reimpostazione della password**  >  **& Insights**.
1. Dalle panoramiche sulla **registrazione** o **sull'utilizzo** , è possibile scegliere di aprire i report pre-filtrati per filtrare in base alle esigenze.

![Panoramica sull'utilizzo di & Insights](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Per accedere direttamente all'utilizzo & Insights, passare a [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) . Questo collegamento consente di visualizzare la panoramica della registrazione.

I riquadri utenti registrati, utenti abilitati e in grado di supportare gli utenti mostrano i dati di registrazione seguenti per gli utenti:

- Registrato: un utente viene considerato registrato se (o un amministratore) ha registrato un numero sufficiente di metodi di autenticazione per soddisfare i criteri di SSPR o di Multi-Factor Authentication dell'organizzazione.
- Enabled: un utente viene considerato abilitato se è nell'ambito del criterio SSPR. Se SSPR è abilitato per un gruppo, l'utente viene considerato abilitato se si trova in tale gruppo. Se la SSPR è abilitata per tutti gli utenti, tutti gli utenti nel tenant (esclusi i guest) vengono considerati abilitati.
- In grado di supportare: un utente è considerato idoneo se è sia registrato che abilitato. Questo stato indica che è possibile eseguire SSPR in qualsiasi momento, se necessario.

Facendo clic su uno di questi riquadri o sulle informazioni approfondite visualizzate, viene visualizzato un elenco di dettagli di registrazione pre-filtrato.

Il grafico **registrazioni** nella scheda **registrazione** Mostra il numero di registrazioni del metodo di autenticazione riuscite e non riuscite in base al metodo di autenticazione. Il grafico **Reimposta** nella scheda **utilizzo** Mostra il numero di autenticazioni riuscite e non riuscite durante il flusso di reimpostazione della password in base al metodo di autenticazione.

Se si fa clic su uno dei grafici, si passerà a un elenco pre-filtrato di eventi di registrazione o di reimpostazione.

Utilizzando il controllo nell'angolo superiore destro, è possibile modificare l'intervallo di date per i dati di controllo mostrati nelle registrazioni e Reimposta i grafici su 24 ore, 7 giorni o 30 giorni.

### <a name="registration-details"></a>Dettagli registrazione

Se si fa clic sugli **utenti registrati**, **gli utenti abilitati** o i riquadri abilitati per **gli** utenti o le informazioni dettagliate, sarà possibile visualizzare i dettagli di registrazione.

Il report Dettagli registrazione Mostra le informazioni seguenti per ogni utente:

- Nome
- Nome utente
- Stato della registrazione (tutti, registrato, non registrato)
- Stato abilitato (tutti, abilitato, non abilitato)
- Stato in grado di supportare (tutti, idoneo, non in grado di supportare)
- Metodi (notifica dell'app, codice dell'app, telefonata, SMS, posta elettronica, domande di sicurezza)

Utilizzando i controlli nella parte superiore dell'elenco, è possibile cercare un utente e filtrare l'elenco di utenti in base alle colonne visualizzate.

### <a name="reset-details"></a>Reimposta dettagli

Se si fa clic sulle registrazioni o si reimpostano i grafici, è possibile visualizzare i dettagli della reimpostazione.

Il report dettagli Reset Mostra gli eventi di registrazione e reimpostazione degli ultimi 30 giorni, tra cui:

- Nome
- Nome utente
- Funzionalità (tutti, registrazione, reimpostazione)
- Metodo di autenticazione (notifica dell'app, codice dell'app, telefonata, telefonata, SMS, posta elettronica, domande di sicurezza)
- Stato (tutti, esito positivo, esito negativo)

Utilizzando i controlli nella parte superiore dell'elenco, è possibile cercare un utente e filtrare l'elenco di utenti in base alle colonne visualizzate.

## <a name="limitations"></a>Limitazioni

I dati visualizzati in questi report verranno posticipati di un massimo di 60 minuti. Nel portale di Azure è presente un campo "Ultimo aggiornamento" per identificare il modo in cui i dati sono recenti.

I dati relativi all'utilizzo e alle informazioni dettagliate non sono sostitutivi per il Azure AD Multi-Factor Authentication report attività o le informazioni contenute nel report Azure AD accessi.

Attualmente non è possibile filtrare il report per escludere gli utenti esterni.

## <a name="next-steps"></a>Passaggi successivi

- [Utilizzo dell'API report utilizzo metodi di autenticazione](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Scelta dei metodi di autenticazione per l'organizzazione](concept-authentication-methods.md)
- [Esperienza di registrazione combinata](concept-registration-mfa-sspr-combined.md)