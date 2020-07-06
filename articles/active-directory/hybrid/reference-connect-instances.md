---
title: 'Azure AD Connect: Istanze del servizio di sincronizzazione | Documentazione Microsoft'
description: Questa pagina contiene considerazioni speciali per le istanze di Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c342eac5460d8d52422b0497b1283f367660eb3c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "66298824"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect: Considerazioni speciali per le istanze
Azure AD Connect si usa soprattutto con l'istanza di Azure AD e Office 365 disponibili in tutto il mondo. Ma esistono anche altre istanze e hanno requisiti diversi per gli URL e altre considerazioni speciali.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) è un sovereign cloud gestito da un trustee dei dati tedesco.

| URL da aprire nel server proxy |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +Elenchi di revoche dei certificati |

Quando si accede al tenant di Azure AD, è necessario usare un account nel dominio onmicrosoft.de.

Funzionalità attualmente non presenti in Microsoft Cloud Germany:

* Il **writeback delle password** è disponibile per l'anteprima con Azure ad Connect versione 1.1.570.0 e successive.
* Altri servizi di Azure AD Premium non sono disponibili.

## <a name="microsoft-azure-government"></a>Microsoft Azure per enti pubblici
Il [cloud di Microsoft Azure per enti pubblici](https://azure.microsoft.com/features/gov/) è un cloud riservato al governo degli Stati Uniti.

È stato supportato da versioni precedenti di DirSync. A partire dalla build 1.1.180 di Azure AD Connect è supportata la generazione successiva del cloud. Questa generazione usa endpoint situati esclusivamente negli Stati Uniti e un elenco diverso di URL da aprire nel server proxy.

| URL da aprire nel server proxy |
| --- |
| \*.microsoftonline.com |
| \*. microsoftonline.us |
| \*. windows.net (obbligatorio per il rilevamento automatico del tenant di Azure per enti pubblici) |
| \*.gov.us.microsoftonline.com |
| +Elenchi di revoche dei certificati |

> [!NOTE]
> A partire da Azure AD Connect versione 1.1.647.0, l'impostazione del valore AzureInstance nel registro di sistema non è più necessaria purché *. windows.net sia aperto nei server proxy. Tuttavia, per i clienti che non consentono la connettività Internet dai rispettivi server di Azure AD Connect, è possibile usare la seguente configurazione manuale.

### <a name="manual-configuration"></a>Configurazione manuale

I seguenti passaggi di configurazione manuale vengono usati per assicurarsi che Azure AD Connect usi gli endpoint di sincronizzazione di Azure per enti pubblici.

1. Avviare l'installazione di Azure AD Connect.
2. Quando viene visualizzata la prima pagina in cui si accetta il contratto di licenza, non continuare ma lasciare che venga eseguita l'installazione guidata.
3. Avviare regedit e modificare la chiave del Registro di sistema `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` impostandola sul valore `4`.
4. Tornare all'installazione guidata di Azure AD Connect, accettare il contratto di licenza e continuare. Durante l'installazione, assicurarsi di usare il percorso di installazione della **configurazione personalizzata** (e non l'installazione rapida), quindi continuare l'installazione come di consueto.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
