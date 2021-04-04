---
title: 'Självstudie: autentisera användare E2E'
description: 'Lär dig hur du använder App Service autentisering och auktorisering för att skydda dina App Service appar från slut punkt till slut punkt, inklusive åtkomst till fjärr-API: er.'
keywords: app service, azure app service, authN, authZ, säker, säkerhet, flera nivåer, azure active directory, azure ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 8c3fca6cf7782c3aaac91388a8f8395e288f5ea5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96558750"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>Självstudie: Autentisera och auktorisera användare från slutpunkt till slutpunkt i Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst. I App Service finns dessutom funktioner för [användarautentisering och auktorisering](overview-authentication-authorization.md). I den här kursen visar vi hur du skyddar appar med App Service-autentisering och auktorisering. Den använder en ASP.NET Core-app med en Angular.js klient del som exempel. App Service-autentisering och autentisering stöder alla språkkörningar. Du kan lära dig hur du använder det med det språk du föredrar genom att följa självstudiekursen.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) ger en mycket skalbar och automatisk korrigering av webb värd tjänst som använder Linux-operativsystemet. I App Service finns dessutom funktioner för [användarautentisering och auktorisering](overview-authentication-authorization.md). I den här kursen visar vi hur du skyddar appar med App Service-autentisering och auktorisering. Den använder en ASP.NET Core-app med en Angular.js klient del som exempel. App Service-autentisering och autentisering stöder alla språkkörningar. Du kan lära dig hur du använder det med det språk du föredrar genom att följa självstudiekursen.

::: zone-end

![Enkel autentisering och auktorisering](./media/tutorial-auth-aad/simple-auth.png)

Här ser du också hur du skyddar en app med flera nivåer genom att öppna ett skyddat server-API åt den autentiserade användaren, från både [serverkoden](#call-api-securely-from-server-code) och [webbläsarkoden](#call-api-securely-from-browser-code).

![Avancerad autentisering och auktorisering](./media/tutorial-auth-aad/advanced-auth.png)

Det här är endast några av de möjliga scenarierna för autentisering och auktorisering i App Service. 

Här är en mer utförlig lista över saker du lär dig under självstudiekursen:

> [!div class="checklist"]
> * Aktivera inbyggd autentisering och auktorisering
> * Skydda appar mot oautentiserade begäranden
> * Använda Azure Active Directory som identitetsleverantör
> * Få åtkomst till en fjärrapp å den inloggade användarens vägnar
> * Säkra tjänst-till-tjänst-anrop med tokenautentisering
> * Använda åtkomsttoken från serverkod
> * Använd åtkomsttoken från klientkod (webbläsare)

Du kan följa stegen i den här självstudien i macOS, Linux och Windows.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- <a href="https://git-scm.com/" target="_blank">Installera Git</a>
- <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">Installera den senaste .NET Core 3,1 SDK</a>
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-local-net-core-app"></a>Skapa en lokal .NET Core-app

I det här steget konfigurerar du det lokala .NET Core-projektet. Du kan använda samma projekt för att distribuera en API-app på serversidan och en webbapp på klientsidan.

### <a name="clone-and-run-the-sample-application"></a>Klona och kör exempelprogrammet

Kör följande kommandon för att klona exempellagringsplatsen och köra den.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Öppna `http://localhost:5000` och prova att lägga till, redigera och ta bort att göra-objekt. 

![ASP.NET Core-API som körs lokalt](./media/tutorial-auth-aad/local-run.png)

Du kan när som helst stoppa ASP.NET Core genom att trycka på `Ctrl+C` i terminalen.

## <a name="deploy-apps-to-azure"></a>Distribuera appar till Azure

I det här steget distribuerar du projektet till två App Service-appar. Den ena är en klientdelsapp och den andra är en serverdelsapp.

### <a name="configure-a-deployment-user"></a>Konfigurera en distributionsanvändare

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Skapa Azure-resurser

::: zone pivot="platform-windows"  

I Cloud Shell kör du följande kommandon för att skapa två Windows-webbappar. Ersätt _\<front-end-app-name>_ och _\<back-end-app-name>_ med två globalt unika app-namn (giltiga tecken är `a-z` , `0-9` och `-` ). Mer information om varje kommando finns i [RESTful-API med CORS i Azure App Service](app-service-web-tutorial-rest-api.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

::: zone pivot="platform-linux"

Kör följande kommandon i Cloud Shell för att skapa två webbappar. Ersätt _\<front-end-app-name>_ och _\<back-end-app-name>_ med två globalt unika app-namn (giltiga tecken är `a-z` , `0-9` och `-` ). Mer information om varje kommando finns i [skapa en .net Core-app i Azure App Service](quickstart-dotnetcore.md).

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

> [!NOTE]
> Spara webbadresserna till Git-fjärrobjekten för klientdels- och serverdelsapparna. De visas i utdata från `az webapp create`.
>

### <a name="push-to-azure-from-git"></a>Skicka till Azure från Git

Åter i det _lokala terminalfönstret_ kör du följande Git-kommandon för att distribuera till serverdelsappen. Ersätt _\<deploymentLocalGitUrl-of-back-end-app>_ med URL: en för den git-fjärrdatabas som du sparade från [Skapa Azure-resurser](#create-azure-resources). När du uppmanas att ange autentiseringsuppgifter av Git Autentiseringshanteraren kontrollerar du att du anger [autentiseringsuppgifterna för distributionen](deploy-configure-credentials.md), inte de autentiseringsuppgifter som du använder för att logga in på Azure Portal.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

Kör följande Git-kommandon i det lokala terminalfönstret för att distribuera samma kod till klientdelsappen. Ersätt _\<deploymentLocalGitUrl-of-front-end-app>_ med URL: en för den git-fjärrdatabas som du sparade från [Skapa Azure-resurser](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>Bläddra till apparna

Navigera till följande URL:er i en webbläsare och se hur de två apparna fungerar.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

:::image type="content" source="./media/tutorial-auth-aad/azure-run.png" alt-text="Skärm bild av ett Azure App Service REST API-exempel i ett webbläsarfönster som visar en att göra-lista-app.":::

> [!NOTE]
> Om appen startar om noterade du kanske att nya data har tagits bort. Det här är avsiktligt eftersom ASP.NET Core-exempelappen använder en minnesintern databas.
>
>

## <a name="call-back-end-api-from-front-end"></a>Anropa serverdels-API från klientsidan

I det här steget använder du serverkoden för klientdelsappen för att komma åt serverdels-API:t. Du aktiverar senare autentiserad åtkomst från klientdelen till serverdelen.

### <a name="modify-front-end-code"></a>Ändra klientdelskod

Öppna _Controllers/TodoController.cs_ på den lokala lagringsplatsen. I början av `TodoController` klassen lägger du till följande rader och ersätter _\<back-end-app-name>_ med namnet på din server dels app:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

Hitta metoden som är dekorerad `[HttpGet]` och Ersätt koden inuti klammerparenteserna med:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

Den första raden gör ett `GET /api/Todo`-anrop till serverdels-API-appen.

Sedan söker du efter den metod som är dekorerad `[HttpGet("{id}")]` och ersätter koden inuti klammerparenteserna med:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
return Content(data, "application/json");
```

Den första raden gör ett `GET /api/Todo/{id}`-anrop till serverdels-API-appen.

Sedan söker du efter den metod som är dekorerad `[HttpPost]` och ersätter koden inuti klammerparenteserna med:

```cs
var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
var data = await response.Content.ReadAsStringAsync();
return Content(data, "application/json");
```

Den första raden gör ett `POST /api/Todo`-anrop till serverdels-API-appen.

Sedan söker du efter den metod som är dekorerad `[HttpPut("{id}")]` och ersätter koden inuti klammerparenteserna med:

```cs
var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
return new NoContentResult();
```

Den första raden gör ett `PUT /api/Todo/{id}`-anrop till serverdels-API-appen.

Sedan söker du efter den metod som är dekorerad `[HttpDelete("{id}")]` och ersätter koden inuti klammerparenteserna med:

```cs
var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
return new NoContentResult();
```

Den första raden gör ett `DELETE /api/Todo/{id}`-anrop till serverdels-API-appen.

Spara alla ändringar. I det lokala terminalfönstret distribuerar du ändringarna till klientdelsappen med följande Git-kommandon:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Kontrollera ändringarna

Öppna `http://<front-end-app-name>.azurewebsites.net` och lägg till några objekt, exempelvis `from front end 1` och `from front end 2`.

Navigera till `http://<back-end-app-name>.azurewebsites.net` för att visa de objekt som lades till från klientdelsappen. Lägg även till några objekt, som `from back end 1` och `from back end 2`, och uppdatera sedan klientdelsappen för att se om ändringarna visas.

:::image type="content" source="./media/tutorial-auth-aad/remote-api-call-run.png" alt-text="Skärm bild av ett Azure App Service REST API-exempel i ett webbläsarfönster som visar en att göra-lista-app med objekt som har lagts till från frontend-appen.":::

## <a name="configure-auth"></a>Konfigurera autentisering

I det här steget aktiverar du autentisering och auktorisering för två appar. Du kan också konfigurera klientdelsappen och generera en åtkomsttoken som du kan använda till att göra autentiserade anrop till serverdelsappen.

Du använder Azure Active Directory som identitetsleverantör. Mer information finns i [Konfigurera Azure Active Directory-autentisering för App Services-appen](configure-authentication-provider-aad.md).

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Aktivera autentisering och auktorisering för serverdelsapp

I [Azure Portal](https://portal.azure.com) -menyn väljer du **resurs grupper** eller söker efter och väljer *resurs grupper* från vilken sida som helst.

I **resurs grupper** söker du efter och väljer din resurs grupp. I **Översikt** väljer du din backend-appens hanterings sida.

:::image type="content" source="./media/tutorial-auth-aad/portal-navigate-back-end.png" alt-text="Skärm bild av fönstret resurs grupper som visar översikten över ett exempel på en resurs grupp och en backend-appens hanterings sida vald.":::

I backend-appens vänstra meny väljer du **autentisering/auktorisering** och aktiverar App Service autentisering genom att välja **på**.

För **Åtgärd att vidta när en begäran inte har autentiserats** väljer du **Logga in med Azure Active Directory**.

Under **autentiseringsproviders** väljer du **Azure Active Directory**.

:::image type="content" source="./media/tutorial-auth-aad/configure-auth-back-end.png" alt-text="Skärm bild av backend-appens vänstra meny med autentisering/auktorisering valt och inställningar som valts på den högra menyn.":::

Välj **Express** och godkänn standardinställningarna för att skapa en ny AD-App och välj **OK**.

På sidan **autentisering/auktorisering** väljer du **Spara**.

Uppdatera portal sidan när du ser meddelandet med meddelandet `Successfully saved the Auth Settings for <back-end-app-name> App` .

Välj **Azure Active Directory** igen och välj sedan **Azure AD App**.

Kopiera **klient-ID** för Azure AD-programmet till en anteckning. Du behöver det här värdet senare.

:::image type="content" source="./media/tutorial-auth-aad/get-application-id-back-end.png" alt-text="Skärm bild av fönstret Azure Active Directory inställningar som visar Azure AD App och fönstret Azure AD-program visar det klient-ID som ska kopieras.":::

Om du slutar här har du en fristående app som redan är skyddad av App Service autentisering och auktorisering. I de återstående avsnitten kan du se hur du skyddar en lösning för flera appar genom att "flöda" den autentiserade användaren från klient delen till Server delen. 

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Aktivera autentisering och auktorisering för klientappen

Följ samma steg för klientdelsappen, men hoppa över det sista steget. Du behöver inte klient-ID: t för frontend-appen.

Om du vill kan du navigera till `http://<front-end-app-name>.azurewebsites.net`. Du bör nu dirigeras till en säker inloggningssida. När du har loggat in kan *du fortfarande inte komma åt data från backend-appen* eftersom backend-appen nu kräver Azure Active Directory inloggning från frontend-appen. Du måste göra tre saker:

- Ge klientsidan åtkomst till serversidan
- Konfigurera App Service för att returnera en användbar token
- Använda token i koden

> [!TIP]
> Om du stöter på fel och konfigurerar om inställningarna för autentisering/auktorisering för appen kan det hända att tokenenheterna i tokenarkivet inte skapas om från de nya inställningarna. För att se till att dina token skapas måste du logga ut och sedan logga in i appen igen. Ett enkelt sätt att göra det är att använda webbläsaren i privat läge. Stäng webbläsaren och öppna den sedan i privat läge igen när du har ändrat inställningarna i dina appar.

### <a name="grant-front-end-app-access-to-back-end"></a>Ge klientdelsappen åtkomst till serversidan

Nu när du har aktiverat autentisering och auktorisering för båda dina appar backas var och en av dem upp av ett AD-program. I det här steget kan ge du klientdelsappen åtkomstbehörighet till serverdelsappen å användarens vägnar. (Tekniskt sett ger du _AD-programmet_ för klientdelsappen behörighet att komma åt _AD-programmet_ för serverdelen å användarens vägnar.)

I menyn [Azure Portal](https://portal.azure.com) väljer du **Azure Active Directory** eller söker efter och väljer *Azure Active Directory* från vilken sida som helst.

Välj **Appregistreringar**  >  **ägda program**  >  **Visa alla program i den här katalogen**. Välj namn på klient sidan och välj sedan API- **behörigheter**.

:::image type="content" source="./media/tutorial-auth-aad/add-api-access-front-end.png" alt-text="Skärm bild av Microsoft-Appregistreringar-fönstret med ägda program, ett klient namn för klient sidan och de API-behörigheter som valts.":::

Välj **Lägg till en behörighet** och välj sedan de **API: er som används i organisationen**  >  **\<back-end-app-name>** .

På sidan **begär API-behörigheter** för backend-appen väljer du **delegerade behörigheter** och **User_impersonation** och väljer sedan **Lägg till behörigheter**.

:::image type="content" source="./media/tutorial-auth-aad/select-permission-front-end.png" alt-text="Skärm bild av sidan begär API-behörigheter som visar delegerade behörigheter, user_impersonation och knappen Lägg till behörighet vald.":::

### <a name="configure-app-service-to-return-a-usable-access-token"></a>Konfigurera App Service för att returnera en användbar åtkomsttoken

Frontend-appen har nu de behörigheter som krävs för att komma åt backend-appen som den inloggade användaren. I det här steget konfigurerar du autentisering och auktorisering för App Service, så att du får en användbar åtkomsttoken för att komma åt serverdelen. I det här steget behöver du Server delens klient-ID som du kopierade från [aktivera autentisering och auktorisering för backend-appen](#enable-authentication-and-authorization-for-back-end-app).

Navigera till [Azure Resource Explorer](https://resources.azure.com) och Använd resurs trädet och leta upp din frontend-webbapp.

[Azure Resource Explorer](https://resources.azure.com) öppnas nu med din frontend-app vald i resurs trädet. Överst på sidan klickar du på **Läs/skriv** för att aktivera redigeringen av Azure-resurserna.

:::image type="content" source="./media/tutorial-auth-aad/resources-enable-write.png" alt-text="Skärm bild av knapparna skriv skydd och Läs/skriv överst på sidan Azure Resource Explorer med knappen Läs/skriv markerad.":::

Gå nedåt till **config** authsettings i den vänstra webbläsaren  >  .

I vyn **authsettings** (autentiseringsinställningar) klickar du på **Edit** (Redigera). Ange `additionalLoginParams` till följande JSON-sträng med det klient-ID som du kopierade. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

:::image type="content" source="./media/tutorial-auth-aad/additional-login-params-front-end.png" alt-text="Skärm bild av ett kod exempel i authsettings-vyn som visar additionalLoginParams-strängen med ett exempel på ett klient-ID.":::

Spara inställningarna genom att klicka på **PUT** (Placera).

Nu har apparna konfigurerats. Klientdelen är nu redo att komma åt serverdelen med en åtkomsttoken.

Information om hur du konfigurerar åtkomsttoken för andra providrar finns i [Uppdatera identitet Provider tokens](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="call-api-securely-from-server-code"></a>Anropa API på säkert sätt från serverkod

I det här steget aktiverar du din tidigare ändrade serverkod så att du kan göra autentiserade anrop till serverdels-API:et.

Din frontend-app har nu den behörighet som krävs och lägger även till Server delens klient-ID i inloggnings parametrarna. Den kan därför få en åtkomsttoken för autentisering utan serverdelsappen. App Service tillhandahåller denna token i serverkoden genom att införa en `X-MS-TOKEN-AAD-ACCESS-TOKEN`-rubrik för varje autentiserad begäran (se [Hämta token i appkod](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!NOTE]
> Dessa rubriker införs för alla språk som stöds. Du har åtkomst till dem med hjälp av standardmönstret för respektive språk.

Öppna _Controllers/TodoController.cs_ igen i den lokala databasen. Under konstruktorn `TodoController(TodoContext context)` lägger du till följande kod:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Den här koden lägger till den vanliga HTTP-rubriken`Authorization: Bearer <access-token>` till alla fjärranslutna API-anrop. I ASP.NET Core MVC-begäran körnings pipeline `OnActionExecuting` körs den precis före respektive åtgärd, så var och en av ditt utgående API-anrop nu visar åtkomsttoken.

Spara alla ändringar. I det lokala terminalfönstret distribuerar du ändringarna till klientdelsappen med följande Git-kommandon:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Logga in på `https://<front-end-app-name>.azurewebsites.net` igen. På sidan med dataanvändningsavtalet klickar du på **acceptera**.

Du bör nu kunna skapa, läsa, uppdatera och ta bort data från serverdelsappen, precis som innan. Den enda skillnaden är att båda apparna är nu skyddade med App Service-autentisering och -auktorisering, inklusive tjänst-till-tjänst-anrop.

Grattis! Serverkoden har nu åtkomst till serverdelsdata å den autentiserade användarens vägnar.

## <a name="call-api-securely-from-browser-code"></a>Anropa API på ett säkert sätt från webbläsarkoden

I det här steget pekar du Angular.js-appen för klientdelen till API:et för serverdelen. På så sätt får du veta hur du kan hämta åtkomsttoken och göra API-anrop till serverdelsappen med den.

Medan serverkoden har åtkomst till begäranderubriker kan klientkoden komma åt `GET /.auth/me` för att få samma åtkomsttoken (se [Hämta token i appkod](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)).

> [!TIP]
> Det här avsnittet använder vi standard-HTTP-metoder för att demonstrera det säkra HTTP-anropet. Du kan dock använda [Microsoft Authentication Library för Java Script](https://github.com/AzureAD/microsoft-authentication-library-for-js) för att förenkla Angular.js program mönstret.
>

### <a name="configure-cors"></a>Konfigurera CORS

I Cloud Shell aktiverar du CORS till din klients URL med hjälp av [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) kommandot. Ersätt _\<back-end-app-name>_ _\<front-end-app-name>_ plats hållarna och.

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

Det här steget är inte kopplat till autentisering och auktorisering. Du behöver det dock för att webbläsaren ska tillåta API-anrop från flera domäner från Angular.js-appen. Mer information finns i [Lägga till CORS-funktioner](app-service-web-tutorial-rest-api.md#add-cors-functionality).

### <a name="point-angularjs-app-to-back-end-api"></a>Peka Angular.js-app till serverdels-API

Öppna _wwwroot/index.html_ på den lokala lagringsplatsen.

På rad 51 anger du `apiEndpoint` variabeln till HTTPS-URL: en för backend-appen ( `https://<back-end-app-name>.azurewebsites.net` ). Ersätt _\<back-end-app-name>_ med namnet på appen i App Service.

På den lokala lagringsplatsen öppnar du _wwwroot/app/scripts/todoListSvc.js_ och ser till att `apiEndpoint` är tillagt för alla API-anrop. Nu anropar Angular.js-appen serverdels-API:erna. 

### <a name="add-access-token-to-api-calls"></a>Lägga till åtkomsttoken till API-anrop

Öppna _wwwroot/app/scripts/todoListSvc.js_. Ovanför listan med API-anrop (ovanför raden `getItems : function(){`), lägger du till följande funktion i listan:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Den här funktionen anropas för att ange standardrubriken `Authorization` med åtkomsttoken. Du anropar den i nästa steg.

På den lokala lagringsplatsen öppnar du _wwwroot/app/scripts/app.js_ och hittar följande kod:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Ersätt hela kodblocket med följande kod:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

Den nya ändringen lägger till `resolve` mappningen som anropar`/.auth/me` och anger åtkomsttoken. Det innebär att du med säkerhet har åtkomsttoken innan kontrollanten `todoListCtrl` initieras. På så sätt omfattar alla API-anrop av kontrollanten token.

### <a name="deploy-updates-and-test"></a>Distribuera uppdateringar och testa

Spara alla ändringar. I det lokala terminalfönstret distribuerar du ändringarna till klientdelsappen med följande Git-kommandon:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Navigera till `https://<front-end-app-name>.azurewebsites.net` igen. Du bör nu kunna skapa, läsa, uppdatera och ta bort data från serverdelsappen, direkt i Angular.js-appen.

Grattis! Klientkoden har nu åtkomst till serverdelsdata å den autentiserade användarens vägnar.

## <a name="when-access-tokens-expire"></a>När åtkomsttoken upphör att gälla

Ditt åtkomsttoken upphör att gälla efter ett tag. Information om hur du uppdaterar dina åtkomsttoken utan att användarna måste autentiseras på nytt med din app finns på sidan om att [uppdatera token för identitetsprovider](app-service-authentication-how-to.md#refresh-identity-provider-tokens).

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort resursgruppen genom att köra följande kommando i Cloud Shell:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Det kan några minuter att köra kommandot.

<a name="next"></a>
## <a name="next-steps"></a>Nästa steg

Vad du lärt dig:

> [!div class="checklist"]
> * Aktivera inbyggd autentisering och auktorisering
> * Skydda appar mot oautentiserade begäranden
> * Använda Azure Active Directory som identitetsleverantör
> * Få åtkomst till en fjärrapp å den inloggade användarens vägnar
> * Säkra tjänst-till-tjänst-anrop med tokenautentisering
> * Använda åtkomsttoken från serverkod
> * Använd åtkomsttoken från klientkod (webbläsare)

Gå vidare till nästa självstudie där du får lära dig att mappa ett anpassat DNS-namn till appen.

> [!div class="nextstepaction"]
> [Mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md)
