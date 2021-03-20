---
title: Skicka push-meddelanden till vissa enheter med Azure Notification Hubs och Google Firebase Cloud Messaging | Microsoft Docs
description: Lär dig hur du använder Notification Hubs för att skicka meddelanden till vissa Android-enheter med hjälp av Azure Notification Hubs och Google Firebase Cloud Messaging (FCM).
services: notification-hubs
documentationcenter: android
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 04/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/30/2019
ms.openlocfilehash: be83471681948cbb9fe85f0d135343ab73bd72bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92315087"
---
# <a name="tutorial-send-notifications-to-specific-devices-using-notification-hubs-and-google-firebase-cloud-messaging"></a>Självstudie: skicka meddelanden till vissa enheter med hjälp av Notification Hubs och Google Firebase Cloud Messaging

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

Den här självstudien bygger på den app som du skapade i [Självstudier: push-meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md). Innan du börjar den här självstudien ska du slutföra [självstudien: push-meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Firebase Cloud Messaging](notification-hubs-android-push-notification-google-fcm-get-started.md).

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

    ![Skärm bild av en emulator som visar hur den grafiska layouten för huvud aktivitet X M L kommer att se ut.][A1]
3. Skapa en `Notifications`-klass i samma paket som din `MainActivity`-klass.

    ```java
    import java.util.HashSet;
    import java.util.Set;
    import java.util.concurrent.TimeUnit;
    
    import android.content.Context;
    import android.content.SharedPreferences;
    import android.os.AsyncTask;
    import android.util.Log;
    import android.widget.Toast;
    
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    
    public class Notifications {
        private static final String PREFS_NAME = "BreakingNewsCategories";
        private FirebaseInstanceId fcm;
        private NotificationHub hub;
        private Context context;
        private String senderId;
        public static String FCM_token = "";
        private static final String TAG = "Notifications";
    
        public Notifications(Context context, String hubName, String listenConnectionString) {
            this.context = context;
            this.senderId = senderId;
    
            fcm = FirebaseInstanceId.getInstance();
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
                        FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() {
                            @Override
                            public void onSuccess(InstanceIdResult instanceIdResult) {
                                FCM_token = instanceIdResult.getToken();
                                Log.d(TAG, "FCM Registration Token: " + FCM_token);
                            }
                        });
    
                        TimeUnit.SECONDS.sleep(1);
    
                        String templateBodyFCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
    
                        hub.registerTemplate(FCM_token,"simpleFCMTemplate", templateBodyFCM,
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
4. I din `MainActivity` klass lägger du till ett fält för `Notifications` :

    ```java
    private Notifications notifications;
    ```
5. Uppdatera sedan `onCreate` metoden som visas i följande kod. Du registrerar dig för Notification Hubs i **subscribeToCategories** -metoden i **meddelande** klassen. 

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;

        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
        notifications = new Notifications(this, NotificationSettings.HubName, NotificationSettings.HubListenConnectionString);
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
    import android.view.View;
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

1. Bekräfta att följande kod är i slutet av- `onCreate` metoden i- `MainActivity` klassen:

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
>[Skicka push-meddelanden till specifika användare](push-notifications-android-specific-users-firebase-cloud-messaging.md)

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
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