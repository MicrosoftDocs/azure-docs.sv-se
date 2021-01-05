---
title: 'Självstudie: bygga en app för att chatta med en blixt Server – Azure SignalR'
description: I den här självstudien får du lära dig hur du skapar och ändrar en app med en blixt server med Azure SignalR-tjänsten
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: b0059e986b9a6ba8152a1a61f8d696f1caa4646a
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/23/2020
ms.locfileid: "97755913"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>Självstudie: bygga en app för att chatta med en blixt Server

I den här självstudien får du lära dig hur du skapar och ändrar en app med en blixt Server. Du lär dig följande:

> [!div class="checklist"]
> * Bygg ett enkelt chattrum med en snabb server app.
> * Ändra kniv-komponenter.
> * Använd händelse hantering och data bindning i komponenter.
> * Snabb distribution till Azure App Service i Visual Studio.
> * Migrera den lokala signalen till Azure SignalR-tjänsten.

## <a name="prerequisites"></a>Förutsättningar
* Installera [.net Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) (version >= 3.0.100)
* Installera [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (version >= 16,3)
> Visual Studio 2019 Preview-versionen fungerar också, som släpps med den senaste installations mal len för den senaste versionen av installations programmet för den nyare .net Core-versionen.

[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Bygg ett lokalt chattrum i den snabba Server appen

Från Visual Studio 2019 version 16.2.0 är Azure Signaling-tjänsten att publicera webb program publicerings process och hantera beroenden mellan Web App och SignalR service är mycket bekvämare. Du kan uppleva att du arbetar med den lokala signalen i den lokala dev-miljön och arbetar med Azure SignalR-tjänsten för Azure App Service samtidigt utan några kod ändringar.

1. Skapa en app i appen för chatt
   
   I Visual Studio väljer du skapa en ny projekt-> blixt app-> (namnge appen och välja en mapp) – > program vara för blixt Server. Kontrol lera att du redan har installerat .NET Core SDK 3.0 + för att Visual Studio ska kunna identifiera mål ramverket korrekt.

   [![I skapa ett nytt projekt väljs de nya apparna i mappen. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   Eller kör cmd
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. Lägg till en `BlazorChatSampleHub.cs` fil som ska implementeras `Hub` för chatt.
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. Lägg till en slut punkt för hubben i `Startup.Configure()` .
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. Installera `Microsoft.AspNetCore.SignalR.Client` paket för att använda signal klienten.

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. Skapa `ChatRoom.razor` under `Pages` mapp för att implementera SignalR-klienten. Följ stegen nedan eller kopiera filen [ChatRoom. kniv](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor)genom att kopiera.

   1. Lägg till sid länk och referens.
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. Lägg till kod till den nya signal klienten för att skicka och ta emot meddelanden.
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. Lägg till åter givnings del före `@code` för användar gränssnittet för att interagera med signal klienten.
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. Uppdatera `NavMenu.razor` för att infoga en post-meny för chatt-rummet under `NavMenuCssClass` liknande rest.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. Uppdatera `site.css` för att optimera vyer för chatt-ytan. Lägg till följande kod i slutet.

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. Klicka på <kbd>F5</kbd> för att köra appen. Du kan chatta som nedan.

   [![En animerad chatt mellan Robert och Alice visas. Alice står Hej, Bob står Hej. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Publicera till Azure

   Hittills arbetar appen blixt i den lokala signalen och när du distribuerar till Azure App Service, rekommenderar vi att du använder [Azure SignalR-tjänsten](/aspnet/core/signalr/scale?view=aspnetcore-3.1#azure-signalr-service) , vilket gör att du kan skala upp en blixt Server-App till ett stort antal samtidiga signalers anslutningar. Dessutom kan signal tjänstens globala räckvidd och data Center med hög prestanda avsevärt hjälpa till att minska svars tiden på grund av geografi.

> [!IMPORTANT]
> I den här program varan för en blixt Server, underhålls GRÄNSSNITTs tillstånd på Server sidan, vilket innebär att serverns fästis krävs i det här fallet. Om det finns en enda app-server är serverns fästis säkerställd av design. Men om det finns flera App-servrar, finns det en risk för att klient förhandlingen och anslutningen kan gå till olika servrar och leda till användar gränssnitts fel i den snabba appen. Så du måste aktivera serverns fästis som nedan i `appsettings.json` :
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. Högerklicka på projektet och navigera till `Publish` .

   * Mål: Azure
   * Specifika mål: alla typer av **Azure App Service** stöds.
   * App Service: skapa en ny eller Välj en befintlig app service.

   [![Animeringen visar valet av Azure som mål och Azure App Service som ett särskilt mål. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Lägg till tjänst beroende för Azure SignalR

   När publicerings profilen har skapats kan du se ett rekommenderat meddelande under **tjänst beroenden**. Klicka på **Konfigurera** för att skapa en ny eller Välj en befintlig Azure SignalR-tjänst i panelen.

   [![Vid publicering markeras länken som ska konfigureras. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   Tjänst beroendet gör saker nedan för att aktivera appen automatiskt till Azure SignalR-tjänsten när den körs på Azure.

   * Uppdatera [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) för att använda Azure SignalR-tjänsten.
   * Lägg till Azure SignalR service-NuGet paket referens.
   * Uppdatera profil egenskaper för att spara beroende inställningarna.
   * Att konfigurera hemligheter lagras beror på ditt val.
   * Lägg till `appsettings` konfiguration för att göra din app Target vald som Azure SignalR-tjänst.

   [![Vid Sammanfattning av ändringar används kryss rutorna för att markera alla beroenden. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. Publicera appen

   Nu är det dags att publicera. Och den ska automatiskt webbläsare sidan när publiceringen är klar. 
   > [!NOTE]
   > Det kan hända att den inte fungerar direkt på sidan för första gången på grund av Azure App Service fördröjning för distributions start och försök att uppdatera sidan för att ge en fördröjning.
   > Utöver detta kan du använda webbläsarens fel söknings läge med <kbd>F12</kbd> för att verifiera att trafiken redan har omdirigerats till Azure SignalR-tjänsten.

   [![Chat-exemplet i blixt SignalR har en text ruta för ditt namn och en chat!-knapp för att starta en chatt. ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png)](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>Ytterligare ämne: Aktivera Azure SignalR service i lokal utveckling

1. Lägg till en referens till Azure SignalR SDK

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. Lägg till ett anrop till Azure SignalR-tjänsten i `Startup.ConfigureServices()` .

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. Konfigurera Azure SignalR service `ConnectionString` antingen i `appsetting.json` eller med verktyget [Secret Manager](/aspnet/core/security/app-secrets?tabs=visual-studio&view=aspnetcore-3.1#secret-manager)

> [!NOTE]
> Steg 2 kan ersättas med hjälp av [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) till SignalR SDK.
> 
> 1. Lägg till konfiguration för att aktivera Azure SignalR service i `appsetting.json`
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Tilldela värd start sammansättning för att använda Azure SignalR SDK. Redigera `launchSettings.json` och Lägg till en konfiguration som nedan i `environmentVariables` .
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[Har du problem? Berätta för oss.](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>Rensa resurser

Rensa resurserna som skapats i den här självstudien genom att ta bort resursgruppen via Azure-portalen.

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Bygg ett enkelt chattrum med en snabb server app.
> * Ändra kniv-komponenter.
> * Använd händelse hantering och data bindning i komponenter.
> * Snabb distribution till Azure App Service i Visual Studio.
> * Migrera den lokala signalen till Azure SignalR-tjänsten.

Läs mer om hög tillgänglighet.
> [!div class="nextstepaction"]
> [Återhämtning och haveriberedskap](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [ASP.NET Core blixt](/aspnet/core/blazor)
