---
title: .NET-program med icke-interaktiv autentisering – Azure HDInsight
description: Lär dig hur du skapar icke-interaktiva autentisering Microsoft .NET program i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/23/2019
ms.openlocfilehash: 0fffe763f77fffcec6010d43deb92ffea9ece15d
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094914"
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Skapa ett .NET HDInsight-program med icke-interaktiv autentisering

Kör din Microsoft .NET Azure HDInsight-app antingen under programmets egna identitet (icke-interaktiv) eller under identiteten för den inloggade användaren av programmet (interaktiv). Den här artikeln visar hur du skapar ett icke-interaktivt .NET-program för autentisering för att ansluta till Azure och hantera HDInsight. Ett exempel på ett interaktivt program finns i [ansluta till Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight).

Du behöver följande från ditt icke-interaktiva .NET-program:

* Ditt klient-ID för Azure-prenumerationen (även kallat ett *katalog-ID*). Se [Hämta klient-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* Klient-ID för Azure Active Directory (Azure AD). Se [skapa ett Azure Active Directory program](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) och [Hämta ett program-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
* Azure AD-programmets hemliga nyckel. Se [Hämta Application Authentication Key](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).

## <a name="prerequisites"></a>Förutsättningar

An-HDInsight kluster. Se [komma igång-kursen](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Tilldela en roll till Azure AD-programmet

Tilldela Azure AD-programmet en [roll](../role-based-access-control/built-in-roles.md)för att ge IT-behörighet att utföra åtgärder. Du kan ange omfång på nivån för prenumerationen, resurs gruppen eller resursen. Behörigheterna ärvs till lägre omfattnings nivåer. Om du till exempel lägger till ett program till rollen läsare för en resurs grupp innebär det att programmet kan läsa resurs gruppen och alla resurser i den. I den här artikeln anger du omfånget på resurs grupps nivå. Mer information finns i [tilldela Azure-roller för att hantera åtkomst till dina Azure-prenumerations resurser](../role-based-access-control/role-assignments-portal.md).

**Lägga till ägar rollen i Azure AD-programmet**

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till den resurs grupp som har HDInsight-klustret där du vill köra Hive-frågan senare i den här artikeln. Om du har ett stort antal resurs grupper kan du använda filtret för att hitta den som du vill ha.
1. Välj **åtkomst kontroll (IAM)** på menyn resurs grupp.
1. Välj fliken **roll tilldelningar** för att se de aktuella roll tilldelningarna.
1. Längst upp på sidan väljer du **+ Lägg till**.
1. Följ anvisningarna för att lägga till ägar rollen i Azure AD-programmet. När du har lagt till rollen visas programmet under rollen ägare.

## <a name="develop-an-hdinsight-client-application"></a>Utveckla ett HDInsight-klientprogram

1. Skapa ett C#-konsolprogram
2. Lägg till följande [NuGet](https://www.nuget.org/) -paket:

    * `Install-Package Microsoft.Azure.Common.Authentication -Pre`
    * `Install-Package Microsoft.Azure.Management.HDInsight -Pre`
    * `Install-Package Microsoft.Azure.Management.Resources -Pre`

3. Kör följande kod:

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

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Azure Active Directory program och tjänstens huvud namn i Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).
* Lär dig hur du [autentiserar ett huvud namn för tjänsten med Azure Resource Manager](../active-directory/develop/howto-authenticate-service-principal-powershell.md).
* Lär dig mer om [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md).
