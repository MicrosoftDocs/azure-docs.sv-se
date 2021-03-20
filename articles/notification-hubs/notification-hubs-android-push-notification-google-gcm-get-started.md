---
title: Skicka push-meddelanden till Android med Azure Notification Hubs och Google Cloud Messaging | Microsoft Docs
description: I de här självstudierna får du lära dig hur du använder Azure Notification Hubs och Google Firebase Cloud Messaging för att skicka push-meddelanden till Android-enheter.
services: notification-hubs
documentationcenter: android
keywords: push-meddelanden, push-meddelande, push-meddelande i android
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
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 8c97710202a448c613ab685932cb335bbaed4953
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87832662"
---
# <a name="tutorial-send-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>Självstudie: skicka push-meddelanden till Android-enheter med hjälp av Azure Notification Hubs och Google Cloud Messaging (inaktuellt)

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

> [!WARNING]
> Från och med den 10 april 2018 har Google föråldrad Google Cloud Messaging (GCM). GCM-servern och klient-API: erna är inaktuella och tas bort så snart som den 29 maj 2019. Mer information finns i [GCM och FCM vanliga frågor och svar](https://developers.google.com/cloud-messaging/faq).

## <a name="overview"></a>Översikt

I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Android-app.
Du skapar en tom Android-app som tar emot push-meddelanden med hjälp av Google Cloud Messaging (GCM).

> [!IMPORTANT]
> Google Cloud Messaging (GCM) är föråldrad och kommer [snart](https://developers.google.com/cloud-messaging/faq)att tas bort.

> [!IMPORTANT]
> Det här ämnet visar push-meddelanden med Google Cloud Messaging (GCM). Se [Skicka push-meddelanden till Android med Azure Notification Hubs och FCM](notification-hubs-android-push-notification-google-fcm-get-started.md) om du använder Googles Firebase Cloud Messaging (FCM).

Den slutförda koden för de här självstudierna kan hämtas från GitHub [här](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp).

I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapar ett projekt som stöder Google Cloud Messaging.
> * Skapa en meddelandehubb
> * Anslut appen till meddelandehubben
> * Testa appen

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).

## <a name="creating-a-project-that-supports-google-cloud-messaging"></a>Skapa ett projekt som har stöd för Google Cloud Messaging

[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>Skapa en meddelandehubb

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-setting-for-the-notification-hub"></a>Konfigurera GCM-inställningen för meddelandehubben

1. Välj **Google (GCM)** i **MEDDELANDEINSTÄLLNINGAR**.
2. Ange den **API-nyckel** du fick från Google Cloud-konsolen.
3. Välj **Spara** i verktygsfältet.

    ![Azure Notification Hubs – Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

Din meddelandehubb har nu konfigurerats för att fungera med GCM och du har anslutningssträngar för att registrera din app för att både ta emot och skicka push-meddelanden.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>Anslut appen till meddelandehubben

### <a name="create-a-new-android-project"></a>Skapa ett nytt Android-projekt

1. Starta ett nytt Android Studio-projekt i Android Studio.

   ![Android Studio – nytt projekt][13]
2. Välj formfaktorn för **Telefoner och surfplattor** samt det **Minimum SDK** som du vill stödja. Klicka på **Nästa**.

   ![Android Studio – arbetsflöde för att skapa projekt][14]
3. Välj **Tom aktivitet** som huvudsaklig aktivitet, klicka på **Nästa** och sedan på **Slutför**.

### <a name="add-google-play-services-to-the-project"></a>Lägga till Google Play-tjänster till projektet

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Lägga till bibliotek för Azure Notification Hubs

1. Lägg till följande rader i avsnittet för **beroenden** i filen `Build.Gradle` för **appen**.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. Lägg till följande lagringsplats efter avsnittet **beroenden**.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="updating-the-projects-androidmanifestxml"></a>Uppdatera projektets AndroidManifest.xml

1. För att stöda GCM, implementerar du en lyssnartjänst för instans-ID i koden som används för att [hämta registreringstoken](https://developers.google.com/cloud-messaging/) med hjälp av [Googles API för instans-ID](https://developers.google.com/instance-id/). I den här självstudien är namnet på klassen `MyInstanceIDService`.

    Lägg till följande tjänstedefinition i filen AndroidManifest.xml inuti taggen `<application>`. Ersätt platshållaren `<your package>` med det faktiska paketnamnet som visas överst i filen `AndroidManifest.xml`.
  
    ```xml
    <service android:name="<your package>.MyInstanceIDService" android:exported="false">
        <intent-filter>
            <action android:name="com.google.android.gms.iid.InstanceID"/>
        </intent-filter>
    </service>
    ```

2. När programmet har tagit emot registreringstoken för GCM från API:et för instans-ID, kan den användas för [registrering med Azure Notification Hub](notification-hubs-push-notification-registration-management.md). Registreringen utförs i bakgrunden med en `IntentService` med namnet `RegistrationIntentService`. Den här tjänsten är ansvarig för att [uppdatera GCM-registreringstoken](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).

    Lägg till följande tjänstedefinition i filen AndroidManifest.xml inuti taggen `<application>`. Ersätt platshållaren `<your package>` med det faktiska paketnamnet som visas överst i filen `AndroidManifest.xml`.

    ```xml
    <service
        android:name="<your package>.RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. Definiera en mottagare för att ta emot meddelanden. Lägg till följande mottagardefinition i filen AndroidManifest.xml inuti taggen `<application>`. Ersätt platshållaren `<your package>` med det faktiska paketnamnet som visas överst i filen `AndroidManifest.xml`.

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

4. Lägg till följande nödvändiga GCM-behörigheter under taggen `<application>`. Ersätt `<your package>` med det paketnamn som visas högst upp i filen `AndroidManifest.xml`.

    Mer information om dessa behörigheter finns i [Konfigurera en GCM-klient för Android](https://developers.google.com/cloud-messaging/).

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

    <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>
    ```

### <a name="adding-code"></a>Lägga till kod

1. I projektvyn expanderar du **app**  >  **src**  >  **main**  >  **Java**. Högerklicka på paketmappen under **java**, klicka på **Ny** och sedan klickar du på **Java-klass**. Lägg till en ny klass med namnet `NotificationSettings`.

    ![Android Studio – ny Java-klass][6]

    Uppdatera de tre platshållarna i följande kod för klassen `NotificationSettings`:

   * `SenderId`: Det projekt nummer du fick tidigare i [Google Cloud-konsolen](https://cloud.google.com/console).
   * `HubListenConnectionString`: **DefaultListenAccessSignature** anslutnings sträng för hubben. Du kan kopiera denna anslutningssträng genom att klicka på **Åtkomstprinciper** på sidan **Inställningar** i din hubb i [Azure Portal].
   * `HubName`: Använd namnet på din Notification Hub som visas på sidan hubb i [Azure Portal].

     `NotificationSettings` kod:

     ```java
     public class NotificationSettings {
        public static String SenderId = "<Your project number>";
        public static String HubName = "<Your HubName>";
        public static String HubListenConnectionString = "<Your default listen connection string>";
     }
     ```

2. Lägg till ytterligare en ny klass med namnet `MyInstanceIDService`. Den här klassen är implementeringen av lyssnartjänsten för instans-ID.

    Koden för den här klassen anropar `IntentService` för att [uppdatera GCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) i bakgrunden.

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.android.gms.iid.InstanceIDListenerService;

    public class MyInstanceIDService extends InstanceIDListenerService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.i(TAG, "Refreshing GCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. Lägg till en ny klass i projektet med namnet `RegistrationIntentService` . Den här klassen implementerar `IntentService`, som hanterar [uppdateringen av GCM-token](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) och [registreringen hos meddelandehubben](notification-hubs-push-notification-registration-management.md).

    Använd följande kod för den här klassen.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.google.android.gms.iid.InstanceID;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {
            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;

            try {
                InstanceID instanceID = InstanceID.getInstance(this);
                String token = instanceID.getToken(NotificationSettings.SenderId,
                        GoogleCloudMessaging.INSTANCE_ID_SCOPE);
                Log.i(TAG, "Got GCM Registration Token: " + token);

                // Storing the registration id that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if ((regID=sharedPreferences.getString("registrationID", null)) == null) {
                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.i(TAG, "Attempting to register with NH using token : " + token);

                    regID = hub.register(token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                    resultString = "Registered Successfully - RegId : " + regID;
                    Log.i(TAG, resultString);
                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                } else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete token refresh", e);
                // If an exception happens while fetching the new token or updating the registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. Lägg till följande `import`-uttryck i början av klassen `MainActivity`.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.google.android.gms.gcm.*;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    import android.content.Intent;
    ```

5. Lägg till följande privata medlemmar högst upp i klassen. Koden [kontrollerar tillgängligheten för Google Play-tjänster, enligt rekommendationer från Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private GoogleCloudMessaging gcm;
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    private static final String TAG = "MainActivity";
    ```

6. Lägg till följande metod i tillgängligheten för Google Play-tjänster i klassen `MainActivity`.

    ```java
    /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

7. Lägg till följande kod i din `MainActivity`-klass, som gör kontroller för Google Play Services innan din `IntentService` anropas för att hämta ditt registreringstoken för GCM och utföra registreringen hos meddelandehubben.

    ```java
    public void registerWithNotificationHubs()
    {
        Log.i(TAG, " Registering with Notification Hubs");

        if (checkPlayServices()) {
            // Start IntentService to register this application with GCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

8. Lägg till följande kod i metoden `OnCreate` för klassen `MainActivity` för att starta registreringsprocessen när aktivitet skapas.

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        registerWithNotificationHubs();
    }
    ```

9. Lägg till de ytterligare metoderna till `MainActivity` för att kontrollera appens status och rapportera statusen i din app.

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

10. I metoden `ToastNotify` används kontrollen *”Hello World”* `TextView` för att rapportera status och meddelanden på ett beständigt sätt i appen. Lägg till följande ID för den kontrollen i activity_main.xml.

    ```xml
    android:id="@+id/text_hello"
    ```

11. Lägg till en underklass för den mottagare som har definierats i AndroidManifest.xml. Lägg till ytterligare en ny klass i projektet och ge den namnet `MyHandler`.

12. Lägg till följande importuttryck längst upp i `MyHandler.java`:

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;
    import android.net.Uri;
    import android.media.RingtoneManager;
    ```

13. Lägg till följande kod för `MyHandler` -klassen, vilket gör den till en underordnad klass `com.microsoft.windowsazure.notifications.NotificationsHandler` .

    Den här koden åsidosätter metoden `OnReceive` vilket i sin tur gör att hanteraren rapporterar meddelanden som tas emot. Hanteraren skickar även push-meddelandena till Android Notification Manager genom att använda metoden `sendNotification()`. Metoden `sendNotification()` ska utföras när appen inte körs och ett meddelande har tagits emot.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }

        private void sendNotification(String msg) {

            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);

            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);

            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(ctx)
                            .setSmallIcon(R.mipmap.ic_launcher)
                            .setContentTitle("Notification Hub Demo")
                            .setStyle(new NotificationCompat.BigTextStyle()
                                    .bigText(msg))
                            .setSound(defaultSoundUri)
                            .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
    }
    ```

14. I Android Studio på Meny raden klickar du på **skapa** återskapa  >  **projekt** för att kontrol lera att det inte finns några fel i koden.

## <a name="testing-your-app"></a>Testa din app

### <a name="run-the-mobile-application"></a>Kör mobilprogrammet

1. Kör appen och lägg märke till att ett registrerings-ID rapporteras vid en lyckad registrering.

      ![Tester på Android – Kanalregistrering][18]
2. Ange ett meddelande som ska skickas till alla Android-enheter som har registrerats på hubben.

      ![Tester på Android – skicka ett meddelande][19]

3. Tryck på **Skicka meddelande**. Alla enheter där appen körs visar en `AlertDialog`-instans med push-meddelandet. Enheter där appen inte körs men som tidigare hade registrerats för att ta emot push-meddelanden får ett meddelande i Android Notification Manager. Du kan visa meddelandena genom att svepa nedåt från det övre vänstra hörnet.

      ![Tester på Android – meddelanden][21]

### <a name="test-send-push-notifications-from-the-azure-portal"></a>Testa skicka push-meddelanden från Azure Portal

Du kan testa att ta emot push-meddelanden i appen genom att skicka dem från [Azure Portal].

1. Välj **Testskicka** i avsnittet **Felsökning**.
2. Välj **Android** under **Plattformar**.
3. Välj **Skicka** för att skicka testmeddelandet.
4. Bekräfta att du ser meddelandet på Android-enheten.

    ![Azure Notification Hubs – Prova att skicka](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

#### <a name="push-notifications-in-the-emulator"></a>Push-meddelanden i emulatorn

Om du vill testa push-meddelanden inne i en emulator, måste du se till att emulatorbilden stöder den Google-API-nivå som du har valt för din app. Om avbildningen inte stöder interna Google-API:er kommer processen att avslutas med undantaget **TJÄNSTEN\_ÄR INTE\_TILLGÄNGLIG**.

Se dessutom till att du har lagt till ditt Google-konto till din aktiva emulator under **Inställningar**  >  **konton**. Annars kan försöken att registrera med GCM leda till undantaget **AUTENTISERINGEN\_MISSLYCKADES**.

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Valfritt) Skicka push-meddelanden direkt från appen

Normalt sett skickar du meddelanden med hjälp av en backend-server. I vissa fall kanske du vill kunna skicka push-meddelanden direkt från klientprogrammet. I det här avsnittet beskrivs hur du skickar meddelanden från klienten med hjälp av [Azure Notification Hub REST API](/previous-versions/azure/reference/dn223264(v=azure.100)).

1. Expandera **app**  >  **src**  >  **main**  >  **res**  >  **layout** i Android Studio projektvyn. Öppna layoutfilen för `activity_main.xml` och klicka på fliken **Text** för att uppdatera filens textinnehåll. Uppdatera den med koden nedan. Detta lägger till nya `Button`- och `EditText`-kontroller för att skicka push-meddelanden till meddelandehubben. Lägg till den här koden längst ned, precis före `</RelativeLayout>`.

    ```xml
    <Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/send_button"
    android:id="@+id/sendbutton"
    android:layout_centerVertical="true"
    android:layout_centerHorizontal="true"
    android:onClick="sendNotificationButtonOnClick" />

    <EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/editTextNotificationMessage"
    android:layout_above="@+id/sendbutton"
    android:layout_centerHorizontal="true"
    android:layout_marginBottom="42dp"
    android:hint="@string/notification_message_hint" />
    ```

2. Expandera **app**  >  **src**  >  **main**  >  **res**  >  **värden** i Android Studio projektvyn. Öppna filen `strings.xml` och lägg till strängvärden som refererar till de nya `Button`- och `EditText`-kontrollerna. Lägg till följande rader längst ned i filen, precis före `</resources>`.

    ```xml
    <string name="send_button">Send Notification</string>
    <string name="notification_message_hint">Enter notification message text</string>
    ```

3. Lägg till följande inställning till klassen `NotificationSettings` i filen `NotificationSetting.java`.

    Uppdatera `HubFullAccess` med anslutningssträngen **DefaultFullSharedAccessSignature** för hubben. Den här anslutningssträngen kan kopieras från [Azure Portal] genom att klicka på **Åtkomstprinciper** på sidan **Inställningar** för meddelandehubben.

    ```java
    public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";
    ```

4. Lägg till följande `import`-uttryck i början filen `MainActivity.java`.

    ```java
    import java.io.BufferedOutputStream;
    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.io.OutputStream;
    import java.net.HttpURLConnection;
    import java.net.URL;
    import java.net.URLEncoder;
    import javax.crypto.Mac;
    import javax.crypto.spec.SecretKeySpec;
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;
    ```

5. Lägg till följande medlemmar högst upp i klassen `MainActivity` i filen `MainActivity.java`.

    ```java
    private String HubEndpoint = null;
    private String HubSasKeyName = null;
    private String HubSasKeyValue = null;
    ```

6. Skapa en SaS-token (signatur för delad åtkomst) för att autentisera en POST-begäran för att skicka meddelanden till Notification Hub. Parsa nyckeluppgifterna från anslutningssträngen och skapa sedan ett SaS-token enligt REST API-referensen [Vanliga koncept](/previous-versions/azure/reference/dn495627(v=azure.100)). Följande kod är ett exempel på en implementering.

    I `MainActivity.java` lägger du till följande metod i klassen `MainActivity` för att parsa anslutningssträngen.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx
        * to parse the connection string so a SaS authentication token can be
        * constructed.
        *
        * @param connectionString This must be the DefaultFullSharedAccess connection
        *                         string for this example.
        */
    private void ParseConnectionString(String connectionString)
    {
        String[] parts = connectionString.split(";");
        if (parts.length != 3)
            throw new RuntimeException("Error parsing connection string: "
                    + connectionString);

        for (int i = 0; i < parts.length; i++) {
            if (parts[i].startsWith("Endpoint")) {
                this.HubEndpoint = "https" + parts[i].substring(11);
            } else if (parts[i].startsWith("SharedAccessKeyName")) {
                this.HubSasKeyName = parts[i].substring(20);
            } else if (parts[i].startsWith("SharedAccessKey")) {
                this.HubSasKeyValue = parts[i].substring(16);
            }
        }
    }
    ```

7. I `MainActivity.java` lägger du till följande metod i klassen `MainActivity` för att skapa ett SaS-token för autentisering.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx to
        * construct a SaS token from the access key to authenticate a request.
        *
        * @param uri The unencoded resource URI string for this operation. The resource
        *            URI is the full URI of the Service Bus resource to which access is
        *            claimed. For example,
        *            "http://<namespace>.servicebus.windows.net/<hubName>"
        */
    private String generateSasToken(String uri) {

        String targetUri;
        String token = null;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Using android.util.Base64 for Android Studio instead of
            // Apache commons codec
            String signature = URLEncoder.encode(
                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

            // Construct authorization string
            token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
        } catch (Exception e) {
            if (isVisible) {
                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
            }
        }

        return token;
    }
    ```

8. I `MainActivity.java` lägger du till följande metod för klassen `MainActivity` för att hantera knappen **Skicka meddelande**. Klicka på och skicka push-meddelandet till hubben med hjälp av det inbyggda REST-API:et.

    ```java
    /**
        * Send Notification button click handler. This method parses the
        * DefaultFullSharedAccess connection string and generates a SaS token. The
        * token is added to the Authorization header on the POST request to the
        * notification hub. The text in the editTextNotificationMessage control
        * is added as the JSON body for the request to add a GCM message to the hub.
        *
        * @param v
        */
    public void sendNotificationButtonOnClick(View v) {
        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
        final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";

        new Thread()
        {
            public void run()
            {
                try
                {
                    // Based on reference documentation...
                    // https://msdn.microsoft.com/library/azure/dn223273.aspx
                    ParseConnectionString(NotificationSettings.HubFullAccess);
                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                            "/messages/?api-version=2015-01");

                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();

                    try {
                        // POST request
                        urlConnection.setDoOutput(true);

                        // Authenticate the POST request with the SaS token
                        urlConnection.setRequestProperty("Authorization",
                            generateSasToken(url.toString()));

                        // Notification format should be GCM
                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");

                        // Include any tags
                        // Example below targets 3 specific tags
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags",
                        //        "tag1 || tag2 || tag3");

                        // Send notification message
                        urlConnection.setFixedLengthStreamingMode(json.length());
                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                        bodyStream.write(json.getBytes());
                        bodyStream.close();

                        // Get response
                        urlConnection.connect();
                        int responseCode = urlConnection.getResponseCode();
                        if ((responseCode != 200) && (responseCode != 201)) {
                            BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                            String line;
                            StringBuilder builder = new StringBuilder("Send Notification returned " +
                                    responseCode + " : ")  ;
                            while ((line = br.readLine()) != null) {
                                builder.append(line);
                            }

                            ToastNotify(builder.toString());
                        }
                    } finally {
                        urlConnection.disconnect();
                    }
                }
                catch(Exception e)
                {
                    if (isVisible) {
                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                    }
                }
            }
        }.start();
    }
    ```

## <a name="next-steps"></a>Nästa steg

I de här självstudierna har du skickat meddelanden till alla Android-enheter som är registrerade hos serverdelen. Information om hur du skickar meddelanden till specifika Android-enheter finns i följande självstudier:  

 > [!div class="nextstepaction"]
 > [Skicka push-meddelanden till specifika enheter](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png
[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png
[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png
[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure-portalen]: https://portal.azure.com
