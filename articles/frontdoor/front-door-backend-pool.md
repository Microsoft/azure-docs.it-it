---
title: Back-end e pool back-end in Azure front door | Microsoft Docs
description: Questo articolo descrive i back-end e i pool back-end che si trovano nella configurazione di sportello anteriore.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4beba141fec7a819df52e4c3a669312a4ad76998
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449295"
---
# <a name="backends-and-backend-pools-in-azure-front-door"></a>Back-end e pool back-end in Azure front door
Questo articolo descrive i concetti relativi a come eseguire il mapping della distribuzione di applicazioni Web con Azure front door. Vengono inoltre illustrati i diversi termini usati nella configurazione della porta anteriore intorno ai backend dell'applicazione.

## <a name="backends"></a>Back-end
Un back-end fa riferimento a una distribuzione di applicazioni Web in un'area. La porta anteriore supporta le risorse di Azure e non di Azure nel pool back-end. L'applicazione può trovarsi nel Data Center locale o in un altro provider di servizi cloud.

I backend front door fanno riferimento al nome host o all'indirizzo IP pubblico dell'applicazione che fornisce le richieste del client. I backend non devono essere confusi con il livello di database, il livello di archiviazione e così via. I back-end devono essere visualizzati come endpoint pubblico per il back-end dell'applicazione. Quando si aggiunge un back-end a un pool back-end della porta anteriore, è necessario aggiungere anche quanto segue:

- **Tipo di host back-end**. Tipo di risorsa che si desidera aggiungere. Lo sportello anteriore supporta l'individuazione automatica dei backend dell'app dal servizio app, dal servizio cloud o dall'archiviazione. Se si vuole una risorsa diversa in Azure o anche in un back-end non di Azure, selezionare **host personalizzato**.

    >[!IMPORTANT]
    >Durante la configurazione, le API non vengono convalidate se il back-end non è accessibile dagli ambienti di porte anteriori. Verificare che lo sportello anteriore possa raggiungere il back-end.

- **Sottoscrizione e nome host back-end**. Se non è stato selezionato **host personalizzato** per il tipo di host back-end, selezionare il back-end scegliendo la sottoscrizione appropriata e il nome host back-end corrispondente nell'interfaccia utente.

- **Intestazione host back-end**. Valore dell'intestazione host inviato al back-end per ogni richiesta. Per altre informazioni, vedere [intestazione host back-end](#hostheader).

- **Priorità**. Assegnare le priorità ai back-end diversi quando si vuole usare un back-end del servizio primario per tutto il traffico. Inoltre, fornire backup se i back-end primari o di backup non sono disponibili. Per altre informazioni, vedere [Priority](front-door-routing-methods.md#priority).

- **Peso**. Assegnare pesi ai diversi backend per distribuire il traffico in un set di backend, in modo uniforme o in base ai coefficienti di peso. Per ulteriori informazioni, vedere [weights](front-door-routing-methods.md#weighted).

### <a name="backend-host-header"></a><a name = "hostheader"></a>Intestazione host backend

Le richieste inviate dalla porta anteriore a un back-end includono un campo di intestazione host usato dal back-end per recuperare la risorsa di destinazione. Il valore di questo campo deriva in genere dall'URI back-end con l'intestazione e la porta host.

Ad esempio, una richiesta effettuata per `www.contoso.com` avrà l'intestazione host www.contoso.com. Se si usa portale di Azure per configurare il back-end, il valore predefinito per questo campo è il nome host del back-end. Se il back-end è contoso-westus.azurewebsites.net, nella portale di Azure il valore popolato automaticamente per l'intestazione dell'host back-end sarà contoso-westus.azurewebsites.net. Tuttavia, se si usano Azure Resource Manager modelli o un altro metodo senza impostare in modo esplicito questo campo, lo sportello anteriore invierà il nome host in ingresso come valore per l'intestazione host. Se la richiesta è stata effettuata per \. contoso.com www e il back-end è contoso-westus.azurewebsites.NET che include un campo di intestazione vuoto, la porta anteriore imposta l'intestazione host come www \. contoso.com.

La maggior parte dei back-end dell'app (app Web di Azure, archiviazione BLOB e servizi cloud) richiede che l'intestazione host corrisponda al dominio del back-end. Tuttavia, l'host front-end che viene indirizzato al back-end utilizzerà un nome host diverso, ad esempio www.contoso.net.

Se il back-end richiede che l'intestazione host corrisponda al nome host back-end, assicurarsi che l'intestazione host backend includa il nome host del back-end.

#### <a name="configuring-the-backend-host-header-for-the-backend"></a>Configurazione dell'intestazione host back-end per il back-end

Per configurare il campo **intestazione host backend** per un back-end nella sezione pool back-end:

1. Aprire la risorsa front door e selezionare il pool back-end con il back-end da configurare.

2. Aggiungere un back-end, se non è già stato fatto, o modificarne uno esistente.

3. Impostare il campo intestazione host back-end su un valore personalizzato o lasciarlo vuoto. Il nome host per la richiesta in ingresso verrà usato come valore dell'intestazione host.

## <a name="backend-pools"></a>Pool back-end
Un pool back-end nella porta anteriore si riferisce al set di back-end che riceve traffico simile per l'app. In altre parole, si tratta di un raggruppamento logico delle istanze dell'app in tutto il mondo che ricevono lo stesso traffico e rispondono con il comportamento previsto. Questi backend vengono distribuiti in aree diverse o all'interno della stessa area. Tutti i backend possono essere in modalità di distribuzione attiva/attiva o definire una configurazione attiva/passiva.

Un pool back-end definisce la modalità di valutazione dei diversi back-end tramite i probe di integrità. Definisce anche il modo in cui viene eseguito il bilanciamento del carico tra di essi.

### <a name="health-probes"></a>Probe di integrità
Il portello anteriore invia richieste di probe HTTP/HTTPS periodiche a ognuno dei backend configurati. Le richieste di probe determinano la vicinanza e l'integrità di ogni back-end per il bilanciamento del carico delle richieste degli utenti finali. Le impostazioni del probe di integrità per un pool back-end definiscono come viene eseguito il polling dello stato di integrità dei back-end Per la configurazione del bilanciamento del carico sono disponibili le impostazioni seguenti:

- **Path**: URL usato per le richieste di probe per tutti i back-end nel pool back-end. Ad esempio, se uno dei backend è contoso-westus.azurewebsites.net e il percorso è impostato su/probe/test.aspx, quindi gli ambienti front-end, supponendo che il protocollo sia impostato su HTTP, invierà le richieste di probe di integrità a http \: //Contoso-westus.azurewebsites.NET/probe/test.aspx.

- **Protocollo**: definisce se inviare le richieste di probe di integrità dalla porta anteriore ai backend con il protocollo http o HTTPS.

- **Metodo**: metodo HTTP da usare per l'invio di probe di integrità. Le opzioni includono GET o HEAD (impostazione predefinita).
    > [!NOTE]
    > Per un carico inferiore e un costo sui backend, la porta anteriore consiglia di usare le richieste HEAD per i probe di integrità.

- **Intervallo (secondi)**: definisce la frequenza di probe di integrità per i backend o gli intervalli in cui ognuno degli ambienti della porta anteriore Invia un probe.

    >[!NOTE]
    >Per un failover più veloce, impostare l'intervallo su un valore inferiore. Più basso è il valore, maggiore è il volume del probe di integrità che i backend ricevono. Se, ad esempio, l'intervallo è impostato su 30 secondi, ad esempio, 100 la porta anteriore viene visualizzata a livello globale, ogni back-end riceverà circa 200 richieste di probe al minuto.

Per altre informazioni, vedere [Probe di integrità](front-door-health-probes.md).

### <a name="load-balancing-settings"></a>Impostazioni di bilanciamento del carico
Le impostazioni di bilanciamento del carico per il pool back-end definiscono il modo in cui vengono valutati i probe di integrità. Queste impostazioni determinano se il back-end è integro o non integro. Viene inoltre controllato come bilanciare il carico del traffico tra back-end diversi nel pool back-end. Per la configurazione del bilanciamento del carico sono disponibili le impostazioni seguenti:

- **Dimensioni campione**. Identifica il numero di campioni di probe di integrità che è necessario considerare per la valutazione dell'integrità back-end.

- **Dimensioni del campione riuscite**. Definisce le dimensioni del campione come indicato in precedenza, il numero di campioni riusciti necessari per chiamare il back-end integro. Si supponga ad esempio che un intervallo di probe di integrità della porta anteriore sia 30 secondi, che le dimensioni del campione siano pari a 5 e che le dimensioni del campione siano 3. Ogni volta che si valutano i probe di integrità per il back-end, si esaminano gli ultimi cinque campioni oltre 150 secondi (5 x 30). Per dichiarare il back-end come integro sono necessari almeno tre Probe con esito positivo.

- **Sensibilità alla latenza (latenza aggiuntiva)**. Definisce se si vuole che la porta anteriore invii la richiesta ai back-end entro l'intervallo di sensibilità della misurazione della latenza o inoltra la richiesta al back-end più vicino.

Per ulteriori informazioni, vedere [metodo di routing con minore latenza](front-door-routing-methods.md#latency).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un profilo Frontdoor](quickstart-create-front-door.md)
- [Funzionamento della porta anteriore](front-door-routing-architecture.md)
