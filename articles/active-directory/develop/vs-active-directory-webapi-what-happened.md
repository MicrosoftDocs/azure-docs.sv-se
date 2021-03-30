---
title: Ändringar som görs i WebAPI-projekt vid anslutning till Azure AD
description: Beskriver vad som händer med ditt WebAPI-projekt när du ansluter till Azure AD med hjälp av Visual Studio
author: ghogen
manager: jillfra
ms.workload: azure-vs
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: a070bee7a9fa836eeac7c739cf2757295533ad7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88165370"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Vad hände med mitt WebAPI-projekt (Visual Studio Azure Active Directory Connected service)

> [!div class="op_single_selector"]
> - [Kom igång](vs-active-directory-webapi-getting-started.md)
> - [Vad hände](vs-active-directory-webapi-what-happened.md)

Den här artikeln beskriver de exakta ändringar som gjorts i ASP.NET WebAPI, ASP.NET Single-Page program och ASP.NET Azure API-projekt när du lägger till [Azure Active Directory anslutna tjänsten med Visual Studio](vs-active-directory-add-connected-service.md). Gäller även för ASP.NET Azure Mobile Service-projekt i Visual Studio 2015.

Information om hur du arbetar med den anslutna tjänsten finns i [komma igång](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Tillagda referenser

Påverkar projekt filen *. NET-referenser) och `packages.config` (NuGet-referenser).

| Typ | Referens |
| --- | --- |
| Nettotid NuGet | Microsoft. OWIN |
| Nettotid NuGet | Microsoft.Owin.Host.SystemWeb |
| Nettotid NuGet | Microsoft. OWIN. Security |
| Nettotid NuGet | Microsoft. OWIN. Security. ActiveDirectory |
| Nettotid NuGet | Microsoft. OWIN. Security. JWT |
| Nettotid NuGet | Microsoft. OWIN. Security. OAuth |
| Nettotid NuGet | Owin |
| Nettotid NuGet | System. IdentityModel. tokens. JWT |

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
| .NET        | Microsoft. IdentityModel. clients. ActiveDirectory. WindowsForms<br>(Endast Visual Studio 2015) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Endast Visual Studio 2017: även följande post har lagts till under `<appSettings>` "

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- Element har lagts till `<dependentAssembly>` under `<runtime><assemblyBinding>` noden för `System.IdentityModel.Tokens.Jwt` .

- Om du har valt alternativet **Läs katalog data** lade du till följande konfigurations post under `<appSettings>` :

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Kod ändringar och tillägg

- Attributet har lagts `[Authorize]` till `Controllers/ValueController.cs` och andra befintliga kontrollanter.

- Har lagt till en start klass `App_Start/Startup.Auth.cs` för autentisering, som innehåller start logik för Azure AD-autentisering eller ändrat den. Om du har valt alternativet **Läs katalog data** innehåller den här filen även kod för att ta emot en OAuth-kod och utbyta den för en åtkomsttoken.

- (Endast Visual Studio 2015 med ASP.NET 4 app) Har tagits bort `App_Start/IdentityConfig.cs` och lagts till `Controllers/AccountController.cs` , `Models/IdentityModel.cs` och `Providers/ApplicationAuthProvider.cs` .

- Lade till `Connected Services/AzureAD/ConnectedService.json` (Visual studio 2017) eller `Service References/Azure AD/ConnectedService.json` (visual Studio 2015) som innehåller information som Visual Studio använder för att spåra tillägget av den anslutna tjänsten.

### <a name="file-backup-visual-studio-2015"></a>Fil säkerhets kopiering (Visual Studio 2015)

När du lägger till den anslutna tjänsten säkerhetskopierar Visual Studio 2015 ändrade och borttagna filer. Alla berörda filer sparas i mappen `Backup/AzureAD` . I Visual Studio 2017 skapas inga säkerhets kopior.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Ändringar i Azure

- Skapade ett Azure AD-program i den domän som du valde när du lade till den anslutna tjänsten.
- Uppdaterade appen för att inkludera behörigheten **läsa katalog data** om alternativet har valts.

[Läs mer om Azure Active Directory](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Nästa steg

- [Autentiserings scenarier för Azure Active Directory](./authentication-vs-authorization.md)
- [Lägga till inloggning med Microsoft till en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md)