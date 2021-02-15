---
title: QnA Maker la crittografia dei dati inattivi
titleSuffix: Azure Cognitive Services
description: Microsoft offre chiavi di crittografia gestite da Microsoft e consente inoltre di gestire le sottoscrizioni di servizi cognitivi con chiavi personalizzate, denominate chiavi gestite dal cliente (CMK). Questo articolo illustra la crittografia dei dati inattivi per QnA Maker e come abilitare e gestire CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 19dc0f3a676d5373b28e4b7055050477c426f847
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524462"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker la crittografia dei dati inattivi

QnA Maker crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud, contribuendo a soddisfare gli obiettivi di sicurezza e conformità dell'organizzazione.

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft. È anche possibile gestire la sottoscrizione con chiavi personalizzate denominate chiavi gestite dal cliente (CMK). CMK offre una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati. Se CMK è configurato per la sottoscrizione, viene fornita la crittografia doppia, che offre un secondo livello di protezione, consentendo al contempo di controllare la chiave di crittografia tramite il Azure Key Vault.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

QnA Maker usa il supporto CMK da ricerca di Azure. Configurare [CMK in ricerca di Azure usando Azure Key Vault](../../search/search-security-manage-encryption-keys.md). Questa istanza di Azure deve essere associata al servizio QnA Maker per renderla CMK abilitata.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

QnA Maker usa il [supporto CMK da ricerca di Azure](../../search/search-security-manage-encryption-keys.md)e associa automaticamente il CMK fornito per crittografare i dati archiviati nell'indice di ricerca di Azure.

---

> [!IMPORTANT]
> La risorsa del servizio ricerca di Azure deve essere stata creata dopo il 2019 gennaio e non può trovarsi nel livello gratuito (condiviso). Non è disponibile alcun supporto per la configurazione delle chiavi gestite dal cliente nel portale di Azure.

## <a name="enable-customer-managed-keys"></a>Abilitare chiavi gestite dal cliente

Il servizio QnA Maker USA CMK dal servizio ricerca di Azure. Per abilitare CMK, attenersi alla procedura seguente:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker - disponibilità generale (versione stabile)](#tab/v1)

1. Creare una nuova istanza di ricerca di Azure e abilitare i prerequisiti indicati nei [prerequisiti della chiave gestita dal cliente per Azure ricerca cognitiva](../../search/search-security-manage-encryption-keys.md#prerequisites).

   ![Visualizzare le impostazioni di crittografia 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Quando si crea una risorsa di QnA Maker, questa viene associata automaticamente a un'istanza di ricerca di Azure. Questa istanza non può essere utilizzata con CMK. Per usare CMK, è necessario associare l'istanza appena creata di ricerca di Azure creata nel passaggio 1. In particolare, è necessario aggiornare `AzureSearchAdminKey` e `AzureSearchName` nella risorsa QnA Maker.

   ![Visualizzare le impostazioni di crittografia 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Successivamente, creare una nuova impostazione dell'applicazione:
   * **Nome**: impostare su `CustomerManagedEncryptionKeyUrl`
   * **Valore**: usare il valore ottenuto nel passaggio 1 durante la creazione dell'istanza di ricerca di Azure.

   ![Visualizzare le impostazioni di crittografia 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Al termine, riavviare il Runtime. Il servizio QnA Maker è ora abilitato per CMK.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker - gestito (versione in anteprima)](#tab/v2)

1.  Passare alla scheda **crittografia** del servizio QnA Maker gestito (anteprima).
2.  Selezionare l'opzione **chiavi gestite dal cliente** . Specificare i dettagli delle [chiavi gestite dal cliente](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) e fare clic su **Salva**.

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="Impostazione CMK gestita (anteprima) QnA Maker" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  In una corretta operazione di salvataggio, il CMK verrà usato per crittografare i dati archiviati nell'indice di ricerca di Azure.

> [!IMPORTANT]
> È consigliabile impostare il CMK in un servizio ricerca cognitiva di Azure nuovo prima di creare le Knowledge base. Se si imposta CMK in un servizio QnA Maker con Knowledge base esistenti, è possibile che si perda l'accesso. Scopri di più sull' [uso di contenuto crittografato](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content) in ricerca cognitiva di Azure.

> [!NOTE]
> Per richiedere la possibilità di usare chiavi gestite dal cliente, compilare e inviare il [modulo di richiesta di Customer-Managed chiave di servizi cognitivi](https://aka.ms/cogsvc-cmk).

---

## <a name="regional-availability"></a>Disponibilità a livello di area

Le chiavi gestite dal cliente sono disponibili in tutte le aree di ricerca di Azure.

## <a name="encryption-of-data-in-transit"></a>Crittografia dei dati in transito

QnA Maker portale viene eseguito nel browser dell'utente. Ogni azione attiva una chiamata diretta alla rispettiva API di servizi cognitivi. Di conseguenza, QnA Maker è conforme ai dati in transito.
Tuttavia, poiché il servizio di QnA Maker portale è ospitato negli Stati Uniti occidentali, non è ancora ideale per i clienti non statunitensi. 

## <a name="next-steps"></a>Passaggi successivi

* [Crittografia in ricerca di Azure con CMK in Azure Key Vault](../../search/search-security-manage-encryption-keys.md)
* [Crittografia dei dati inattivi](../../security/fundamentals/encryption-atrest.md)
* [Altre informazioni su Azure Key Vault](../../key-vault/general/overview.md)