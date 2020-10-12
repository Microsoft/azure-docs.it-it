---
title: Ospitare app Web di Azure con carico bilanciato nel dominio radice
description: Usare un record alias del DNS di Azure per ospitare app Web con carico bilanciato nel dominio radice
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: e7c4db7a2fc3ba931415e3b167f7fe72ee2b3980
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710542"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Ospitare app Web di Azure con carico bilanciato nel dominio radice

Il protocollo DNS impedisce l'assegnazione di qualsiasi elemento eccetto un record A/AAAA nel dominio radice, Un esempio di dominio radice è contoso.com. Questa restrizione presenta un problema per i proprietari delle applicazioni che dispongono di applicazioni con carico bilanciato dietro Gestione traffico. Non è possibile puntare al profilo di Gestione traffico dal record di dominio radice. I proprietari delle applicazioni devono quindi usare una soluzione alternativa. Un reindirizzamento al livello applicazione deve reindirizzare dal dominio radice a un altro dominio. Un esempio è un Reindirizzamento da contoso.com a www \. contoso.com. Questo approccio presenta un singolo punto di guasto in termini di funzionalità di reindirizzamento.

Con i record di alias, questo problema non esiste più. I proprietari delle applicazioni possono ora indirizzare i record del dominio radice a un profilo di Gestione traffico con endpoint esterni. I proprietari delle applicazioni possono puntare allo stesso profilo di Gestione traffico che viene usato per qualsiasi altro dominio all'interno della zona DNS.

Ad esempio, contoso.com e www \. contoso.com possono puntare allo stesso profilo di gestione traffico. Ciò avviene fintanto che nel profilo di Gestione traffico sono configurati solo endpoint esterni.

Questo articolo descrive come creare un record alias per il dominio radice e configurare il profilo di Gestione traffico per le app Web.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

È necessario disporre di un nome di dominio che si possa ospitare in DNS di Azure per il test. È necessario disporre del controllo completo di questo dominio, inclusa la possibilità di impostare i record di nome server (NS) per il dominio.

Per istruzioni su come ospitare il dominio in DNS di Azure, vedere [Esercitazione: Ospitare un dominio in DNS di Azure](dns-delegate-domain-azure-dns.md).

Il dominio di esempio usato per questa esercitazione è contoso.com, ma in questo caso usare il nome di dominio personale.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse per contenere le risorse utilizzate in questo articolo.

## <a name="create-app-service-plans"></a>Creare piani di servizio app

Creare due piani di servizio app Web nel gruppo di risorse usando la tabella seguente per le informazioni di configurazione. Per altre informazioni sulla creazione di un piano di servizio app, vedere [Gestire un piano di servizio app in Azure](../app-service/app-service-plan-manage.md).


|Nome  |Sistema operativo  |Location  |Piano tariffario  |
|---------|---------|---------|---------|
|ASP-01     |Windows|Stati Uniti orientali|Sviluppo/test D1-Shared|
|ASP-02     |Windows|Stati Uniti centrali|Sviluppo/test D1-Shared|

## <a name="create-app-services"></a>Creare Servizi app

Creare due app Web, una in ciascun piano di servizio app.

1. Nell'angolo superiore sinistro della pagina portale di Azure selezionare **Crea una risorsa**.
2. Digitare **App Web** nella barra di ricerca e premere INVIO.
3. Selezionare **app Web**.
4. Selezionare **Crea**.
5. Accettare le impostazioni predefinite e usare la tabella seguente per configurare le due app Web:

   |Nome<br>(deve essere univoco all'interno di .azurewebsites.net)|Gruppo di risorse |Stack di runtime|Region|Piano di servizio app/Località
   |---------|---------|-|-|-------|
   |App-01|Usa esistente<br>Selezionare un gruppo di risorse|.NET Core 2.2|Stati Uniti orientali|ASP-01 (D1)|
   |App-02|Usa esistente<br>Selezionare un gruppo di risorse|.NET Core 2.2|Stati Uniti centrali|ASP-02 (D1)|

### <a name="gather-some-details"></a>Raccogliere alcune informazioni dettagliate

A questo punto è necessario annotare l'indirizzo IP e il nome host per le app Web.

1. Aprire il gruppo di risorse e selezionare la prima app Web (**app-01** in questo esempio).
2. Nella colonna sinistra selezionare **Proprietà**.
3. Prendere nota dell'indirizzo sotto **URL** e del primo indirizzo IP dell'elenco sotto **Indirizzi IP in uscita**. Queste informazioni verranno usate in un secondo momento quando si configurano gli endpoint di gestione traffico.
4. Ripetere l'operazione per **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Creare un profilo di Gestione traffico

Creare un gruppo di Gestione traffico nel gruppo di risorse. Usare le impostazioni predefinite e digitare un nome univoco all'interno dello spazio dei nomi trafficmanager.net.

Per informazioni sulla creazione di un profilo di Gestione traffico, vedere [Guida introduttiva: Creare un profilo di Gestione traffico per un'applicazione Web a disponibilità elevata](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Creare endpoint

Ora è possibile creare gli endpoint per le due app Web.

1. Aprire il gruppo di risorse e selezionare il profilo di gestione traffico.
2. Nella colonna sinistra selezionare **endpoint**.
3. Selezionare **Aggiungi**.
4. Usare la tabella seguente per configurare gli endpoint:

   |Type  |Nome  |Destinazione  |Location  |Impostazioni intestazione personalizzata|
   |---------|---------|---------|---------|---------|
   |Endpoint esterno     |End-01|Indirizzo IP registrato per App-01|Stati Uniti orientali|host\<the URL you recorded for App-01\><br>Esempio: **host:app-01.azurewebsites.net**|
   |Endpoint esterno     |End-02|Indirizzo IP registrato per App-02|Stati Uniti centrali|host\<the URL you recorded for App-02\><br>Esempio: **host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Creare una zona DNS

È possibile usare una zona DNS esistente per il testing o creare una nuova zona. Per creare e delegare una nuova zona DNS in Azure, vedere [Esercitazione: Ospitare un dominio in DNS di Azure](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Aggiungere un record TXT per la convalida del dominio personalizzato

Quando si aggiunge un nome host personalizzato alle app Web, si cercherà un record TXT specifico per convalidare il dominio.

1. Aprire il gruppo di risorse e selezionare la zona DNS.
2. Selezionare **Set di record**.
3. Aggiungere il set di record usando la tabella seguente. Per il valore, usare l'URL dell'app Web effettivo registrato in precedenza:

   |Nome  |Type  |valore|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Aggiungere un dominio personalizzato

Aggiungere un dominio personalizzato per entrambe le app Web.

1. Aprire il gruppo di risorse e selezionare la prima app Web.
2. Nella colonna sinistra selezionare **domini personalizzati**.
3. In **domini personalizzati**selezionare **Aggiungi dominio personalizzato**.
4. In **dominio personalizzato**Digitare il nome di dominio personalizzato. Ad esempio, contoso.com.
5. Selezionare **Convalida**.

   Il dominio deve superare la convalida e mostrare segni di spunta verdi accanto alla **disponibilità del nome host** e alla **proprietà del dominio**.
5. Selezionare **Aggiungi dominio personalizzato**.
6. Per vedere il nuovo nome host in **Nomi host assegnati al sito**, aggiornare il browser. L'aggiornamento nella pagina non Mostra sempre le modifiche immediatamente.
7. Ripetere questa procedura per la seconda app Web.

## <a name="add-the-alias-record-set"></a>Aggiungere il set di record di alias

Aggiungere ora un record alias per il vertice della zona.

1. Aprire il gruppo di risorse e selezionare la zona DNS.
2. Selezionare **Set di record**.
3. Aggiungere il set di record usando la tabella seguente:

   |Nome  |Type  |Set di record alias  |Tipo di alias  |Risorsa di Azure|
   |---------|---------|---------|---------|-----|
   |@     |Una|Sì|Risorsa di Azure|Gestione traffico - Profilo personale|


## <a name="test-your-web-apps"></a>Testare le app Web

Ora è possibile eseguire il test per assicurarsi che sia possibile raggiungere l'app Web e che sia in fase di bilanciamento del carico.

1. Aprire un Web browser e passare al dominio. Ad esempio, contoso.com. Dovrebbe essere visualizzata la pagina predefinita dell'app Web.
2. Arrestare la prima app Web.
3. Chiudere il Web browser e attendere alcuni minuti.
4. Avviare il Web browser e passare al dominio. Dovrebbe essere visualizzata ancora la pagina predefinita dell'app Web.
5. Arrestare la seconda app Web.
6. Chiudere il Web browser e attendere alcuni minuti.
7. Avviare il Web browser e passare al dominio. Dovrebbe essere visualizzato l'errore 403 che indica che l'app Web è stata arrestata.
8. Avviare la seconda app Web.
9. Chiudere il Web browser e attendere alcuni minuti.
10. Avviare il Web browser e passare al dominio. Dovrebbe essere visualizzata di nuovo la pagina predefinita dell'app Web.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui record alias, vedere gli articoli seguenti:

- [Esercitazione: Configurare un record alias per fare riferimento a un indirizzo IP pubblico di Azure](tutorial-alias-pip.md)
- [Esercitazione: Configurare un record alias per supportare nomi della radice del dominio con Gestione traffico](tutorial-alias-tm.md)
- [Domande frequenti su DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Per informazioni su come eseguire la migrazione di un nome DNS attivo, vedere [eseguire la migrazione di un nome DNS attivo al servizio app Azure](../app-service/manage-custom-dns-migrate-domain.md).
