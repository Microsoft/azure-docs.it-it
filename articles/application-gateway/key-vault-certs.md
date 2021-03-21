---
title: Terminazione TLS con certificati Azure Key Vault
description: Informazioni su come integrare il gateway applicazione con Key Vault per i certificati server associati a listener abilitati per HTTPS.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 694868f2a75cc66bf9e3ede9d12e30a2cc3d7af9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185938"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Terminazione TLS con certificati di Key Vault

[Azure Key Vault](../key-vault/general/overview.md) è un archivio segreto gestito da piattaforma che è possibile usare per proteggere segreti, chiavi e certificati TLS/SSL. Il gateway applicazione di Azure supporta l'integrazione con Key Vault per i certificati server associati a listener abilitati per HTTPS. Questo supporto è limitato allo SKU V2 del gateway applicazione.

L'integrazione di Key Vault offre due modelli per la terminazione TLS:

- È possibile specificare in modo esplicito i certificati TLS/SSL collegati al listener. Questo modello è il modo tradizionale per passare i certificati TLS/SSL al gateway applicazione per la terminazione TLS.
- È possibile specificare facoltativamente un riferimento a un certificato o un segreto Key Vault esistente quando si crea un listener abilitato per HTTPS.

L'integrazione del gateway applicazione con Key Vault offre molti vantaggi, tra cui:

- Maggiore sicurezza, perché i certificati TLS/SSL non sono gestiti direttamente dal team di sviluppo di applicazioni. L'integrazione consente a un team di sicurezza separato di:
  * Configurare i gateway applicazione.
  * Controllare i cicli di vita del gateway applicazione.
  * Concedere le autorizzazioni ai gateway applicazione selezionati per accedere ai certificati archiviati nell'insieme di credenziali delle chiavi.
- Supporto per l'importazione di certificati esistenti nell'insieme di credenziali delle chiavi. In alternativa, è possibile usare Key Vault API per creare e gestire nuovi certificati con i partner Key Vault attendibili.
- Supporto per il rinnovo automatico dei certificati archiviati nell'insieme di credenziali delle chiavi.

Il gateway applicazione supporta attualmente solo certificati convalidati per il software. Modulo di protezione hardware (HSM): i certificati convalidati non sono supportati. Dopo la configurazione del gateway applicazione per l'uso di certificati Key Vault, le relative istanze recuperano il certificato da Key Vault e le installano localmente per la terminazione TLS. Anche le istanze eseguono il polling Key Vault a intervalli di 4 ore per recuperare una versione rinnovata del certificato, se esistente. Se viene trovato un certificato aggiornato, il certificato TLS/SSL attualmente associato al listener HTTPS viene ruotato automaticamente.

> [!NOTE]
> Il portale di Azure supporta solo certificati dell'insieme di credenziali delle chiavi, non segreti. Il gateway applicazione supporta ancora i segreti di riferimento da un insieme di credenziali delle chiavi, ma solo tramite risorse non del portale come PowerShell, CLI, API, modelli ARM e così via. 

## <a name="how-integration-works"></a>Funzionamento dell'integrazione

L'integrazione del gateway applicazione con Key Vault richiede un processo di configurazione in tre passaggi:

1. **Creare un'identità gestita assegnata dall'utente**

   Si crea o si riutilizza un'identità gestita assegnata dall'utente esistente, che il gateway applicazione usa per recuperare i certificati da Key Vault per conto dell'utente. Per altre informazioni, vedere [creare, elencare, eliminare o assegnare un ruolo a un'identità gestita assegnata dall'utente usando il portale di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Questo passaggio consente di creare una nuova identità nel tenant Azure Active Directory. L'identità è considerata attendibile dalla sottoscrizione usata per creare l'identità.

1. **Configurare l'insieme di credenziali delle chiavi**

   È quindi possibile importare un certificato esistente o crearne uno nuovo nell'insieme di credenziali delle chiavi. Il certificato verrà usato dalle applicazioni che vengono eseguite tramite il gateway applicazione. In questo passaggio, è anche possibile usare un segreto dell'insieme di credenziali delle chiavi archiviato come file PFX con codifica base-64 senza password. È consigliabile usare un tipo di certificato a causa della funzionalità di rinnovo automatico disponibile con gli oggetti tipo di certificato nell'insieme di credenziali delle chiavi. Dopo aver creato un certificato o un segreto, è necessario definire i criteri di accesso nell'insieme di credenziali delle chiavi per consentire all'identità di concedere *l'accesso al* segreto.
   
   > [!IMPORTANT]
   > Il gateway applicazione richiede attualmente Key Vault per consentire l'accesso da tutte le reti per sfruttare l'integrazione. Non supporta l'integrazione di Key Vault quando Key Vault è impostato in modo da consentire solo endpoint privati e selezionare l'accesso alle reti. Il supporto per le reti private e Select è in funziona per l'integrazione completa di Key Vault con il gateway applicazione. 

   > [!NOTE]
   > Se si distribuisce il gateway applicazione tramite un modello ARM tramite l'interfaccia della riga di comando di Azure o PowerShell oppure tramite un'applicazione Azure distribuita dalla portale di Azure, il certificato SSL viene archiviato nell'insieme di credenziali delle chiavi come file PFX con codifica Base64. È necessario completare i passaggi descritti in [usare Azure Key Vault per passare il valore del parametro sicuro durante la distribuzione](../azure-resource-manager/templates/key-vault-parameter.md). 
   >
   > È particolarmente importante impostare `enabledForTemplateDeployment` su `true` . Il certificato potrebbe essere privo di password o potrebbe avere una password. Nel caso di un certificato con una password, l'esempio seguente illustra una possibile configurazione per la `sslCertificates` voce in `properties` per la configurazione del modello ARM per un gateway applicazione. I valori di `appGatewaySSLCertificateData` e `appGatewaySSLCertificatePassword` vengono cercati dall'insieme di credenziali delle chiavi, come descritto nella sezione [segreti di riferimento con ID dinamico](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id). Seguire i riferimenti precedenti da `parameters('secretName')` per vedere come viene eseguita la ricerca. Se il certificato è privo di password, omettere la `password` voce.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **Configurare il gateway applicazione**

   Dopo aver completato i due passaggi precedenti, è possibile impostare o modificare un gateway applicazione esistente per usare l'identità gestita assegnata dall'utente. Per ulteriori informazioni, vedere [set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity).

   È anche possibile configurare il certificato TLS/SSL del listener HTTP in modo che punti all'URI completo del certificato Key Vault o dell'ID segreto.

   ![Certificati di Key Vault](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Passaggi successivi

[Configurare la terminazione TLS con Key Vault certificati usando Azure PowerShell](configure-keyvault-ps.md)
