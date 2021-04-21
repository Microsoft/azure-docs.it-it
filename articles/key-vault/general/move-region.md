---
title: Spostare un insieme di credenziali delle chiavi in un'altra area - Azure Key Vault | Microsoft Docs
description: Questo articolo spiega come spostare un insieme di credenziali delle chiavi in un'area diversa.
services: key-vault
author: msmbaldwin
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: ac2f6347776c2f5d230065b80b1c0336e21e181c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751797"
---
# <a name="move-an-azure-key-vault-across-regions"></a>Spostare un insieme di credenziali delle chiavi di Azure tra aree

Azure Key Vault non supporta operazioni di spostamento delle risorse che consentano lo spostamento di un insieme di credenziali delle chiavi da un'area a un'altra. Questo articolo illustra alcune soluzioni alternative per le organizzazioni che hanno l'esigenza di spostare un insieme di credenziali delle chiavi in un'altra area. Ognuna presenta delle limitazioni. È fondamentale comprendere le implicazioni di queste soluzioni alternative prima di provare ad applicarle in un ambiente di produzione.

Per spostare un insieme di credenziali delle chiavi in un'altra area, occorre creare un insieme di credenziali delle chiavi nell'area di destinazione e quindi copiare manualmente ogni singolo segreto dall'insieme di credenziali delle chiavi esistente in quello nuovo. A questo scopo è possibile usare una delle due opzioni seguenti.

## <a name="design-considerations"></a>Considerazioni sulla progettazione

Prima di iniziare, tenere presenti i concetti seguenti:

* I nomi degli insiemi di credenziali delle chiavi sono univoci a livello globale. Non è possibile riutilizzare un nome di insieme di credenziali.
* È necessario riconfigurare i criteri di accesso e le impostazioni di configurazione della rete nel nuovo insieme di credenziali delle chiavi.
* È necessario riconfigurare l'eliminazione temporanea e la protezione dall'eliminazione nel nuovo insieme di credenziali delle chiavi.
* L'operazione di backup e ripristino non mantiene le impostazioni di rotazione automatica. Potrebbe essere necessario riconfigurare le impostazioni.

## <a name="option-1-use-the-key-vault-backup-and-restore-commands"></a>Opzione 1: usare i comandi di backup e ripristino dell'insieme di credenziali delle chiavi

È possibile eseguire il backup di ogni segreto, chiave e certificato nell'insieme di credenziali usando il comando backup. I segreti vengono scaricati come BLOB crittografato. Si può quindi ripristinare il BLOB nel nuovo insieme di credenziali delle chiavi. Per un elenco dei comandi, vedere [Comandi di Azure Key Vault](/powershell/module/azurerm.keyvault#key_vault).

L'uso dei comandi di backup e ripristino presenta due limitazioni:

* Non è possibile eseguire il backup di un insieme di credenziali delle chiavi in un'area geografica e ripristinarlo in un'altra area geografica. Per altre informazioni, vedere [Aree geografiche di Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

* Il comando backup esegue il backup di tutte le versioni di ogni segreto. Se un segreto ha un numero elevato di versioni precedenti (più di 10), le dimensioni della richiesta potrebbero superare il valore massimo consentito e l'operazione potrebbe non riuscire.

## <a name="option-2-manually-download-and-upload-the-key-vault-secrets"></a>Opzione 2: scaricare e caricare manualmente i segreti dell'insieme di credenziali delle chiavi

Alcuni tipi di segreti possono essere scaricati manualmente. Ad esempio, è possibile scaricare i certificati come file PFX. Questa opzione elimina le restrizioni geografiche per alcuni tipi di segreti, come i certificati. I file PFX possono infatti essere caricati in qualsiasi insieme di credenziali delle chiavi in qualunque area. I segreti vengono scaricati in un formato non protetto da password. La responsabilità della protezione dei segreti durante lo spostamento de dell'utente.