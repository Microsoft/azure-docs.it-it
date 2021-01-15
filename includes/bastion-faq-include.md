---
title: File di inclusione
description: File di inclusione
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48a7912b1a6375111b6f9af1496cbbd41966202c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183932"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>Quali aree sono disponibili?

[!INCLUDE [Azure Bastion region availability](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>È necessario un indirizzo IP pubblico nella macchina virtuale per connettersi tramite Azure Bastion?

No. Quando ci si connette a una macchina virtuale con Azure Bastion, non è necessario un indirizzo IP pubblico nella macchina virtuale di Azure a cui ci si connette. Il servizio Bastion aprirà la sessione/connessione RDP/SSH con la macchina virtuale tramite l'indirizzo IP privato della macchina virtuale, all'interno della rete virtuale.

### <a name="is-ipv6-supported"></a>IPv6 è supportato?

IPv6 non è attualmente supportato. Azure Bastion supporta solo IPv4.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>È necessario un client RDP o SSH?

No. Non è necessario un client RDP o SSH per accedere alla macchina virtuale di Azure tramite RDP/SSH nel portale di Azure. Usare il [portale di Azure](https://portal.azure.com) per accedere tramite RDP/SSH alla macchina virtuale direttamente nel browser.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>È necessario un agente in esecuzione nella macchina virtuale di Azure?

No. Non è necessario installare un agente o altro software nel browser o nella macchina virtuale di Azure. Il servizio Bastion è senza agente e non richiede alcun software aggiuntivo per la connettività RDP/SSH.

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>Quante sessioni RDP e SSH simultanee sono supportate da ogni istanza di Azure Bastion?

RDP e SSH sono protocolli basati sull'utilizzo. Un utilizzo elevato delle sessioni comporta un numero totale inferiore di sessioni supportate dall'host bastion. I numeri riportati di seguito presuppongono normali flussi di lavoro quotidiani.

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Quali funzionalità sono supportate in una sessione RDP?

Al momento, è supportata solo la funzionalità di copia e incolla del testo. Funzionalità quali la copia di file non sono supportate. È possibile condividere commenti e suggerimenti sulle nuove funzionalità nella pagina di [feedback su Azure Bastion](https://feedback.azure.com/forums/217313-networking?category_id=367303).

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>La protezione avanzata di Bastion funziona con le VM aggiunte ad Azure Active Directory?

Questa funzionalità non funziona con le VM aggiunte ad Azure Active Directory con utenti di Azure AD. Per altre informazioni, vedere [Macchine virtuali di Windows Azure e Azure AD](../articles/active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Quali browser sono supportati?

Usare il browser Microsoft Edge o Google Chrome in Windows. Per Apple Mac, usare il browser Google Chrome. Anche Microsoft Edge Chromium è supportato sia in Windows sia in Mac.

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Dove archivia i dati dei clienti Azure Bastion?

Azure Bastion non sposta o archivia i dati dei clienti al di fuori dell'area in cui è distribuito.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Sono necessari ruoli specifici per accedere alla macchina virtuale?

Per stabilire una connessione, sono necessari i ruoli seguenti:

* Ruolo Lettore nella macchina virtuale
* Ruolo Lettore nella scheda di interfaccia di rete con l'indirizzo IP privato della macchina virtuale
* Ruolo Lettore nella risorsa Azure Bastion

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Quali sono i prezzi?

Per altre informazioni vedere la [pagina dei prezzi](https://aka.ms/BastionHostPricing).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Azure Bastion richiede una licenza CAL di Servizi Desktop remoto per scopi amministrativi nelle macchine virtuali ospitate da Azure?

No, per accedere alle macchine virtuali Windows Server con Azure Bastion, non è richiesta una [licenza CAL di Servizi Desktop remoto](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) se viene usato esclusivamente per scopi amministrativi.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Quali layout di tastiera sono supportati durante la sessione remota di Bastion?

Azure Bastion supporta attualmente il layout di tastiera QWERTY en-us nella macchina virtuale.  Il supporto di altre impostazioni locali per il layout di tastiera è in fase di elaborazione.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Il routing definito dall'utente è supportato in una subnet Azure Bastion?

No. Il routing definito dall'utente non è supportato in una subnet Azure Bastion.

Per gli scenari che includono Azure Bastion e Firewall di Azure/appliance di rete virtuale nella stessa rete virtuale, non è necessario forzare il traffico da una subnet Azure Bastion a Firewall di Azure, perché la comunicazione tra Azure Bastion e le VM è privata. Per altre informazioni, vedere [Accesso a macchine virtuali protette da Firewall di Azure con Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Perché viene visualizzato il messaggio di errore "Sessione utente scaduta" prima dell'avvio della sessione di Bastion?

Una sessione deve essere avviata solo dal portale di Azure. Accedere al portale di Azure e avviare di nuovo la sessione. Questo errore è previsto se si passa all'URL direttamente da un'altra sessione o scheda del browser. Consente di assicurarsi che la sessione sia più sicura e che sia accessibile solo tramite il portale di Azure.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Come si gestiscono gli errori di distribuzione?

Esaminare gli eventuali messaggi di errore e, se necessario, [aprire una richiesta di supporto nel portale di Azure](../articles/azure-portal/supportability/how-to-create-azure-support-request.md). Gli errori di distribuzione possono derivare da [limiti, quote e vincoli della sottoscrizione di Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md). In particolare, i clienti potrebbero riscontrare un limite sul numero di indirizzi IP pubblici consentiti per ogni sottoscrizione, che causa un errore della distribuzione di Azure Bastion.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>Come si incorpora Azure Bastion nel piano di ripristino di emergenza?

Azure Bastion viene distribuito all'interno di reti virtuali o reti virtuali con peering ed è associato a un'area di Azure. L'utente è responsabile della distribuzione di Azure Bastion in una rete virtuale del sito di ripristino di emergenza. In caso di errore di un'area di Azure, eseguire un'operazione di failover per le macchine virtuali nell'area di ripristino di emergenza. Usare quindi l'host di Azure Bastion distribuito nell'area di ripristino di emergenza per connettersi alle macchine virtuali ora distribuite in tale area.