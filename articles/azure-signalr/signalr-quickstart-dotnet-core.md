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
ms.openlocfilehash: 3cfcf57f455a5c3b17b794acf2ded66ed2285eff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94873513"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>Snabb start: skapa ett chattrum med hjälp av signal tjänsten

Azure SignalR Service är en Azure-tjänst som hjälper utvecklare att enkelt skapa webbappar med realtidsfunktioner. Den här tjänsten grundades ursprungligen på [SignalR för ASP.NET Core 2,1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1), men har nu stöd för senare versioner.

Den här artikeln visar hur du kommer igång med Azure SignalR Service. I den här snabb starten skapar du ett chatt-program med hjälp av en ASP.NET Core MVC-webbapp. Den här appen skapar en anslutning till din Azure SignalR Service-resurs för att aktivera uppdateringar av innehåll i realtid. Du ska vara värd för webb programmet lokalt och ansluta till flera webb läsar klienter. Varje klient kommer att kunna skicka innehållsuppdateringar till alla andra klienter. 

Du kan använda valfritt kodredigeringsprogram för att slutföra stegen i den här snabbstarten. Ett alternativ är [Visual Studio Code](https://code.visualstudio.com/), som finns på Windows-, MacOS-och Linux-plattformarna.

Koden för de här självstudierna är tillgänglig för nedladdning på [GitHub-lagringsplatsen AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom). Du kan också skapa Azure-resurserna som används i den här snabb starten genom att följa [skapa ett signal tjänst skript](scripts/signalr-cli-create-service.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Förutsättningar

* Installera [.net Core SDK](https://www.microsoft.com/net/download/windows).
* Ladda ned eller klona GitHub-lagringsplatsen [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples). 

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-azure-signalr-resource"></a>Skapa en Azure SignalR-resurs

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnetcore).

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

I det här avsnittet använder du [.net Core kommando rads gränssnitt (CLI)](/dotnet/core/tools/) för att skapa ett ASP.net Core MVC-webbprogram. Fördelen med att använda .NET Core CLI i Visual Studio är att den är tillgänglig på Windows-, macOS-och Linux-plattformarna. 

1. Skapa en mapp för ditt projekt. I den här snabb starten används mappen *E:\Testing\chattest* .

2. I den nya mappen kör du följande kommando för att skapa projektet:

    ```dotnetcli
    dotnet new mvc
    ```

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnetcore).

## <a name="add-secret-manager-to-the-project"></a>Lägg till Secret Manager i projektet

I det här avsnittet ska du lägga till [verktyget Secret Manager](/aspnet/core/security/app-secrets) i projektet. Verktyget Secret Manager lagrar känsliga data för utvecklings arbete utanför projekt trädet. Den här metoden hjälper till att förhindra att program hemligheter delas av misstag i käll koden.

1. Öppna *.csproj*-filen. Lägg till ett `DotNetCliToolReference`-element att inkludera *Microsoft.Extensions.SecretManager.Tools*. Lägg också till ett- `UserSecretsId` element som det visas i följande kod för den *chattaste. CSPROJ* och spara filen.

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

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnetcore).

## <a name="add-azure-signalr-to-the-web-app"></a>Lägg till Azure SignalR till webbappen

1. Lägg till en referens till `Microsoft.Azure.SignalR` NuGet-paketet genom att köra följande kommando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. Kör följande kommando för att återställa paket för ditt projekt:

    ```dotnetcli
    dotnet restore
    ```

3. Lägg till en hemlighet med namnet *Azure: SignalR:ConnectionString* till Secret Manager. 

    Den här hemligheten innehåller anslutningssträngen för åtkomst till din SignalR Service-resurs. *Azure: SignalR: ConnectionString* är standard konfigurations nyckeln som signalerar för att upprätta en anslutning. Ersätt värdet i följande kommando med anslutnings strängen för signal tjänst resursen.

    Du måste köra det här kommandot i samma katalog som *. CSPROJ* -filen.

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    Secret Manager används bara för att testa webbappen när den är lokalt värd. I en senare själv studie kurs distribuerar du chatt-webbappen till Azure. När webbappen har distribuerats till Azure använder du en program inställning i stället för att lagra anslutnings strängen med Secret Manager.

    Den här hemligheten nås med Konfigurations-API: et. Ett kolon (:) fungerar i konfigurations namnet med Konfigurations-API: et på alla plattformar som stöds. Se [konfiguration efter miljö](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider).


4. Öppna *Start. cs* och uppdatera `ConfigureServices` metoden för att använda Azure SignalR-tjänsten genom att `AddSignalR()` anropa `AddAzureSignalR()` metoderna och:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR()
                .AddAzureSignalR();
    }
    ```

    Genom att inte skicka en parameter till `AddAzureSignalR()` använder den här koden standard konfigurations nyckeln för signal anslutningens tjänst resurs anslutnings sträng. Standard konfigurations nyckeln är *Azure: SignalR: ConnectionString*.

5. I *startup. cs* uppdaterar du `Configure` metoden genom att ersätta den med följande kod.

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

I SignalR är en hubb en kärn komponent som exponerar en uppsättning metoder som kan anropas från klienten. I det här avsnittet får du lära dig att definiera en hubbklass med en av två metoder:

* `Broadcast`: Den här metoden sänder ett meddelande till alla klienter.
* `Echo`: Den här metoden skickar ett meddelande tillbaka till anroparen.

Båda metoderna använder `Clients` gränssnittet som ASP.net Core SignalR SDK tillhandahåller. Det här gränssnittet ger dig åtkomst till alla anslutna klienter, så att du kan skicka innehåll till dina klienter.

1. Lägg till en ny mapp med namnet *Hubb* i projektkatalogen. Lägg till en ny hubb kod fil med namnet *ChatHub. cs* i den nya mappen.

2. Lägg till följande kod i *ChatHub. cs* för att definiera Hub-klassen och spara filen.

    Uppdatera namn området för den här klassen om du använde ett projekt namn som skiljer sig från *SignalR. MVC*.

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

### <a name="add-the-client-interface-for-the-web-app"></a>Lägg till klient gränssnittet för webbappen

Klientens användar gränssnitt för chattrum-appen kommer att bestå av HTML och Java Script i en fil med namnet *index.html* i katalogen *wwwroot* .

Kopiera CSS */Site. CSS-* filen från mappen *wwwroot* i [exempel lagrings platsen](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot). Ersätt projektets *CSS/site. CSS* med den som du kopierade.

Här är huvud koden för *index.html*:

Skapa en ny fil i katalogen *wwwroot* med namnet *index.html*, kopiera och klistra in följande HTML i den nyligen skapade filen:

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

Koden i *index.html* -anrop `HubConnectionBuilder.build()` för att upprätta en HTTP-anslutning till Azure SignalR-resursen.

Om anslutningen lyckas skickas anslutningen till `bindConnectionMessage` som lägger till händelsehanterare för inkommande innehåll som skickas till klienten. 

`HubConnection.start()` startar kommunikationen med hubben. Sedan `onConnected()` lägger du till knapp händelse hanterare. Dessa hanterare använder anslutningen för att klientens ska kunna skicka innehållsuppdateringar till alla anslutna klienter.

## <a name="add-a-development-runtime-profile"></a>Lägg till en körningsprofil för utveckling

I det här avsnittet ska du lägga till en utvecklings körnings miljö för ASP.NET Core. Mer information finns i [arbeta med flera miljöer i ASP.net Core](/aspnet/core/fundamentals/environments).

1. Skapa en mapp med namnet *Egenskaper* i ditt projekt.

2. Lägg till en ny fil med namnet *launchSettings.jsi* mappen, med följande innehåll och spara filen.

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

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnetcore).

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommando gränssnittet:

    ```dotnetcli
    dotnet build
    ```

1. När build-versionen har slutförts kör du följande kommando för att köra webbappen lokalt:

    ```dotnetcli
    dotnet run
    ```

    Appen kommer att finnas lokalt på port 5000, enligt konfigurationen i vår utvecklings körnings profil:

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

1. Öppna två webb läsar fönster. I varje webbläsare går du till `http://localhost:5000` . Du uppmanas att ange ditt namn. Ange ett klient namn för båda klienterna och testa att skicka meddelande innehåll mellan båda klienterna med hjälp av knappen **Skicka** .

    ![Exempel på en Azure SignalR-grupp Chat](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnetcore).

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa självstudie kan du hålla resurserna som du skapade i den här snabb starten och återanvända dem.

Om du är klar med exempel programmet för snabb start kan du ta bort Azure-resurserna som du skapade i den här snabb starten för att undvika avgifter. 

> [!IMPORTANT]
> Att ta bort en resurs grupp går inte att ångra och innehåller alla resurser i gruppen. Var noga så att du inte tar bort fel resursgrupp eller resurser av misstag. Om du har skapat resurserna för att vara värd för det här exemplet i en befintlig resurs grupp som innehåller resurser som du vill behålla, kan du ta bort varje resurs individuellt från bladet i stället för att ta bort resurs gruppen.

Logga in på [Azure Portal](https://portal.azure.com) och välj **Resursgrupper**.

Skriv namnet på din resurs grupp i text rutan **Filtrera efter namn** . Anvisningarna för den här snabbstarten använde en resursgrupp med namnet *SignalRTestResources*. I resurs gruppen i resultat listan väljer du ellipsen (..**.**) > **ta bort resurs grupp**.

![Val för att ta bort en resurs grupp](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Du blir ombedd att bekräfta borttagningen av resursgruppen. Ange namnet på resurs gruppen som ska bekräftas och välj **ta bort**.

Efter en liten stund tas resursgruppen och de resurser som finns i den bort.

Har du problem? Prova [fel söknings guiden](signalr-howto-troubleshoot-guide.md) eller [berätta för oss](https://aka.ms/asrs/qsnetcore).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en ny Azure SignalR service-resurs. Sedan använde du den med en ASP.NET Core webbapp för att skicka innehålls uppdateringar i real tid till flera anslutna klienter. Om du vill veta mer om hur du använder Azure SignalR-tjänsten fortsätter du till självstudien som visar autentisering.

> [!div class="nextstepaction"]
> [Azure SignalR Service-autentisering](./signalr-concept-authenticate-oauth.md)
