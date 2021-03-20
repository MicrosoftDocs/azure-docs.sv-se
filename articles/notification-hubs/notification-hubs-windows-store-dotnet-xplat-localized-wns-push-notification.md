---
title: Skicka lokaliserade meddelanden till Windows-appar med hjälp av Azure Notification Hubs | Microsoft Docs
description: Lär dig hur du använder Azure Notification Hubs för att skicka lokaliserade meddelanden med senaste nytt.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: d91320d8f78942ca916084cbf29a88818d95f531
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92314627"
---
# <a name="tutorial-send-localized-push-notifications-to-windows-apps-using-azure-notification-hubs"></a>Självstudie: skicka lokaliserade push-meddelanden till Windows-appar med hjälp av Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C #](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>Översikt

Den här kursen visar hur du kan skicka lokaliserade meddelanden till mobila enheter som registrerats för Notification Hubs-tjänsten. I kursen uppdaterar du appar som du skapat i [Självstudier: Skicka meddelanden till specifika enheter (Universal Windows Platform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) som stöd för följande scenarier:

- Windows Store-appen tillåter att klientenheter anger ett språk och prenumererar på olika nyhetskategorier.
- Serverdelsappen skickar meddelanden med hjälp av funktionerna **tag** (tagg) och **template** (mall) i Azure Notification Hubs.

När du är klar med självstudiekursen låter det mobila programmet dig registrera dig för kategorier som du är intresserad av och även ange på vilket språk meddelandena ska tas emot. Serverdelsprogrammet skickar meddelanden som är lokaliserade efter språk och enhet.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Uppdatera Windows-appen så att den stödjer språkinformation
> * Uppdatera serverdelsappen så att den skickar lokaliserade meddelanden
> * Testa appen

## <a name="prerequisites"></a>Förutsättningar

Slutför [Självstudier: Skicka meddelanden till specifika enheter (Universal Windows Platform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).

I [Självstudier: Skicka meddelanden till specifika enheter (Universal Windows Platform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) har du skapat en app som använde **taggar** att prenumerera på meddelanden för olika nyhets **kategorier**. I den här självstudiekursen använder du **mall** funktion i Notification Hubs till att enkelt leverera **lokaliserade** nyhetsmeddelanden.

På en hög nivå är mallar ett sätt att ange i vilket format en specifik enhet bör ta emot ett meddelande. Mallen anger det exakta nyttolastformatet genom att referera till egenskaper som ingår i meddelandet som skickas av appserverdelen. I den här självstudiekursen skickar serverdelsprogrammet ett språkvariantsoberoende meddelande som innehåller alla språk som stöds:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Enheterna registreras med en mall som refererar till korrekt egenskap. En Windows Store-app som du vill ta emot ett popup-meddelande på engelska registreras för följande mall med en motsvarande taggar:

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

Mer information om mallar finns i avsnittet om [push-mallar](notification-hubs-templates-cross-platform-push-messages.md).

## <a name="update-windows-app-to-support-locale-information"></a>Uppdatera Windows-appen så att den stödjer språkinformation

1. Öppna den Visual Studio-lösning som du slutförde i [Självstudier: Skicka meddelanden till specifika enheter (Universal Windows Platform)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md).
2. Uppdatera lösningens `MainPage.xaml`-fil till att inkludera en kombinationsruta för nationella inställningar:

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
3. I klassen `Notifications` lägger du till en språkvariantparameter till metoderna `StoreCategoriesAndSubscribe` och `SubscribeToCategories`.

    ```csharp
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(locale, categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    I stället för att anropa metoden `RegisterNativeAsync` anropar du `RegisterTemplateAsync`. Du registrerar ett visst meddelandeformat där mallen beror på språket. Du kan också ge mallen ett namn (”localizedWNSTemplateExample”), eftersom du kanske vill registrera mer än en mall (t.ex. en för popup-meddelanden och en för paneler). Du måste också namnge dem att kunna uppdatera eller ta bort dem.

    Om en enhet registrerar flera mallar med samma tagg resulterar ett inkommande meddelande som är inriktat på den taggen i att flera meddelanden levereras till enheten (ett för varje mall). Detta är användbart när samma logiska meddelande måste resultera i flera visuella meddelanden, t.ex. att visa både en aktivitetsikon och ett popup-meddelande i ett Windows Store-program.
4. Hämta det lagrade språket genom att lägga till följande metod:

    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

5. I filen `MainPage.xaml.cs` uppdaterar du hanteraren för knappklick till att hämta det aktuella värdet för kombinationsrutan för nationella inställningar och ange den till anropet till klassen `Notifications`:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
6. I filen `App.xaml.cs` uppdaterar du slutligen metoden `InitNotificationsAsync` till att hämta de nationella inställningarna och använda den vid prenumeration:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```



## <a name="run-the-uwp-application"></a>Kör UWP-programmet

1. Kör Universell Windows-plattform-programmet. Vänta tills du ser meddelandet **Registreringen lyckades**.

    ![Mobilprogram och registrering](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
2. Välj **kategorier** och **språk** och klicka på **Prenumerera**. Appen konverterar de valda kategorierna till taggar och begär en ny enhetsregistrering för de valda taggarna från meddelandehubben.

    ![Mobilprogram](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
3. En **bekräftelse** om **prenumerationerna** visas.

    ![Prenumerationsmeddelande](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)

## <a name="update-console-app-to-send-localized-notifications"></a>Uppdatera konsol program för att skicka lokaliserade meddelanden

När du skickar mallmeddelanden behöver du bara ange en uppsättning egenskaper. I den här självstudiekursen skickar serverdelsprogrammet en uppsättning egenskaper som innehåller den lokaliserade versionen av senaste nytt:

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

I det här avsnittet uppdaterar du konsolprogramsprojektet i lösningen. Ändra metoden `SendTemplateNotificationAsync` i den konsolapp som du skapade tidigare med följande kod:

> [!IMPORTANT]
> Ange namn och anslutningssträng med fullständig åtkomst för din meddelandehubb i koden.

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(
        "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications.
    // This includes APNS, FCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Detta enkla anrop levererar de lokaliserade nyheterna till **alla** enheter, oavsett plattform, eftersom din meddelandehubb skapar och levererar korrekt intern nyttolast för alla enheter som prenumererar på en specifik tagg.

## <a name="run-console-app-to-send-localized-notification"></a>Köra en konsol app för att skicka lokaliserad avisering
Kör- **konsol programmet** för att skicka meddelanden för varje kategori och för varje språk som stöds. Kontrollera att du bara får ett meddelande i de kategorier som du prenumererar på och meddelandet gäller för det språk du valt.

![Meddelanden](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig mer om lokaliserade push-meddelanden till specifika enheter som har taggar associerade med sina registreringar. Information om hur du skickar meddelanden till specifika användare som kanske använder mer än en enhet finns i följande självstudiekurs:

> [!div class="nextstepaction"]
>[Skicka push-meddelanden till specifika användare](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)