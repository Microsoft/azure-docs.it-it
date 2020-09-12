---
title: Uso di Azure AD Connect Health per la sincronizzazione | Documentazione Microsoft
description: Questa è la pagina di Azure AD Connect Health in cui viene descritto come monitorare  il Servizio di sincronizzazione Azure AD Connect.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 1dfbeaba-bda2-4f68-ac89-1dbfaf5b4015
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1e988bef3876fafdee4f7f6f109858e85d75d0
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89278735"
---
# <a name="monitor-azure-ad-connect-sync-with-azure-ad-connect-health"></a>Monitorare la sincronizzazione di Azure AD Connect con Azure AD Connect Health
La documentazione seguente è specifica per il monitoraggio di Azure Active Directory Connect (Sincronizzazione) con Azure AD Connect Health.  Per informazioni sul monitoraggio di AD FS con Azure AD Connect Health, vedere [Uso di Azure AD Connect Health con AD FS](how-to-connect-health-adfs.md). Per informazioni sul monitoraggio di Servizi di dominio Active Directory con Azure AD Connect Health, vedere [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](how-to-connect-health-adds.md).

![Azure AD Connect Health per la sincronizzazione](./media/how-to-connect-health-sync/syncsnapshot.png)

## <a name="alerts-for-azure-ad-connect-health-for-sync"></a>Avvisi per Azure AD Connect Health per la sincronizzazione
La sezione Avvisi di Azure AD Connect Health contiene l'elenco degli avvisi attivi. Ogni avviso include informazioni pertinenti, la procedura di risoluzione e collegamenti alla documentazione correlata. Selezionando un avviso attivo o risolto, verrà visualizzato un nuovo pannello con altre informazioni, oltre ai passaggi che è possibile eseguire per risolvere l'avviso e i collegamenti ad altri documenti. È anche possibile visualizzare dati storici sugli avvisi risolti in passato.

Selezionando un avviso, vengono fornite informazioni aggiuntive, nonché la procedura per risolvere l'avviso e collegamenti ad altra documentazione.

![Errore del servizio di sincronizzazione Azure AD Connect](./media/how-to-connect-health-sync/alert.png)

### <a name="limited-evaluation-of-alerts"></a>Valutazione limitata degli avvisi
Se Azure AD Connect NON usa la configurazione predefinita (ad esempio, se il filtro attributi è passato dalla configurazione predefinita a una configurazione personalizzata), l'agente Azure AD Connect Health non caricherà gli eventi di errore correlati ad Azure AD Connect.

La valutazione degli avvisi da parte del servizio è quindi soggetta a limiti. Verrà visualizzato un banner indicante questa condizione nel portale di Azure sotto il servizio.

![Azure AD Connect Health per la sincronizzazione](./media/how-to-connect-health-sync/banner.png)

Per modificare questa condizione, fare clic su "Impostazioni" e consentire all'agente Azure AD Connect Health di caricare tutti i log degli errori.

![Azure AD Connect Health per la sincronizzazione](./media/how-to-connect-health-sync/banner2.png)

## <a name="sync-insight"></a>Informazioni dettagliate sulla sincronizzazione
Gli amministratori in genere vogliono conoscere il tempo richiesto per sincronizzare le modifiche in Azure AD e la quantità di modifiche apportate. Questa funzionalità consente di visualizzare facilmente queste informazioni usando i grafici seguenti:   

* Latenza delle operazioni di sincronizzazione
* Tendenza nelle modifiche agli oggetti

### <a name="sync-latency"></a>Latenza della sincronizzazione
Questa funzionalità fornisce una tendenza grafica della latenza delle operazioni di sincronizzazione, importazione, esportazione e così via, per i connettori.  È così disponibile un modo semplice e rapido per conoscere non solo la latenza delle operazioni (che è superiore in caso di set di modifiche esteso), ma anche per rilevare le anomalie a livello di latenza che potrebbero richiedere approfondimenti.

![Latenza della sincronizzazione](./media/how-to-connect-health-sync/synclatency02.png)

Per impostazione predefinita, viene visualizzata solo la latenza dell'operazione di esportazione per il connettore Azure AD.  Per visualizzare altre operazioni nel connettore o le operazioni di altri connettori, fare clic con il pulsante destro del mouse sul grafico e scegliere Modifica grafico oppure fare clic sul pulsante "Modifica il grafico sulla latenza" e scegliere l'operazione e i connettori specifici.

### <a name="sync-object-changes"></a>Sincronizzazione delle modifiche agli oggetti
Questa funzionalità fornisce una tendenza grafica del numero di modifiche valutate ed esportate in Azure AD.  Attualmente, è difficile provare a raccogliere informazioni dai log di sincronizzazione.  Il grafico fornisce non solo un modo più semplice per monitorare il numero di modifiche in corso nell'ambiente, ma anche una panoramica visiva degli errori che si sono verificati.

![Latenza della sincronizzazione](./media/how-to-connect-health-sync/syncobjectchanges02.png)

## <a name="object-level-synchronization-error-report"></a>Report degli errori di sincronizzazione a livello di oggetto
Questa funzionalità offre un report sugli errori di sincronizzazione che possono verificarsi durante la sincronizzazione dei dati relativi alle identità tra Active Directory di Windows Server e Azure AD con Azure AD Connect.

* Il report contiene gli errori registrati dal client di sincronizzazione (Azure AD Connect versione 1.1.281.0 o successiva).
* Include gli errori verificatisi nell'ultima operazione di sincronizzazione nel motore di sincronizzazione (operazione di esportazione nel connettore Azure AD).
* Affinché il report includa i dati più recenti, l'agente di Azure AD Connect Health per la sincronizzazione deve avere la connettività in uscita agli endpoint necessari.
* Il report viene **aggiornato dopo ogni 30 minuti** utilizzando i dati caricati da Azure ad Connect Health agente per la sincronizzazione. Offre le funzionalità principali seguenti:

  * Categorizzazione degli errori
  * Elenco degli oggetti con errore per categoria
  * Tutti i dati sugli errori in un'unica posizione
  * Confronto affiancato degli oggetti con errore a causa di un conflitto
  * Download del report degli errori in formato CSV

### <a name="categorization-of-errors"></a>Categorizzazione degli errori
Il report suddivide gli errori di sincronizzazione esistenti nelle categorie seguenti:

| Category | Descrizione |
| --- | --- |
| Attributo duplicato |Errori che si verificano quando Azure AD Connect prova a creare o aggiornare oggetti con valori duplicati per uno o più attributi di Azure AD che devono essere univoci in un tenant, come proxyAddresses o UserPrincipalName. |
| Dati non corrispondenti |Errori di sincronizzazione che si verificano quando la funzionalità di corrispondenza flessibile non riesce a trovare una corrispondenza con l'oggetto. |
| Errore di convalida dei dati |Errori causati da dati non validi, ad esempio caratteri non supportati in attributi critici come UserPrincipalName o errori di formato che non superano la convalida prima della scrittura in Azure AD. |
| Modifica del dominio federato | Errori che si verificano quando gli account usano un dominio federato diverso. |
| Attributo di grandi dimensioni |Errori che si verificano quando uno o più attributi superano le dimensioni, la lunghezza o il numero consentito. |
| Altri |Tutti gli altri errori che non rientrano nelle categorie precedenti. Questa categoria verrà suddivisa in sottocategorie in base ai commenti e suggerimenti. |

![Riepilogo del report degli errori di sincronizzazione](./media/how-to-connect-health-sync/errorreport01.png)
![Categorie del report degli errori di sincronizzazione](./media/how-to-connect-health-sync/SyncErrorByTypes.PNG)

### <a name="list-of-objects-with-error-per-category"></a>Elenco degli oggetti con errore per categoria
Analizzando ogni categoria, verrà visualizzato l'elenco degli oggetti interessati dall'errore della categoria.
![Elenco del report degli errori di sincronizzazione](./media/how-to-connect-health-sync/errorreport03.png)

### <a name="error-details"></a>Dettagli errore
Nella visualizzazione dettagliata relativa a ogni errore sono disponibili i dati seguenti:

* Attributo in conflitto evidenziato
* Identificatori dell'*oggetto AD* coinvolto
* Identificatori dell'*oggetto Azure AD* coinvolto (a seconda dei casi)
* Descrizione dell'errore e istruzioni per la risoluzione

![Dettagli del report degli errori di sincronizzazione](./media/how-to-connect-health-sync/duplicateAttributeSyncError.png)

### <a name="download-the-error-report-as-csv"></a>Download del report degli errori in formato CSV
La selezione del pulsante "Esporta" consente di scaricare un file CSV con tutti i dettagli relativi a tutti gli errori.

### <a name="diagnose-and-remediate-sync-errors"></a>Diagnosticare e correggere gli errori di sincronizzazione 
Per uno scenario con errori di sincronizzazione di attributi duplicati che comportano l'aggiornamento degli ancoraggi di origine, è possibile correggere gli errori direttamente dal portale. Per altre informazioni, vedere [Diagnosticare e correggere gli errori di sincronizzazione di attributi duplicati](how-to-connect-health-diagnose-sync-errors.md)

## <a name="related-links"></a>Collegamenti correlati
* [Risoluzione degli problemi durante la sincronizzazione](tshoot-connect-sync-errors.md)
* [Resilienza degli attributi duplicati](how-to-connect-syncservice-duplicate-attribute-resiliency.md)
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installazione dell'agente di Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Operazioni di Azure AD Connect Health](how-to-connect-health-operations.md)
* [Uso di Azure AD Connect Health con AD FS](how-to-connect-health-adfs.md)
* [Uso di Azure AD Connect Health con Servizi di dominio Active Directory](how-to-connect-health-adds.md)
* [Domande frequenti su Azure AD Connect Health](reference-connect-health-faq.md)
* [Cronologia delle versioni di Azure AD Connect Health](reference-connect-health-version-history.md)