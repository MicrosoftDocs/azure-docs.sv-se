---
title: Ändringar som gjorts i ett MVC-projekt när du ansluter till Azure AD
description: Beskriver vad som händer med ditt MVC-projekt när du ansluter till Azure AD med hjälp av Visual Studio Connected Services
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: ab72512b2e5d915dbd4b8a3d7d8446a5932eef9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88165455"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Vad hände med mitt MVC-projekt (Visual Studio Azure Active Directory Connected service)?

> [!div class="op_single_selector"]
> - [Kom igång](vs-active-directory-dotnet-getting-started.md)
> - [Vad hände](vs-active-directory-dotnet-what-happened.md)

Den här artikeln beskriver de exakta ändringar som gjorts i ett ASP.NET MVC-projekt när du lägger till den [Azure Active Directory anslutna tjänsten med Visual Studio](vs-active-directory-add-connected-service.md).

Information om hur du arbetar med den anslutna tjänsten finns i [komma igång](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Tillagda referenser

Påverkar projekt filen (*. NET-referenser) och `packages.config` (NuGet-referenser).

| Typ | Referens |
| --- | --- |
| Nettotid NuGet | Microsoft. IdentityModel. Protocol. Extensions |
| Nettotid NuGet | Microsoft. OWIN |
| Nettotid NuGet | Microsoft.Owin.Host.SystemWeb |
| Nettotid NuGet | Microsoft. OWIN. Security |
| Nettotid NuGet | Microsoft.Owin.Security.Cookies |
| Nettotid NuGet | Microsoft.Owin.Security.OpenIdConnect |
| Nettotid NuGet | Owin |
| .NET        | System. IdentityModel |
| Nettotid NuGet | System. IdentityModel. tokens. JWT |
| .NET        | System.Runtime.Serialization |

Ytterligare referenser om du har valt alternativet **Läs katalog data** :

| Typ | Referens |
| --- | --- |
| Nettotid NuGet | EntityFramework |
| .NET        | EntityFramework. SqlServer (endast Visual Studio 2015) |
| Nettotid NuGet | Microsoft. Azure. ActiveDirectory. GraphClient |
| Nettotid NuGet | Microsoft. data. EDM |
| Nettotid NuGet | Microsoft. data. OData |
| Nettotid NuGet | Microsoft. data. Services. client |
| Nettotid NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms (endast Visual Studio 2015) |
| Nettotid NuGet | System. spatial |

Följande referenser tas bort (endast ASP.NET 4-projekt, som i Visual Studio 2015):

| Typ | Referens |
| --- | --- |
| Nettotid NuGet | Microsoft. ASPNET. Identity. Core |
| Nettotid NuGet | Microsoft. ASPNET. Identity. EntityFramework |
| Nettotid NuGet | Microsoft. ASPNET. Identity. OWIN |

## <a name="project-file-changes"></a>Projekt fil ändringar

- Ange `IISExpressSSLPort` en distinkt siffra som egenskap.
- Ange egenskapen `WebProject_DirectoryAccessLevelKey` till 0, eller 1 om du har valt alternativet **Läs katalog data** .
- Ange egenskapen `IISUrl` till `https://localhost:<port>/` där `<port>` matchar `IISExpressSSLPort` värdet.

## <a name="webconfig-or-appconfig-changes"></a>web.config eller app.config ändringar

- Följande konfigurations poster har lagts till:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Element har lagts till `<dependentAssembly>` under `<runtime><assemblyBinding>` noden för `System.IdentityModel.Tokens.Jwt` och `Microsoft.IdentityModel.Protocol.Extensions` .

Ytterligare ändringar om du har valt alternativet **Läs katalog data** :

- Följande konfigurations post har lagts till under `<appSettings>` :

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Följande element har lagts till under `<configuration>` ; värdena för projekt-MDF-filen och projekt-Catalog-ID: t varierar:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- Element har lagts till `<dependentAssembly>` under `<runtime><assemblyBinding>` noden för `Microsoft.Data.Services.Client` , `Microsoft.Data.Edm` , och `Microsoft.Data.OData` .

## <a name="code-changes-and-additions"></a>Kod ändringar och tillägg

- Attributet har lagts `[Authorize]` till `Controllers/HomeController.cs` och andra befintliga kontrollanter.

- En start klass för autentisering har lagts till, `App_Start/Startup.Auth.cs` som innehåller start logik för Azure AD-autentisering. Om du har valt alternativet **Läs katalog data** innehåller den här filen även kod för att ta emot en OAuth-kod och utbyta den för en åtkomsttoken.

- En styrenhets klass har lagts till, `Controllers/AccountController.cs` som innehåller `SignIn` och- `SignOut` metoder.

- Har lagt till en partiell vy, `Views/Shared/_LoginPartial.cshtml` som innehåller en åtgärds länk för `SignIn` och `SignOut` .

- Lade till en partiell vy, `Views/Account/SignoutCallback.cshtml` som innehåller HTML för inloggnings gränssnitt.

- Har uppdaterat `Startup.Configuration` metoden för att inkludera ett anrop till `ConfigureAuth(app)` om klassen redan fanns, annars lade till en `Startup` klass som innehåller anrop till metoden.

- Lade till `Connected Services/AzureAD/ConnectedService.json` (Visual studio 2017) eller `Service References/Azure AD/ConnectedService.json` (visual Studio 2015) som innehåller information som Visual Studio använder för att spåra tillägget av den anslutna tjänsten.

- Om du har valt alternativet **Läs katalog data** lägger till `Models/ADALTokenCache.cs` och `Models/ApplicationDbContext.cs` stöder cachelagring av token. Även lagt till ytterligare en kontrollant och vy för att illustrera åtkomst till användar profils information med hjälp av Azure Graph API: er: `Controllers/UserProfileController.cs` , `Views/UserProfile/Index.cshtml` och `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Fil säkerhets kopiering (Visual Studio 2015)

När du lägger till den anslutna tjänsten säkerhetskopierar Visual Studio 2015 ändrade och borttagna filer. Alla berörda filer sparas i mappen `Backup/AzureAD` . I Visual Studio 2017 och senare skapas inga säkerhets kopior.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Ändringar i Azure

- Skapade ett Azure AD-program i den domän som du valde när du lade till den anslutna tjänsten.
- Uppdaterade appen för att inkludera behörigheten **läsa katalog data** om alternativet har valts.

[Läs mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Nästa steg

- [Autentiserings scenarier för Azure Active Directory](./authentication-vs-authorization.md)
- [Lägga till inloggning med Microsoft till en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)