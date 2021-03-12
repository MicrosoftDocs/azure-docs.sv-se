---
title: Använda utjämnare med Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Lär dig att konfigurera Xamarin iOS-program som kan använda Microsoft Authenticator och Microsoft Authentication Library för .NET (MSAL.NET). Lär dig också hur du migrerar från Azure AD Authentication Library för .NET (ADAL.NET) till Microsoft Authentication Library för .NET (MSAL.NET).
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 226e94510709b37a7e6b1aae90a7e0ec5b4222b9
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199580"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Använda Microsoft Authenticator eller Intune-företagsportal på Xamarin-program

På Android och iOS kan-utjämnare som Microsoft Authenticator och de Android-/regionsspecifika Microsoft Intune Företagsportal aktivera:

- **Enkel inloggning (SSO)**: användarna behöver inte logga in på varje program.
- **Enhets identifiering**: koordinatorn har åtkomst till enhets certifikatet. Det här certifikatet skapas på enheten när det är anslutet till arbets platsen.
- **Verifiering av program identifiering**: när ett program anropar Service Broker skickas dess omdirigerings-URL. Service Broker verifierar URL: en.

Om du vill aktivera någon av dessa funktioner använder `WithBroker()` du parametern när du anropar- `PublicClientApplicationBuilder.CreateApplication` metoden. `.WithBroker()`Parametern har angetts till true som standard.

Installationen av Brokered Authentication i Microsoft Authentication Library för .NET (MSAL.NET) varierar beroende på plattform:

* [iOS-program](#brokered-authentication-for-ios)
* [Android-program](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Brokered Authentication för iOS

Använd följande steg för att aktivera Xamarin. iOS-appen för kommunikation med [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) -appen. Om du är mål för iOS 13 kan du läsa om [Apples bryta API-förändring](./msal-net-xamarin-ios-considerations.md).

### <a name="step-1-enable-broker-support"></a>Steg 1: Aktivera stöd för Broker

Du måste aktivera stöd för Broker för enskilda instanser av `PublicClientApplication` . Support är inaktiverat som standard. När du skapar `PublicClientApplication` genom `PublicClientApplicationBuilder` använder du `WithBroker()` parametern som följande exempel visar. `WithBroker()`Parametern har angetts till true som standard.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Steg 2: aktivera nyckel rings åtkomst

Om du vill aktivera nyckel rings åtkomst måste du ha en åtkomst grupp för nyckel ringar för ditt program. Du kan använda `WithIosKeychainSecurityGroup()` API: et för att ställa in åtkomst gruppen för nyckel ringar när du skapar ditt program:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Mer information finns i [aktivera nyckel rings åtkomst](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Steg 3: uppdatera AppDelegate för att hantera återanropet

När MSAL.NET anropar koordinatorn anropar Broker tillbaka till ditt program via- `OpenUrl` metoden för- `AppDelegate` klassen. Eftersom MSAL väntar på svar från Service Broker måste ditt program samar beta för att anropa MSAL.NET tillbaka. Om du vill aktivera det här samarbetet uppdaterar du *AppDelegate.cs* -filen för att åsidosätta följande metod.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
    if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
    {
      AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
      return true;
    }

    else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
    {
         return false;
    }

    return true;
}
```

Den här metoden anropas varje gång programmet startas. Den används som en möjlighet att bearbeta svaret från Service Broker och slutföra autentiseringsprocessen som MSAL.NET startade.

### <a name="step-4-set-uiviewcontroller"></a>Steg 4: Ange UIViewController ()

Ange ett objekt fönster fortfarande i *AppDelegate.cs* -filen. Du behöver normalt inte ange objekt fönstret för Xamarin iOS, men du behöver ett objekt fönster för att skicka och ta emot svar från Service Broker.

Så här konfigurerar du objekt fönstret:

1. I *AppDelegate.cs* -filen anger `App.RootViewController` du till en ny `UIViewController()` . Den här tilldelningen säkerställer att anropet till Service Broker innehåller `UIViewController` . Om den här inställningen tilldelas felaktigt kan du få följande fel meddelande:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. `AcquireTokenInteractive`Använd `.WithParentActivityOrWindow(App.RootViewController)` och skicka sedan i referensen till objekt fönstret som du vill använda på anropet.

    I *app.cs*:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    I *AppDelegate.cs*:

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    I `AcquireToken` anropet:

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Steg 5: registrera ett URL-schema

MSAL.NET använder URL: er för att anropa Service Broker och returnerar sedan Service Broker-svaret till din app. Registrera ett URL-schema för din app i filen *info. plist* för att slutföra den runda resan.

`CFBundleURLSchemes`Namnet måste vara `msauth.` ett prefix. Följ prefixet med `CFBundleURLName` .

I URL-schemat `BundleId` identifierar en unik app: `$"msauth.(BundleId)"` . Så om `BundleId` är är `com.yourcompany.xforms` URL-schemat `msauth.com.yourcompany.xforms` .

> [!NOTE]
> Detta URL-schema blir en del av omdirigerings-URI: n som unikt identifierar din app när den tar emot svaret från Broker.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Steg 6: Lägg till Service Broker-identifieraren i avsnittet LSApplicationQueriesSchemes

MSAL använder `–canOpenURL:` för att kontrol lera om Broker är installerad på enheten. I iOS 9 låser Apple de scheman som ett program kan fråga efter.

Lägg till i `msauthv2` `LSApplicationQueriesSchemes` avsnittet i filen *info. plist* , som i följande exempel:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>Steg 7: Lägg till en omdirigerings-URI till din app-registrering

När du använder Broker, har omdirigerings-URI: n ett extra krav. Omdirigerings-URI: n _måste_ ha följande format:

```csharp
$"msauth.{BundleId}://auth"
```

Här är ett exempel:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

Observera att omdirigerings-URI: n matchar `CFBundleURLSchemes` namnet som du inkluderade i filen *info. plist* .

Lägg till omdirigerings-URI: n i appens registrering i [Azure Portal](https://portal.azure.com). Om du vill generera en korrekt formaterad omdirigerings-URI använder du **Appregistreringar** i Azure Portal för att generera den Brokered omdirigerings-URI: n från paket-ID

**Generera omdirigerings-URI: n:**

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
1. Välj **Azure Active Directory**  >  **Appregistreringar** > din registrerade app
1. Välj **autentisering**  >  **Lägg till en plattform**  >  **iOS/MacOS**
1. Ange ditt paket-ID och välj sedan **Konfigurera**.

    Kopiera den genererade omdirigerings-URI: n som visas i text rutan **omdirigerings-URI** för att inkludera i koden:

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="iOS-plattforms inställningar med genererad omdirigerings-URI i Azure Portal":::
1. Välj **klar** för att slutföra genereringen av omdirigerings-URI: n.

## <a name="brokered-authentication-for-android"></a>Brokered Authentication för Android

### <a name="step-1-enable-broker-support"></a>Steg 1: Aktivera stöd för Broker

Stöd för Broker aktive ras per `PublicClientApplication` beräkning. Den är inaktive rad som standard. Använd `WithBroker()` parametern (anges till sant som standard) när du skapar `IPublicClientApplication` via `PublicClientApplicationBuilder` .

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Steg 2: uppdatera AppDelegate för att hantera återanropet

När MSAL.NET anropar Service Broker, kommer Broker i sin tur att gå tillbaka till ditt program med- `OnActivityResult()` metoden. Eftersom MSAL väntar på svar från Service Broker måste programmet dirigera resultatet till MSAL.NET.

Dirigera resultatet till- `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` metoden genom att åsidosätta `OnActivityResult()` metoden som visas här:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Den här metoden anropas varje gång Broker-programmet startas och används som en möjlighet att bearbeta svaret från Service Broker och slutföra autentiseringsprocessen som startats av MSAL.NET.

### <a name="step-3-set-an-activity"></a>Steg 3: Ange en aktivitet

Om du vill aktivera Brokered Authentication ställer du in en aktivitet så att MSAL kan skicka och ta emot svaret till och från Service Broker. Det gör du genom att ange aktiviteten (vanligt vis `MainActivity` ) till `WithParentActivityOrWindow(object parent)` det överordnade objektet.

Till exempel, i anropet till `AcquireTokenInteractive()` :

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>Steg 4: Lägg till en omdirigerings-URI i din app-registrering

MSAL använder URL: er för att anropa Service Broker och återgår sedan till din app. För att slutföra den här tur och retur registrerar du en **omdirigerings-URI** för din app med hjälp av [Azure Portal](https://portal.azure.com).

Formatet på omdirigerings-URI för programmet beror på vilket certifikat som används för att signera APK. Exempel:

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Den sista delen av URI: n, `hgbUYHVBYUTvuvT&Y6tr554365466=` är den base64-kodade versionen av signaturen som APK är signerad med. När du utvecklar din app i Visual Studio, och du felsöker koden utan att behöva signera APK med ett särskilt certifikat, signerar Visual Studio APK för fel söknings syfte. När Visual Studio signerar APK för dig på det här sättet ger den en unik signatur för den dator som den bygger på. Varje gången du skapar din app på en annan dator måste du därför uppdatera omdirigerings-URI: n i programmets kod och programmets registrering i Azure Portal för att kunna autentisera med MSAL.

Vid fel sökning kan du stöta på ett MSAL-undantag (eller logg meddelande) som anger att den angivna omdirigerings-URI: n är felaktig. **I undantags-eller logg meddelandet anges också den omdirigerings-URI som du ska använda** med den aktuella datorn som du felsöker. Du kan använda den tillhandahållna omdirigerings-URI: n för att fortsätta utveckla appen så länge du uppdaterar omdirigerings-URI i kod och lägger till den angivna omdirigerings-URI: n i appens registrering i Azure Portal.

När du är redo att slutföra din kod uppdaterar du omdirigerings-URI: n i koden och programmets registrering i Azure Portal att använda signaturen för certifikatet som du signerar APK med.

I praktiken innebär detta att du bör överväga att lägga till en omdirigerings-URI för varje medlem i utvecklings teamet, *plus* en omdirigerings-URI för produktions signerad version av APK.

Du kan beräkna signaturen själv, på samma sätt som med MSAL gör det:

```csharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Du kan också välja att förvärva signaturen för ditt paket med hjälp av ett **verktyg** med följande kommandon:

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS:
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>Steg 5 (valfritt): återgå till systemets webbläsare

Om MSAL har kon figurer ATS för att använda Service Broker men Service Broker inte är installerat, kommer MSAL att återgå till att använda en webbvy (en webbläsare). MSAL kommer att försöka autentisera med standard systemets webbläsare på enheten, vilket Miss lyckas eftersom omdirigerings-URI: n har kon figurer ATS för Service Broker och system läsaren inte vet hur den ska användas för att navigera tillbaka till MSAL. För att undvika det här problemet kan du konfigurera ett *avsikts filter* med den omdirigerings-URI för Broker som du använde i steg 4.

Ändra programmets manifest för att lägga till avsikts filtret:

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

Om du till exempel har en omdirigerings-URI för `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` ska ditt manifest se ut som följande XML-kodfragment.

Forward-snedstreck ( `/` ) framför signaturen i `android:path` värdet är **obligatoriskt**.

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

Mer information om hur du konfigurerar ditt program för system webbläsare och stöd för Android 11 finns i [Uppdatera Android-manifestet för stöd för system webbläsare](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support).

Alternativt kan du konfigurera MSAL så att den återgår till den inbäddade webbläsaren, som inte förlitar sig på en omdirigerings-URI:

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Fel söknings tips för Android Brokered Authentication

Här följer några tips på hur du undviker problem när du implementerar Broker-autentisering på Android:

- **Omdirigerings-URI** – Lägg till en omdirigerings-URI i program registreringen i [Azure Portal](https://portal.azure.com/). En saknad eller felaktig omdirigerings-URI är ett vanligt problem som uppstått av utvecklarna.
- **Broker-version** – installera den lägsta version som krävs för Service Broker-apparna. Någon av dessa två appar kan användas för Broker-autentisering på Android.
  - [Intune-företagsportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (version 5.0.4689.0 eller senare)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (version 6.2001.0140 eller senare).
- **Prioritet för Service Broker** – MSAL kommunicerar med den *första Service Broker* som är installerad på enheten när flera hanterare är installerade.

    Exempel: om du först installerar Microsoft Authenticator och sedan installerar Intune-företagsportal sker *endast* den sammanslagna autentiseringen på Microsoft Authenticator.
- **Loggar** – om du stöter på ett problem med Broker-autentisering kan du med hjälp av Service Broker-loggarna hjälpa dig att diagnostisera orsaken.
  - Hämta Microsoft Authenticator loggar:

    1. Välj Meny knappen i det övre högra hörnet i appen.
    1. Väljer du **Skicka feedback**  >  **med problem?**.
    1. Under **Vad försöker du göra? väljer du** ett alternativ och lägger till en beskrivning.
    1. Om du vill skicka loggarna väljer du pilen i det övre högra hörnet i appen.

    När du har skickat loggarna visas incident-ID i dialog rutan. Registrera incident-ID och inkludera det när du ber om hjälp.

  - Hämta Intune-företagsportal loggar:

    1. Välj Meny knappen i det övre vänstra hörnet i appen.
    1. Välj **Hjälp** för  >  **e-postsupport**.
    1. Om du vill skicka loggarna väljer du **överför endast loggar**.

    När du har skickat loggarna visas incident-ID i dialog rutan. Registrera incident-ID och inkludera det när du ber om hjälp.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [att tänka på när du använder universell Windows-plattform med MSAL.net](msal-net-uwp-considerations.md).
