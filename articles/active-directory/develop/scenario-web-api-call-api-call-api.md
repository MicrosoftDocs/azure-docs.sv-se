---
title: 'Webb-API som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar ett webb-API som anropar webb-API: er.'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f4dfd2c7f9dbdd111f70a5dd5a648c11eacbf7b0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038551"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Ett webb-API som anropar webb-API: er: anropa ett API

När du har en token kan du anropa ett skyddat webb-API. Du anropar vanligt vis de underordnade API: erna från styrenheten eller sidorna i webb-API: et.

## <a name="controller-code"></a>Kod för styrenhet

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

När du använder *Microsoft. Identity. Web* har du tre användnings scenarier:

- [Alternativ 1: anropa Microsoft Graph med SDK: n](#option-1-call-microsoft-graph-with-the-sdk)
- [Alternativ 2: anropa ett underordnat webb-API med hjälp av klassen](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Alternativ 3: anropa ett underordnat webb-API utan hjälp klassen](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Alternativ 1: anropa Microsoft Graph med SDK: n

I det här scenariot har du lagt till `.AddMicrosoftGraph()` i *startup.cs* som det anges i [kod konfigurationen](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph)och du kan mata in direkt `GraphServiceClient` i din styrenhet eller sidlayout för användning i åtgärderna. I följande exempel på kniv-sidan visas en bild av den inloggade användaren.

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

I det här scenariot har du lagt till `.AddDownstreamWebApi()` i *startup.cs* som det anges i [kod konfigurationen](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph)och du kan mata in en `IDownstreamWebApi` tjänst direkt i din styrenhet eller sidlayout och använda den i åtgärder:

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
```

`CallWebApiForUserAsync`Metoden har också starkt skrivna generiska åsidosättningar som gör att du kan ta emot ett objekt direkt. Följande metod tog till exempel emot en `Todo` instans, som är en starkt skriven representation av den JSON som returneras av webb-API: et.

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

Om du har bestämt dig för att hämta en token manuellt med hjälp av `ITokenAcquisition` tjänsten måste du nu använda token. I så fall fortsätter följande kod exempel koden som visas i [ett webb-API som anropar webb-API: er: Hämta en token för appen](scenario-web-api-call-api-acquire-token.md). Koden anropas i API-styrenhetens åtgärder. Den anropar ett underordnat API med namnet *ToDoList*.

 När du har skaffat token ska du använda den som en Bearer-token för att anropa det underordnade API: et.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

Följande kod fortsätter med exempel koden som visas i [ett webb-API som anropar webb-API: er: Hämta en token för appen](scenario-web-api-call-api-acquire-token.md). Koden anropas i API-styrenhetens åtgärder. Den anropar det underordnade API MS-diagrammet.

När du har skaffat token ska du använda den som en Bearer-token för att anropa det underordnade API: et.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Ett exempel som demonstrerar det här flödet med MSAL python finns i [MS-Identity-python-on-of](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of).

---

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [Flytta till produktion](scenario-web-api-call-api-production.md).
