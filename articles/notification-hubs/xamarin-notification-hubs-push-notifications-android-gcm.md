---
title: Skicka push-meddelanden till Xamarin. Android-appar med hjälp av Azure Notification Hubs | Microsoft Docs
description: I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin-Android-app.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: matthewp
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: e7d4206de1e097c30e9f5e96bbd935e94892ce0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98221042"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>Självstudie: skicka push-meddelanden till Xamarin. Android-appar med hjälp av Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin.Android-app. Du skapar en tom Xamarin.Android-app som tar emot push-meddelanden via Firebase Cloud Messaging (FCM). Du använder meddelandehubben för att sända push-meddelanden till alla enheter som kör appen. Den färdiga koden finns tillgänglig i exemplet [NotificationHubs-app](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa ett Firebase-projekt och aktivera Firebase Cloud Messaging
> * Skapa en meddelandehubb
> * Skapa en Xamarin.Android-app och anslut den till meddelandehubben
> * Skicka testmeddelanden från Azure Portal

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* [Visual Studio med Xamarin] på Windows eller [Visual Studio för Mac] på OS X.
* Aktivt Google-konto

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Skapa ett Firebase-projekt och aktivera Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>Skapa en meddelandehubb

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>Konfigurera inställningar för GCM/FCM för Notification Hub

1. Välj **Google (GCM/FCM)/** i avsnittet **Inställningar** på den vänstra menyn.
2. Ange **Server nyckeln** som du antecknade från Google Firebase-konsolen.
3. Välj **Spara** i verktygsfältet.

    ![Skärm bild av Notification Hub i Azure Portal med alternativet Google G C M F C M markerat och beskrivs i rött.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Meddelandehubben har konfigurerats för att fungera med FCM och du har anslutningssträngar för att registrera din app för att både ta emot meddelanden och skicka push-meddelanden.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Skapa en Xamarin.Android-app och anslut den till meddelandehubben

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Skapa Visual Studio-projekt och lägg till NuGet-paket

> [!NOTE]
> De steg som beskrivs i den här självstudien gäller för Visual Studio 2017. 

1. I Visual Studio öppnar du menyn **Arkiv**. Välj **Nytt** och sedan **Projekt**. Utför följande steg i fönstret **nytt projekt** :
    1. Expandera **installerad**, **Visual C#** och klicka sedan på **Android**.
    2. Välj **Android-app (Xamarin)** i listan.
    3. Ange ett **namn** för projektet.
    4. Välj en **plats** för projektet.
    5. Välj **OK**

        ![Dialogrutan Nytt projekt](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. I dialog rutan **ny Android-app** väljer du **Tom app** och väljer **OK**.

    ![Skärm bild som markerar den tomma app-mallen.](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. Utvidga **Egenskaper** i fönstret **Solution Explorer** och klicka på **AndroidManifest.xml**. Uppdatera paketnamnet så att det matchar paketnamnet som du angav när du lade till Firebase Cloud Messaging till ditt projekt i Google Firebase-konsolen.

    ![Paketnamn i GCM](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. Gör så här för att ställa in målets Android-version för projektet på **android 10,0** : 
    1. Högerklicka på projektet och välj **Egenskaper**. 
    1. I fältet **kompilera med Android-version: (mål ramverk)** väljer du **Android 10,0**. 
    1. Välj **Ja** i meddelande rutan om du vill fortsätta med att ändra mål ramverket.
1. Lägg till nödvändiga NuGet-paket i projektet genom att följa dessa steg:
    1. Högerklicka på projektet och välj **Hantera NuGet-paket...**.
    1. Växla till fliken **installerad** , Välj **Xamarin.Android.support.design** och välj sedan **Uppdatera** i den högra rutan för att uppdatera paketet till den senaste versionen.
    1. Växla till fliken **Bläddra** . Sök efter **Xamarin. GooglePlayServices. Base**. Välj **Xamarin.GooglePlayServices.Base** i resultatlistan. Markera **Installera**.

        ![Google Play Services NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. Sök efter **Xamarin.Firebase.Messaging** i fönstret **NuGet Package Manager**. Välj **Xamarin.Firebase.Messaging** i resultatlistan. Markera **Installera**.
    7. Sök efter **Xamarin.Azure.NotificationHubs.Android**. Välj **Xamarin.Azure.NotificationHubs.Android** i resultatlistan. Markera **Installera**.

### <a name="add-the-google-services-json-file"></a>Lägg till Google Services JSON-filen

1. Kopiera den `google-services.json`-fil som du laddade ned från Google Firebase-konsolen till projektmappen.
2. Lägg till `google-services.json` i projektet.
3. Välj `google-services.json` i **Solution Explorer**-fönstret.
4. Ställ in Skapa-åtgärden på **GoogleServicesJson** i rutan **Egenskaper**. Om **GoogleServicesJson** inte visas, så stäng och starta om Visual Studio, öppna projektet på nytt och försök igen.

    ![Skapa-åtgärd för GoogleServicesJson](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>Konfigurera meddelandehubbar i projektet

#### <a name="registering-with-firebase-cloud-messaging"></a>Registering med Firebase Cloud Messaging

1. Om du migrerar från Google Cloud Messaging till Firebase `AndroidManifest.xml` kan projekt filen innehålla en inaktuell GCM-konfiguration, vilket kan orsaka meddelande duplicering. Redigera filen och ta bort följande rader inuti `<application>` avsnittet om det är tillgängligt:

    ```xml
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver"
        android:exported="false" />
    <receiver
        android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver"
        android:exported="true"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
            <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

2. Lägg till följande-uttryck **före program** elementet.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Samla in följande information för Android-appen och meddelandehubben:

   * **Lyssna anslutningssträng**: På instrumentpanelen på [Azure Portal] väljer du **Visa anslutningssträngar**. Kopiera `DefaultListenSharedAccessSignature`-anslutningssträngen för det här värdet.
   * **Hubbnamn**: Namnet på hubben från [Azure Portal]. Till exempel *mynotificationhub2*.
4. I **Solution Explorer**-fönstret högerklickar du på ditt **projekt** och väljer **Lägg till** följt av **Klass**.
5. Skapa en `Constants.cs`-klass för Xamarin-projektet och definiera följande konstantvärden i klassen. Ersätt platshållarna med värdena.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. Lägg till följande using-uttryck i `MainActivity.cs`:

    ```csharp
    using Azure.Messaging.NotificationHubs;
    ```

7. Lägg till följande egenskaper i MainActivity-klassen:

    ```csharp
    internal static readonly string CHANNEL_ID = "my_notification_channel";

8. In `MainActivity.cs`, add the following code to `OnCreate` after `base.OnCreate(savedInstanceState)`:

    ```csharp
    // Listen for push notifications
    NotificationHub.SetListener(new AzureListener());

    // Start the SDK
    NotificationHub.Start(this.Application, HubName, ConnectionString);
    ```

9. Lägg till en klass med namnet `AzureListener` på ditt projekt.
10. Lägg till följande using-uttryck i `AzureListener.cs`.

    ```csharp
    using Android.Content;
    using WindowsAzure.Messaging.NotificationHubs;
    ```

11. Lägg till följande ovanför klass deklarationen och låt klassen ärva från `Java.Lang.Object` och implementera `INotificationListener` :

    ```csharp
    public class AzureListener : Java.Lang.Object, INotificationListener
    ```

12. Lägg till följande kod inuti `MyFirebaseMessagingService` klassen för att bearbeta meddelanden som tas emot.

    ```csharp
        public void OnPushNotificationReceived(Context context, INotificationMessage message)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle(message.Title)
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(message.Body)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

1. **Skapa** ditt projekt.
1. **Kör** appen på din enhet eller en inläst emulator

## <a name="send-test-notification-from-the-azure-portal"></a>Skicka ett testmeddelande från Azure Portal

Du kan testa att ta emot meddelanden i appen med alternativet **Skicka test** i [Azure Portal]. Den skickar ett test-push-meddelande till enheten.

![Azure Portal – Skicka test](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

Push-meddelanden skickas vanligtvis via en serverdelstjänst som Mobile Services eller ASP.NET med hjälp av ett kompatibelt bibliotek. Om ett bibliotek inte är tillgängligt för Server delen kan du också använda REST API direkt för att skicka meddelanden.

## <a name="next-steps"></a>Nästa steg

I de här självstudierna har du skickat meddelanden till alla Android-enheter som är registrerade hos serverdelen. Information om hur du skickar meddelanden till specifika Android-enheter finns i följande självstudier:

> [!div class="nextstepaction"]
>[Skicka push-meddelanden till specifika enheter](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Visual Studio med Xamarin]: /visualstudio/install/install-visual-studio
[Visual Studio för Mac]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure-portalen]: https://portal.azure.com/
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android