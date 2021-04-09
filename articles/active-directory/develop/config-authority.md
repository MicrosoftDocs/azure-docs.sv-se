---
title: Konfigurera identitets leverantörer (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du använder olika myndigheter som B2C, suveräna moln och gäst användare med MSAL för iOS och macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d8a176fff0da932d0fafd40b9ab895b635acc5f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96169451"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Gör så här: Konfigurera MSAL för iOS och macOS för att använda olika identitets leverantörer

Den här artikeln visar hur du konfigurerar din Microsoft Authentication Library-app för iOS och macOS (MSAL) för olika myndigheter, till exempel Azure Active Directory (Azure AD), företags-till-konsument (B2C), suveräna moln och gäst användare.  I den här artikeln kan du vanligt vis tänka på en utfärdare som en identitets leverantör.

## <a name="default-authority-configuration"></a>Standard auktoritets konfiguration

`MSALPublicClientApplication` är konfigurerad med standard auktoritets-URL: en `https://login.microsoftonline.com/common` , som är lämplig för de flesta Azure Active Directory-scenarier (AAD). Om du inte implementerar avancerade scenarier som nationella moln eller arbetar med B2C behöver du inte ändra det.

> [!NOTE]
> Modern autentisering med Active Directory Federation Services (AD FS) som identitets leverantör (ADFS) stöds inte (se [ADFS för utvecklare](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) för mer information). ADFS stöds via Federation.

## <a name="change-the-default-authority"></a>Ändra standard auktoritet

I vissa fall, t. ex. företags-till-konsument (B2C), kan du behöva ändra standard utfärdaren.

### <a name="b2c"></a>B2C

För att arbeta med B2C kräver [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) en annan auktoritets konfiguration. MSAL känner igen ett URL-format för auktoritet som B2C. Formatet känd B2C Authority är `https://<host>/tfp/<tenant>/<policy>` till exempel `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy` . Du kan dock också använda andra URL: er som stöds av B2C genom att deklarera utfärdare som B2C-auktoritet.

För att stödja ett godtyckligt URL-format för B2C `MSALB2CAuthority` kan ställas in med en godtycklig URL, så här:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Alla B2C-myndigheter som inte använder standard formatet för B2C-utfärdare måste deklareras som kända myndigheter.

Lägg till varje annan B2C-auktoritet i listan med kända utfärdare även om myndigheterna bara skiljer sig åt i principen.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

När din app begär en ny princip måste auktoritets-URL: en ändras eftersom auktoritets-URL: en är olika för varje princip. 

Konfigurera ett B2C-program genom att ange `@property MSALAuthority *authority` en instans av `MSALB2CAuthority` i `MSALPublicClientApplicationConfig` innan du skapar `MSALPublicClientApplication` , så här:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Nationella moln

Om din app körs i ett suveränt moln kan du behöva ändra auktoritets-URL: en i `MSALPublicClientApplication` . I följande exempel anges auktoritets-URL: en för att fungera med tyska AAD-molnet:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Du kan behöva skicka olika omfattningar till varje suverän moln. Vilka omfattningar som ska skickas beror på vilken resurs du använder. Du kan till exempel använda `"https://graph.microsoft.com/user.read"` i globalt moln och `"https://graph.microsoft.de/user.read"` i tyskt moln.

### <a name="signing-a-user-into-a-specific-tenant"></a>Signera en användare till en speciell klient

När auktoritets-URL: en är inställt på `"login.microsoftonline.com/common"` , kommer användaren att loggas in på sin hem klient. Vissa appar kan dock behöva signera användaren till en annan klient och vissa appar fungerar bara med en enda klient.

Om du vill signera användaren till en speciell klient konfigurerar du `MSALPublicClientApplication` med en speciell myndighet. Exempel:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Följande visar hur du signerar en användare till en speciell klient:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Myndigheter som stöds

### <a name="msalauthority"></a>MSALAuthority

`MSALAuthority`Klassen är den grundläggande abstrakta klassen för MSAL-auktoritets klasserna. Försök inte att skapa en instans av den med hjälp av `alloc` eller `new` . Skapa i stället en av dess underklasser direkt ( `MSALAADAuthority` , `MSALB2CAuthority` ) eller Använd fabriks metoden `authorityWithURL:error:` för att skapa underklasser med en auktoritets-URL.

Använd `url` egenskapen för att hämta en normaliserad auktoritets-URL. Extra parametrar och Sök vägs komponenter eller fragment som inte tillhör en instans finns inte i den returnerade normaliserade auktoritets-URL: en.

Följande är underklasser till `MSALAuthority` som du kan skapa en instans av, beroende på vilken auktoritet som ska användas.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` representerar en AAD-utfärdare. Auktoritets-URL: en ska ha följande format, där `<port>` är valfritt: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` representerar en B2C-utfärdare. Som standard ska URL: en för B2C-utfärdare ha följande format, där `<port>` är valfritt: `https://<host>:<port>/tfp/<tenant>/<policy>` . MSAL stöder dock även andra godtyckliga B2C-auktoritets format.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md)