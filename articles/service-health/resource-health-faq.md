---
title: Domande frequenti su Integrità risorse di Azure
description: Panoramica su Integrità risorse di Azure
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: dc24688d8393b7a13e8c93d3f4d0171cce4c213c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570060"
---
# <a name="azure-resource-health-faq"></a>Domande frequenti su Integrità risorse di Azure
Risposte alle domande comuni su Integrità risorse di Azure.

## <a name="what-is-azure-resource-health"></a>Che cos'è Integrità risorse di Azure?
Integrità risorse consente di eseguire una diagnosi e di ottenere supporto quando un problema di Azure ha effetto sulle risorse. Informa sull'integrità corrente e passata delle risorse e consente di attenuare i problemi. Integrità risorse offre supporto tecnico quando è necessaria assistenza per problemi con i servizi di Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>A cosa serve Integrità risorse?
Quando viene rilevato un problema relativo a una risorsa, Integrità risorse consente di diagnosticarne la causa radice. Offre suggerimenti per attenuare il problema e supporto tecnico quando è necessaria ulteriore assistenza per risolvere problemi legati ai servizi di Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Quali controlli di integrità vengono eseguiti da Integrità risorse?
Integrità risorse esegue vari controlli in base al [tipo di risorsa](resource-health-checks-resource-types.md). Questi controlli sono progettati per implementare tre tipi di problemi: 
- Eventi non pianificati, ad esempio un riavvio imprevisto dell'host
- Eventi pianificati, ad esempio aggiornamenti pianificati del sistema operativo host
- Eventi attivati dalle azioni dell'utente, ad esempio il riavvio di una macchina virtuale

## <a name="what-does-each-of-the-health-status-mean"></a>Cosa significa ciascuno stato di integrità delle risorse?
Esistono tre stati di integrità diversi:
- Disponibile: non si sono verificati problemi noti nella piattaforma Azure che potrebbero influire sulla risorsa
- Non disponibile: Integrità risorse ha rilevato problemi che influiscono sulla risorsa
- Sconosciuto: Integrità risorse non può determinare l'integrità di una risorsa perché non riceve più informazioni su di essa. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Che cosa significa lo stato Sconosciuto? Indica un problema relativo alla risorsa?
Lo stato di integrità è impostato su Sconosciuto quando Integrità risorse non riceve più informazioni su una risorsa specifica. Sebbene non si tratti di un'indicazione definitiva dello stato della risorsa, in caso di errata esecuzione, potrebbe indicare la presenza di un problema di Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Come è possibile ottenere supporto per una risorsa che non è disponibile?
È possibile inviare una richiesta di supporto dal pannello di Integrità risorse. Se la risorsa non è disponibile a causa di eventi della piattaforma, non è necessario un contratto di supporto con Microsoft per aprire una richiesta.

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>Integrità risorse distingue tra la mancata disponibilità causata da problemi della piattaforma rispetto a una cosa che ho fatto?
Sì, quando una risorsa non è disponibile, Integrità risorse identifica la causa radice tra una delle categorie seguenti: 
-   Azione avviata dall'utente
-   Evento pianificato 
-   Evento non pianificato

Nel portale le azioni avviate dall'utente sono indicate da un'icona di notifica blu, mentre gli eventi pianificati e non pianificati sono indicati da un'icona di avviso rossa. Altri dettagli sono disponibili in [Panoramica su Integrità risorse di Azure](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>È possibile aggiungere Integrità risorse agli strumenti di monitoraggio personali?
Integrità risorse [supporta](resource-health-alert-arm-template-guide.md) gli avvisi basati sul log attività. Gli avvisi del log attività usano i [gruppi di azioni](../azure-monitor/alerts/action-groups.md) per notificare agli utenti che è stato attivato un avviso. I gruppi di azione supportano un'ampia gamma di canali di notifica, ad esempio le azioni di posta elettronica, SMS, webhook e ITSM.

## <a name="where-do-i-find-resource-health"></a>Dove si trova Integrità risorse?
Dopo avere eseguito l'accesso al portale di Azure, è possibile accedere a Integrità risorse in diversi modi:
- Passare alla risorsa. Nel riquadro di spostamento a sinistra selezionare **Integrità risorse**.
- Passare al pannello Integrità dei servizi di Azure.  Nella finestra di spostamento a sinistra selezionare **integrità risorse**.

È anche possibile usare l'API di Integrità risorse per ottenere informazioni sullo stato delle risorse.

## <a name="is-resource-health-available-for-all-resource-types"></a>Integrità risorse è disponibile per tutti i tipi di risorse?
L'elenco dei controlli di integrità e dei tipi di risorse supportati per Integrità risorse è reperibile [qui](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Quale operazione è necessario eseguire se la risorsa risulta disponibile ma si ritiene che non lo sia?
Durante il controllo dell'integrità di una risorsa, immediatamente sotto lo stato di integrità è possibile fare clic su **Segnala stato di integrità non corretto**. Prima di inviare il report, è possibile specificare ulteriori dettagli sul motivo per cui si ritiene che lo stato di integrità corrente non sia corretto.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Integrità risorse è disponibile in tutte le aree di Azure? 
Integrità risorse è disponibile in tutte le aree geografiche di Azure.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>In che modo Integrità risorse differisce dallo Stato di Azure o dal dashboard per l'integrità dei servizi?
Le informazioni messe a disposizione da Integrità risorse sono più specifiche di quelle messe a disposizione da Stato di Azure o dal dashboard per l'integrità dei servizi di Azure.

Diversamente dallo [Stato di Azure](https://status.azure.com) e dal dashboard per l'integrità dei servizi che segnalano la presenza di problemi che interessano un elevato numero di clienti, ad esempio quelli di una determinata area di Azure, Integrità risorse espone eventi più granulari rilevanti solo per la risorsa specifica. Se, ad esempio, un host viene riavviato in modo imprevisto, Integrità risorse avvisa solo i clienti le cui macchine virtuali sono in esecuzione nell'host.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>È necessario attivare Integrità risorse per ogni risorsa?
No, le informazioni di integrità sono disponibili per tutti i tipi di risorse supportati da Integrità risorse. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>È necessario abilitare Integrità risorse per l'organizzazione?
No.  Integrità risorse di Azure è accessibile dal portale di Azure e non prevede requisiti di configurazione.

## <a name="is-resource-health-available-free-of-charge"></a>Integrità risorse è disponibile gratuitamente?
Sì.  Integrità risorse di Azure è gratuito.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Quali sono i suggerimenti offerti da Integrità risorse?
In base allo stato di integrità, Integrità risorse offre consigli per ridurre il tempo impiegato per la risoluzione dei problemi. Per le risorse disponibili, i consigli hanno l'obiettivo di risolvere i problemi più comuni riscontrati dai clienti. Se la risorsa non è disponibile a causa di un evento non pianificato di Azure, l'obiettivo sarà quello di assistere l'utente durante e dopo il processo di ripristino. 

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su Integrità risorse:
-  [Panoramica di Integrità risorse di Azure](Resource-health-overview.md)
-  [Tipi di risorse e controlli di integrità disponibili tramite Integrità risorse di Azure](resource-health-checks-resource-types.md)
