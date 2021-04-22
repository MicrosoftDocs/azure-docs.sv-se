---
title: Snabbstart för att lära dig hur du använder Azure SignalR Service
description: En snabbstart för att använda Azure SignalR Service för att skapa ett chattrum med ASP.NET Core MVC-appar.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 9099a8620f6e4d87ec38c10226d94b1b3cd3462f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865989"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Snabbstart: Skapa ett chattrum med hjälp av SignalR Service

Azure SignalR Service är en Azure-tjänst som hjälper utvecklare att enkelt skapa webbappar med realtidsfunktioner. Den här tjänsten baserades [ursprungligen på SignalR för ASP.NET Core 2.1,](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1)men har nu stöd för senare versioner.

Den här artikeln visar hur du kommer igång med Azure SignalR Service. I den här snabbstarten skapar du ett chattprogram med hjälp av en ASP.NET Core MVC-webbapp. Den här appen skapar en anslutning till din Azure SignalR Service-resurs för att aktivera uppdateringar av innehåll i realtid. Du kommer att vara värd för webbappen lokalt och ansluta till flera webbläsarklienter. Varje klient kommer att kunna skicka innehållsuppdateringar till alla andra klienter. 

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här snabbstarten. Ett alternativ är [Visual Studio Code](https://code.visualstudio.com/), som är tillgängligt på Windows-, macOS- och Linux-plattformarna.

Koden för de här självstudierna är tillgänglig för nedladdning på [GitHub-lagringsplatsen AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Du kan också skapa De Azure-resurser som används i den här snabbstarten genom att [följa Skapa SignalR Service skript](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Förutsättningar

* Installera [.NET Core SDK](https://dotnet.microsoft.com/download).
* Ladda ned eller klona GitHub-lagringsplatsen [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples). 

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-azure-signalr-resource"></a>Skapa en Azure SignalR-resurs

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

I det här avsnittet använder du [.NET Core-kommandoradsgränssnittet (CLI) för](/dotnet/core/tools/) att skapa ett ASP.NET Core MVC-webbappsprojekt. Fördelen med att använda .NET Core CLI jämfört Visual Studio är att den är tillgänglig på Plattformarna Windows, macOS och Linux. 

1. Skapa en mapp för projektet. I den här snabbstarten *används mappen E:\Testing\chattest.*

2. Kör följande kommando i den nya mappen för att skapa projektet:

    ```dotnetcli
    dotnet new mvc
    ```

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnetcore).

## <a name="add-secret-manager-to-the-project"></a>Lägg till Secret Manager i projektet

I det här avsnittet lägger du till [verktyget Secret Manager](/aspnet/core/security/app-secrets) i projektet. Verktyget Secret Manager lagrar känsliga data för utvecklingsarbete utanför projektträdet. Den här metoden förhindrar oavsiktlig delning av apphemligheter i källkoden.

1. Öppna *.csproj*-filen. Lägg till ett `DotNetCliToolReference`-element att inkludera *Microsoft.Extensions.SecretManager.Tools*. Lägg även till `UserSecretsId` ett -element enligt följande kod för *chattest.csproj* och spara filen.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.2" />
    </ItemGroup>

    </Project>
    ```

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsnetcore)

## <a name="add-azure-signalr-to-the-web-app"></a>Lägg till Azure SignalR till webbappen

1. Lägg till en referens till `Microsoft.Azure.SignalR` NuGet-paketet genom att köra följande kommando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Kör följande kommando för att återställa paket för projektet:

    ```dotnetcli
    dotnet restore
    ```

3. Lägg till en hemlighet med namnet *Azure: SignalR:ConnectionString* till Secret Manager. 

    Den här hemligheten innehåller anslutningssträngen för åtkomst till din SignalR Service-resurs. *Azure:SignalR:ConnectionString är* standardkonfigurationsnyckeln som SignalR letar efter för att upprätta en anslutning. Ersätt värdet i följande kommando med anslutningssträngen för din SignalR Service resurs.

    Du måste köra det här kommandot i samma katalog som *.csproj-filen.*

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    Secret Manager används endast för att testa webbappen när den finns lokalt. I en senare självstudiekurs distribuerar du chattwebbappen till Azure. När webbappen har distribuerats till Azure använder du en programinställning i stället för att lagra anslutningssträngen med Secret Manager.

    Den här hemligheten nås med konfigurations-API:et. Ett kolon (:) fungerar i konfigurationsnamnet med konfigurations-API:et på alla plattformar som stöds. Se [Konfiguration efter miljö.](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider)


4. Öppna *Startup.cs* och uppdatera metoden `ConfigureServices` så att den använder Azure SignalR Service anropa metoderna och `AddSignalR()` `AddAzureSignalR()` :

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR()
                .AddAzureSignalR();
    }
    ```

    Genom att inte skicka en parameter `AddAzureSignalR()` till använder den här koden standardkonfigurationsnyckeln för SignalR Service för resursanslutningssträngen. Standardkonfigurationsnyckeln är *Azure:SignalR:ConnectionString*.

5. I *Startup.cs* uppdaterar du `Configure` metoden genom att ersätta den med följande kod.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseFileServer();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ChatHub>("/chat");
        });
    }
    ```

### <a name="add-a-hub-class"></a>Lägg till en hubbklass

I SignalR är en hubb en kärnkomponent som exponerar en uppsättning metoder som kan anropas från klienten. I det här avsnittet får du lära dig att definiera en hubbklass med en av två metoder:

* `Broadcast`: Den här metoden sänder ett meddelande till alla klienter.
* `Echo`: Den här metoden skickar ett meddelande tillbaka till anroparen.

Båda metoderna använder gränssnittet `Clients` som ASP.NET Core SignalR SDK tillhandahåller. Det här gränssnittet ger dig åtkomst till alla anslutna klienter, så att du kan skicka innehåll till dina klienter.

1. Lägg till en ny mapp med namnet *Hubb* i projektkatalogen. Lägg till en ny hubbkodfil med *namnet ChatHub.cs* i den nya mappen.

2. Lägg till följande kod i *ChatHub.cs* för att definiera hubbklassen och spara filen.

    Uppdatera namnområdet för den här klassen om du använde ett projektnamn som skiljer sig från *SignalR.Mvc*.

    ```csharp
    using Microsoft.AspNetCore.SignalR;
    using System.Threading.Tasks;
    
    namespace SignalR.Mvc
    {
        public class ChatHub : Hub
        {
            public Task BroadcastMessage(string name, string message) =>
                Clients.All.SendAsync("broadcastMessage", name, message);
    
            public Task Echo(string name, string message) =>
                Clients.Client(Context.ConnectionId)
                       .SendAsync("echo", name, $"{message} (echo from server)");
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Lägga till klientgränssnittet för webbappen

Klientens användargränssnitt för den här chattrumsappen består av HTML och JavaScript i en *fil med namnetindex.html* i katalogen *wwwroot.*

Kopiera filen *css/site.css* från *wwwroot-mappen på* lagringsplatsen [för exemplen](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Ersätt projektets *css/site.css med* det som du kopierade.

Här är huvudkoden för *index.html*:

Skapa en ny fil i *wwwroot-katalogen* *med namnetindex.html*, kopiera och klistra in följande HTML i den nya filen:

```html
<!DOCTYPE html>
<html>
<head>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="css/site.css" rel="stylesheet" />
    <title>Azure SignalR Group Chat</title>
</head>
<body>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px; padding-bottom: 30px;">Azure SignalR Group Chat</h2>
    <div class="container" style="height: calc(100% - 110px);">
        <div id="messages" style="background-color: whitesmoke; "></div>
        <div style="width: 100%; border-left-style: ridge; border-right-style: ridge;">
            <textarea id="message"
                      style="width: 100%; padding: 5px 10px; border-style: hidden;"
                      placeholder="Type message and press Enter to send..."></textarea>
        </div>
        <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
            <button class="btn-warning pull-right" id="echo">Echo</button>
            <button class="btn-success pull-right" id="sendmessage">Send</button>
        </div>
    </div>
    <div class="modal alert alert-danger fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <div>Connection Error...</div>
                    <div><strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)</div>
                </div>
            </div>
        </div>
    </div>

    <!--Reference the SignalR library. -->
    <script src="https://cdn.jsdelivr.net/npm/@microsoft/signalr@3.1.8/dist/browser/signalr.min.js"></script>

    <!--Add script to update the page and send messages.-->
    <script type="text/javascript">
        document.addEventListener('DOMContentLoaded', function () {

            const generateRandomName = () =>
                Math.random().toString(36).substring(2, 10);

            let username = generateRandomName();
            const promptMessage = 'Enter your name:';
            do {
                username = prompt(promptMessage, username);
                if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
                    username = '';
                    promptMessage = 'Invalid input. Enter your name:';
                }
            } while (!username)

            const messageInput = document.getElementById('message');
            messageInput.focus();

            function createMessageEntry(encodedName, encodedMsg) {
                var entry = document.createElement('div');
                entry.classList.add("message-entry");
                if (encodedName === "_SYSTEM_") {
                    entry.innerHTML = encodedMsg;
                    entry.classList.add("text-center");
                    entry.classList.add("system-message");
                } else if (encodedName === "_BROADCAST_") {
                    entry.classList.add("text-center");
                    entry.innerHTML = `<div class="text-center broadcast-message">${encodedMsg}</div>`;
                } else if (encodedName === username) {
                    entry.innerHTML = `<div class="message-avatar pull-right">${encodedName}</div>` +
                        `<div class="message-content pull-right">${encodedMsg}<div>`;
                } else {
                    entry.innerHTML = `<div class="message-avatar pull-left">${encodedName}</div>` +
                        `<div class="message-content pull-left">${encodedMsg}<div>`;
                }
                return entry;
            }

            function bindConnectionMessage(connection) {
                var messageCallback = function (name, message) {
                    if (!message) return;
                    var encodedName = name;
                    var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
                    var messageEntry = createMessageEntry(encodedName, encodedMsg);

                    var messageBox = document.getElementById('messages');
                    messageBox.appendChild(messageEntry);
                    messageBox.scrollTop = messageBox.scrollHeight;
                };
                connection.on('broadcastMessage', messageCallback);
                connection.on('echo', messageCallback);
                connection.onclose(onConnectionError);
            }

            function onConnected(connection) {
                console.log('connection started');
                connection.send('broadcastMessage', '_SYSTEM_', username + ' JOINED');
                document.getElementById('sendmessage').addEventListener('click', function (event) {
                    if (messageInput.value) {
                        connection.send('broadcastMessage', username, messageInput.value);
                    }

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
                    connection.send('echo', username, messageInput.value);

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
            }

            function onConnectionError(error) {
                if (error && error.message) {
                    console.error(error.message);
                }
                var modal = document.getElementById('myModal');
                modal.classList.add('in');
                modal.style = 'display: block;';
            }

            const connection = new signalR.HubConnectionBuilder()
                .withUrl('/chat')
                .build();
            bindConnectionMessage(connection);
            connection.start()
                .then(() => onConnected(connection))
                .catch(error => console.error(error.message));
        });
    </script>
</body>
</html>
```

Koden i *index.htmanropar* `HubConnectionBuilder.build()` för att upprätta en HTTP-anslutning till Azure SignalR-resursen.

Om anslutningen lyckas skickas anslutningen till `bindConnectionMessage` som lägger till händelsehanterare för inkommande innehåll som skickas till klienten. 

`HubConnection.start()` startar kommunikationen med hubben. Lägger sedan `onConnected()` till knapphändelsehanterare. Dessa hanterare använder anslutningen för att klientens ska kunna skicka innehållsuppdateringar till alla anslutna klienter.

## <a name="add-a-development-runtime-profile"></a>Lägg till en körningsprofil för utveckling

I det här avsnittet lägger du till en utvecklingskörningsmiljö för ASP.NET Core. Mer information finns i [Arbeta med flera miljöer i ASP.NET Core.](/aspnet/core/fundamentals/environments)

1. Skapa en mapp med *namnet Egenskaper* i projektet.

2. Lägg till en ny *launchSettings.jsmed* namnet och lägg till den i mappen med följande innehåll och spara filen.

    ```json
    {
        "profiles" : {
            "ChatRoom": {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnetcore).

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommandogränssnittet:

    ```dotnetcli
    dotnet build
    ```

1. När bygget är klart kör du följande kommando för att köra webbappen lokalt:

    ```dotnetcli
    dotnet run
    ```

    Appen kommer att finnas lokalt på port 5000, enligt konfiguration i vår utvecklingskörningsprofil:

    ```output
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: https://localhost:5001
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: http://localhost:5000
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: E:\Testing\chattest
    ```

1. Öppna två webbläsarfönster. I varje webbläsare går du till `http://localhost:5000` . Du uppmanas att ange ditt namn. Ange ett klientnamn för båda klienterna och testa att skicka meddelandeinnehåll mellan båda klienterna med hjälp av **knappen** Skicka.

    ![Exempel på en Azure SignalR-gruppchatt](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsnetcore)

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa självstudie kan du behålla resurserna som skapats i den här snabbstarten och återanvända dem.

Om du är klar med exempelprogrammet för snabbstart kan du ta bort De Azure-resurser som skapades i den här snabbstarten för att undvika kostnader. 

> [!IMPORTANT]
> Det går inte att ångra borttagningen av en resursgrupp och innehåller alla resurser i gruppen. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag. Om du har skapat resurserna som är värdar för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs individuellt från dess blad i stället för att ta bort resursgruppen.

Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.

I **textrutan Filtrera efter** namn skriver du namnet på din resursgrupp. Anvisningarna för den här snabbstarten använde en resursgrupp med namnet *SignalRTestResources*. I resursgruppen i resultatlistan väljer du ellipsen (**...**) > **Ta bort resursgrupp**.

![Val för att ta bort en resursgrupp](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Du blir ombedd att bekräfta borttagningen av resursgruppen. Ange namnet på resursgruppen för att bekräfta och välj Ta **bort.**

Efter en liten stund tas resursgruppen och de resurser som finns i den bort.

Har du problem? Prova [felsökningsguiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss.](https://aka.ms/asrs/qsnetcore)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Azure SignalR Service resurs. Sedan använde du den med en ASP.NET Core-webbapp för att skicka innehållsuppdateringar i realtid till flera anslutna klienter. Om du vill veta mer om Azure SignalR Service kan du fortsätta till självstudien som visar autentisering.

> [!div class="nextstepaction"]
> [Azure SignalR Service-autentisering](./signalr-concept-authenticate-oauth.md)
