---
title: Come proteggere l'accesso ad Azure Data Catalog
description: Questo articolo illustra come proteggere un catalogo dati e gli asset di dati in Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b0972be2b8a6e05d3d90cde7354b4890ea95cbe4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "68976754"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>Come proteggere l'accesso al catalogo dati e agli asset di dati

> [!IMPORTANT]
> Questa funzionalità è disponibile solo nell'edizione Standard di Azure Data Catalog.

Azure Data Catalog consente di specificare chi può accedere al catalogo dati e quali operazioni può eseguire sui metadati del catalogo (registrazione, annotazione, assunzione della proprietà del catalogo). 

## <a name="catalog-users-and-permissions"></a>Utenti e autorizzazioni del catalogo

Per assegnare a un utente o un gruppo l'accesso a un catalogo dati e impostare le autorizzazioni:

1. Nella [home page del catalogo dati](https://www.azuredatacatalog.com) fare clic su **Impostazioni** sulla barra degli strumenti.

   ![Pulsante Impostazioni home page Azure Data Catalog](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)

2. Nella pagina delle impostazioni espandere la sezione **Utenti del catalogo**.

   ![Pulsante Aggiungi Azure Data Catalog utenti](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)

3. Scegliere **Aggiungi**.

4. Immettere il **nome utente** completo o il nome del **gruppo di sicurezza** di Azure Active Directory (AAD) associato al catalogo. Se si aggiungono più utenti o gruppi usare la virgola (',') come separatore.

   ![Utenti Azure Data Catalog-utenti o gruppi](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)

5. Premere **INVIO** o **TAB** per uscire dalla casella di testo. 

6. Verificare che tutte le autorizzazioni (**Annota**, **Registra** e **Diventa proprietario**) siano assegnate per impostazione predefinita a tali utenti o gruppi. In tal modo l'utente o il gruppo potrà [registrare asset di dati]( data-catalog-how-to-register.md), [annotare asset di dati]( data-catalog-how-to-annotate.md) e [diventare proprietario di asset di dati]( data-catalog-how-to-manage.md). 

   ![Utenti Azure Data Catalog-autorizzazioni predefinite](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)

7. Per assegnare a un utente o un gruppo solo l'accesso in lettura al catalogo, disattivare l'opzione **Annota** per l'utente o il gruppo. In questo modo l'utente o il gruppo non può aggiungere annotazioni agli asset di dati del catalogo, ma può visualizzarli. 

8. Per impedire a un utente o un gruppo di registrare asset di dati, disattivare l'opzione **Registra** per l'utente o il gruppo.

9. Per impedire a un utente o un gruppo di diventare proprietario di un asset di dati, disattivare l'opzione **Diventa proprietario** per l'utente o il gruppo. 

10. Per eliminare un utente o un gruppo dagli utenti del catalogo, fare clic su **x** in corrispondenza dell'utente o del gruppo nella parte inferiore dell'elenco. 

   ![Utenti del catalogo Azure Data Catalog-icona Elimina utente X](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

   > [!IMPORTANT]
   > È consigliabile aggiungere gruppi di sicurezza come utenti del catalogo, anziché aggiungere direttamente singoli utenti e assegnare loro le autorizzazioni. Aggiungere quindi a ogni gruppo di sicurezza gli utenti che corrispondono ai ruoli e ai diritti di accesso al catalogo del gruppo.

## <a name="special-considerations"></a>Considerazioni speciali

- Le autorizzazioni assegnate ai gruppi di sicurezza sono cumulative. Si supponga ad esempio che un utente appartenga a due gruppi. Un gruppo ha l'autorizzazione di annotazione e l'altro gruppo non ha tale autorizzazione. L'utente avrà dell'autorizzazione di annotazione. 
- Le autorizzazioni assegnate in modo esplicito a un utente eseguono l'override delle autorizzazioni assegnate ai gruppi a cui appartiene l'utente. Nell'esempio precedente, si supponga di aver aggiunto in modo esplicito l'utente agli utenti del catalogo e di non aver assegnato a tale utente l'autorizzazione di annotazione. L'utente non può aggiungere annotazioni agli asset di dati, anche se è membro di un gruppo che dispone dell'autorizzazione di annotazione.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al Catalogo dati di Azure](data-catalog-get-started.md)
