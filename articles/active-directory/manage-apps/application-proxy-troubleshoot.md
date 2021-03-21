---
title: Risolvere i problemi del proxy dell'applicazione | Documentazione Microsoft
description: Illustra come risolvere gli errori nel Proxy applicazione di Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 06/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: cb8fb0e194b4c43b5e247f2ea5d1e38d924591db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257964"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Risolvere i problemi e i messaggi di errore del proxy dell'applicazione

Quando si risolvono i problemi del proxy di applicazione, è consigliabile iniziare con la revisione del flusso di risoluzione dei problemi, il [debug del connettore del proxy dell'](application-proxy-debug-connectors.md)applicazione per determinare se i connettori del proxy di applicazione sono configurati correttamente. Se si verificano ancora problemi di connessione all'applicazione, seguire il flusso di risoluzione dei [problemi di debug](application-proxy-debug-apps.md)dell'applicazione proxy di applicazione.

Se si verificano errori durante l'accesso a un'applicazione pubblicata o durante la pubblicazione di applicazioni, controllare le opzioni seguenti per verificare se il Proxy applicazione di Microsoft Azure AD funziona correttamente: 

* Aprire la console dei servizi Windows. Verificare che il servizio **connettore proxy dell'applicazione Microsoft AAD** sia abilitato e in esecuzione. È anche possibile osservare la pagina delle proprietà del servizio Proxy applicazione, come mostrato nell'immagine seguente:   
  ![Schermata della finestra delle proprietà del connettore Proxy applicazione di Microsoft AAD](./media/application-proxy-troubleshoot/connectorproperties.png)
* Aprire Visualizzatore eventi e cercare gli eventi del connettore del proxy di applicazione in **registri applicazioni e servizi**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector**  >  **admin**.
* Se necessario, [attivando i log di sessione dei connettore proxy di applicazione](application-proxy-connectors.md#under-the-hood) sono disponibili log più dettagliati.

## <a name="the-page-is-not-rendered-correctly"></a>La pagina non viene visualizzata correttamente
È possibile che si verifichino problemi di rendering o di funzionamento dell'applicazione, ma che non vengano visualizzati messaggi di errore specifici. Ciò può verificarsi se è stato pubblicato il percorso dell'articolo, ma l'applicazione richiede contenuto esistente al di fuori di tale percorso.

Se ad esempio si pubblica il percorso `https://yourapp/app`, ma l'applicazione chiama le immagini in `https://yourapp/media`, il rendering di queste ultime non verrà eseguito. Assicurarsi di pubblicare l'applicazione usando il percorso di livello più alto necessario per includere tutto il contenuto rilevante. In questo esempio si tratta di `http://yourapp/`.

## <a name="connector-errors"></a>Errori del connettore

Se la registrazione non riesce durante l'installazione guidata del connettore, esistono due modi per visualizzare il motivo dell'errore. Cercare nel log eventi in **Registri applicazioni e servizi\Microsoft\AadApplicationProxy\Connector\Admin** oppure eseguire il comando di Windows PowerShell seguente:

```powershell
Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1
```

Dopo aver individuato l'errore del connettore nel log eventi, usare questa tabella di errori comuni per risolvere il problema:

| Errore | Procedure consigliate |
| ----- | ----------------- |
| La registrazione del connettore non è riuscita: assicurarsi di avere abilitato il Proxy applicazione nel portale di gestione di Azure e di avere immesso il nome utente e la password di Active Directory corretti. Errore: "Si sono verificati uno o più errori". | Se si è chiusa la finestra di registrazione senza aver eseguito l'accesso ad Azure AD, eseguire di nuovo la creazione guidata del connettore e registrarlo. <br><br> Se si apre la finestra di registrazione e si chiude immediatamente senza consentire l'accesso, probabilmente si otterrà questo errore. L'errore si verifica quando viene rilevato un errore di rete nel sistema. Assicurarsi che sia possibile connettersi da un browser a un sito Web pubblico e che le porte siano aperte come specificato nei prerequisiti del [proxy di applicazione](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| Viene visualizzato un errore di cancellazione nella finestra di registrazione. Impossibile proseguire | Se viene visualizzato questo errore e la finestra si chiude, è stato commesso un errore durante l'immissione di nome utente o password. Riprovare. |
| La registrazione del connettore non è riuscita: assicurarsi di avere abilitato il Proxy applicazione nel portale di gestione di Azure e di avere immesso il nome utente e la password di Active Directory corretti. Errore: "AADSTS50059: non sono state trovate informazioni di identificazione del tenant nella richiesta o incluse in modo implicito nelle credenziali fornite e la ricerca in base all'URI dell'entità servizio non è riuscita". | Si sta provando ad accedere con un account Microsoft e non con un dominio che fa parte dell'ID organizzazione della directory a cui si sta tentando di accedere. Assicurarsi che l'amministratore faccia parte dello stesso nome di dominio del dominio tenant. Ad esempio, se il dominio di Azure AD è contoso.com, l'amministratore dovrà essere admin@contoso.com. |
| Non è possibile recuperare i criteri di esecuzione correnti per l'esecuzione degli script PowerShell. | Se l'installazione del connettore non riesce, verificare che i criteri di esecuzione di PowerShell non siano disabilitati. <br><br>1. Aprire l'Editor Criteri di gruppo.<br>2. passare a **Configurazione computer**  >  **modelli amministrativi**  >  **componenti di Windows**  >  **PowerShell** e fare doppio clic **su attiva l'esecuzione di script**.<br>3. i criteri di esecuzione possono essere impostati su **non configurato** o **abilitato**. Se l'impostazione è **Abilitato**, verificare che in Opzioni la voce Criteri di esecuzione sia impostata su **Consenti script locali e script remoti firmati** o su **Consenti tutti gli script**. |
| Non è stato possibile configurare il connettore. | Il certificato client del connettore usato per l'autenticazione è scaduto. Questo errore può essere visualizzato anche se l'installazione del connettore è protetta da un proxy. In questo caso il connettore non può accedere a Internet e non riuscirà a fornire applicazioni agli utenti remoti. Rinnovare manualmente l'attendibilità con il cmdlet `Register-AppProxyConnector` in Windows PowerShell. Se il connettore è protetto da un proxy, è necessario concedere l'accesso a Internet agli account del connettore "servizi di rete" e "sistema locale". A questo scopo, è possibile concedere agli account l'accesso al proxy o impostarli perché ignorino il proxy. |
| La registrazione del connettore non è riuscita: assicurarsi di essere un amministratore dell'applicazione del Active Directory per registrare il connettore. Errore: "La richiesta di registrazione è stata negata". | L'alias con cui si sta provando ad accedere non è un amministratore nel dominio. Il connettore viene sempre installato per la directory a cui appartiene il dominio dell'utente. Assicurarsi che l'account amministratore con cui si sta provando ad accedere disponga almeno delle autorizzazioni di amministratore dell'applicazione per il tenant Azure AD. |
| Il connettore non è stato in grado di connettersi al servizio a causa di problemi di rete. Il connettore ha tentato di accedere all'URL seguente. | Il connettore non è in grado di connettersi al servizio cloud del proxy di applicazione. Questo problema può verificarsi se si dispone di una regola del firewall che blocca la connessione. Assicurarsi di aver consentito l'accesso alle porte e agli URL corretti elencati nei [prerequisiti del proxy di applicazione](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Errori di Kerberos

Questa tabella contiene gli errori più comuni generati dall'installazione e dalla configurazione di Kerberos e include suggerimenti per la risoluzione.

| Errore | Procedure consigliate |
| ----- | ----------------- |
| Non è possibile recuperare i criteri di esecuzione correnti per l'esecuzione degli script PowerShell. | Se l'installazione del connettore non riesce, verificare che il criterio di esecuzione di PowerShell non sia disabilitato.<br><br>1. Aprire l'Editor Criteri di gruppo.<br>2. passare a **Configurazione computer**  >  **modelli amministrativi**  >  **componenti di Windows**  >  **PowerShell** e fare doppio clic **su attiva l'esecuzione di script**.<br>3. i criteri di esecuzione possono essere impostati su **non configurato** o **abilitato**. Se l'impostazione è **Abilitato**, verificare che in Opzioni la voce Criteri di esecuzione sia impostata su **Consenti script locali e script remoti firmati** o su **Consenti tutti gli script**. |
| 12008: Azure AD ha superato il numero massimo di tentativi di autenticazione Kerberos consentiti al server back-end. | Questo errore può indicare una configurazione non corretta tra Azure AD e il server back-end dell'applicazione oppure un problema di configurazione di data e ora su entrambi i computer. Il server back-end ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che Azure AD e il server applicazioni back-end siano configurati correttamente. Assicurarsi che la configurazione di data e ora in Azure AD e nel server back-end dell'applicazione siano sincronizzate. |
| 13016: Azure AD non riesce a recuperare un ticket Kerberos per conto dell'utente, perché non esiste alcun UPN nel token perimetrale o nel cookie di accesso. | Si è verificato un problema con la configurazione del servizio token di sicurezza. Correggere la configurazione dell'attestazione UPN nel servizio token di sicurezza. |
| 13019: Azure AD non riesce a recuperare un ticket Kerberos per conto dell'utente a causa dell'errore generale dell'API seguente. | Questo evento può indicare una configurazione non corretta tra Azure AD e il server controller di dominio oppure un problema di configurazione di data e ora su entrambi i computer. Il controller di dominio ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che Azure AD e il server applicazioni back-end siano configurati correttamente, in particolare la configurazione del nome dell'entità servizio. Verificare che Azure AD sia aggiunto allo stesso dominio del controller di dominio, per assicurarsi che il controller di dominio stabilisca relazioni di trust con Azure AD. Assicurarsi che la configurazione di data e ora in Azure AD e nel controller di dominio dell'applicazione siano sincronizzate. |
| 13020: Azure AD non riesce a recuperare un ticket Kerberos per conto dell'utente, perché il nome dell'entità servizio del server back-end non è definito. | Questo evento può indicare una configurazione non corretta tra Azure AD e il server controller di dominio oppure un problema di configurazione di data e ora su entrambi i computer. Il controller di dominio ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che Azure AD e il server applicazioni back-end siano configurati correttamente, in particolare la configurazione del nome dell'entità servizio. Verificare che Azure AD sia aggiunto allo stesso dominio del controller di dominio, per assicurarsi che il controller di dominio stabilisca relazioni di trust con Azure AD. Assicurarsi che la configurazione di data e ora in Azure AD e nel controller di dominio dell'applicazione siano sincronizzate. |
| 13022: Azure AD non riesce ad autenticare l'utente perché il server back-end risponde ai tentativi di autenticazione Kerberos con un errore HTTP 401. | Questo evento può indicare una configurazione non corretta tra Azure AD e il server back-end dell'applicazione oppure un problema di configurazione di data e ora su entrambi i computer. Il server back-end ha rifiutato il ticket Kerberos creato da Azure AD. Verificare che Azure AD e il server applicazioni back-end siano configurati correttamente. Assicurarsi che la configurazione di data e ora in Azure AD e nel server back-end dell'applicazione siano sincronizzate. Per altre informazioni, vedere [Risolvere i problemi delle configurazioni della delega vincolata Kerberos per il proxy di applicazione](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Errori utente finale

Questo elenco contiene gli errori che potrebbero verificarsi quando gli utenti finali tentano di accedere all'app senza riuscirci. 

| Errore | Procedure consigliate |
| ----- | ----------------- |
| Il sito Web non riesce a visualizzare la pagina. | L'utente può visualizzare questo errore quando prova ad accedere all'app pubblicata, se l'applicazione è un'applicazione con autenticazione integrata di Windows. Il nome dell'entità servizio dell'applicazione definito potrebbe non essere corretto. Per le app con autenticazione integrata di Windows, assicurarsi che il nome dell'entità servizio configurato per l'applicazione sia corretto. |
| Il sito Web non riesce a visualizzare la pagina. | L'utente può visualizzare questo errore quando prova ad accedere all'app pubblicata, se si tratta di un'applicazione OWA. Il problema può dipendere da uno dei motivi seguenti: <br><li>Il nome dell'entità servizio dell'applicazione definito non è corretto. Assicurarsi che il nome dell'entità servizio configurato per l'applicazione sia corretto.</li><li>L'utente che ha provato ad accedere all'applicazione sta usando un account Microsoft invece dell'account aziendale appropriato oppure si tratta di un utente guest. Assicurarsi che l'utente acceda con il proprio account aziendale corrispondente al dominio dell'applicazione pubblicata. Gli utenti con account Microsoft o guest non possono accedere alle applicazioni con autenticazione integrata di Windows.</li><li>L'utente che ha tentato di accedere all'applicazione non è definito correttamente per l'applicazione sul lato locale. Verificare che l'utente disponga delle autorizzazioni appropriate definite per questa applicazione back-end nel computer locale. |
| Non è possibile accedere a questa app aziendale. L'utente non è autorizzato ad accedere a questa applicazione. Autorizzazione non riuscita. Assicurarsi di assegnare all'utente l'accesso a questa applicazione. | L'utente può ricevere questo errore quando tenta di accedere all'app pubblicata se Usa account Microsoft anziché il proprio account aziendale per accedere. Anche gli utenti guest possono visualizzare questo errore. Gli utenti con account Microsoft o guest non possono accedere alle applicazioni con autenticazione integrata di Windows. Assicurarsi che l'utente acceda con il proprio account aziendale corrispondente al dominio dell'applicazione pubblicata.<br><br>L'utente potrebbe non essere stato assegnato per l'applicazione. Passare alla scheda **Applicazione**, quindi in **Utenti e gruppi** assegnare l'utente o il gruppo di utenti all'applicazione. |
| Non è possibile accedere a questa app aziendale in questo momento. Riprovare più tardi. Timeout del connettore. | L'utente può ricevere questo errore quando tenta di accedere all'app pubblicata se non sono definite correttamente per questa applicazione sul lato locale. Assicurarsi che gli utenti dispongano delle autorizzazioni appropriate come definito per questa applicazione back-end nel computer locale. |
| Non è possibile accedere a questa app aziendale. L'utente non è autorizzato ad accedere a questa applicazione. Autorizzazione non riuscita. Verificare che l'utente disponga di una licenza per Azure Active Directory Premium. | L'utente può ricevere questo errore quando tenta di accedere all'app pubblicata se non è stata assegnata in modo esplicito con una licenza Premium dall'amministratore del Sottoscrittore. Passare alla scheda **licenze** Active Directory del Sottoscrittore e verificare che all'utente o al gruppo di utenti sia stata assegnata una licenza Premium. |
| Impossibile trovare un server con il nome host specificato. | L'utente può ricevere questo errore quando tenta di accedere all'app pubblicata se il dominio personalizzato dell'applicazione non è configurato correttamente. Assicurarsi che sia stato caricato un certificato per il dominio e che il record DNS sia stato configurato correttamente seguendo la procedura descritta in [utilizzo di domini personalizzati nel proxy di applicazione Azure ad](application-proxy-configure-custom-domain.md) |
|Accesso negato: non è possibile accedere a questa app aziendale oppure l'utente non può essere autorizzato. Assicurarsi che l'utente sia definito nell'istanza locale di Active Directory e che l'utente abbia accesso all'app in AD locale. | Potrebbe trattarsi di un problema relativo all'accesso alle informazioni di autorizzazione. vedere [alcune applicazioni e le API richiedono l'accesso alle informazioni di autorizzazione per gli oggetti account]( https://support.microsoft.com/help/331951/some-applications-and-apis-require-access-to-authorization-information). In breve, aggiungere l'account del computer del connettore del proxy di applicazione al gruppo di dominio predefinito "gruppo di accesso di autorizzazione Windows" da risolvere. |

## <a name="my-error-wasnt-listed-here"></a>L'errore non è riportato in questi elenchi

Se si verifica un errore o un problema con il Proxy dell'applicazione Azure AD che non è elencato in questa guida alla risoluzione dei problemi, è necessario segnalarlo. Invia un'email al [team che si occupa del feedback](mailto:aadapfeedback@microsoft.com) specificando i dettagli dell'errore che si è verificato.

## <a name="see-also"></a>Vedi anche
* [Abilitare il proxy di applicazione per Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Pubblicare le applicazioni con il proxy di applicazione](application-proxy-add-on-premises-application.md)
* [Abilita Single Sign-On](application-proxy-configure-single-sign-on-with-kcd.md)
* [Abilitare l'accesso condizionale](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
