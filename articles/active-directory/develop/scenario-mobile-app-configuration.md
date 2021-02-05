---
title: 'Konfigurera mobilappar som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: Lär dig hur du konfigurerar din mobilapp-kod för att anropa ett webb-API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1187c768a54dd04d25b6de0e6785ebb81a7dfc24
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584439"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Konfigurera en mobilapp som anropar webb-API: er

När du har skapat ditt program får du lära dig hur du konfigurerar koden med hjälp av registrerings parametrarna för appen. Mobila program innehåller några komplexa lösningar som rör inpassning i deras ramverk för skapande.

## <a name="find-msal-support-for-mobile-apps"></a>Hitta MSAL-stöd för mobila appar

Följande typer av MSAL-typer (Microsoft Authentication Library) stöder mobila appar.

MSAL | Description
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Används för att utveckla bärbara program. MSAL.NET stöder följande plattformar för att skapa ett mobil program: Universell Windows-plattform (UWP), Xamarin. iOS och Xamarin. Android.
![MSAL. iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL. iOS | Används för att utveckla interna iOS-program med hjälp av mål-C eller Swift.
![MSAL. Android](media/sample-v2-code/logo_android.png) <br/> MSAL. Android | Används för att utveckla inbyggda Android-program i Java för Android.

## <a name="instantiate-the-application"></a>Instansiera programmet

### <a name="android"></a>Android

Mobil program använder `PublicClientApplication` klassen. Så här instansierar du det:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Mobil program på iOS måste instansiera `MSALPublicClientApplication` klassen. Om du vill instansiera klassen använder du följande kod.

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Ytterligare MSALPublicClientApplicationConfig-egenskaper](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) kan åsidosätta standard utfärdaren, ange en omdirigerings-URI eller ändra beteendet för cachelagring av MSAL-token.

### <a name="xamarin-or-uwp"></a>Xamarin eller UWP

I det här avsnittet beskrivs hur du instansierar programmet för Xamarin. iOS-, Xamarin. Android-och UWP-appar.

#### <a name="instantiate-the-application"></a>Instansiera programmet

I Xamarin eller UWP är det enklaste sättet att instansiera programmet genom att använda följande kod. I den här koden `ClientId` är GUID för din registrerade app.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Ytterligare `With<Parameter>` metoder ange överordnat användar namn, Åsidosätt standard auktoritet, ange ett klient namn och version för telemetri, ange en omdirigerings-URI och ange den HTTP-fabrik som ska användas. HTTP-fabriken kan till exempel användas för att hantera proxyservrar och ange telemetri och loggning.

I följande avsnitt finns mer information om att instansiera programmet.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Ange överordnat gränssnitt, fönster eller aktivitet

På Android överför du den överordnade aktiviteten innan du utför den interaktiva autentiseringen. I iOS, när du använder en Service Broker-överföring `ViewController` . På samma sätt på UWP kanske du vill skicka det överordnade fönstret. Du skickar det i när du hämtar token. Men när du skapar appen kan du också ange ett återanrop som ett ombud som returnerar `UIParent` .

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

På Android rekommenderar vi att du använder [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin) . Den resulterande `PublicClientApplication` Builder-koden ser ut som i det här exemplet:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Hitta fler parametrar för att skapa appar

En lista över alla metoder som är tillgängliga finns i `PublicClientApplicationBuilder` [listan metoder](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

En beskrivning av alla alternativ som visas i finns i `PublicClientApplicationOptions` [referens dokumentationen](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Uppgifter för Xamarin iOS

Om du använder MSAL.NET på Xamarin iOS utför du följande uppgifter.

* [Åsidosätt och implementera `OpenUrl` funktionen i `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Aktivera nyckel rings grupper](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Aktivera delning av token cache](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Aktivera nyckel rings åtkomst](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Mer information finns i [Xamarin iOS-överväganden](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Uppgifter för MSAL för iOS och macOS

De här uppgifterna är nödvändiga när du använder MSAL för iOS och macOS:

* [Implementera `openURL` motringningen](#brokered-authentication-for-msal-for-ios-and-macos)
* [Aktivera nyckel rings åtkomst grupper](howto-v2-keychain-objc.md)
* [Anpassa webbläsare och webbvyer](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Uppgifter för Xamarin. Android

Om du använder Xamarin. Android utför du följande uppgifter:

- [Se till att kontrollen går tillbaka till MSAL när den interaktiva delen av autentiserings flödet slutar](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Uppdatera Android-manifestet](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Använda den inbäddade webb visningen (valfritt)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Felsök vid behov](msal-net-xamarin-android-considerations.md#troubleshooting)

Mer information finns i [Xamarin. Android-överväganden](msal-net-xamarin-android-considerations.md).

Överväganden om webbläsare på Android finns i [Xamarin. Android-/regionsspecifika överväganden med MSAL.net](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Uppgifter för UWP

På UWP kan du använda företags nätverk. I följande avsnitt beskrivs de uppgifter som du bör utföra i företags scenariot.

Mer information finns i [UWP-/regionsspecifika överväganden med MSAL.net](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Konfigurera programmet så att det använder Service Broker

I Android och iOS aktiverar mäklaren:

- **Enkel inloggning (SSO)**: du kan använda SSO för enheter som är registrerade med Azure Active Directory (Azure AD). När du använder SSO behöver användarna inte logga in på varje program.
- **Enhets identifiering**: den här inställningen aktiverar villkorliga åtkomst principer som är relaterade till Azure AD-enheter. Autentiseringsprocessen använder enhets certifikatet som skapades när enheten anslöts till arbets platsen.
- **Verifiering av program identifiering**: när ett program anropar Service Broker skickas dess omdirigerings-URL. Sedan verifieras den av Service Broker.

### <a name="enable-the-broker-on-xamarin"></a>Aktivera Broker på Xamarin

Om du vill aktivera Broker för Xamarin använder `WithBroker()` du parametern när du anropar- `PublicClientApplicationBuilder.CreateApplication` metoden. Som standard `.WithBroker()` är inställt på sant.

Om du vill aktivera Brokered Authentication för Xamarin. iOS följer du stegen i [avsnittet Xamarin. iOS](#enable-brokered-authentication-for-xamarin-ios) i den här artikeln.

### <a name="enable-the-broker-for-msal-for-android"></a>Aktivera Broker för MSAL för Android

Information om hur du aktiverar en Service Broker på Android finns i [Broker-autentisering på Android](msal-android-single-sign-on.md).

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Aktivera Broker för MSAL för iOS och macOS

Brokered Authentication är aktiverat som standard för Azure AD-scenarier i MSAL för iOS och macOS.

I följande avsnitt finns anvisningar för hur du konfigurerar programmet för stöd för Brokered Authentication för antingen MSAL för Xamarin. iOS eller MSAL för iOS och macOS. I de två uppsättningarna med instruktioner skiljer sig några av stegen.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Aktivera Brokered Authentication för Xamarin iOS

Följ stegen i det här avsnittet om du vill att din Xamarin. iOS-app ska kommunicera med [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) -appen.

#### <a name="step-1-enable-broker-support"></a>Steg 1: Aktivera stöd för Broker

Stöd för Broker är inaktiverat som standard. Du aktiverar det för en enskild `PublicClientApplication` klass. Använd `WithBroker()` parametern när du skapar `PublicClientApplication` klassen genom `PublicClientApplicationBuilder` . `WithBroker()`Parametern har angetts till true som standard.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Steg 2: uppdatera AppDelegate för att hantera återanropet

När MSAL.NET anropar Service Broker anropar Service Broker tillbaka till ditt program. Den anropar igen med hjälp av `AppDelegate.OpenUrl` metoden. Eftersom MSAL väntar på svar från Service Broker måste ditt program samar beta för att anropa MSAL.NET tillbaka. Du ställer in det här beteendet genom `AppDelegate.cs` att uppdatera filen för att åsidosätta metoden, som följande kod visar.

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

Den här metoden anropas varje gång programmet startas. Det är en möjlighet att bearbeta svaret från Service Broker och slutföra autentiseringsprocessen som MSAL.NET startade.

#### <a name="step-3-set-a-uiviewcontroller"></a>Steg 3: Ange en UIViewController ()

För Xamarin iOS behöver du vanligt vis inte ange ett objekt fönster. Men i det här fallet bör du ställa in det så att du kan skicka och ta emot svar från en Service Broker. Om du vill ange ett objekt fönster i `AppDelegate.cs` , ställer du in en `ViewController` .

Följ dessa steg om du vill ange objekt fönstret:

1. I `AppDelegate.cs` , anger `App.RootViewController` du till en ny `UIViewController()` . Den här inställningen garanterar att anropet till Service Broker innehåller `UIViewController` . Om den inte anges korrekt kan du få följande fel meddelande:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Använd på `AcquireTokenInteractive` anropet `.WithParentActivityOrWindow(App.RootViewController)` . Skicka i referensen till det objekt fönster som du kommer att använda. Här är ett exempel:

    Följande gäller i `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    Följande gäller i `AppDelegate.cs`:
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

#### <a name="step-4-register-a-url-scheme"></a>Steg 4: registrera ett URL-schema

MSAL.NET använder URL: er för att anropa Service Broker och returnerar sedan Service Broker-svaret tillbaka till din app. Om du vill slutföra den runda resan registrerar du appens URL-schema i `Info.plist` filen.

Följ dessa steg om du vill registrera appens URL-schema:

1. Prefix `CFBundleURLSchemes` med `msauth` .
1. Lägg till `CFBundleURLName` i slutet. Följ det här mönstret:

   `$"msauth.(BundleId)"`

   Här `BundleId` identifierar din enhet unikt. Till exempel `BundleId` `yourcompany.xforms` är ditt URL-schema `msauth.com.yourcompany.xforms` .

  
      Detta URL-schema kommer att bli en del av omdirigerings-URI: n som unikt identifierar din app när den tar emot Service Broker-svaret.

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

#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Steg 5: Lägg till i LSApplicationQueriesSchemes-avsnittet

MSAL använder `–canOpenURL:` för att kontrol lera om Broker är installerad på enheten. I iOS 9 låser Apple de scheman som ett program kan fråga efter.

Lägg till i `msauthv2` `LSApplicationQueriesSchemes` avsnittet i `Info.plist` filen, som i följande kod exempel:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Brokered Authentication for MSAL för iOS och macOS

Brokered Authentication är aktiverat som standard för Azure AD-scenarier.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Steg 1: uppdatera AppDelegate för att hantera återanropet

När MSAL för iOS och macOS anropar Broker, anropar Broker tillbaka till ditt program med hjälp av- `openURL` metoden. Eftersom MSAL väntar på svar från Service Broker måste ditt program samar beta för att kunna ringa tillbaka MSAL. Konfigurera den här funktionen genom att uppdatera `AppDelegate.m` filen för att åsidosätta metoden, som följande kod exempel visar.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Om du `UISceneDelegate` har använt iOS 13 eller senare placerar du MSAL-återanropet i `scene:openURLContexts:` i `UISceneDelegate` stället. MSAL `handleMSALResponse:sourceApplication:` får bara anropas en gång för varje URL.

Mer information finns i [Apples dokumentation](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc).

#### <a name="step-2-register-a-url-scheme"></a>Steg 2: registrera ett URL-schema

MSAL för iOS och macOS använder URL: er för att anropa Service Broker och returnerar sedan Service Broker-svaret till din app. Om du vill slutföra den runda resan registrerar du ett URL-schema för din app i `Info.plist` filen.

Registrera ett schema för din app:

1. Lägg till ett anpassat URL-schema med `msauth` .

1. Lägg till ditt paket-ID i slutet av schemat. Följ det här mönstret:

   `$"msauth.(BundleId)"`

   Här `BundleId` identifierar din enhet unikt. Till exempel `BundleId` `yourcompany.xforms` är ditt URL-schema `msauth.com.yourcompany.xforms` .

    Detta URL-schema kommer att bli en del av omdirigerings-URI: n som unikt identifierar din app när den tar emot Service Broker-svaret. Se till att omdirigerings-URI i formatet `msauth.(BundleId)://auth` är registrerat för ditt program i [Azure Portal](https://portal.azure.com).

   ```XML
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>msauth.[BUNDLE_ID]</string>
           </array>
       </dict>
   </array>
   ```

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Steg 3: Lägg till LSApplicationQueriesSchemes

Lägg till `LSApplicationQueriesSchemes` för att tillåta anrop till Microsoft Authenticator-appen om den är installerad.

> [!NOTE]
> `msauthv3`Schemat krävs när din app kompileras med hjälp av Xcode 11 och senare.

Här är ett exempel på hur du lägger till `LSApplicationQueriesSchemes` :

```XML
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Brokered Authentication för Xamarin. Android

Information om hur du aktiverar en Service Broker på Android finns i [Broker-autentisering på Xamarin. Android](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot och [Skaffa en token](scenario-mobile-acquire-token.md).