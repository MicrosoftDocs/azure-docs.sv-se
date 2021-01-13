---
title: 'Snabb start: lägga till inloggning med Microsoft i en iOS-eller macOS-app | Azure'
titleSuffix: Microsoft identity platform
description: 'I den här snabb starten får du lära dig hur en iOS-eller macOS-app kan logga in användare, hämta en åtkomsttoken från Microsoft Identity Platform och anropa Microsoft Graph-API: et.'
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: 2fbff2abeb454fe02aa4bd50615033b9a0b7f27b
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178610"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Snabb start: Logga in användare och anropa Microsoft Graph API från en iOS-eller macOS-app

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur ett inbyggt iOS-eller macOS-program kan logga in användare och få en åtkomsttoken för att anropa Microsoft Graph-API: et.

Snabb starten gäller både iOS-och macOS-appar. Vissa steg behövs bara för iOS-appar och visas som sådana.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* XCode 10 +
* iOS 10 +
* macOS 10.12 +

## <a name="how-the-sample-works"></a>Så här fungerar exemplet

![Visar hur exempel appen som genereras av den här snabb starten fungerar](media/quickstart-v2-ios/ios-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrera och ladda ned snabbstartsappen
> Det finns två alternativ för att starta snabbstartsprogrammet:
> * [Express] [Alternativ 1: Registrera och konfigurera appen automatiskt och ladda sedan ned ditt kodexempel](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Manuellt] [Alternativ 2: Registrera och konfigurera programmet och kodexemplet](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Alternativ 1: registrera och konfigurera appen automatiskt och ladda ned kod exemplet
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> För att registrera din app
> 1. Gå till snabb starts upplevelsen för <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Appregistreringar <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
> 1. Ange ett namn för programmet och välj **Registrera**.
> 1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Alternativ 2: Registrera och konfigurera programmet och kodexemplet
>
> #### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Sök efter och välj **Azure Active Directory**.    
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för ditt program. Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. Välj **Register** (Registrera).
> 1. Under **Hantera** väljer du **autentisering**  >  **Lägg till plattform**  >  **iOS**.
> 1. Ange **paket-ID** : t för ditt program. Paket-ID: t är en unik sträng som unikt identifierar ditt program, till exempel `com.<yourname>.identitysample.MSALMacOS` . Anteckna värdet du använder. Observera att iOS-konfigurationen även gäller för macOS-program.
> 1. Välj **Konfigurera** och spara **konfigurations** informationen för MSAL för senare i den här snabb starten.
> 1. Välj **Klar**.

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Steg 1: Konfigurera programmet
> För att kod exemplet för den här snabb starten ska fungera måste du lägga till en omdirigerings-URI som är kompatibel med auth Broker.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör den här ändringen åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-ios/green-check.png) appen konfigureras med de här attributen
>
> #### <a name="step-2-download-the-sample-project"></a>Steg 2: Hämta exempel projektet
> > [!div id="autoupdate_ios" class="nextstepaction"]
> > [Ladda ned kod exemplet för iOS]()
>
> > [!div id="autoupdate_macos" class="nextstepaction"]
> > [Ladda ned kod exemplet för macOS]()
> [!div renderon="docs"]
> #### <a name="step-2-download-the-sample-project"></a>Steg 2: Hämta exempel projektet
>
> - [Ladda ned kod exemplet för iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
> - [Ladda ned kod exemplet för macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Steg 3: Installera beroenden

I ett terminalfönster navigerar du till mappen med det nedladdade kod exemplet och kör `pod install` för att installera det senaste MSAL-biblioteket.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-4-your-app-is-configured-and-ready-to-run"></a>Steg 4: appen är konfigurerad och redo att köras
> Vi har konfigurerat ditt projekt med värdena för appens egenskaper och är redo att köras.
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
>
> [!div renderon="docs"]
>#### <a name="step-4-configure-your-project"></a>Steg 4: Konfigurera ditt projekt
> Om du valde alternativ 1 ovan kan du hoppa över de här stegen.
> 1. Extrahera zip-filen och öppna projektet i XCode.
> 1. Redigera **ViewController. SWIFT** och ersätt raden som börjar med "Låt kClientID" med följande kodfragment. Kom ihåg att uppdatera värdet för `kClientID` med clientID som du sparade när du registrerade din app i portalen tidigare i den här snabb starten:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Om du skapar en app för [nationella Azure AD-moln](/graph/deployments#app-registration-and-token-service-root-endpoints)ersätter du raden som börjar med "Låt kGraphEndpoint" och "Låt kAuthority" med rätt slut punkter. Använd standardvärden för global åtkomst:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Andra slut punkter dokumenteras [här](/graph/deployments#app-registration-and-token-service-root-endpoints). Om du till exempel vill köra snabb starten med Azure AD Tyskland använder du följande:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Öppna projekt inställningarna. I avsnittet **identitet** anger du det **paket-ID** som du angav i portalen.
> 1. Högerklicka på **info. plist** och välj **öppna som**  >  **källkod**.
> 1. Under noden dict root ersätter `Enter_the_bundle_Id_Here` du med **_paket-ID_* _ som du använde i portalen.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Bygg & kör appen!

## <a name="more-information"></a>Mer information

Läs dessa avsnitten om du vill lära dig mer om den här snabbstarten.

### <a name="get-msal"></a>Hämta MSAL

MSAL ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Du kan lägga till MSAL i ditt program med hjälp av följande process:

```
$ vi Podfile

```
Lägg till följande i den här Podfile (med projektets mål):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Kör CocoaPods-installations kommando:

```pod install```

### <a name="initialize-msal"></a>Initiera MSAL

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```swift
import MSAL
```

Initiera sedan MSAL med hjälp av följande kod:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Där: | Description |
> |---------|---------|
> | `clientId` | Program-ID: t från programmet som är registrerat i _portal. Azure. com * |
> | `authority` | Slut punkten för Microsoft Identity Platform. I de flesta fall är detta `https://login.microsoftonline.com/common` |
> | `redirectUri` | Omdirigerings-URI för programmet. Du kan skicka Nil om du vill använda standardvärdet eller din anpassade omdirigerings-URI. |

### <a name="for-ios-only-additional-app-requirements"></a>För endast iOS, ytterligare krav för appar

Din app måste också ha följande i din `AppDelegate` . Detta låter MSAL SDK hantera token-svar från auth Broker-appen när du utför autentisering.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> I iOS 13 +, om du antar `UISceneDelegate` i stället för `UIApplicationDelegate` , placerar du koden i `scene:openURLContexts:` återanropet i stället (se [Apples dokumentation](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Om du har stöd för både UISceneDelegate och UIApplicationDelegate för kompatibilitet med äldre iOS måste MSAL-återanropet placeras på båda platserna.

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

        guard let urlContext = URLContexts.first else {
            return
        }

        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication

        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

Slutligen måste appen ha en `LSApplicationQueriesSchemes` post i ***info. plist** _ tillsammans med `CFBundleURLTypes` . Exemplet följer med detta.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Logga in användare & token för begäran

MSAL har två metoder som används för att hämta token: `acquireToken` och `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: få en token interaktivt

Vissa situationer kräver att användare interagerar med Microsoft Identity Platform. I sådana fall kan slutanvändaren behöva välja sitt konto, ange sina autentiseringsuppgifter eller godkänna appens behörigheter. Exempel:

_ Första gången användare loggar in i programmet
* Om en användare återställer sitt lösen ord måste de ange sina autentiseringsuppgifter
* När ditt program begär åtkomst till en resurs för första gången
* När MFA eller andra principer för villkorlig åtkomst krävs

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Där:| Description |
> |---------|---------|
> | `scopes` | Innehåller de omfattningar som begärs (det vill säga `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API: er ( `api://<Application ID>/access_as_user` ) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Hämta en åtkomsttoken tyst

Appar bör inte kräva att användarna loggar in varje gång de begär en token. Om användaren redan har loggat in, tillåter den här metoden appar att begära token tyst.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Där: | Description |
> |---------|---------|
> | `scopes` | Innehåller de omfattningar som begärs (det vill säga `[ "user.read" ]` för Microsoft Graph eller `[ "<Application ID URL>/scope" ]` för anpassade webb-API: er ( `api://<Application ID>/access_as_user` ) |
> | `account` | Det konto som en token begärs för. Den här snabb starten är ungefär samma konto program. Om du vill skapa en app med flera konton måste du definiera logik för att identifiera vilket konto som ska användas för token-begäranden som använder `accountsFromDeviceForParameters:completionBlock:` och skickar korrekt `accountIdentifier` |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Gå vidare till den stegvisa själv studie kursen där du skapar en iOS-eller macOS-app som hämtar en åtkomsttoken från Microsoft Identity Platform och använder den för att anropa Microsoft Graph-API: et.

> [!div class="nextstepaction"]
> [Självstudie: Logga in användare och anropa Microsoft Graph från en iOS-eller macOS-app](tutorial-v2-ios.md)
