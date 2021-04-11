---
title: Läge för delad enhet för iOS-enheter
titleSuffix: Microsoft identity platform | Azure
description: Lär dig hur du aktiverar delad enhets läge så att Frontline arbetare kan dela en iOS-enhet
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 3353d87291492563acbd3a85bbae266c711377f2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611811"
---
# <a name="shared-device-mode-for-ios-devices"></a>Läge för delad enhet för iOS-enheter

>[!IMPORTANT]
> Den här funktionen [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Frontline-anställda som detalj handels företag, flyg besättnings medlemmar och fält tjänst arbetare använder ofta en delad mobil enhet för att utföra sitt arbete. Dessa delade enheter kan utgöra en säkerhets risk om användarna delar sina lösen ord eller PIN-filer, avsiktligt eller inte, för att komma åt kund-och affärs data på den delade enheten.

Med delad enhets läge kan du konfigurera en iOS 13-enhet eller en högre enhet så att den blir enklare och på ett säkert sätt delas av anställda. Anställda kan snabbt logga in och komma åt kund information. När de är klara med Skift eller uppgift kan de logga ut från enheten och den är omedelbart redo att användas av nästa anställd.

Delad enhets läge tillhandahåller även Microsoft Identity-baserad hantering av enheten.

Den här funktionen använder [appen Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) för att hantera användare på enheten och distribuera [Microsoft SSO-plugin-programmet för Microsoft Enterprise för Apple-enheter](apple-sso-plugin.md).

## <a name="create-a-shared-device-mode-app"></a>Skapa en app för delad enhets läge

För att skapa en app för delad enhets läge fungerar utvecklare och moln enhets administratörer tillsammans:

1. **Programutvecklare** skriver en app med ett enda konto (appar med flera konton stöds inte i läget för delad enhet) och skriver kod för att hantera saker som delade enhets utloggning.

1. **Enhets administratörer** förbereder enheten för delning med hjälp av en MDM-Provider (Mobile Device Management) som Microsoft Intune för att hantera enheterna i organisationen. MDM push-överför Microsoft Authenticator-appen till enheterna och aktiverar "delat läge" för varje enhet genom en profil uppdatering till enheten. Den här inställningen för delat läge är det som ändrar beteendet för de appar som stöds på enheten. Den här konfigurationen från MDM-providern anger delad enhets läge för enheten och aktiverar [Microsoft Enterprise SSO-plugin-programmet för Apple-enheter](apple-sso-plugin.md) som krävs för delad enhets läge.

1. [**Krävs endast under offentlig för hands version**] En användare med rollen som [moln enhets administratör](../roles/permissions-reference.md#cloud-device-administrator) måste sedan starta [Microsoft Authenticator-appen](../user-help/user-help-auth-app-overview.md) och ansluta sin enhet till organisationen.

    Konfigurera medlemskap för organisations roller i Azure Portal: **Azure Active Directory**  >  **roller och administratörer**  >  **moln enhets administratör**

Följande avsnitt hjälper dig att uppdatera programmet så att det stöder delat enhets läge.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Använd Intune för att aktivera delat enhets läge & SSO-tillägg

> [!NOTE]
> Följande steg krävs endast under den offentliga för hands versionen.

Enheten måste konfigureras för att stödja delat enhets läge. Den måste ha iOS 13 + installerat och vara MDM-registrerad. MDM-konfigurationen måste också aktivera [Microsoft Enterprise SSO-plugin-programmet för Apple-enheter](apple-sso-plugin.md). Mer information om SSO-tillägg finns i [Apple-videon](https://developer.apple.com/videos/play/tech-talks/301/).

1. I konfigurations portalen för Intune ber du enheten att aktivera [Microsoft Enterprise SSO-plugin-programmet för Apple-enheter](apple-sso-plugin.md) med följande konfiguration:

    - **Typ**: omdirigera
    - **Tilläggs-ID**: com. Microsoft. azureauthenticator. ssoextension
    - **Team-ID**: (det här fältet behövs inte för iOS)
    - **URL: er**:   
        - `https://login.microsoftonline.com`
        - `https://login.microsoft.com`
        - `https://sts.windows.net`
        - `https://login.partner.microsoftonline.cn`
        - `https://login.chinacloudapi.cn`
        - `https://login.microsoftonline.de`
        - `https://login.microsoftonline.us`
        - `https://login.usgovcloudapi.net`
        - `https://login-us.microsoftonline.com`
    - **Ytterligare data att konfigurera**:
      - Nyckel: sharedDeviceMode
      - Typ: boolesk
      - Värde: true

    Mer information om hur du konfigurerar med Intune finns i [dokumentation om Intune-konfiguration](/intune/configuration/ios-device-features-settings).

1. Konfigurera sedan MDM för att skicka Microsoft Authenticator-appen till enheten via en MDM-profil.

    Ange följande konfigurations alternativ för att aktivera delad enhets läge:

    - Konfiguration 1:
      - Nyckel: sharedDeviceMode
      - Typ: boolesk
      - Värde: true

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Ändra iOS-programmet så att det stöder delad enhets läge

Användarna är beroende av att deras data inte läcker till en annan användare. Följande avsnitt innehåller användbara signaler för att ange för programmet att en ändring har skett och bör hanteras.

Du ansvarar för att kontrol lera status för användaren på enheten varje gång appen används och sedan rensa den tidigare användarens data. Detta inkluderar om det läses in på nytt från bakgrunden vid multi-tasking.

Om en användare ändras bör du se till att både den tidigare användarens data är avmarkerad och att alla cachelagrade data som visas i programmet tas bort. Vi rekommenderar starkt att du och ditt företag genomför en säkerhets gransknings process när du har uppdaterat din app för att stödja delat enhets läge.

### <a name="detect-shared-device-mode"></a>Identifiera delad enhets läge

Att identifiera delad enhets läge är viktigt för ditt program. Många program kräver en ändring i användar upplevelsen (UX) när programmet används på en delad enhet. Ditt program kan till exempel ha en "registrerings funktion" som inte passar för en Frontline Worker eftersom de förmodligen redan har ett konto. Du kanske också vill lägga till extra säkerhet för programmets hantering av data om det är i delat enhets läge.

Använd `getDeviceInformationWithParameters:completionBlock:` API: et i `MSALPublicClientApplication` för att avgöra om en app körs på en enhet i delat enhets läge.

Följande kodfragment visar exempel på hur du använder API: `getDeviceInformationWithParameters:completionBlock:` et.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Hämta den inloggade användaren och ta reda på om en användare har ändrats på enheten

En annan viktig del av stödda delade enhets lägen är att bestämma status för användaren på enheten och rensa program data om en användare har ändrats eller om det inte finns någon användare på enheten. Du ansvarar för att säkerställa att data inte läcker till en annan användare.

Du kan använda `getCurrentAccountWithParameters:completionBlock:` API för att fråga det aktuella inloggade kontot på enheten.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Logga in en användare globalt

När en enhet har kon figurer ATS som en delad enhet kan ditt program anropa `acquireTokenWithParameters:completionBlock:` API: et för att logga in på kontot. Kontot är tillgängligt globalt för alla berättigade appar på enheten när den första appen loggar in på kontot.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Logga ut en användare globalt

Följande kod tar bort det inloggade kontot och rensar cachelagrade token från inte bara appen, utan även från den enhet som är i läget för delad enhet. Det innebär dock inte att *data* rensas från ditt program. Du måste ta bort data från ditt program och rensa alla cachelagrade data som programmet kan visa för användaren.

#### <a name="clear-browser-state"></a>Rensa webb läsar status

> [!NOTE]
> Följande steg krävs endast under den offentliga för hands versionen.

I den här offentliga för hands versionen rensar [Microsoft Enterprise SSO-plugin-programmet för Apple-enheter](apple-sso-plugin.md) endast tillstånd för program. Det tar inte bort status i Safari-webbläsaren. Vi rekommenderar att du rensar webbläsarsessionen manuellt för att se till att inga spår av användar status är kvar bakom. Du kan använda valfri `signoutFromBrowser` egenskap som visas nedan för att rensa eventuella cookies. Detta leder till att webbläsaren startar på enheten kort.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Nästa steg

Följande kod exempel på GitHub innehåller ett exempel på att köra en Frontline Worker-app på en iOS-enhet i läget delad enhet för att se läget delad enhet:

[MSAL iOS Swift Microsoft Graph API-exempel](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
