---
title: Verifiera omfattningar och app-roller skyddad webb-API | Azure
titleSuffix: Microsoft identity platform
description: 'Kontrol lera att API: t endast anropas av program för användare som har rätt omfattningar och av daemon-appar som har rätt program roller.'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6d737f107e7a9b1476fdf86ac0320931ea137671
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94442913"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Skyddat webb-API: kontrol lera omfattningar och app-roller

I den här artikeln beskrivs hur du kan lägga till auktorisering i ditt webb-API. Det här skyddet säkerställer att API: et endast anropas av:

- Program åt användare som har rätt omfång.
- Daemon-appar som har rätt program roller.

> [!NOTE]
> Kodfragmenten i den här artikeln extraheras från följande kod exempel på GitHub:
>
> - [Stegvis självstudie för ASP.NET Core webb-API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [ASP.NET-webb-API-exempel](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

Om du vill skydda ett ASP.NET-eller ASP.NET Core-webb-API måste du lägga till `[Authorize]` attributet till något av följande objekt:

- Själva styrenheten om du vill att alla åtgärder för styrenheter ska skyddas
- Åtgärden individuell kontrollant för ditt API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Men det här skyddet räcker inte. Det garanterar endast att ASP.NET och ASP.NET Core validera token. Ditt API måste verifiera att token som används för att anropa API: et begärs med förväntade anspråk. Dessa anspråk måste särskilt verifieras:

- *Omfattningarna* om API: t anropas för en användares räkning.
- *App-rollerna* om API: et kan anropas från en daemon-app.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Verifiera omfattningarna i API: er som anropas för användarnas räkning

Om en klient app anropar ditt API för en användares räkning måste API: et begära en Bearer-token som har vissa omfång för API: et. Mer information finns i [kod konfiguration | Bearer-token](scenario-protected-web-api-app-configuration.md#bearer-token).

### <a name="net-core"></a>.NET Core

#### <a name="verify-the-scopes-on-each-controller-action"></a>Verifiera omfattningarna på varje kontroll enhets åtgärd

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope`Metoden gör något så här:

- Kontrol lera att det finns ett anspråk med namnet `http://schemas.microsoft.com/identity/claims/scope` eller `scp` .
- Kontrol lera att anspråket har ett värde som innehåller omfånget som förväntas av API: et.


#### <a name="verify-the-scopes-more-globally"></a>Verifiera omfattningarna mer globalt

Den rekommenderade metoden är att definiera detaljerade omfattningar för ditt webb-API och verifiera omfattningarna i varje kontroll enhets åtgärd. Det är dock också möjligt att verifiera omfattningarna på nivån för programmet eller en kontrollant med hjälp av ASP.NET Core. Mer information finns i [anspråksbaserad auktorisering](/aspnet/core/security/authorization/claims) i ASP.net Core-dokumentationen.

### <a name="net-mvc"></a>.NET MVC

För ASP.NET ersätter du bara `HttpContext.User` med och `ClaimsPrincipal.Current` ersätter anspråks typen `"http://schemas.microsoft.com/identity/claims/scope"` med `"scp"` . Se även kodfragmentet senare i den här artikeln.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verifiera app-roller i API: er som anropas av daemon-appar

Om ditt webb-API anropas av en [daemon-app](scenario-daemon-overview.md), bör appen kräva en program behörighet för ditt webb-API. Som det visas i [exponera program behörigheter (app-roller)](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles)exponerar ditt API sådana behörigheter. Ett exempel är `access_as_application` app-rollen.

Du måste nu ha ditt API för att verifiera att token som den tar emot innehåller `roles` anspråk och att detta anspråk har det förväntade värdet. Verifierings koden liknar den kod som verifierar delegerade behörigheter, förutom att styrenhets åtgärden testar för roller i stället för omfattningar:

### <a name="aspnet-core"></a>ASP.NET Core

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole`Metoden definieras i Microsoft. Identity. Web i [RolesRequiredHttpContextExtensions. cs](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs#L28).

### <a name="aspnet-mvc"></a>ASP.NET MVC

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Accepterar endast app-token om webb-API: t endast ska anropas av daemon-appar

Användare kan också använda roll anspråk i användar tilldelnings mönster, som du ser i gör så [här: Lägg till app-roller i programmet och ta emot dem i token](howto-add-app-roles-in-azure-ad-apps.md). Om rollerna är tilldelnings bara till båda kan du kontrol lera att appar loggar in som användare och användare loggar in som appar när du kontrollerar roller. Vi rekommenderar att du deklarerar olika roller för användare och appar för att förhindra denna förvirring.

Om du bara vill att daemon-appar ska anropa ditt webb-API lägger du till villkoret att token är en app-only-token när du validerar app-rollen.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Genom att kontrol lera det inverterade villkoret kan endast appar som loggar in en användare anropa ditt API.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [Flytta till produktion](scenario-protected-web-api-production.md).
