---
title: Acquistare un nome di dominio personalizzato
description: Informazioni su come acquistare un dominio del servizio app e usarlo come dominio personalizzato per il servizio app app Azure.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: 0111da00962f267387e66978952e8a7c9f5d7308
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90970058"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Acquistare un nome di dominio personalizzato per Servizio app di Azure

I domini del servizio app sono domini di primo livello gestiti direttamente in Azure. Semplificano le operazioni di gestione dei domini personalizzati per il [Servizio app di Azure](overview.md). In questa esercitazione viene illustrato come acquistare un dominio del servizio app e come assegnare i nomi DNS al Servizio app di Azure.

Per una macchina virtuale di Azure o per Archiviazione di Azure, vedere [Assign App Service domain to Azure VM or Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage) (Assegnare il dominio del servizio app alla macchina virtuale di Azure o ad Archiviazione di Azure). Per i servizi cloud, vedere [Configurazione di un nome di dominio personalizzato per un servizio cloud di Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

* [Creare un'app del servizio app](./index.yml) oppure usare un'app creata per un'altra esercitazione.
* [Rimuovere il limite di spesa per la sottoscrizione](../cost-management-billing/manage/spending-limit.md#remove). Non è possibile acquistare domini del servizio app con crediti della sottoscrizione gratuiti.

## <a name="prepare-the-app"></a>Preparare l'app

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Per usare domini personalizzati in Servizio app di Azure, è necessario che il [piano di servizio app](https://azure.microsoft.com/pricing/details/app-service/) sia un livello a pagamento (**Condiviso**, **Basic**, **Standard** o **Premium**). In questo passaggio, verificare che l'app sia supportata dal piano tariffario adeguato.

### <a name="sign-in-to-azure"></a>Accedere ad Azure

Aprire il [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Passare all'app nel portale di Azure

Dal menu a sinistra selezionare **Servizi app** e quindi selezionare il nome dell'app.

![Passaggio all'app di Azure nel portale](./media/app-service-web-tutorial-custom-domain/select-app.png)

Viene visualizzata la pagina di gestione dell'app del servizio app.  

### <a name="check-the-pricing-tier"></a>Scegliere il piano tariffario

Nel riquadro di spostamento a sinistra della pagina dell'app scorrere fino alla sezione **Impostazioni** e selezionare **Aumenta (piano di servizio app)** .

![Menu di scalabilità verticale](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Il livello corrente dell'app è evidenziato da un bordo blu. Verificare che l'app non sia inclusa nel livello **F1**. Il DNS personalizzato non è supportato nel livello **F1**. 

:::image type="content" source="./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png" alt-text="Screenshot del menu di spostamento a sinistra della pagina dell'app con scalabilità verticale (piano di servizio app) selezionato.":::

Se il piano di servizio app non è **F1**, chiudere la pagina **Aumenta** e passare a [Acquista il dominio](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Passare a un piano di servizio app di livello superiore

Selezionare uno dei livelli non gratuiti (**D1**, **B1**, **B2**, **B3** o uno qualsiasi dei livelli della categoria **Produzione**). Per altre opzioni, fare clic su **Visualizza opzioni aggiuntive**.

Fare clic su **Applica**.

:::image type="content" source="./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png" alt-text="Screenshot dei piani tariffari del dominio personalizzato nella categoria produzione con la scheda produzione, il piano B1 e il pulsante Applica evidenziato.":::

La visualizzazione della notifica seguente indica che l'operazione di passaggio al livello superiore è stata completata.

![Conferma operazione di scalabilità](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Acquistare il dominio

### <a name="pricing-information"></a>Informazioni sui prezzi
Per informazioni sui prezzi dei domini app Azure Service, visitare la [pagina dei prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/windows/) e scorrere verso il basso fino al dominio del servizio app.

### <a name="sign-in-to-azure"></a>Accedere ad Azure
Aprire il [portale di Azure](https://portal.azure.com/) e accedere con l'account Azure.

### <a name="launch-buy-domains"></a>Avvio di Acquistare un dominio
Nella scheda **Servizi app** fare clic sul nome dell'app, selezionare **Impostazioni** e quindi **Domini personalizzati**
   
![Screenshot che mostra i domini personalizzati evidenziati.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Nella pagina **Domini personalizzati** fare clic su **Acquista il dominio**.

![Screenshot che mostra l'acquisto del dominio evidenziato.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Se non è possibile visualizzare la sezione **Domini del servizio app**, è necessario rimuovere il limite di spesa per l'account di Azure (vedere [Prerequisiti](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Configurare l’acquisto del dominio

Nella pagina **Dominio del servizio app**, digitare il nome di dominio che si desidera acquistare nel riquadro **Ricerca dominio** , quindi digitare `Enter`. I domini disponibili suggeriti vengono visualizzati sotto la casella di testo. Selezionare uno o più domini che si desidera acquistare.

![Screenshot che mostra la casella Cerca dominio di ricerca.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> I [domini di primo livello](https://wikipedia.org/wiki/Top-level_domain) seguenti sono supportati dai domini del servizio app: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ e _co.in_.
>
>

Scegliere **Informazioni di contatto** e compilare il modulo di informazioni di contatto del dominio. Al termine, fare clic su **OK** per tornare alla pagina del dominio di servizio app.

È molto importante compilare tutti i campi obbligatori con la massima correttezza possibile. I dati non corretti relativi alle informazioni di contatto causano errori nell'acquisto dei domini.

Selezionare quindi le opzioni desiderate per il dominio. Vedere la tabella seguente per alcune spiegazioni:

| Impostazione | Valore consigliato | Descrizione |
|-|-|-|
|Protezione della privacy | Abilita | Dare il consenso esplicito per l'opzione "Protezione della privacy", inclusa _gratuitamente_ nel prezzo di acquisto. Alcuni domini di primo livello, elencati nella pagina **Protezione della privacy**, sono gestiti da registrar che non supportano la protezione della privacy. |
| Assegnare i nomi host predefiniti | **www** e **\@** | Selezionare le associazioni del nome host desiderate, se necessario. Una volta completata l'operazione di acquisto del dominio, l'app è accessibile tramite i nomi host selezionati. Se l'app è nascosta da [Gestione traffico di Microsoft Azure](https://azure.microsoft.com/services/traffic-manager/), non viene visualizzata l'opzione per assegnare il dominio radice (@), perché Gestione traffico non supporta i record A. Al termine dell'acquisto di un dominio, è possibile apportare modifiche alle assegnazioni di nome host. |

### <a name="accept-terms-and-purchase"></a>Accettare i termini di acquisto

Fare clic su **Termini legali** per leggere i termini e le tariffe, quindi fare clic su **Acquista**.

> [!NOTE]
> I domini del servizio app usano GoDaddy per la registrazione del dominio e DNS di Azure per ospitare i domini. Oltre alla tariffa di registrazione del dominio, si applicano le spese di utilizzo per il DNS di Azure. Per altre informazioni, vedere la pagina relativa ai [prezzi del DNS di Azure](https://azure.microsoft.com/pricing/details/dns/).
>
>

Nella pagina **Dominio di servizio app** fare clic su **OK**. Mentre l'operazione è in corso, vedrai le notifiche seguenti:

![Screenshot che mostra il messaggio di convalida in corso.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![Screenshot che mostra la notifica che l'acquisto ha avuto esito positivo.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Verifica dei nomi host

Se sono stati assegnati nomi host predefiniti all'app, è anche possibile visualizzare una notifica di esito positivo per ogni nome host selezionato.

![Screenshot che mostra la notifica di esito positivo per ogni nome host selezionato.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

I nomi host selezionati vengono visualizzati nella pagina **Domini personalizzati**, nella sezione **Nomi host personalizzati**.

![Screenshot che mostra i nomi host selezionati nella sezione nomi host personalizzati della pagina domini personalizzati.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Un'etichetta **non sicura** per il dominio personalizzato significa che non è ancora associata a un certificato TLS/SSL e qualsiasi richiesta HTTPS da un browser al dominio personalizzato riceverà un errore o un avviso, a seconda del browser. Per configurare il binding TLS, vedere [proteggere un nome DNS personalizzato con un'associazione TLS/SSL nel servizio app Azure](configure-ssl-bindings.md).
>

Per verificare i nomi host, navigare nei nomi host elencati dal browser. Nell'esempio dello screenshot precedente, provare a passare a _kontoso.NET_ e _www \. kontoso.NET_.

## <a name="assign-hostnames-to-app"></a>Assegnare i nomi host all'app

Se si sceglie di non assegnare uno o più nomi host predefiniti all'app durante il processo di acquisto o se è necessario assegnare un nome host non elencato, è possibile assegnare un nome host in qualsiasi momento.

È inoltre possibile assegnare i nomi host nel dominio del servizio app a qualsiasi altra app. I passaggi variano in base all'appartenenza alla stessa sottoscrizione da parte del dominio del servizio app e dell'app.

- Sottoscrizione diversa: eseguire il mapping dei record DNS personalizzati dal dominio del servizio app all'app come un dominio acquistato esternamente. Per informazioni sull'aggiunta di nomi DNS personalizzati a un dominio del servizio app, vedere [Gestione dei record DNS personalizzati](#custom). Per eseguire il mapping di un dominio esterno acquistato a un'app, vedere [Eseguire il mapping di un nome DNS personalizzato esistente per il Servizio app di Azure](app-service-web-tutorial-custom-domain.md). 
- In caso di medesima sottoscrizione, eseguire la procedura seguente.

### <a name="launch-add-hostname"></a>Avviare Aggiungi nome host
Nella pagina **Servizi app**, selezionare il nome dell'app a cui si desidera assegnare i nomi host, selezionare **Impostazioni**, quindi selezionare **Domini personalizzati**.

![Screenshot che mostra i domini personalizzati evidenziati.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Verificare che il dominio acquistato sia presente nella sezione **domini del servizio app**, ma non selezionarlo. 

![Screenshot che mostra il dominio acquistato nella sezione domini del servizio app.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Tutti i domini del servizio app con la stessa sottoscrizione vengono visualizzati nella pagina dell'app **Domini personalizzati**. Se il dominio è nella sottoscrizione dell'app, ma non è possibile visualizzarlo nella pagina dell'app **Domini personalizzati**, provare a riaprire la pagina **Domini personalizzati** o aggiornare la pagina Web. Inoltre, selezionare l'icona a forma di campanella nella parte superiore del portale di Azure per verificare lo stato di avanzamento.
>
>

Selezionare **Aggiungi il nome host**.

### <a name="configure-hostname"></a>Configurare il nome host
Nella finestra di dialogo **Aggiungere nome host**, digitare il nome di dominio completo del dominio del servizio app o di qualsiasi sottodominio. Ad esempio:

- kontoso.net
- \.kontoso.net www
- abc.kontoso.net

Al termine, selezionare **Convalida**. Il tipo di record del nome host viene selezionato automaticamente.

Selezionare **Aggiungi il nome host**.

Una volta completata l'operazione, viene visualizzata una notifica di esito positivo per il nome di host assegnato.  

![Screenshot che mostra la notifica di esito positivo per il nome host assegnato.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Chiudere Aggiungi il nome host
Nella pagina **Aggiungi il nome host**, assegnare qualsiasi nome host all'app, in base alle proprie esigenze. Al termine, chiudere la pagina **Aggiungi il nome host**.

Viene visualizzato il nome host appena assegnato nella pagina dell’app **Domini personalizzati**.

![Screenshot che mostra i nomi host appena assegnati nella pagina domini personalizzati dell'app.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Verifica dei nomi host

Navigare tra i nomi host elencati nel browser. Nell'esempio della schermata precedente, navigare in _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Rinnovare il dominio

Il dominio del servizio app acquistato è valido per un anno dalla data di acquisto. Per impostazione predefinita, il dominio è configurato per il rinnovo automatico tramite l'addebito dell'importo per l'anno successivo in base al metodo di pagamento selezionato. È possibile rinnovare manualmente il nome di dominio.

Se si desidera disattivare il rinnovo automatico o se si desidera rinnovare manualmente il dominio, eseguire la procedura seguente.

Nella scheda **Servizi app** fare clic sul nome dell'app, selezionare **Impostazioni**e quindi selezionare **domini personalizzati**.

![Screenshot che mostra i domini personalizzati evidenziati.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Nella sezione **Domini del servizio app** selezionare il dominio che si desidera configurare.

![Screenshot che mostra il dominio acquistato nella sezione domini del servizio app.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

Nel riquadro di spostamento a sinistra del dominio selezionare **Rinnovo del dominio**. Per interrompere il rinnovo automatico del dominio, selezionare **No** e quindi **Salva**.

![Screenshot che mostra l'opzione per rinnovare automaticamente il dominio.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Per rinnovare manualmente il dominio, selezionare **Rinnova dominio**. Tuttavia, questo pulsante non è attivo fino a [90 giorni prima della scadenza del dominio](#when-domain-expires).

Se il rinnovo del dominio ha esito positivo, si riceverà una notifica tramite posta elettronica entro 24 ore.

## <a name="when-domain-expires"></a>Quando il dominio scade

Azure gestisce i domini del servizio app in scadenza o scaduti come indicato di seguito:

* Se il rinnovo automatico è disabilitato: 90 giorni prima della scadenza del dominio, viene inviato un messaggio di posta elettronica di notifica del rinnovo e il pulsante **rinnova dominio** viene attivato nel portale.
* Se il rinnovo automatico è abilitato: il giorno dopo la data di scadenza del dominio, Azure tenta di fatturare il rinnovo del nome di dominio.
* Se si verifica un errore durante il rinnovo automatico, ad esempio se la scheda è scaduta o se il rinnovo automatico è disabilitato e si consente la scadenza del dominio, Azure invia una notifica alla scadenza del dominio e parcheggia il nome di dominio. È possibile [rinnovare manualmente](#renew-the-domain) il dominio.
* Il giorno 4 e i 12 giorni dopo la scadenza, Azure invia messaggi di posta elettronica di notifica aggiuntivi. È possibile [rinnovare manualmente](#renew-the-domain) il dominio.
* Il 19 ° giorno dopo la scadenza, il dominio rimane in attesa, ma diventa soggetto a una tariffa di riscatto. È possibile contattare il supporto tecnico per rinnovare il nome di dominio, in base alle eventuali tariffe di rinnovo e riscatto applicabili.
* Il 25 ° giorno dopo la scadenza, Azure inserisce il dominio per l'asta a un servizio di vendita all'asta di un nome di dominio. È possibile contattare il supporto tecnico per rinnovare il nome di dominio, in base alle eventuali tariffe di rinnovo e riscatto applicabili.
* Il 30 ° giorno dopo la scadenza non sarà più possibile riscattare il dominio.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Gestire i record DNS personalizzati

In Azure, i record DNS per un dominio del servizio app vengono gestiti tramite [DNS di Azure](https://azure.microsoft.com/services/dns/). È possibile aggiungere, rimuovere e aggiornare i record DNS, esattamente come per un dominio acquistato esternamente.

### <a name="open-app-service-domain"></a>Aprire Dominio del servizio app

Nel portale di Azure scegliere **tutti i servizi**  >  **domini del servizio app**dal menu a sinistra.

![Screenshot che mostra dove accedere ai domini del servizio app.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selezionare il dominio da gestire. 

### <a name="access-dns-zone"></a>Accesso alla Zona DNS

Nel menu a sinistra del dominio, selezionare **Zona DNS**.

![Screenshot che mostra dove selezionare la zona DNS.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Questa azione permette di aprire la pagina [Zona DNS](../dns/dns-zones-records.md) del dominio del servizio App nel DNS di Azure. Per informazioni su come modificare i record DNS, vedere [Come gestire le zone DNS nel portale di Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Annullare l'acquisto (elimina dominio)

Dopo aver acquistato il dominio del servizio app, si hanno a disposizione cinque giorni per annullare l'acquisto e ricevere un rimborso completo. Dopo cinque giorni, è possibile eliminare il dominio del servizio app, ma non è possibile ricevere un rimborso.

### <a name="open-app-service-domain"></a>Aprire Dominio del servizio app

Nel portale di Azure scegliere **tutti i servizi**  >  **domini del servizio app**dal menu a sinistra.

![Screenshot che mostra dove accedere ai domini del servizio app.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selezionare il dominio che si desidera annullare o eliminare. 

### <a name="delete-hostname-bindings"></a>Eliminare associazioni nome host

Nel menu a sinistra del dominio, selezionare **Associazioni nome host**. Le associazioni nome host da tutti i servizi Azure sono elencate di seguito.

![Screenshot che mostra la pagina associazioni nome host.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Non è possibile eliminare il dominio del servizio app finché non vengono eliminate tutte le associazioni nome host.

Eliminare ogni binding hostname selezionando **...**  >  **Eliminare**. Dopo l'eliminazione di tutte le associazioni, selezionare **Salva**.

![Screenshot che mostra dove eliminare le associazioni del nome host.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Annullare o eliminare

Nel menu a sinistra del dominio, selezionare **Panoramica**. 

Se non è trascorso il periodo di cancellazione del dominio acquistato, selezionare **Annulla acquisto**. In caso contrario, viene visualizzato il pulsante **Elimina**. Per eliminare il dominio senza rimborso, selezionare **Elimina**.

![Screenshot che mostra dove eliminare o annullare un dominio acquistato.](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Per confermare l'operazione, selezionare **ì**.

Al termine dell’operazione, il dominio viene rilasciato dalla sottoscrizione e può essere riacquistato dagli altri utenti. 

## <a name="direct-default-url-to-a-custom-directory"></a>Indirizzare l'URL predefinito a una directory personalizzata

Per impostazione predefinita, il servizio app indirizza le richieste Web alla directory radice del codice dell'app. Per indirizzarle a una sottodirectory, ad esempio `public`, vedere la sezione relativa all'[indirizzamento dell'URL predefinito a una directory personalizzata](app-service-web-tutorial-custom-domain.md#virtualdir).