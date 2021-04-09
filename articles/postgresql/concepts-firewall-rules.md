---
title: Regole del firewall-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come usare le regole del firewall per connettersi al database di Azure per PostgreSQL-server singolo.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: ba353cf41cf3876a681f8f18d4121401260ff4ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98877171"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Regole del firewall in database di Azure per PostgreSQL-server singolo
Il database di Azure per il server PostgreSQL è sicuro per impostazione predefinita e impedisce l'accesso al server di database finché non si specificano gli host IP autorizzati ad accedervi. Il firewall concede l'accesso al server in base all'indirizzo IP di origine di ogni richiesta.
Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole firewall a livello di server.

**Regole firewall:** queste regole consentono ai client di accedere all'intero Database di Azure per il server PostgreSQL, ovvero a tutti i database all'interno dello stesso server logico. Le regole firewall a livello di server possono essere configurate usando il portale di Azure o i comandi dell'interfaccia della riga di comando di Azure. Per creare regole firewall a livello di server è necessario essere il proprietario o un collaboratore della sottoscrizione.

## <a name="firewall-overview"></a>Panoramica del firewall
Tutti gli accessi al database di Azure per il server PostgreSQL sono bloccati dal firewall per impostazione predefinita. Per accedere al server da un altro computer, client o applicazione, è necessario specificare una o più regole del firewall a livello di server per consentire l'accesso al server. Usare le regole del firewall per specificare gli intervalli di indirizzi IP pubblici consentiti. L'accesso al sito Web del portale di Azure non è determinato dalle regole firewall.
I tentativi di connessione da Internet e Azure devono prima superare il firewall prima di poter raggiungere il database PostgreSQL, come illustrato nel diagramma seguente:

:::image type="content" source="media/concepts-firewall-rules/1-firewall-concept.png" alt-text="Esempio del flusso di funzionamento del firewall":::

## <a name="connecting-from-the-internet"></a>Connessione da Internet
Le regole firewall a livello di server si applicano a tutti i database dello stesso Database di Azure per il server PostgreSQL. Se l'indirizzo IP di origine della richiesta rientra in uno degli intervalli specificati nelle regole del firewall a livello di server, la connessione viene concessa. in caso contrario, viene rifiutata. Ad esempio, se l'applicazione si connette con il driver JDBC per PostgreSQL, potrebbe verificarsi questo errore durante il tentativo di connessione quando il firewall blocca la connessione.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: nessuna voce pg\_hba.conf per l'host "123.45.67.890", utente "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Connessione da Azure
È consigliabile trovare l'indirizzo IP in uscita di qualsiasi applicazione o servizio e consentire in modo esplicito l'accesso a tali indirizzi o intervalli IP singoli. Ad esempio, è possibile trovare l'indirizzo IP in uscita di un servizio app Azure o usare un indirizzo IP pubblico collegato a una macchina virtuale o a un'altra risorsa (vedere di seguito per informazioni sulla connessione con l'indirizzo IP privato di una macchina virtuale tramite gli endpoint del servizio). 

Se un indirizzo IP in uscita fisso non è disponibile per il servizio di Azure, è possibile prendere in considerazione l'abilitazione delle connessioni da tutti gli indirizzi IP dei Data Center di Azure. Questa impostazione può essere abilitata dal portale di Azure impostando l'opzione **Consenti l'accesso a servizi di Azure** **su** attivato dal riquadro **sicurezza connessione** e premendo **Salva**. Dall'interfaccia della riga di comando di Azure, un'impostazione della regola del firewall con indirizzo iniziale e finale uguale a 0.0.0.0 esegue l'equivalente. Se il tentativo di connessione viene rifiutato dalle regole del firewall, non raggiunge il database di Azure per il server PostgreSQL.

> [!IMPORTANT]
> L'opzione **Consenti l'accesso a servizi di Azure** consente di configurare il firewall in modo da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, verificare che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

:::image type="content" source="media/concepts-firewall-rules/allow-azure-services.png" alt-text="Configurare Possibilità di accedere ai servizi di Azure nel portale":::

## <a name="connecting-from-a-vnet"></a>Connessione da un VNet
Per connettersi in modo sicuro al database di Azure per il server PostgreSQL da una VNet, è consigliabile usare gli [endpoint del servizio VNet](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Gestione di regole del firewall a livello di programmazione
Oltre al portale di Azure, le regole firewall possono essere gestite a livello di programmazione usando l'interfaccia della riga di comando di Azure.
Vedere anche [Create and manage Azure Database for PostgreSQL firewall rules using Azure CLI](howto-manage-firewall-using-cli.md) (Creare e gestire regole firewall per il Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure)

## <a name="troubleshooting-firewall-issues"></a>Risoluzione dei problemi relativi al firewall
Quando si effettua l'accesso al servizio Database di Microsoft Azure per il server PostgreSQL non si comporta come previsto, considerare quanto segue:

* **Le modifiche all'elenco Consentiti non sono state ancora applicate:** per rendere effettive le modifiche apportate al firewall del Database di Azure per il server PostgreSQL possono essere necessari fino a cinque minuti.

* **L'accesso non è autorizzato oppure è stata usata una password errata:** quando un account di accesso non dispone delle autorizzazioni per il Database di Azure per il server PostgreSQL o la password usata non è corretta, la connessione al Database di Azure per il server PostgreSQL verrà negata. La creazione di un'impostazione del firewall fornisce ai client soltanto un'opportunità di connessione al server; ogni client deve ancora fornire le credenziali di sicurezza necessarie.

   Ad esempio, se si usa un client JDBC potrebbe essere visualizzato l'errore seguente.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: autenticazione della password non riuscita per l'utente "yourusername"

* **Indirizzo IP dinamico:** se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi di comunicazione attraverso il firewall, è possibile provare una delle seguenti soluzioni:

   * Richiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al Database di Azure per il server MySQL, quindi aggiungere l'intervallo di indirizzi IP come regola firewall.

   * Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP statici come una regola del firewall.

* **L'IP del server sembra essere pubblico:** Le connessioni al database di Azure per il server PostgreSQL vengono instradate tramite un gateway di Azure accessibile pubblicamente. Tuttavia, l'indirizzo IP effettivo del server è protetto dal firewall. Per altre informazioni, vedere l' [articolo sull'architettura della connettività](concepts-connectivity-architecture.md).

* **Non è possibile connettersi dalla risorsa di Azure con l'IP consentito:** Controllare se l'endpoint del servizio **Microsoft. SQL** è abilitato per la subnet da cui si esegue la connessione. Se **Microsoft. SQL** è abilitato, significa che si desidera utilizzare solo [le regole dell'endpoint del servizio VNet](concepts-data-access-and-security-vnet.md) in tale subnet.

   Ad esempio, è possibile che venga visualizzato l'errore seguente se ci si connette da una macchina virtuale di Azure in una subnet in cui è abilitato **Microsoft. SQL** ma non è presente alcuna regola VNet corrispondente:  `FATAL: Client from Azure Virtual Networks is not allowed to access the server`

* La **regola del firewall non è disponibile per il formato IPv6:** Le regole del firewall devono essere in formato IPv4. Se si specificano le regole del firewall in formato IPv6, verrà visualizzato l'errore di convalida.


## <a name="next-steps"></a>Passaggi successivi
* [Create and manage Azure Database for PostgreSQL firewall rules using the Azure portal](howto-manage-firewall-using-portal.md) (Creare e gestire regole firewall per il Database di Azure per PostgreSQL usando il portale di Azure)
* [Creare e gestire regole del firewall di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure](howto-manage-firewall-using-cli.md)
* [Endpoint del servizio VNet nel database di Azure per PostgreSQL](./concepts-data-access-and-security-vnet.md)
