---
title: Aggiungere attributi personalizzati ai flussi di iscrizione self-service-Azure AD
description: Informazioni su come personalizzare gli attributi per i flussi utente di iscrizione self-service.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46b498f8b8512d0202f47dd31ba25cc851ca71e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644103"
---
# <a name="define-custom-attributes-for-user-flows"></a>Definire attributi personalizzati per i flussi utente

È possibile definire requisiti diversi per le informazioni da raccogliere durante l'iscrizione alle singole applicazioni. Azure AD include un set predefinito di informazioni archiviate in attributi, ad esempio nome, cognome, città e codice postale. Con Azure AD è possibile estendere il set di attributi archiviati in un account Guest quando l'utente esterno si iscrive tramite un flusso utente.

È possibile creare attributi personalizzati nel portale di Azure e usarli nei flussi utente di iscrizione self-service. Questi attributi possono anche essere scritti e letti usando l'[API Microsoft Graph](../../active-directory-b2c/microsoft-graph-operations.md). L'API Microsoft Graph supporta la creazione e l'aggiornamento di un utente con attributi di estensione. Gli attributi di estensione nell'API Graph vengono denominati usando la convenzione `extension_<extensions-app-id>_attributename`. Ad esempio:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

`<extensions-app-id>`È specifico del tenant. Per trovare questo identificatore, passare a Azure Active Directory > Registrazioni app > tutte le applicazioni. Cercare l'app che inizia con "AAD-Extensions-app" e selezionarla. Nella pagina Panoramica dell'app prendere nota dell'ID applicazione (client).

## <a name="create-a-custom-attribute"></a>Creare un attributo personalizzato

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. In **Servizi di Azure** selezionare **Azure Active Directory**.
3. Nel menu a sinistra selezionare **Identità esterne**.
4. Selezionare **attributi utente personalizzati**. Sono elencati gli attributi utente disponibili.

   ![Selezionare gli attributi utente per l'iscrizione](media/user-flow-add-custom-attributes/user-attributes.png)

5. Per aggiungere un attributo, selezionare **Aggiungi**.
6. Nel riquadro **Aggiungi un attributo** immettere i valori seguenti:

   - **Nome**: specificare un nome per l'attributo personalizzato, ad esempio "Shoesize".
   - **Tipo di dati**: scegliere un tipo di dati (**String**, **Boolean** o **Int**).
   - **Descrizione**: immettere una descrizione facoltativa dell'attributo personalizzato per uso interno. Questa descrizione non è visibile all'utente.

   ![Aggiungi un attributo](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Selezionare **Crea**.

L'attributo personalizzato è ora disponibile nell'elenco degli attributi utente e per l'uso nei flussi utente. Un attributo personalizzato viene creato solo la prima volta che viene usato in un flusso utente e non quando viene aggiunto all'elenco di attributi utente.

Dopo aver creato un nuovo utente con un flusso utente che usa l'attributo personalizzato appena creato, è possibile eseguire query sull'oggetto in [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Verrà visualizzato **ShoeSize** nell'elenco di attributi raccolti durante il percorso di iscrizione nell'oggetto utente. È possibile chiamare il API Graph dall'applicazione per ottenere i dati da questo attributo dopo che è stato aggiunto all'oggetto utente.

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere un flusso utente di iscrizione self-service a un'app](self-service-sign-up-user-flow.md)