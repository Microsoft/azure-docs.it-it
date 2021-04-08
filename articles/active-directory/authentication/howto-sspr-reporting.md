---
title: Report di reimpostazione della password self-service - Azure Active Directory
description: Creazione di rapporti in reimpostazione self-service della password di Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad1b8318e261c7dfef7fc125716736087a84bdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579179"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opzioni di creazione di rapporti per la gestione delle password di Azure AD

Dopo la distribuzione molte organizzazioni vogliono sapere come o se il servizio Reimpostazione password self-service viene effettivamente usato. La funzionalità di creazione di report disponibile in Azure Active Directory (Azure AD) consente di rispondere a domande specifiche grazie a report predefiniti. Se si dispone di una licenza appropriata, è anche possibile creare query personalizzate.

![Creazione di report su SSPR tramite i log di controllo in Azure AD][Reporting]

È possibile rispondere alle domande seguenti grazie ai report presenti nel [Portale di Azure](https://portal.azure.com/):

> [!NOTE]
> È necessario essere [un amministratore globale](../roles/permissions-reference.md) e si deve acconsentire esplicitamente alla raccolta di questi dati per conto dell'organizzazione. A tale scopo, è necessario visualizzare almeno una volta la scheda **Creazione report** o i log di controllo. I dati vengono raccolti per l'organizzazione solo dopo questa operazione.
>

* Quante persone si sono registrate per la reimpostazione delle password?
* Chi ha eseguito la registrazione per la reimpostazione delle password?
* Quali dati vengono registrati?
* Quante persone hanno reimpostato le proprie password negli ultimi sette giorni?
* Quali sono i metodi più comuni usati da utenti o amministratori per reimpostare le proprie password?
* Quali sono i problemi più comuni affrontati da utenti o amministratori quando provano a usare la reimpostazione delle password?
* Con quale frequenza gli amministratori reimpostano le proprie password?
* Sono state rilevate eventuali attività sospette nell'ambito della reimpostazione delle password?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Come visualizzare i rapporti di gestione delle password nel portale di Azure

Nel portale di Azure è disponibile un modo migliore per visualizzare la reimpostazione delle password e l'attività di registrazione per la reimpostazione delle password. Attenersi alla procedura seguente per trovare gli eventi correlati alla reimpostazione delle password e alla registrazione per la reimpostazione delle password:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **Tutti i servizi** nel riquadro a sinistra.
3. Cercare **Azure Active Directory** nell'elenco dei servizi e selezionarlo.
4. Selezionare **utenti** nella sezione Gestisci.
5. Selezionare **log di controllo** nel pannello **utenti** . Vengono visualizzati tutti gli eventi di controllo che si verificano per tutti gli utenti nella directory. È possibile filtrare la visualizzazione per vedere tutti gli eventi correlati alle password.
6. Dal menu **filtro** nella parte superiore del riquadro selezionare l'elenco a discesa **servizio** e modificarlo nel tipo di servizio di gestione delle **password self-service** .
7. Facoltativamente, è possibile filtrare ulteriormente l'elenco scegliendo un'attività specifica in **Attività**.

### <a name="combined-registration"></a>Registrazione combinata

Se è stata abilitata la [registrazione combinata](./concept-registration-mfa-sspr-combined.md), le informazioni relative all'attività dell'utente nei log di controllo saranno disponibili in metodi di autenticazione di **sicurezza**  >  .

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Descrizione delle colonne dei report nel portale di Azure

L'elenco seguente descrive in modo dettagliato le colonne dei report nel portale di Azure:

* **Utente** : l'utente che ha provato a eseguire un'operazione di registrazione per la reimpostazione delle password.
* **Ruolo** : il ruolo dell'utente nella directory.
* **Data e ora** : la data e l'ora del tentativo.
* **Dati registrati**: i dati di autenticazione specificati dall'utente durante la registrazione per la reimpostazione delle password.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Descrizione dei valori dei report nel portale di Azure

Nella tabella seguente sono descritti i diversi valori che è possibile impostare per ogni colonna nel portale di Azure:

| Colonna | Valori consentiti e relativi significati |
| --- | --- |
| Dati registrati |**Indirizzo di posta elettronica alternativo**: l'utente ha usato un indirizzo di posta elettronica alternativo o di autenticazione per autenticarsi.<p><p>**Telefono ufficio**: l'utente ha usato il telefono dell'ufficio per autenticarsi.<p>**Telefono cellulare**: l'utente ha usato un telefono cellulare o di autenticazione per autenticarsi.<p>**Domande di sicurezza**: l'utente ha usato le domande di sicurezza per autenticarsi.<p>**Qualsiasi combinazione dei metodi precedenti (ad esempio, indirizzo di posta elettronica alternativo + cellulare)**: si verifica quando vengono specificati criteri con doppio controllo che indicano i due metodi usati dall'utente durante l'autenticazione per richiedere la reimpostazione della password. |

## <a name="self-service-password-management-activity-types"></a>Tipi di attività di gestione delle password self-service

Vengono visualizzati i seguenti tipi di attività nella categoria degli eventi di controllo **Self-Service Password Management** (Gestione delle password self-service):

* [Bloccato dalla reimpostazione self-service delle password](#activity-type-blocked-from-self-service-password-reset): indica che un utente ha tentato di reimpostare una password, usato un'attività di controllo specifica o convalidato un numero di telefono più di cinque volte in 24 ore.
* [Modificare la password (self-service)](#activity-type-change-password-self-service): indica che un utente ha eseguito una modifica della password volontaria o obbligata (a causa della scadenza).
* [Reimpostare la password (amministratore)](#activity-type-reset-password-by-admin): indica che un amministratore ha eseguito una reimpostazione della password per conto di un utente dal portale di Azure.
* [Reimpostare la password (self-service)](#activity-type-reset-password-self-service): indica che un utente ha reimpostato correttamente la propria password nel [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com).
* [Stato di avanzamento dell'attività di reimpostazione della password self-service](#activity-type-self-serve-password-reset-flow-activity-progress): indica ogni passaggio specifico seguito da un utente (come il passaggio dell'attività di controllo dell'autenticazione per la reimpostazione della password) come parte del processo di reimpostazione della password.
* [Sbloccare l'account utente (self-service)](#activity-type-unlock-a-user-account-self-service)): indica che un utente ha sbloccato correttamente l'account Active Directory senza reimpostare la password dal [portale di reimpostazione della password Azure ad](https://passwordreset.microsoftonline.com) usando la funzionalità Active Directory di sblocco dell'account senza reimpostazione.
* [Utente registrato per la reimpostazione della password self-service](#activity-type-user-registered-for-self-service-password-reset): indica che un utente ha registrato tutte le informazioni necessarie per essere in grado di reimpostare la password in conformità con i criteri di reimpostazione della password del tenant attualmente specificati.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo di attività: Bloccato dalla reimpostazione self-service delle password

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha tentato di reimpostare una password, usato un'attività di controllo specifica o convalidato un numero di telefono più di cinque volte in 24 ore.
* **Activity Actor** (Attore attività): l'utente per il quale è stata imposta una limitazione per ulteriori operazioni di reimpostazione. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente per il quale è stata imposta una limitazione per ulteriori operazioni di reimpostazione. L'utente può essere un utente finale o un amministratore.
* **Stato attività**:
  * _Success_ (Operazione riuscita): indica che un utente è stato limitato dall'esecuzione di qualsiasi reimpostazione, tentativo di metodi di autenticazione alternativi o convalida di numeri di telefono aggiuntivi per le 24 ore successive.
* **Activity Status Failure Reason** (Motivo dell'errore sullo stato dell'attività): non applicabile.

### <a name="activity-type-change-password-self-service"></a>Tipo di attività: Modificare la password (self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha eseguito una modifica della password volontaria o obbligata (a causa della scadenza).
* **Activity Actor** (Attore attività): l'utente che ha modificato la propria password. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha modificato la propria password. L'utente può essere un utente finale o un amministratore.
* **Stati attività**:
  * _Success_ (Operazione riuscita): indica che un utente ha modificato correttamente la propria password.
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a modificare correttamente la propria password. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Activity Status Failure Reason** (Motivo dell'errore sullo stato dell'attività):
  * _FuzzyPolicyViolationInvalidPassword_: l'utente ha selezionato una password automaticamente esclusa dalle funzionalità di rilevamento delle password da escludere di Microsoft, che hanno riscontrato una password troppo comune o particolarmente debole.

### <a name="activity-type-reset-password-by-admin"></a>Tipo di attività: Reimpostare la password (amministratore)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un amministratore ha eseguito una reimpostazione della password per conto di un utente dal portale di Azure.
* **Activity Actor** (Attore attività): l'amministratore che ha eseguito la reimpostazione della password per conto di un altro utente finale o amministratore. Deve essere un amministratore password, un amministratore utente o un amministratore del supporto tecnico.
* **Activity Target** (Destinatario attività): l'utente la cui password è stata reimpostata. L'utente può essere un utente finale o un amministratore diverso.
* **Stati attività**:
  * _Success_ (Operazione riuscita): indica che un amministratore ha reimpostato correttamente la password dell'utente.
  * _Failure_ (Operazione non riuscita): indica che un amministratore non è riuscito a modificare correttamente la password di un utente. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.

### <a name="activity-type-reset-password-self-service"></a>Tipo di attività: Reimpostare la password (self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha reimpostato correttamente la propria password nel [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com).
* **Activity Actor** (Attore attività): l'utente che ha reimpostato la propria password. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha reimpostato la propria password. L'utente può essere un utente finale o un amministratore.
* **Stati attività**:
  * _Success_ (Operazione riuscita): indica che un utente ha reimpostato correttamente la propria password.
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a reimpostare correttamente la propria password. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Activity Status Failure Reason** (Motivo dell'errore sullo stato dell'attività):
  * _FuzzyPolicyViolationInvalidPassword_: l'amministratore ha selezionato una password automaticamente esclusa dalle funzionalità di rilevamento delle password da escludere di Microsoft, che hanno riscontrato una password troppo comune o particolarmente debole.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo di attività: Stato di avanzamento dell'attività di reimpostazione della password self-service

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica ogni passaggio specifico seguito da un utente (come il passaggio dell'attività di controllo dell'autenticazione per la reimpostazione della password) come parte del processo di reimpostazione della password.
* **Activity Actor** (Attore attività): l'utente che ha eseguito parte della password del flusso di reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha eseguito parte della password del flusso di reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Stati attività**:
  * _Success_ (Operazione riuscita): indica che un utente ha completato un passaggio specifico del flusso di reimpostazione della password.
  * _Failure_ (Operazione non riuscita): indica che un passaggio specifico del flusso di reimpostazione della password non è riuscito. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.
* **Activity status reasons** (Motivi dello stato attività): vedere la tabella seguente per [tutti i motivi degli stati relativi alle attività di reimpostazione consentite](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tipo di attività: sbloccare un account utente (self-service)

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha sbloccato il proprio account Active Directory senza reimpostare la password dal [portale di reimpostazione delle password di Azure AD](https://passwordreset.microsoftonline.com) usando la funzionalità di Active Directory per lo sblocco dell'account senza reimpostazione.
* **Activity Actor** (Attore attività): l'utente che ha sbloccato il proprio account senza reimpostare la password. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha sbloccato il proprio account senza reimpostare la password. L'utente può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite):
  * _Success_ (Operazione riuscita): indica che un utente ha sbloccato correttamente il proprio account.
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a sbloccare correttamente il proprio account. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo di attività: Utente registrato per la reimpostazione della password self-service

Nell'elenco seguente viene illustrata in dettaglio questa attività:

* **Activity Description** (Descrizione attività): indica che un utente ha registrato tutte le informazioni necessarie per essere in grado di reimpostare la password in conformità con i criteri di reimpostazione della password del tenant attualmente specificati. 
* **Activity Actor** (Attore attività): l'utente che ha eseguito la registrazione per la reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Activity Target** (Destinatario attività): l'utente che ha eseguito la registrazione per la reimpostazione della password. L'utente può essere un utente finale o un amministratore.
* **Allowed Activity Statuses** (Stati attività consentite):
  * _Success_ (Operazione riuscita): indica che un utente è riuscito a eseguire la registrazione per la reimpostazione della password in base ai criteri correnti. 
  * _Failure_ (Operazione non riuscita): indica che un utente non è riuscito a eseguire la registrazione per la reimpostazione della password. È possibile selezionare la riga per visualizzare la categoria **Activity Status Reason** (Motivo dello stato dell'attività) per altre informazioni sulla causa dell'errore.

     >[!NOTE]
     >Failure (Operazione non riuscita) non significa che un utente non è in grado di reimpostare la propria password. Indica invece che l'utente non ha completato il processo di registrazione. Se sull'account dell'utente sono presenti dati non verificati e corretti, ad esempio un numero di telefono non convalidato, anche se non si esegue la verifica di questo numero di telefono, l'utente può comunque usarlo per reimpostare la password.

## <a name="next-steps"></a>Passaggi successivi

* [Report sull'utilizzo e informazioni dettagliate su SSPR e multi-factor authentication](./howto-authentication-methods-activity.md)
* [Come completare l'implementazione della reimpostazione della password self-service per gli utenti](howto-sspr-deployment.md)
* [Reimpostare o modificare la password](../user-help/active-directory-passwords-update-your-own-password.md).
* Eseguire [la registrazione per la reimpostazione della password self-service](../user-help/active-directory-passwords-reset-register.md).
* [Domande sulle licenze](concept-sspr-licensing.md)
* [Dati usati dalla reimpostazione della password self-service e dati da immettere per gli utenti](howto-sspr-authenticationdata.md)
* [Metodi di autenticazione disponibili per gli utenti](concept-sspr-howitworks.md#authentication-methods)
* [Opzioni dei criteri per la reimpostazione della password self-service](concept-sspr-policy.md)
* [Panoramica del writeback delle password](./tutorial-enable-sspr-writeback.md)
* [Informazioni sulle opzioni della reimpostazione della password self-service](concept-sspr-howitworks.md)
* [Come risolvere i problemi di reimpostazione della password self-service](./troubleshoot-sspr.md)
* [Altre informazioni non illustrate altrove](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Esempio di log di controllo delle attività di reimpostazione password self-service in Azure AD"