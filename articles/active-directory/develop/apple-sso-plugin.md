---
title: Microsoft Enterprise SSO-plugin-program för Apple-enheter
titleSuffix: Microsoft identity platform | Azure
description: Lär dig mer om Microsofts Azure Active Directory SSO-plugin-program för iOS, iPad och macOS-enheter.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 96fbf23128896f23beee70a6b3d32b4b87a454ea
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427104"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO-plugin-program för Apple-enheter (för hands version)

>[!IMPORTANT]
> Den här funktionen [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Microsoft Enterprise SSO-plugin-programmet för Apple-enheter* tillhandahåller enkel inloggning (SSO) för Azure Active Directory (Azure AD)-konton på MacOS, iOS och iPad i alla program som har stöd för [enkel inloggnings funktion i Enterprise](https://developer.apple.com/documentation/authenticationservices) . Detta inkluderar äldre program som ditt företag kan vara beroende av men som ännu inte stöder de senaste identitets biblioteken eller protokollen. Microsoft har arbetat nära med Apple för att utveckla det här plugin-programmet för att öka programmets användbarhet samtidigt som det ger bästa möjliga skydd som Apple och Microsoft kan tillhandahålla.

SSO-plugin-programmet för företag är för närvarande tillgängligt som en inbyggd funktion i följande appar:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) -iOS, iPad
* Microsoft Intune [företagsportal](/mem/intune/apps/apps-company-portal-macos) – MacOS

## <a name="features"></a>Funktioner

Microsoft Enterprise SSO-plugin-programmet för Apple-enheter ger följande fördelar:

- Ger enkel inloggning för Azure AD-konton i alla program som har stöd för Apples Enterprise Single Sign-On-funktion.
- Kan aktive ras med en lösning för hantering av mobila enheter (MDM).
- Utökar SSO till program som ännu inte använder Microsoft Identity Platform-bibliotek.
- Utökar SSO till program som använder OAuth2, OpenID Connect och SAML.

## <a name="requirements"></a>Krav

Använda Microsoft Enterprise SSO-plugin-programmet för Apple-enheter:

- Enheten måste ha **stöd** för och ha en app som innehåller Microsoft Enterprise SSO-plugin-programmet för Apple-enheter **installerade**:
  - iOS 13.0 +: [Microsoft Authenticator app](../user-help/user-help-auth-app-overview.md)
  - iPad 13.0 +: [Microsoft Authenticator app](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 +: [Intune-företagsportal app](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Enheten måste vara **MDM-registrerad** (till exempel med Microsoft Intune).
- Konfigurationen måste skickas **till enheten** för att aktivera SSO-plugin-programmet för företag på enheten. Den här säkerhets begränsningen krävs av Apple.

### <a name="ios-requirements"></a>iOS-krav:
- iOS 13,0 eller senare måste vara installerat på enheten.
- Ett Microsoft-program som tillhandahåller Microsoft Enterprise SSO-plugin-programmet för Apple-enheter måste installeras på enheten. För en offentlig för hands version är de här programmen [Microsoft Authenticator-appen](/intune/user-help/user-help-auth-app-overview.md).


### <a name="macos-requirements"></a>macOS-krav:
- macOS 10,15 eller högre måste vara installerat på enheten. 
- Ett Microsoft-program som tillhandahåller Microsoft Enterprise SSO-plugin-programmet för Apple-enheter måste installeras på enheten. För den allmänt tillgängliga för hands versionen inkluderar programmen [Intune-företagsportal-appen](/intune/user-help/enroll-your-device-in-intune-macos-cp.md).

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Aktivera plugin-programmet för SSO med hantering av mobila enheter (MDM)

### <a name="microsoft-intune-configuration"></a>Microsoft Intune konfiguration

Om du använder Microsoft Intune som MDM-tjänst kan du använda inbyggda konfigurations profil inställningar för att aktivera Microsoft Enterprise SSO-plugin-programmet.

Konfigurera först inställningarna för [tillägg för enkel inloggning](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) för en konfigurations profil och [tilldela profilen till en användar-eller enhets grupp](/mem/intune/configuration/device-profile-assign) (om den inte redan har tilldelats).

Profil inställningarna som aktiverar SSO-plugin-programmet tillämpas automatiskt på gruppens enheter nästa gången varje enhet checkar in med Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Manuell konfiguration för andra MDM-tjänster

Om du inte använder Microsoft Intune för hantering av mobila enheter använder du följande parametrar för att konfigurera Microsoft Enterprise SSO-plugin-programmet för Apple-enheter.

#### <a name="ios-settings"></a>Inställningar för iOS:

- **Tilläggs-ID**: `com.microsoft.azureauthenticator.ssoextension`
- **Team-ID**: (det här fältet behövs inte för iOS)

#### <a name="macos-settings"></a>Inställningar för macOS:

- **Tilläggs-ID**: `com.microsoft.CompanyPortalMac.ssoextension`
- **Team-ID**: `UBF8T346G9`

#### <a name="common-settings"></a>Vanliga inställningar:

- **Typ**: omdirigera
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>Ytterligare konfigurationsalternativ
Ytterligare konfigurations alternativ kan läggas till för att utöka SSO-funktioner till ytterligare appar.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Aktivera SSO för appar som inte använder ett bibliotek med Microsoft Identity Platform

SSO-plugin-programmet gör det möjligt för alla program att delta i enkel inloggning, även om det inte har utvecklats med hjälp av ett Microsoft-SDK som Microsoft Authentication Library (MSAL).

SSO-plugin-programmet installeras automatiskt av enheter som har laddat ned Microsoft Authenticator-appen på iOS och iPad eller Intune-företagsportal app på macOS och registrerat sin enhet med din organisation. I din organisation används Authenticator-appen idag för scenarier som Multi-Factor Authentication, lösen ords lös autentisering och villkorlig åtkomst. Den kan aktive ras för dina program med hjälp av en MDM-Provider, men Microsoft har gjort det enkelt att konfigurera i Microsoft Endpoint Manager för Intune. En lista över tillåtna används för att konfigurera dessa program att använda SSO-plugin-programmet.

>[!IMPORTANT]
> Endast appar som använder inbyggd teknik för Apple-nätverk eller webviews stöds. Om ett program levererar sin egen implementering av nätverks skikt, stöds inte Microsoft Enterprise SSO-plugin-programmet.  

Använd följande parametrar för att konfigurera Microsoft Enterprise SSO-plugin-programmet för appar som inte använder ett bibliotek för Microsoft Identity Platform:

Om du vill ange en lista över vissa appar:

- **Nyckel**: `AppAllowList`
- **Typ**: `String`
- **Värde**: kommaavgränsad lista över programpaket-ID: n för program som tillåts delta i SSO
- **Exempel**: `com.contoso.workapp, com.contoso.travelapp`

Eller om du vill ange en lista över prefix:
- **Nyckel**: `AppPrefixAllowList`
- **Typ**: `String`
- **Värde**: kommaavgränsad lista över programpaket-ID-prefix för de program som tillåts delta i SSO. Observera att detta gör att alla appar som börjar med ett visst prefix kan delta i SSO
- **Exempel**: `com.contoso., com.fabrikam.`

[Samskickade appar](./application-consent-experience.md) som tillåts av MDM-administratören för att delta i SSO kan tyst hämta en token för slutanvändaren. Därför är det viktigt att bara lägga till betrodda program i listan över tillåtna. 

>[!NOTE]
> Du behöver inte lägga till program som använder MSAL eller ASWebAuthenticationSession i den här listan. Dessa program är aktiverade som standard. 

##### <a name="how-to-discover-app-bundle-identifiers-on-ios-devices"></a>Identifiera appars paket identifierare på iOS-enheter

Apple tillhandahåller inget enkelt sätt att identifiera paket-ID: n från App Store. Det enklaste sättet att identifiera paket-ID: n för de appar som vill använda för enkel inloggning är att be din leverantör eller app-utvecklare. Om det alternativet inte är tillgängligt kan du använda MDM-konfigurationen för att identifiera paket-ID: n. 

Aktivera tillfälligt följande flagga i MDM-konfigurationen:

- **Nyckel**: `admin_debug_mode_enabled`
- **Typ**: `Integer`
- **Värde**: 1 eller 0

När den här flaggan har loggat in på iOS-appar på den enhet som du vill känna till paket-ID: t för. Öppna sedan Microsoft Authenticator app-> hjälp-> skicka loggar-> Visa loggar. 

Leta efter följande rad i logg filen:

`[ADMIN MODE] SSO extension has captured following app bundle identifiers:`

Detta ska avbilda alla programpaket-ID: n synliga för SSO-tillägget. Du kan sedan använda dessa identifierare för att konfigurera SSO för dessa appar. 

#### <a name="allow-user-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Tillåt användare att logga in från okända program och Safari-webbläsaren.

Som standard ger Microsoft Enterprise SSO-plugin-programmet enkel inloggning för auktoriserade appar när en användare har loggat in från en app som använder ett Microsoft Identity Platform-bibliotek som ADAL eller MSAL. Microsoft Enterprise SSO-plugin-programmet kan också hämta en delad autentiseringsuppgift när den anropas av en annan app som använder ett bibliotek för Microsoft Identity Platform under en ny token-hämtning.

Genom att aktivera `browser_sso_interaction_enabled` flagga kan appar som inte använder ett Microsoft Identity Platform-bibliotek utföra den första start programmet och få en delad autentiseringsuppgift. Det innebär också att Safari-webbläsare kan utföra den första initieringen och få en delad autentiseringsuppgift. Om Microsoft Enterprise SSO-plugin-programmet inte har någon delad autentiseringsuppgift ännu, försöker det att hämta en när en inloggning begärs från en Azure AD-URL i Safari-webbläsare, ASWebAuthenticationSession, SafariViewController eller något annat tillåtet internt program.  

- **Nyckel**: `browser_sso_interaction_enabled`
- **Typ**: `Integer`
- **Värde**: 1 eller 0

För macOS krävs den här inställningen för att få en mer konsekvent upplevelse för alla appar. För iOS och iPad krävs inte den här inställningen eftersom de flesta appar använder Microsoft Authenticator-programmet för inloggning. Men om du har vissa program som inte använder Microsoft Authenticator på iOS eller iPad, kommer den här flaggan att förbättra upplevelsen så vi rekommenderar att du aktiverar inställningen. Det är inaktiverat som standard.

#### <a name="disable-asking-for-mfa-on-initial-bootstrapping"></a>Inaktivera att du ber om MFA vid första start

Som standard tillfrågas Microsoft Enterprise SSO-plugin-programmet alltid användaren för Multi-Factor Authentication (MFA) vid första start och hämtning av delade autentiseringsuppgifter, även om det inte krävs för det aktuella programmet som användaren har startat. Detta är så att den delade autentiseringsuppgiften enkelt kan användas i alla ytterligare program utan att användaren tillföras om MFA krävs senare. Detta minskar de tider då användaren behöver uppmanas till enheten och är vanligt vis ett lämpligt beslut.

Aktivering `browser_sso_disable_mfa` stänger av detta och kommer bara att uppmana användaren när MFA krävs av ett program eller en resurs. 

- **Nyckel**: `browser_sso_disable_mfa`
- **Typ**: `Integer`
- **Värde**: 1 eller 0

Vi rekommenderar att du behåller den här flaggan inaktive rad eftersom den minskar de tider då användaren behöver uppmanas till enheten. Om din organisation sällan använder MFA kanske du vill aktivera flaggan, men vi rekommenderar att du använder MFA oftare i stället. Av den anledningen är det inaktiverat som standard.

#### <a name="disable-oauth2-application-prompts"></a>Inaktivera OAuth2-programprompter

Microsoft Enterprise SSO-plugin-programmet tillhandahåller SSO genom att bifoga delade autentiseringsuppgifter till nätverks begär Anden som kommer från tillåtna program. Vissa OAuth2-program kan dock felaktigt framtvinga meddelanden om slutanvändare i protokoll skiktet. Om detta händer ser du att delade autentiseringsuppgifter ignoreras för dessa appar och användaren uppmanas att logga in även om Microsoft Enterprise SSO-plugin-programmet fungerar för andra program.  

Aktiverings `disable_explicit_app_prompt` flaggan begränsar möjligheten för både ursprungliga och webb program att tvinga fram en slutanvändares fråga om protokoll skiktet och kringgå SSO.

- **Nyckel**: `disable_explicit_app_prompt`
- **Typ**: `Integer`
- **Värde**: 1 eller 0

Vi rekommenderar att du aktiverar den här flaggan för att få mer konsekvent upplevelse i alla appar. Det är inaktiverat som standard. 

#### <a name="enable-sso-through-cookies-for-specific-application"></a>Aktivera SSO via cookies för specifika program

Ett litet antal appar kan vara inkompatibla med SSO-tillägget. Mer specifikt kan appar som har avancerade nätverks inställningar uppleva oväntade problem när de aktive ras för SSO (t. ex. kan ett fel meddelande visas om att nätverks förfrågan har avbrutits eller avbrutits). 

Om du har problem med att logga in med hjälp av metoden som beskrivs i `Enable SSO for apps that don't use MSAL` avsnittet kan du testa den alternativa konfigurationen för dessa appar. 

Använd följande parametrar för att konfigurera Microsoft Enterprise SSO-plugin-programmet för de aktuella apparna:

- **Nyckel**: `AppCookieSSOAllowList`
- **Typ**: `String`
- **Värde**: kommaavgränsad lista över programpaket-ID-prefix för de program som tillåts delta i SSO. Observera att detta gör att alla appar som börjar med ett visst prefix kan delta i SSO
- **Exempel**: `com.contoso.myapp1, com.fabrikam.myapp2`

Observera att program som har Aktiver ATS för enkel inloggning med den här mekanismen måste läggas till både `AppCookieSSOAllowList` och `AppPrefixAllowList` .

Vi rekommenderar att du bara testar det här alternativet för program som har oväntade inloggnings fel. 

#### <a name="use-intune-for-simplified-configuration"></a>Använd Intune för förenklad konfiguration

Som tidigare nämnts kan du använda Microsoft Intune som MDM-tjänst för att under lätta konfigurationen av Microsoft Enterprise SSO-plugin-programmet, inklusive aktivera plugin-programmet och lägga till dina äldre appar i en lista över tillåtna användare så att de får SSO. Mer information finns i dokumentation om [Intune-konfiguration](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Använda SSO-plugin-programmet i ditt program

[Microsoft Authentication Library (MSAL) för Apple-enheter](https://github.com/AzureAD/microsoft-authentication-library-for-objc) version 1.1.0 och högre stöder Microsoft Enterprise SSO-plugin-programmet för Apple-enheter. Det är det rekommenderade sättet att lägga till stöd för Microsoft-plugin-programmet för enkel inloggning och se till att du får alla funktioner i Microsoft Identity Platform.

Om du skapar ett program för Frontline Worker-scenarier, se [delad enhets läge för iOS-enheter](msal-ios-shared-devices.md) om du vill ha ytterligare konfiguration av funktionen.

## <a name="how-the-sso-plug-in-works"></a>Så här fungerar SSO-plugin-programmet

Microsoft Enterprise SSO-plugin-programmet är beroende av [Apples Enterprise Single Sign-On Framework](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Identitets leverantörer som är inbyggda i ramverket kan fånga nätverks trafik för sina domäner och förbättra eller ändra hur dessa begär Anden hanteras. SSO-plugin-programmet kan till exempel Visa ytterligare gränssnitt för att samla in autentiseringsuppgifter för slutanvändare på ett säkert sätt, kräva MFA eller tyst tillhandahålla token till programmet.

Inbyggda program kan också implementera anpassade åtgärder och prata direkt med SSO-plugin-programmet.
Du kan lära dig mer om Single Sign-in-ramverket i den här [2019 WWDC-videon från Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-a-microsoft-identity-platform-library"></a>Program som använder ett bibliotek för Microsoft Identity Platform

[Microsoft Authentication Library (MSAL) för Apple-enheter](https://github.com/AzureAD/microsoft-authentication-library-for-objc) version 1.1.0 och högre stöder Microsoft Enterprise SSO-plugin-programmet för Apple-enheter för arbets-och skol konton. 

Det behövs ingen särskild konfiguration om du har följt [alla rekommenderade steg](./quickstart-v2-ios.md) och använt standardvärdet för [omdirigerings-URI](./redirect-uris-ios.md). När du kör på en enhet som har SSO-plugin-programmet så anropar MSAL automatiskt för alla interaktiva och tysta token-begäranden, samt konto uppräkning och borttagning av konto. Eftersom MSAL implementerar inbyggt SSO-plugin-protokoll som förlitar sig på anpassade åtgärder, ger den här installationen den smidigaste inbyggda upplevelsen för slutanvändaren. 

Om SSO-plugin-programmet inte är aktiverat av MDM, men Microsoft Authenticator-appen finns på enheten, använder MSAL i stället Microsoft Authenticator-appen för alla interaktiva token-begäranden. SSO-plugin-programmet delar SSO med Microsoft Authenticator-appen.

### <a name="applications-that-dont-use-a-microsoft-identity-platform-library"></a>Program som inte använder ett bibliotek för Microsoft Identity Platform

Program som inte använder ett Microsoft Identity Platform-bibliotek som MSAL kan fortfarande få SSO om en administratör lägger till dem i listan över tillåtna explicit. 

Det behövs inga kod ändringar i dessa appar så länge följande villkor är uppfyllda:

- Programmet använder Apple Frameworks för att köra nätverks begär Anden (till exempel [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- Programmet använder standard protokoll för att kommunicera med Azure AD (till exempel OAuth2, SAML, WS-Federation)
- Programmet samlar inte in användar namn och lösen ord i klartext i det inbyggda användar gränssnittet

I det här fallet anges SSO när programmet skapar en nätverks förfrågan och öppnar en webbläsare för att logga in användaren i. När en användare omdirigeras till en inloggnings-URL för Azure AD, verifierar SSO-plugin-programmet URL: en och kontrollerar om det finns en SSO-autentiseringsuppgift som är tillgänglig för den URL: en. Om det finns en så skickar SSO-plugin-programmet SSO-autentiseringsuppgiften till Azure AD, som auktoriserar programmet att slutföra nätverksbegäran utan att be användaren ange sina autentiseringsuppgifter. Dessutom, om enheten är känd för Azure AD, så skickar SSO-plugin-programmet även enhets certifikatet för att uppfylla den enhets villkorliga åtkomst kontrollen. 

För att stödja SSO för icke-MSAL appar implementerar SSO-plugin-programmet ett protokoll som liknar plugin-programmet för Windows-webbläsare, [vilket beskrivs i vad är en primär uppdateringstoken?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Jämfört med MSAL-baserade appar fungerar SSO-plugin-programmet mer transparent för icke-MSAL appar genom att integrera med den befintliga webbläsarens inloggnings upplevelse som appar tillhandahåller. Slutanvändaren ser sin välbekanta upplevelse, med fördelen att du inte behöver utföra ytterligare inloggningar i vart och ett av programmen. I stället för att visa den inbyggda konto väljaren, lägger till exempel SSO-plugin-programmet SSO-sessioner till den webbaserade konto väljaren. 

## <a name="next-steps"></a>Nästa steg

För mer information om delad enhets läge på iOS, se [delad enhets läge för iOS-enheter](msal-ios-shared-devices.md).
