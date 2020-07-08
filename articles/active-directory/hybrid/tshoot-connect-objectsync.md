---
title: 'Azure AD Connect: Risolvere i problemi di sincronizzazione degli oggetti | Microsoft Docs'
description: Questo argomento include la procedura per la risoluzione dei problemi relativi alla sincronizzazione degli oggetti tramite l'attività di risoluzione dei problemi.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73d4239dd34f2a64aa7b3edbf88bad4348e01291
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85356203"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Risolvere i problemi di sincronizzazione degli oggetti con la sincronizzazione di Azure AD Connect
Questo articolo include la procedura per la risoluzione dei problemi relativi alla sincronizzazione degli oggetti tramite l'attività di risoluzione dei problemi. Per informazioni sulla risoluzione dei problemi in Connect di Azure Active Directory (Azure AD), guardare [questo breve video](https://aka.ms/AADCTSVideo).

## <a name="troubleshooting-task"></a>Attività di risoluzione dei problemi
Per la distribuzione di Azure AD Connect versione 1.1.749.0 o successiva, usare l'attività specifica nella procedura guidata per la risoluzione dei problemi di sincronizzazione degli oggetti. Per le versioni precedenti, eseguire manualmente la risoluzione dei problemi come illustrato [qui](tshoot-connect-object-not-syncing.md).

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Eseguire l'attività di risoluzione dei problemi nella procedura guidata
Per eseguire l'attività di risoluzione dei problemi nella procedura guidata, seguire questa procedura:

1.  Aprire una nuova sessione di Windows PowerShell nel server di Azure AD Connect con l'opzione Esegui come amministratore.
2.  Eseguire `Set-ExecutionPolicy RemoteSigned` o `Set-ExecutionPolicy Unrestricted`.
3.  Avviare la procedura guidata di Azure AD Connect.
4.  Passare alla pagina Attività aggiuntive, selezionare Risoluzione dei problemi e fare clic su Avanti.
5.  Nella pagina Risoluzione dei problemi fare clic su Avvia per avviare il menu per la risoluzione dei problemi in PowerShell.
6.  Dal menu principale scegliere l'opzione per la risoluzione dei problemi della sincronizzazione degli oggetti.
![Risoluzione dei problemi di sincronizzazione degli oggetti](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Parametri di input per la risoluzione dei problemi
I parametri di input seguenti sono necessari per l'attività di risoluzione dei problemi:
1.  **Nome distinto oggetto**: si tratta del nome distinto dell'oggetto che necessita di risoluzione dei problemi.
2.  **Nome connettore di Active Directory**: si tratta del nome della foresta di AD in cui si trova l'oggetto indicato in precedenza.
3.  Credenziali ![ di amministratore globale delle credenziali di amministratore globale del tenant Azure ad](media/tshoot-connect-objectsync/objsynch1.png)

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Informazioni sui risultati dell'attività di risoluzione dei problemi
L'attività di risoluzione dei problemi effettua i controlli seguenti:

1.  Individuare la mancata corrispondenza del nome dell'entità utente in caso di sincronizzazione dell'oggetto con Azure Active Directory
2.  Verificare se l'oggetto viene escluso a causa dei filtri di dominio
3.  Verificare se l'oggetto viene escluso a causa dei filtri dell'unità organizzativa
4.  Verificare se la sincronizzazione degli oggetti è bloccata a causa di una cassetta postale collegata
5. Verificare se l'oggetto è un gruppo di distribuzione dinamica che non dovrebbe essere sincronizzato

La parte restante di questa sezione descrive i risultati specifici restituiti dall'attività. In ogni caso l'attività fornisce un'analisi seguita dalle azioni consigliate per risolvere il problema.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Individuare la mancata corrispondenza del nome dell'entità utente in caso di sincronizzazione dell'oggetto con Azure Active Directory
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Il suffisso del nome dell'entità utente NON viene verificato nel tenant di Azure AD
Se il nome dell'entità utente o l'ID di accesso alternativo non viene verificato nel tenant di Azure AD, Azure Active Directory sostituisce i suffissi del nome dell'entità utente con il nome di dominio predefinito "onmicrosoft.com".

![Azure AD sostituisce UPN](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>La funzionalità DirSync 'SynchronizeUpnForManagedUsers' del tenant di Azure AD è disabilitata
Se la funzionalità DirSync 'SynchronizeUpnForManagedUsers' del tenant di Azure AD è disabilitata, Azure Active Directory non consente aggiornamenti alla sincronizzazione per il nome dell'entità utente o l'ID di accesso alternativo per account utente con licenza e autenticazione gestita.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>L'oggetto viene escluso a causa dei filtri di dominio
### <a name="domain-is-not-configured-to-sync"></a>Il dominio non è configurato per la sincronizzazione
L'oggetto non è compreso nell'ambito a causa della mancata configurazione del dominio. Nell'esempio seguente l'oggetto non è compreso nell'ambito di sincronizzazione, perché il dominio a cui appartiene viene escluso dalla sincronizzazione tramite filtri.

![Il dominio non è configurato per la sincronizzazione](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Il dominio è configurato per la sincronizzazione ma alcuni profili di esecuzione o passaggi di esecuzione non sono presenti
L'oggetto non è compreso nell'ambito perché nel dominio non sono presenti alcuni profili di esecuzione o passaggi di esecuzione. Nell'esempio seguente l'oggetto non è compreso nell'ambito di sincronizzazione, perché il dominio a cui appartiene non include i passaggi di esecuzione per il profilo di esecuzione dell'importazione completa.
![profili di esecuzione mancanti](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>L'oggetto viene escluso a causa dei filtri dell'unità organizzativa
L'oggetto non è compreso nell'ambito di sincronizzazione a causa della configurazione dei filtri dell'unità organizzativa. Nell'esempio seguente l'oggetto appartiene a OU=NoSync,DC=bvtadwbackdc,DC=com.  Questa unità organizzativa non è inclusa nell'ambito di sincronizzazione.</br>

![OU](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problema relativo alla cassetta postale collegata
Una cassetta postale collegata deve essere associata a un account master esterno che si trova in un'altra foresta account attendibile. Se tale account master esterno non è presente, Azure AD Connect non sincronizzerà l'account utente che corrisponde alla cassetta postale collegata nella foresta Exchange con il tentant Azure AD tenant.</br>
![Cassetta postale collegata](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Problema relativo al gruppo di distribuzione dinamico
A causa di varie differenze l'Active Directory locale e Azure Active Directory, Azure AD Connect non sincronizza i gruppi di distribuzione dinamici con il tenant di Azure AD.

![Gruppo di distribuzione dinamico](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>Report HTML
Oltre ad analizzare l'oggetto, l'attività di risoluzione dei problemi genera anche un report HTML che include tutte le informazioni note sull'oggetto. Il report HTML può essere condiviso con il team del supporto tecnico per operazioni di risoluzione dei problemi aggiuntive, se necessario.

![Report HTML](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
