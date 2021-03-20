---
title: Bygg återhämtning genom att använda enhets tillstånd i Azure Active Directory
description: En guide för arkitekter och IT-administratörer för att skapa återhämtning med enhets tillstånd
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d476be7a417cfc31cca76d3409074aaaa281a56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724618"
---
# <a name="build-resilience-with-device-states"></a>Bygg återhämtning med enhets tillstånd

Genom att aktivera [enhets tillstånd](../devices/overview.md) med Azure AD kan administratörer skapa [principer för villkorlig åtkomst](../conditional-access/overview.md) som styr åtkomsten till program baserat på enhetens tillstånd. Den extra fördelen med enheter är att den uppfyller starka autentiseringskrav för åtkomst till resurser, vilket minskar ytterligare MFA-autentiseringsbegäranden och förbättrar återhämtnings förmågan. 

I följande flödes diagram presenteras olika sätt att publicera enheter i Azure AD som aktiverar enhets tillstånd. Du kan använda mer än en i din organisation.

![flödes diagram för att välja enhets tillstånd](./media/resilience-with-device-states/admin-resilience-devices.png)

När du använder [enhets tillstånd](../devices/overview.md)kommer användare i de flesta fall att uppleva enkel inloggning till resurser via en [primär uppdateringstoken](../devices/concept-primary-refresh-token.md) (PRT). PRT innehåller anspråk om användaren och enheten och kan användas för att hämta autentiseringstoken för att få åtkomst till program från enheten. PRT är giltigt i 14 dagar och förnyas kontinuerligt så länge användaren använder enheten aktivt, vilket ger användarna en elastisk upplevelse. En PRT kan också få ett Multi-Factor Authentication-anspråk på flera sätt. Mer information finns i [när en PRT får ett MFA-anspråk](../devices/concept-primary-refresh-token.md).

## <a name="how-do-device-states-help"></a>Hur hjälper enhets tillstånd?

När en PRT används för att begära åtkomst till ett program, är dess enhet, session och MFA-anspråk betrodda av Azure AD. När administratörer skapar principer som kräver en enhets kontroll eller Multi-Factor Authentication-kontroll, kan princip kravet uppfyllas genom enhetens tillstånd utan att Multi-Factor Authentication används. Användarna kommer inte att se ytterligare Multi-Factor Authentication-prompter på samma enhet. Detta ökar återhämtningen till ett avbrott i Azure MFA-tjänsten, eller dess beroenden som lokala telekommunikations leverantörer.

## <a name="how-do-i-implement-device-states"></a>Hur gör jag för att implementera enhets tillstånd?

* Aktivera [hybrid Azure AD](../devices/hybrid-azuread-join-plan.md) - [anslutning och Azure AD Join](../devices/azureadjoin-plan.md) för företagsägda Windows-enheter och Kräv att de är anslutna om möjligt. Om detta inte är möjligt måste de registreras.

  Om det finns äldre versioner av Windows i din organisation uppgraderar du enheterna till Windows 10.

* Standardisera användarens webb läsar åtkomst för att använda antingen [Microsoft Edge](/deployedge/microsoft-edge-security-identity) eller Google Chrome med [stödda](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) [tillägg](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) som har aktiverat sömlös SSO till webb program med hjälp av PRT.

* För personliga eller företagsägda iOS-och Android-enheter distribuerar du [Microsoft Authenticator-appen](../user-help/user-help-auth-app-overview.md). Förutom Multi-Factor Authentication och lösen ords lös inloggnings funktioner, kommer Microsoft Authenticator-appen att aktivera enkel inloggning mellan interna program via [Broker-autentisering](../develop/msal-android-single-sign-on.md) med färre autentiserings-prompter för slutanvändare.

* För personliga eller företagsägda iOS-och Android-enheter använder du [hantering av mobil program](/mem/intune/apps/app-management) för att på ett säkert sätt få åtkomst till företags resurser med färre autentiseringsbegäranden. 

* [Använd Microsoft Enterprise SSO-plugin-programmet för Apple-enheter (för hands version)](../develop/apple-sso-plugin.md). Detta registrerar enheten och ger enkel inloggning mellan webbläsare och interna Azure AD-program. 

## <a name="next-steps"></a>Nästa steg
Återhämtnings resurser för administratörer och arkitekter
 
* [Bygg återhämtning med hantering av autentiseringsuppgifter](resilience-in-credentials.md)

* [Bygg återhämtning med hjälp av den kontinuerliga åtkomst utvärderingen (CAE)](resilience-with-continuous-access-evaluation.md)

* [Bygg återhämtning vid extern användarautentisering](resilience-b2b-authentication.md)

* [Bygg återhämtning i din hybrid autentisering](resilience-in-hybrid.md)

* [Bygg återhämtning i program åtkomst med Application Proxy](resilience-on-premises-access.md)


Återhämtnings resurser för utvecklare

* [Bygg IAM-återhämtning i dina program](resilience-app-development-overview.md)

* [Bygg återhämtning i dina CIAM-system](resilience-b2c.md)