---
title: Autenticazione IIS e server Azure MFA | Azure Active Directory
description: Distribuzione dell'autenticazione IIS e del server Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2377ca4b929200ecd0a3a7de01dd3a58be6b7863
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845441"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Configurazione di Azure Multi-Factor Authentication per le App Web IIS

Usare la sezione Autenticazione IIS del server Azure Multi-Factor Authentication (MFA) per abilitare e configurare l'autenticazione IIS per l'integrazione con le applicazioni Web Microsoft IIS. Il server Azure MFA installa un plug-in che può filtrare le richieste inoltrate al server Web IIS per aggiungere Azure Multi-Factor Authentication. Il plug-in di IIS fornisce inoltre il supporto per l'autenticazione basata su form e l'autenticazione HTTP integrata di Windows. È possibile anche configurare gli indirizzi IP attendibili in modo da escludere gli indirizzi IP interni dall'autenticazione a due fattori.

> [!IMPORTANT]
> A partire dal 1° luglio 2019, Microsoft non offrirà più il server MFA per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori per gli utenti dovranno usare il servizio Azure Multi-Factor Authentication basato sul cloud. Gli attuali clienti che anno attivato il server MFA prima del 1° luglio potranno scaricare la versione più recente e gli aggiornamenti futuri, oltre a generare le credenziali di attivazione come di consueto. Quando si usa il servizio Azure Multi-Factor Authentication basato sul cloud, non esiste alcuna alternativa al plug-in IIS fornito dal server Azure Multi-Factor Authentication (MFA). Usare invece il proxy applicazione Web (WAP) con Active Directory Federation Services (AD FS) o Azure Active Directory Application Proxy.

![Autenticazione IIS nel server MFA](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Uso dell'autenticazione IIS basata su form con il server Azure Multi-Factor Authentication

Per proteggere un'applicazione Web IIS che usa l'autenticazione basata su form, installare il server Azure Multi-Factor Authentication nel server Web IIS e configurare il server seguendo questa procedura:

1. Nel server Azure Multi-Factor Authentication fare clic sull'icona Autenticazione IIS nel menu a sinistra.
2. Fare clic sulla scheda **Basata su form**.
3. Scegliere **Aggiungi**.
4. Per rilevare automaticamente le variabili di nome utente, password e dominio, immettere l'URL di accesso (ad esempio `https://localhost/contoso/auth/login.aspx`) nella finestra di dialogo Configura automaticamente sito Web basato su form e fare clic su **OK**.
5. Se tutti gli utenti sono già stati o verranno importati nel server e saranno soggetti all'autenticazione a più fattori, selezionare la casella **Richiedi corrispondenza utente di Multi-Factor Authentication**. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella.
6. Se le variabili di pagina non possono essere rilevate automaticamente, fare clic su **Specifica manualmente** nella finestra di dialogo Configura automaticamente sito Web basato su form.
7. Nella finestra di dialogo Aggiungi sito Web immettere l'URL alla pagina di accesso nel campo URL di invio e immettere un nome di applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS.
8. Selezionare il formato di richiesta corretto È impostato su **POST or GET** (OTTIENI) per la maggior parte delle applicazioni Web.
9. Immettere la variabile nome utente, la variabile password e la variabile dominio (se è presente nella pagina di accesso). Per individuare i nomi delle caselle di input, passare alla pagina di accesso in un Web browser, fare clic con il pulsante destro del mouse nella pagina e quindi scegliere **Visualizza origine**.
10. Selezionare la casella **Require Azure Multi-Factor Authentication user match** (Richiedi corrispondenza utente di Azure Multi-Factor Authentication) se tutti gli utenti sono già stati o verranno importati nel server e saranno soggetti all'autenticazione a più fattori. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella.
11. Fare clic su **Avanzate** per rivedere le impostazioni avanzate, tra cui:

    - Selezionare un file di paging di rifiuto personalizzato
    - Memorizzare nella cache le autenticazioni riuscite per il sito Web per un periodo di tempo tramite cookie
    - Scegliere se autenticare le credenziali primarie in un dominio di Windows, in una directory LDAP o in un server RADIUS.

12. Fare clic su **OK** per tornare alla finestra di dialogo Aggiungi sito Web basato su form.
13. Fare clic su **OK**.
14. Dopo aver rilevato o immesso le variabili di pagina e URL, i dati del sito Web vengono visualizzati nel pannello Basata su form.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Uso dell'autenticazione integrata di Windows con il server Azure Multi-Factor Authentication

Per proteggere un'applicazione Web IIS che usa l'autenticazione HTTP integrata di Windows, installare il server Azure MFA nel server Web IIS e configurare il server seguendo questa procedura:

1. Nel server Azure Multi-Factor Authentication fare clic sull'icona Autenticazione IIS nel menu a sinistra.
2. Fare clic sulla scheda **HTTP**.
3. Scegliere **Aggiungi**.
4. Nella finestra di dialogo Aggiungi URL di base immettere l'URL del sito Web in cui viene eseguita l'autenticazione HTTP (ad esempio <http://localhost/owa>) e immettere il nome dell'applicazione (facoltativo). Il nome dell'applicazione viene visualizzato nei report di Azure multi-Factor Authentication e potrebbe essere visualizzato all'interno di messaggi di autenticazione dell'App Mobile o SMS.
5. Modificare i valori relativi al timeout di inattività e al tempo massimo delle sessioni se i valori predefiniti non sono sufficienti.
6. Se tutti gli utenti sono già stati o verranno importati nel server e saranno soggetti all'autenticazione a più fattori, selezionare la casella **Richiedi corrispondenza utente di Multi-Factor Authentication**. Se un numero significativo di utenti non è ancora stato importato nel Server e/o esenti dall'autenticazione a più fattori, lasciare deselezionata la casella.
7. Se lo si desidera, selezionare la casella **Cookie cache** (Cache di cookie).
8. Fare clic su **OK**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Abilitare i plug-in di IIS per il server Azure Multi-Factor Authentication

Dopo aver configurato gli URL e le impostazioni dell'autenticazione HTTP o basata su form, selezionare le destinazioni in cui i plug-in di IIS del server Azure Multi-Factor Authentication devono essere caricati e abilitati in IIS. Utilizzare la procedura seguente:

1. Se si esegue IIS 6, fare clic sulla scheda **ISAPI**. Selezionare il sito Web che esegue l'applicazione Web (ad esempio "Sito Web predefinito") per abilitare il plug-in dei filtri ISAPI di Azure Multi-Factor Authentication per tale sito.
2. Se si esegue IIS 7 o versione successiva, fare clic sulla scheda **Modulo nativo**. Selezionare il server, i siti Web o le applicazioni per abilitare il plug-in di IIS ai livelli desiderati.
3. Scegliere la casella **Abilita autenticazione IIS** nella parte superiore della schermata. A questo punto l'applicazione IIS selezionata è protetta da Azure Multi-Factor Authentication. Verificare che gli utenti siano stati importati nel server.

## <a name="trusted-ips"></a>Indirizzi IP attendibili

Il provider di identità attendibili consente agli utenti di ignorare Azure multi-Factor Authentication per le richieste del sito Web provenienti da specifici indirizzi IP o subnet. Ad esempio, è possibile escludere gli utenti da Azure multi-Factor Authentication durante l'accesso dall'ufficio. A tale scopo, specificare la subnet dell'ufficio come voce di Indirizzi IP attendibili. Per configurare gli indirizzi IP attendibili, seguire questa procedura:

1. Fare clic sulla scheda **IP attendibili** nella sezione Autenticazione IIS.
2. Scegliere **Aggiungi**.
3. Quando viene visualizzata la finestra di dialogo Add Trusted IPs (Aggiungi IP attendibili), selezionare il pulsante di opzione **IP singolo**, **Intervallo IP** o **Subnet**.
4. Immettere l'indirizzo IP, l'intervallo di indirizzi IP o la subnet da includere nell'elenco degli elementi consentiti. Se si immette una subnet, selezionare la Netmask appropriata e fare clic su **OK**.
