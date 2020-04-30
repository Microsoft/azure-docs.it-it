---
title: Immettere i dettagli della vetrina per l'offerta di consulenza | Azure Marketplace
description: Definire i dettagli della vetrina in un'offerta di servizio di consulenza di Azure o Dynamics 365 nella portale Cloud Partner.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 9fd1330e0ca0a49556e79044570173adaf458e13
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148153"
---
# <a name="storefront-details-tab"></a>Scheda Dettagli vetrina

>[!Important]
>A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte del servizio di consulenza al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [Panoramica della creazione di servizi di consulenza](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-consulting-service-offer) .

Questo articolo illustra come immettere i dettagli relativi alla vetrina. La scheda **Dettagli vetrina** è costituita dalle sezioni seguenti:

-   **Dettagli offerta**
-   **Informazioni sul server di pubblicazione**
-   **Dettagli elenco**
-   **Elementi di marketing**

![Creare una nuova offerta di servizi di consulenza - Scheda Dettagli vetrina](media/consultingoffer-storefront-details.png)


## <a name="offer-details"></a>Dettagli offerta

La sezione **Dettagli offerta** contiene le caselle seguenti:

-   **Riepilogo offerta\***
-   **Descrizione offerta\***


### <a name="offer-summary"></a>Riepilogo offerta\*

Il riepilogo dell'offerta è una breve descrizione dell'offerta visualizzata immediatamente sotto al nome dell'offerta. Per immettere il riepilogo dell'offerta, usare testo normale senza interruzioni di riga. Di seguito sono riportati alcuni ottimi esempi di riepilogo offerta insieme ai relativi nomi offerta corrispondenti.

*Esempio 1*

-   **Nome offerta**: analisi cloud: workshop di 3 giorni
-   **Riepilogo dell'offerta**: panoramica di Microsoft Azure e Power bi, valutazione dell'ambiente corrente e mini poc.

*Esempio 2*

-   **Nome offerta**: Industrial Azure Internet: modello di verifica per 30 giorni
-   **Riepilogo dell'offerta**: creare un progetto pilota di prodotto connesso industrialmente per connettere in modo sicuro le apparecchiature nel campo a una soluzione hub Azure Internet con dashboard, report e notifiche.

*Esempio 3*

-   **Nome offerta**: Servizi professionali: Briefing 1-hr
-   **Riepilogo dell'offerta**: panoramica e demo di una soluzione preconfigurata di Dynamics 365 per le operazioni che offre una gestione avanzata di progetti, fatturazione e risorse per servizi professionali.

*Esempio 4*

-   **Nome offerta**: Power bi nel mondo: workshop di 4 ore
-   **Riepilogo dell'offerta**: iniziare a usare il primo dashboard e apprendere le procedure consigliate. Per un massimo di 12 studenti, in loco.

*Esempio 5*

-   **Nome dell'offerta**: Dynamics e Projects: valutazione di 3 giorni
-   **Riepilogo dell'offerta**: raccolta e valutazione dei requisiti per una soluzione ERP progettata per le aziende di servizi professionali e per le aziende basate su progetti.


### <a name="offer-description"></a>Descrizione dell'offerta\*

Immettere la descrizione dell'offerta del servizio di consulenza nella casella **Descrizione dell'offerta**. Una buona descrizione dell'offerta include i dettagli esatti di come sarà l'engagement e quale sarà la consegna finale al cliente. Dovrebbe aiutare i clienti a comprendere in maniera chiara qual'è il risultato finale. Includere anche come l'offerta si rapporta con il prodotto Microsoft per cui si offrono servizi di consulenza.

Non includere l'indirizzo e-mail o il numero di telefono nella descrizione dell'offerta. L'offerta comprenderà un pulsante **Contattami** per caricare i clienti potenziali nella destinazione di gestione dei clienti potenziali che verrà individuata per l'offerta.

Inserire la descrizione dell'offerta in formato Markdown. Se non si ha familiarità con Markdown o con la formattazione per HTML, vedere [Usare Markdown per scrivere articoli di Docs](/contribute/markdown-reference).

Usare questi formati per assicurarsi che l'offerta sia di facile lettura per i clienti.

La descrizione dell'offerta deve essere breve e rispettare il limite di caratteri, in quanto gli utenti non amano leggere testi lunghi. È inoltre possibile caricare brochure di marketing, schede tecniche e altri documenti che descrivono l'offerta in modo più dettagliato.

L'esempio che segue illustra una descrizione dell'offerta ben composta e il relativo nome e riepilogo:

**Nome offerta**: analisi cloud: workshop di 3 giorni

**Riepilogo dell'offerta**: panoramica di Microsoft Azure e Power bi, valutazione dell'ambiente corrente e mini poc.

**Descrizione dell'offerta**: questo workshop di 3 giorni è per i leader tecnici e aziendali e viene mantenuto in sede presso la struttura del client.

Agenda

Giorno 1

-   Dedicato a come proteggere, ridimensionare e organizzare i dati nel cloud Microsoft usando Azure Data Lake, Azure HDInsight o Azure SQL Data Warehouse.

Giorno 2

-   Descrive come configurare e distribuire soluzioni di analitica avanzata con Microsoft R e Azure Machine Learning.

Giorno 3

-   Illustra come disegnare informazioni dettagliate pratiche e rendere operative le analisi con Power BI e comprende una sessione collaborativa per compilare un dashboard di Power BI.

Risultati finali

Al termine del workshop, il cliente sarà in grado di definire un piano di alto livello e una roadmap di implementazione per soluzioni relative a dati e analisi nel cloud Microsoft.

Il seguente file Markdown di esempio è relativo all'offerta precedente:

    This 3-day workshop is for technical and business leaders and is held on-site at the client's facility.

      ### Agenda

      **Day 1**

      * Focuses on how to secure, scale, and organize data within the Microsoft cloud by using Azure Data Lake, Azure HDInsight, or Azure SQL Data Warehouse.

      **Day 2**

      * Covers how to configure and deploy advanced analytics solutions with Microsoft R and Azure Machine Learning.

      **Day 3**

      * Covers how to draw actionable insights and operationalize analytics with Power BI and includes a collaborative session to cobuild a Power BI dashboard.


      ### Deliverables
      By the end of the workshop, the client will be able to define a high-level plan and an implementation roadmap for data and analytics solutions in the Microsoft cloud.


## <a name="publisher-information"></a>Informazioni sull'editore

**ID MPN**

Immettere l'ID Microsoft Partner Network (MPN) a nove cifre. Se non si dispone di un ID MPN, è possibile acquisirne uno nel Centro per i partner Microsoft.

**ID Centro per i partner**

Immettere il nuovo ID del Centro per i partner, se disponibile.

**ID MPN**

Immettere una chiave privata per visualizzare un'anteprima dell'offerta in AppSource prima della pubblicazione.
Questo identificatore non è una password.


## <a name="listing-details"></a>Dettagli inserzione

**Tipo di servizio di consulenza**

Microsoft si concentra esclusivamente su offerte con ambito fisso, durata fissa, prezzo stimato o fisso (o gratuito) e principalmente su offerte di servizi di consulenza pre-vendita per un cliente singolo. I tipi di servizi sono offerte di valutazione, briefing, implementazione, modello di verifica e workshop che si svolgono in loco o virtualmente. Il marketplace dei servizi di consulenza di AppSource non supporta inserzioni per servizi di sottoscrizione o gestiti.

>[!Note]
>I servizi di consulenza di AppSource non costituiscono il marketplace appropriato per sottoscrizioni o training su richiesta.

Sono inclusi i seguenti cinque tipi di offerte:

-   **Valutazione**: valutazione dell'ambiente di un cliente per determinare l'applicabilità di una soluzione e fornire una stima dei costi e delle tempistiche.
-   **Briefing**: introduzione a una soluzione o a un servizio di consulenza per suscitare l'interesse del cliente con framework, demo ed esempi di clienti. I briefing devono essere condotti in loco.
-   **Implementazione**: un'installazione completa che produce una soluzione completamente funzionante. Per questo progetto pilota, Microsoft raccomanda di limitarsi a soluzioni che possono essere implementate in una settimana o meno.
-   Modello **di prova**: implementazione con ambito limitato per determinare se una soluzione soddisfa i requisiti di un cliente.
-   **Workshop**: impegno interattivo condotto su un cliente locale che può includere sessioni di formazione, briefing, valutazioni o demo basate sui dati o sull'ambiente del cliente.

**Disponibilità per Paese/area geografica**

Selezionare il paese e l'area geografica in cui è disponibile questo servizio di consulenza. Impossibile pubblicare una singola offerta in più paesi o aree geografiche. Per ogni paese o area, è necessario creare una nuova offerta.

>[!Note]
>I servizi di consulenza di AppSource sono attualmente attivi negli Stati Uniti, nel Regno Unito e in Canada. È possibile inviare un'offerta per un paese o un'area geografica non ancora attiva e che verrà esaminata e preparata per l'uso. Per aprire un nuovo paese/area geografica è necessario un numero minimo di offerte pronte per l'uso, quindi le offerte per paesi/aree geografiche non attive sono consigliate.

**Settori**

Selezionare i settori a cui si adatta maggiormente la propria offerta di servizi di consulenza.

**Duration**

Selezionare un numero (ad esempio, 3 o 4) relativo alla **durata**, quindi selezionare **l'ora**, **il giorno** o **la settimana**.

**Prodotti primari**

Per pubblicare in Azure Marketplace, selezionare **Azure** come prodotto primario, quindi selezionare le **aree della soluzione** pertinenti.

Per pubblicare in AppSource, selezionare **Dynamics 365**, **Power BI** o **PowerApps** come prodotto primario. È possibile selezionare anche altri **prodotti applicabili** pertinenti. L'offerta di servizi di consulenza verrà visualizzata in elenchi associati a ciascuno di questi prodotti in AppSource.

**Competenze pertinenti**

Selezionare le competenze pertinenti per questa offerta in modo che vengano visualizzate con i dettagli dell'offerta.

## <a name="marketing-artifacts"></a>Artefatti di marketing

**Logo società (formato .png, 48 x 48 pixel)**

Caricare un'immagine che verrà visualizzata nel riquadro della propria offerta nella pagina di visualizzazione della raccolta delle offerte. L'immagine deve essere un'immagine .png con una risoluzione di 48 x 48 pixel.

**Logo società (formato PNG, 216 x 216 pixel)**

Caricare un'immagine che verrà visualizzata nella pagina dei dettagli dell'offerta. L'immagine deve essere un'immagine .png con una risoluzione di 216 x 216 pixel.

**Video (al massimo quattro)**

Caricare al massimo quattro video di case study dei clienti o video di riferimento dei clienti. Se non si dispone di alcun video, caricare un video che illustri le competenze aziendali correlate all'offerta. Se si dispone di una presentazione di soluzioni Power BI o PowerApps, caricare il video della presentazione qui. I collegamenti al video devono essere per YouTube o Vimeo.

**Documenti (al massimo tre)**

Caricare la brochure di marketing che descrive l'offerta del servizio di consulenza in modo dettagliato. È anche possibile caricare una presentazione dell'azienda, schede informative o case study. Assicurarsi che nei documenti vengano usati i nomi correnti dei prodotti presentati e che non siano riportati prodotti concorrenti di Microsoft.

**Screenshot (al massimo cinque)**

Caricare fino a cinque immagini che offrono ulteriori informazioni sull'offerta, sui risultati dell'offerta o sulla propria azienda. Ad esempio, un frammento della brochure di marketing, una diapositiva rilevante di una presentazione o un'immagine che illustra l'espansione o le competenze dell'azienda.


## <a name="next-steps"></a>Passaggi successivi

Ora si è pronti per [pubblicare l'offerta del servizio di consulenza](./cpp-consulting-service-publish-offer.md).
