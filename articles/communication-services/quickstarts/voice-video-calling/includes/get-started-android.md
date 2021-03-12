---
title: Snabb start – Lägg till VOIP-anrop till en Android-app med Azure Communication Services
description: I den här självstudien får du lära dig hur du använder Azure Communication Services som anropar klient bibliotek för Android
author: matthewrobertson
ms.author: marobert
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: cde9dd719bb614d9838e50080d4bd9cd758389f4
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021674"
---
I den här snabb starten får du lära dig hur du startar ett samtal med Azure Communication Services som anropar klient bibliotek för Android.

> [!NOTE]
> Det här dokumentet använder version 1.0.0 – beta. 8 av det anropande klient biblioteket.

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

Välj minsta klient bibliotek för "API 26: Android 8,0 (Oreo)" eller större.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Skärm bild som visar alternativet &quot;Tom aktivitet&quot; markerat i projekt mal len skärm 2.":::


### <a name="install-the-package"></a>Installera paketet

<!-- TODO: update with instructions on how to download, install and add package to project -->
Leta upp build. gradle på projekt nivå och se till att lägga till `mavenCentral()` i listan över databaser under `buildscript` och `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Sedan lägger du till följande rader i avsnitten beroenden och Android i din modulnivå build. gradle.

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>Lägg till behörigheter i program manifestet

För att kunna begära behörigheter som krävs för att ringa ett samtal måste de först deklareras i applikations manifestet ( `app/src/main/AndroidManifest.xml` ). Ersätt innehållet i filen med följande:

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our calling client library depends on the Apache HTTP client library.
When targeting Android client library 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="set-up-the-layout-for-the-app"></a>Konfigurera layouten för appen

Två indata krävs: en text inmatning för ID för inbjudan och en knapp för att placera anropet. Dessa kan läggas till via designern eller genom att redigera XML-layouten. Skapa en knapp med ID `call_button` och text inmatat för `callee_id` . Navigera till ( `app/src/main/res/layout/activity_main.xml` ) och ersätt innehållet i filen med följande:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>Skapa ramverk och bindningar för huvud aktivitet

Med layouten skapad kan bindningarna läggas till samt grundläggande ramverk för aktiviteten. Aktiviteten hanterar begär ande körnings behörigheter, skapar anrops agenten och placerar samtalet när knappen trycks ned. Var och en kommer att omfattas i sitt eget avsnitt. `onCreate`Metoden kommer att åsidosättas för att anropa `getAllPermissions` och `createAgent` även lägga till bindningarna för anrops knappen. Detta inträffar bara en gång när aktiviteten skapas. Mer information om `onCreate` finns i hand boken [förstå aktivitetens livs cykel](https://developer.android.com/guide/components/activities/activity-lifecycle).

Navigera till **MainActivity. java** och ersätt innehållet med följande kod:

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
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
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> När du utformar din app bör du tänka på när behörigheterna ska begäras. Behörigheter bör begäras eftersom de behövs, inte i förväg. Mer information finns i [hand boken för Android-behörigheter.](https://developer.android.com/training/permissions/requesting)

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services som anropar klient biblioteket:

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient är den huvudsakliga start punkten för det anropande klient biblioteket.|
| CallAgent | CallAgent används för att starta och hantera samtal. |
| CommunicationUserCredential | CommunicationUserCredential används som token-autentiseringsuppgifter för att instansiera CallAgent.|
| CommunicationIdentifier | CommunicationIdentifier används som en annan typ av deltagare som skulle kunna vara en del av ett samtal.|

## <a name="create-an-agent-from-the-user-access-token"></a>Skapa en agent från åtkomsttoken för användare

Med användartoken kan en autentiserad anrops agent instansieras. Vanligt vis skapas denna token från en tjänst med en autentisering som är specifik för programmet. Mer information om åtkomsttoken för användar åtkomst finns i hand boken för [användar åtkomst-token](../../access-tokens.md) . För snabb starten ersätter du `<User_Access_Token>` med en användartoken som skapats för Azure Communication Service-resursen.

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>Starta ett anrop med hjälp av anrops agenten

Att placera samtalet kan göras via anrops agenten och behöver bara tillhandahålla en lista med ID: n för inbjudan och anrops alternativen. För snabb starten används standard alternativen för anrop utan video och ett ID för en inbjudan från text ingången.

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.startCall(
        getApplicationContext(),
        new CommunicationUserIdentifier[] {new CommunicationUserIdentifier(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>Starta appen och anropa ekot-roboten

Nu kan du starta appen med hjälp av knappen "kör app" i verktygsfältet (Shift + F10). Kontrol lera att du kan placera samtal genom att anropa `8:echo123` . Ett förregistrerat meddelande spelas upp och upprepar tillbaka ditt meddelande till dig.

:::image type="content" source="../media/android/quickstart-android-call-echobot.png" alt-text="Skärm bild som visar det färdiga programmet.":::

## <a name="sample-code"></a>Exempelkod

Du kan ladda ned exempel appen från [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add%20Voice%20Calling)
