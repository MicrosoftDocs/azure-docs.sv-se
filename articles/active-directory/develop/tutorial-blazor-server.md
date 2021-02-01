---
title: Självstudie – Skapa en snabb server app som använder Microsoft Identity Platform för autentisering | Azure
titleSuffix: Microsoft identity platform
description: I den här självstudien konfigurerar du autentisering med hjälp av Microsoft Identity Platform i en snabb server app.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 5a631d9ae7a7d1792e3c4e4a2cbf8281e1168283
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226024"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>Självstudie: skapa en snabb server app som använder Microsoft Identity Platform för autentisering

I den här självstudien skapar du en snabb server app som loggar in användare och hämtar data från Microsoft Graph genom att använda Microsoft Identity Platform och registrera din app i Azure Active Directory (Azure AD).

Vi har också en själv studie kurs om [blixt WASM](tutorial-blazor-webassembly.md).

I de här självstudierna har du

> [!div class="checklist"]
> * Skapa en ny snabb server app som kon figurer ATS för att använda Azure Active Directory (Azure AD) för autentisering
> * Hantera både autentisering och auktorisering med Microsoft. Identity. Web
> * Hämta data från ett skyddat webb-API, Microsoft Graph

## <a name="prerequisites"></a>Förutsättningar

- [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- En Azure AD-klient där du kan registrera en app. Om du inte har åtkomst till en Azure AD-klient kan du skaffa en genom att registrera dig hos [Microsoft 365 Developer-programmet](https://developer.microsoft.com/microsoft-365/dev-program) eller genom att skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free).

## <a name="register-the-app-in-the-azure-portal"></a>Registrera appen i Azure Portal

Varje app som använder Azure Active Directory (Azure AD) för autentisering måste registreras med Azure AD. Följ instruktionerna i [Registrera ett program](quickstart-register-app.md) med följande tillägg:

- För **konto typer som stöds** väljer du **konton endast i den här organisations katalogen**.
- Lämna List rutan **omdirigerings-URI** inställd på **webben** och ange `https://localhost:5001/signin-oidc` . Standard porten för en app som körs på Kestrel är 5001. Om appen är tillgänglig på en annan port anger du Port numret i stället för `5001` .

Under **Hantera** väljer du **autentisering**  >  **implicit beviljande och hybrid flöden**. Välj **åtkomsttoken** och **ID-token** och välj sedan **Spara**.

Slutligen, eftersom appen anropar ett skyddat API (i det här fallet Microsoft Graph), behöver den en klient hemlighet för att verifiera identiteten när den begär en åtkomsttoken för att anropa detta API.

1. I samma app-registrering under **Hantera** väljer du **certifikat & hemligheter**.
2. Skapa en **ny klient hemlighet** som aldrig upphör att gälla.
3. Anteckna hemlighetens **värde** eftersom du kommer att använda det i nästa steg. Du kan inte komma åt den igen när du har navigerat bort från det här fönstret. Du kan dock återskapa den efter behov.

## <a name="create-the-app-using-the-net-cli"></a>Skapa appen med .NET CLI

Kör följande kommando för att ladda ned mallarna för Microsoft. identitet. Web, som vi använder i den här självstudien.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

Kör sedan följande kommando för att skapa programmet. Ersätt plats hållarna i kommandot med rätt information från appens översikts sida och kör kommandot i ett kommando gränssnitt. Den utmatnings plats som anges med `-o|--output` alternativet skapar en projektmapp om den inte finns och blir en del av appens namn.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Platshållare   | Azure Portal namn       | Exempel                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | Program-ID (klient) | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | Katalog-ID (klient)   | `e86c78e2-0000-0000-0000-918e0565a45e` |

Gå nu till din nya blixt app i redigeraren och Lägg till klient hemligheten i *appsettings.jspå* filen, och Ersätt texten "Secret-from-app-Registration".

```json
"ClientSecret": "secret-from-app-registration",
```

## <a name="test-the-app"></a>Testa appen

Nu kan du skapa och köra appen. När du kör den här appen måste du ange ramverket som ska köras med--Framework. I den här självstudien används SDK för .NET Core 3,1.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

I webbläsaren navigerar du till `https://localhost:5001` och loggar in med ett Azure AD-användarkonto för att se appen som körs.

## <a name="retrieving-data-from-microsoft-graph"></a>Hämtar data från Microsoft Graph

[Microsoft Graph](/graph/overview) erbjuder ett antal API: er som ger åtkomst till användarnas Microsoft 365 data. Genom att använda Microsoft Identity Platform som identitets leverantör för din app har du enklare åtkomst till den här informationen eftersom Microsoft Graph direkt stöder de token som utfärdats av Microsoft Identity Platform. I det här avsnittet lägger du till kod för att visa den inloggade användarens e-postmeddelanden på sidan "Hämta data" i programmet.

Innan du börjar kan du logga ut från din app eftersom du kommer att göra ändringar i de behörigheter som krävs, och din aktuella token fungerar inte. Om du inte redan har gjort det kör du appen igen och väljer **Logga ut** innan du uppdaterar koden nedan.

Nu ska du uppdatera appens registrering och kod för att hämta en användares e-post och visa meddelandena i appen. För att åstadkomma detta måste du först utöka appens registrerings behörigheter i Azure AD för att ge åtkomst till e-postdata. Sedan kan du lägga till kod i den blixt appen för att hämta och visa dessa data på en av sidorna.

1. I Azure Portal väljer du din app i **Appregistreringar**.
1. Under **Hantera**, Välj **API-behörigheter**.
1. Välj **Lägg till en behörighets**  >  **Microsoft Graph**.
1. Välj **delegerade behörigheter** och Sök sedan efter och välj **e-post. Läs** behörighet.
1. Välj **Lägg till behörigheter**.

I *appsettings.js* filen uppdaterar du koden så att den hämtar rätt token med rätt behörigheter. Lägg till "e-post. Read" efter omfattningen "User. Read" under "DownstreamAPI". Detta anger vilka omfattningar (eller behörigheter) som appen ska begära åtkomst till.

```json
"Scopes": "user.read mail.read"
```

Sedan uppdaterar du koden i filen *FetchData. kniv* för att hämta e-postdata i stället för standard informationen (slumpmässig). Ersätt koden i filen med följande:

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

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
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

Starta appen. Observera att du uppmanas att ange de nyligen tillagda behörigheterna, vilket indikerar att allt fungerar som förväntat. Nu, utöver grundläggande användar profil data, begär appen åtkomst till e-postdata.

När du har beviljat godkännande navigerar du till sidan "Hämta data" för att läsa e-post.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="Skärm bild av den sista appen. Den har en rubrik med texten Hej Nicholas och visar en lista med e-postmeddelanden som hör till Nicholas.":::

## <a name="next-steps"></a>Nästa steg

Läs om hur du anropar att skapa webbappar som loggar in användare i vår scenario serie med flera delar:

> [!div class="nextstepaction"]
> [Scenario: webb program som loggar in användare](scenario-web-app-sign-user-overview.md)
