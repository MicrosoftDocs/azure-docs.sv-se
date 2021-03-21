---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "70803014"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Hämta en Azure Resource Manager-token
Azure Active Directory måste autentisera alla aktiviteter som du utför på resurser med hjälp av Azure Resource Manager. Exemplet som visas här använder lösenordsautentisering, för andra metoder se [autentisering Azure Resource Manager begär Anden][lnk-authenticate-arm].

1. Lägg till följande kod i **main** -metoden i program. cs för att hämta en token från Azure AD med hjälp av program-ID och lösen ord.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Skapa ett **ResourceManagementClient** -objekt som använder token genom att lägga till följande kod i slutet av **main** -metoden:
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Skapa eller hämta en referens till den resurs grupp du använder:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx