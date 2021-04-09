---
title: Elimina Azure Active Directory Domain Services | Microsoft Docs
description: Informazioni su come disabilitare o eliminare un Azure Active Directory Domain Services dominio gestito usando il portale di Azure
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: a5126abd6643eba7f63b2bf4ca984bb9892b2d7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619811"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Eliminare un Azure Active Directory Domain Services dominio gestito utilizzando il portale di Azure

Se non è più necessario un dominio gestito di Azure Active Directory Domain Services (Azure AD DS), è possibile eliminarlo. Non è possibile disattivare o disabilitare temporaneamente un dominio gestito di Azure AD DS. L'eliminazione del dominio gestito non comporta l'eliminazione o influisce negativamente sul tenant Azure AD.

Questo articolo illustra come usare la portale di Azure per eliminare un dominio gestito.

> [!WARNING]
> **L'eliminazione è permanente e non può essere invertita.**
> 
> Quando si elimina un dominio gestito, si verificano i passaggi seguenti:
>   * Viene effettuato il deprovisioning dei controller di dominio per il dominio gestito e vengono rimossi dalla rete virtuale.
>   * I dati del dominio gestito vengono eliminati definitivamente, Questi dati includono unità organizzative personalizzate, oggetti Criteri di gruppo, record DNS personalizzati, entità servizio, servizi gestiti e così via, creati dall'utente.
>   * I computer aggiunti al dominio gestito perdono la relazione di trust con il dominio e devono essere separati dal dominio.
>       * Non è possibile accedere a questi computer usando le credenziali di Active Directory aziendali. Al contrario, è necessario utilizzare le credenziali di amministratore locale per il computer.

## <a name="delete-the-managed-domain"></a>Eliminare il dominio gestito

Per eliminare un dominio gestito, attenersi alla procedura seguente:

1. Nel portale di Azure cercare e selezionare **Azure AD Domain Services**.
1. Selezionare il nome del dominio gestito, ad esempio *aaddscontoso.com*.
1. Nella pagina **Panoramica** selezionare **Elimina**. Per confermare l'eliminazione, digitare di nuovo il nome di dominio del dominio gestito e quindi selezionare **Elimina**.

Per eliminare il dominio gestito può essere necessario più di 15-20 minuti.

## <a name="next-steps"></a>Passaggi successivi

Valutare la possibilità di [condividere commenti e suggerimenti][feedback] per le funzionalità che si desidera visualizzare in Azure AD DS.

Per iniziare a usare nuovamente Azure AD DS, vedere [creare e configurare un dominio gestito Azure Active Directory Domain Services][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160593%3fcategory_id%3d160593
[create-instance]: tutorial-create-instance.md