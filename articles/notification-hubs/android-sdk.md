---
title: Skicka push-meddelanden till Android med Azure Notification Hubs och Firebase SDK-version 1.0.0-preview1
description: I den här självstudien får du lära dig hur du använder Azure Notification Hubs och Google Firebase Cloud Messaging för att skicka push-meddelanden till Android-enheter (version 1.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 31a411cbcecab8192643f86b6b54d09ac03e7f45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581720"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>Självstudie: skicka push-meddelanden till Android-enheter med Firebase SDK-version 1.0.0-preview1

Den här självstudien visar hur du använder Azure Notification Hubs och den uppdaterade versionen av Firebase Cloud Messaging (FCM) SDK (version 1.0.0-preview1) för att skicka push-meddelanden till ett Android-program. I den här självstudien skapar du en tom Android-app som tar emot push-meddelanden med Firebase Cloud Messaging (FCM).

Du kan hämta den färdiga koden för den här självstudien från [GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh).

Den här självstudien omfattar följande steg:

- Skapa ett Android Studio-projekt.
- Skapa ett Firebase-projekt som har stöd för Firebase Cloud Messaging.
- Skapa en meddelandehubb.
- Anslut din app till hubben.
- Testa appen.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/).

Du behöver också följande objekt:

- Den senaste versionen av [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797) rekommenderas.
- Lägsta support är API-nivå 19.

## <a name="create-an-android-studio-project"></a>Skapa ett Android Studio-projekt

Det första steget är att skapa ett projekt i Android Studio:

1. Starta Android Studio.

2. Välj **fil**, Välj **ny** och sedan nytt **projekt**.

3. På sidan **Välj ditt projekt väljer du** **Tom aktivitet** och väljer sedan **Nästa**.

4. Gör följande på sidan **Konfigurera ditt projekt** :
   1. Ange ett namn på programmet.
   2. Ange en plats där du vill spara projektfilerna.
   3. Välj **Slutför**.

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="Konfigurera projekt":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>Skapa ett Firebase-projekt som har stöd för FCM

1. Logga in på [Firebase-konsolen](https://firebase.google.com/console/). Skapa ett nytt Firebase-projekt om du inte redan har ett.

2. När du har skapat ett projekt väljer du **Lägg till Firebase till din Android-app**.

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="Lägg till Firebase":::

3. På sidan **Lägg till Firebase till din Android-app** gör du följande:

   1. För **namn på Android-paket** kopierar du värdet för **applicationId** i programmets **build. gradle** -fil. I det här exemplet är det `com.fabrikam.fcmtutorial1app` .

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="Ange paket namn":::

   2. Välj **Registrera app**.

4. Välj **hämta google-services.jspå**, spara filen i **app** -mappen i projektet och välj sedan **Nästa**.

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="Hämta Google-tjänst":::

5. Välj kugghjulet för ditt projekt i Firebase-konsolen. Välj sedan **Projektinställningar**.

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="Projektinställningar":::

6. Om du inte har hämtat **google-services.js** filen till mappen **app** i Android Studio-projektet kan du göra det på den här sidan.

7. Växla till fliken **Cloud Messaging** .

8. Kopiera och spara **Server nyckeln** för senare användning. Du använder det här värdet för att konfigurera hubben.

## <a name="configure-a-notification-hub"></a>Konfigurera en meddelandehubb

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **alla tjänster** på den vänstra menyn och välj sedan **Notification Hubs** i avsnittet **mobil** . Välj stjärn ikonen bredvid tjänst namnet för att lägga till tjänsten i **Favoriter** -avsnittet på den vänstra menyn. När du har lagt till **Notification Hubs** i **Favoriter** väljer du den på den vänstra menyn.

3. På sidan **Notification Hubs** väljer du **Lägg till** i verktygsfältet.

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="Lägg till hubb":::

4. Gör följande på sidan **Notification Hubs** :

   1. Ange ett namn i **Notification Hub**.

   2. Ange ett namn i **skapa ett nytt namn område**. En namnrymd innehåller en eller flera hubbar.

   3. Välj ett värde i list rutan **plats** . Det här värdet anger den plats där du vill skapa hubben.

   4. Välj en befintlig resurs grupp i **resurs gruppen** eller skapa en ny.

   5. Välj **Skapa**.

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="Skapa hubb":::

5. Välj **meddelanden** (klock ikonen) och välj sedan **gå till resurs**. Du kan också uppdatera listan på sidan **Notification Hubs** och välja hubben.

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="Välj hubb":::

6. Välj **Åtkomstprinciper** i listan. Observera att det finns två anslutnings strängar tillgängliga. Du behöver dem senare för att hantera push-meddelanden.

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="Åtkomstprinciper":::

   > [!IMPORTANT]
   > Använd inte **DefaultFullSharedAccessSignature** -principen i ditt program. Den här principen ska endast användas i appens Server del.

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Konfigurera Firebase Cloud Messaging-inställningar för hubben

1. Välj **Google (GCM/FCM)** i den vänstra rutan under **Inställningar** .

2. Ange **Server nyckeln** för det FCM-projekt som du sparade tidigare.

3. Välj **Spara** i verktygsfältet.

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="Server nyckel":::

4. Azure Portal visar ett meddelande om att navet har uppdaterats. Knappen **Spara** är inaktiverad.

Notification Hub har nu kon figurer ATS för att fungera med Firebase Cloud Messaging. Du har också de anslutnings strängar som krävs för att skicka meddelanden till en enhet och registrera en app för att ta emot meddelanden.

## <a name="connect-your-app-to-the-notification-hub"></a>Anslut appen till meddelandehubben

### <a name="add-google-play-services-to-the-project"></a>Lägga till Google Play-tjänster till projektet

1. I Android Studio väljer du **verktyg** på menyn och sedan **SDK-hanteraren**.

2. Välj mål versionen av Android SDK som används i projektet. Välj sedan **Visa paket information**.

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="SDK-hanteraren":::

3. Välj **Google API: er** om det inte redan är installerat.

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="API:er":::

4. Växla till fliken **SDK-verktyg** . Om du inte redan har installerat Google Play-tjänster väljer du **Google Play Services** som visas i följande bild. Välj sedan **Använd** för att installera. Anteckna SDK-sökvägen för användning i ett senare steg.

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Spela upp tjänster":::

5. Om du ser dialogrutan **Bekräfta ändringen** väljer du **OK**. Komponent installations programmet installerar de begärda komponenterna. Välj **Slutför** när komponenterna har installerats.

6. Välj **OK** för att stänga dialogrutan **Inställningar för nya projekt**.

### <a name="add-azure-notification-hubs-libraries"></a>Lägg till Azure Notification Hubs-bibliotek

1. I filen **build. gradle** för appen lägger du till följande rader i avsnittet beroenden:

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk-fcm:1.1.4'
   implementation 'androidx.appcompat:appcompat:1.0.0'
   ```

2. Lägg till följande lagrings plats efter avsnittet beroenden:

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>Lägg till stöd för Google Firebase

1. Lägg till följande plugin-program i slutet av filen om det inte redan finns där.

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. Välj **Synkronisera nu** i verktygsfältet.

### <a name="add-code"></a>Lägg till kod

1. Skapa ett **NotificationHubListener** -objekt som hanterar avlyssning av meddelanden från Azure Notification Hubs.

   ```csharp
   public class CustomNotificationListener implements NotificationHubListener {

      @override
      public void onNotificationReceived(Context context, RemoteMessage message) {
    
         /* The following notification properties are available. */
         Notification notification = message.getNotification();
         String title = notification.getTitle();
         String body = notification.getBody();
         Map<String, String> data = message.getData();
    
         if (message != null) {
            Log.d(TAG, "Message Notification Title: " + title);
            Log.d(TAG, "Message Notification Body: " + message);
         }

         if (data != null) {
             for (Map.Entry<String, String> entry : data.entrySet()) {
                 Log.d(TAG, "key, " + entry.getKey() + " value " + entry.getValue());
             }
         }
      }
   }
   ```

2. I `OnCreate` `MainActivity` klassen-metoden lägger du till följande kod för att starta Notification Hubs initierings processen när aktiviteten skapas:

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.initialize(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. I Android Studio väljer du **build** på Meny raden och väljer sedan **Återskapa projekt** för att se till att det inte finns några fel i koden. Om du får ett fel meddelande om **ic_launcher** -ikonen tar du bort följande-sats från AndroidManifest.xml-filen:

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. Se till att du har en virtuell enhet för att köra appen. Om du inte har ett kan du lägga till ett på följande sätt:

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="Enhets hanteraren":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="Virtuella enheter":::
   3. Kör appen på den valda enheten och kontrol lera att den har registrerats med hubben.

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="Enhetsregistrering":::

      > [!NOTE]
      > Registreringen kan Miss inledas under den första starten tills `onTokenRefresh()` metoden för instans-ID-tjänst anropas. En uppdatering bör initiera en lyckad registrering med Notification Hub.

## <a name="send-a-test-notification"></a>Skicka ett testmeddelande

Du kan skicka push-meddelanden till Notification Hub från [Azure Portal](https://portal.azure.com/), enligt följande:

1. På sidan för aviserings hubben på sidan Azure Portal väljer du **testa skicka** i **fel söknings** avsnittet.

2. I **plattformar** väljer du **Android**.

3. Välj **Skicka**. Du kan inte se ett meddelande på Android-enheten ännu eftersom du inte har kört mobilappen på den. När du har kört mobilappen väljer du knappen **Skicka** igen för att se meddelandet.

4. Se resultatet av åtgärden i listan längst ned på Portal sidan.

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="Skicka test meddelande":::

5. Du ser meddelandet på enheten.

Push-meddelanden skickas vanligt vis i en backend-tjänst, till exempel Mobile Apps eller ASP.NET med ett kompatibelt bibliotek. Om ett bibliotek inte är tillgängligt för Server delen kan du också använda REST API direkt för att skicka meddelanden.

## <a name="run-the-mobile-app-on-emulator"></a>Kör mobilappen i emulatorn

Innan du testar push-meddelanden i en emulator ser du till att din emulator-avbildning stöder den Google API-nivå som du har valt för din app. Om avbildningen inte stöder interna Google-API: er kan du få ett **SERVICE_NOT_AVAILABLE** undantag.

Kontrol lera också att du har lagt till ditt Google-konto till din aktiva emulator under **Inställningar**  >  **konton**. Annars kan dina försök att registrera med FCM resultera i ett **AUTHENTICATION_FAILED** undantag.

## <a name="next-steps"></a>Nästa steg

I den här självstudien använde du Firebase Cloud Messaging för att skicka meddelanden till alla Android-enheter som har registrerats med tjänsten. Information om hur du skickar meddelanden till specifika enheter finns i följande självstudie:

> [!div class="nextstepaction"]
>[Självstudie: skicka meddelanden till vissa användare](push-notifications-android-specific-users-firebase-cloud-messaging.md)

Följande är en lista över några andra självstudier för att skicka meddelanden:

- Azure-Mobile Apps: ett exempel på hur du skickar meddelanden från en Mobile Apps Server del som är integrerad med Notification Hubs finns i [lägga till push-meddelanden till din iOS-app](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

- ASP.NET: [använd Notification Hubs för att skicka push-meddelanden till användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).

- Azure Notification Hubs Java SDK: Se [Använda Notification Hubs från Java](notification-hubs-java-push-notification-tutorial.md) för att skicka meddelanden från Java. Det här har testats i Eclipse för Android-utveckling.

- PHP: [Använda Notification Hubs från PHP](notification-hubs-php-push-notification-tutorial.md).