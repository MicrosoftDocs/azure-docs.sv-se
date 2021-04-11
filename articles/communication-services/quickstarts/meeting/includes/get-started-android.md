---
title: Snabb start – Lägg till ett team som är möte i en Android-app med Azure Communication Services
description: I den här snabb starten får du lära dig hur du använder Azure Communication Services Teams Inbäddnings bibliotek för Android.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5ac4c53550468d33e9ed533303749d29e772d766
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108491"
---
I den här snabb starten får du lära dig hur du ansluter till ett team möte med hjälp av Azure Communication Services Teams Inbäddnings bibliotek för Android.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio)för att skapa ett Android-program.
- En distribuerad kommunikations tjänst resurs. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- En [token för användar åtkomst](../../access-tokens.md) för Azure Communication Service.

## <a name="setting-up"></a>Konfigurera

### <a name="create-an-android-app-with-an-empty-activity"></a>Skapa en Android-app med en tom aktivitet

Från Android Studio väljer du starta ett nytt Android Studio-projekt.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Skärm bild som visar knappen &quot;starta ett nytt Android Studio projekt&quot; markerad i Android Studio.":::

Välj projekt mal len "Tom aktivitet" under "telefon och surfplatta".

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Skärm bild som visar alternativet &quot;Tom aktivitet&quot; markerat på skärmen för projekt mal len.":::

Namnge projektet `TeamsEmbedAndroidGettingStarted` , ange språk som Java och välj lägsta SDK för "API 21: Android 5,0 (Lollipop)" eller större.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Skärm bild som visar alternativet &quot;Tom aktivitet&quot; markerat i projekt mal len skärm 2.":::


### <a name="install-the-azure-package"></a>Installera Azure-paketet

Lägg till följande rader i avsnitten beroenden och Android på din app Level-version. gradle

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
    ...
}
```

### <a name="install-the-teams-embed-package"></a>Installera Teams Inbäddnings paketet

Ladda ned `MicrosoftTeamsSDK` paketet.

Zippa sedan upp mappen MicrosoftTeamsSDK i mappen Projects app. Till exempel `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Lägg till Teams Inbäddnings paket i build. gradle

`build.gradle`Lägg till följande rad i slutet av filen på din app-nivå:

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

Synkronisera projekt med gradle-filer.

### <a name="create-application-class"></a>Skapa program klass

Skapa en ny Java-klassfil med namnet `TeamsEmbedAndroidGettingStarted` . Detta är program klassen som måste utökas `TeamsSDKApplication` . [Android-dokumentation](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Skärm bild som visar var du kan skapa program klassen i Android Studio":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>Uppdatera teman

Ange format namnet `AppTheme` i både dina `theme.xml` och `theme.xml (night)` filer.

:::image type="content" source="../media/android/theme-settings.png" alt-text="Skärm bild som visar theme.xml-filer i Android Studio":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>Lägg till behörigheter i program manifestet

Lägg till `RECORD_AUDIO` behörigheten för ditt program manifest ( `app/src/main/AndroidManifest.xml` ):  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>Lägg till app-namn och tema i program manifestet

Lägg till xmlns: tools = http://schemas.android.com/tools i manifestet.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

Lägg till `.TeamsEmbedAndroidGettingStarted` i, `android:name` `android:name` `tools:replace` och ändra `android:theme` till `@style/AppTheme`

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>Konfigurera layouten för appen

Skapa en knapp med ID för `join_meeting` . Navigera till ( `app/src/main/res/layout/activity_main.xml` ) och ersätt innehållet i filen med följande:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Skapa ramverk och bindningar för huvud aktivitet

När layouten har skapats kan grundläggande ramverk för aktiviteten läggas till tillsammans med obligatoriska bindningar. Aktiviteten hanterar begär ande körnings behörigheter, skapar Mötes klienten och ansluter till ett möte när knappen trycks ned. Var och en kommer att omfattas i sitt eget avsnitt. 

`onCreate`Metoden kommer att åsidosättas för att anropa `getAllPermissions` och `createAgent` även lägga till bindningar för `Join Meeting` knappen. Detta inträffar bara en gång när aktiviteten skapas. Mer information om `onCreate` finns i hand boken [förstå aktivitetens livs cykel](https://developer.android.com/guide/components/activities/activity-lifecycle).

Navigera till **MainActivity. java** och ersätt innehållet med följande kod:

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>Begär behörigheter vid körning

För Android 6,0 och högre (API-nivå 23) och `targetSdkVersion` 23 eller högre beviljas behörigheter vid körning i stället för när appen installeras. För att kunna stödja detta `getAllPermissions` kan implementeras för att anropa `ActivityCompat.checkSelfPermission` och `ActivityCompat.requestPermissions` för varje nödvändig behörighet.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> När du utformar din app bör du tänka på när behörigheterna ska begäras. Behörigheter bör begäras eftersom de behövs, inte i förväg. Mer information finns i [hand boken för Android-behörigheter.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services Teams Inbäddnings bibliotek:

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient| MeetingUIClient är den huvudsakliga start punkten för Teams Inbäddnings biblioteket. |
| MeetingJoinOptions | MeetingJoinOptions används för konfigurerbara alternativ som visnings namn. |
| CallState | CallState används för att rapportera ändringar i anrops tillstånd. Alternativen är följande:,, `connecting` `waitingInLobby` `connected` och `ended` . |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>Skapa en MeetingClient från token för användar åtkomst

En autentiserad Mötes klient kan instansieras med en åtkomsttoken för användare. Den här token skapas vanligt vis av en tjänst med autentisering som är specifik för programmet. Mer information om åtkomsttoken för användare finns i Guide för [användar åtkomst-token](../../access-tokens.md) . För snabb starten ersätter du `<USER_ACCESS_TOKEN>` med en användartoken som skapats för Azure Communication Service-resursen.

```java
private void createMeetingClient() {
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>Uppdatera token uppdatera

Skapa en metod som anropas `tokenRefresher` . Skapa sedan en `fetchToken` metod för att hämta användartoken. [Du hittar anvisningar om hur du gör detta här](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>Hämta team Mötes länken

Länken Teams möte kan hämtas med Graph API: er. Detta beskrivs i [Graph-dokumentationen](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Kommunikations tjänsterna som anropar SDK accepterar en länk till en fullständig Teams möte. Den här länken returneras som en del av `onlineMeeting` resursen, som är tillgänglig under [ `joinWebUrl` egenskapen](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) . du kan också hämta nödvändig Mötes information från URL: en **till kopplings mötet** i team mötets inbjudan.

## <a name="start-a-meeting-using-the-meeting-client"></a>Starta ett möte med hjälp av mötes klienten

Anslutning till ett möte kan göras via `MeetingClient` , och kräver bara a `meetingURL` och `JoinOptions` . Ersätt `<MEETING_URL>` med en Teams Mötes-URL.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>Starta appen och ansluta till ett möte

Nu kan du starta appen med hjälp av knappen "kör app" i verktygsfältet (Shift + F10). 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="Skärm bild som visar det färdiga programmet.":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="Skärm bild som visar det färdiga programmet efter att mötet har anslutits.":::

## <a name="sample-code"></a>Exempelkod

Du kan ladda ned exempel appen från [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started)
