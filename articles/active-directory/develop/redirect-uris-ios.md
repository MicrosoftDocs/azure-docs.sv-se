---
title: 'Använda omdirigerings-URI: er med MSAL (iOS/macOS) | Azure'
titleSuffix: Microsoft identity platform
description: Lär dig mer om skillnaderna mellan Microsoft Authentication Library för ObjectiveC (MSAL för iOS och macOS) och Azure AD Authentication Library för ObjectiveC (ADAL. ObjC) och hur du migrerar mellan dem.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 2c728854846a3add1f5b41cb318d7dc5fd86e742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064206"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Använda omdirigerings-URI: er med Microsoft Authentication Library för iOS och macOS

När en användare autentiseras skickar Azure Active Directory (Azure AD) token till appen genom att använda den omdirigerings-URI som är registrerad i Azure AD-programmet.

Microsoft Authentication Library (MSAL) kräver att omdirigerings-URI: n registreras med Azure AD-appen i ett särskilt format. MSAL använder en standard-omdirigerings-URI, om du inte anger någon. Formatet är `msauth.[Your_Bundle_Id]://auth`.

Standardvärdet för omdirigerings-URI fungerar för de flesta appar och scenarier, inklusive Brokered Authentication and system Web View. Använd standard formatet när det är möjligt.

Du kan dock behöva ändra omdirigerings-URI: n för avancerade scenarier, enligt beskrivningen nedan.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Scenarier som kräver en annan omdirigerings-URI

### <a name="cross-app-single-sign-on-sso"></a>Enkel inloggning mellan appar (SSO)

För att Microsoft Identity Platform ska kunna dela tokens mellan appar måste varje app ha samma klient-ID eller program-ID. Detta är den unika identifieraren som angavs när du registrerade din app i portalen (inte programpaket-ID: t som du registrerar per app med Apple).

Omdirigerings-URI: erna måste vara olika för varje iOS-app. På så sätt kan Microsoft Identity service unikt identifiera olika appar som delar ett program-ID. Varje program kan ha flera omdirigerings-URI: er registrerade i Azure Portal. Varje app i din svit har en annan omdirigerings-URI. Exempel:

Följande program registrering i Azure Portal:

* Klient-ID: `ABCDE-12345` (detta är ett enda klient-ID)
* RedirectUris: `msauth.com.contoso.app1://auth` , `msauth.com.contoso.app2://auth` , `msauth.com.contoso.app3://auth`

APP1 använder omdirigering `msauth.com.contoso.app1://auth` . \
APP2 använder `msauth.com.contoso.app2://auth` . \
App3 använder `msauth.com.contoso.app1://auth` .

### <a name="migrating-from-adal-to-msal"></a>Migrera från ADAL till MSAL

När du migrerar kod som använde Azure AD Authentication Library (ADAL) till MSAL kanske du redan har konfigurerat en omdirigerings-URI för appen. Du kan fortsätta att använda samma omdirigerings-URI så länge din ADAL-app har kon figurer ATS för att stödja sammanställda scenarier och omdirigerings-URI uppfyller kraven för omdirigerings-URI-format.

## <a name="msal-redirect-uri-format-requirements"></a>Krav för omdirigering av URI-format för MSAL

* MSAL omdirigerings-URI måste vara i formatet `<scheme>://host`

    Var `<scheme>` är en unik sträng som identifierar din app. Det är främst baserat på paket-ID: n för ditt program för att garantera unika uppgifter. Om appens paket-ID till exempel är `com.contoso.myapp` , skulle omdirigerings-URI: n ha formatet: `msauth.com.contoso.myapp://auth` .

    Om du migrerar från ADAL kommer din omdirigerings-URI förmodligen att ha det här formatet: `<scheme>://[Your_Bundle_Id]` , där `scheme` är en unik sträng. Det här formatet fungerar fortfarande när du använder MSAL.

* `<scheme>` måste registreras i appens info. plist under `CFBundleURLTypes > CFBundleURLSchemes` .  I det här exemplet har info. plist öppnats som käll kod:

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
    ```

MSAL kontrollerar om din omdirigerings-URI registrerar sig korrekt och returnerar ett fel om det inte är det.
    
* Om du vill använda Universal Links som en omdirigerings-URI `<scheme>` måste du vara `https` och behöver inte deklareras i `CFBundleURLSchemes` . Konfigurera i stället appen och domänen per Apple-instruktioner på [Universal Links för utvecklare](https://developer.apple.com/ios/universal-links/) och anropa `handleMSALResponse:sourceApplication:` metoden `MSALPublicClientApplication` när programmet öppnas via en Universal-länk.

## <a name="use-a-custom-redirect-uri"></a>Använd en anpassad omdirigerings-URI

Om du vill använda en anpassad omdirigerings-URI skickar `redirectUri` du parametern till `MSALPublicClientApplicationConfig` och skickar objektet till `MSALPublicClientApplication` när du initierar objektet. Om omdirigerings-URI: n är ogiltig, kommer initieraren att returnera `nil` och ange `redirectURIError` Ytterligare information.  Exempel:

Mål-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Införliva

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Hanterar händelsen öppnad URL

Programmet bör anropa MSAL när det får svar via URL-scheman eller Universal-länkar. Anropa `handleMSALResponse:sourceApplication:` metoden för `MSALPublicClientApplication` när programmet öppnas. Här är ett exempel på anpassade scheman:

Mål-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Införliva

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Nästa steg

Lär dig mer om [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md)
