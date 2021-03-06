---
title: Usare un riquadro markdown personalizzato nei dashboard di Azure
description: Informazioni su come aggiungere un riquadro markdown a un dashboard di Azure per visualizzare il contenuto statico
ms.date: 03/19/2021
ms.topic: how-to
ms.custom: devx-track-js
ms.openlocfilehash: 8324b736565cfa353e48cf49b76e2784866f47f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774457"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Usare un riquadro markdown nei dashboard di Azure per visualizzare il contenuto personalizzato

È possibile aggiungere un riquadro markdown ai dashboard di Azure per visualizzare il contenuto statico personalizzato. Ad esempio, è possibile visualizzare le istruzioni di base, un'immagine o un set di collegamenti ipertestuali in un riquadro Markdown.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Aggiungere un riquadro markdown al dashboard

1. Selezionare **Dashboard** nella barra laterale del portale di Azure.

   ![Screenshot che mostra la sidebar del portale](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Nella visualizzazione Dashboard selezionare il dashboard in cui deve essere visualizzato il riquadro Markdown personalizzato, quindi selezionare **modifica**.

   ![Screenshot con la modifica della visualizzazione del dashboard](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Nella **raccolta di riquadri** individuare il riquadro denominato **Markdown** e selezionare **Aggiungi**. Il riquadro viene aggiunto al dashboard e si apre il riquadro **Modifica markdown**.

1. Immettere i valori per **titolo** e **sottotitolo**, che vengono visualizzati nel riquadro dopo lo spostamento in un altro campo.

   ![Screenshot che mostra i risultati dell'immissione di titolo e sottotitolo](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Selezionare una delle opzioni per includere il contenuto di Markdown: **modifica inline** o **Inserisci contenuto tramite URL**.

   - Selezionare **modifica inline** se si desidera immettere direttamente Markdown.

      ![Screenshot che mostra l'immissione di contenuto inline](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Selezionare **Inserisci contenuto con URL** se si vuole usare il contenuto Markdown esistente ospitato online.

      ![Screenshot che illustra l'immissione dell'URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Per una maggiore sicurezza, è possibile creare un file Markdown e archiviarlo in un [BLOB dell'account di archiviazione di Azure in cui è abilitata la crittografia](../storage/common/storage-service-encryption.md), quindi puntare al file usando l'opzione URL. Il contenuto di Markdown viene crittografato tramite le opzioni di crittografia dell'account di archiviazione. Solo gli utenti con autorizzazioni per il file possono visualizzare il contenuto di Markdown nel dashboard. Potrebbe essere necessario impostare una regola di [condivisione delle risorse tra le origini (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) nell'account di archiviazione in modo che il portale di Azure ( _https://portal.azure.com/_ ) possa accedere al file Markdown nel BLOB.

1. Selezionare **Fine** per chiudere il riquadro **Modifica markdown**. Il contenuto viene visualizzato nel riquadro Markdown, che è possibile ridimensionare trascinando il quadratino nell'angolo in basso a destra.

   ![Screenshot con il riquadro markdown personalizzato](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Limitazioni e funzionalità del contenuto markdown

È possibile usare qualsiasi combinazione di testo normale, sintassi Markdown e contenuto HTML nel riquadro markdown. Il portale di Azure usa una libreria open source denominata _marked_ per trasformare il contenuto nel codice HTML visualizzato nel riquadro. Il codice HTML generato da _marked_ viene preelaborato dal portale prima del rendering. Questo passaggio consente di assicurarsi che la personalizzazione non influisca sulla sicurezza o sul layout del portale. Durante la preelaborazione, viene rimossa qualsiasi parte del codice HTML che rappresenta una potenziale minaccia. I tipi di contenuto seguenti non sono consentiti dal portale:

* JavaScript: `<script>` i tag e le valutazioni JavaScript inline vengono rimossi.
* iframes: `<iframe>` i tag vengono rimossi.
* `<style>`I tag di stile vengono rimossi. Gli attributi di stile in linea negli elementi HTML non sono ufficialmente supportati. Alcuni elementi di stile in linea possono essere accettabili, ma se interferiscono con il layout del portale, potrebbero smettere di funzionare in qualsiasi momento. Il riquadro markdown è destinato al contenuto statico di base che usa gli stili predefiniti del portale.

## <a name="next-steps"></a>Passaggi successivi

* Per creare un dashboard personalizzato, vedere [Creare e condividere dashboard nel portale di Azure](../azure-portal/azure-portal-dashboards.md)
