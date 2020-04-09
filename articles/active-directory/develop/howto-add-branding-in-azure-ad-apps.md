---
title: Linee guida sulla personalizzazione delle app | Azure
titleSuffix: Microsoft identity platform
description: Guida completa alle risorse rivolte agli sviluppatori per Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: arielgo
ms.custom: aaddev, signin_art
ms.openlocfilehash: c5b63fb0b66c78b33fb073eca919ed55bd05107c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884427"
---
# <a name="branding-guidelines-for-applications"></a>Linee guida sulla personalizzazione per le applicazioni

Quando si sviluppano applicazioni con Azure Active Directory (Azure AD), le linee guida consentiranno agli sviluppatori di fornire ai clienti le indicazioni più appropriate quando questi intendono usare l'account aziendale o dell'istituto di istruzione, gestito in Azure AD, o l'account personale per iscriversi e accedere all'applicazione.

Contenuto dell'articolo:

- Informazioni sui due tipi di account utente gestiti da Microsoft e come fare riferimento agli account di Azure AD nell'applicazione
- Scoprire cosa è necessario per aggiungere il logo Microsoft per l'uso nell'app
- Scaricare le immagini **Accedi** oppure **Accedi con Microsoft** da usare nell'app
- Informazioni sulle operazioni di personalizzazione e navigazione consigliate e sconsigliate

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Confronto tra account Microsoft personali e aziendali o dell'istituto di istruzione

Microsoft gestisce due tipi di account utente:

- **Account personali** (noti in precedenza come Windows Live ID). Rappresentano la relazione tra utenti *individuali* e Microsoft e vengono usati per accedere a dispositivi di consumo e servizi forniti da Microsoft. Questi account sono concepiti per un uso personale.
- **Account aziendali o dell'istituto di istruzione.**  Questi account sono gestiti da Microsoft per conto delle organizzazioni che usano Azure Active Directory. Vengono usati per accedere a Office 365 e altri servizi aziendali forniti da Microsoft.

Gli account Microsoft aziendali o dell'istituto di istruzione vengono normalmente assegnati agli utenti finali (dipendenti, studenti, impiegati pubblici) dalle relative organizzazioni (azienda, istituto di istruzione, agenzia governativa). Questi account vengono gestiti direttamente nel cloud, nella piattaforma Azure AD, o sincronizzati con Azure AD da una directory locale, ad esempio Windows Server Active Directory. Microsoft è *responsabile* degli account aziendali o dell'istituto di istruzione, che sono tuttavia di proprietà e controllati dall'organizzazione.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Riferimento agli account Azure AD nella propria applicazione

Microsoft non mostra agli utenti finali il nome del marchio Azure o Active Directory, né dovrebbero farlo gli sviluppatori.

- Dopo che gli utenti hanno eseguito l'accesso, è consigliabile visualizzare il nome e il logo dell'organizzazione il più estesamente possibile. Questa soluzione è preferibile rispetto all'uso di termini generici come "organizzazione".
- Quando gli utenti non hanno eseguito l'accesso, è consigliabile fare riferimento ai loro account come "Account aziendali o dell'istituto di istruzione" e usare il logo Microsoft per indicare che sono account gestiti da Microsoft. Non usare termini quali "account dell'azienda", "account dell'impresa" o "account della società", perché potrebbero creare confusione nell'utente.

## <a name="user-account-pictogram"></a>Pittogramma dell'account utente

In una versione precedente di queste linee guida si è consigliato di usare un pittogramma di "badge blu". In base ai commenti e suggerimenti degli utenti e degli sviluppatori, ora si consiglia invece di usare il logo Microsoft. In questo modo sarà più facile per gli utenti comprendere che possono riutilizzare l'account che usano con Office 365 o altri servizi commerciali Microsoft per accedere all'app.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Iscrizione e accesso con Azure AD

Le app degli sviluppatori possono presentare percorsi separati per l'iscrizione e l'accesso. Nelle sezioni seguenti vengono fornite indicazioni visive per entrambi gli scenari.

**Se l'app supporta l'iscrizione da parte dell'utente, ad esempio, per una versione di valutazione gratuita o per il modello freemium**: è possibile visualizzare un pulsante di **accesso** per consentire agli utenti di accedere all'app con l'account aziendale o personale. La prima volta che l'utente accede all'app, Azure AD visualizzerà una richiesta di consenso.

**Se l'app richiede autorizzazioni che solo gli amministratori possono concedere oppure se l'app richiede una licenza dell'organizzazione**: separare l'acquisizione amministrativa dall'accesso utente. Il **pulsante per "ottenere l'app"** reindirizzerà gli amministratori alla pagina di accesso, quindi verrà richiesto di concedere il consenso per conto degli utenti dell'organizzazione, che il vantaggio aggiunto di sopprimere i messaggi di richiesta di consenso dell'utente finale all'app.

## <a name="visual-guidance-for-app-acquisition"></a>Indicazioni visive per l'acquisizione di app

Il collegamento per "ottenere l'app" deve reindirizzare l'utente alla pagina di Azure AD per concedere (autorizzare) l'accesso, in modo che l'amministratore di un'organizzazione possa autorizzare l'app perché possa accedere ai dati dell'organizzazione ospitati da Microsoft. Verranno fornite informazioni dettagliate su come richiedere l'accesso nell’articolo [Integrazione di applicazioni con Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md) .

Dopo che gli amministratori hanno acconsentito all'app, possono scegliere di aggiungerla all'esperienza [https://portal.office.com/myapps](https://portal.office.com/myapps)di avvio delle app di Office 365 degli utenti (accessibile dalla cialda e da ). Se si vuole annunciare questa funzionalità, è possibile usare termini come "Aggiungere questa app alla propria organizzazione" e visualizzare un pulsante simile al seguente:

![Pulsante che mostra il logo Microsoft e il testo "Aggiungi alla mia organizzazione"](./media/howto-add-branding-in-azure-ad-apps/add-to-my-org.png)

È tuttavia consigliabile scrivere un testo descrittivo, invece di fare affidamento sui pulsanti. Ad esempio:

> *Se si usa già Office 365 o un altro servizio aziendale di Microsoft, è possibile concedere a <your_app_name l'accesso> ai dati dell'organizzazione. Ciò consentirà agli utenti di accedere a<your_app_name> con i loro account di lavoro esistenti.*

Per scaricare il logo ufficiale di Microsoft per l'uso nell'app, fare clic con il pulsante destro del mouse su quello che si vuole usare e salvarlo sul computer.

| Asset                                | Formato PNG | Formato SVG |
| ------------------------------------ | ---------- | ---------- |
| Logo Microsoft  | ![Logo Microsoft scaricabile in formato PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.png) | ![Logo Microsoft scaricabile in formato SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Indicazioni visive per l'accesso

Nell'app dovrà essere visualizzato un pulsante di accesso che reindirizza gli utenti all'endpoint di accesso corrispondente al protocollo usato per l'integrazione con Azure AD. La sezione seguente illustra in dettaglio come dovrebbe apparire questo pulsante.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Pittogramma e "Accedi con Microsoft"

È l'associazione del logo Microsoft e dei termini "Accedi con Microsoft" che distingue in modo univoco Azure AD dagli altri provider di identità eventualmente supportati dall'app. Se non è disponibile spazio sufficiente per "Accedi con Microsoft" è possibile usare la dicitura abbreviata "Accedi". È anche possibile usare una combinazione di colori chiara per i pulsanti.

Il diagramma seguente mostra le annotazioni consigliate da Microsoft quando si usano gli asset con l'app. Le annotazioni si applicano ad "Accedi con Microsoft" o alla versione più breve "Accedi".

![Mostra limiti "Accedi con Microsoft"](./media/howto-add-branding-in-azure-ad-apps/sign-in-with-microsoft-redlines.png)

Per scaricare le immagini ufficiali per l'uso nell'app, fare clic con il pulsante destro del mouse su quelle che si vuole usare e salvarle sul computer.

| Asset                                | Formato PNG | Formato SVG |
| ------------------------------------ | ---------- | ---------- |
| Accedi con Microsoft (tema scuro)  | ![Pulsante "Accedi con Microsoft" con tema scuro PNG scaricabile](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.png) | ![Pulsante "Accedi con Microsoft" con tema scuro SVG scaricabile](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.svg) |
| Accedi con Microsoft (tema chiaro) | ![Pulsante "Accedi con Microsoft" con tema chiaro PNG scaricabile](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.png) | ![Pulsante "Accedi con Microsoft" con tema chiaro SVG scaricabile](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.svg) |
| Accedi (tema scuro)                 | ![Pulsante versione breve "Accedi" con tema scuro PNG scaricabile](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.png) | ![Pulsante versione breve "Accedi" con tema scuro SVG scaricabile](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.svg) |
| Accedi (tema chiaro)                | ![Pulsante versione breve "Accedi" con tema chiaro PNG scaricabile](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.png) | ![Pulsante versione breve "Accedi" con tema chiaro SVG scaricabile](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.svg) |

## <a name="branding-dos-and-donts"></a>Azioni consentite e non consentite per la personalizzazione

**USARE** "account aziendale o dell'istituto di istruzione" insieme al pulsante "Accedi con Microsoft" per fornire altre spiegazioni che consentano agli utenti finali di comprendere se possono usare l'account. **NON** usare termini quali "account dell'azienda", "account dell'impresa" o "account della società".

**NON** usare "ID Office 365" o "ID Azure". Office 365 è anche il nome di un'offerta Microsoft per i consumatori che non prevede l'uso di Azure AD per l'autenticazione.

**NON MODIFICARE** il logo Microsoft.

**NON ESPORRE** le personalizzazioni di Azure o Active Directory agli utenti finali. È tuttavia accettabile usare questi termini con sviluppatori, professionisti IT e amministratori.

## <a name="navigation-dos-and-donts"></a>Azioni consentite e non consentite per la navigazione

**FORNIRE** agli utenti un modo per disconnettersi e passare a un altro account utente. Quasi tutte le persone hanno in genere un singolo account per Microsoft, Facebook, Google e Twitter, ma sono spesso associate a più organizzazioni. Il supporto per l'accesso di più utenti sarà presto disponibile.
