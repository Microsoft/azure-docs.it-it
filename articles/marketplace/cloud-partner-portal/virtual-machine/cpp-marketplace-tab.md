---
title: Scheda Marketplace della macchina virtuale nel portale Cloud Partner per Azure Marketplace
description: Descrive la scheda Marketplace usata nella creazione di un'offerta di macchina virtuale in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 2c3d316d0d2810cb2a25ffd3bc4e34cf3454c10d
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146866"
---
# <a name="virtual-machine-marketplace-tab"></a>Scheda Marketplace per una macchina virtuale

> [!IMPORTANT]
> A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte della macchina virtuale di Azure al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [creare un'offerta di macchina virtuale di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) .

La scheda **Marketplace** della pagina **Nuova offerta** consente di offrire ai clienti potenziali informazioni e contratti legali, di vendita e marketing e di gestire i clienti potenziali generati dal marketplace. Questa modulo di grandi dimensioni è suddiviso in quattro sezioni: **Overview** (Panoramica), **Marketing Artifacts** (Artefatti di marketing), **Lead Management** (Gestione clienti potenziali) e **Legal** (Legale).


## <a name="overview-section"></a>Sezione Panoramica
In questa sezione immettere le informazioni generali sull'offerta di Azure Marketplace.  Un asterisco (*) accodato al nome del campo indica che si tratta di un campo obbligatorio.

![Sezione Panoramica della scheda Marketplace per le macchine virtuali](./media/publishvm_008.png)

La tabella seguente descrive lo scopo e il contenuto di questi campi. I campi obbligatori sono indicati da un asterisco (*).

|  **Campo**                |     **Descrizione**                                                          |
|  ---------                |     ---------------                                                          |
| **Titolo\***                 | Titolo dell'offerta, spesso corrispondente alla forma estesa e formale del nome. Questo titolo verrà visualizzato in una posizione di estremo rilievo nel marketplace.  La lunghezza massima consentita è di 50 caratteri. |
| **Riepilogo\***               | Descrizione breve dello scopo o della funzione della soluzione.  La lunghezza massima consentita è di 100 caratteri. |
| **Riepilogo lungo\***          | Scopo o funzione della soluzione.  La lunghezza massima consentita è di 256 caratteri. |
| **Descrizione\***           | Descrizione della soluzione.  La lunghezza massima consentita è di 3000 caratteri. Supporta la formattazione HTML semplice. |
| **Canale rivenditore Microsoft CSP\*** | Il consenso esplicito del canale partner Cloud Solution Provider (CSP) è ora disponibile.  Per ulteriori informazioni sul marketing dell'offerta tramite i canali del partner Microsoft CSP, vedere [provider di soluzioni cloud](../../cloud-solution-providers.md) . |
| **Identificatore marketing\***  | URL univoco da associare a questa offerta. Include in genere il nome dell'organizzazione e della soluzione. La lunghezza massima consentita è di 50 caratteri.  Ad esempio: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Anteprima ID sottoscrizione\*** | Aggiungere da uno a 100 identificatori di sottoscrizione di visualizzatori anteprima. Queste sottoscrizioni consentite avranno accesso all'offerta dopo la pubblicazione, prima che venga attivata. |
| **Collegamenti utili**          | Aggiungere gli URL per la documentazione, le note sulla versione, le domande frequenti e così via. |
| **Categorie suggerite\*** | Selezionare un massimo di due (2) categorie, tra cui una categoria primaria e una secondaria (facoltativo). Selezionare un massimo di due (2) sottocategorie per ogni categoria primaria e/o secondaria. Se non è selezionata alcuna sottocategoria, l'offerta sarà comunque individuabile solo per la categoria selezionata. |
|  |  |


## <a name="marketing-artifacts-section"></a>Sezione Marketing Artifacts (Artefatti di marketing)

Questa seconda sezione è suddivisa in tre sottosezioni: **Logos** (Logo), **Screenshots** (Screenshot) e **Videos** (Video). I logo sono gli unici artefatti di marketing obbligatori, ma tutti sono vivamente consigliati per catturare meglio l'attenzione dei clienti. 

![Sezione Marketing Artifacts (Artefatti di marketing) della scheda Marketplace nel modulo Nuova offerta per le macchine virtuali](./media/publishvm_009.png)

La tabella seguente descrive lo scopo e il contenuto di questi campi. I campi obbligatori sono indicati da un asterisco (*).

|  **Campo**                |     **Descrizione**                                                          |
|  ---------                |     ---------------                                                          |
| *Loghi*  |  |
| **Piccolo\***                 | Bitmap con estensione ico, 40x40 pixel                                                      |
| **Medio\***                | Bitmap con estensione ico, 90x90 pixel                                                      |
| **large\***                 | Bitmap con estensione ico, 115x115 pixel                                                   |
| **Wide\***                  | Bitmap con estensione ico, 255x115 pixel                                                    |
| **Hero** (Banner)                  | Bitmap, 815x290.  Facoltativo. Una volta caricata, tuttavia, l'icona del banner non può essere eliminata. |
| *Screenshot*  | Facoltativi, ma non più di cinque per SKU. |
| **Nome**                  | Nome o titolo  <!-- TODO - max char length? none specified in UI -->                               |
| **Immagine**                 | Acquisizione di schermata, 533x324 pixel                                         |
| *Video*  |  |
| **Nome**                  | Nome o titolo   <!-- TODO - max char length? -->                              |
| **Collegamento**                  | URL del video, ospitato in YouTube o Vimeo                                        |
| **Anteprima**             | Bitmap, 533x324                                                               |
|   |   |

### <a name="logo-guidelines"></a>Linee guida per il logo

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Ogni logo caricato nel portale Cloud Partner deve rispettare le linee guida seguenti:

*  La progettazione di Azure ha una tavolozza dei colori semplice. Nel logo usare un numero ridotto di colori primari e secondari.
*  I colori del tema del portale di Azure sono il bianco e il nero. Evitare pertanto di usare questi colori per lo sfondo dei logo. Usare un colore che faccia risaltare i logo nel portale di Azure. Si consiglia di usare colori primari semplici. Se si usa lo sfondo trasparente, verificare che i logo e il testo non siano bianchi, neri o blu.
*  Non usare uno sfondo sfumato per il logo.
*  Non inserire testo nel logo, neanche il nome del marchio o della società. L'aspetto del logo deve essere semplice e senza sfumature.
*  Non estendere il logo.

#### <a name="hero-logo"></a>Logo alto

Il logo Hero (Banner) è facoltativo. Una volta caricata, tuttavia, l'icona del banner non può essere eliminata.  L'icona del logo Hero (Banner) deve rispettare le linee guida seguenti:

*  Lo sfondo nero, bianco o trasparente non è consentito per le icone banner.
*  Evitare di usare un colore chiaro per lo sfondo del logo del banner.  Il nome visualizzato dell'editore, il titolo del piano e il riepilogo lungo dell'offerta sono visualizzati con caratteri bianchi e devono risaltare sullo sfondo.
*  Evitare di usare testo mentre si progetta il logo del banner.  Il nome dell'editore, il titolo del piano, il riepilogo lungo dell'offerta e un pulsante di creazione vengono incorporati a livello di codice all'interno dell'icona del banner quando l'offerta viene presentata. 
* Includere un rettangolo vuoto a destra dell'icona del banner, con dimensioni 415x100 pixel e uno scostamento di 370 pixel da sinistra.  

L'icona del banner di esempio riportata di seguito è per il servizio Azure Container.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Icona del banner di esempio per il servizio Azure Container](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Esempio di informazioni di marketing 

L'immagine seguente illustra il modo in cui vengono visualizzate le informazioni di marketing nella pagina del prodotto principale di Microsoft Windows Server.

![Pagina di prodotto di esempio per Microsoft Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Sezione Lead Management (Gestione clienti potenziali)

La terza sezione consente di raccogliere clienti potenziali generati dalle offerte in Azure Marketplace. Per queste informazioni, questa sezione offre le opzioni di archiviazione seguenti da un elenco a discesa.

* **Nessuno**: impostazione predefinita, non vengono raccolte informazioni sui clienti potenziali.
* Tabella di Azure: le informazioni vengono scritte nella tabella di Azure specificata da una stringa di connessione.
* Dynamics CRM Online: le informazioni vengono scritte nell'istanza di [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) specificata da un URL e da credenziali di autenticazione.
* HTTPS Endpoint (Endpoint HTTPS): le informazioni vengono scritte nell'endpoint HTTPS specificato come payload JSON.
* Marketo: le informazioni vengono scritte nell'istanza [Marketo](https://www.marketo.com/) specificata da ID server, ID Munchkin e ID modulo.
* Salesforce: le informazioni vengono scritte in un database [Salesforce](https://www.salesforce.com/) specificato da un identificatore di oggetto.

Dopo aver pubblicato l'offerta, verrà convalidata la connessione al cliente potenziale e verrà automaticamente inviato un cliente potenziale di test alla destinazione configurata. Le informazioni sul cliente potenziale devono essere gestite in modo continuo e queste impostazioni devono essere aggiornate immediatamente quando si apportano modifiche all'architettura di gestione dei clienti.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Sezione legale

Questa ultima sezione consente di fornire i documenti legali richiesti per ogni offerta.  

|  **Campo**                    |     **Descrizione**                                        |
|  ---------                    |     ---------------                                        |
| **URL dell'Informativa sulla privacy\***      | URL dell'informativa sulla privacy pubblicata                          |
| **Usare il contratto standard?\***  |   |
| **Condizioni per l'utilizzo\***            | Norme in testo normale o HTML semplice.                       |
|  |  |


## <a name="next-steps"></a>Passaggi successivi

Nella scheda successiva, [Support](./cpp-support-tab.md) (Supporto) è possibile specificare le risorse tecniche e di supporto utente della propria offerta.
