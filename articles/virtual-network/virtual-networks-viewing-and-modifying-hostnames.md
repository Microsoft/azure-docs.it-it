---
title: Visualizzazione e modifica di nomi host | Documentazione Microsoft
description: Come visualizzare e modificare i nomi host per macchine virtuali di Azure, web e ruoli di lavoro per la risoluzione dei nomi
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c668cd8e-4e43-4d05-acc3-db64fa78d828
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 3f8c35604af9780fb4299bbd7bfd87c3d93ac537
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84702840"
---
# <a name="viewing-and-modifying-hostnames"></a>Visualizzazione e modifica di nomi host
Per consentire alle istanze di ruolo di essere collegate al nome host, è necessario impostare il valore per il nome host nel file di configurazione del servizio per ogni ruolo. Tale operazione è possibile aggiungendo il nome host desiderato all'attributo **vmName** dell'elemento **Role**. Il valore dell'attributo **vmName** viene usato come base per il nome host di ogni istanza del ruolo. Se ad esempio **vmName** è *webrole* e sono disponibili tre istanze di quel ruolo, i nomi host delle istanze saranno *webrole0*, *webrole1* e *webrole2*. Non è necessario specificare un nome host per le macchine virtuali nel file di configurazione, poiché il nome host per una macchina virtuale viene formulato in base al nome della macchina virtuale stessa. Per altre informazioni sulla configurazione di un servizio di Microsoft Azure, vedere [Schema di configurazione dei servizi di Azure (file .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## <a name="viewing-hostnames"></a>Visualizzazione dei nomi host
È possibile visualizzare i nomi host delle macchine virtuali e le istanze del ruolo in un servizio cloud usando uno degli strumenti indicati di seguito.

### <a name="service-configuration-file"></a>File di configurazione del servizio
È possibile scaricare il file di configurazione del servizio per un servizio distribuito dal pannello **Configura** del servizio nel portale di Azure. È possibile quindi cercare l'attributo **vmName** per l'elemento **Role name** per visualizzare il nome host. Tenere presente che questo nome host viene utilizzato come base per il nome host di ogni istanza del ruolo. Se ad esempio **vmName** è *webrole* e sono disponibili tre istanze di quel ruolo, i nomi host delle istanze saranno *webrole0*, *webrole1* e *webrole2*.

### <a name="remote-desktop"></a>Desktop remoto
Dopo aver abilitato Desktop remoto (Windows), comunicazione remota di Windows PowerShell (Windows) o connessioni SSH (Linux e Windows) per le macchine virtuali o le istanze del ruolo, è possibile visualizzare il nome host da una connessione Desktop remoto attiva in vari modi:

* Digitare il nome host nel prompt dei comandi o terminal SSH.
* Digitare ipconfig/all con il comando richiesto (solo Windows).
* Visualizzare il nome del computer nelle Impostazioni di sistema (solo Windows).

### <a name="azure-service-management-rest-api"></a>API REST di gestione dei servizi di Azure
Da un client REST, seguire queste istruzioni:

1. Assicurarsi di disporre di un certificato client per connettersi al portale di Azure. Per ottenere un certificato client, eseguire la procedura descritta in [Procedura: informazioni sul Download e Importazione delle impostazioni di pubblicazione e Sottoscrizione](https://msdn.microsoft.com/library/dn385850.aspx). 
2. Impostare una voce di intestazione denominata x-ms-version con un valore di 2013-11-01.
3. Inviare una richiesta nel formato seguente: https: \/ /Management.Core.Windows.NET/ \<subscrition-id\> sottoscrizione/services/hostedservices/nome \<service-name\> ? Embed-detail = true
4. Cercare l'elemento **HostName** per ogni elemento **RoleInstance**.

> [!WARNING]
> È inoltre possibile visualizzare il suffisso del dominio interno per il servizio cloud dalla risposta chiamata REST controllando l’elemento **InternalDnsSuffix** , o eseguendo ipconfig/all da un prompt dei comandi in una sessione di Desktop remoto (Windows) o eseguendo cat /etc/resolv.conf da un terminale SSH (Linux).
> 
> 

## <a name="modifying-a-hostname"></a>Modifica di un nome host
Si può modificare il nome host per una macchina virtuale o istanza del ruolo caricando un file di configurazione del servizio modificato o rinominando il computer da una sessione Desktop remoto.

## <a name="next-steps"></a>Passaggi successivi
[Risoluzione del nome (DNS)](virtual-networks-name-resolution-for-vms-and-role-instances.md)

[Schema di configurazione dei servizi di Azure (file .cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Attività di configurazione di Rete virtuale di Azure](https://go.microsoft.com/fwlink/?LinkId=248093)

[Specificare le impostazioni DNS tramite i file di configurazione di rete](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)

