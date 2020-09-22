---
title: Gestire le regole del firewall-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Creare e gestire le regole del firewall per database di Azure per PostgreSQL-server singolo usando il portale di Azure
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: b2cb6a5378afac74c971ba5429775782723bef09
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90882078"
---
# <a name="create-and-manage-firewall-rules-for-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Creare e gestire le regole del firewall per database di Azure per PostgreSQL-server singolo usando il portale di Azure
Le regole del firewall a livello di server possono essere usate per gestire l'accesso a un database di Azure per il server PostgreSQL da un indirizzo IP specificato o da un intervallo di indirizzi IP.

Le regole della rete virtuale (VNet) possono essere usate anche per proteggere l'accesso al server. Altre informazioni sulla [creazione e la gestione di endpoint e regole del servizio rete virtuale usando il portale di Azure](howto-manage-vnet-using-portal.md).

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Un server [Creare un database di Azure per PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Creare una regola del firewall a livello di server nel portale di Azure
1. Nell'intestazione Impostazioni della pagina del server PostgreSQL fare clic su **Sicurezza connessione** per aprire la pagina Sicurezza connessione del Database di Azure per PostgreSQL.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Portale di Azure: fare clic su Sicurezza connessione":::

2. Fare clic su **Aggiungi indirizzo IP corrente** sulla barra degli strumenti. Viene creata automaticamente una regola del firewall con l'indirizzo IP pubblico del computer rilevato dal sistema Azure.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Portale di Azure: fare clic su Aggiungi indirizzo IP corrente":::

3. Verificare l'indirizzo IP prima di salvare la configurazione. In alcuni casi, l'indirizzo IP individuato dal portale di Azure è diverso da quello usato per l'accesso a Internet e ai server Azure, quindi potrebbe essere necessario modificare l'indirizzo IP iniziale e l'indirizzo IP finale per fare in modo che la regola funzioni come previsto.
   Usare un motore di ricerca o un altro strumento online per controllare l'indirizzo IP. Ad esempio, cercare "qual è l'indirizzo IP".

   :::image type="content" source="./media/howto-manage-firewall-using-portal/3-what-is-my-ip.png" alt-text="Ricerca Bing di What is my IP":::

4. Aggiungere altri intervalli di indirizzi. Nelle regole firewall per Database di Azure per PostgreSQL è possibile specificare un solo indirizzo IP o un intervallo di indirizzi. Per limitare la regola a un solo indirizzo IP, digitare lo stesso indirizzo nei campi Indirizzo IP iniziale e Indirizzo IP finale. L'apertura del firewall consente agli amministratori, agli utenti e alle applicazioni di accedere a qualsiasi database nel server PostgreSQL per cui hanno credenziali valide.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Regole del firewall portale di Azure":::

5. Fare clic su **Salva** sulla barra degli strumenti per salvare questa regola del firewall a livello di server. Attendere la conferma che l'aggiornamento delle regole del firewall abbia avuto esito positivo.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Portale di Azure: fare clic su Salva":::

## <a name="connecting-from-azure"></a>Connessione da Azure
Per consentire alle applicazioni da Azure di stabilire la connessione al server di Database di Azure per PostgreSQL, è necessario abilitare le connessioni da Azure. Ad esempio, per ospitare un'applicazione delle app Web di Azure o un'applicazione che viene eseguita in una macchina virtuale di Azure oppure per connettersi da un gateway di gestione dati di Azure Data Factory. Le risorse non devono essere necessariamente nella stessa rete virtuale (VNet) o nello stesso gruppo di risorse perché la regola del firewall consenta tali connessioni. Quando si tenta di connettersi al server di database da un'applicazione di Azure, il firewall verifica che le connessioni da Azure siano consentite. Sono disponibili alcuni metodi per consentire questi tipi di connessioni. Un'impostazione del firewall con indirizzo iniziale e finale uguale a 0.0.0.0 indica che queste connessioni sono consentite. In alternativa, è possibile impostare l'opzione **Consenti l'accesso a servizi di Azure** **su on** nel portale dal riquadro **sicurezza connessione** e fare clic su **Salva**. Se il tentativo di connessione non è consentito, la richiesta non raggiungerà il server di Database di Azure per PostgreSQL.

> [!IMPORTANT]
> Questa opzione configura il firewall in modo da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gestione delle regole del firewall a livello di server esistenti tramite il portale di Azure
Ripetere i passaggi per gestire le regole del firewall.
* Per aggiungere il computer corrente, fare clic sul pulsante + **Aggiungi indirizzo IP corrente**. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per aggiungere ulteriori indirizzi IP, digitare nome della regola, indirizzo IP iniziale e indirizzo IP finale. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per modificare una regola esistente, fare clic su uno dei campi nella regola e modificare. È consigliabile fare clic su **Salva** per salvare le modifiche.
* Per eliminare una regola esistente, fare clic sui puntini di sospensione […] e fare clic su **Elimina** per rimuovere la regola. È consigliabile fare clic su **Salva** per salvare le modifiche.

## <a name="next-steps"></a>Passaggi successivi
- Analogamente, è possibile creare uno script per [creare e gestire regole del firewall di database di Azure per PostgreSQL usando l'interfaccia della riga](howto-manage-firewall-using-cli.md)di comando
- Proteggere ulteriormente l'accesso al server [creando e gestendo gli endpoint e le regole del servizio rete virtuale usando il portale di Azure](howto-manage-vnet-using-portal.md).
- Per informazioni sulla connessione a un database di Azure per il server PostgreSQL, vedere [raccolte di connessioni per database di Azure per PostgreSQL](concepts-connection-libraries.md).
