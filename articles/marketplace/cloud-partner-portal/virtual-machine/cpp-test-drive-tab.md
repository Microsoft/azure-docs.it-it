---
title: Scheda test drive della macchina virtuale nel portale Cloud Partner per Azure Marketplace
description: Descrive la scheda Test Drive usata nella creazione di un'offerta di macchina virtuale in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: adac73d64feb6280c5043776249072e9f7595faa
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142978"
---
# <a name="virtual-machine-test-drive-tab"></a>Scheda Test Drive di macchina virtuale

> [!IMPORTANT]
> A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte della macchina virtuale di Azure al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [creare un'offerta di macchina virtuale di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) .

La scheda **Test Drive** della pagina **Nuova offerta** consente di offrire ai potenziali clienti una dimostrazione pratica e autoguidata delle funzionalità chiave e dei vantaggi offerti dal proprio prodotto, presentandola in uno scenario standardizzato.  Test Drive è una funzionalità facoltativa per i tipi di offerta che supportano il test drive.  Richiede asset di supporto da implementare come si conviene.  Per altre informazioni, vedere l'articolo [Azure Marketplace Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/) (Test Drive di Azure Marketplace).  <!--TD: Replace with migrated version of Test Drive article! -->

Per abilitare questa funzionalità, nella scheda **Test drive** fare clic sull'opzione **Sì** in **Enable a Test Drive** (Abilita un Test Drive).  La scheda **Test Drive** visualizza i campi disponibili per la modifica.  Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio.

![Scheda Test Drive nel modulo Nuova offerta per le macchine virtuali](./media/publishvm_007.png)


## <a name="field-values"></a>Valori dei campi

La tabella seguente descrive lo scopo e il contenuto di questi campi.  I campi obbligatori sono indicati da un asterisco (*).


|    Campo                  |       Descrizione                                                            |
|  ---------                |     ---------------                                                          |
|  *Dettagli*   |  |
| **Descrizione\***           | Offrire una panoramica dello scenario di test drive. Questo testo verrà mostrato all'utente durante il provisioning del test drive. Supporta il linguaggio HTML di base qualora si volesse offrire contenuto formattato.  |
| **Manuale dell'utente\***           | Caricare un manuale dell'utente dettagliato (file con estensione pdf) che consente agli utenti del test drive di capire come usare la soluzione presentata.  |
| **Video demo del test drive** | Caricare un video che illustra la soluzione.  Se si sceglie questa opzione, è necessario specificare un nome, l'URL del video (su YouTube o Vimeo) e un'immagine di anteprima (533 x 324 pixel) del video. |
| *Configurazione tecnica* |  |
| **Istanze\***             | Specificare la disponibilità di aree e la disponibilità relativa dell'istanza di macchina virtuale (fare clic sull'icona delle informazioni per altri dettagli).  <br/>È consigliabile che le sessioni di test drive potenzialmente simultanee non superino il limite di quota per la sottoscrizione.  La prima viene calcolata nel modo seguente: [Numero di aree selezionate] x [Istanze ad accesso frequente] + [Numero di aree selezionate] x [Istanze ad accesso medio] + [Numero di aree selezionate] x [Istanze ad accesso sporadico] |
| **Durata test drive\***   | Durata massima della sessione in ore La sessione del test drive termina automaticamente dopo il superamento di questo periodo di tempo.  |
|**Modello ARM di test drive\***| Caricare il modello di Azure Resource Manager associato a questo test drive. Per altre informazioni, vedere [Transforming Virtual Machine Deployment Template for Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive) (Trasformazione del modello di distribuzione di macchine virtuali per il test drive). |
| **Informazioni di accesso\***    | Informazioni di accesso ad Azure Resource Manager e alla versione di valutazione, scritte in testo normale o in HTML semplice. |
| *Dettagli sottoscrizione della distribuzione test drive* |  |
| **ID sottoscrizione di Azure\*** | È reperibile accedendo al [portale di Microsoft Azure](https://ms.portal.azure.com) e facendo clic su **Sottoscrizioni** nella barra dei menu a sinistra. (Esempio: "a83645ac-1234-5AB6-6789-1h234g764ghty")    Questo identificatore deve essere un GUID del form `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **ID tenant Azure AD\***    | ID del tenant di Azure Active Directory.  È reperibile accedendo al [portale di Microsoft Azure](https://ms.portal.azure.com) e facendo clic su **Azure Active Directory** nella barra dei menu a sinistra, quindi facendo clic su **Proprietà** nella barra dei menu centrale e quindi copiando l'**ID directory** dal modulo.  Anche questo identificatore deve essere un GUID.  Se questo campo è vuoto, è necessario creare un ID tenant per l'organizzazione. |
| **ID App Azure AD\***       | Identificatore per la soluzione di macchina virtuale di Azure registrata  |
| **Chiave di App Azure AD\***      | Chiave di autenticazione per la soluzione registrata |
|   |   |


## <a name="next-steps"></a>Passaggi successivi

Nella scheda [Marketplace](./cpp-marketplace-tab.md) che segue si specificheranno le informazioni legali e di marketing della soluzione.
