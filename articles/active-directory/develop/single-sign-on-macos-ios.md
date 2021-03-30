---
title: Konfigurera SSO på macOS och iOS
titleSuffix: Microsoft identity platform
description: Lär dig hur du konfigurerar enkel inloggning (SSO) på macOS och iOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80881256"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Gör så här: Konfigurera SSO på macOS och iOS

Microsoft Authentication Library (MSAL) för macOS och iOS stöder enkel inloggning (SSO) mellan macOS/iOS-appar och webbläsare. I den här artikeln beskrivs följande SSO-scenarier:

- [Tyst SSO mellan flera appar](#silent-sso-between-apps)

Den här typen av SSO fungerar mellan flera appar som distribueras av samma Apple-utvecklare. Den ger tyst SSO (dvs. användaren behöver inte ange autentiseringsuppgifter) genom att läsa uppdateringstoken som skrivits av andra appar från nyckel ringen och att utväxla dem för att få åtkomst-token tyst.  

- [SSO via Authentication Broker](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Det här flödet är inte tillgängligt på macOS.

Microsoft tillhandahåller appar, som kallas hanterare, som möjliggör enkel inloggning mellan program från olika leverantörer så länge den mobila enheten är registrerad med Azure Active Directory (AAD). Den här typen av SSO kräver att ett Broker-program installeras på användarens enhet.

- **SSO mellan MSAL och Safari**

SSO uppnås via klassen [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) . Den använder befintligt inloggnings tillstånd från andra appar och Safari-webbläsaren. Den är inte begränsad till appar som distribueras av samma Apple-utvecklare, men kräver vissa åtgärder från användaren.

Om du använder standard-webbvy i din app för att logga in användare får du automatiskt SSO mellan MSAL-baserade program och Safari. Om du vill veta mer om de webbvyer som MSAL stöder kan du gå [till anpassa webbläsare och webbvyer](customize-webviews.md).

> [!IMPORTANT]
> Den här typen av SSO är för närvarande inte tillgänglig på macOS. MSAL på macOS stöder bara WKWebView som inte har stöd för enkel inloggning med Safari. 

- **Tyst SSO mellan ADAL och MSAL macOS/iOS-appar**

MSAL mål-C stöder migrering och SSO med ADAL-baserade appar. Apparna måste distribueras av samma Apple-utvecklare.

Se [SSO mellan ADAL-och MSAL-appar på MacOS och iOS](sso-between-adal-msal-apps-macos-ios.md) för instruktioner för enkel inloggning mellan appar mellan ADAL och MSAL-baserade appar.

## <a name="silent-sso-between-apps"></a>Tyst SSO mellan appar

MSAL stöder SSO-delning via iOS nyckel rings åtkomst grupper.

Om du vill aktivera enkel inloggning för dina program måste du utföra följande steg, som förklaras i detalj nedan:

1. Se till att alla program använder samma klient-ID eller program-ID.
1. Se till att alla dina program delar samma signerings certifikat från Apple så att du kan dela nyckel ringar.
1. Begär samma nyckel rings rättigheter för var och en av dina program.
1. Berätta för MSAL SDK: er om den delade nyckel ring som vi vill använda om den skiljer sig från standard-en.

### <a name="use-the-same-client-id-and-application-id"></a>Använd samma klient-ID och program-ID

För att Microsoft Identity Platform ska kunna ta reda på vilka program som kan dela tokens, måste programmen dela samma klient-ID eller program-ID. Detta är den unika identifierare som du fick när du registrerade ditt första program i portalen.

På samma sätt som Microsoft Identity Platform säger att appar som använder samma program-ID skiljer sig från **omdirigerings-URI: er**. Varje program kan ha flera omdirigerings-URI: er registrerade i onboarding-portalen. Varje app i din svit har en annan omdirigerings-URI. Exempel:

APP1 omdirigerings-URI: `msauth.com.contoso.mytestapp1://auth`  
APP2 omdirigerings-URI: `msauth.com.contoso.mytestapp2://auth`  
App3 omdirigerings-URI: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Formatet för omdirigerings-URI: er måste vara kompatibelt med formatet MSAL stöder, som dokumenteras i [kraven för omdirigerings-URI-format](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Konfigurera nyckel rings delning mellan program

Läs artikeln om att [lägga till funktioner](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) för att aktivera delning av nyckel ringar. Det är viktigt att du bestämmer vad du vill att nyckel ringen ska anropas och lägger till den funktionen i alla dina program som kommer att ingå i SSO.

När behörigheterna har ställts in korrekt visas en `entitlements.plist` fil i din projekt katalog som innehåller något som liknar det här exemplet:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>Lägg till en ny nyckel rings grupp

Lägg till en ny nyckel rings grupp till dina projekt **funktioner**. Nyckel rings gruppen ska vara:
* `com.microsoft.adalcache` på iOS 
* `com.microsoft.identity.universalstorage` på macOS.

![exempel på nyckel Ring](media/single-sign-on-macos-ios/keychain-example.png)

Mer information finns i [nyckel rings grupper](howto-v2-keychain-objc.md).

## <a name="configure-the-application-object"></a>Konfigurera programobjektet

När du har aktiverat nyckel ringen i varje program och du är redo att använda SSO, konfigurerar du `MSALPublicClientApplication` med nyckel rings åtkomst gruppen som i följande exempel:

Mål-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Införliva

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> När du delar en nyckel Ring i dina program kan alla program ta bort användare eller till och med alla tokens i ditt program.
> Detta är särskilt användbart om du har program som förlitar sig på att token ska fungera i bakgrunden.
> Att dela en nyckel Ring innebär att du måste vara mycket försiktig när appen använder Microsoft Identity SDK Remove-åtgärder.

Klart! Microsoft Identity SDK kommer nu att dela autentiseringsuppgifter för alla dina program. Konto listan kommer också att delas mellan program instanser.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO via Authentication Broker på iOS

MSAL tillhandahåller stöd för Brokered Authentication med Microsoft Authenticator. Microsoft Authenticator tillhandahåller SSO för AAD-registrerade enheter och hjälper även ditt program att följa principer för villkorlig åtkomst.

Följande steg är hur du aktiverar SSO med en autentiseringsprovider för din app:

1. Registrera en Service Broker-kompatibel omdirigerings-URI-format för programmet i appens info. plist. URI-formatet för Broker-kompatibel omdirigering är `msauth.<app.bundle.id>://auth` . Ersätt<app.bundle.id>med programmets paket-ID. Exempel:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Lägg till följande scheman i appens info. plist under `LSApplicationQueriesSchemes` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Lägg till följande i `AppDelegate.m` filen för att hantera återanrop:

    Mål-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Införliva
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Om du använder Xcode 11** bör du placera MSAL-återanrop i `SceneDelegate` filen i stället.
Om du har stöd för både UISceneDelegate och UIApplicationDelegate för kompatibilitet med äldre iOS måste MSAL-återanropet placeras i båda filerna.

Mål-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Införliva

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

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md)