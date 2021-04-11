---
title: Skapa avdelningens kontroll-konto med .NET SDK
description: Skapa ett Azure avdelningens kontroll-konto med .NET SDK.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: 04ed5cef351c81355a2390dd0b983c162f2b9532
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387577"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Snabb start: skapa ett avdelningens kontroll-konto med .NET SDK

I den här snabb starten beskrivs hur du använder .NET SDK för att skapa ett Azure avdelningens kontroll-konto 

> [!IMPORTANT]
> Azure avdelningens kontroll är för närvarande en för hands version. Kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* En egen [klientorganisation i Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Ditt konto måste ha behörighet att skapa resurser i prenumerationen

* Om du **Azure policy** blockera alla program från att skapa **lagrings konto** och **EventHub-namnområde** måste du göra princip undantag med hjälp av taggen, som kan anges under processen för att skapa ett avdelningens kontroll-konto. Huvud orsaken är att för varje avdelningens kontroll-konto måste det skapas en hanterad resurs grupp och inom den här resurs gruppen, ett lagrings konto och ett EventHub-namnområde. Mer information hittar du i [Skapa katalog Portal](create-catalog-portal.md)

### <a name="visual-studio"></a>Visual Studio

Genom gången i den här artikeln används Visual Studio 2019. Procedurerna för Visual Studio 2013, 2015 eller 2017 skiljer sig något.

### <a name="azure-net-sdk"></a>SDK för Azure .NET

Ladda ned och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/) på datorn.

## <a name="create-an-application-in-azure-active-directory"></a>Skapa ett program i Azure Active Directory

Från avsnitten i *How to: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som kan komma åt resurser*, följer du anvisningarna för att utföra dessa uppgifter:

1. Skapa ett program i [skapa ett Azure Active Directory-program](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)som representerar det .NET-program som du skapar i den här självstudien. För inloggnings-URL kan du ange en låtsas-URL enligt artikeln (`https://contoso.org/exampleapp`).
2. I [Hämta värden för att logga in](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), hämtar du **program-ID** och **klient-ID** och noterar de här värdena som du använder senare i den här självstudien. 
3. I [certifikat och hemligheter](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options), Hämta **autentiseringsnyckel** och anteckna det här värdet som du använder senare i den här självstudien.
4. I [tilldela programmet till en roll](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)tilldelar du programmet rollen **deltagare** på prenumerations nivå så att programmet kan skapa data fabriker i prenumerationen.

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

Skapa sedan ett C# .NET-konsol program i Visual Studio:

1. Starta **Visual Studio**.
2. I fönstret Starta väljer du **skapa en ny projekt**  >  **konsol app (.NET Framework)**. .NET version 4.5.2 eller senare krävs.
3. Skriv **ADFv2QuickStart** i **projekt namn**.
4. Välj **Skapa** för att skapa projektet.

## <a name="install-nuget-packages"></a>Installera NuGet-paket

1. Välj **Verktyg** > **NuGet-pakethanteraren** > **Pakethanterarkonsolen**.
2. I fönstret **Package Manager-konsol** kör du följande kommandon för att installera paket. Mer information finns i [Microsoft. Azure. Management. avdelningens kontroll NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/).

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Skapa en avdelningens kontroll-klient

1. Öppna **Program.cs**, lägg till följande instruktioner för att lägga till referenser till namnområden.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Lägg till följande kod till **Main**-metoden som anger variablerna. Ersätt plats hållarna med dina egna värden. Om du vill ha en lista över Azure-regioner där avdelningens kontroll är tillgängligt kan du söka på **Azure avdelningens kontroll** och välja de regioner som intresserar dig på följande sida: [produkter tillgängliga per region](https://azure.microsoft.com/global-infrastructure/services/).

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. Lägg till följande kod i **main** -metoden som skapar en instans av klassen **PurviewManagementClient** . Du kan använda det här objektet för att skapa ett avdelningens kontroll-konto.

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Skapa ett avdelningens kontroll-konto

Lägg till följande kod i **main** -metoden som skapar ett **avdelningens kontroll-konto**. 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>Kör koden

Skapa och starta programmet och verifiera sedan körningen.

Konsolen skriver ut förloppet för att skapa avdelningens kontroll-konto.

### <a name="sample-output"></a>Exempelutdata

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verifiera utdata

Gå till sidan med **avdelningens kontroll-konton** i [Azure Portal](https://portal.azure.com) och kontrol lera kontot som skapats med ovanstående kod. 

## <a name="delete-purview-account"></a>Ta bort avdelningens kontroll-konto

Om du vill ta bort ett avdelningens kontroll-konto program mässigt lägger du till följande kodrader i programmet: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Kontrol lera om avdelningens kontroll konto namn är tillgängligt

Använd följande kod för att kontrol lera tillgängligheten för ett avdelningens kontroll-konto: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

Koden ovan med utskriften sant om namnet är tillgängligt och falskt om namnet inte är tillgängligt.


## <a name="next-steps"></a>Nästa steg

Koden i den här självstudien skapar ett avdelningens kontroll-konto, tar bort ett avdelningens kontroll-konto och söker efter namn tillgänglighet för avdelningens kontroll-konto. Nu kan du hämta .NET SDK och lära dig mer om andra resurs leverantörs åtgärder som du kan utföra för ett avdelningens kontroll-konto.

Gå vidare till nästa artikel om du vill lära dig hur du ger användare åtkomst till ditt Azure avdelningens kontroll-konto. 

> [!div class="nextstepaction"]
> [Lägg till användare till ditt Azure avdelningens kontroll-konto](catalog-permissions.md)
