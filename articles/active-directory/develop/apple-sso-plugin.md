---
title: Microsoft Enterprise SSO-plugin-program för Apple-enheter
titleSuffix: Microsoft identity platform | Azure
description: Läs om plugin-programmet för Azure Active Directory SSO för iOS, iPad och macOS-enheter.
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
ms.openlocfilehash: 05bfcc86c72d9eb393da919035ce198948b943f2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559136"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Microsoft Enterprise SSO-plugin-program för Apple-enheter (för hands version)

>[!IMPORTANT]
> Den här funktionen [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Microsoft Enterprise SSO-plugin-programmet för Apple-enheter* tillhandahåller enkel inloggning (SSO) för Azure Active Directory (Azure AD)-konton på MacOS, iOS och iPad i alla program som har stöd för [enkel inloggnings funktion i Enterprise](https://developer.apple.com/documentation/authenticationservices) . Plugin-programmet tillhandahåller SSO för till och med gamla program som ditt företag kan vara beroende av men som ännu inte stöder de senaste identitets biblioteken eller protokollen. Microsoft har arbetat nära med Apple för att utveckla det här plugin-programmet för att öka programmets användbarhet samtidigt som det ger bästa möjliga skydd.

SSO-plugin-programmet för företag är för närvarande en inbyggd funktion i följande appar:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): iOS, iPad
* Microsoft Intune [företagsportal](/mem/intune/apps/apps-company-portal-macos): MacOS

## <a name="features"></a>Funktioner

Microsoft Enterprise SSO-plugin-programmet för Apple-enheter ger följande fördelar:

- Den ger enkel inloggning för Azure AD-konton i alla program som har stöd för funktionen för enkel inloggning i Apple Enterprise.
- Den kan aktive ras av alla lösningar för hantering av mobila enheter (MDM).
- Den utökar SSO till program som ännu inte använder Microsoft Identity Platform-bibliotek.
- Den utökar SSO till program som använder OAuth 2, OpenID Connect och SAML.

## <a name="requirements"></a>Krav

Använda Microsoft Enterprise SSO-plugin-programmet för Apple-enheter:

- Enheten måste ha *stöd* för och ha en installerad app som har Microsoft Enterprise SSO-plugin-programmet för Apple-enheter:
  - iOS 13,0 och senare: [Microsoft Authenticator app](../user-help/user-help-auth-app-overview.md)
  - iPad-13,0 och senare: [Microsoft Authenticator app](../user-help/user-help-auth-app-overview.md)
  - macOS 10,15 och senare: [Intune-företagsportal app](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Enheten måste vara *registrerad i MDM*, till exempel via Microsoft Intune.
- Konfigurationen måste skickas *till enheten* för att aktivera SSO-plugin-programmet för företag. Apple kräver denna säkerhets begränsning.

### <a name="ios-requirements"></a>iOS-krav:
- iOS 13,0 eller senare måste vara installerat på enheten.
- Ett Microsoft-program som tillhandahåller Microsoft Enterprise SSO-plugin-programmet för Apple-enheter måste installeras på enheten. För en offentlig för hands version är de här programmen [Microsoft Authenticator-appen](/azure/active-directory/user-help/user-help-auth-app-overview).


### <a name="macos-requirements"></a>macOS-krav:
- macOS 10,15 eller högre måste vara installerat på enheten. 
- Ett Microsoft-program som tillhandahåller Microsoft Enterprise SSO-plugin-programmet för Apple-enheter måste installeras på enheten. För den allmänt tillgängliga för hands versionen inkluderar programmen [Intune-företagsportal-appen](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp).

## <a name="enable-the-sso-plug-in"></a>Aktivera SSO-plugin-programmet

Använd följande information för att aktivera SSO-plugin-programmet med hjälp av MDM.
### <a name="microsoft-intune-configuration"></a>Microsoft Intune konfiguration

Om du använder Microsoft Intune som MDM-tjänst kan du använda inbyggda konfigurations profil inställningar för att aktivera Microsoft Enterprise SSO-plugin-programmet:

1. Konfigurera inställningarna för [SSO-appens tillägg](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) för en konfigurations profil. 
1. Om profilen inte redan har tilldelats [tilldelar du profilen till en användare eller enhets grupp](/mem/intune/configuration/device-profile-assign).

Profil inställningarna som aktiverar SSO-plugin-programmet tillämpas automatiskt på gruppens enheter nästa gången varje enhet checkar in med Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Manuell konfiguration för andra MDM-tjänster

Om du inte använder Intune för MDM, använder du följande parametrar för att konfigurera Microsoft Enterprise SSO-plugin-programmet för Apple-enheter.

Inställningar för iOS:

- **Tilläggs-ID**: `com.microsoft.azureauthenticator.ssoextension`
- **Team-ID**: det här fältet behövs inte för iOS.

Inställningar för macOS:

- **Tilläggs-ID**: `com.microsoft.CompanyPortalMac.ssoextension`
- **Team-ID**: `UBF8T346G9`

Vanliga inställningar:

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
  
### <a name="more-configuration-options"></a>Fler konfigurations alternativ
Du kan lägga till fler konfigurations alternativ för att utöka SSO-funktioner till andra appar.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Aktivera SSO för appar som inte använder ett bibliotek med Microsoft Identity Platform

SSO-plugin-programmet gör det möjligt för alla program att delta i SSO även om det inte har utvecklats med hjälp av ett Microsoft-SDK som Microsoft-MSAL (Microsoft Authentication Library).

SSO-plugin-programmet installeras automatiskt av enheter som har:
* Hämtade Authenticator-appen på iOS eller iPad eller laddat ned Intune-företagsportal-appen på macOS.
* Registrerat sin enhet med din organisation. 

Din organisation använder antagligen Authenticator-appen för scenarier som multifaktorautentisering (MFA), lösenordsautentisering och villkorlig åtkomst. Genom att använda en MDM-Provider kan du aktivera SSO-plugin-programmet för dina program. Microsoft har gjort det enkelt att konfigurera plugin-programmet i Microsoft Endpoint Manager i Intune. En tillåten används för att konfigurera dessa program att använda SSO-plugin-programmet.

>[!IMPORTANT]
> Microsoft Enterprise SSO-plugin-programmet har endast stöd för appar som använder sig av inbyggda Apple Network Technologies eller webviews. Den har inte stöd för program som levererar sin egen implementering av nätverks lagret.  

Använd följande parametrar för att konfigurera Microsoft Enterprise SSO-plugin-programmet för appar som inte använder ett bibliotek med Microsoft Identity Platform.

Använd följande parametrar för att ange en lista över vissa appar:

- **Nyckel**: `AppAllowList`
- **Typ**: `String`
- **Värde**: kommaavgränsad lista över programpaket-ID: n för program som får delta i SSO.
- **Exempel**: `com.contoso.workapp, com.contoso.travelapp`

Använd följande parametrar för att ange en lista över prefix:
- **Nyckel**: `AppPrefixAllowList`
- **Typ**: `String`
- **Värde**: kommaavgränsad lista över programpaket-ID-prefix för de program som tillåts delta i SSO. Med den här parametern kan alla appar som börjar med ett visst prefix delta i SSO.
- **Exempel**: `com.contoso., com.fabrikam.`

[Samskickade appar](./application-consent-experience.md) som MDM-administratören kan delta i SSO kan tyst hämta en token för slutanvändaren. Lägg till endast betrodda program till tillåten. 

>[!NOTE]
> Du behöver inte lägga till program som använder MSAL eller ASWebAuthenticationSession i listan över appar som kan delta i SSO. Dessa program är aktiverade som standard. 

##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>Hitta program paket identifierare på iOS-enheter

Apple tillhandahåller inget enkelt sätt att hämta paket-ID: n från App Store. Det enklaste sättet att hämta paket-ID: n för de appar som du vill använda för SSO är att be din leverantör eller app-utvecklare. Om alternativet inte är tillgängligt kan du använda MDM-konfigurationen för att hitta paket-ID: n: 

1. Aktivera tillfälligt följande flagga i MDM-konfigurationen:

    - **Nyckel**: `admin_debug_mode_enabled`
    - **Typ**: `Integer`
    - **Värde**: 1 eller 0
1. När den här flaggan är påslagen loggar du in på iOS-appar på den enhet som du vill veta paket-ID: t för. 
1. I appen autentiserare väljer du **Hjälp**  >  **skicka loggar**  >  **Visa loggar**. 
1. Leta efter följande rad i logg filen: `[ADMIN MODE] SSO extension has captured following app bundle identifiers` . Den här raden ska avbilda alla programpaket-ID: n som är synliga för SSO-tillägget. 

Använd paket-ID: na för att konfigurera SSO för apparna. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Tillåt användare att logga in från okända program och Safari-webbläsaren

Som standard tillhandahåller Microsoft Enterprise SSO-plugin-programmet SSO endast för auktoriserade appar när en användare har loggat in från en app som använder ett Microsoft Identity Platform-bibliotek som MSAL eller Azure Active Directory Authentication Library (ADAL). Microsoft Enterprise SSO-plugin-programmet kan också hämta en delad autentiseringsuppgift när den anropas av en annan app som använder ett bibliotek för Microsoft Identity Platform under en ny token-hämtning.

När du aktiverar `browser_sso_interaction_enabled` flaggan kan appar som inte använder ett Microsoft Identity Platform-bibliotek utföra den första start filen och hämta en delad autentiseringsuppgift. I Safari-webbläsaren kan du också utföra inledande start och hämta en delad autentiseringsuppgift. 

Om Microsoft Enterprise SSO-plugin-programmet inte har någon delad autentiseringsuppgift, kommer det att försöka hämta en när en inloggning begärs från en Azure AD-URL i Safari-webbläsaren, ASWebAuthenticationSession, SafariViewController eller något annat tillåtet internt program. 

Använd följande parametrar för att aktivera flaggan: 

- **Nyckel**: `browser_sso_interaction_enabled`
- **Typ**: `Integer`
- **Värde**: 1 eller 0

macOS kräver den här inställningen så att den kan ge en konsekvent upplevelse över alla appar. iOS och iPad kräver inte den här inställningen eftersom de flesta appar använder Authenticator-appen för inloggning. Men vi rekommenderar att du aktiverar den här inställningen, eftersom om några av dina program inte använder Authenticator-appen på iOS eller iPad, kommer den här flaggan att förbättra upplevelsen. Inställningen är inaktive rad som standard.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>Inaktivera fråga efter MFA vid första start

Som standard uppmanas Microsoft Enterprise SSO-plugin-programmet alltid användaren för MFA vid den första start filen och när en delad autentiseringsuppgift hämtas. Användaren uppmanas att ange MFA även om det inte krävs för programmet som användaren har öppnat. Med det här beteendet kan delade autentiseringsuppgifter enkelt användas i alla andra program utan att användaren behöver uppmanas om MFA krävs senare. Eftersom användaren får färre uppfattande meddelanden är den här konfigurationen vanligt vis ett lämpligt beslut.

Aktivering `browser_sso_disable_mfa` inaktiverar MFA vid inledande start och när de delade autentiseringsuppgifterna hämtades. I det här fallet tillfrågas användaren bara när MFA krävs av ett program eller en resurs. 

Använd följande parametrar om du vill aktivera flaggan:

- **Nyckel**: `browser_sso_disable_mfa`
- **Typ**: `Integer`
- **Värde**: 1 eller 0

Vi rekommenderar att du behåller den här flaggan inaktive rad eftersom den minskar antalet gånger som användaren uppmanas att logga in. Om din organisation sällan använder MFA, kanske du vill aktivera flaggan. Men vi rekommenderar att du använder MFA oftare i stället. Av den anledningen är flaggan inaktive rad som standard.

#### <a name="disable-oauth-2-application-prompts"></a>Inaktivera OAuth 2-programprompter

Microsoft Enterprise SSO-plugin-programmet tillhandahåller SSO genom att bifoga delade autentiseringsuppgifter till nätverks förfrågningar som kommer från tillåtna program. Vissa OAuth 2-program kan dock felaktigt tvinga slutanvändarens prompt i protokoll skiktet. Om det här problemet visas ser du också att delade autentiseringsuppgifter ignoreras för dessa appar. Användaren uppmanas att logga in även om Microsoft Enterprise SSO-plugin-programmet fungerar för andra program.  

Genom `disable_explicit_app_prompt` att aktivera flaggan begränsar du möjligheten för både ursprungliga program och webb program att tvinga fram en slutanvändares fråga om protokoll skiktet och kringgå SSO. Använd följande parametrar om du vill aktivera flaggan:

- **Nyckel**: `disable_explicit_app_prompt`
- **Typ**: `Integer`
- **Värde**: 1 eller 0

Vi rekommenderar att du aktiverar den här flaggan för att få en konsekvent upplevelse för alla appar. Den är inaktive rad som standard. 

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>Aktivera SSO via cookies för ett specifik program

Några appar kan vara inkompatibla med SSO-tillägget. Mer specifikt kan appar som har avancerade nätverks inställningar uppleva oväntade problem när de är aktiverade för enkel inloggning. Du kan till exempel se ett fel som indikerar att nätverks förfrågan har avbrutits eller avbrutits. 

Om du har problem med att logga in med hjälp av metoden som beskrivs i avsnittet [program som inte använder MSAL](#applications-that-dont-use-msal) kan du prova med en alternativ konfiguration. Använd följande parametrar för att konfigurera plugin-programmet:

- **Nyckel**: `AppCookieSSOAllowList`
- **Typ**: `String`
- **Värde**: kommaavgränsad lista över programpaket-ID-prefix för de program som tillåts delta i SSO. Alla appar som börjar med de listade prefixen kommer att kunna delta i SSO.
- **Exempel**: `com.contoso.myapp1, com.fabrikam.myapp2`

Program som har Aktiver ATS för SSO med hjälp av den här installationen måste läggas till i både `AppCookieSSOAllowList` och `AppPrefixAllowList` .

Testa endast den här konfigurationen för program som har oväntade inloggnings fel. 

#### <a name="use-intune-for-simplified-configuration"></a>Använd Intune för förenklad konfiguration

Du kan använda Intune som MDM-tjänst för att under lätta konfigurationen av Microsoft Enterprise SSO-plugin-programmet. Du kan till exempel använda Intune för att aktivera plugin-programmet och lägga till gamla appar i en tillåten så att de får SSO. 

Mer information finns i dokumentation om [Intune-konfiguration](/intune/configuration/ios-device-features-settings).

## <a name="use-the-sso-plug-in-in-your-application"></a>Använd SSO-plugin-programmet i ditt program

[MSAL för Apple-enheter](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versioner 1.1.0 och senare har stöd för Microsoft Enterprise SSO-plugin-programmet för Apple-enheter. Det är det rekommenderade sättet att lägga till stöd för Microsoft Enterprise SSO-plugin-programmet. Det garanterar att du får alla funktioner i Microsoft Identity Platform.

Om du skapar ett program för Frontline-arbets scenarier, se [delad enhets läge för iOS-enheter](msal-ios-shared-devices.md) för att konfigurera information.

## <a name="understand-how-the-sso-plug-in-works"></a>Förstå hur SSO-plugin-programmet fungerar

Microsoft Enterprise SSO-plugin-programmet förlitar sig på [Apple Enterprise SSO Framework](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Identitets leverantörer som ansluter sig till ramverket kan fånga nätverks trafik för sina domäner och förbättra eller ändra hur dessa begär Anden hanteras. SSO-plugin-programmet kan till exempel Visa mer UIs för att samla in autentiseringsuppgifter för slutanvändare på ett säkert sätt, kräva MFA eller tyst tillhandahålla token till programmet.

Inbyggda program kan också implementera anpassade åtgärder och kommunicera direkt med SSO-plugin-programmet. Mer information finns i den här [2019 Worldwide Developer Conference-videon från Apple](https://developer.apple.com/videos/play/tech-talks/301/).

### <a name="applications-that-use-msal"></a>Program som använder MSAL

[MSAL för Apple-enheter](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versioner 1.1.0 och senare har stöd för Microsoft Enterprise SSO-plugin-programmet för Apple-enheter i eget läge för arbets-och skol konton. 

Du behöver ingen särskild konfiguration om du har följt [alla rekommenderade steg](./quickstart-v2-ios.md) och använt standardvärdet för [omdirigerings-URI](./redirect-uris-ios.md). På enheter som har SSO-plugin-programmet anropar MSAL automatiskt för alla interaktiva och tysta token-begäranden. Den anropar också för konto uppräkning och borttagning av konto. Eftersom MSAL implementerar ett internt SSO-plugin-protokoll som förlitar sig på anpassade åtgärder ger den här installationen den smidigaste inbyggda upplevelsen för slutanvändaren. 

Om SSO-plugin-programmet inte har Aktiver ATS av MDM men Microsoft Authenticator-appen finns på enheten, använder MSAL i stället Authenticator-appen för alla interaktiva token-begäranden. SSO-plugin-programmet delar SSO med Authenticator-appen.

### <a name="applications-that-dont-use-msal"></a>Program som inte använder MSAL

Program som inte använder ett bibliotek för Microsoft Identity Platform, t. ex. MSAL, kan fortfarande få SSO om en administratör lägger till dessa program i tillåten. 

Du behöver inte ändra koden i dessa appar så länge följande villkor är uppfyllda:

- Programmet använder Apple Frameworks för att köra nätverks begär Anden. Dessa ramverk är till exempel [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) och [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession). 
- Programmet använder standard protokoll för att kommunicera med Azure AD. Dessa protokoll inkluderar till exempel OAuth 2, SAML och WS-Federation.
- Programmet samlar inte in oformaterade användar namn och lösen ord i det inbyggda användar gränssnittet.

I det här fallet anges SSO när programmet skapar en nätverks förfrågan och öppnar en webbläsare för att logga in användaren i. När en användare omdirigeras till en inloggnings webb adress för Azure AD, verifierar SSO-plugin-programmet URL: en och söker efter SSO-autentiseringsuppgifter för den URL: en. Om autentiseringsuppgifterna hittas skickar SSO-plugin-programmet det till Azure AD, vilket ger programmet behörighet att slutföra nätverks förfrågan utan att be användaren ange autentiseringsuppgifter. Dessutom, om enheten är känd för Azure AD, så skickar SSO-plugin-programmet enhets certifikatet för att uppfylla den enhets villkorliga åtkomst kontrollen. 

För att stödja SSO för icke-MSAL appar implementerar SSO-plugin-programmet ett protokoll som liknar plugin-programmet för Windows-webbläsare, [vilket beskrivs i vad är en primär uppdateringstoken?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Jämfört med MSAL-baserade appar agerar SSO-plugin-programmet mer transparent för appar som inte är MSAL. Den integreras med den befintliga webbläsarens inloggnings upplevelse som appar tillhandahåller. 

Slutanvändaren ser den bekanta upplevelsen och behöver inte logga in igen i varje program. I stället för att visa den inbyggda konto väljaren, lägger till exempel SSO-plugin-programmet SSO-sessioner till den webbaserade konto väljaren. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [delad enhets läge för iOS-enheter](msal-ios-shared-devices.md).
