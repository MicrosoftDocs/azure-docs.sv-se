---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 26c158145de6ce729d8a7060152b19fb14b63d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95562317"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Förbered för att autentisera Azure Resource Manager begär Anden
Du måste autentisera alla åtgärder som du utför på resurser med hjälp av [Azure Resource Manager][lnk-authenticate-arm] med Azure Active Directory (AD). Det enklaste sättet att konfigurera detta är att använda PowerShell eller Azure CLI.

Installera [Azure PowerShell-cmdletar][lnk-powershell-install] innan du fortsätter.

Följande steg visar hur du konfigurerar lösenordsautentisering för ett AD-program med hjälp av PowerShell. Du kan köra dessa kommandon i en standard-PowerShell-session.

1. Logga in på din Azure-prenumeration med följande kommando:

    ```powershell
    Connect-AzAccount
    ```

1. Om du har flera Azure-prenumerationer ger du till gång till alla Azure-prenumerationer som är kopplade till dina autentiseringsuppgifter genom att logga in på Azure. Använd följande kommando för att lista de Azure-prenumerationer som du kan använda:

    ```powershell
    Get-AzSubscription
    ```

    Använd följande kommando för att välja den prenumeration som du vill använda för att köra kommandona för att hantera IoT-hubben. Du kan antingen använda prenumerationsnamnet eller ID:t från utdata från föregående kommando:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Anteckna ditt **TenantId** och **SubscriptionId**. Du behöver dem senare.
3. Skapa ett nytt Azure Active Directory program med hjälp av följande kommando och ersätt plats innehavarna:
   
   * **{Visnings namn}:** ett visnings namn för programmet, till exempel **MySampleApp**
   * **{URL för hem sida}:** URL: en för appens start sida, till exempel **http: \/ /mysampleapp/Home**. URL: en behöver inte peka på ett verkligt program.
   * **{Program identifierare}:** En unik identifierare, till exempel **http: \/ /mysampleapp**. URL: en behöver inte peka på ett verkligt program.
   * **{Password}:** Ett lösen ord som du använder för att autentisera med din app.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Anteckna **ApplicationId** för det program som du har skapat. Du behöver detta senare.
5. Skapa ett nytt huvud namn för tjänsten med hjälp av följande kommando och Ersätt **{MyApplicationId}** med **ApplicationId** från föregående steg:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Konfigurera en roll tilldelning med följande kommando, Ersätt **{MyApplicationId}** med din **ApplicationId**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Nu har du skapat Azure AD-programmet som gör att du kan autentisera från ditt anpassade C#-program. Du behöver följande värden senare i den här självstudien:

* TenantId
* SubscriptionId
* ApplicationId
* Lösenord

[lnk-authenticate-arm]: /rest/api/
[lnk-powershell-install]: /powershell/azure/install-az-ps