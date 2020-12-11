---
title: 'Självstudie: skapa en iOS-eller macOS-app som använder Microsoft Identity Platform för autentisering | Azure'
titleSuffix: Microsoft identity platform
description: 'I den här självstudien skapar du en iOS-eller macOS-app som använder Microsoft Identity Platform för att logga in användare och få en åtkomsttoken för att anropa Microsoft Graph-API: et för deras räkning.'
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 09/18/2020
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5260ca049f971cce07659f12b54ce30304dd826d
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107642"
---
# <a name="tutorial-sign-in-users-and-call-microsoft-graph-from-an-ios-or-macos-app"></a>Självstudie: Logga in användare och anropa Microsoft Graph från en iOS-eller macOS-app

I den här självstudien skapar du en iOS-eller macOS-app som integreras med Microsoft Identity Platform för att signera användare och hämta en åtkomsttoken för att anropa Microsoft Graph-API: et.

När du har slutfört guiden kommer ditt program att godkänna inloggningar med personliga Microsoft-konton (inklusive outlook.com, live.com och andra) och arbets-eller skol konton från alla företag eller organisationer som använder Azure Active Directory. Den här självstudien gäller för både iOS-och macOS-appar. Vissa steg skiljer sig mellan de två plattformarna.

I de här självstudierna har du

> [!div class="checklist"]
> * Skapa ett iOS-eller macOS-appaket i *Xcode*
> * Registrera appen i Azure Portal
> * Lägg till kod som stöd för användar inloggning och utloggning
> * Lägg till kod för att anropa API: et för Microsoft Graph
> * Testa appen

## <a name="prerequisites"></a>Förutsättningar

- [Xcode 11. x +](https://developer.apple.com/xcode/)

## <a name="how-tutorial-app-works"></a>Så här fungerar själv studie kursen

![Visar hur exempel appen som genereras av den här själv studie kursen fungerar](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Appen i den här självstudien kan logga in användare och hämta data från Microsoft Graph för deras räkning. Dessa data kommer att nås via ett skyddat API (Microsoft Graph-API i det här fallet) som kräver auktorisering och skyddas av Microsoft Identity Platform.

Mer specifikt:

* Din app kommer att logga in användaren antingen via en webbläsare eller Microsoft Authenticator.
* Slutanvändaren kommer att godkänna de behörigheter som programmet har begärt.
* Din app kommer att utfärda en åtkomsttoken för Microsoft Graph-API: et.
* Åtkomsttoken tas med i HTTP-begäran till webb-API: et.
* Bearbeta Microsoft Graph svaret.

I det här exemplet används Microsoft Authentication Library (MSAL) för att implementera autentisering. MSAL förnyar automatiskt token, leverera enkel inloggning (SSO) mellan andra appar på enheten och hanterar kontona.

Om du vill ladda ned en slutförd version av appen som du skapar i den här självstudien kan du hitta båda versionerna på GitHub:

- [exempel på iOS-kod](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/) (GitHub)
- [MacOS-kod exempel](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/) (GitHub)

## <a name="create-a-new-project"></a>Skapa ett nytt projekt

1. Öppna Xcode och välj **skapa ett nytt Xcode-projekt**.
2. För iOS-appar väljer du **iOS**  >  **Single View-appen** och väljer **Nästa**.
3. För MacOS-appar väljer du **MacOS**  >  **kakao app** och väljer **Nästa**.
4. Ange ett produkt namn.
5. Ange **språket** till **Swift** och välj **Nästa**.
6. Välj en mapp för att skapa din app och välj **skapa**.

## <a name="register-your-application"></a>Registrera ditt program

1. Gå till [Azure Portal](https://aka.ms/MobileAppReg)
2. Öppna bladet Appregistreringar och välj **+ ny registrering**.
3. Ange ett **namn** för din app och sedan utan att ange en omdirigerings-URI.
4. Välj **konton i valfri organisations katalog (alla Azure AD Directory-flera klienter) och personliga Microsoft-konton (t. ex. Skype, Xbox)** under **konto typer som stöds**
5. Välj **register**
6. I avsnittet **Hantera** i fönstret som visas väljer du **autentisering**.

7. Välj **prova den nya upplevelsen** längst upp på skärmen för att öppna den nya appens registrerings upplevelse och välj sedan **+ ny registrering**  >  **+ Lägg till en plattforms**  >  **-iOS/MacOS**.
    - Ange ditt projekts paket-ID. Om du har hämtat koden är detta `com.microsoft.identitysample.MSALiOS` . Om du skapar ett eget projekt väljer du ditt projekt i Xcode och öppnar fliken **Allmänt** . Paket-ID visas i avsnittet **identitet** .
8. Välj `Configure` och spara **MSAL-konfigurationen** som visas på sidan **MSAL konfiguration** så att du kan ange den när du konfigurerar appen senare. Välj **Klar**.

## <a name="add-msal"></a>Lägg till MSAL

Välj ett av följande sätt för att installera MSAL-biblioteket i appen:

### <a name="cocoapods"></a>CocoaPods

1. Om du använder [CocoaPods](https://cocoapods.org/)kan du installera `MSAL` genom att först skapa en tom fil `podfile` som kallas i samma mapp som projektets `.xcodeproj` fil. Lägg till följande i `podfile` :

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Ersätt `<your-target-here>` med namnet på ditt projekt.
3. I ett terminalfönster navigerar du till mappen som innehåller det `podfile` du skapade och kör `pod install` för att installera MSAL-biblioteket.
4. Stäng Xcode och öppna `<your project name>.xcworkspace` för att läsa in projektet på nytt i Xcode.

### <a name="carthage"></a>Carthage

Om du använder [Carthage](https://github.com/Carthage/Carthage)installerar du `MSAL` genom att lägga till den i din `Cartfile` :

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Från ett terminalfönster, i samma katalog som det uppdaterade `Cartfile` , kör du följande kommando för att låta Carthage uppdatera beroendena i projektet.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manuellt

Du kan också använda Git-undermodulen eller ta en titt på den senaste versionen för användning som ett ramverk i ditt program.

## <a name="add-your-app-registration"></a>Lägg till din app-registrering

Nu ska vi lägga till din app-registrering i koden.

Lägg först till följande import uttryck överst i `ViewController.swift` , samt `AppDelegate.swift` eller `SceneDelegate.swift` filer:

```swift
import MSAL
```

Lägg sedan till följande kod i `ViewController.swift` före `viewDidLoad()` :

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization's Azure AD tenant to sign in

let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user

var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

Det enda värde som du behöver ändra ovan är det värde som tilldelas som `kClientID` [program-ID](./developer-glossary.md#application-id-client-id). Det här värdet är en del av de konfigurations data för MSAL som du sparade under steget i början av den här självstudien för att registrera programmet i Azure Portal.

## <a name="configure-xcode-project-settings"></a>Konfigurera Xcode-projekt inställningar

Lägg till en ny nyckel rings grupp till projekt **signeringens & funktioner**. Nyckel rings gruppen bör finnas `com.microsoft.adalcache` på iOS och `com.microsoft.identity.universalstorage` MacOS.

![Xcode-gränssnitt som visar hur nyckel rings gruppen ska konfigureras](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro-keychainShare.png)

## <a name="for-ios-only-configure-url-schemes"></a>Konfigurera URL-scheman enbart för iOS

I det här steget registrerar du dig `CFBundleURLSchemes` för att användaren ska kunna omdirigeras tillbaka till appen efter inloggning. Det `LSApplicationQueriesSchemes` innebär också att appen kan använda Microsoft Authenticator.

Öppna `Info.plist` som en käll kods fil i Xcode och Lägg till följande i `<dict>` avsnittet. Ersätt `[BUNDLE_ID]` med värdet som du använde i Azure Portal. Om du har hämtat koden är paket-ID: n `com.microsoft.identitysample.MSALiOS` . Om du skapar ett eget projekt väljer du ditt projekt i Xcode och öppnar fliken **Allmänt** . Paket-ID visas i avsnittet **identitet** .

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Konfigurera begränsat läge för enbart macOS

1. Gå till Xcode-projekt inställningar > **funktioner fliken**  >  **app sandbox**
2. Kryss rutan Välj **utgående anslutningar (klient)** .

## <a name="create-your-apps-ui"></a>Skapa appens användar gränssnitt

Skapa ett användar gränssnitt som innehåller en knapp för att anropa Microsoft Graph API, en annan för att logga ut och en datavy för att se vissa utdata genom att lägga till följande kod i `ViewController` klassen:

### <a name="ios-ui"></a>iOS-användargränssnitt

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {

    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right

    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
    callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add sign out button
    signOutButton = UIButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.setTitle("Sign Out", for: .normal)
    signOutButton.setTitleColor(.blue, for: .normal)
    signOutButton.setTitleColor(.gray, for: .disabled)
    signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)

    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {

    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)

}

@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>macOS-användargränssnitt

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

var usernameLabel: NSTextField!

func initUI() {

    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true

    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true

    // Add sign out button
    signOutButton = NSButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.title = "Sign Out"
    signOutButton.target = self
    signOutButton.action = #selector(signOut(_:))
    signOutButton.bezelStyle = .texturedRounded
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
    signOutButton.isEnabled = false

    // Add logging textfield
    loggingText = NSTextView()
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
    loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
    loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
}

func platformViewDidLoadSetup() {}

```

Sedan, inuti `ViewController` klassen, ersätter du `viewDidLoad()` metoden med:

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()

        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }

        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>Använd MSAL

### <a name="initialize-msal"></a>Initiera MSAL

Lägg till följande `initMSAL` metod i- `ViewController` klassen:

```swift
    func initMSAL() throws {

        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }

        let authority = try MSALAADAuthority(url: authorityURL)

        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Lägg till följande efter- `initMSAL` metod i- `ViewController` klassen.

### <a name="ios-code"></a>iOS-kod:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>macOS-kod:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Hantera inloggnings återanropet endast för iOS

Öppna filen `AppDelegate.swift`. Om du vill hantera återanropet efter inloggning lägger `MSALPublicClientApplication.handleMSALResponse` du till i `appDelegate` klassen så här:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Om du använder Xcode 11** bör du placera MSAL-motanrop i `SceneDelegate.swift` stället.
Om du har stöd för både UISceneDelegate och UIApplicationDelegate för kompatibilitet med äldre iOS måste MSAL-återanropet placeras i båda filerna.

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

#### <a name="acquire-tokens"></a>Hämta token

Nu kan vi implementera programmets GRÄNSSNITTs bearbetnings logik och hämta token interaktivt via MSAL.

MSAL exponerar två primära metoder för att hämta tokens: `acquireTokenSilently()` och `acquireTokenInteractively()` :

- `acquireTokenSilently()` försöker logga in en användare och hämta tokens utan användar interaktion så länge ett konto finns. `acquireTokenSilently()` kräver att du anger en giltig `MSALAccount` som kan hämtas med hjälp av ett av MSAL-API: er för uppräkning av konton. Det här exemplet använder `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` för att hämta aktuellt konto.

- `acquireTokenInteractively()` visar alltid användar gränssnitt när användaren försöker logga in användaren. Den kan använda sessionscookies i webbläsaren eller ett konto i Microsoft Authenticator för att tillhandahålla en interaktiv SSO-upplevelse.

Lägg till följande kod i `ViewController` klassen:

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {

        self.loadCurrentAccount { (account) in

            guard let currentAccount = account else {

                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }

            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {

        guard let applicationContext = self.applicationContext else { return }

        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main

        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }

            if let currentAccount = currentAccount {

                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")

                self.updateCurrentAccount(account: currentAccount)

                if let completion = completion {
                    completion(self.currentAccount)
                }

                return
            }

            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)

            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>Få en token interaktivt

Följande kodfragment hämtar en token för första gången genom att skapa ett `MSALInteractiveTokenParameters` objekt och anropa `acquireToken` . Härnäst ska du lägga till kod som:

1. Skapar `MSALInteractiveTokenParameters` med omfång.
2. Anropar `acquireToken()` med de skapade parametrarna.
3. Hanterar fel. Mer information finns i [MSAL för iOS-och MacOS-fel hanterings guide](msal-error-handling-ios.md).
4. Hanterar det framgångna ärendet.

Lägg till följande kod i klassen `ViewController`.

```swift
func acquireTokenInteractively() {

    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }

    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount

    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in

        // #3
        if let error = error {

            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }

        guard let result = result else {

            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }

        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}
```

`promptType`Egenskapen för `MSALInteractiveTokenParameters` konfigurerar autentiserings-och medgivande beteende. Följande värden stöds:

- `.promptIfNecessary` (standard) – användaren tillfrågas bara om det behövs. SSO-upplevelsen bestäms av förekomsten av cookies i webbvy och konto typen. Om flera användare är inloggade visas konto val. *Detta är standard beteendet*.
- `.selectAccount` -Om ingen användare har angetts visar webbmodulen för autentisering en lista över aktuella inloggade konton som användaren kan välja från.
- `.login` – Kräver att användaren autentiseras i webbvy. Endast ett konto kan vara inloggat i taget om du anger det här värdet.
- `.consent` – Kräver att användaren godkänner den aktuella uppsättningen omfång för begäran.

#### <a name="get-a-token-silently"></a>Hämta en token tyst

Om du vill hämta en uppdaterad token i bakgrunden lägger du till följande kod i- `ViewController` klassen. Det skapar ett `MSALSilentTokenParameters` objekt och anropar `acquireTokenSilent()` :

```swift

    func acquireTokenSilently(_ account : MSALAccount!) {

        guard let applicationContext = self.applicationContext else { return }

        /**

         Acquire a token for an existing account silently

         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */

        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)

        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.

                if (nsError.domain == MSALErrorDomain) {

                    if (nsError.code == MSALError.interactionRequired.rawValue) {

                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }

                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Anropa API: et för Microsoft Graph

När du har en token kan appen använda den i HTTP-huvudet för att skapa en auktoriserad begäran till Microsoft Graph:

| rubrik nyckel    | värde                 |
| ------------- | --------------------- |
| Auktorisering | Ägar \<access-token> |

Lägg till följande kod i `ViewController` klassen:

```swift
    func getContentWithToken() {

        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
        var request = URLRequest(url: url!)

        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in

            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }

            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {

                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            self.updateLogging(text: "Result from Graph: \(result))")

            }.resume()
    }
```

Läs mer om Microsoft Graph API i [Microsoft Graph API](https://graph.microsoft.com) .

### <a name="use-msal-for-sign-out"></a>Använda MSAL för utloggning

Lägg sedan till stöd för utloggning.

> [!Important]
> Genom att logga ut med MSAL tar du bort all känd information om en användare från programmet, samt att ta bort en aktiv session på enheten när den tillåts av enhets konfigurationen. Du kan också välja att logga ut användare från webbläsaren.

Lägg till en utloggnings funktion genom att lägga till följande kod inuti `ViewController` klassen.

```swift
@objc func signOut(_ sender: AnyObject) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account

             - account:    The account to remove from the cache
             */

            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview

            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }

                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })

        }
    }
```

### <a name="enable-token-caching"></a>Aktivera cachelagring av token

Som standard cachelagrar MSAL appens tokens i nyckel ringen för iOS eller macOS.

Så här aktiverar du cachelagring av token:

1. Se till att ditt program är korrekt signerat
1. Gå till Xcode-projekt inställningar **fliken > funktioner**  >  **Aktivera delning av nyckel ringar**
1. Välj **+** och ange en av följande **nyckel rings grupper**:
    - Stoppa `com.microsoft.adalcache`
    - MacOS `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Lägga till hjälp metoder
Slutför exemplet genom att lägga till följande hjälp metoder i- `ViewController` klassen.

### <a name="ios-ui"></a>iOS-gränssnitt:

``` swift

    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

    func updateAccountLabel() {

        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }

        self.usernameLabel.text = currentAccount.username
    }

    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>macOS-gränssnitt:

```swift
    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>Hämta ytterligare enhets information för iOS.

Använd följande kod för att läsa den aktuella enhets konfigurationen, inklusive om enheten är konfigurerad som delad:

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {

        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }

                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>Program med flera konton

Den här appen har skapats för ett scenario med ett enda konto. MSAL har också stöd för flera konto scenarier, men det krävs ytterligare arbete från appar. Du måste skapa ett användar gränssnitt för att hjälpa användarna att välja vilket konto de vill använda för varje åtgärd som kräver tokens. Alternativt kan din app implementera en heuristik för att välja vilket konto som ska användas genom att fråga alla konton från MSAL. Se till exempel `accountsFromDeviceForParameters:completionBlock:` [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>Testa appen

Bygg och distribuera appen till en test enhet eller Simulator. Du bör kunna logga in och hämta token för Azure AD eller personliga Microsoft-konton.

Första gången en användare loggar in på din app uppmanas de av Microsoft-identiteten att godkänna de behörigheter som begärs. Även om de flesta användare kan samtycka har vissa Azure AD-klienter inaktiverat användar medgivande, vilket kräver att administratörer samtycker till alla användares räkning. För att stödja det här scenariot registrerar du appens scope i Azure Portal.

När du har loggat in visar appen de data som returneras från Microsoft Graph `/me` slut punkten.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att skapa mobilappar som anropar skyddade webb-API: er i vår scenario serie med flera delar.

> [!div class="nextstepaction"]
> [Scenario: mobil program som anropar webb-API: er](scenario-mobile-overview.md)
