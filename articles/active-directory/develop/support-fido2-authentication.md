---
title: Stöd för lösenordsskyddad autentisering med FIDO2-nycklar i appar som du utvecklar | Azure
titleSuffix: Microsoft identity platform
description: Den här distributions guiden förklarar hur du stöder lösen ords lös autentisering med FIDO2-säkerhetsnycklar i de program som du utvecklar
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: f63d7aed75b14f5f008a639d667d8806b233b9fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174606"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>Stöd för lösenordsskyddad autentisering med FIDO2-nycklar i appar som du utvecklar

Dessa konfigurationer och bästa praxis hjälper dig att undvika vanliga scenarier som blockerar [FIDO2-lösenordsautentisering](../../active-directory/authentication/concept-authentication-passwordless.md) som är tillgängliga för användare av dina program.

## <a name="general-best-practices"></a>Allmän bästa praxis

### <a name="domain-hints"></a>Domän tips

Använd inte ett domän tips för att kringgå [Start sfär identifiering](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md). Den här funktionen är avsedd att göra inloggningar mer effektiviserade, men federerade identitets leverantören kanske inte har stöd för lösen ords lös autentisering.

### <a name="requiring-specific-credentials"></a>Kräver vissa autentiseringsuppgifter

Om du använder SAML ska du inte ange att ett lösen ord krävs [med RequestedAuthnContext-elementet](single-sign-on-saml-protocol.md#requestauthncontext).

RequestedAuthnContext-elementet är valfritt, så för att lösa det kan du ta bort det från dina SAML-autentiseringsbegäranden. Det här är en allmän metod, som att använda det här elementet även för att förhindra andra autentiseringsalternativ som Multi-Factor Authentication fungerar korrekt.

### <a name="using-the-most-recently-used-authentication-method"></a>Använd autentiseringsmetoden som användes senast

Den inloggnings metod som senast användes av en användare visas först. Detta kan orsaka förvirring när användare tror att de måste använda det första alternativet som visas. De kan dock välja ett annat alternativ genom att välja andra sätt att logga in på som visas nedan.

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="Bild av upplevelsen för användarautentisering som markerar knappen som gör att användaren kan ändra autentiseringsmetoden.":::

## <a name="platform-specific-best-practices"></a>Plattforms säkra metod tips

### <a name="desktop"></a>Skrivbord

De rekommenderade alternativen för att implementera autentisering är i ordning:

- .NET-dator program som använder Microsoft Authentication Library (MSAL) bör använda Windows Authentication Manager (WAM). Den här integrationen och dess fördelar [dokumenteras på GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam).
- Använd [WebView2](/microsoft-edge/webview2/) för att stödja FIDO2 i en inbäddad webbläsare.
- Använd system webbläsare. MSAL-biblioteken för Station ära plattformar använder den här metoden som standard. Du kan se vår sida om FIDO2 browser-kompatibilitet för att säkerställa att den webbläsare du använder stöder FIDO2-autentisering.

### <a name="mobile"></a>Mobilt

Från och med februari 2021 stöds inte FIDO2 för närvarande för inbyggda iOS-eller Android-appar, men det är under utveckling.

För att förbereda program för dess tillgänglighet och som en allmän metod bör iOS-och Android-program använda MSAL med standard konfigurationen av med hjälp av system webbläsare.

Om du inte använder MSAL bör du fortfarande använda system webbläsare för autentisering. Funktioner som enkel inloggning och villkorlig åtkomst förlitar sig på en delad webb yta som tillhandahålls av system webbläsare. Det innebär att du använder [Chrome-anpassade flikar](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android) eller [autentisering av en användare via en webb tjänst | Apple Developer-dokumentation](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) (iOS).

### <a name="web-and-single-page-apps"></a>Webb-och appar på en sida

Tillgängligheten för FIDO2-autentisering av lösen ord för program som körs i en webbläsare beror på kombinationen av webbläsare och plattform. Du kan se vår [FIDO2-kompatibilitet](../authentication/fido2-compatibility.md) för att kontrol lera om den kombination som användarna kommer att stöta på stöds.

## <a name="next-steps"></a>Nästa steg

[Lösen ords verifierings alternativ för Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)