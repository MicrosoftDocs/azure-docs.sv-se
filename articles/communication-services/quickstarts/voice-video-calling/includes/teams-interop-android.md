---
title: Snabbstart – Delta i ett Teams-möte från en Android-app
description: I den här självstudien lär du dig hur du ansluter till ett Teams-möte med hjälp Azure Communication Services Anropa SDK för Android
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: af1794a2f9eeaa1b1de2c62d5131c722dfd152a5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564463"
---
I den här snabbstarten lär du dig hur du ansluter till ett Teams-möte med hjälp Azure Communication Services Anropa SDK för Android.

## <a name="prerequisites"></a>Förutsättningar

- En fungerande [Communication Services anropar Android-appen](../getting-started-with-calling.md).
- En [Teams-distribution](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls"></a>Lägga till ui-kontroller i Teams

Ersätt koden i activity_main.xml med följande kodfragment. Textrutan används för att ange Kontext för Teams-möte och knappen används för att ansluta till det angivna mötet:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <EditText
        android:id="@+id/teams_meeting_link"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Teams meeting link"
        android:inputType="textUri"
        android:layout_marginTop="100dp"
        android:layout_marginHorizontal="20dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginBottom="70dp"
        android:gravity="center"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent">

        <Button
            android:id="@+id/join_meeting_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Join Meeting" />

        <Button
            android:id="@+id/hangup_button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hangup" />

    </LinearLayout>

    <TextView
        android:id="@+id/call_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="40dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/recording_status_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

## <a name="enable-the-teams-ui-controls"></a>Aktivera gränssnittskontrollerna i Teams

Ersätt innehållet i `MainActivity.java` med följande kodfragment:

```java

package com.contoso.acsquickstart;

import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

import java.util.ArrayList;
import java.util.concurrent.ExecutionException;

import com.azure.android.communication.calling.Call;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.HangUpOptions;
import com.azure.android.communication.calling.JoinCallOptions;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.TeamsMeetingLinkLocator;

public class MainActivity extends AppCompatActivity {
    private static final String[] allPermissions = new String[] { Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE };
    private static final String UserToken = "<User_Access_Token>";

    TextView callStatusBar;
    TextView recordingStatusBar;

    private CallAgent agent;
    private Call call;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();

        Button joinMeetingButton = findViewById(R.id.join_meeting_button);
        joinMeetingButton.setOnClickListener(l -> joinTeamsMeeting());

        Button hangupButton = findViewById(R.id.hangup_button);
        hangupButton.setOnClickListener(l -> leaveMeeting());

        callStatusBar = findViewById(R.id.call_status_bar);
        recordingStatusBar = findViewById(R.id.recording_status_bar);
    }

    /**
     * Join Teams meeting
     */
    private void joinTeamsMeeting() {
        if (UserToken.startsWith("<")) {
            Toast.makeText(this, "Please enter token in source code", Toast.LENGTH_SHORT).show();
            return;
        }

        EditText calleeIdView = findViewById(R.id.teams_meeting_link);
        String meetingLink = calleeIdView.getText().toString();
        if (meetingLink.isEmpty()) {
            Toast.makeText(this, "Please enter Teams meeting link", Toast.LENGTH_SHORT).show();
            return;
        }

        JoinCallOptions options = new JoinCallOptions();
        TeamsMeetingLinkLocator teamsMeetingLinkLocator = new TeamsMeetingLinkLocator(meetingLink);
        call = agent.join(
                getApplicationContext(),
                teamsMeetingLinkLocator,
                options);
        call.addOnStateChangedListener(p -> setCallStatus(call.getState().toString()));
        call.addOnIsRecordingActiveChangedListener(p -> setRecordingStatus(call.isRecordingActive()));
    }

    /**
     * Leave from the meeting
     */
    private void leaveMeeting() {
        try {
            call.hangUp(new HangUpOptions()).get();
        } catch (ExecutionException | InterruptedException e) {
            Toast.makeText(this, "Unable to leave meeting", Toast.LENGTH_SHORT).show();
        }
    }

    /**
     * Create the call agent
     */
    private void createAgent() {
        try {
            CommunicationTokenCredential credential = new CommunicationTokenCredential(UserToken);
            agent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
        } catch (Exception ex) {
            Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
        }
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        ArrayList<String> permissionsToAskFor = new ArrayList<>();
        for (String permission : allPermissions) {
            if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
                permissionsToAskFor.add(permission);
            }
        }
        if (!permissionsToAskFor.isEmpty()) {
            ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
        }
    }

    /**
     * Ensure all permissions were granted, otherwise inform the user permissions are missing.
     */
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, int[] grantResults) {
        boolean allPermissionsGranted = true;
        for (int result : grantResults) {
            allPermissionsGranted &= (result == PackageManager.PERMISSION_GRANTED);
        }
        if (!allPermissionsGranted) {
            Toast.makeText(this, "All permissions are needed to make the call.", Toast.LENGTH_LONG).show();
            finish();
        }
    }

    /**
     * Shows call status in status bar
     */
    private void setCallStatus(String status) {
        runOnUiThread(() -> callStatusBar.setText(status));
    }

    /**
     * Shows recording status status bar
     */
    private void setRecordingStatus(boolean status) {
        if (status == true) {
            runOnUiThread(() -> recordingStatusBar.setText("This call is being recorded"));
        }
        else {
            runOnUiThread(() -> recordingStatusBar.setText(""));
        }
    }
}

```

## <a name="get-the-teams-meeting-link"></a>Hämta länken för Teams-mötet

Teams-möteslänken kan hämtas med Graph API:er. Detta beskrivs i [Graph-dokumentationen.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)
Den Communication Services anropande SDK accepterar en fullständig Teams-möteslänk. Den här länken returneras som en del `onlineMeeting` av resursen som är tillgänglig under [ `joinWebUrl` egenskapen](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Du kan också hämta nödvändig mötesinformation från **URL:en för kopplingsmöte** i själva Teams-mötesbjudan.

## <a name="launch-the-app-and-join-teams-meeting"></a>Starta appen och anslut till Teams-mötet

Appen kan nu startas med knappen "Kör app" i verktygsfältet (Skift + F10). Du bör se följande:

:::image type="content" source="../media/android/acs-join-teams-meeting-quickstart.png" alt-text="Skärmbild som visar det färdiga programmet.":::

Infoga Teams-kontexten i textrutan och tryck på *Anslut till möte* för att ansluta till Teams-mötet inifrån ditt Communication Services program.
