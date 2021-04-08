---
title: Skicka säkra push-meddelanden med Azure Notification Hubs
description: Lär dig hur du skickar säkra push-meddelanden till en Android-app från Azure. Kod exempel som skrivits i Java och C#.
documentationcenter: android
keywords: push-meddelande, push-meddelanden, push-meddelanden, Android push-meddelanden
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: f2d5d618fabbe7400ce825f984ace1622a524f05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88004025"
---
# <a name="send-secure-push-notifications-with-azure-notification-hubs"></a>Skicka säkra push-meddelanden med Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Översikt

> [!IMPORTANT]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Med stöd för push-meddelanden i Microsoft Azure kan du få åtkomst till en lättanvänd och utskalad push-meddelande infrastruktur med flera plattformar, vilket avsevärt fören klar implementeringen av push-meddelanden för både konsument-och företags program för mobila plattformar.

På grund av regler eller säkerhets begränsningar kan ett program ibland vilja inkludera något i meddelandet som inte kan skickas via standard infrastrukturen för push-meddelanden. I den här självstudien beskrivs hur du uppnår samma upplevelse genom att skicka känslig information via en säker, autentiserad anslutning mellan klientens Android-enhet och appens Server del.

På hög nivå är flödet följande:

- Server delen för appen:
  * Lagrar säker nytto Last i backend-databasen.
  * Skickar ID för det här meddelandet till Android-enheten (ingen säker information skickas).
- Appen på enheten när meddelandet tas emot:
  * Android-enheten kontaktar Server dels förfrågan om säker nytto Last.
  * Appen kan visa nytto lasten som ett meddelande på enheten.

Det är viktigt att Observera att i föregående flöde (och i den här självstudien) förutsätts att enheten lagrar en autentiseringstoken i den lokala lagringen efter att användaren har loggat in. Den här metoden garanterar en sömlös upplevelse eftersom enheten kan hämta meddelandets säkra nytto last med denna token. Om ditt program inte lagrar autentiseringstoken på enheten, eller om dessa token kan ha upphört att gälla, bör enhetens app vid mottagandet av push-meddelandet Visa ett allmänt meddelande där användaren uppmanas att starta appen. Appen autentiserar sedan användaren och visar meddelande nytto lasten.

I den här självstudien visas hur du skickar säkra push-meddelanden. Den bygger vidare på självstudien [meddela användarna](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , så du bör slutföra stegen i den här självstudien först.

> [!NOTE]
> I den här självstudien förutsätter vi att du har skapat och konfigurerat din Notification Hub enligt anvisningarna i [Kom igång med Notification Hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Ändra Android-projektet

Nu när du ändrade appens Server del för att skicka bara ID: t för ett push-meddelande, måste du ändra din Android-app för att hantera meddelandet och anropa tillbaka till Server delen för att hämta det säkra meddelandet som ska visas.
För att uppnå det här målet måste du se till att din Android-app vet hur de autentiseras med Server delen när de tar emot push-meddelanden.

Ändra nu inloggnings flödet för att spara värdet för Authentication-huvudet i appens delade inställningar. Analoga mekanismer kan användas för att lagra alla autentiseringstoken (t. ex. OAuth-token) som appen måste använda utan att kräva användarautentiseringsuppgifter.

1. I ditt Android-Apps lägger du till följande konstanter högst upp i `MainActivity` klassen:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```

2. `MainActivity`Uppdatera-metoden i-klassen `getAuthorizationHeader()` så att den innehåller följande kod:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```

3. Lägg till följande- `import` uttryck högst upp i `MainActivity` filen:

    ```java
    import android.content.SharedPreferences;
    ```

Ändra nu den hanterare som anropas när meddelandet tas emot.

1. I- `MyHandler` klassen ändrar du `OnReceive()` metoden som ska innehålla:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```

2. Lägg sedan till `retrieveNotification()` -metoden och ersätt plats hållaren `{back-end endpoint}` med backend-slutpunkten som hämtades när du distribuerade Server delen:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Den här metoden anropar appens Server del för att hämta meddelande innehållet med hjälp av de autentiseringsuppgifter som lagras i delade inställningar och visar det som ett normalt meddelande. Meddelandet ser till att appens användare precis liknar alla andra push-meddelanden.

Det är bättre att hantera de fall då en egenskap för autentisering saknas eller avvisas av Server delen. Den specifika hanteringen av dessa fall beror främst på din mål användar upplevelse. Ett alternativ är att visa ett meddelande med en allmän prompt för användaren att autentisera sig för att hämta det faktiska meddelandet.

## <a name="run-the-application"></a>Kör programmet

Utför följande åtgärder för att köra programmet:

1. Kontrol lera att **AppBackend** har distribuerats i Azure. Om du använder Visual Studio kör du **AppBackend** Web API-programmet. En ASP.NET-webbsida visas.
2. I Sol förmörkelse kör du appen på en fysisk Android-enhet eller emulatorn.
3. Ange ett användar namn och lösen ord i Android-appens användar gränssnitt. Det kan vara valfri sträng, men de måste vara samma värde.
4. I Android-appens användar gränssnitt klickar du på **Logga in**. Klicka sedan på **skicka push**.
