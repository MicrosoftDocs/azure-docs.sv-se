---
title: Guide för att autentisera Azure SignalR Service klienter
description: Lär dig hur du implementerar din egen autentisering och integrerar den med Azure SignalR Service genom att följa e2e-exemplet.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: aace45f78bab273b792d10754be431ed3949e4c6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869859"
---
# <a name="azure-signalr-service-authentication"></a>Azure SignalR Service-autentisering

Den här självstudien bygger på chattrumsprogrammet som introducerades i snabbstarten. Om du inte har slutfört [Skapa ett chattrum med SignalR Service](signalr-quickstart-dotnet-core.md) gör du det först.

I den här självstudien får du lära dig att implementera din egen autentisering och integrera den med Microsoft Azure SignalR Service.

Autentiseringen som användes initialt i snabbstartens chattrumsprogram är för enkel för verkliga scenarier. Med programmet kan varje klient göra anspråk på vem den är, och servern accepterar det enkelt. Metoden är inte särskilt användbar i verkliga program där en obehörig användare kan personifiera andra för att få åtkomst till känslig information.

[GitHub](https://github.com/) tillhandahåller API:er för autentisering baserat på ett populärt branschstandardprotokoll som heter [OAuth](https://oauth.net/). Dessa API:er tillåter att program från tredje part autentiserar GitHub-konton. I den här självstudien använder du dessa API:er för att implementera autentisering via ett Github-konto innan du tillåter klientinloggningar till chattrumsrprogrammet. När du har autentiserat ett GitHub-konto läggs kontoinformationen till som en cookie som ska användas av webbklienten för att autentisera.

Mer information om API:er för OAuth-autentisering som tillhandahålls via GitHub finns i [Basics of Authentication](https://developer.github.com/v3/guides/basics-of-authentication/) (Grunderna om autentisering).

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här snabbstarten. [Visual Studio Code](https://code.visualstudio.com/) är dock ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformar.

Koden för de här självstudierna är tillgänglig för nedladdning på [GitHub-lagringsplatsen AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat).

![OAuth finns i Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Registrera en ny OAuth-app med ditt GitHub-konto
> * Lägga till en autentiseringskontroll för att stödja GitHub-autentisering
> * Distribuera din ASP.NET Core-webbapp till Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudien behöver du följande:

- Ett konto som skapades på [GitHub](https://github.com/)
- [Git](https://git-scm.com/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)
- [Azure Cloud Shell](../cloud-shell/quickstart.md) konfigurerat för bash-miljön.
- Ladda ned eller klona GitHub-lagringsplatsen [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples).

## <a name="create-an-oauth-app"></a>Skapa en OAuth-app

1. Öppna en webbläsare och gå till `https://github.com` och logga in på ditt konto.

2. För ditt konto går du till **Inställningar Utvecklarinställningar** och klickar på Registrera ett nytt program  >   eller Ny **OAuth-app** under *OAuth-appar.* 

3. Använd följande inställningar för den nya OAuth-appen och klicka sedan på **Registrera program**:

    | Inställningsnamn | Föreslaget värde | Description |
    | ------------ | --------------- | ----------- |
    | Programnamn | *Azure SignalR-chatt* | GitHub-användaren ska kunna känna igen och lita på den app som används för att autentisera.   |
    | Hemsides-URL | `http://localhost:5000/home` | |
    | Programbeskrivning | *Ett chattrumsexempel med Azure SignalR Service med GitHub-autentisering* | En praktisk beskrivning av programmet som hjälper dina programanvändare att förstå kontexten för autentiseringen som används. |
    | URL-adress för återanrop av auktorisering | `http://localhost:5000/signin-github` | Den här inställningen är den viktigaste inställningen för OAuth-programmet. Det är motringnings-URL:en som GitHub returnerar användaren till efter en lyckad autentisering. I den här självstudien måste du använda standard-motringnings-URL:en för *AspNet.Security.OAuth.GitHub*-paketet, */signin-github*.  |

4. När den nya OAuth-appregistreringen är klar lägger du till *klient-ID* och *klienthemlighet* till Secret Manager med följande kommandon. Ersätt *Your_GitHub_Client_Id* och *Your_GitHub_Client_Secret* med OAuth-appens värden.

    ```dotnetcli
    dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
    dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret
    ```

## <a name="implement-the-oauth-flow"></a>Implementera OAuth-flödet

### <a name="update-the-startup-class-to-support-github-authentication"></a>Uppdatera startklassen för att stödja GitHub-autentiseringen

1. Lägg till en referens till de senaste *Microsoft.AspNetCore.Authentication.Cookies*- och *AspNet.Security.OAuth.GitHub*-paketen och återställ alla paket.

    ```dotnetcli
    dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
    dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
    dotnet restore
    ```

1. Öppna *Startup.cs* och lägg till `using` instruktioner för följande namnrymder:

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. Överst i klassen `Startup` lägger du till konstanter för Secret Manager-nycklarna som innehåller GitHub OAuth-apphemligheterna.

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. Lägg till följande kod till metoden `ConfigureServices` för att stödja autentisering med GitHub OAuth-appen:

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. Lägg till hjälpkomponentmetoden `GetUserCompanyInfoAsync` i klassen `Startup`.

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Uppdatera metoden `Configure` i startklassen med följande kodrad och spara filen.

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>Lägga till en autentiseringskontroll

I det här avsnittet implementerar du en `Login` API som autentiserar klienter med GitHub OAuth-appen. När autentiseringen är klar lägger API:t till en cookie till webbklientsvaret innan klienten dirigeras om chattappen igen. Denna cookie används sedan för att identifiera klienten.

1. Lägg till en ny kontrollkodfil i katalogen *chattest\Controllers*. Ge filen namnet *AuthController.cs*.

2. Lägg till följande kod för autentiseringskontrollen. Se till att uppdatera namnrymnden om din projektkatalog inte var *chattest*:

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. Spara ändringarna.

### <a name="update-the-hub-class"></a>Uppdatera hubbklassen

Som standard när en webbklient försöker ansluta till SignalR Service beviljas anslutningen baserat på en åtkomsttoken som tillhandahålls internt. Denna åtkomsttoken är inte associerad med en autentiserad identitet. Åtkomsten är faktiskt anonym.

I det här avsnittet aktiverar du riktig autentisering genom att lägga till attributet `Authorize` till hubbklassen och uppdaterar hubbmetoderna för att läsa användarnamnet från den autentiserade användarens anspråk.

1. Öppna *Hub\Chat.cs* och lägg till referenser till namnrymderna:

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. Uppdatera hubbkoden som det visas nedan. Den här koden lägger till attributet `Authorize` till klassen `Chat` och använder den användarautentiserade identiteten i hubbmetoderna. Även metoden `OnConnectedAsync` läggs till, som loggar ett systemmeddelande till chattrummet varje gång en ny klient ansluter.

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. Spara ändringarna.

### <a name="update-the-web-client-code"></a>Uppdatera webbklientkoden

1. Öppna *wwwroot\index.html* och ersätt koden som ber om användarnamnet med kod för att använda den cookie som returneras av autentiseringskontrollanten.

    Ta bort följande kod från *index.html*:

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    Lägg till följande kod istället för koden ovan för att använda cookien:

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. Precis under kodraden du la till för att använda cookien lägger du till följande definition för funktionen `appendMessage`:

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. Uppdatera funktionerna `bindConnectionMessage` och `onConnected` med följande kod för att använda `appendMessage`.

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. Överst i *index.html* uppdaterar du felhanteraren för `connection.start()` som visas nedan för att uppmana användaren att logga in.

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. Spara ändringarna.

## <a name="build-and-run-the-app-locally"></a>Skapa och kör appen lokalt

1. Spara ändringarna i alla filer.

2. Skapa appen med .NET Core CLI och kör följande kommando i kommandogränssnittet:

    ```dotnetcli
    dotnet build
    ```

3. När versionen har slutförts, kör du följande kommando för att köra webbappen lokalt:

    ```dotnetcli
    dotnet run
    ```

    Som standard finns appen lokalt på port 5000:

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Production
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
                    Application started. Press Ctrl+C to shut down.
    ```

4. Öppna ett webbläsarfönster och gå till `http://localhost:5000`. Klicka på länken **här** överst för att logga in med GitHub.

    ![OAuth finns i Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

    Du uppmanas att godkänna chattappens åtkomst till ditt GitHub-konto. Klicka på knappen **Auktorisera**.

    ![Auktorisera OAuth-app](media/signalr-concept-authenticate-oauth/signalr-authorize-oauth-app.png)

    Du blir omdirigerad till chattprogrammet och loggas in med ditt GitHub-kontonamn. Webbprogrammet bestämde ditt kontonamn genom att autentisera dig med hjälp av den nya autentiseringen som du har lagt till.

    ![Konto identifierat](media/signalr-concept-authenticate-oauth/signalr-oauth-account-identified.png)

    Nu när chattappen utför autentisering med GitHub och lagrar autentiseringsinformationen som cookies ska du distribuera den till Azure så att andra användare kan autentisera med sina konton och kommunicera från andra arbetsstationer.

## <a name="deploy-the-app-to-azure"></a>distribuera appen till Azure

Förbered din miljö för Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

I det här avsnittet använder du Azure-kommandoradsgränssnittet (CLI) för att skapa en ny webbapp i [Azure App Service](../app-service/index.yml) som värd för ASP.NET i Azure. Webbappen konfigureras för att använda lokal Git-distribution. Webbappen ska också konfigureras med anslutningssträngen för SignalR, GitHub OAuth-apphemligheter och en distributionsanvändare.

När du skapar följande resurser ska du se till att använda samma resursgrupp som SignalR Service-resursen finns i. Den här metoden gör rensningen mycket enklare senare när du vill ta bort alla resurser. I exemplen förutsätts att du använde gruppnamnet som rekommenderades i tidigare självstudier, *SignalRTestResources*.

### <a name="create-the-web-app-and-plan"></a>Skapa webbappen och planen

Kopiera texten för nedanstående kommandon och uppdatera parametrarna. Klistra in det uppdaterade skriptet i Azure Cloud Shell och tryck på **Retur** för att skapa en ny App Service-plan och -webbapp.

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| Parameter | Beskrivning |
| -------------------- | --------------- |
| ResourceGroupName | Den här resursgruppens namn föreslogs i föregående självstudier. Det är bra att hålla alla resurser från självstudier grupperade ihop. Använd samma resursgrupp som du använde i de tidigare självstudierna. |
| WebAppPlan | Ange ett nytt och unikt namn på App Service-planen. |
| WebAppName | Det här är namnet på den nya webbappen och en del av webbadressen. Använd ett unikt namn. Exempelvis signalrtestwebapp22665120.   |

### <a name="add-app-settings-to-the-web-app"></a>Lägga till appinställningar i webbappen

I det här avsnittet lägger du till app-inställningar för följande komponenter:

* SignalR-tjänstens resursanslutningssträng
* Klient-ID för GitHub OAuth-app
* Klienthemlighet för GitHub OAuth-app

Kopiera texten för nedanstående kommandon och uppdatera parametrarna. Klistra in det uppdaterade skriptet i Azure Cloud Shell och tryck på **Retur** för att lägga till appinställningarna:

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $SignalRServiceResource \
  --resource-group $ResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$primaryConnectionString"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| Parameter | Beskrivning |
| -------------------- | --------------- |
| GitHubClientId | Tilldela den här variabeln det hemliga klient-ID:t för din GitHub OAuth-app. |
| GitHubClientSecret | Tilldela den här variabeln det hemliga lösenordet för din GitHub OAuth-app. |
| ResourceGroupName | Uppdatera variabeln så att den har samma resursgruppnamn som du använde i det tidigare avsnittet. |
| SignalRServiceResource | Uppdatera den här variabeln med namnet på SignalR Service-resursen du skapade i snabbstarten. Till exempel signalrtestsvc48778624. |
| WebAppName | Uppdatera variabeln med namnet på den nya webbappen du skapade i föregående avsnitt. |

### <a name="configure-the-web-app-for-local-git-deployment"></a>Konfigurera webbappen för lokal Git-distribution

I Azure Cloud Shell klistrar du in följande skript. Skriptet skapar ett nytt användarnamn och lösenord för distributionen som du ska använda när du distribuerar din kod till en webbapp med Git. Skriptet konfigurerar även webbappen för distribution med en lokal Git-lagringsplats och returnerar Git-distributionens URL.

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| Parameter | Beskrivning |
| -------------------- | --------------- |
| DeploymentUserName | Välj ett nytt användarnamn för distribution. |
| DeploymentUserPassword | Välj ett lösenord för den nya användaren i distributionen. |
| ResourceGroupName | Använd samma resursgruppnamn som du använde i det tidigare avsnittet. |
| WebAppName | Det här är namnet på den nya webbappen som du skapade tidigare. |

Anteckna Git-distributions-URL:en som returnerades från det här kommandot. Du kommer att använda URL:en senare.

### <a name="deploy-your-code-to-the-azure-web-app"></a>Distribuera din kod till Azure-webbappen

För att distribuera din kod utför du följande kommandon i ett Git-gränssnitt.

1. Gå till roten för projektkatalogen. Om projektet inte är initierats med en Git-lagringsplats kör du följande kommando:

    ```bash
    git init
    ```

2. Lägg till en fjärranslutning för Git-distributionens URL som du antecknade tidigare:

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. Mellanlagra alla filer i den initierade lagringsplatsen och lägg till en incheckning.

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Distribuera din kod till Azure-webbappen.

    ```bash
    git push Azure main
    ```

    Du blir uppmanad att autentisera för att distribuera koden till Azure. Ange användarnamnet och lösenordet för distributionsanvändaren du skapade ovan.

### <a name="update-the-github-oauth-app"></a>Uppdatera GitHub OAuth-appen

Det sista du behöver göra är att uppdatera **webbsidans URL** och **URL-adressen för återanrop av auktorisering** för GitHub OAuth-appen för att peka på den nya värdbaserade appen.

1. Öppna [https://github.com](https://github.com) i en webbläsare och gå till kontots Inställningar   >  **Utvecklarinställningar**  >  **Oauth Apps**.

2. Klicka på autentiseringsappen och uppdatera **webbsidans URL** och **URL-adressen för återanrop av auktorisering** som det visas nedan:

    | Inställning | Exempel |
    | ------- | ------- |
    | Hemsides-URL | `https://signalrtestwebapp22665120.azurewebsites.net/home` |
    | URL-adress för återanrop av auktorisering | `https://signalrtestwebapp22665120.azurewebsites.net/signin-github` |

3. Gå till webbappens URL och testa programmet.

    ![OAuth finns i Azure](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du ska fortsätta till nästa självstudie kan du behålla resurserna som du har skapat i den här självstudien och använda dem igen i nästa självstudie.

Om du är klar med exempelappen för snabbstart kan du ta bort Azure-resurserna som du skapade i snabbstarten för att undvika kostnader.

> [!IMPORTANT]
> Det går inte att ångra borttagningen av en resursgrupp och att resursgruppen och alla resurser i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs separat från deras respektive blad istället för att ta bort resursgruppen.

Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

Skriv namnet på din resursgrupp i textrutan **Filter by name...** (Filtrera efter namn...). Anvisningarna för den här artikeln använde en resursgrupp med namnet *SignalRTestResources*. På din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

![Ta bort](./media/signalr-concept-authenticate-oauth/signalr-delete-resource-group.png)

Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp för att bekräfta och klicka på **Ta bort**.

Efter en liten stund tas resursgruppen och resurser som finns i den bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt till autentisering med OAuth för at erbjuda en bättre metod för autentisering med Azure SignalR Service. Om du vill veta mer om att använda Azure SignalR Server fortsätter du till Azure CLI-exemplen för SignalR Service.

> [!div class="nextstepaction"]
> [Azure SignalR CLI-exempel](./signalr-reference-cli.md)