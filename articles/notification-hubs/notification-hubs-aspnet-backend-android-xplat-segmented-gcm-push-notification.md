---
title: Skicka meddelanden till vissa Android-enheter med Azure Notification Hubs och Google Cloud Messaging | Microsoft Docs
description: Lär dig hur du använder Notification Hubs för att skicka meddelanden till specifika Android-enheter med hjälp av Azure Notification Hubs och Google Cloud Messaging.
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: c0c0018ac3007f77da820b9b0cecbb69c68bef31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92308309"
---
# <a name="tutorial-send-push-notifications-to-specific-android-devices-using-google-cloud-messaging-deprecated"></a>Självstudie: skicka push-meddelanden till vissa Android-enheter med hjälp av Google Cloud Messaging (inaktuell)

> [!WARNING]
> Från och med den 10 april 2018 har Google föråldrad Google Cloud Messaging (GCM). GCM-servern och klient-API: erna är inaktuella och tas bort så snart som den 29 maj 2019. Mer information finns i [GCM och FCM vanliga frågor och svar](https://developers.google.com/cloud-messaging/faq).

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Översikt

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka nyhetsmeddelanden till en Android-app. När du är klar kan du registrera dig för olika nyhetskategorier som du är intresserad av och därmed endast få push-meddelanden för dessa kategorier. Det här scenariot är ett vanligt mönster för många appar där meddelanden måste skickas till olika grupper av användare som tidigare har deklarerat intresse för dem, t.ex. RSS-läsare, appar för musiklyssnare osv.

Du aktiverar sändningsscenarier genom att inkludera en eller flera *taggar* när du skapar en registrering i meddelandehubben. När meddelanden skickas till en tagg tar alla enheter som har registrerats för taggen emot meddelandet. Eftersom taggar bara är strängar behöver de inte etableras i förväg. Mer information om taggar finns i [Notification Hubs-routning och tagguttryck](notification-hubs-tags-segment-push-message.md).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Lägga till kategorival i mobilappen.
> * Registrerad för meddelanden med taggar.
> * Skicka taggade meddelanden.
> * Testa appen

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien bygger på den app som du skapade i [Självstudie: Skicka meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Cloud Messaging][get-started]. Innan du påbörjar den här självstudien så slutför [Självstudie: Skicka meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Cloud Messaging][get-started].

## <a name="add-category-selection-to-the-app"></a>Lägga till kategorival till appen

Det första steget är att lägga till de UI-element i din befintliga huvudaktivitet som gör det möjligt för användaren att välja kategorier att registrera. De kategorier som valts av en användare lagras på enheten. När appen startar skapas en enhetsregistrering i din meddelandehubb med de valda kategorierna som taggar.

1. Öppna `res/layout/activity_main.xml file` och ersätt innehållet med följande:

    ```xml
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.example.breakingnews.MainActivity"
        android:orientation="vertical">

            <CheckBox
                android:id="@+id/worldBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_world" />
            <CheckBox
                android:id="@+id/politicsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_politics" />
            <CheckBox
                android:id="@+id/businessBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_business" />
            <CheckBox
                android:id="@+id/technologyBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_technology" />
            <CheckBox
                android:id="@+id/scienceBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_science" />
            <CheckBox
                android:id="@+id/sportsBox"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="@string/label_sports" />
            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="subscribe"
                android:text="@string/button_subscribe" />
            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Hello World!"
                android:id="@+id/text_hello"
            />
    </LinearLayout>
    ```
2. Öppna filen `res/values/strings.xml` och lägg till följande rader:

    ```xml
    <string name="button_subscribe">Subscribe</string>
    <string name="label_world">World</string>
    <string name="label_politics">Politics</string>
    <string name="label_business">Business</string>
    <string name="label_technology">Technology</string>
    <string name="label_science">Science</string>
    <string name="label_sports">Sports</string>
    ```

    Den grafiska layouten för `main_activity.xml` bör se ut som på följande bild:

    ![Skärm bild av en utvecklings miljö med en app-skärm synlig. Appen visar de nyhets kategorier som har lagts till i koden.][A1]
3. Skapa en `Notifications`-klass i samma paket som din `MainActivity`-klass.

    ```java
    import java.util.HashSet;
    import java.util.Set;

    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    import android.view.View;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private GoogleCloudMessaging gcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;

        public Notifications(Context context, String senderId, String hubName,
                                String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;

            gcm = GoogleCloudMessaging.getInstance(context);
            hub = new NotificationHub(hubName, listenConnectionString, context);
        }

        public void storeCategoriesAndSubscribe(Set<String> categories)
        {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            settings.edit().putStringSet("categories", categories).commit();
            subscribeToCategories(categories);
        }

        public Set<String> retrieveCategories() {
            SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
            return settings.getStringSet("categories", new HashSet<String>());
        }

        public void subscribeToCategories(final Set<String> categories) {
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        String regid = gcm.register(senderId);

                        String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                        hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM,
                            categories.toArray(new String[categories.size()]));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to register - " + e.getMessage());
                        return e;
                    }
                    return null;
                }

                protected void onPostExecute(Object result) {
                    String message = "Subscribed for categories: "
                            + categories.toString();
                    Toast.makeText(context, message,
                            Toast.LENGTH_LONG).show();
                }
            }.execute(null, null, null);
        }

    }
    ```

    Den här klassen använder lokal lagring för att lagra de nyhetskategorier som den här enheten ska ta emot. Den innehåller också metoder för att registrera dig för dessa kategorier.
4. I din `MainActivity`-klass tar du bort dina privata fält för `NotificationHub` och `GoogleCloudMessaging` och lägger till ett fält för `Notifications`:

    ```java
    // private GoogleCloudMessaging gcm;
    // private NotificationHub hub;
    private Notifications notifications;
    ```
5. I metoden `onCreate` tar du sedan bort initieringen av fältet `hub` och metoden `registerWithNotificationHubs`. Lägg sedan till följande rader som instansierar klassen `Notifications`.

    ```java
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mainActivity = this;

        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId,
                MyHandler.class);

        notifications = new Notifications(this, NotificationSettings.SenderId, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);

        notifications.subscribeToCategories(notifications.retrieveCategories());
    }
    ```

    Bekräfta att hubbnamnet och anslutningssträngen är korrekt inställda i klassen NotificationSettings.

    > [!NOTE]
    > Eftersom autentiseringsuppgifterna som distribueras med ett klientprogram vanligtvis inte är säkra bör du bara distribuera nyckeln för lyssningsåtkomst med din klientapp. Lyssningsåtkomst gör det möjligt för din app att registrera sig för meddelanden, men befintliga registreringar kan inte ändras och meddelanden kan inte skickas. Nyckeln för fullständig åtkomst används i en skyddad serverdelstjänst för att skicka meddelanden och ändra befintliga registreringar.

6. Lägg sedan till följande importer:

    ```java
    import android.widget.CheckBox;
    import java.util.HashSet;
    import java.util.Set;
    ```
7. Lägg till följande `subscribe`-metod om du vill hantera prenumerationsknappens klickhändelse:

    ```java
    public void subscribe(View sender) {
        final Set<String> categories = new HashSet<String>();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        if (world.isChecked())
            categories.add("world");
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        if (politics.isChecked())
            categories.add("politics");
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        if (business.isChecked())
            categories.add("business");
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        if (technology.isChecked())
            categories.add("technology");
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        if (science.isChecked())
            categories.add("science");
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        if (sports.isChecked())
            categories.add("sports");

        notifications.storeCategoriesAndSubscribe(categories);
    }
    ```

    Den här metoden skapar en lista över kategorier och använder klassen `Notifications` för att lagra listan lokalt och registrera motsvarande taggar i meddelandehubben. När kategorier ändras återskapas registreringen med de nya kategorierna.

Din app kan nu lagra en uppsättning kategorier i lokal lagring på enheten och registrera med meddelandehubben närhelst användaren ändrar kategoriurvalet.

## <a name="register-for-notifications"></a>Registrera sig för meddelanden

De här stegen registreras med meddelandehubben vid start med hjälp av de kategorier som har lagrats i lokal lagring.

> [!NOTE]
> Eftersom det registrationId som tilldelats av Google Cloud Messaging (GCM) kan ändras när som helst, bör du ofta registrera dig för meddelanden ofta och undvika meddelandefel. Det här exemplet registrerar för meddelande varje gång som appen startas. För appar som körs ofta, mer än en gång om dagen, kan du förmodligen hoppa över registreringen och spara bandbredd om mindre än en dag har gått sedan den tidigare registreringen.

1. Lägg till följande kod i slutet av metoden `onCreate` i klassen `MainActivity`:

    ```java
    notifications.subscribeToCategories(notifications.retrieveCategories());
    ```

    Den här koden ser till att appen varje gång den startas hämtar kategorier från lokal lagring och begär en registrering för dessa kategorier.
2. Uppdatera sedan metoden `onStart()` för klassen `MainActivity` enligt följande:

    ```java
    @Override
    protected void onStart() {

        super.onStart();
        isVisible = true;

        Set<String> categories = notifications.retrieveCategories();

        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
    ```

    Den här koden uppdaterar huvudaktiviteten baserat på tidigare sparade kategoriers status.

Appen är nu klar och kan lagra en uppsättning kategorier i den enhetens lokala lagring som används för registrering i meddelandehubben närhelst användaren ändrar kategoriurvalet. Definiera sedan en serverdel som kan skicka kategorimeddelanden till den här appen.

## <a name="send-tagged-notifications"></a>Skicka taggade meddelanden

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="test-the-app"></a>Testa appen

1. Kör appen på din Android-enhet eller emulator i Android Studio. Appens användargränssnitt innehåller en uppsättning växlar med vilka du kan välja vilka kategorier du vill prenumerera på.
2. Aktivera en eller flera kategoriväxlar och klicka sedan på **Prenumerera**. Appen konverterar de valda kategorierna till taggar och begär en ny enhetsregistrering för de valda taggarna från meddelandehubben. De registrerade kategorierna returneras och visas i ett popup-meddelande.

    ![Prenumerera på kategorier](./media/notification-hubs-aspnet-backend-android-breaking-news/subscribe-for-categories.png)
3. Kör .NET-konsolappen som skickar meddelanden för varje kategori. Meddelanden för de valda kategorierna visas som popup-meddelanden.

    ![Tekniknyhetsmeddelanden](./media/notification-hubs-aspnet-backend-android-breaking-news/technolgy-news-notification.png)

## <a name="next-steps"></a>Nästa steg

I den här kursen har du skickat meddelanden till specifika Android-enheter som har registrerats för kategorier. Information om hur du skickar meddelanden till specifika användare finns i följande självstudie:

> [!div class="nextstepaction"]
>[Skicka push-meddelanden till specifika användare](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure portal]: https://portal.azure.com
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)