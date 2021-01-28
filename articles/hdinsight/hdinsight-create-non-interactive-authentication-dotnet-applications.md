---
title: Applicazione .NET di autenticazione non interattiva-Azure HDInsight
description: Informazioni su come creare applicazioni Microsoft .NET di autenticazione non interattive in Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/23/2019
ms.openlocfilehash: 121c5850ef47999f54d206b95b69e10775d3e5c9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946093"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Creare un'applicazione HDInsight .NET di autenticazione non interattiva

Eseguire l'applicazione Microsoft .NET Azure HDInsight con l'identità dell'applicazione (non interattiva) o con l'identità dell'utente che ha eseguito l'accesso all'applicazione (interattiva). Questo articolo descrive come creare un'applicazione .NET di autenticazione non interattiva per connettersi ad Azure e gestire HDInsight. Per un esempio di applicazione interattiva, vedere [Connettersi ad Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

Dall'applicazione .NET non interattiva, è necessario disporre degli elementi seguenti:

* ID tenant della sottoscrizione di Azure, detto anche *ID directory*. Vedere [Ottenere l'ID tenant](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* ID client dell'applicazione Azure Active Directory (Azure AD). Vedere [creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) e [ottenere un ID applicazione](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* Chiave privata dell'applicazione Azure AD. Vedere [ottenere la chiave di autenticazione dell'applicazione](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="prerequisites"></a>Prerequisiti

Un cluster HDInsight. Vedere l'[esercitazione introduttiva](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Assegnare un ruolo all'applicazione Azure AD

Assegnare un [ruolo](../role-based-access-control/built-in-roles.md) all'applicazione Azure AD per concedere le autorizzazioni per l'esecuzione di azioni. È possibile impostare l'ambito al livello della sottoscrizione, del gruppo di risorse o della risorsa. Le autorizzazioni vengono ereditate ai livelli inferiori dell'ambito. Ad esempio, l'aggiunta di un'applicazione al ruolo lettore per un gruppo di risorse indica che l'applicazione può leggere il gruppo di risorse e tutte le risorse al suo interno. In questo articolo viene impostato l'ambito a livello di gruppo di risorse. Per altre informazioni, vedere [Usare l'assegnazione dei ruoli per gestire l'accesso alle risorse della sottoscrizione di Azure](../role-based-access-control/role-assignments-portal.md).

**Per aggiungere il ruolo Proprietario all'applicazione Azure AD**

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al gruppo di risorse che contiene il cluster HDInsight in cui verrà eseguita la query hive più avanti in questo articolo. Se c'è un numero elevato di gruppi di risorse, è possibile usare il filtro per trovare il gruppo desiderato.
1. Nel menu relativo al gruppo di risorse selezionare **Controllo di accesso (IAM)**.
1. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco corrente di assegnazioni di ruolo.
1. Nella parte superiore della pagina selezionare **+ Aggiungi**.
1. Seguire le istruzioni per aggiungere il ruolo Proprietario all'applicazione Azure AD. Dopo avere aggiunto il ruolo, l'applicazione viene elencata per il ruolo Proprietario.

## <a name="develop-an-hdinsight-client-application"></a>Sviluppare un'applicazione client HDInsight

1. Creare un'applicazione console C#.
2. Aggiungere i pacchetti [NuGet](https://www.nuget.org/) seguenti:

    * `Install-Package Microsoft.Azure.Common.Authentication -Pre`
    * `Install-Package Microsoft.Azure.Management.HDInsight -Pre`
    * `Install-Package Microsoft.Azure.Management.Resources -Pre`

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
* Informazioni sul [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/role-assignments-portal.md).
