---
title: Connettori nell'interfaccia utente di Synchronization Service Manager per Azure AD | Microsoft Docs
description: Comprendere la scheda Connettori in Synchronization Service Manager di Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: d215c2e200308664f24daa28a0054c8f1bcfc09c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91319878"
---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Uso dei connettori con Sync Service Manager di Azure AD Connect

![Screenshot che mostra la Service Manager di sincronizzazione.](./media/how-to-connect-sync-service-manager-ui-connectors/connectors.png)

La scheda Connettori consente di gestire tutti i sistemi a cui il motore di sincronizzazione è connesso.

## <a name="connector-actions"></a>Azioni del connettore
| Azione | Commento |
| --- | --- |
| Crea |Non usare. Per la connessione ad altre foreste AD, usare l'installazione guidata. |
| Proprietà |Si usa per i filtri di unità organizzativa e dominio. |
| [Elimina](#delete) |Si usa per eliminare i dati nello spazio connettore o per eliminare la connessione a una foresta. |
| [Configura profili di esecuzione](#configure-run-profiles) |Fatta eccezione per i filtri di dominio, qui non è richiesta alcuna configurazione. Questa azione consente di visualizzare i profili di esecuzione già configurati. |
| Esegui |Si usa per avviare l'esecuzione occasionale di un profilo. |
| Interrompere |Arresta un connettore che sta eseguendo attualmente un profilo. |
| Esporta connettore |Non usare. |
| Importa connettore |Non usare. |
| Aggiorna connettore |Non usare. |
| Aggiorna schema |Aggiorna lo schema memorizzato nella cache. È preferibile usare l'opzione nell'installazione guidata perché aggiorna anche le regole di sincronizzazione. |
| [Spazio connettore di ricerca](#search-connector-space) |Consente di trovare oggetti e seguire un oggetto e i relativi dati attraverso il sistema. |

### <a name="delete"></a>Delete
L'azione di eliminazione viene usata per due scopi diversi.  
![Screenshot che mostra la finestra "Elimina connettore" con il "Elimina solo spazio connettore" selezionato.](./media/how-to-connect-sync-service-manager-ui-connectors/connectordelete.png)

L'opzione **Delete connector space only** (Elimina solo lo spazio connettore) rimuove tutti i dati, ma mantiene la configurazione.

L'opzione **Delete Connector and connector space** (Elimina connettore e spazio connettore) rimuove i dati e la configurazione. Questa opzione viene usata quando non si intende più connettersi a una foresta.

Entrambe le opzioni sincronizzano tutti gli oggetti e aggiornano gli oggetti del metaverse. L'esecuzione di questa azione richiede molto tempo.

### <a name="configure-run-profiles"></a>Configura profili di esecuzione
Questa opzione consente di visualizzare i profili di esecuzione configurati per un connettore.

![Screenshot che mostra la finestra "Configura profili di esecuzione" con l'opzione "importazione delta" selezionata.](./media/how-to-connect-sync-service-manager-ui-connectors/configurerunprofiles.png)

### <a name="search-connector-space"></a>Spazio connettore di ricerca
L’azione Cerca spazio connettore è utile per trovare oggetti e risolvere problemi relativi ai dati.

![Screenshot che mostra la finestra "spazio connettore di ricerca".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearch.png)

Iniziare selezionando un **ambito**. È possibile eseguire ricerche in base ai dati (RDN, DN, Ancoraggio, Sottoalbero) o allo stato dell'oggetto (tutte le altre opzioni).  
![Screenshot che mostra il menu a discesa "scope".](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchscope.png)  
 Se ad esempio si esegue una ricerca nel sottoalbero, si ottengono tutti gli oggetti in una OU.  
![Screenshot che mostra un esempio di ricerca di un sottoalbero.](./media/how-to-connect-sync-service-manager-ui-connectors/cssearchsubtree.png)  
Da questa griglia è possibile selezionare un oggetto, selezionare le **proprietà** e [seguirlo](tshoot-connect-object-not-syncing.md) dallo spazio connettore di origine attraverso il metaverse e fino allo spazio connettore di destinazione.

### <a name="changing-the-ad-ds-account-password"></a>Modifica della password dell'account Active Directory Domain Services
Se si modifica la password dell'account, il servizio di sincronizzazione non sarà più in grado di importare/esportare le modifiche in Active Directory locale.   Potrebbe verificarsi quanto segue:

- L'operazione di importazione/esportazione per il connettore di Active Directory non riesce con errore "no-start-credentials".
- In Visualizzatore eventi di Windows, il registro eventi dell'applicazione contiene un errore con Event ID 6000 e il messaggio 'The management agent "contoso.com" failed to run because the credentials were invalid'.

Per risolvere il problema, aggiornare l'account utente di Active Directory Domain Services mediante la procedura seguente:


1. Avviare Synchronization Service Manager (START → Servizio di sincronizzazione).
</br>![Synchronization Service Manager](./media/how-to-connect-sync-service-manager-ui-connectors/startmenu.png)
2. Passare alla scheda **Connettori**.
3. Selezionare il connettore di Active Directory configurato per usare l'account di Active Directory Domain Services.
4. In azioni selezionare **Proprietà**.
5. Nella finestra di dialogo popup, selezionare Connetti a Foresta Active Directory:
6. Il nome della foresta indica l'annuncio locale corrispondente.
7. Il nome utente indica l'account di Active Directory Domain Services usato per la sincronizzazione.
8. Immettere la nuova password dell'account di Active Directory Domain Services nella casella di testo Password ![Utility per la chiave di crittografia per la sincronizzazione Azure AD Connect](./media/how-to-connect-sync-service-manager-ui-connectors/key6.png)
9. Fare clic su OK per salvare la nuova password e riavviare il servizio di sincronizzazione per rimuovere la vecchia password dalla cache in memoria.



## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sulla configurazione della [sincronizzazione di Azure AD Connect](how-to-connect-sync-whatis.md).

Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
