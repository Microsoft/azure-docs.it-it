---
title: Bloccare l'autenticazione legacy - Azure Active Directory
description: Informazioni su come migliorare il comportamento di sicurezza bloccando l'autenticazione legacy con l'accesso condizionale di Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84c8b82219f2b2aea39bbcd23f030243d9ea8635
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861806"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Procedura: Bloccare l'autenticazione legacy ad Azure AD con l'accesso condizionale   

Per consentire agli utenti di accedere facilmente alle app cloud, Azure Active Directory (Azure AD) supporta una vasta gamma di protocolli di autenticazione, inclusa l'autenticazione legacy. I protocolli legacy, tuttavia, non supportano l'autenticazione a più fattori (MFA). La MFA è in molti ambienti un requisito comune contro il furto di identità di indirizzo. 

Alex Weinert, Direttore di Identity Security di Microsoft, nel post di blog del 12 marzo 2020 Nuovi strumenti per bloccare l'autenticazione [legacy](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) nell'organizzazione sottolinea perché le organizzazioni devono bloccare l'autenticazione legacy e quali altri strumenti microsoft offre per eseguire questa attività:

> Affinché l'autenticazione MFA sia efficace, è anche necessario bloccare l'autenticazione legacy. I protocolli di autenticazione legacy come POP, SMTP, IMAP e MAPI, infatti, non possono imporre l'autenticazione MFA e costituiscono pertanto i punti di ingresso preferiti dagli antagonisti che attaccano l'organizzazione...
> 
>...I numeri relativi all'autenticazione legacy risultanti da un'analisi del traffico di Azure Active Directory (Azure AD) sono chiari:
> 
> - Oltre il 99% degli attacchi password spraying usa protocolli di autenticazione legacy
> - Oltre il 97% degli attacchi credential stuffing usa l'autenticazione legacy
> - Gli account Azure AD delle organizzazioni che hanno disabilitato l'esperienza di autenticazione legacy sono soggetti al 67% di compromissioni in meno rispetto a quelli per cui l'autenticazione legacy è abilitata
>

Se l'ambiente è pronto per il blocco dell'autenticazione legacy per migliorare la protezione dei tenant, è possibile usare a tale scopo l'accesso condizionale. Questo articolo illustra come configurare i criteri di accesso condizionale che bloccano l'autenticazione legacy per un tenant. I clienti senza licenze che includono l'accesso condizionale possono usare le impostazioni [predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md)) per bloccare l'autenticazione legacy.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia familiarità con i concetti [di base](overview.md) dell'Azure AD condizionale.

## <a name="scenario-description"></a>Descrizione dello scenario

Azure AD supporta diversi dei protocolli di autenticazione e autorizzazione più ampiamente usati. L'autenticazione legacy fa riferimento ai protocolli che usano l'autenticazione di base. In genere, questi protocolli non possono applicare nessun tipo di secondo fattore di autenticazione. Alcuni esempi di app che si basano sull'autenticazione legacy:

- App Microsoft Office meno recenti
- App che usano protocolli di posta elettronica quali POP, IMAP e SMTP

Al giorno d’oggi, l'autenticazione a fattore singolo (ad esempio, nome utente e password) non è più sufficiente. Le password non sono sicure perché sono facili da indovinare e le persone non sanno scegliere password efficaci. Le password sono inoltre vulnerabili a vari attacchi, ad esempio phishing e password spray. Una delle operazioni più semplici da eseguire per la protezione dalle minacce alle password è l'implementazione dell'autenticazione a più fattori(MFA). Con la MFA, anche se un utente malintenzionato entra in possesso di una password utente, la sola password non è sufficiente per l’autenticazione e l’accesso ai dati.

Come si può impedire alle app che usano l'autenticazione legacy di accedere alle risorse dei tenant? È consigliabile bloccarle con criteri di accesso condizionale. Se necessario, è possibile autorizzare solo determinati utenti e percorsi di rete specifici per l’uso delle app basate sull’autenticazione legacy.

I criteri di accesso condizionale vengono applicati dopo il completamento dell'autenticazione con primo fattore. Pertanto l'accesso condizionale non è da intendersi come una prima misura difensiva da attacchi Denial of Service (DoS), ma può utilizzare segnali da questi eventi, come il livello di rischio di accesso, la posizione della richiesta e così via, per determinare l'accesso.

## <a name="implementation"></a>Implementazione

Questa sezione illustra come configurare criteri di accesso condizionale per bloccare l'autenticazione legacy. 

### <a name="legacy-authentication-protocols"></a>Protocolli di autenticazione legacy

Le opzioni seguenti sono considerate protocolli di autenticazione legacy.

- SMTP autenticato: usato dai client POP e IMAP per inviare messaggi di posta elettronica.
- Individuazione automatica: usata dai client Outlook e EAS per trovare le cassette postali in Exchange Online e connettervisi.
- Exchange ActiveSync (EAS): usato per connettersi alle cassette postali in Exchange Online.
- Exchange Online PowerShell: usato per connettersi a Exchange Online con PowerShell remoto. Se si blocca l'autenticazione di base per Exchange Online PowerShell, è necessario usare il modulo Exchange Online PowerShell per connettersi. Per istruzioni, vedere [Connettersi a Exchange Online PowerShell con l'autenticazione a più fattori](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
- Servizi Web Exchange: interfaccia di programmazione usata da Outlook, Outlook per Mac e app di terze parti.
- IMAP4: usato dai client di posta elettronica IMAP.
- MAPI su HTTP (MAPI/HTTP): usato da Outlook 2010 e versioni successive.
- Rubrica offline: copia delle raccolte di elenchi di indirizzi scaricate e usate da Outlook.
- Outlook via Internet (RPC su HTTP): usato da Outlook 2016 e versioni precedenti.
- Servizio Outlook: usato dall'app di posta elettronica e di calendario per Windows 10.
- POP3: usato dai client di posta elettronica POP.
- Servizi Web per la creazione di report: funzionalità usata per recuperare i dati dei report in Exchange Online.
- Altri client: altri protocolli identificati come protocolli che utilizzano l'autenticazione legacy.

Per altre informazioni su questi protocolli e servizi di autenticazione, vedere [Report delle attività di accesso nel portale di Azure Active Directory](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities).

### <a name="identify-legacy-authentication-use"></a>Identificare l'uso dell'autenticazione legacy

Prima di poter bloccare l'autenticazione legacy nella directory, è necessario anzitutto capire se gli utenti usano app che sfruttano l'autenticazione legacy e il modo in cui questa influisce sulla directory complessiva. Per capire se viene usata l'autenticazione legacy è possibile usare i log di accesso di Azure AD.

1. Passare al **portale di Azure** > **Azure Active Directory** > **Accessi**.
1. Se non viene visualizzata, aggiungere la colonna App client facendo clic su **Colonne** > **App client**.
1. **Aggiungere filtri**  >  **App client** > tutti i protocolli di autenticazione legacy. Selezionare all'esterno della finestra di dialogo di filtro per applicare le selezioni e chiudere la finestra di dialogo.
1. Se è stata [](../reports-monitoring/concept-all-sign-ins.md)attivata l'anteprima dei nuovi report attività di accesso, ripetere i passaggi precedenti anche nella scheda User **sign-ins (non-interactive) (Account** di accesso utente (non interattivo).

Applicando il filtro verranno visualizzati solo i tentativi di accesso eseguiti dai protocolli di autenticazione legacy. Facendo clic su ogni singolo tentativo di accesso verranno visualizzati altri dettagli. Nel campo **App client** nella scheda **Info di base** sarà indicato il protocollo di autenticazione legacy che è stato usato.

Questi log indicano quali utenti ancora dipendono dall'autenticazione legacy e quali applicazioni usano protocolli legacy per eseguire le richieste di autenticazione. Per gli utenti che non sono riportati in questi log e per i quali è confermato che non usano l'autenticazione legacy, implementare criteri di accesso condizionale specifici.

## <a name="block-legacy-authentication"></a>Bloccare l'autenticazione legacy 

Esistono due modi per usare i criteri di accesso condizionale per bloccare l'autenticazione legacy.

- [Blocco diretto dell'autenticazione legacy](#directly-blocking-legacy-authentication)
- [Blocco indiretto dell'autenticazione legacy](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>Blocco diretto dell'autenticazione legacy

Il modo più semplice per bloccare l'autenticazione legacy nell'intera organizzazione è configurare criteri di accesso condizionale che si applicano in modo specifico ai client di autenticazione legacy e bloccano l'accesso. Quando si assegnano utenti e applicazioni ai criteri, assicurarsi di escludere gli utenti e gli account del servizio che devono comunque accedere usando l'autenticazione legacy. Configurare la condizione delle app client selezionando **Client Exchange ActiveSync** **e Altri client**. Per bloccare l'accesso per queste app client, configurare i controlli di accesso su Blocca l'accesso.

![Condizione delle app client configurata per bloccare l'autenticazione legacy](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>Blocco indiretto dell'autenticazione legacy

Anche se l'organizzazione non è pronta a bloccare l'autenticazione legacy nell'intera organizzazione, è necessario assicurarsi che gli account di accesso che usano l'autenticazione legacy non esercitino i criteri che richiedono controlli di concessione, ad esempio la richiesta di autenticazione a più fattori o dispositivi aggiunti Azure AD conformi o ibridi. Durante l'autenticazione, i client di autenticazione legacy non supportano l'invio di informazioni sull'autenticazione a più fattori, la conformità del dispositivo o l'aggiunta di informazioni sullo stato Azure AD. Applicare quindi criteri con controlli di concessione a tutte le applicazioni client in modo che gli account di accesso legacy basati sull'autenticazione che non possono soddisfare i controlli di concessione siano bloccati. Con la disponibilità generale della condizione delle app client di agosto 2020, i criteri di accesso condizionale appena creati si applicano a tutte le app client per impostazione predefinita.

![Configurazione predefinita della condizione delle app client](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>Informazioni utili

Bloccando l'accesso con **Altri client** vengono bloccati anche Exchange Online PowerShell e Dynamics 365 con autenticazione di base.

La configurazione di un criterio per **Altri client** blocca l'intera organizzazione per determinati client come SPConnect. Questo blocco si verifica perché l'autenticazione di client meno recenti avviene in modo imprevisto. Questo problema non si applica alle principali applicazioni di Office, come i client di Office meno recenti.

Perché il criterio abbia effetto, possono essere necessarie fino a 24 ore.

È possibile selezionare tutti i controlli di concessione disponibili per la condizione **Altri client**, ma l'esperienza dell'utente finale sarà sempre la stessa: l'accesso è bloccato.

### <a name="sharepoint-online-and-b2b-guest-users"></a>Utenti guest di SharePoint Online e B2B

Per bloccare l'accesso utente B2B tramite l'autenticazione legacy a SharePoint Online, le organizzazioni devono disabilitare l'autenticazione legacy in SharePoint usando il comando di PowerShell e impostando `Set-SPOTenant` il `-LegacyAuthProtocolsEnabled` parametro su `$false` . Altre informazioni sull'impostazione di questo parametro sono disponibili nel documento di riferimento di PowerShell per SharePoint [relativo a Set-SPOTenant](/powershell/module/sharepoint-online/set-spotenant)

## <a name="next-steps"></a>Passaggi successivi

- [Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-insights-reporting.md)
- Se non si ha ancora familiarità con la configurazione dei criteri di accesso condizionale, per un esempio vedere [Richiedere l'autenticazione MFA per app specifiche con l'accesso condizionale di Azure Active Directory](../authentication/tutorial-enable-azure-mfa.md).
- Per altre informazioni sull'autenticazione moderna, vedere [Funzionamento dell'autenticazione moderna per le app client di Office 2013 e Office 2016](/office365/enterprise/modern-auth-for-office-2013-and-2016). 
- [Come configurare un dispositivo multifunzione o un'applicazione per inviare messaggi di posta elettronica usando Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)