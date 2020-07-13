---
title: Esercitazione per l'API di creazione report di Azure AD con certificati | Microsoft Docs
description: Questa esercitazione spiega come usare l'API di creazione report di Azure AD con le credenziali del certificato per ottenere i dati provenienti da directory senza intervento dell'utente.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 4f27385cc33c6c289718c3143d03e24f0454a9f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608009"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Esercitazione: Ottenere dati con l'API di creazione report di Azure Active Directory con certificati

Le [API di creazione report di Azure Active Directory (Azure AD)](concept-reporting-api.md) forniscono l'accesso ai dati dal codice tramite un set di API basate su REST. È possibile chiamare le API da numerosi linguaggi di programmazione e strumenti. Se si vuole accedere all'API di creazione report di Azure AD senza l'intervento dell'utente, è necessario configurare l'accesso per l'uso di certificati.

In questa esercitazione si apprenderà come usare un certificato di test per accedere all'API Graph Microsoft e creare report. Non è consigliabile usare certificati di test in un ambiente di produzione. 

## <a name="prerequisites"></a>Prerequisiti

1. Per accedere a dati di accesso, assicurarsi di avere un tenant di Azure Active Directory con una licenza Premium (P1/P2). vedere [Procedura: Effettuare l'iscrizione alle edizioni Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) per aggiornare l'edizione di Azure Active Directory in uso. Si noti che se i dati sulle attività non fossero disponibili prima dell'aggiornamento, saranno necessari un paio di giorni per visualizzare i dati nei report dopo aver eseguito l'aggiornamento a una licenza Premium. 

2. Creare o passare a un account utente nel ruolo **Amministratore globale**, **Amministratore della sicurezza**, **Ruolo con autorizzazioni di lettura per la sicurezza** o **Ruolo con autorizzazioni di lettura per i report** per il tenant. 

3. Completare i [prerequisiti di accesso all'API di creazione report di Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

4. Scaricare e installare [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Installare [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Questo modulo offre diversi cmdlet di utilità, tra cui:
    - Le librerie ADAL necessarie per l'autenticazione
    - Token di accesso dell'utente, chiavi dell'applicazione e certificati con ADAL
    - API Graph che gestisce i risultati di paging

6. Se è la prima volta che si usa il modulo, eseguire **Install-MSCloudIdUtilsModule**; in caso contrario, è possibile importarlo tramite il comando **Import-Module** di PowerShell. La sessione avrà un aspetto simile a questa schermata: ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Usare il cmdlet **New-SelfSignedCertificate** di PowerShell per creare un certificato di test.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Usare il cmdlet **Export-Certificate** per esportarlo in un file del certificato.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Ottenere dati con l'API di creazione report di Azure Active Directory con certificati

1. Passare al [portale di Azure](https://portal.azure.com), selezionare **Azure Active Directory**, quindi selezionare **Registrazioni app** e scegliere l'app dall'elenco. 

2. Selezionare **Certificati e segreti** nella sezione **Gestisci** nel pannello Registrazione dell'applicazione e selezionare **Carica certificato**.

3. Selezionare il file di certificato del passaggio precedente e selezionare **Aggiungi**. 

4. Si noti l'ID applicazione e l'identificazione personale del certificato appena registrato con l'applicazione. Per trovare l'identificazione personale, dalla pagina dell'applicazione nel portale passare a **Certificati e segreti** nella sezione **Gestisci**. L'identificazione personale sarà visualizzata nell'elenco **Certificati**.

5. Aprire il manifesto dell'applicazione nell'editor manifesto inline e verificare che la proprietà *keyCredentials* sia aggiornata con le nuove informazioni del certificato usando lo schema seguente. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ``` 
6. A questo punto è possibile ottenere un token di accesso per l'API Graph usando questo certificato. Usare il cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** dal modulo MSCloudIdUtils di PowerShell passando l'ID dell'applicazione e l'identificazione personale ottenuta nel passaggio precedente. 

   ![Portale di Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Usare il token di accesso nello script di PowerShell per eseguire una query sull'API Graph. Usare il cmdlet **Invoke-MSCloudIdMSGraphQuery** da MSCloudIDUtils per enumerare l'endpoit Signins e directoryAudits. Questo cmdlet gestisce i risultati di multi-paging e li invia alla pipeline di PowerShell.

8. Eseguire una query sull'endpoint directoryAudits per recuperare i log di controllo. 
   ![Azure portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Eseguire una query dell'endpoint Signins per recuperare i log di accesso.
    ![Azure portal](./media/tutorial-access-api-with-certificates/query-signins.png)

10. È ora possibile scegliere di esportare i dati in un file CSV e salvarlo in un sistema SIEM. È anche possibile eseguire il wrapping dello script in un'attività pianificata per ottenere periodicamente i dati di Azure AD dal tenant senza dover archiviare le chiavi dell'applicazione nel codice sorgente. 

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere una prima impressione delle API di creazione report](concept-reporting-api.md)
* [Informazioni di riferimento sulle API di controllo](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Informazioni di riferimento sulle API di report di attività di accesso](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
