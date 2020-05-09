---
title: includere il file
description: Includere file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/10/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 427117fe47294a1db1fa8d3fa1e46ee1efb91b4d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79129288"
---
## <a name="limitations"></a>Limitazioni

- I set di scalabilità di macchine virtuali non sono attualmente supportati negli host dedicati.
- Le dimensioni e i tipi di hardware disponibili per gli host dedicati variano in base all'area. Per altre informazioni, vedere la pagina relativa ai [prezzi](https://aka.ms/ADHPricing) per gli host.

## <a name="create-a-host-group"></a>Creare un gruppo host

Un **gruppo host** è una nuova risorsa che rappresenta una raccolta di host dedicati. È possibile creare un gruppo host in un'area e una zona di disponibilità e aggiungervi host. Quando si pianifica la disponibilità elevata, sono disponibili opzioni aggiuntive. Con gli host dedicati è possibile usare una o entrambe le opzioni seguenti: 
- Span tra più zone di disponibilità. In questo caso, è necessario disporre di un gruppo host in ognuna delle zone che si desidera utilizzare.
- Si estende su più domini di errore di cui è stato eseguito il mapping ai rack fisici. 
 
In entrambi i casi, è necessario specificare il numero di domini di errore per il gruppo host. Se non si desidera estendere i domini di errore nel gruppo, utilizzare un numero di domini di errore pari a 1. 

È anche possibile decidere di usare le zone di disponibilità e i domini di errore. 

In questo esempio verrà creato un gruppo host con 1 zona di disponibilità e 2 domini di errore. 


1. Aprire il [portale](https://portal.azure.com)di Azure.
1. Selezionare **Crea una risorsa** nell'angolo in alto a sinistra.
1. Cercare il **gruppo host** e quindi selezionare **gruppi host** dai risultati.
1. Nella pagina **gruppi host** selezionare **Crea**.
1. Selezionare la sottoscrizione che si vuole usare e quindi selezionare **Crea nuovo** per creare un nuovo gruppo di risorse.
1. Digitare *myDedicatedHostsRG* come **nome** e quindi fare clic su **OK**.
1. Per **nome gruppo host**digitare *myHostGroup*.
1. In **Località** selezionare **Stati Uniti orientali**.
1. Per **zona di disponibilità**selezionare **1**.
1. Per **conteggio domini di errore**selezionare **2**.
1. Selezionare **Verifica + crea** e quindi attendere la convalida.
1. Quando viene visualizzato il messaggio **convalida superata** , selezionare **Crea** per creare il gruppo host.

La creazione del gruppo host dovrebbe richiedere solo alcuni istanti.

## <a name="create-a-dedicated-host"></a>Creazione di un host dedicato

A questo punto, creare un host dedicato nel gruppo host. Oltre a un nome per l'host, è necessario fornire lo SKU per l'host. Lo SKU host acquisisce la serie di macchine virtuali supportate e la generazione dell'hardware per l'host dedicato.

Per altre informazioni sugli SKU e i prezzi degli host, vedere [prezzi di host dedicati di Azure](https://aka.ms/ADHPricing).

Se si imposta un numero di domini di errore per il gruppo host, verrà richiesto di specificare il dominio di errore per l'host.  

1. Selezionare **Crea una risorsa** nell'angolo in alto a sinistra.
1. Cercare **host dedicato** e quindi selezionare **host dedicati** dai risultati.
1. Nella pagina **host dedicati** selezionare **Crea**.
1. Selezionare la sottoscrizione che si vuole usare.
1. Selezionare *myDedicatedHostsRG* come **gruppo di risorse**.
1. In **Dettagli istanza**digitare *host* per **nome** e selezionare *Stati Uniti orientali* per la località.
1. In **profilo hardware**selezionare *standard ES3 Family-type 1* per la **famiglia di dimensioni**, selezionare *myHostGroup* per il **gruppo host** e quindi selezionare *1* per il **dominio di errore**. Lasciare le impostazioni predefinite per gli altri campi.
1. Al termine, selezionare **Verifica + crea** e attendere la convalida.
1. Quando viene visualizzato il messaggio **convalida superata** , selezionare **Crea** per creare l'host.


