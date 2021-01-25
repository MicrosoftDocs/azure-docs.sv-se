---
title: Anropa ett webb-API från en webbapp | Azure
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar en webbapp som anropar webb-API: er (som anropar ett skyddat webb-API)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b294a56a523adaa2629a5d1e72a7ccef532956e0
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753289"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>En webbapp som anropar webb-API: er: anropa ett webb-API

Nu när du har en token kan du anropa ett skyddat webb-API. Du anropar vanligt vis ett underordnat API från kontrollanten eller sidorna i din webbapp.

## <a name="call-a-protected-web-api"></a>Anropa ett skyddat webb-API

Att anropa ett skyddat webb-API beror på vilket språk och ramverk du väljer:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

När du använder *Microsoft. Identity. Web* har du tre användnings alternativ för att anropa ett API:

- [Alternativ 1: anropa Microsoft Graph med Microsoft Graph SDK](#option-1-call-microsoft-graph-with-the-sdk)
- [Alternativ 2: anropa ett underordnat webb-API med hjälp av klassen](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Alternativ 3: anropa ett underordnat webb-API utan hjälp klassen](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Alternativ 1: anropa Microsoft Graph med SDK: n

Du vill anropa Microsoft Graph. I det här scenariot har du lagt till `AddMicrosoftGraph` i *startup.cs* som det anges i [kod konfigurationen](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph)och du kan mata in direkt `GraphServiceClient` i din styrenhet eller sidlayout för användning i åtgärderna. I följande exempel på kniv-sidan visas en bild av den inloggade användaren.

```CSharp
[Authorize]
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
 private readonly GraphServiceClient _graphServiceClient;

 public IndexModel(GraphServiceClient graphServiceClient)
 {
    _graphServiceClient = graphServiceClient;
 }

 public async Task OnGet()
 {
  var user = await _graphServiceClient.Me.Request().GetAsync();
  try
  {
   using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
   {
    byte[] photoByte = ((MemoryStream)photoStream).ToArray();
    ViewData["photo"] = Convert.ToBase64String(photoByte);
   }
   ViewData["name"] = user.DisplayName;
  }
  catch (Exception)
  {
   ViewData["photo"] = null;
  }
 }
}
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>Alternativ 2: anropa ett underordnat webb-API med hjälp av klassen

Du vill anropa ett annat webb-API än Microsoft Graph. I så fall har du lagt till `AddDownstreamWebApi` i *startup.cs* som det anges i [kod konfigurationen](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph), och du kan mata in en `IDownstreamWebApi` tjänst direkt i din styrenhet eller sidlayout och använda den i åtgärder:

```CSharp
[Authorize]
[AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
public class TodoListController : Controller
{
  private IDownstreamWebApi _downstreamWebApi;
  private const string ServiceName = "TodoList";

  public TodoListController(IDownstreamWebApi downstreamWebApi)
  {
    _downstreamWebApi = downstreamWebApi;
  }

  public async Task<ActionResult> Details(int id)
  {
    var value = await _downstreamWebApi.CallWebApiForUserAsync(
      ServiceName,
      options =>
      {
        options.RelativePath = $"me";
      });
      return View(value);
  }
}
```

`CallWebApiForUserAsync`Dessutom har starkt skrivna allmänna åsidosättningar som gör att du kan ta emot ett objekt direkt. Följande metod tar till exempel emot en `Todo` instans, som är en starkt skriven representation av den JSON som returneras av webb-API: et.

```CSharp
    // GET: TodoList/Details/5
    public async Task<ActionResult> Details(int id)
    {
        var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
            ServiceName,
            null,
            options =>
            {
                options.HttpMethod = HttpMethod.Get;
                options.RelativePath = $"api/todolist/{id}";
            });
        return View(value);
    }
   ```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>Alternativ 3: anropa ett underordnat webb-API utan hjälp klassen

Du har bestämt dig för att hämta en token manuellt med `ITokenAcquisition` tjänsten och du måste nu använda token. I så fall fortsätter följande kod exempel koden som visas i [en webbapp som anropar webb-API: er: Hämta en token för appen](scenario-web-app-call-api-acquire-token.md). Koden anropas i webbappens åtgärder.

När du har skaffat token ska du använda den som en Bearer-token för att anropa det underordnade API: et, i det här fallet Microsoft Graph.

 ```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```
> [!NOTE]
> Du kan använda samma princip för att anropa alla webb-API: er.
>
> De flesta Azure-webb-API: er tillhandahåller ett SDK som fören klar anropet till API: t, som är fallet för Microsoft Graph. Se till exempel [skapa ett webb program som ger åtkomst till Blob Storage med Azure AD](../../storage/common/storage-auth-aad-app.md?tabs=dotnet&toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för ett exempel på en webbapp som använder Microsoft. Identity. Web och använder Azure Storage SDK.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [Flytta till produktion](scenario-web-app-call-api-production.md).