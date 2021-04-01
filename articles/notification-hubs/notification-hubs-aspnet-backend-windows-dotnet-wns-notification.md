---
title: Skicka meddelanden till specifika användare med Azure Notification Hubs | Microsoft Docs
description: Lär dig mer om att skicka meddelanden till specifika användare med hjälp av Universal Windows Platform-program (UWP).
documentationcenter: windows
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 97a6a45ab01fc113b79a48ba7fcb246d528684be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019488"
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>Självstudier: Skicka meddelanden till specifika användare med Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en speciell App-användare på en speciell enhet. En ASP.NET-WebAPI-serverdel används för att autentisera klienter. När serverdelen autentiserar en klientprogramsanvändare lägger den automatiskt till en tagg till meddelanderegistreringen. Serverdelen använder den här taggen för att skicka meddelanden till specifika användare.

> [!NOTE]
> Den färdiga koden för den här självstudien hittar du på [GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett WebAPI-projekt
> * Autentisera klienter mot WebAPI-serverdelen
> * Registrera för meddelanden med hjälp av WebAPI-serverdelen
> * Skicka meddelanden från WebAPI-serverdelen
> * Publicera den nya WebAPI-serverdelen
> * Uppdatera klientprojektets kod
> * Testa programmet

## <a name="prerequisites"></a>Förutsättningar

Den här kursen bygger på meddelandehubben och det Visual Studio-projekt som du skapade i kursen [Självstudier: Skicka meddelanden till UWP-appar med Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Slutför den därför innan du påbörjar den här kursen.

> [!NOTE]
> Om du använder Mobile Apps i Azure App Service som serverdelstjänst, så läs [Mobile Apps-version](/previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push) i den här kursen.

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-uwp-client"></a>Uppdatera koden för UWP-klienten

I det här avsnittet kommer du att uppdatera koden i det projekt som du slutförde i kursen [Självstudier: Skicka meddelanden till UWP-appar med Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md). Projektet bör redan ha associerats till Windows Store. Det bör också ha konfigurerats för meddelandehubben. I det här avsnittet kommer du att lägga till kod för att anropa den nya WebAPI-serverdelen och använda den för att registrera och skicka meddelanden.

1. Öppna den lösning som du slutförde i [Självstudier: Skicka meddelanden till UWP-appar med Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) i Visual Studio.
2. I Solution Explorer högerklickar du på projektet Universell Windows-plattform (UWP) och klickar sedan på **Hantera NuGet-paket**.
3. Välj **Bläddra** till vänster på sidan.
4. Skriv **Http-klient** i rutan **Sök**.
5. Klicka på **System.Net.Http** i resultatlistan och klicka på **Installera**. Slutför installationen.
6. Gå tillbaka till rutan **Sök** i NuGet och skriv **Json.net**. Installera **Newtonsoft.json**-paketet och stäng sedan NuGet Package Manager-fönstret.
7. Dubbelklicka på filen **MainPage.xaml** i **WindowsApp**-projektet i Solution Explorer och öppna den i Visual Studio-redigeraren.
8. I `MainPage.xaml` filen ersätter du `<Grid>` avsnittet med följande kod: den här koden lägger till en text ruta för användar namn och lösen ord som användaren autentiserar med. Den lägger även till textrutor för aviseringsmeddelandet och den användarnamnstagg som ska ta emot meddelandet:

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>

        <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

        <StackPanel Grid.Row="1" VerticalAlignment="Center">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                    <RowDefinition Height="Auto"/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                    <ColumnDefinition></ColumnDefinition>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                            Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                <ToggleButton Name="toggleFCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="FCM" />
                <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
            </Grid>
        </StackPanel>
    </Grid>
    ```

9. I Solution Explorer öppnar du `MainPage.xaml.cs`-filen för **(Windows 8.1)**- och **(Windows Phone 8.1)**-projekten. Lägg till följande `using`-uttryck högst upp i respektive fil:

    ```csharp
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```

10. I `MainPage.xaml.cs` för projektet **WindowsApp** lägger du till följande medlem i klassen `MainPage`. Kom ihåg att ersätta `<Enter Your Backend Endpoint>` med din faktiska serverdelsslutpunkt som hämtades tidigare. Till exempel `http://mybackend.azurewebsites.net`.

    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```

11. Lägg till koden nedan i klassen MainPage i `MainPage.xaml.cs` för **(Windows 8.1)**- och **(Windows Phone 8.1)**-projekten.

    Metoden `PushClick` är klickhanterare för knappen **Skicka Push**. Anropar serverdelen för att utlösa en avisering till alla enheter med en användarnamnstagg som matchar `to_tag`-parametern. Meddelandet skickas som JSON-innehåll i begärandetexten.

    Metoden `LoginAndRegisterClick` är klickhanterare för knappen **Logga in och registrera**. Den laggrar grundläggande autentiseringstoken (motsvarar alla token ditt autentiseringsschema använder) i lokal lagring, och använder sedan `RegisterClient` för att registrera för meddelanden som använder serverdelen.

    ```csharp
    private async void PushClick(object sender, RoutedEventArgs e)
    {
        if (toggleWNS.IsChecked.Value)
        {
            await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleFCM.IsChecked.Value)
        {
            await sendPush("fcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleAPNS.IsChecked.Value)
        {
            await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

        }
    }

    private async Task sendPush(string pns, string userTag, string message)
    {
        var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
            pns + "&to_tag=" + userTag;

        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                alert.ShowAsync();
            }
        }
    }

    private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
    {
        SetAuthenticationTokenInLocalStorage();

        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // The "username:<user name>" tag gets automatically added by the message handler in the backend.
        // The tag passed here can be whatever other tags you may want to use.
        try
        {
            // The device handle used is different depending on the device and PNS.
            // Windows devices use the channel uri as the PNS handle.
            await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            SendPushButton.IsEnabled = true;
        }
        catch (Exception ex)
        {
            MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
            alert.ShowAsync();
        }
    }

    private void SetAuthenticationTokenInLocalStorage()
    {
        string username = UsernameTextBox.Text;
        string password = PasswordTextBox.Password;

        var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
        ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
    }
    ```

12. Öppna `App.xaml.cs` och hitta anropet till `InitNotificationsAsync()` i `OnLaunched()`-händelsehanteraren. Kommentera ut eller ta bort anropet till `InitNotificationsAsync()`. Knapp hanteraren initierar meddelande registreringar:

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```

13. Högerklicka på **WindowsApp**-projektet, klicka på **Lägg till** och sedan på **Klass**. Namnge klassen `RegisterClient.cs` och generera sedan klassen genom att klicka på **OK**.

    Den här klassen omsluter de REST-anrop som krävs för att kontakta appens serverdel och registrera push-meddelanden. Den lagrar även lokalt de *registrationIds* som skapas av meddelandehubben som anges i [Registrering från din apps serverdel](/previous-versions/azure/azure-services/dn743807(v=azure.100)). Den använder en autentiseringstoken som lagras lokalt när du klickar på knappen **Logga in och registrera**.
14. Lägg till följande `using`-uttryck högst upp i filen RegisterClient.cs:

    ```csharp
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```

15. Lägg till följande kod inuti `RegisterClient` klass definitionen:

    ```csharp
    private string POST_URL;

    private class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    public RegisterClient(string backendEndpoint)
    {
        POST_URL = backendEndpoint + "/api/register";
    }

    public async Task RegisterAsync(string handle, IEnumerable<string> tags)
    {
        var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

        var deviceRegistration = new DeviceRegistration
        {
            Platform = "wns",
            Handle = handle,
            Tags = tags.ToArray<string>()
        };

        var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

        if (statusCode == HttpStatusCode.Gone)
        {
            // regId is expired, deleting from local storage & recreating
            var settings = ApplicationData.Current.LocalSettings.Values;
            settings.Remove("__NHRegistrationId");
            regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
            statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
        }

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
        {
            // log or throw
            throw new System.Net.WebException(statusCode.ToString());
        }
    }

    private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
    {
        using (var httpClient = new HttpClient())
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

            var putUri = POST_URL + "/" + regId;

            string json = JsonConvert.SerializeObject(deviceRegistration);
                            var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
    {
        var settings = ApplicationData.Current.LocalSettings.Values;
        if (!settings.ContainsKey("__NHRegistrationId"))
        {
            using (var httpClient = new HttpClient())
            {
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                if (response.IsSuccessStatusCode)
                {
                    string regId = await response.Content.ReadAsStringAsync();
                    regId = regId.Substring(1, regId.Length - 2);
                    settings.Add("__NHRegistrationId", regId);
                }
                else
                {
                    throw new System.Net.WebException(response.StatusCode.ToString());
                }
            }
        }
        return (string)settings["__NHRegistrationId"];

    }
    ```

16. Spara alla ändringar.

## <a name="test-the-application"></a>Testa programmet

1. Starta programmet i båda Windows-versionerna.
2. Ange **Användarnamn** och **Lösenord** så som visas på skärmen nedan. Det bör skilja sig från det användarnamn och lösenord som du anger på Windows Phone.
3. Klicka på **Logga in och registrera** och en verifieringsdialogruta visar att du har loggat in. Den här koden aktiverar även knappen **Skicka Push**.

    ![Skärm bild av Notification Hubs programmet som visar användar namn och lösen ord ifyllt.][14]
4. Ange sedan det registrerade användarnamnet i fältet **Mottagarens användarnamnstagg**. Skriv ett aviseringsmeddelande och klicka på **Skicka Push**.
5. Endast de enheter som har registrerats med matchande användarnamnstagg får aviseringsmeddelandet.

    ![Skärm bild av Notification Hubs programmet som visar meddelandet som skickades.][15]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig mer om push-meddelanden till specifika användare som har taggar associerade med sina registreringar. Information om hur du skickar platsbaserade meddelanden finns i nästa självstudie:

> [!div class="nextstepaction"]
>[Skicka platsbaserade push-meddelanden](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png

<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
