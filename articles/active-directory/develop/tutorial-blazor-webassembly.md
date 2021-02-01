---
title: Självstudie – logga in användare och anropa ett skyddat API från en Blixts WebAssembly-app
titleSuffix: Microsoft identity platform
description: I den här självstudien loggar du in användare och anropar ett skyddat API med hjälp av Microsoft Identity Platform i en WASM-app.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 30b7f1054f7bfee8dc58638791f0b8a424de92a4
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226429"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>Självstudie: Logga in användare och anropa ett skyddat API från en blixt WebAssembly-app

I den här självstudien skapar du en Blixts WebAssembly-app som loggar in användare och hämtar data från Microsoft Graph genom att använda Microsoft Identity Platform och registrera din app i Azure Active Directory (Azure AD). 

I de här självstudierna har du

> [!div class="checklist"]
>
> * Skapa en ny konfigurations program för Windows-webbsammansättning som kon figurer ATS för att använda Azure Active Directory (Azure AD) för [autentisering och auktorisering](authentication-vs-authorization.md) med hjälp av Microsoft Identity Platform
> * Hämta data från ett skyddat webb-API, i det här fallet [Microsoft Graph](/graph/overview)

I den här självstudien används .NET Core 3,1. .NET-dokumenten innehåller instruktioner om [hur du skyddar en Blixts WebAssembly-app](/aspnet/core/blazor/security/webassembly/graph-api) med ASP.net Core 5,0. 

Vi har också en [själv studie kurs för](tutorial-blazor-server.md)den här guiden. 

## <a name="prerequisites"></a>Förutsättningar

* [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* En Azure AD-klient där du kan registrera en app. Om du inte har åtkomst till en Azure AD-klient kan du skaffa en genom att registrera dig hos [Microsoft 365 Developer-programmet](https://developer.microsoft.com/microsoft-365/dev-program) eller genom att skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free).

## <a name="register-the-app-in-the-azure-portal"></a>Registrera appen i Azure Portal

Varje app som använder Azure Active Directory (Azure AD) för autentisering måste registreras med Azure AD. Följ instruktionerna i [Registrera ett program](quickstart-register-app.md) med följande specifikationer:

- För **konto typer som stöds** väljer du **konton endast i den här organisations katalogen**.
- Lämna List rutan **omdirigerings-URI** inställd på **webben** och ange `https://localhost:5001/authentication/login-callback` . Standard porten för en app som körs på Kestrel är 5001. Om appen är tillgänglig på en annan port anger du Port numret i stället för `5001` .

När du har registrerat, under **Hantera**, väljer du **autentisering**  >  **implicit beviljande och hybrid flöden**. Välj **åtkomsttoken** och **ID-token** och välj sedan **Spara**.

## <a name="create-the-app-using-the-net-core-cli"></a>Skapa appen med hjälp av .NET Core CLI

Du behöver de senaste blixt mallarna för att skapa appen. Du kan installera dem för .NET Core CLI med följande kommando:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.1
```

Kör sedan följande kommando för att skapa programmet. Ersätt plats hållarna i kommandot med rätt information från appens översikts sida och kör kommandot i ett kommando gränssnitt. Den utmatnings plats som anges med `-o|--output` alternativet skapar en projektmapp om den inte finns och blir en del av appens namn.

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Platshållare   | Azure Portal namn       | Exempel                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | Program-ID (klient) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | Katalog-ID (klient)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>Testa appen

Nu kan du skapa och köra appen. När du kör den här appen måste du ange ramverket som ska köras med--Framework. I den här självstudien används .NET standard 2,1, men mallen stöder även andra ramverk.

```dotnetcli
dotnet run --framework netstandard2.1
```

I webbläsaren navigerar du till `https://localhost:5001` och loggar in med ett Azure AD-användarkonto för att se appen som kör och loggar in användare i med Microsoft Identity Platform.

Komponenterna i den här mallen som aktiverar inloggningar med Azure AD med hjälp av Microsoft Identity Platform beskrivs i [ASP.net-dokumentet i det här avsnittet](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package).

## <a name="retrieving-data-from-a-protected-api-microsoft-graph"></a>Hämta data från ett skyddat API (Microsoft Graph)

[Microsoft Graph](/graph/overview) innehåller API: er som ger åtkomst till Microsoft 365 data för dina användare och som har stöd för de token som utfärdats av Microsoft Identity Platform, vilket gör det till ett lämpligt skydds-API att använda som exempel. I det här avsnittet ska du lägga till kod för att anropa Microsoft Graph och Visa användarens e-postmeddelanden på sidan "Hämta data" på programmets sida.

Det här avsnittet är skrivet med en gemensam metod för att anropa en skyddad API med hjälp av en namngiven klient. Samma metod kan användas för andra skyddade API: er som du vill anropa. Men om du planerar att anropa Microsoft Graph från ditt program kan du använda Graph SDK för att minska standard. .NET-dokumenten innehåller instruktioner om [hur du använder Graph SDK](/aspnet/core/blazor/security/webassembly/graph-api?view=aspnetcore-5.0&preserve-view=true).

Innan du börjar kan du logga ut från din app eftersom du kommer att göra ändringar i de behörigheter som krävs, och din aktuella token fungerar inte. Om du inte redan har gjort det kör du appen igen och väljer **Logga ut** innan du uppdaterar koden nedan.

Nu ska du uppdatera appens registrering och kod för att hämta en användares e-post och visa meddelandena i appen.

Lägg först till `Mail.Read` API-behörighet till appens registrering så att Azure AD är medveten om att appen kommer att begära åtkomst till användarnas e-post.

1. I Azure Portal väljer du din app i **Appregistreringar**.
1. Under **Hantera**, Välj **API-behörigheter**.
1. Välj **Lägg till en behörighets**  >  **Microsoft Graph**.
1. Välj **delegerade behörigheter** och Sök sedan efter och välj **e-post. Läs** behörighet.
1. Välj **Lägg till behörigheter**.

Lägg sedan till följande i projektets *. CSPROJ* -fil i netstandard 2.1- **ItemGroup**. Det gör att du kan skapa anpassade HttpClient i nästa steg.

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

Ändra sedan koden enligt anvisningarna i följande steg. De här ändringarna lägger till [åtkomsttoken](access-tokens.md) till de utgående förfrågningar som skickas till Microsoft Graph-API: et. Det här mönstret beskrivs i detalj i [ASP.net Core blixt WebAssembly ytterligare säkerhets scenarier](/aspnet/core/blazor/security/webassembly/additional-scenarios).

Börja med att skapa en ny fil med namnet *GraphAuthorizationMessageHandler.cs* med följande kod. Den här hanteraren kommer att vara användare att lägga till en åtkomsttoken för- `User.Read` och- `Mail.Read` omfattningarna till utgående begär anden till Microsoft Graph-API: et.

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

Ersätt sedan innehållet i- `Main` metoden i *program.cs* med följande kod. Den här koden använder den nya `GraphAPIAuthorizationMessageHandler` och lägger till `User.Read` och `Mail.Read` som standard omfattningar som appen begär när användaren loggar in första gången.

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

Ersätt slutligen innehållet på sidan *FetchData. kniv* med följande kod. Den här koden hämtar användar-e-postdata från Microsoft Graph-API och visar dem som en lista. I `OnInitializedAsync` skapas den nya `HttpClient` som använder rätt åtkomsttoken och används för att skicka begäran till Microsoft Graph API.

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

Starta nu appen igen. Du ser att du uppmanas att ge appen åtkomst till att läsa din e-post. Detta förväntas när en app begär `Mail.Read` omfånget.

När du har beviljat godkännande navigerar du till sidan "Hämta data" för att läsa e-post.

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="Skärm bild av den sista appen. Den har en rubrik med texten Hej Nicholas och visar en lista med e-postmeddelanden som hör till Nicholas.":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Metod tips och rekommendationer för Microsoft Identity Platform](./identity-platform-integration-checklist.md)