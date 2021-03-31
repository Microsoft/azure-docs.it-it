---
title: 'Esercitazione: Ospitare il dominio e il sottodominio - DNS di Azure'
description: Questa esercitazione illustra come configurare DNS di Azure per ospitare le zone DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: rohink
ms.openlocfilehash: a8f64ab3141459142def12a1758b0fe0a94ca432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92282166"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Esercitazione: Ospitare il dominio in DNS di Azure

È possibile usare DNS di Azure per ospitare il dominio DNS e gestire i record DNS. L'hosting dei domini in Azure consente di gestire i record DNS usando gli stessi strumenti, credenziali, API e fatturazione usati per altri servizi Azure.

Si supponga di acquistare il dominio "contoso.net" da un registrar di nomi di dominio e di creare una zona con il nome "contoso.net" nel servizio DNS di Azure. Il registrar offre al proprietario del dominio la possibilità di configurare i record del server dei nomi (NS) per il dominio. Il registrar archivia i record NS nella zona padre .NET. Gli utenti di Internet di tutto il mondo vengono quindi reindirizzati al dominio nella zona DNS di Azure quando provano a risolvere i record DNS in contoso.net.


In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una zona DNS.
> * Recuperare un elenco di server dei nomi.
> * Delegare il dominio.
> * Verificare il funzionamento della delega.


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per il test deve essere disponibile un nome di dominio che possa essere ospitato in DNS di Azure. È necessario disporre del controllo completo di questo dominio, inclusa la possibilità di impostare i record di nome server (NS) per il dominio.

In questo esempio si farà riferimento al dominio padre come **contoso.net**

## <a name="create-a-dns-zone"></a>Creare una zona DNS

1. Accedere al [portale di Azure](https://portal.azure.com/) per creare una zona DNS. Cercare e selezionare **Zone DNS**.

   ![Zona DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Selezionare **Crea zona DNS**.
1. Nella pagina **Crea zona DNS** immettere i valori seguenti e quindi selezionare **Crea**: ad esempio, **contoso.net**
      > [!NOTE] 
      > Se la nuova zona che si sta creando è una zona figlio (ad esempio, zona padre = contoso.net zona figlio = figlio.contoso.net), fare riferimento all'[esercitazione Creazione di una nuova zona DNS figlio](./tutorial-public-dns-zones-child.md)

    | **Impostazione** | **Valore** | **Informazioni dettagliate** |
    |--|--|--|
    | **Dettagli del progetto:**  |  |  |
    | **Gruppo di risorse**    | ContosoRG | Creare un gruppo di risorse. Il nome del gruppo di risorse deve essere univoco all'interno della sottoscrizione selezionata. La località del gruppo di risorse non ha alcun impatto sulla zona DNS. La posizione della zona DNS è sempre "globale" e non viene visualizzata. |
    | **Dettagli istanza:** |  |  |
    | **Zona figlio**        | lasciare deselezionata | Poiché questa zona **non** è una [zona figlio](./tutorial-public-dns-zones-child.md) è necessario lasciare deselezionata questa opzione |
    | **Nome**              | contoso.net | Campo per il nome della zona padre      |
    | **Posizione**          | Stati Uniti orientali | Questo campo si basa sulla posizione selezionata durante la creazione del gruppo di risorse  |
    

## <a name="retrieve-name-servers"></a>Recuperare i server dei nomi

Prima di poter delegare la zona DNS a DNS di Azure, è necessario conoscere i nomi dei server dei nomi per la zona. DNS Azure alloca i server dei nomi da un pool ogni volta che viene creata una zona.

1. Dopo la creazione della zona DNS, nel riquadro **Preferiti** del portale di Azure selezionare **Tutte le risorse**. Nella pagina **Tutte le risorse** selezionare la zona DNS. Se nella sottoscrizione selezionata sono già presenti diverse risorse, è possibile digitare il nome del dominio nella casella **Filtra per nome** per accedere facilmente al gateway applicazione. 

1. Recuperare i server dei nomi dalla pagina della zona DNS. In questo esempio, alla zona contoso.net sono stati assegnati i server dei nomi *ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org* e *ns4-01.azure-dns.info*:

   ![Elenco dei nomi di server](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

DNS di Azure crea automaticamente nella zona i record NS autorevoli per i server dei nomi assegnati.

## <a name="delegate-the-domain"></a>Delegare il dominio

Dopo la creazione della zona DNS e il recupero dei server dei nomi, è necessario aggiornare il dominio padre con i server dei nomi di DNS di Azure. Ogni registrar prevede i propri strumenti di gestione DNS per modificare i record del server dei nomi per un dominio. 

1. Nella pagina di gestione DNS del registrar, modificare i record NS e sostituirli con i server dei nomi di DNS di Azure.

1. Quando si delega un dominio a DNS di Azure, è necessario usare i server dei nomi forniti da DNS di Azure. Usare tutti e quattro i server dei nomi, indipendentemente dal nome del dominio. La delega del dominio non richiede che un server dei nomi usi lo stesso dominio di primo livello del dominio in uso.

> [!NOTE]
> Quando si copia ogni indirizzo di server dei nomi, assicurarsi di copiare il punto finale alla fine dell'indirizzo. Il punto finale indica la fine di un nome di dominio completo. Alcuni registrar aggiungono il punto se il nome del server dei nomi non lo contiene alla fine. Ai fini della conformità a RFC di DNS, includere il punto finale.

Le deleghe che usano server dei nomi nella propria zona, definiti a volte *server dei nomi personali*, non sono attualmente supportate in DNS di Azure.

## <a name="verify-the-delegation"></a>Verificare il funzionamento della delega

Dopo aver completato la delega, è possibile verificarne il corretto funzionamento usando uno strumento come *nslookup* per eseguire una query sul record SOA (Start of Authority) per la zona. Il record SOA viene creato automaticamente durante la creazione della zona. Potrebbe essere necessario attendere più di 10 minuti dopo il completamento della delega prima che sia possibile verificarne il funzionamento. La propagazione delle modifiche in tutto il sistema DNS può richiedere tempo.

Non è necessario specificare i server dei nomi DNS di Azure. Se la delega è configurata in modo corretto, il normale processo di risoluzione DNS trova automaticamente i server dei nomi.

1. Da un prompt dei comandi immettere un comando nslookup simile al seguente:

   ```
   nslookup -type=SOA contoso.net
   ```

1. Verificare che la risposta sia simile all'output di nslookup seguente:

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile mantenere il gruppo di risorse **contosoRG** se si prevede di eseguire l'esercitazione successiva. In caso contrario, eliminare il gruppo di risorse **contosoRG** per eliminare le risorse create in questa esercitazione.

- Selezionare il gruppo di risorse **contosoRG** e quindi **Elimina gruppo di risorse**. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come creare una zona DNS per il dominio ed eseguirne la delega a DNS di Azure. Per informazioni su DNS di Azure e le app Web, proseguire con l'esercitazione sulle app Web.

> [!div class="nextstepaction"]
> [Creare record DNS per un'app Web in un dominio personalizzato](./dns-web-sites-custom-domain.md)
