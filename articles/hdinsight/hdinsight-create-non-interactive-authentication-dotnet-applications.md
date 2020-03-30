---
title: Autenticazione non interattiva Applicazione .NET - Azure HDInsight
description: Informazioni su come creare applicazioni Microsoft .NET di autenticazione non interattive in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 5e6a0586bc750f8972586920c15dbb297295aa20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371274"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Creare un'applicazione HDInsight .NET di autenticazione non interattiva

Eseguire l'applicazione Microsoft Azure HDInsight con l'identità dell'applicazione (non interattiva) o con l'identità dell'utente connesso dell'applicazione (interattiva). Questo articolo descrive come creare un'applicazione .NET di autenticazione non interattiva per connettersi ad Azure e gestire HDInsight. Per un esempio di applicazione interattiva, vedere [Connettersi ad Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

Dall'applicazione .NET non interattiva, è necessario disporre degli elementi seguenti:

* ID tenant della sottoscrizione di Azure, detto anche *ID directory*. Vedere [Ottenere l'ID tenant](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
* ID client dell'applicazione Azure Active Directory (Azure AD). Vedere [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) e Ottenere un ID [applicazione.](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)
* Chiave privata dell'applicazione Azure AD. Vedere Ottenere la chiave di [autenticazione dell'applicazione](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).

## <a name="prerequisites"></a>Prerequisiti

Un cluster HDInsight. Vedere l'[esercitazione introduttiva](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Assegnare un ruolo all'applicazione Azure AD

Assegnare un [ruolo](../role-based-access-control/built-in-roles.md) all'applicazione Azure AD per concedere le autorizzazioni per l'esecuzione di azioni. È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate ai livelli inferiori dell'ambito. Ad esempio, l'aggiunta di un'applicazione al ruolo Lettore per un gruppo di risorse significa che l'applicazione può leggere il gruppo di risorse e le eventuali risorse in esso contenuto. In questo articolo si imposta l'ambito a livello di gruppo di risorse. Per altre informazioni, vedere [Usare l'assegnazione dei ruoli per gestire l'accesso alle risorse della sottoscrizione di Azure](../role-based-access-control/role-assignments-portal.md).

**Per aggiungere il ruolo Proprietario all'applicazione Azure AD**

1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Passare al gruppo di risorse con il cluster HDInsight in cui verrà eseguita la query Hive più avanti in questo articolo. Se c'è un numero elevato di gruppi di risorse, è possibile usare il filtro per trovare il gruppo desiderato.
1. Nel menu relativo al gruppo di risorse selezionare **Controllo di accesso (IAM)**.
1. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco corrente di assegnazioni di ruolo.
1. Nella parte superiore della pagina, selezionare **Aggiungi**.
1. Seguire le istruzioni per aggiungere il ruolo Proprietario all'applicazione Azure AD. Dopo avere aggiunto il ruolo, l'applicazione viene elencata per il ruolo Proprietario.

## <a name="develop-an-hdinsight-client-application"></a>Sviluppare un'applicazione client HDInsight

1. Creare un'applicazione console C#.
2. Aggiungere i seguenti pacchetti [NuGet:](https://www.nuget.org/)

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Eseguire il codice seguente:

    ```csharp
    using System;
    using System.Security;
    using Microsoft.Azure;
    using Microsoft.Azure.Common.Authentication;
    using Microsoft.Azure.Common.Authentication.Factories;
    using Microsoft.Azure.Common.Authentication.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.HDInsight;
    
    namespace CreateHDICluster
    {
        internal class Program
        {
            private static HDInsightManagementClient _hdiManagementClient;
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
            private static void Main(string[] args)
            {
                var key = new SecureString();
                foreach (char c in secretKey) { key.AppendChar(c); }
    
                var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
    
                var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                resourceManagementClient.Providers.Register("Microsoft.HDInsight");
    
                _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
    
                var results = _hdiManagementClient.Clusters.List();
                foreach (var name in results.Clusters)
                {
                    Console.WriteLine("Cluster Name: " + name.Name);
                    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                    Console.WriteLine("\t Cluster location: " + name.Location);
                    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                }
                Console.WriteLine("Press Enter to continue");
                Console.ReadLine();
            }
    
            /// Get the access token for a service principal and provided key.          
            public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
            {
                var authFactory = new AuthenticationFactory();
                var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                var accessToken =
                    authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
    
                return new TokenCloudCredentials(accessToken);
            }
    
            public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
            {
                return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
            }
        }
    }
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Creare un'applicazione Azure Active Directory e un'entità servizio nel portale di Azure](../active-directory/develop/howto-create-service-principal-portal.md).
* Leggere le informazioni su come [autenticare un'entità servizio con Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Leggere le informazioni sul [controllo degli accessi in base al ruolo di Azure](../role-based-access-control/role-assignments-portal.md).
