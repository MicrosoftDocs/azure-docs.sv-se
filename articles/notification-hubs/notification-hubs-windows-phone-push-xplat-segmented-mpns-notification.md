---
title: Skicka push-meddelanden till vissa Windows-telefoner med Azure Notification Hubs | Microsoft Docs
description: I den här självstudiekursen kommer du att få lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till specifika (inte alla) Windows Phone 8- eller Windows Phone 8.1-enheter som har registrerats med programserverdelen.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 67700ec643a27f8ae5c581fe9de8e3295a1925e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88998245"
---
# <a name="tutorial-send-push-notifications-to-specific-windows-phones-using-azure-notification-hubs"></a>Självstudie: skicka push-meddelanden till vissa Windows-telefoner med Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till specifika Windows Phone 8- eller Windows Phone 8.1-enheter. Om du vill skicka meddelanden till Windows Phone 8.1 (utan Silverlight) så gå till [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)-versionen av den här självstudiekursen.

Du aktiverar det här scenariot genom att inkludera en eller flera *taggar* när du skapar en registrering i meddelandehubben. När meddelanden skickas till en tagg tar alla enheter som har registrerats för taggen emot meddelandet. Mer information om taggar finns [i taggar i registreringar](notification-hubs-tags-segment-push-message.md).

> [!NOTE]
> SDK:erna för Windows Phone på Notification Hubs stöder inte användning av Windows Push Notification Service (WNS) med Silverlight-appar för Windows Phone 8.1. Om du vill använda WNS (istället för MPNS) med Silverlight-appar för Windows Phone 8.1, ska du följa anvisningarna i Notification Hubs – självstudiekurs för Windows Phone Silverlight. Där används istället REST-API:er.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Lägga till kategorival i mobilappen
> * Registrera för meddelanden med taggar
> * Skicka taggade meddelanden
> * Testa appen

## <a name="prerequisites"></a>Förutsättningar

Slutför [Självstudier: Skicka meddelanden till Windows Phone-appar med hjälp av Azure Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md). I den här självstudiekursen uppdaterar du mobilprogrammet så att du kan registrera dig för olika nyhetskategorier som du är intresserad av och därmed endast få push-meddelanden för dessa kategorier.

## <a name="add-category-selection-to-the-mobile-app"></a>Lägga till kategorival i mobilappen

Det första steget är att lägga till de UI-element i din befintliga huvudsida som gör det möjligt för användaren att välja kategorier att registrera. De kategorier som valts av en användare lagras på enheten. När appen startar skapas en enhetsregistrering i din meddelandehubb med de valda kategorierna som taggar.

1. Öppna filen `MainPage.xaml` och ersätt de `Grid`-element som heter `TitlePanel` och `ContentPanel` med följande kod:

    ```xml
    <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
        <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
        <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
    </StackPanel>

    <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
            <RowDefinition Height="auto" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
        <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
        <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
        <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
        <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
        <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
2. Lägg till en klass med namnet `Notifications` i projektet. Lägg till modifieraren `public` i klassdefinitionen. Lägg sedan till följande `using`-instruktioner i den nya filen:

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    using System.IO.IsolatedStorage;
    using System.Windows;
    ```
3. Kopiera följande kod till den nya `Notifications`-klassen:

    ```csharp
    private NotificationHub hub;

    // Registration task to complete registration in the ChannelUriUpdated event handler
    private TaskCompletionSource<Registration> registrationTask;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
        return categories != null ? categories.Split(',') : new string[0];
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        var categoriesAsString = string.Join(",", categories);
        var settings = IsolatedStorageSettings.ApplicationSettings;
        if (!settings.Contains("categories"))
        {
            settings.Add("categories", categoriesAsString);
        }
        else
        {
            settings["categories"] = categoriesAsString;
        }
        settings.Save();

        return await SubscribeToCategories();
    }

    public async Task<Registration> SubscribeToCategories()
    {
        registrationTask = new TaskCompletionSource<Registration>();

        var channel = HttpNotificationChannel.Find("MyPushChannel");

        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
            channel.ChannelUriUpdated += channel_ChannelUriUpdated;

            // This is optional, used to receive notifications while the app is running.
            channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
        }

        // If channel.ChannelUri is not null, complete the registrationTask here.  
        // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
        if (channel.ChannelUri != null)
        {
            await RegisterTemplate(channel.ChannelUri);
        }

        return await registrationTask.Task;
    }

    async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
    {
        await RegisterTemplate(e.ChannelUri);
    }

    async Task<Registration> RegisterTemplate(Uri channelUri)
    {
        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                            "<wp:Toast>" +
                                                "<wp:Text1>$(messageParam)</wp:Text1>" +
                                            "</wp:Toast>" +
                                        "</wp:Notification>";

        // The stored categories tags are passed with the template registration.

        registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(),
            templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));

        return await registrationTask.Task;
    }

    // This is optional. It is used to receive notifications while the app is running.
    void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
    {
        StringBuilder message = new StringBuilder();
        string relativeUri = string.Empty;

        message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());

        // Parse out the information that was part of the message.
        foreach (string key in e.Collection.Keys)
        {
            message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);

            if (string.Compare(
                key,
                "wp:Param",
                System.Globalization.CultureInfo.InvariantCulture,
                System.Globalization.CompareOptions.IgnoreCase) == 0)
            {
                relativeUri = e.Collection[key];
            }
        }

        // Display a dialog of all the fields in the toast.
        System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
        {
            MessageBox.Show(message.ToString());
        });
    }
    ```

    Den här klassen använder isolerad lagring för att lagra de nyhetskategorier som den här enheten ska ta emot. Den innehåller också metoder för att registrera dig för dessa kategorier med hjälp av en [mall](notification-hubs-templates-cross-platform-push-messages.md)meddelanderegistrering.
4. I `App.xaml.cs`-projektfilen lägger du till följande egenskap i klassen `App`. Ersätt platshållarna `<hub name>` och `<connection string with listen access>` med namnet på din meddelandehubb och anslutningssträngen för *DefaultListenSharedAccessSignature* som du fick tidigare.

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

   > [!NOTE]
   > Eftersom autentiseringsuppgifterna som distribueras med ett klientprogram vanligtvis inte är säkra bör du bara distribuera nyckeln för lyssningsåtkomst med din klientapp. Lyssningsåtkomst gör det möjligt för din app att registrera sig för meddelanden, men befintliga registreringar kan inte ändras och meddelanden kan inte skickas. Nyckeln för fullständig åtkomst används i en skyddad serverdelstjänst för att skicka meddelanden och ändra befintliga registreringar.

5. I `MainPage.xaml.cs` lägger du till följande rad:

    ```csharp
    using Windows.UI.Popups;
    ```
6. Lägg till följande metod i MainPage.xaml.cs:

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldCheckBox.IsChecked == true) categories.Add("World");
        if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
        if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
        if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
        if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
        if (SportsCheckBox.IsChecked == true) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
            result.RegistrationId);
    }
    ```

    Den här metoden skapar en lista över kategorier och använder klassen `Notifications` för att lagra listan lokalt och registrera motsvarande taggar i meddelandehubben. När kategorier ändras återskapas registreringen med de nya kategorierna.

Din app kan nu lagra en uppsättning kategorier i lokal lagring på enheten och registrera med meddelandehubben närhelst användaren ändrar kategoriurvalet.

## <a name="register-for-notifications"></a>Registrera sig för meddelanden

De här stegen registreras med meddelandehubben vid start med hjälp av de kategorier som har lagrats i lokal lagring.

> [!NOTE]
> Eftersom den kanal-URI som tilldelats av Microsoft Push Notification Service (MPNS) kan ändras när som helst bör du ofta registrera dig för meddelanden för att undvika meddelandefel. Det här exemplet registrerar för meddelanden varje gång som appen startas. För appar som körs ofta, mer än en gång om dagen, kan du förmodligen hoppa över registreringen och spara bandbredd om mindre än en dag har gått sedan den tidigare registreringen.

1. Öppna filen App.xaml.cs och lägg till modifieraren `async` i metoden `Application_Launching`. Ersätt den Notification Hubs-registreringskod som du lade till i [Kom igång med Notification Hubs] med följande kod:

    ```csharp
    private async void Application_Launching(object sender, LaunchingEventArgs e)
    {
        var result = await notifications.SubscribeToCategories();

        if (result != null)
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
    }
    ```

    Den här koden ser till att appen varje gång den startas hämtar kategorier från lokal lagring och begär en registrering för dessa kategorier.
2. I projektfilen MainPage.xaml.cs lägger du till följande kod som implementerar metoden `OnNavigatedTo`:

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
        if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
        if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
        if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
        if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
        if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
    }
    ```

    Den här koden uppdaterar huvudsidan baserat på tidigare sparade kategoriers status.

Appen är nu klar och kan lagra en uppsättning kategorier i den enhetens lokala lagring som används för registrering i meddelandehubben närhelst användaren ändrar kategoriurvalet. Definiera sedan en serverdel som kan skicka kategorimeddelanden till den här appen.

## <a name="send-tagged-notifications"></a>Skicka taggade meddelanden

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Testa appen

1. Kompilera och starta appen genom att trycka på F5 i Visual Studio.

    ![Mobila appar med kategorier][1]

    Appens användargränssnitt innehåller en uppsättning växlar med vilka du kan välja vilka kategorier du vill prenumerera på.
2. Aktivera en eller flera kategoriväxlar och klicka sedan på **Prenumerera**.

    Appen konverterar de valda kategorierna till taggar och begär en ny enhetsregistrering för de valda taggarna från meddelandehubben. De registrerade kategorierna returneras och visas i en dialogruta.

    ![Prenumerationsmeddelande][2]
3. När du har fått en bekräftelse på att dina kategorier var prenumerationsklara kör du konsolappen för att skicka meddelanden för respektive kategori. Kontrollera att endast får ett meddelande för de kategorier som du prenumererar på.

    ![Aviseringsmeddelande][3]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig mer om push-meddelanden till specifika enheter som har taggar associerade med sina registreringar. Information om hur du skickar meddelanden till specifika användare som kanske använder flera enheter finns i följande självstudiekurs: 

> [!div class="nextstepaction"]
>[Skicka push-meddelanden till specifika användare](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png

<!-- URLs.-->
[Kom igång med Notification Hub]: notification-hubs-windows-mobile-push-notifications-mpns.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Phone]: ??
