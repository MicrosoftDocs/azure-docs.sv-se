---
title: 'Självstudie: skapa en Android-app som använder Microsoft Identity Platform för autentisering | Azure'
titleSuffix: Microsoft identity platform
description: 'I den här självstudien skapar du en Android-app som använder Microsoft Identity Platform för att logga in användare och få en åtkomsttoken för att anropa Microsoft Graph-API: et för deras räkning.'
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f3dee72180d0850ce6d920c7e3180cebcbe2f4b4
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753036"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-application"></a>Självstudie: Logga in användare och anropa Microsoft Graph API från ett Android-program

I den här självstudien skapar du en Android-app som integreras med Microsoft Identity Platform för att logga in användare och hämta en åtkomsttoken för att anropa API: et för Microsoft Graph.

När du har slutfört den här självstudien accepterar programmet inloggnings uppgifter för personliga Microsoft-konton (inklusive outlook.com, live.com och andra) samt arbets-eller skol konton från alla företag eller organisationer som använder Azure Active Directory.

I de här självstudierna har du 

> [!div class="checklist"]
> * Skapa ett Android-PROG-projekt i *Android Studio*
> * Registrera appen i Azure Portal
> * Lägg till kod som stöd för användar inloggning och utloggning
> * Lägg till kod för att anropa API: et för Microsoft Graph
> * Testa appen

## <a name="prerequisites"></a>Krav

* Android Studio 3.5 +

## <a name="how-this-tutorial-works"></a>Hur den här självstudien fungerar

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Appen i den här självstudien kommer att logga in användare och hämta data för deras räkning. Dessa data kommer att nås via en skyddad API (Microsoft Graph-API) som kräver auktorisering och skyddas av Microsoft Identity Platform.

Mer specifikt:

* Din app kommer att logga in användaren antingen via en webbläsare eller Microsoft Authenticator och Intune-företagsportal.
* Slutanvändaren kommer att godkänna de behörigheter som programmet har begärt.
* Din app kommer att utfärda en åtkomsttoken för Microsoft Graph-API: et.
* Åtkomsttoken tas med i HTTP-begäran till webb-API: et.
* Bearbeta Microsoft Graph svaret.

I det här exemplet används Microsoft Authentication Library för Android (MSAL) för att implementera autentisering: [com. Microsoft. Identity. client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

MSAL förnyar automatiskt token, leverera enkel inloggning (SSO) mellan andra appar på enheten och hanterar kontona.

Den här självstudien visar förenklade exempel på hur du arbetar med MSAL för Android. För enkelhetens skull använder den bara ett enda konto läge. Om du vill utforska mer komplexa scenarier, se ett slutfört [kod exempel för arbete](https://github.com/Azure-Samples/ms-identity-android-java/) på GitHub.

## <a name="create-a-project"></a>Skapa ett projekt
Om du inte redan har ett Android-program följer du dessa steg för att skapa ett nytt projekt.

1. Öppna Android Studio och välj **starta ett nytt Android Studio-projekt**.
2. Välj **grundläggande aktivitet** och välj **Nästa**.
3. Namnge ditt program.
4. Spara paket namnet. Du kommer att ange den senare i Azure Portal.
5. Ändra språket från **Kotlin** till **Java**.
6. Ange **lägsta API-nivå** till **API 19** eller högre och klicka på **Slutför**.
7. I projektvyn väljer du **projekt** i list rutan för att Visa käll-och icke-källfiler, öppna **app/build. gradle** och Ställ in `targetSdkVersion` på `28` .

## <a name="integrate-with-the-microsoft-authentication-library"></a>Integrera med Microsoft Authentication Library

### <a name="register-your-application"></a>Registrera ditt program

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
1. Sök efter och välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
1. Ange ett **namn** för ditt program. Användare av appen kan se det här namnet och du kan ändra det senare.
1. Välj **Register** (Registrera).
1. Under **Hantera** väljer du **autentisering**  >  **Lägg till en plattforms**-  >  **Android**.
1. Ange ditt projekts paket namn. Om du har hämtat koden är det här värdet `com.azuresamples.msalandroidapp` .
1. I avsnittet **Signature hash** på sidan **Konfigurera din Android-app** väljer du **skapa en hash för utvecklings signaturen.** och kopiera det kommando kommando som ska användas för din plattform.


     KeyTool.exe installeras som en del av Java Development Kit (JDK). Du måste också installera OpenSSL-verktyget för att köra kommandot. Läs Android- [dokumentationen om hur du genererar en nyckel](https://developer.android.com/studio/publish/app-signing#generate-key) för mer information.

1. Ange **signatur-hashen** som genereras av ett-verktyg.
1. Välj **Konfigurera** och spara **MSAL-konfigurationen** som visas på sidan med **Android-konfiguration** så att du kan ange den när du konfigurerar appen senare.  
1. Välj **Klar**.

### <a name="configure-your-application"></a>Konfigurera ditt program

1. I Android Studio projekt fönstret navigerar du till **app\src\main\res**.
1. Högerklicka på **res** och välj **ny**  >  **katalog**. Ange `raw` som det nya katalog namnet och klicka på **OK**.
1. I **app**  >  **src**  >  **main**  >  **res**  >  **RAW** skapar du en ny JSON-fil med namnet `auth_config_single_account.json` och klistrar in MSAL-konfigurationen som du sparade tidigare.

    Under omdirigerings-URI: n klistrar du in:
    ```json
      "account_mode" : "SINGLE",
    ```
    Konfigurations filen bör likna följande exempel:
    ```json
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "broker_redirect_uri_registered" : true,
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```

     Den här kursen visar bara hur du konfigurerar en app i ett enda konto läge. Läs dokumentationen om du vill ha mer information om [Single vs. Multiple Account mode](./single-multi-account.md) och [Konfigurera appen](./msal-configuration.md)

4.   >    >    >  **** Lägg till `BrowserTabActivity` aktiviteten under program texten i appens huvudAndroidManifest.xml. Med den här posten kan Microsoft anropa programmet igen när autentiseringen är klar:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Ersätt det paket namn som du registrerade i Azure Portal för `android:host=` värdet.
    Ersätt den nyckel-hash som du registrerade i Azure Portal för `android:path=` värdet. Signaturens hash ska **inte** vara URL-kodad. Se till att det finns en rad `/` i början av signaturens hash.
    
    "Paket namn" du ersätter `android:host` värdet med bör se ut ungefär så här: "com. azuresamples. msalandroidapp".
    "Signaturens hash" du ersätter ditt `android:path` värde med bör se ut ungefär så här: "/1wIqXSqBj7w + h11ZifsnqwgyKrY =".
    
    Du kommer också att kunna hitta dessa värden på bladet autentisering i din app-registrering. Observera att omdirigerings-URI: n ser ut ungefär så här: "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D". Medan signatur-hashen är URL-kodad i slutet av det här värdet ska signaturens hash **inte** vara URL-kodat i ditt `android:path` värde.

## <a name="use-msal"></a>Använd MSAL

### <a name="add-msal-to-your-project"></a>Lägg till MSAL i projektet

1. I fönstret Android Studio projekt navigerar du till **app**  >  **src**  >  **build. gradle** och lägger till följande:

    ```gradle
    repositories{
        jcenter()
    }
    dependencies{
        implementation 'com.microsoft.identity.client:msal:2.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version")
    }
    ```
    [Mer om Microsoft Graph SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Nödvändiga importer

Lägg till följande överst i **appens**  >  **src**  >  **main** >  **Java**  >  **com. exempel (yourapp)**  >  **MainActivity. java**

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>Instansiera PublicClientApplication
#### <a name="initialize-variables"></a>Initiera variabler
```java
private final static String[] SCOPES = {"Files.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
I- `MainActivity` klassen, se följande onCreate ()-metod för att INSTANSIERA MSAL med hjälp av `SingleAccountPublicClientApplication` .

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount

```java
//When app comes to the foreground, load existing account to determine if user is signed in
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
Lyssna på knappar och anropa metoder eller Logga fel i enlighet med detta.
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);

        //Sign in user
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });

        //Interactive
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> Om du loggar ut med MSAL tas all känd information om en användare bort från programmet, men användaren kan fortfarande ha en aktiv session på sin enhet. Om användaren försöker logga in igen kan de se inloggnings gränssnittet, men kanske inte behöver ange sina autentiseringsuppgifter på nytt eftersom sessionen fortfarande är aktiv.

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Motringning används för interaktiva begär Anden.

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Motringning används för tysta begär Anden
```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Anropa Microsoft Graph API

Följande kod visar hur du anropar GraphAPI med hjälp av Graph SDK.

### <a name="callgraphapi"></a>callGraphAPI

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Lägga till användargränssnitt
### <a name="activity"></a>Aktivitet
Om du vill modellera ditt användar gränssnitt från den här självstudien tillhandahåller följande metoder en guide för att uppdatera text och lyssna på knappar.

#### <a name="updateui"></a>updateUI
Aktivera/inaktivera knappar baserat på inloggnings status och ange text.
```java
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Metod för att uppdatera text i UI för att avspegla utloggning.

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout

Exempel `activity_main.xml` fil för att Visa knappar och text rutor.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Testa appen

### <a name="run-locally"></a>Lokal körning

Bygg och distribuera appen till en test enhet eller emulator. Du bör kunna logga in och hämta token för Azure AD eller personliga Microsoft-konton.

När du har loggat in visar appen de data som returneras från Microsoft Graph `/me` slut punkten.
PR 4
### <a name="consent"></a>Samtycke

Första gången användaren loggar in i din app uppmanas de av Microsoft-identiteten att godkänna de behörigheter som begärs. Vissa Azure AD-klienter har inaktiverat användar medgivande som kräver att administratörer samtycker till alla användares räkning. För att stödja det här scenariot måste du antingen skapa en egen klient eller få administratörs tillåtelse.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort app-objektet som du skapade i steget [Registrera ditt program](#register-your-application) när de inte längre behövs.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att skapa mobilappar som anropar skyddade webb-API: er i vår scenario serie med flera delar.

> [!div class="nextstepaction"]
> [Scenario: mobil program som anropar webb-API: er](scenario-mobile-overview.md)
