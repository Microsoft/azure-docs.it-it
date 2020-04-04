---
title: Autenticazione di Windows e Azure MFA Server - Azure Active Directory
description: Distribuzione dell'autenticazione di Windows e del server Azure Multi-Factor Authentication.
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
ms.openlocfilehash: 4abfb970ca322724adb0f8919b7509bc8a641378
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652794"
---
# <a name="windows-authentication-and-azure-multi-factor-authentication-server"></a>L'autenticazione di Windows e Azure il Server Multi-Factor Authentication

Usare la sezione Autenticazione di Windows del server Azure Multi-Factor Authentication per abilitare e configurare l'autenticazione di Windows per le applicazioni. Prima di configurare l'autenticazione di Windows, tenere presente quanto segue:

* Dopo aver eseguito la configurazione, riavviare Azure Multi-Factor Authentication per rendere effettivo Servizi terminal.
* Se l'opzione 'Richiedi corrispondenza utente di Azure Multi-Factor Authentication' è selezionata e non si è presenti nell'elenco degli utenti, non sarà possibile accedere al computer dopo il riavvio.
* La funzione IP attendibili dipende da se l'applicazione può fornire l'IP del client con l'autenticazione. Attualmente solo la funzione Servizi Terminal è supportata.  

> [!IMPORTANT]
> A partire dal 1 luglio 2019, Microsoft non offrirà più MFA Server per le nuove distribuzioni. I nuovi clienti che desiderano richiedere l'autenticazione a più fattori dai propri utenti devono usare L'autenticazione a più fattori di Azure basata su cloud. I clienti esistenti che hanno attivato MFA Server prima del 1 luglio potranno scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.

> [!NOTE]
> Questa funzionalità non è supportata per proteggere Servizi Terminal in Windows Server 2012 R2.

## <a name="to-secure-an-application-with-windows-authentication-use-the-following-procedure"></a>Per proteggere un'applicazione con l'autenticazione di Windows, utilizzare la procedura seguenteTo secure an application with Windows Authentication, use the following procedure

1. Nel server Microsoft Azure Multi-Factor Authentication fare clic sull'icona Autenticazione di Windows.
   ![Autenticazione di Windows in MFA Server](./media/howto-mfaserver-windows/windowsauth.png)
2. Selezionare la casella di controllo **Abilita autenticazione di Windows**. Per impostazione predefinita, questa casella è deselezionata.
3. La scheda applicazioni consente all'amministratore di configurare uno o più applicazioni per l'autenticazione di Windows.
4. Selezionare un server o un'applicazione, specificare se il server/applicazione è abilitato. Fare clic su **OK**.
5. Fare clic su **Aggiungi...**
6. La scheda di ID attendibili consente di ignorare Azure Multi-Factor Authentication per le sessioni Windows provenienti da IP specifici. Ad esempio, se i dipendenti usano l'applicazione dall'ufficio e da casa, è possibile decidere di non volere che i loro telefoni squillino per Azure Multi-Factor Authentication in ufficio. A tale scopo, specificare la subnet dell'ufficio come voce di ID attendibili.
7. Fare clic su **Aggiungi...**
8. Selezionare **IP singolo** se si desidera ignorare un singolo indirizzo IP.
9. Selezionare **Intervallo IP** se si desidera ignorare un intero intervallo IP. Esempio: 10.63.193.1-10.63.193.100.
10. Selezionare **Subnet** se si desidera specificare un intervallo di indirizzi IP utilizzando la notazione subnet. Immettere l’IP iniziale della subnet e scegliere la mask appropriata dall'elenco a discesa.
11. Fare clic su **OK**.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare dispositivi VPN di terze parti per il server Azure MFA](howto-mfaserver-nps-vpn.md)

- [Ampliare l'infrastruttura di autenticazione esistente con l'estensione NPS per Azure MFA](howto-mfa-nps-extension.md)
