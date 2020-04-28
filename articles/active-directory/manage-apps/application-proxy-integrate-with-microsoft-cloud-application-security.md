---
title: Integrare app locali con Cloud App Security Azure AD
description: Configurare un'applicazione in locale in Azure Active Directory in modo che collabori con Microsoft Cloud App Security (MCAS). Usare il Controllo app per l'accesso condizionale MCAS per monitorare e controllare le sessioni in tempo reale in base ai criteri di accesso condizionale. È possibile applicare questi criteri alle applicazioni locali che usano Proxy di applicazione in Azure Active Directory (Azure AD).
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb97f9dd87277215a5d4708d3a6f49564c490204
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74275503"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Configurare il monitoraggio in tempo reale degli accessi alle applicazioni con Microsoft Cloud App Security e Azure Active Directory
Configurare un'applicazione in locale in Azure Active Directory (Azure AD) per usare Microsoft Cloud App Security (MCAS) per il monitoraggio in tempo reale. MCAS USA Controllo app per l'accesso condizionale per monitorare e controllare le sessioni in tempo reale in base ai criteri di accesso condizionale. È possibile applicare questi criteri alle applicazioni locali che usano Proxy di applicazione in Azure Active Directory (Azure AD).

Di seguito sono riportati alcuni esempi dei tipi di criteri che è possibile creare con MCAS:

- Blocco o protezione del download dei documenti sensibili in dispositivi non gestiti.
- Monitoraggio dell’accesso alle applicazioni e delle azioni eseguite dagli utenti ad alto rischio. Con queste informazioni, è possibile analizzare il comportamento degli utenti per decidere come applicare i criteri di sessione.
- Utilizzo dei certificati client o di conformità del dispositivo per bloccare l'accesso ad applicazioni specifiche da dispositivi non gestiti.
- Limitazione delle sessioni utente dalle reti non aziendali. È possibile concedere un accesso limitato agli utenti che non fanno parte della rete aziendale. Ad esempio, l’accesso limitato può impedire all'utente di scaricare i documenti riservati.

Per altre informazioni, vedere [Proteggere le app con Controllo app per l’accesso condizionale di Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Requisiti

Licenza:

- Licenza EMS E5, oppure 
- Azure Active Directory Premium P1 e MCAS Standalone.

Applicazione locale:

- L'applicazione locale deve usare la delega vincolata Kerberos (KCD)

Configurare il proxy dell'applicazione:

- Configurare Azure AD in modo che utilizzi il proxy dell’applicazione, inclusa la preparazione dell’ambiente e l’installazione del connettore proxy dell’applicazione. Per un’esercitazione, vedere [Aggiungere applicazioni locali per l'accesso remoto tramite il proxy di applicazione in Azure AD](application-proxy-add-on-premises-application.md). 

## <a name="add-on-premises-application-to-azure-ad"></a>Aggiunta di un'applicazione locale ad Azure AD

Aggiungere un'applicazione locale ad Azure AD. Pre una guida introduttiva, vedere [Aggiungere un'app locale ad Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). Quando si aggiunge l'applicazione, assicurarsi di impostare le due impostazioni seguenti nel pannello **Aggiunta dell’applicazione locale**:

- **Pre-autenticazione**: immettere **Azure Active Directory**.
- **Convertire gli URL nel corpo dell'applicazione**: scegliere **Sì**.

Queste due impostazioni sono necessarie per consentire l’interazione tra l’applicazione e MCAS.

## <a name="test-the-on-premises-application"></a>Verificare l’applicazione locale

Dopo aver aggiunto l'applicazione ad Azure AD, seguire la procedura descritta in [Verifica dell'applicazione](application-proxy-add-on-premises-application.md#test-the-application) per aggiungere un utente di verifica e verificare l'accesso. 

## <a name="deploy-conditional-access-app-control"></a>Distribuzione di Controllo app per l'accesso condizionale

Per configurare l'applicazione con Controllo app per l’accesso condizionale, seguire le istruzioni in [Distribuzione di Controllo app per l’accesso condizionale per le app Azure AD](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Verifica di Controllo app per l'accesso condizionale

Per verificare la distribuzione di applicazioni di Azure AD con Controllo app per l’accesso condizionale, seguire le istruzioni riportate in [Verifica della distribuzione di app Azure AD](/cloud-app-security/proxy-deployment-aad).





