---
title: Kom igång med Azure CDN-biblioteket för .NET | Microsoft Docs
description: Lär dig hur du skriver .NET-program för att hantera Azure CDN med Visual Studio.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: a812704c42a4da5ddf89fe6c5ba0c9a684047f75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88919496"
---
# <a name="get-started-with-the-azure-cdn-library-for-net"></a>Kom igång med Azure CDN-biblioteket för .NET
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
>
>

Du kan använda [Azure CDN-biblioteket för .net](/dotnet/api/overview/azure/cdn) för att automatisera skapandet och hanteringen av CDN-profiler och slut punkter.  Den här självstudien vägleder dig genom skapandet av ett enkelt .NET-konsol program som visar flera av de tillgängliga åtgärderna.  Den här självstudien är inte avsedd att beskriva alla aspekter av Azure CDNs biblioteket för .NET i detalj.

Du behöver Visual Studio 2015 för att slutföra den här kursen.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) är gratis tillgängligt för hämtning.

> [!TIP]
> Det [färdiga projektet från den här självstudien](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) är tillgängligt för hämtning på MSDN.
>
>

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Skapa ditt projekt och Lägg till NuGet-paket
Nu när vi har skapat en resurs grupp för våra CDN-profiler och gett vår Azure AD-program behörighet att hantera CDN-profiler och slut punkter inom gruppen kan vi börja skapa vårt program.

I Visual Studio 2015 klickar du på **Arkiv**, **nytt**, **projekt...** för att öppna dialog rutan nytt projekt.  Expandera **Visual C#** och välj sedan **Windows** i rutan till vänster.  Klicka på **konsol program** i rutan i mitten.  Namnge projektet och klicka sedan på **OK**.

![Nytt projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Vårt projekt kommer att använda vissa Azure-bibliotek som finns i NuGet-paket.  Nu ska vi lägga till dem i projektet.

1. Klicka på **verktyg** -menyn, **NuGet Package Manager** och sedan **Package Manager-konsolen**.

    ![Hantera NuGet-paket](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. Kör följande kommando i Package Manager-konsolen för att installera **Active Directory-autentiseringsbibliotek (ADAL)**:

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Kör följande för att installera **Azure CDN hanterings biblioteket**:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Direktiv, konstanter, huvudsakliga metoder och hjälp metoder
Låt oss få den grundläggande strukturen i vårt program skrivet.

1. Gå tillbaka till fliken program. CS och Ersätt `using` direktiven överst med följande:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. Vi måste definiera vissa konstanter som metoderna kommer att använda.  I `Program` -klassen, men före `Main` -metoden lägger du till följande.  Se till att ersätta plats hållarna, inklusive **&lt; vinkel paren tes &gt;**, med dina egna värden efter behov.

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Definiera även dessa två variabler på klass nivå.  Vi använder dem senare för att avgöra om vår profil och slut punkt redan finns.

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Ersätt `Main` metoden på följande sätt:

   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();

       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };

       ListProfilesAndEndpoints(cdn);

       // Create CDN Profile
       CreateCdnProfile(cdn);

       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);

       Console.WriteLine();

       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);

       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);

       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);

       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Några av våra andra metoder kommer att uppmana användaren att ange "Ja/Nej"-frågor.  Lägg till följande metod för att göra det lite enklare:

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Nu när den grundläggande strukturen i programmet är skriven bör vi skapa de metoder som anropas av `Main` metoden.

## <a name="authentication"></a>Autentisering
Innan vi kan använda Azure CDN hanterings biblioteket måste vi autentisera tjänstens huvud namn och skaffa en autentiseringstoken.  Den här metoden använder ADAL för att hämta token.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult =
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Om du använder individuell användarautentisering `GetAccessToken` ser metoden annorlunda ut.

> [!IMPORTANT]
> Använd bara det här kod exemplet om du väljer att ha individuell användarautentisering i stället för ett huvud namn för tjänsten.
>
>

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Se till att ersätta `<redirect URI>` med den omdirigerings-URI som du angav när du registrerade programmet i Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN-profiler och slut punkter
Nu är vi redo att utföra CDN-åtgärder.  Den första metoden är att visa en lista över alla profiler och slut punkter i vår resurs grupp, och om den hittar en matchning för profil-och slut punkts namnen som anges i våra konstanter, antecknar du detta för senare så att vi inte försöker skapa dubbletter.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Skapa CDN-profiler och slut punkter
Nu ska vi skapa en profil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

När profilen har skapats skapar vi en slut punkt.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> Exemplet ovan tilldelar slut punkten ett ursprung med namnet *contoso* med ett värdnamn `www.contoso.com` .  Du bör ändra detta så att det pekar på ditt eget ursprungs namn.
>
>

## <a name="purge-an-endpoint"></a>Rensa en slut punkt
Förutsatt att slut punkten har skapats kan en vanlig uppgift som vi vill utföra i programmet rensa innehållet i vår slut punkt.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> I exemplet ovan `/*` noterar strängen att jag vill rensa allt i roten för slut punkts Sök vägen.  Det motsvarar att kontrol lera **Rensa alla** i dialog rutan för Azure Portal rensa. I- `CreateCdnProfile` metoden skapade jag vår profil som en **Azure CDN från Verizon** -profilen med hjälp av koden `Sku = new Sku(SkuName.StandardVerizon)` , så det kommer att lyckas.  **Azure CDN från Akamai** -profiler stöder dock inte **Rensa alla**, så om jag använde en Akamai-profil för den här själv studie kursen skulle jag behöva inkludera vissa sökvägar för att rensa.
>
>

## <a name="delete-cdn-profiles-and-endpoints"></a>Ta bort CDN-profiler och slut punkter
De sista metoderna tar bort vår slut punkt och profil.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Köra programmet
Nu kan vi kompilera och köra programmet genom att klicka på **Start** -knappen i Visual Studio.

![Program som körs](./media/cdn-app-dev-net/cdn-program-running-1.png)

När programmet når ovanstående prompt ska du kunna återgå till resurs gruppen i Azure Portal och se att profilen har skapats.

![Lyckades!](./media/cdn-app-dev-net/cdn-success.png)

Vi kan sedan bekräfta prompterna för att köra resten av programmet.

![Programmet slutförs](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Nästa steg
[Hämta exemplet](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)för att se det slutförda projektet från den här genom gången.

Om du vill ha mer dokumentation om Azure CDN hanterings bibliotek för .NET kan du läsa [referens på MSDN](/dotnet/api/overview/azure/cdn).

Hantera dina CDN-resurser med [PowerShell](cdn-manage-powershell.md).
