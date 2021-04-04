---
title: Använda AppAuth i ett iOS-program
titleSuffix: Azure AD B2C
description: Så här skapar du en iOS-app som använder AppAuth med Azure Active Directory B2C för att hantera användar identiteter och autentisera användare.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6064bd2c62922abea44508b8bf6cdfa3e7ecbc92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953312"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Logga in med ett iOS-program

Microsofts identitetsplattform använder öppna standarder som OAuth2 och OpenID Connect. Med ett öppet standard protokoll får fler utvecklare möjlighet att välja ett bibliotek som ska integreras med våra tjänster. Vi har angett den här genom gången och andra som kan hjälpa utvecklare att skriva program som ansluter till Microsoft Identity Platform. De flesta bibliotek som implementerar [RFC6749 OAuth2-specifikationen](https://tools.ietf.org/html/rfc6749) kan ansluta till Microsoft Identity Platform.

> [!WARNING]
> Microsoft tillhandahåller inte korrigeringar för bibliotek från tredje part och har inte utfört en granskning av dessa bibliotek. Det här exemplet använder ett bibliotek från tredje part med namnet AppAuth som har testats för kompatibilitet i grundläggande scenarier med Azure AD B2C. Problem och funktions begär Anden ska dirigeras till bibliotekets projekt med öppen källkod. Mer information finns i [den här artikeln](../active-directory/develop/reference-v2-libraries.md).
>
>

Om du inte har använt OAuth2 eller OpenID Connect kan det hända att mycket av den här exempel konfigurationen inte passar dig. Vi rekommenderar att du läser en kort [översikt över protokollet som vi har dokumenterat här](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Hämta en Azure AD B2C-katalog
Innan du kan använda Azure AD B2C måste du skapa en katalog eller klient. En katalog är en behållare för alla användare, appar, grupper och mer. Om du inte redan har en [skapar du en B2C-katalog](tutorial-create-tenant.md) innan du fortsätter.

## <a name="create-an-application"></a>Skapa ett program

Registrera sedan ett program i Azure AD B2C klient organisationen. Det ger Azure AD den information som krävs för att kommunicera säkert med din app.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Registrera **program-ID: t (Client)** för användning i ett senare steg.

Registrera även en anpassad omdirigerings-URI för användning i ett senare steg. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Skapa dina användar flöden
I Azure AD B2C definieras varje användar upplevelse av ett [användar flöde](user-flow-overview.md). Det här programmet innehåller en identitets upplevelse: en kombinerad inloggning och registrering. När du skapar användar flödet måste du se till att:

* Under **registrerings attribut** väljer du attributets **visnings namn**.  Du kan också välja andra attribut.
* Under **program anspråk** väljer du **visnings namn** för anspråk och **användarens objekt-ID**. Du kan även välja andra anspråk.
* Kopiera **namnet** på varje användar flöde efter att du har skapat det. Ditt användar flödes namn föregås av `b2c_1_` när du sparar användar flödet.  Du behöver användar flödes namnet senare.

När du har skapat dina användar flöden är du redo att skapa din app.

## <a name="download-the-sample-code"></a>Hämta exempel koden
Vi har tillhandahållit ett fungerande exempel som använder AppAuth med Azure AD B2C [på GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Du kan ladda ned koden och köra den. Följ anvisningarna i [Readme.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md)om du vill använda en egen Azure AD B2C klient.

Det här exemplet skapades genom att följa README-instruktionerna i [iOS AppAuth-projektet på GitHub](https://github.com/openid/AppAuth-iOS). För mer information om hur exemplet och biblioteket fungerar, referera till README-AppAuth för GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Ändra appen så att den använder Azure AD B2C med AppAuth

> [!NOTE]
> AppAuth stöder iOS 7 och senare.  Men för att stödja sociala inloggningar på Google krävs SFSafariViewController som kräver iOS 9 eller senare.
>

### <a name="configuration"></a>Konfiguration

Du kan konfigurera kommunikation med Azure AD B2C genom att ange både behörighets slut punkten och URI för token-slut punkt.  Om du vill generera dessa URI: er behöver du följande information:
* Klient-ID (till exempel contoso.onmicrosoft.com)
* Användar flödes namn (till exempel B2C \_ 1 \_ signupin)

URI: n för token-slutpunkt kan genereras genom att ersätta klient \_ -ID och princip \_ namnet i följande URL:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

URI: n för auktoriserings slut punkt kan genereras genom att ersätta klient \_ -ID och princip \_ namnet i följande URL:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Kör följande kod för att skapa AuthorizationServiceConfiguration-objektet:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Auktorisera

När du har konfigurerat eller hämtat en Authorization service-konfiguration kan en auktoriseringsbegäran konstrueras. Om du vill skapa begäran behöver du följande information:

* Klient-ID (program-ID) som du registrerade tidigare. Till exempel `00000000-0000-0000-0000-000000000000`.
* Anpassad omdirigerings-URI som du registrerade tidigare. Till exempel `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Båda objekten bör ha sparats när du [registrerade appen](#create-an-application).

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Om du vill konfigurera ditt program för att hantera omdirigeringen till URI: n med det anpassade schemat måste du uppdatera listan med URL-scheman i info. pList:
* Öppna info. pList.
* Hovra över en rad som "paketera OS-typ kod" och klicka på \+ symbolen.
* Byt namn på den nya raden "URL types".
* Öppna trädet genom att klicka på pilen till vänster om "URL-typer".
* Klicka på pilen till vänster om "objekt 0" för att öppna trädet.
* Byt namn på det första objektet under objekt 0 till URL-scheman.
* Klicka på pilen till vänster om "URL-scheman" för att öppna trädet.
* I kolumnen "värde" finns ett tomt fält till vänster om "Item 0" under "URL-scheman".  Ange värdet för programmets unika schema.  Värdet måste matcha det schema som används i redirectURL när du skapar OIDAuthorizationRequest-objektet.  I exemplet används schemat com. onmicrosoft. fabrikamb2c. ExampleApp.

Läs AppAuth- [guiden](https://openid.github.io/AppAuth-iOS/) om hur du Slutför resten av processen. Om du snabbt behöver komma igång med en fungerande app kan du kolla [in exemplet](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Följ stegen i [Readme.MD](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) för att ange din egen Azure AD B2C konfiguration.

Vi är alltid öppna för feedback och förslag! Om du har problem med den här artikeln eller har rekommendationer för att förbättra det här innehållet, skulle vi uppskatta din feedback längst ned på sidan. För funktions begär Anden lägger du till dem i [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).