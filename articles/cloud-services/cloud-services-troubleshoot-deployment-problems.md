---
title: Risolvere i problemi di distribuzione del servizio cloud (versione classica) | Microsoft Docs
description: Durante la distribuzione di un servizio cloud in Azure, è possibile che si verifichino problemi comuni. Questo articolo fornisce soluzioni per alcuni problemi.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 7b3d7a9a674aab3976da9399f71ff4d8df08eb62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741078"
---
# <a name="troubleshoot-azure-cloud-services-classic-deployment-problems"></a>Risolvere i problemi di distribuzione dei servizi cloud di Azure (versione classica)

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).

Quando si distribuisce un pacchetto dell'applicazione di servizi cloud in Azure, è possibile ottenere informazioni sulla distribuzione nel riquadro **Proprietà** del portale di Azure. I dettagli riportati in questo riquadro possono essere usati per risolvere i problemi relativi al servizio cloud e queste informazioni possono essere fornite al supporto tecnico di Azure quando viene aperta una nuova richiesta di supporto.

È possibile trovare il riquadro **Proprietà** come indicato di seguito:

* Nel portale di Azure fare clic sulla distribuzione del servizio cloud, selezionare **Tutte le impostazioni** e quindi **Proprietà**.

> [!NOTE]
> È possibile copiare il contenuto del riquadro **Proprietà** negli Appunti facendo clic sull'icona nell'angolo superiore destro del riquadro.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problema: non è possibile accedere al sito Web, ma la distribuzione è stata avviata e tutte le istanze del ruolo sono pronte.
Il collegamento URL del sito Web mostrato nel portale non include la porta. La porta predefinita per i siti Web è 80. Se l'applicazione è configurata per l'esecuzione in una porta diversa, è necessario aggiungere all'URL il numero di porta corretto durante l'accesso al sito Web.

1. Nel portale di Azure fare clic sulla distribuzione del servizio cloud.
2. Nel riquadro **Proprietà** del portale di Azure selezionare le porte per le istanze del ruolo in **Endpoint di input**.
3. Se la porta non è 80, aggiungere il valore di porta corretto all'URL quando si accede all'applicazione. Per specificare una porta non predefinita, digitare l'URL, seguito da due punti (:) e dal numero di porta, senza spazi.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problema: istanze del ruolo personalizzate riciclate senza intervento dell'utente.
La correzione del servizio si verifica automaticamente quando Azure rileva nodi problematici e quindi le istanze del ruolo vengono spostate in nodi nuovi. In tal caso, è possibile che le istanze del ruolo vengano riciclate automaticamente. Per determinare se si è verificata la correzione del servizio:

1. Nel portale di Azure fare clic sulla distribuzione del servizio cloud.
2. Nel riquadro **Proprietà** del portale di Azure controllare le informazioni e determinare se la correzione del servizio si è verificata quando si è notato il riciclo dei ruoli.

I ruoli vengono riciclati approssimativamente una volta al mese durante gli aggiornamenti del sistema operativo host e del sistema operativo guest.  
Per altre informazioni, vedere il post di blog sui [riavvii delle istanze del ruolo in seguito agli aggiornamenti del sistema operativo](/archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problema: Non è possibile eseguire uno scambio VIP e si riceve un errore
Non è possibile eseguire uno scambio VIP se è in corso l'aggiornamento di una distribuzione. Gli aggiornamenti di distribuzione si possono verificare automaticamente quando:

* È disponibile un nuovo sistema operativo guest e sono stati configurati gli aggiornamenti automatici.
* Si verifica la correzione del servizio.

Per determinare se uno scambio VIP viene impedito da un aggiornamento automatico:

1. Nel portale di Azure fare clic sulla distribuzione del servizio cloud.
2. Nel riquadro **Proprietà** del portale di Azure esaminare il valore di **Stato**. Se è **Pronto**, selezionare **Ultima operazione** per verificare se ne è stata eseguita una di recente che potrebbe impedire lo scambio VIP.
3. Ripetere i passaggi 1 e 2 per la distribuzione di produzione.
4. Se è in corso un aggiornamento automatico, attenderne il completamento prima di provare a eseguire lo scambio VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problema: Viene eseguito il ciclo di un'istanza del ruolo tra Avviato, Inizializzazione in corso, Occupato e Arrestato.
Questa condizione potrebbe indicare un problema relativo al codice, al pacchetto o al file di configurazione dell'applicazione. In tal caso, è possibile osservare nel portale di Azure che lo stato cambia ogni pochi minuti, diventando ad esempio **Riciclo in corso**, **Occupato** o **Inizializzazione in corso**. a indicare un errore dell'applicazione che impedisce l'esecuzione dell'istanza del ruolo.

Per altre informazioni sulla risoluzione di questo problema, vedere il post di blog [Azure PaaS Compute Diagnostics Data](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data) (Dati di diagnostica del calcolo Azure PaaS) e [Problemi comuni che comportano il riciclo dei ruoli](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

## <a name="problem-my-application-stopped-working"></a>Problema: Arresto del funzionamento dell'applicazione
1. Nel portale di Azure fare clic sull'istanza del ruolo.
2. Nel riquadro **Proprietà** del portale di Azure considerare le condizioni seguenti per risolvere il problema:
   * Se l'istanza del ruolo è stata arrestata di recente (si può controllare il valore di **Conteggio interruzioni**), la distribuzione potrebbe essere in fase di aggiornamento. Attendere per verificare se il funzionamento dell'istanza del ruolo riprende autonomamente.
   * Se l'istanza del ruolo è **Occupata**, controllare il codice dell'applicazione per vedere se l'evento [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) è gestito. Potrebbe essere necessario aggiungere o correggere il codice mediante il quale viene gestito l'evento.
   * Esaminare i dati diagnostici e gli scenari per la risoluzione di problemi nel post di blog relativo ai [dati di diagnostica del calcolo Azure PaaS](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).

> [!WARNING]
> Se si ricicla il servizio cloud, si reimpostano le proprietà per la distribuzione, cancellando effettivamente le informazioni per il problema originale.
>
>

## <a name="next-steps"></a>Passaggi successivi
Altri [articoli sulla risoluzione dei problemi](./cloud-services-allocation-failures.md) per i servizi cloud.

Per informazioni su come risolvere i problemi dei ruoli del servizio cloud utilizzando i dati di diagnostica del calcolo Azure PaaS, vedere la [serie di blog di Kevin Williamson](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data).