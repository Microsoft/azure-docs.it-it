---
title: 'Esercitazione: Integrare una singola foresta con un singolo tenant di Azure AD'
description: Questo argomento descrive i prerequisiti e i requisiti hardware per la sincronizzazione cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3bc9378539e6a7f98e34d0a149848d0e892c224
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306093"
---
# <a name="tutorial-integrate-a-single-forest-with-a-single-azure-ad-tenant"></a>Esercitazione: Integrare una singola foresta con un singolo tenant di Azure AD

Questa esercitazione illustra la creazione di un ambiente di identità ibrido usando Azure Active Directory (Azure AD) Connect Cloud Sync.

![Crea](media/tutorial-single-forest/diagram-2.png)

È possibile usare l'ambiente creato in questa esercitazione per i test o per acquisire familiarità con la sincronizzazione cloud.

## <a name="prerequisites"></a>Prerequisiti
### <a name="in-the-azure-active-directory-admin-center"></a>Nell'interfaccia di amministrazione di Azure Active Directory

1. Creare un account amministratore globale di tipo solo cloud nel tenant di Azure AD. In questo modo è possibile gestire la configurazione del tenant in caso di errore o mancata disponibilità dei servizi locali. Informazioni su come [aggiungere un account amministratore globale di tipo solo cloud](../fundamentals/add-users-azure-active-directory.md). Il completamento di questo passaggio è fondamentale ed evita di rimanere bloccati fuori dal tenant.
2. Aggiungere uno o più [nomi di dominio personalizzati](../fundamentals/add-custom-domain.md) al tenant di Azure AD. Gli utenti possono accedere usando uno di questi nomi di dominio.

### <a name="in-your-on-premises-environment"></a>Nell'ambiente locale

1. Identificare un server host aggiunto a un dominio che esegue Windows Server 2012 R2 o versione successiva con almeno 4 GB di RAM e il runtime di .NET 4.7.1 o versione successiva 

2. Se è presente un firewall tra i server e Azure AD, è necessario configurare gli elementi seguenti:
   - Assicurarsi che gli agenti possano effettuare richieste *in uscita* ad Azure AD sulle porte seguenti:

     | Numero della porta | Uso |
     | --- | --- |
     | **80** | Scarica gli elenchi di revoche di certificati (CRL) durante la convalida del certificato TLS/SSL |
     | **443** | Gestisce tutte le comunicazioni in uscita con il servizio |
     | **8080** (facoltativo) | Se la porta 443 non è disponibile, gli agenti di autenticazione segnalano il proprio stato ogni dieci minuti attraverso la porta 8080. Lo stato viene visualizzato nel portale di Azure AD. |
     
     Se il firewall applica regole in base agli utenti di origine, aprire queste porte per il traffico proveniente da servizi di Windows in esecuzione come servizi di rete.
   - Se il firewall o il proxy consente di specificare suffissi sicuri, aggiungere connessioni a **\*.msappproxy.net** e **\*.servicebus.windows.net**. In caso contrario, è necessario consentire l'accesso agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653), che vengono aggiornati ogni settimana.
   - Gli agenti devono poter accedere a **login.windows.net** e **login.microsoftonline.net** per la registrazione iniziale. Aprire il firewall anche per questi URL.
   - Per la convalida del certificato, sbloccare questi URL: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** e **www\.microsoft.com:80**. Poiché vengono usati per la convalida del certificato con altri prodotti Microsoft, questi URL potrebbero essere già sbloccati.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installare l'agente di provisioning cloud di Azure AD Connect
1. Accedere al server aggiunto al dominio.  Se si usa l'esercitazione  [di base di un ambiente D e Azure](tutorial-basic-ad-azure.md) , sarà DC1.
2. Accedere al portale di Azure con le credenziali di amministratore globale solo cloud.
3. A sinistra selezionare **Azure Active Directory**, fare clic su **Azure ad Connect** e al centro selezionare **Gestisci sincronizzazione cloud**.

   ![Portale di Azure](media/how-to-install/install-6.png)

4. Fare clic su **Scarica agente**.
5. Eseguire l'agente di provisioning di Azure AD Connect.
6. Nella schermata iniziale **accettare** le condizioni di licenza e fare clic su **Installa**.

   ![Screenshot che mostra la schermata iniziale del pacchetto dell'agente di provisioning di Microsoft Azure AD Connect.](media/how-to-install/install-1.png)

7. Al termine dell'operazione verrà avviata la configurazione guidata.  Accedere con l'account amministratore globale di Azure AD.  Si noti che se è abilitata la sicurezza avanzata di Internet Explorer, l'accesso verrà bloccato.  In tal caso, chiudere l'installazione, disabilitare la sicurezza avanzata di Internet Explorer in Server Manager e fare clic su **AAD Connect Provisioning Agent Wizard** (Configurazione guidata dell'agente di provisioning AAD Connect) per riavviare l'installazione.
8. Nella schermata **Connect Active Directory** (Connetti Active Directory) fare clic su **Aggiungi directory** quindi accedere con l'account amministratore di dominio di Active Directory.  NOTA:  L'account amministratore di dominio non dovrebbe avere requisiti per la modifica della password. Se la password scade o viene modificata, sarà necessario riconfigurare l'agente con le nuove credenziali. Questa operazione aggiungerà la directory locale.  Fare clic su **Avanti**.

   ![Screenshot della schermata per la connessione di Active Directory.](media/how-to-install/install-3a.png)

9. Nella schermata **Configurazione completata** fare clic su **Conferma**.  Questa operazione registrerà e riavvierà l'agente.

   ![Screenshot che mostra la schermata di completamento della configurazione.](media/how-to-install/install-4a.png)

10. Al termine dell'operazione verrà visualizzato un avviso: **Your agent configuration was successfully verified** (La configurazione dell'agente è stata verificata).  È possibile fare clic su **Esci**.</br>
![Schermata iniziale](media/how-to-install/install-5.png)</br>
11. Se è ancora visualizzata la schermata iniziale, fare clic su **Chiudi**.


## <a name="verify-agent-installation"></a>Verificare l'installazione dell'agente
La verifica dell'agente si esegue nel portale di Azure e nel server locale che esegue l'agente.

### <a name="azure-portal-agent-verification"></a>Verifica dell'agente nel portale di Azure
Per verificare se l'agente viene visto da Azure, seguire questa procedura:

1. Accedere al portale di Azure.
2. A sinistra selezionare **Azure Active Directory**, fare clic su **Azure ad Connect** e al centro selezionare **Gestisci sincronizzazione cloud**.</br>
![Azure portal](media/how-to-install/install-6.png)</br>

3.  Nella schermata **Azure ad Connect sincronizzazione cloud** fare clic su **Controlla tutti gli agenti**.
![Provisioning di Azure A D](media/how-to-install/install-7.png)</br>
 
4. Nella **schermata degli agenti di provisioning locali** verranno visualizzati gli agenti installati.  Verificare che l'agente in questione sia presente e contrassegnato come **attivo**.
![Agenti di provisioning](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>Nel server locale
Per verificare se l'agente è in esecuzione, seguire questa procedura:

1.  Accedere al server con un account amministratore
2.  Aprire **Servizi** passando all'opzione oppure tramite Start/Esegui/Services.msc.
3.  In **Servizi** assicurarsi che siano presenti i servizi **Microsoft Azure AD Connect Agent Updater** e **Microsoft Azure AD Connect Provisioning Agent** e che il relativo stato sia **In esecuzione**.
![Services](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Configurare Azure AD Connect sincronizzazione cloud
 Per configurare il provisioning, seguire questa procedura

1.  Accedere al portale di Azure AD.
2.  Fare clic su **Azure Active Directory**
3.  Fare clic su **Azure AD Connect**
4.  Per la schermata di **gestione della sincronizzazione cloud** 
 ![ , vedere il collegamento "Gestisci sincronizzazione cloud".](media/how-to-configure/manage-1.png)
5.  Fare clic su **nuova configurazione** 
 ![ screenshot della schermata di Azure A D Connect Cloud Sync con il collegamento "nuova configurazione" evidenziato.](media/tutorial-single-forest/configure-1.png)
7.  Nella schermata di configurazione, immettere un valore per **Messaggio di posta elettronica di notifica**, spostare il selettore su **Abilita** e fare clic su **Salva**.
![Screenshot della schermata di configurazione con il messaggio di posta elettronica di notifica compilato e l'opzione Abilita selezionata.](media/how-to-configure/configure-2.png)
1.  Lo stato della configurazione dovrebbe ora essere **Integro**.
![Screenshot della schermata di sincronizzazione cloud di Azure A D Connect che mostra lo stato integro.](media/how-to-configure/manage-4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verificare che gli utenti siano stati creati e che la sincronizzazione sia in esecuzione
A questo punto, si verificherà che gli utenti presenti nella directory locale siano stati sincronizzati e che ora esistano nel tenant di Azure AD.  Per essere completata, questa operazione può richiedere alcune ore.  Per verificare che gli utenti siano sincronizzati, eseguire le operazioni seguenti.


1. Selezionare il [portale di Azure](https://portal.azure.com) e accedere con un account che dispone di una sottoscrizione di Azure.
2. A sinistra selezionare **Azure Active Directory**
3. In **Gestisci** selezionare **Utenti**.
4. Verificare che vengano visualizzati i nuovi utenti nel tenant</br>

## <a name="test-signing-in-with-one-of-your-users"></a>Testare l'accesso con uno degli utenti

1. Passare a [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Accedere con un account utente creato nel tenant.  Per accedere, sarà necessario usare il formato seguente: (user@domain.onmicrosoft.com). Usare la stessa password che l'utente usa per accedere in locale.</br>
   ![Verificare](media/tutorial-single-forest/verify-1.png)</br>

A questo punto è stato configurato un ambiente di identità ibrido usando Azure AD Connect sincronizzazione cloud.


## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-sync.md)
