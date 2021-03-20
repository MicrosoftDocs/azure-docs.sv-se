---
title: ADAL till MSAL migration guide (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig skillnaderna mellan MSAL för iOS/macOS och Azure AD Authentication Library för ObjectiveC (ADAL. ObjC) och hur du migrerar till MSAL för iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 7dc3241198fbc6eeddba059251f28c6dc35c8a29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98754939"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Migrera program till MSAL för iOS och macOS

Biblioteket för Azure Active Directory-autentisering ([ADAL-mål-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) har skapats för att fungera med Azure Active Directory-konton via slut punkten för v 1.0.

Microsoft Authentication Library för iOS och macOS (MSAL) är utformat för att fungera med alla Microsoft-identiteter som Azure Active Directory (Azure AD)-konton, personliga Microsoft-konton och Azure AD B2C konton via Microsoft Identity Platform (formellt i Azure AD v 2.0-slutpunkten).

Microsoft Identity Platform har några viktiga skillnader i Azure Active Directory v 1.0. Den här artikeln fokuserar på dessa skillnader och ger vägledning om hur du migrerar en app från ADAL till MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Skillnader mellan funktioner för ADAL och MSAL-appar

### <a name="who-can-sign-in"></a>Vem kan logga in

* ADAL stöder endast arbets-och skol konton – även kallade Azure AD-konton.
* MSAL stöder personliga Microsoft-konton (MSA-konton) som Hotmail.com, Outlook.com och Live.com.
* MSAL stöder arbets-och skol konton och Azure AD B2C konton.

### <a name="standards-compliance"></a>Efterlevnad av standarder

* Microsoft Identity Platform följer OAuth 2,0 och OpenId Connect-standarder.

### <a name="incremental-and-dynamic-consent"></a>Det stegvisa och dynamiska godkännandet

* Azure Active Directory v 1.0-slutpunkten kräver att alla behörigheter deklareras i förväg under program registreringen. Det innebär att dessa behörigheter är statiska.
* Med Microsoft Identity Platform kan du begära behörigheter dynamiskt. Appar kan bara begära behörigheter vid behov och begära fler när appen behöver dem.

Mer information om skillnader mellan Azure Active Directory v 1.0 och Microsoft Identity Platform finns i [varför uppdatera till Microsoft Identity Platform?](../azuread-dev/azure-ad-endpoint-comparison.md).

## <a name="adal-and-msal-library-differences"></a>Skillnader mellan ADAL och MSAL-bibliotek

MSAL offentliga API motsvarar några viktiga skillnader mellan Azure AD v 1.0 och Microsoft Identity Platform.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication i stället för ADAuthenticationContext

`ADAuthenticationContext` är det första objektet som en ADAL-app skapar. Den representerar en instansiering av ADAL. Appar skapar en ny instans av `ADAuthenticationContext` för varje kombination av Azure Active Directory Cloud och Tenant (auktoritet). Samma `ADAuthenticationContext` kan användas för att hämta tokens för flera offentliga klient program.

I MSAL är huvud interaktionen via ett- `MSALPublicClientApplication` objekt som modelleras efter den [offentliga OAuth 2,0-klienten](https://tools.ietf.org/html/rfc6749#section-2.1). En instans av `MSALPublicClientApplication` kan användas för att interagera med flera AAD-moln, och klienter, utan att behöva skapa en ny instans för varje instans. För de flesta appar `MSALPublicClientApplication` räcker en instans.

### <a name="scopes-instead-of-resources"></a>Omfattningar i stället för resurser

I ADAL hade en app angett ett *resurs* -ID som `https://graph.microsoft.com` hämtar tokens från Azure Active Directory v 1.0-slutpunkten. En resurs kan definiera ett antal omfattningar, eller oAuth2Permissions i app-manifestet, som det förstår. De här tillåtna klient programmen begär token från den resursen för en viss uppsättning omfång som definierats i förväg under registreringen av appen.

I MSAL, i stället för en enda resurs-ID, tillhandahåller appar en uppsättning omfång per begäran. Ett omfång är ett resurs-ID följt av ett behörighets namn i formatet resurs/behörighet. Till exempel `https://graph.microsoft.com/user.read`

Det finns två sätt att tillhandahålla omfång i MSAL:

* Ange en lista över alla behörigheter som dina appar behöver. Exempel: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    I det här fallet begär appen och- `directory.read` `directory.write` behörigheterna. Användaren uppmanas att godkänna behörigheten om de inte har samtyckt till dem före för den här appen. Programmet kan också få ytterligare behörigheter som användaren redan har samtyckt till för programmet. Användaren uppmanas bara att ange medgivande för nya behörigheter eller behörigheter som inte har beviljats.

* `/.default`Omfånget.

Detta är det inbyggda omfånget för varje program. Den refererar till den statiska listan med behörigheter som kon figurer ATS när programmet registrerades. Det fungerar på samma sätt som för `resource` . Detta kan vara användbart när du migrerar för att säkerställa att en liknande uppsättning omfång och användar upplevelse upprätthålls.

Om du vill använda `/.default` omfånget lägger du till i `/.default` resurs identifieraren. Exempel: `https://graph.microsoft.com/.default`. Om resursen slutar med ett snedstreck ( `/` ) bör du fortfarande lägga till `/.default` , inklusive det inledande snedstrecket, vilket resulterar i en omfattning som har ett dubbelt snedstreck ( `//` ).

Du kan läsa mer om hur du använder området "/.default" [här](./v2-permissions-and-consent.md#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Stöd för olika WebView-typer & webbläsare

ADAL stöder endast UIWebView/WKWebView för iOS och webbvy för macOS. MSAL för iOS stöder fler alternativ för att Visa webb innehåll när du begär en auktoriseringskod och inte längre stöder `UIWebView` , vilket kan förbättra användar upplevelsen och säkerheten.

Som standard använder MSAL på iOS [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), vilket är webb komponenten som Apple rekommenderar för autentisering på iOS 12 +-enheter. Den ger enkel Sign-On (SSO)-förmåner via cookie-delning mellan appar och Safari-webbläsaren.

Du kan välja att använda en annan webb komponent beroende på appens krav och den slut användar upplevelse som du vill ha. Fler alternativ finns i [WebView-typer som stöds](customize-webviews.md) .

När du migrerar från ADAL till MSAL `WKWebView` ger den användar upplevelsen som liknar ADAL på iOS och MacOS. Vi rekommenderar att du migrerar till `ASWebAuthenticationSession` på iOS, om möjligt. För macOS rekommenderar vi att du använder `WKWebView` .

### <a name="account-management-api-differences"></a>API-skillnader för konto hantering

När du anropar ADAL `acquireToken()` -metoderna eller `acquireTokenSilent()` får du ett `ADUserInformation` objekt som innehåller en lista över anspråk från `id_token` som representerar det konto som autentiseras. Dessutom `ADUserInformation` returneras en `userId` baserat på `upn` anspråket. Efter den inledande interaktiva token-hämtningen förväntar ADAL utvecklare att tillhandahålla `userId` i alla tysta anrop.

ADAL tillhandahåller inte ett API för att hämta kända användar identiteter. Den förlitar sig på appen för att spara och hantera dessa konton.

MSAL innehåller en uppsättning API: er som visar alla konton som är kända för MSAL utan att behöva skaffa en token.

Som ADAL returnerar MSAL konto information som innehåller en lista över anspråk från `id_token` . Den är en del av `MSALAccount` objektet i `MSALResult` objektet.

MSAL innehåller en uppsättning API: er för att ta bort konton, vilket gör de borttagna kontona otillgängliga för appen. När kontot har tagits bort uppmanas användaren att göra interaktiva token-hämtningar vid senare anrop. Konto borttagning gäller endast för det klient program som startade det och tar inte bort kontot från de andra appar som körs på enheten eller från system läsaren. Detta säkerställer att användaren fortsätter att ha en SSO-upplevelse på enheten även efter att ha loggat ut från en enskild app.

Dessutom returnerar MSAL också en konto identifierare som kan användas för att begära en token senare. Konto identifieraren (som är tillgänglig via `identifier` egenskapen i `MSALAccount` objektet) kan dock inte visas och du kan inte välja det format som det är i och inte bör försöka tolka eller parsa det.

### <a name="migrating-the-account-cache"></a>Migrerar konto-cachen

När du migrerar från ADAL lagrar appar vanligt vis ADAL `userId` , som inte har den `identifier` som krävs av MSAL. Som ett steg med migreringen kan en app fråga ett MSAL-konto med hjälp av ADAL-userId med följande API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Detta API läser både MSAL och ADAL cacheminne för att hitta kontot efter ADAL userId (UPN).

Om kontot hittas bör utvecklaren använda kontot för att utföra hämtning av tysta token. Den första hämtningen av tyst token kommer att uppgradera kontot och utvecklaren får en MSAL-kompatibel konto identifierare i MSAL-resultatet ( `identifier` ). Efter det ska endast `identifier` användas för kontos ökningar med hjälp av följande API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Även om det är möjligt att fortsätta använda ADAL `userId` för alla åtgärder i MSAL, eftersom `userId` är baserat på UPN, är det beroende av flera begränsningar som resulterar i en felaktig användar upplevelse. Om UPN t. ex. ändras måste användaren logga in igen. Vi rekommenderar att alla appar använder ett icke-displayt konto `identifier` för alla åtgärder.

Läs mer om [migrering av cache-tillstånd](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Ändringar av nyckelns förvärv

MSAL introducerar vissa anrops ändringar för token-hämtning:

* Som ADAL `acquireTokenSilent` resulterar alltid i en tyst begäran.
* Till skillnad från ADAL `acquireToken` resulterar alltid i användar åtgärds bara användar gränssnitt via webbvy eller appen Microsoft Authenticator. Beroende på SSO-status i webbvy/Microsoft Authenticator kan användaren uppmanas att ange sina autentiseringsuppgifter.
* I ADAL, `acquireToken` med `AD_PROMPT_AUTO` första försök att hämta tyst token, och endast visar gränssnitt om den tysta begäran Miss lyckas. I MSAL kan du uppnå den här logiken genom att först anropa `acquireTokenSilent` och bara anropa `acquireToken` om tyst förvärv Miss lyckas. På så sätt kan utvecklare anpassa användar upplevelsen innan den börjar hämta interaktiva token.

### <a name="error-handling-differences"></a>Fel hanterings skillnader

MSAL ger mer skärpa mellan fel som kan hanteras av din app och de som kräver åtgärder från användaren. Det finns ett begränsat antal fel som utvecklare måste hantera:

* `MSALErrorInteractionRequired`: Användaren måste göra en interaktiv begäran. Detta kan orsakas av olika orsaker, till exempel en förfallen klientsession, en princip för villkorlig åtkomst har ändrats, en uppdateringstoken har förfallit eller återkallats, det finns inga giltiga tokens i cachen och så vidare.
* `MSALErrorServerDeclinedScopes`: Begäran slutfördes inte fullständigt och vissa scope har inte beviljats åtkomst. Detta kan bero på att en användare har avböjt medgivande till ett eller flera omfång.

Att hantera alla andra fel i [ `MSALError` listan](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) är valfritt. Du kan använda informationen i dessa fel för att förbättra användar upplevelsen.

Mer information om hantering av MSAL-fel finns i [Hantera undantag och fel med hjälp av MSAL](msal-error-handling-ios.md) .

### <a name="broker-support"></a>Stöd för Broker

MSAL, från och med version 0.3.0, ger stöd för Brokered Authentication med hjälp av Microsoft Authenticator-appen. Microsoft Authenticator aktiverar även stöd för scenarier med villkorlig åtkomst. Exempel på scenarier för villkorlig åtkomst omfattar efterlevnadsprinciper för enheter som kräver att användaren registrerar enheten via Intune eller registrera med AAD för att få en token. Och MAM-principer (Mobile Application Management), som kräver bevis för efterlevnad innan appen kan hämta en token.

Så här aktiverar du koordinator för ditt program:

1. Registrera ett Service Broker-kompatibelt omdirigerings-URI-format för programmet. URI-formatet för Broker-kompatibel omdirigering är `msauth.<app.bundle.id>://auth` . Ersätt `<app.bundle.id>` med programmets paket-ID. Om du migrerar från ADAL och ditt program redan har stöd för Service Broker behöver du inte göra något. Din tidigare omdirigerings-URI är helt kompatibel med MSAL, så du kan gå vidare till steg 3.

2. Lägg till programmets omdirigerings-URI-schema i filen info. plist. För standard omdirigerings-URI för MSAL är formatet `msauth.<app.bundle.id>` . Exempel:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Lägg till följande scheman i appens info. plist under LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Lägg till följande i din AppDelegate. m-fil för att hantera återanrop: mål-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
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

### <a name="business-to-business-b2b"></a>Företag till företag (B2B)

I ADAL skapar du separata instanser av `ADAuthenticationContext` för varje klient som appen begär token för. Detta är inte längre ett krav i MSAL. I MSAL kan du skapa en enda instans av `MSALPublicClientApplication` och använda den för alla AAD-moln och organisationer genom att ange en annan auktoritet för acquireToken-och acquireTokenSilent-anrop.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO i partnerskap med andra SDK: er

MSAL för iOS kan nå SSO via en enhetlig cache med följande SDK: er:

- ADAL-mål – C 2.7. x +
- MSAL.NET för Xamarin 2.4. x +
- ADAL.NET för Xamarin 4.4. x +

SSO uppnås via delning av nyckel ringar och är endast tillgängligt mellan appar som publicerats från samma Apple Developer-konto.

Delning av nyckel ringar för enkel nyckel är den enda tysta SSO-typen.

På macOS kan MSAL nå enkel inloggning med andra MSAL för iOS-och macOS-baserade program och ADAL mål-C-baserade program.

MSAL på iOS stöder också två typer av SSO:

* Enkel inloggning via webbläsaren. MSAL för iOS stöder `ASWebAuthenticationSession` , vilket ger enkel inloggning via cookies som delas mellan andra appar på enheten och särskilt Safari-webbläsaren.
* SSO via en autentiseringstjänst. Microsoft Authenticator fungerar som Authentication Broker på en iOS-enhet. Det kan följa principer för villkorlig åtkomst, t. ex. krav på en kompatibel enhet, och ger enkel inloggning för registrerade enheter. MSAL SDK: er som börjar med version 0.3.0 stöder en Service Broker som standard.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

[INTUNE Mam SDK](/intune/app-sdk-get-started) stöder MSAL för iOS från och med version [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL och ADAL i samma app

ADAL version 2.7.0, och senare, kan inte samverka med MSAL i samma program. Huvud orsaken är på grund av den delade undermodulens gemensamma kod. Eftersom mål-C inte stöder namn områden, kommer det att finnas två instanser av samma klass om du lägger till både ADAL-och MSAL-ramverket i programmet. Det finns ingen garanti för vilken en som plockas vid körning. Om båda SDK: erna använder samma version av klassen som är i konflikt med varandra kanske din app fortfarande fungerar. Men om det är en annan version kan din app uppleva oväntade krascher som är svåra att diagnostisera.

Det finns inte stöd för att köra ADAL och MSAL i samma produktions program. Men om du bara testar och migrerar dina användare från ADAL mål-C till MSAL för iOS och macOS kan du fortsätta att använda [ADAL mål-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). Det är den enda versionen som fungerar med MSAL i samma program. Det kommer inte att finnas några nya funktions uppdateringar för den här ADAL-versionen, så den bör endast användas för migrerings-och testnings ändamål. Din app bör inte förlita sig på långsiktig ADAL-och MSAL-samexistens.

Det finns inte stöd för ADAL och MSAL i samma program.
ADAL-och MSAL-samexistens mellan flera program stöds fullt ut.

## <a name="practical-migration-steps"></a>Praktiska steg för migrering

### <a name="app-registration-migration"></a>Migrering av app-registrering

Du behöver inte ändra ditt befintliga AAD-program för att växla till MSAL och aktivera AAD-konton. Om ditt ADAL-baserade program inte stöder Broker-autentisering måste du dock registrera en ny omdirigerings-URI för programmet innan du kan växla till MSAL.

Omdirigerings-URI: n måste ha följande format: `msauth.<app.bundle.id>://auth` . Ersätt `<app.bundle.id>` med programmets paket-ID. Ange omdirigerings-URI i [Azure Portal](https://aka.ms/MobileAppReg).

För att endast iOS ska stödja certifikatbaserad autentisering måste ytterligare en omdirigerings-URI registreras i programmet och Azure Portal i följande format: `msauth://code/<broker-redirect-uri-in-url-encoded-form>` . Till exempel `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Vi rekommenderar att alla appar registrerar båda omdirigerings-URI: er.

Om du vill lägga till stöd för det stegvisa godkännandet väljer du de API: er och behörigheter som appen är konfigurerad för att begära åtkomst till i din app-registrering under fliken **API-behörigheter** .

Om du migrerar från ADAL och vill stödja både AAD-och MSA-konton måste din befintliga program registrering uppdateras för att stödja båda. Vi rekommenderar inte att du uppdaterar din befintliga webbapp för att stödja både AAD och MSA direkt. Skapa i stället ett annat klient-ID som stöder både AAD och MSA för testning och när du har kontrollerat att alla scenarier fungerar, uppdaterar du den befintliga appen.

### <a name="add-msal-to-your-app"></a>Lägg till MSAL i din app

Du kan lägga till MSAL SDK i din app med det önskade paket hanterings verktyget. Se [detaljerade instruktioner här](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Uppdatera appens info. plist-fil

För endast iOS lägger du till programmets omdirigerings-URI-schema i filen info. plist. För ADAL-kompatibla appar bör det redan finnas. Standardvärdet för omdirigering av MSAL-URI är i formatet: `msauth.<app.bundle.id>` .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Lägg till följande scheman i appen info. plist under `LSApplicationQueriesSchemes` .

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Uppdatera din AppDelegate-kod

För endast iOS lägger du till följande i din AppDelegate. m-fil:

Mål-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
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

Detta gör att MSAL kan hantera svar från Broker-och webb komponenten.
Detta var inte nödvändigt i ADAL eftersom appen "swizzled" Delegerings metoder automatiskt. Det går inte att lägga till den manuellt eftersom det är mindre känsligt och ger programmet mer kontroll.

### <a name="enable-token-caching"></a>Aktivera cachelagring av token

Som standard cachelagrar MSAL appens tokens i nyckel ringen för iOS eller macOS. 

Så här aktiverar du cachelagring av token:
1. Se till att ditt program är korrekt signerat
2. Gå till Xcode-projekt inställningar **fliken > funktioner**  >  **Aktivera delning av nyckel ringar**
3. Klicka på **+** och ange följande **grupper för nyckel ringar** : 3. a för iOS, ange `com.microsoft.adalcache` 3. b för MacOS Enter `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Skapa MSALPublicClientApplication och växla till dess acquireToken-och acquireTokeSilent-anrop

Du kan skapa `MSALPublicClientApplication` med hjälp av följande kod:

Mål-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Införliva

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Anropa sedan konto hanterings-API: et för att se om det finns några konton i cachen:

Mål-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Införliva

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



eller Läs alla konton:

Mål-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Införliva

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Om ett konto hittas anropar du MSAL- `acquireTokenSilent` API: et:

Mål-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Införliva

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Nästa steg

Lär dig mer om [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md)