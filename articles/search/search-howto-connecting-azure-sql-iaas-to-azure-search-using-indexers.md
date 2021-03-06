---
title: Connessione alla macchina virtuale SQL di Azure per l'indicizzazione della ricerca
titleSuffix: Azure Cognitive Search
description: Abilitare le connessioni crittografate e configurare il firewall per consentire le connessioni a SQL Server in una macchina virtuale (VM) di Azure da un indicizzatore in Azure ricerca cognitiva.
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: 23c5d138463a52f4ff4c52b4a919b71a87b7fd6d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802880"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurare una connessione da un indicizzatore di Azure ricerca cognitiva per SQL Server in una macchina virtuale di Azure

Quando si configura un [indicizzatore SQL di Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq) per estrarre contenuto da un database in una macchina virtuale di Azure, sono necessari passaggi aggiuntivi per le connessioni sicure. 

Una connessione da Azure ricerca cognitiva a SQL Server in una macchina virtuale è una connessione Internet pubblica. Affinché le connessioni sicure abbiano esito positivo, completare i passaggi seguenti:

+ Ottenere un certificato da un [provider di autorità di certificazione](https://en.wikipedia.org/wiki/Certificate_authority#Providers) per il nome di dominio completo dell'istanza di SQL Server nella macchina virtuale

+ Installare il certificato nella macchina virtuale e quindi abilitare e configurare le connessioni crittografate nella VM seguendo le istruzioni riportate in questo articolo.

## <a name="enable-encrypted-connections"></a>Abilitare le connessioni crittografate

Azure ricerca cognitiva richiede un canale crittografato per tutte le richieste di indicizzatore tramite una connessione Internet pubblica. Questa sezione elenca i passaggi necessari per eseguire questa operazione.

1. Controllare le proprietà del certificato per verificare che il nome del soggetto sia il nome di dominio completo (FQDN) della VM di Azure. È possibile usare uno strumento come CertUtils o lo snap-in Certificati per visualizzare le proprietà. È possibile ottenere il nome FQDN dalla sezione Informazioni di base del pannello dei servizi della VM nel campo **Etichetta Indirizzo IP pubblico/Nome DNS** del [portale di Azure](https://portal.azure.com/).
  
   + Per le VM create con il nuovo modello di **Gestione risorse**, il nome FQDN segue la formattazione `<your-VM-name>.<region>.cloudapp.azure.com`

   + Per le macchine virtuali meno recenti create come VM **classica**, il nome FQDN segue la formattazione `<your-cloud-service-name.cloudapp.net>`.

1. Configurare SQL Server per l'uso del certificato con l'editor del Registro di sistema (regedit). 

   Anche se Gestione configurazione SQL Server viene usato spesso per questa attività, non è possibile usarlo per questo scenario. Non troverà il certificato importato perché il nome FQDN della VM in Azure non corrisponde al nome FQDN determinato dalla VM. Identifica il dominio come computer locale o come dominio di rete a cui è aggiunto. Quando i nomi non corrispondono, usare regedit per specificare il certificato.

   + In regedit passare a questa chiave del Registro di sistema: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.

     La parte `[MSSQL13.MSSQLSERVER]` varia a seconda della versione e del nome dell'istanza. 

   + Impostare il valore della chiave del **certificato** sull' **identificazione personale** del certificato TLS/SSL importato nella macchina virtuale.

     È possibile ottenere l'identificazione personale in diversi modi, alcuni dei quali preferibili ad altri. Se la si copia dallo snap-in **Certificati** in MMC, esiste il rischio di selezionare un carattere iniziale invisibile, [come illustrato in questo articolo del supporto](https://support.microsoft.com/kb/2023869/), e di ottenere un errore quando si prova a stabilire una connessione. Esistono diverse soluzioni alternative per risolvere il problema. La più semplice consiste nel premere il tasto BACKSPACE e quindi nel digitare di nuovo il primo carattere dell'identificazione personale per rimuovere il carattere iniziale nel campo del valore della chiave in regedit. In alternativa, è possibile usare un altro strumento per copiare l'identificazione personale.

1. Concedere autorizzazioni all'account del servizio. 

    Verificare che all'account del servizio SQL Server sia concessa l'autorizzazione appropriata per la chiave privata del certificato TLS/SSL. Se si ignora questo passaggio, SQL Server non verrà avviato. È possibile usare lo snap-in **Certificates** o **CertUtils** per questa attività.

1. Riavviare il servizio SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurare la connettività di SQL Server nella VM

Dopo aver configurato la connessione crittografata richiesta da Azure ricerca cognitiva, è necessario eseguire altri passaggi di configurazione intrinseci per SQL Server nelle VM di Azure. Se non è già stato fatto, il passaggio successivo consiste nel completare la configurazione usando uno di questi articoli:

+ Per una VM di **Resource Manager** , vedere [Connettersi a una macchina virtuale di SQL Server in Azure (Gestione risorse)](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md). 

+ Per una VM **classica** , vedere [Connettersi a una macchina virtuale di SQL Server in Azure (distribuzione classica)](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect).

In particolare, vedere in ogni articolo la sezione relativa alla "connessione via Internet".

## <a name="configure-the-network-security-group-nsg"></a>Configurare il gruppo di sicurezza di rete

Non è insolito configurare il gruppo di sicurezza di rete e il corrispondente endpoint o elenco di controllo di accesso (ACL) di Azure in modo da rendere accessibile la VM di Azure ad altri soggetti. Probabilmente questa operazione è stata eseguita prima per consentire alla logica applicativa di connettersi alla VM di SQL Azure. Non è diverso per una connessione di Azure ricerca cognitiva alla VM SQL Azure. 

I collegamenti seguenti forniscono istruzioni sulla configurazione del gruppo di sicurezza di rete per le distribuzioni VM. Usare queste istruzioni per l'ACL di un endpoint di Azure ricerca cognitiva in base al relativo indirizzo IP.

> [!NOTE]
> Per informazioni, vedere [Che cos'è un gruppo di sicurezza di rete](../virtual-network/network-security-groups-overview.md)

+ Per una VM di **Resource Manager** , vedere [Come creare gruppi di sicurezza di rete per le distribuzioni Azure Resource Manager](../virtual-network/tutorial-filter-network-traffic.md).

+ Per una VM **classica** , vedere [Come creare gruppi di sicurezza di rete per le distribuzioni classiche](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps).

L'indirizzamento IP può creare alcune difficoltà facilmente superabili se si conoscono il problema e le potenziali soluzioni alternative. Le sezioni rimanenti contengono suggerimenti per gestire i problemi correlati agli indirizzi IP nell'elenco di controllo di accesso.

### <a name="restrict-access-to-the-azure-cognitive-search"></a>Limitare l'accesso alla ricerca cognitiva di Azure

Si consiglia di limitare l'accesso all'indirizzo IP del servizio di ricerca e all'intervallo di indirizzi IP del tag di `AzureCognitiveSearch` [servizio](../virtual-network/service-tags-overview.md#available-service-tags) nell'ACL anziché rendere aperte le macchine virtuali SQL Azure a tutte le richieste di connessione.

È possibile trovare l'indirizzo IP eseguendo il ping del nome di dominio completo (ad esempio, `<your-search-service-name>.search.windows.net` ) del servizio di ricerca. Sebbene sia possibile modificare l'indirizzo IP del servizio di ricerca, è improbabile che venga modificato. L'indirizzo IP tende a essere statico per la durata del servizio.

È possibile trovare l'intervallo di indirizzi IP del `AzureCognitiveSearch` [tag di servizio](../virtual-network/service-tags-overview.md#available-service-tags) usando [file JSON scaricabili](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) o tramite l' [API di individuazione dei tag di servizio](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). L'intervallo di indirizzi IP viene aggiornato settimanalmente.

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Includere gli indirizzi IP del portale di Azure ricerca cognitiva

Se si usa il portale di Azure per creare un indicizzatore, è necessario concedere al portale l'accesso in ingresso alla macchina virtuale SQL Azure. Una regola in ingresso nel firewall richiede di fornire l'indirizzo IP del portale.

Per ottenere l'indirizzo IP del portale, eseguire il ping `stamp2.ext.search.windows.net` , che è il dominio di gestione traffico. Si otterrà il timeout della richiesta, ma l'indirizzo IP sarà visibile nel messaggio di stato. Ad esempio, nel messaggio "Pinging azsyrie.northcentralus.cloudapp.azure.com [52.252.175.48]", l'indirizzo IP è "52.252.175.48".

> [!NOTE]
> I cluster in aree diverse si connettono a Traffic Manager diversi. Indipendentemente dal nome di dominio, l'indirizzo IP restituito dal ping è quello corretto da usare per la definizione di una regola del firewall in ingresso per il portale di Azure nella propria area.

## <a name="next-steps"></a>Passaggi successivi

Con la configurazione, è ora possibile specificare una SQL Server nella macchina virtuale di Azure come origine dati per un indicizzatore di Azure ricerca cognitiva. Per altre informazioni, vedere [connessione del database SQL di Azure ad azure ricerca cognitiva usando gli indicizzatori](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).