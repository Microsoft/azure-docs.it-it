---
title: Unità di test dell'app per la logica | Azure Marketplace
description: Viene illustrato come creare un test drive che si connette a un'istanza di Dynamics AX/CRM o a qualsiasi altra risorsa non di Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 766f893d71ca0830fe8b69c50145603c6544cc3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278383"
---
<a name="logic-app-test-drive"></a>Test drive di app per la logica
====================

Questo articolo è per gli autori che hanno un'offerta in AppSource e vogliono creare il test drive che si connette a un'istanza di Dynamics AX/CRM o a qualsiasi altra risorsa non di Azure.

<a name="how-to-build-a-logic-app-test-drive"></a>Come compilare un test drive di app per la logica
-----------------------------------

La documentazione di test drive per le unità di test per l'app per la logica è attualmente ancora in GitHub per [le operazioni](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e il [coinvolgimento dei clienti](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app). per altre informazioni, vedere.

<a name="how-to-publish-a-test-drive"></a>Come pubblicare un test drive
---------------------------

Dopo aver compilato il test drive, la sezione conterrà i campi da compilare per pubblicare correttamente il test drive.

![Abilitare la funzionalità di test drive](./media/azure-resource-manager-test-drive/howtopub1.png)

Il primo e più importante campo consiste nel selezionare se si desidera che il test del form con tutti i campi richiesti venga compilato. Quando si seleziona **No,** il modulo viene disabilitato e se si ripubblica con la test drive disabilitata, la test drive viene rimosso dalla produzione.

*Nota*: se non vi sono test drive usati attivamente dagli utenti, quei test drive devono continuare l'esecuzione fino alla fine della sessione.

### <a name="details"></a>Dettagli

La sezione successiva da compilare contiene i dettagli relativi all'offerta del test drive.

![Dettagli del test drive](./media/azure-resource-manager-test-drive/howtopub2.png)

**Descrizione** *[Campo obbligatorio]* Il campo in cui scrivere la descrizione principale di ciò che è presente nel test drive. Il cliente potrà scoprire gli scenari relativi al prodotto descritti dal test drive. 

**Manuale dell'utente** *[Campo obbligatorio]* Tratta in modo approfondito la procedura guidata dell'esperienza di test drive. Una volta aperto, il cliente verrà guidato a compiere i passaggi richiesti attraverso il test drive. È importante che il contenuto sia facile da comprendere e seguire. (Deve essere un file con estensione PDF)

**Video demo del test drive** \[Consigliato\] Come per il Manuale per l'utente, la cosa migliore è inserire un tutorial video dell'esperienza di test drive. Il cliente lo vedrà prima o durante il test drive e potrà compiere i passaggi a lui richiesti attraverso il test drive. È importante che il contenuto sia facile da comprendere e seguire.

- **Nome** Titolo del video.
- **Collegamento** Deve essere un URL di YouTube o Vimeo. Di seguito, un esempio di come recuperare l'URL incorporato:
- **Anteprima** Deve essere un'immagine di alta qualità (533x324 pixel). È consigliabile acquisire una schermata di alcune parti dell'esperienza di test drive.

In basso, viene illustrato come il cliente visualizza i campi durante l'esperienza di test drive.

![Aspetto dei campi del test drive](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Configurazione tecnica

Nella sezione successiva da compilare, si configura l'app per la logica del test drive e si definisce il grado di specificità delle istanze del test drive.

![Configurazione tecnica del test drive](./media/azure-resource-manager-test-drive/howtopub5_logicapp.png)

- **Region** - *[campo obbligatorio]* l'area selezionata è la posizione in cui si sceglie la posizione in cui vengono distribuite le risorse dell'app per la logica test drive.

    *Note:* se vi sono risorse personalizzate archiviate in un'area, assicurarsi qui che l'area sia selezionata. Il modo migliore per farlo è **distribuire completamente l'app per la logica in locale nella sottoscrizione di Azure del portale e verificare che funzioni** prima di scrivere qui.

- **Numero massimo di unità** - di test simultanee *[campo obbligatorio]* numero di istanze di test drive già distribuite e in attesa di accesso per area selezionata. I clienti possono accedere immediatamente a questo test drive, anziché dover attendere la distribuzione.

    *Nota:* se si svolge un webinar/lezione dove si vuole che tutti gli studenti eseguano un test drive, pubblicare con un numero N di istanze a livello di accesso frequente. Quindi, una volta finita la lezione, ripubblicare con il numero N normale di istanze a livello di accesso frequente.

- **Durata del test (ore)** *[Campo obbligatorio]* Tempo in cui il test drive rimane attivo, in \# di ore. Il test drive termina automaticamente alla fine di questo periodo di tempo.

- **Nome del gruppo di risorse di Azure** *[Campo obbligatorio]* Aggiungere il nome del gruppo di risorse in cui sono salvati i test drive delle app per logica.

- **Assegnare il nome dell'app per la logica** *[Campo obbligatorio]* Aggiungere l'app per la logica usata per assegnare un utente nel test drive prima che il cliente lo riceva, aggiungere il nome dell'app per la logica qui. Assicurarsi che questo file venga salvato nel gruppo di risorse precedente.

- **Effettuare il deprovisioning del nome dell'app per la logica** *[Campo obbligatorio]* Aggiungere l nome dell'app per la logica per effettuare il deprovisioning di tutte le risorse create nel test drive. Assicurarsi che questo file venga salvato nel gruppo di risorse precedente.

- **Accedere alle informazioni** *[Campo obbligatorio]* Dopo che un cliente ha ottenuto il test drive, vengono visualizzate le informazioni di accesso. Queste istruzioni sono pensate per condividere i parametri di output utili dal modello di Resource Manager del test drive. Per includere i parametri di output, usare le parentesi graffe doppie (ad esempio, **{{nomeoutput}}**) e questi verranno inseriti correttamente nella posizione. (La formattazione delle stringhe HTML è consigliata qui per eseguire il rendering nel front-end).

### <a name="test-drive-deployment-subscription-details"></a>Dettagli di sottoscrizione della distribuzione del test drive

L'ultima sezione da compilare consente di distribuire automaticamente i test drive connettendo la sottoscrizione di Azure e Azure Active Directory (AD).

![Dettagli di sottoscrizione della distribuzione del test drive](./media/azure-resource-manager-test-drive/subdetails1.png)

**ID di sottoscrizione di Azure** *[Campo obbligatorio]* Si concede l'accesso a servizi di Azure e al portale di Azure. Nella sottoscrizione viene tenuto traccia dell'uso delle risorse e i servizi vengono fatturati. Se non si dispone ancora di una sottoscrizione **separata** di Azure solo per test drive, proseguire e crearne una. Per trovare gli ID di sottoscrizione di Azure, registrarsi sul portale di Azure e andare nella sezione delle sottoscrizioni nella parte in basso a sinistra del menu.
(Esempio: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Sottoscrizioni di Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**ID del tenant di Azure AD** *[Campo obbligatorio]* È possibile trovare l'ID del tenant già disponibile di seguito in Proprietà \> ID directory.

![Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

In caso contrario, creare un nuovo tenant in Azure Active Directory.

![Schermata delle proprietà di Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

! Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails5.png)

![Tenant di Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails6.png)

**ID dell'app Azure AD** *[Campo obbligatorio]* Il passaggio successivo consente di creare e registrare una nuova applicazione. Si userà questa applicazione per eseguire operazioni nell'istanza di test drive.

1. Nella directory appena creata o in quelle già esistenti, selezionare la directory Azure Active nel filtro.
2. Cercare "Registrazioni app" e fare clic su "Aggiungi"
3. Immettere un nome applicazione.
4. Selezionare il tipo come "app/API Web"
5. Fornire qualsiasi valore nell'URL di accesso, quel campo non verrà utilizzato.
6. Fare clic su Crea.
7. Dopo aver creato l'applicazione, andare su Proprietà \> Impostare l'applicazione come multi tenant e fare clic su Salva.

Fare clic su Salva. L'ultimo passaggio consiste nell'individuare l'ID applicazione per l'app registrata e nell'incollarlo nel campo del test drive qui di seguito.

![ID dell'applicazione Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails7.png)

Poiché l'applicazione da distribuire nella sottoscrizione è in uso, è necessario aggiungere l'applicazione come collaboratore nella sottoscrizione. Le istruzioni per questi sono le seguenti:

1. Sul pannello di sottoscrizione, selezionare la sottoscrizione in uso usando soltanto per il test drive.
1. Fare clic su **controllo di accesso (IAM)**.
1. Fare clic sulla scheda **assegnazioni di ruolo** .  ![Azure Active Directory, aggiunta di una nuova entità di controllo di accesso](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Fare clic su **Aggiungi assegnazione ruolo**.
1. Impostare il ruolo come **collaboratore**.
1. Digitare il nome dell'applicazione Azure AD e selezionare l'applicazione per assegnare il ruolo.
    ![Autorizzazioni di Azure Active Directory](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Fare clic su **Save**.

**Chiave dell'app Azure AD ** *[Campo obbligatorio]* L'ultimo campo genera una chiave di autenticazione. In Chiavi, aggiungere una descrizione di chiave, impostare la scadenza come Non scade, quindi selezionare Salva. È **importante** evitare di avere una chiave scaduta, che interrompe il test drive in corso. Copiare questo valore e incollarlo nel campo corrispondente del test drive.

![Sezione delle chiavi di Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails8.png)

> [!CAUTION]
> Non è possibile usare l'anteprima della registrazione app Azure perché attualmente non genera una chiave con codifica Base64.


<a name="next-steps"></a>Passaggi successivi
----------

Dopo aver compilato tutti i campi del test drive, proseguire e **Ripubblicare** l'offerta. Dopo che il test drive ha superato il processo di certificazione, è possibile effettuare un test completo dell'esperienza del cliente nell'**anteprima** dell'offerta. Avviare un test drive nell'interfaccia utente e verificare che i test drive siano distribuiti correttamente.

È importante notare che non si possono eliminare parti del test drive poiché ne viene effettuato il provisioning per i clienti, quindi il servizio di test drive pulirà automaticamente i gruppi di risorse dopo che il cliente avrà terminato l'operazione.

Una volta acquisita familiarità con l'offerta di anteprima, è il momento di **passare alla fase operativa**. Una volta che l'offerta è stata pubblicata, Microsoft provvederà a una revisione finale per controllare l'intera esperienza end-to-end. Se per qualche motivo l'offerta viene rifiutata, verrà inviata una notifica al contatto tecnico relativa all'offerta che spiegherà le problematiche da risolvere.

In caso di domande, richieste di suggerimenti per la risoluzione dei problemi o per rendere più efficiente il test drive, visitare [Domande frequenti, risoluzione dei problemi e procedure consigliate](./marketing-and-best-practices.md).
