---
title: Stöd för enkel inloggning och skydds principer för appar i mobilappar som du utvecklar | Azure
titleSuffix: Microsoft identity platform
description: Förklaring och översikt över att skapa mobila program som stöder enkel inloggning och appars skydds principer med hjälp av Microsoft Identity Platform och integrering med Azure Active Directory.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 4f0588667df6acb11a43e8c3469c67f65ed3cdd9
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165186"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>Stöd för enkel inloggning och skydds principer för appar i mobilappar som du utvecklar

Enkel inloggning (SSO) är ett viktigt utbud av Microsoft Identity Platform och Azure Active Directory och ger enkel och säker inloggning för användare av din app. Dessutom möjliggör app Protection-principer (APP) stöd för de nyckel säkerhets principer som skyddar användarens data. Tillsammans möjliggör de här funktionerna säkra användar inloggningar och hantering av appens data.

> [!VIDEO https://www.youtube.com/embed/JpeMeTjQJ04]

I den här artikeln förklaras varför SSO och APP är viktiga och ger vägledning på hög nivå för att skapa mobila program som stöder dessa funktioner. Detta gäller för både telefon-och surfplattor. Om du är IT-administratör som vill distribuera SSO över din organisations Azure Active Directory klient kan du läsa vår [vägledning för att planera en distribution med enkel inloggning](../manage-apps/plan-sso-deployment.md)

## <a name="about-single-sign-on-and-app-protection-policies"></a>Om enkel inloggning och skydds principer för appar

[Enkel inloggning (SSO)](../manage-apps/plan-sso-deployment.md) gör att en användare kan logga in en gång och få åtkomst till andra program utan att ange autentiseringsuppgifter igen. Detta gör det enklare att komma åt appar och eliminera användarnas behov av att komma ihåg långa listor över användar namn och lösen ord. Att implementera det i din app gör det enklare att komma åt och använda appen.

Dessutom är det möjligt att aktivera enkel inloggning i din app för att låsa upp nya autentiseringsmekanismer som medföljer modern autentisering, t. ex. [lösen ords](../authentication/concept-authentication-passwordless.md)skydd. Användar namn och lösen ord är ett av de mest populära angrepps sätten mot program, och att aktivera SSO gör att du kan minska risken genom att framtvinga villkorlig åtkomst eller lösen ords lösa inloggningar som lägger till ytterligare säkerhet eller förlitar sig på säkrare autentiseringsmekanismer. Slutligen aktiverar enkel inloggning även [enkel utloggning](v2-protocols-oidc.md#single-sign-out). Detta är användbart i situationer som arbets program som ska användas på delade enheter.

[App Protection-principer (app)](/mem/intune/apps/app-protection-policy) säkerställer att organisationens data förblir skyddade och inneslutna. De gör det möjligt för företag att hantera och skydda sina data i en app och tillåta kontroll över vem som har åtkomst till appen och dess data. Genom att implementera skydds principer för appar kan din app ansluta användare till resurser som skyddas av principer för villkorlig åtkomst och överföra data på ett säkert sätt till och från andra skyddade appar. Scenarier som är olåsta av appars skydds principer är att kräva en PIN-kod för att öppna en app, styra delningen av data mellan appar och förhindra att företags AppData sparas till personliga lagrings platser.

## <a name="implementing-single-sign-on"></a>Implementera enkel inloggning

Vi rekommenderar följande för att göra det möjligt för din app att dra nytta av enkel inloggning.

### <a name="use-the-microsoft-authentication-library-msal"></a>Använda Microsoft Authentication Library (MSAL)

Det bästa valet för att implementera enkel inloggning i ditt program är att använda [Microsoft Authentication Library (MSAL)](msal-overview.md). Genom att använda MSAL kan du lägga till autentisering i din app med minimala kod-och API-anrop, få alla funktioner i [Microsofts identitets plattform](./index.yml)och låta Microsoft hantera underhållet av en säker autentiserings lösning. Som standard lägger MSAL till SSO-stöd för ditt program. Dessutom är användningen av MSAL ett krav om du också planerar att implementera skydds principer för appar.

> [!NOTE]
> Det går att konfigurera MSAL till att använda en inbäddad webbvy. På så sätt förhindras enkel inloggning. Använd standard beteendet (det vill säga system webbläsare) för att säkerställa att SSO fungerar.

Om du använder [ADAL-biblioteket](../azuread-dev/active-directory-authentication-libraries.md) i ditt program, rekommenderar vi starkt att du [migrerar det till MSAL](msal-migration.md), eftersom [ADAL är föråldrad](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363).

För iOS-program har vi en [snabb start](quickstart-v2-ios.md) som visar hur du konfigurerar inloggningar med hjälp av MSAL, samt [vägledning för att konfigurera MSAL för olika SSO-scenarier](single-sign-on-macos-ios.md).

För Android-program har vi en [snabb start](quickstart-v2-android.md) som visar hur du konfigurerar inloggningar med hjälp av MSAL och vägledning för [hur du aktiverar enkel inloggning mellan appar på Android med MSAL](msal-android-single-sign-on.md).

### <a name="use-the-system-web-browser"></a>Använda system webbläsare

En webbläsare krävs för interaktiv autentisering. För mobilappar som använder andra moderna autentiseringspaket än MSAL (det vill säga andra OpenID Connect-eller SAML-bibliotek), eller om du implementerar din egen autentiseringsnyckel, bör du använda system läsaren som din autentiseringsnivå för att aktivera SSO.

Google har vägledning för att göra detta i Android-program: [anpassade flikar – Google Chrome](https://developer.chrome.com/multidevice/android/customtabs).

Apple har vägledning för att göra detta i iOS-program: [autentisera en användare via en webb tjänst | Apple Developer-dokumentation](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service).

> [!TIP]
> [SSO-plugin-programmet för Apple-enheter](apple-sso-plugin.md) tillåter SSO för iOS-appar som använder inbäddade webbvyer på hanterade enheter med Intune. Vi rekommenderar MSAL och system webbläsare som det bästa alternativet för att utveckla appar som möjliggör enkel inloggning för alla användare, men detta tillåter enkel inloggning i vissa situationer där det annars inte är möjligt.

## <a name="enable-app-protection-policies"></a>Aktivera skydds principer för appar

Om du vill aktivera skydds principer för appar använder du [Microsoft Authentication Library (MSAL)](msal-overview.md). MSAL är Microsofts identitets plattforms bibliotek för autentisering och auktorisering och Intune SDK har utvecklats för att fungera med det.

Dessutom måste du använda en Service Broker-app för autentisering. Service Broker kräver att appen tillhandahåller program-och enhets information för att säkerställa appens kompatibilitet. iOS-användare kommer att använda [Microsoft Authenticator app](../user-help/user-help-auth-app-sign-in.md) och Android-användare använder antingen Microsoft Authenticator-appen eller [företagsportal-appen](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) för [Brokered Authentication](./msal-android-single-sign-on.md). Som standard använder MSAL en Service Broker som första valet för att uppfylla en autentiseringsbegäran, så att när du använder Service Broker för autentisering aktive ras den automatiskt för appen när du använder MSAL out-of-box.

Slutligen [lägger du till INTUNE SDK](/mem/intune/developer/app-sdk-get-started) till din app för att aktivera skydds principer för appar. SDK: n för den mesta delen följer en avlyssnings modell och tillämpar automatiskt skydds principer för appar för att avgöra om åtgärder som appen tar emot är tillåtna eller inte. Det finns också API: er som du kan anropa manuellt för att tala om för appen om det finns några begränsningar för vissa åtgärder.

## <a name="additional-resources"></a>Ytterligare resurser

- [Planera en Azure Active Directory distribution av enkel inloggning](../manage-apps/plan-sso-deployment.md)
- [Gör så här: Konfigurera SSO på macOS och iOS](single-sign-on-macos-ios.md)
- [Microsoft Enterprise SSO-plugin-program för Apple-enheter (för hands version)](apple-sso-plugin.md)
- [Brokered Authentication i Android](./msal-android-single-sign-on.md)
- [Auktoriserings agenter och hur du aktiverar dem](./msal-android-single-sign-on.md)
- [Kom igång med Microsoft Intune App SDK](/mem/intune/developer/app-sdk-get-started)
- [Konfigurera inställningar för Intune App SDK](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [Skyddade appar i Microsoft Intune](/mem/intune/apps/apps-supported-intune-apps)
