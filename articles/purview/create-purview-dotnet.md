---
title: Skapa Purview-konto med hjälp av .NET SDK
description: Skapa ett Azure Purview-konto med hjälp av .NET SDK.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: b3dc7bf8ac7650a7219c15a09a31d4dcf84a40bf
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587807"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Snabbstart: Skapa ett Purview-konto med hjälp av .NET SDK

Den här snabbstarten beskriver hur du använder .NET SDK för att skapa ett Azure Purview-konto 

> [!IMPORTANT]
> Azure Purview finns för närvarande i FÖRHANDSVERSION. De [kompletterande användningsvillkoren för förhandsversioner Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som ännu inte har släppts för allmän tillgänglighet.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* En egen [klientorganisation i Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Ditt konto måste ha behörighet att skapa resurser i prenumerationen

* Om du har **Azure Policy** blockera alla program från att skapa **lagringskonto** och **EventHub-namnområde** måste du göra ett principfel med hjälp av taggen , som kan anges när du skapar ett Purview-konto. Huvudorsaken är att för varje Purview-konto som skapas måste det skapa en hanterad resursgrupp och i den här resursgruppen ett lagringskonto och ett EventHub-namnområde. Mer information finns i [Skapa katalogportal](create-catalog-portal.md)

### <a name="visual-studio"></a>Visual Studio

Genomgången i den här artikeln använder Visual Studio 2019. Procedurerna för Visual Studio 2013, 2015 eller 2017 skiljer sig något.

### <a name="azure-net-sdk"></a>SDK för Azure .NET

Ladda ned och installera [Azure .NET SDK](https://azure.microsoft.com/downloads/) på datorn.

## <a name="create-an-application-in-azure-active-directory"></a>Skapa ett program i Azure Active Directory

I avsnitten i *Gör så här:* Använd portalen för att skapa ett Azure AD-program och tjänstens huvudnamn som kan komma åt resurser följer du anvisningarna för att utföra dessa uppgifter:

1. I [Skapa ett Azure Active Directory program](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)skapar du ett program som representerar det .NET-program som du skapar i den här självstudien. För inloggnings-URL kan du ange en låtsas-URL enligt artikeln (`https://contoso.org/exampleapp`).
2. I [Hämta värden för att logga in](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)hämtar du **program-ID** och klientorganisations-ID och noterar dessa värden som du använder senare i den här självstudien.  
3. I [Certifikat och hemligheter hämtar](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)du autentiseringsnyckeln och noterar det här värdet som du använder senare i den här självstudien. 
4. I [Tilldela programmet till en](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)roll tilldelar du programmet till rollen Deltagare på prenumerationsnivå så att programmet kan skapa datafabriker i prenumerationen. 

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

Skapa sedan ett C# .NET-konsolprogram i Visual Studio:

1. Starta **Visual Studio**.
2. I startfönstret väljer du **Skapa ett nytt projekt**  >  **konsolapp (.NET Framework).** .NET version 4.5.2 eller senare krävs.
3. I **Projektnamn** anger du **PurviewQuickStart**.
4. Välj **Skapa** för att skapa projektet.

## <a name="install-nuget-packages"></a>Installera NuGet-paket

1. Välj **Verktyg** > **NuGet-pakethanteraren** > **Pakethanterarkonsolen**.
2. I fönstret **Package Manager Console** kör du följande kommandon för att installera paket. Mer information finns i [Microsoft.Azure.Management.Purview NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/).

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Skapa en Purview-klient

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

2. Lägg till följande kod till **Main**-metoden som anger variablerna. Ersätt platshållarna med dina egna värden. Om du vill se en lista över Azure-regioner där Purview för närvarande är tillgängligt söker du i **Azure Purview** och väljer de regioner som intresserar dig på följande sida: Produkter som är tillgängliga [efter region.](https://azure.microsoft.com/global-infrastructure/services/)

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

3. Lägg till följande kod i **Main-metoden** som skapar en instans av **purviewManagementClient-klassen.** Du använder det här objektet för att skapa ett Purview-konto.

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

## <a name="create-a-purview-account"></a>Skapa ett Purview-konto

Lägg till följande kod i **Main-metoden** som skapar ett **Purview-konto**. 

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

Konsolen skriver ut förloppet för att skapa Purview-konto.

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

Gå till sidan **Purview accounts (Rensa** konton) [i Azure Portal](https://portal.azure.com) och verifiera kontot som skapats med koden ovan. 

## <a name="delete-purview-account"></a>Ta bort Purview-konto

Om du vill ta bort ett Purview-konto programmatiskt lägger du till följande kodrader i programmet: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Kontrollera om Purview-kontonamnet är tillgängligt

Använd följande kod för att kontrollera tillgängligheten för ett kontrollkonto: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

Koden ovan skriver ut "True" om namnet är tillgängligt och "False" om namnet inte är tillgängligt.


## <a name="next-steps"></a>Nästa steg

Koden i den här självstudien skapar ett konto för vy, tar bort ett konto som inte är tillgängligt och söker efter namntillgänglighet för ett konto. Nu kan du ladda ned .NET SDK och lära dig mer om andra resursprovideråtgärder som du kan utföra för ett Purview-konto.

Gå vidare till nästa artikel om du vill lära dig hur du ger användare åtkomst till ditt Azure Purview-konto. 

> [!div class="nextstepaction"]
> [Lägga till användare till ditt Azure Purview-konto](catalog-permissions.md)
