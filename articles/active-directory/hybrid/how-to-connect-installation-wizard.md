---
title: Eseguire di nuovo l'installazione guidata di Azure AD Connect | Documentazione Microsoft
description: Spiega come funziona la procedura di installazione guidata la seconda volta che viene eseguita.
keywords: L'installazione guidata di Azure AD Connect consente di configurare le impostazioni di manutenzione quando viene eseguita la seconda volta
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d81836b47acb19f624075480aafef74c9c0934c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91306124"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Servizio di sincronizzazione Azure AD Connect: eseguire l'installazione guidata una seconda volta
La prima volta che si esegue l'installazione guidata di Azure AD Connect, viene illustrata la procedura di configurazione dell'installazione. Se si esegue nuovamente l'installazione guidata, verranno messe a disposizione le opzioni di manutenzione.

>[!IMPORTANT]
>Tenere presente che non è possibile eseguire l'installazione guidata mentre è in corso una sincronizzazione.  Prima di avviare la procedura guidata, verificare che non sia in esecuzione una sincronizzazione.

L'installazione guidata si trova nel menu Start **Azure AD Connect**.

![Menu Start](./media/how-to-connect-installation-wizard/startmenu.png)

Quando si avvia l'installazione guidata viene visualizzata una pagina con le opzioni seguenti:

![Pagina con un elenco di attività aggiuntive](./media/how-to-connect-installation-wizard/additionaltasks.png)

Se con Azure AD Connect è stato installato AD FS saranno disponibili ancora più opzioni. Le opzioni aggiuntive per AD FS sono descritte in [Gestione di AD FS](how-to-connect-fed-management.md#manage-ad-fs).

Selezionare una delle attività e fare clic su **Avanti** per continuare.

> [!IMPORTANT]
> Mentre l'installazione guidata è aperta, tutte le operazioni nel motore di sincronizzazione vengono sospese. Assicurarsi di chiudere l'installazione guidata subito dopo aver completato le modifiche della configurazione.
>
>

## <a name="view-current-configuration"></a>Visualizzazione della configurazione corrente
Questa opzione consente di visualizzare rapidamente le opzioni attualmente configurate.

![Pagina con un elenco di tutte le opzioni e il relativo stato](./media/how-to-connect-installation-wizard/viewconfig.png)

Fare clic su **Indietro** per tornare alla pagina precedente. Se si seleziona **Esci**, l'installazione guidata verrà chiusa.

## <a name="customize-synchronization-options"></a>Personalizzazione delle opzioni di sincronizzazione
Questa opzione consente di apportare modifiche alla configurazione della sincronizzazione. Viene visualizzato un sottoinsieme di opzioni dal percorso di installazione personalizzato della configurazione. Queste opzioni vengono visualizzate anche se inizialmente è stata usata l'installazione rapida.

* [Add more directories](how-to-connect-install-custom.md#connect-your-directories)(Aggiunta di altre directory). Per rimuovere una directory, vedere [Eliminazione di un connettore](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Change Domain and OU filtering](how-to-connect-install-custom.md#domain-and-ou-filtering)(Modifica dei filtri di unità organizzativa e dominio).
* Remove Group filtering (Rimozione dei filtri di gruppo).
* [Change optional features](how-to-connect-install-custom.md#optional-features)(Modifica delle funzionalità facoltative).

Le altre opzioni dell'installazione iniziale non possono essere modificate e non sono disponibili. Queste opzioni sono:

* Modifica dell'attributo da usare per userPrincipalName e sourceAnchor.
* Modifica del metodo di abbinamento per gli oggetti di un'altra foresta.
* Abilitazione dei filtri basati sui gruppi.

## <a name="refresh-directory-schema"></a>Aggiorna lo schema della directory
Questa opzione viene usata se lo schema è stato modificato in una delle foreste AD DS locali. Potrebbe ad esempio essere installato Exchange o l'aggiornamento a uno schema di Windows Server 2012 con oggetti dispositivo. In questo caso è necessario indicare ad Azure AD Connect di leggere nuovamente lo schema da AD DS e aggiornare la propria cache. Con questa azione verranno anche rigenerate le regole di sincronizzazione. Se si aggiunge lo schema di Exchange, ad esempio, le regole di sincronizzazione per Exchange vengono aggiunte alla configurazione.

Quando si seleziona questa opzione, vengono elencate tutte le directory presenti nella configurazione. È possibile mantenere l'impostazione predefinita e aggiornare tutte le foreste oppure deselezionare alcune di esse.

![Pagina con un elenco di tutte le directory nell'ambiente](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Configurazione della modalità di gestione temporanea
Questa opzione consente di abilitare e disabilitare la modalità di gestione temporanea nel server. Per altre informazioni sull'uso della modalità di gestione temporanea, vedere [Operazioni](how-to-connect-sync-staging-server.md).

L'opzione indicherà se la gestione temporanea è attualmente abilitata o disabilitata:   
![Screenshot che mostra la modalità di gestione temporanea disabilitata.](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Per modificare lo stato, selezionare questa opzione e selezionare o deselezionare la casella di controllo.  
![Opzione che visualizza anche lo stato attuale della modalità di gestione temporanea](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Cambia l'accesso utente
Questa opzione consente di cambiare il metodo di accesso dell'utente in sincronizzazione dell'hash delle password, autenticazione pass-through o federazione e viceversa. Non è possibile passare a **Non configurare**.

Per altre informazioni su questa opzione, vedere [Accesso utente](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sul modello di configurazione usato dal servizio di sincronizzazione Azure AD Connect, vedere [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Argomenti generali**

* [Servizio di sincronizzazione Azure AD Connect: comprendere e personalizzare la sincronizzazione](how-to-connect-sync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)
