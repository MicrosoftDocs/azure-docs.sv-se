---
title: Frontline Worker Management – Azure Active Directory
description: Lär dig mer om Frontline Work Management-funktioner som tillhandahålls via min personal Portal.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: cchiedo
author: Chrispine-Chiedo
manager: CelesteDG
ms.reviewer: stevebal
ms.openlocfilehash: 32cee4ca0558166c44ff83c5ce9d61360e79e535
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969369"
---
# <a name="frontline-worker-management"></a>Frontline Worker Management

Frontline Worker-konto för över 80 procent av den globala arbets styrkan. Men på grund av hög skalning, snabb omsättning och fragmenterade processer, saknar Frontline-arbetsbelastningar verktyg för att göra deras krävande jobb lite enklare. Frontline Worker Management ger digital omvandling till hela Frontline-arbetskraften. Arbets Personalen kan omfatta chefer, Frontline arbetare, åtgärder och IT.

Frontline Worker Management ger Frontline arbets styrka genom att göra följande aktiviteter lättare att utföra:
- Effektivisera vanliga IT-uppgifter med min personal
- Enkel onboarding av Frontline-arbetskrafter via förenklad autentisering
- Sömlös etablering av delade enheter och säker utloggning av Frontline-arbetskrafter

## <a name="delegated-user-management-through-my-staff"></a>Delegerad användar hantering via min personal

Azure Active Directory (Azure AD) ger möjlighet att delegera användar hantering till Frontline-chefer via [min personal Portal](../roles/my-staff-configure.md), vilket hjälper till att spara värdefull tid och minska riskerna. Genom att aktivera förenklade återställningar av lösen ord och telefon hantering direkt från butiken eller fabriks golvet kan chefer bevilja åtkomst till anställda utan att vidarebefordra begäran via supportavdelningen, IT eller åtgärder.

![Delegerad användar hantering i min personal Portal](media/concept-fundamentals-frontline-worker/delegated-user-management.png)

## <a name="accelerated-onboarding-with-simplified-authentication"></a>Snabbare onboarding med förenklad autentisering

Min personal gör det också möjligt för Frontline-ansvariga att registrera sina team medlemmars telefonnummer för [SMS-inloggning](../authentication/howto-authentication-sms-signin.md). I många lodräta bevarar Frontline Worker en lokal kombination av användar namn och lösen ord, en lösning som ofta är besvärlig, kostsam och fel känslig. När du aktiverar autentisering med SMS-inloggning kan Frontline-anställda logga in med [enkel inloggning (SSO)](../manage-apps/what-is-single-sign-on.md) för Microsoft Teams och andra appar som använder sig av deras telefonnummer och eng ång slö sen ord (eng ång slö sen ord) som skickas via SMS. Detta gör inloggning för Frontline-arbetskrafter enkla och säkra och ger snabb åtkomst till de appar som de behöver mest.

![SMS-inloggning](media/concept-fundamentals-frontline-worker/sms-signin.png)

Frontline-ansvariga kan också använda MHS-program (Managed Home Screen) för att tillåta att arbetare får åtkomst till en specifik uppsättning program på sina Intune-registrerade Android-enheter. De dedikerade enheterna har registrerats med [Azure AD Shared Device-läge](../develop/msal-shared-devices.md). När det konfigureras i hel skärms läge för flera appar i Microsoft Endpoint Manager-konsolen (MEM) startas MHS automatiskt som standard start skärmen på enheten och visas för slutanvändaren som den *enda* start skärmen. Mer information finns i så här [konfigurerar du Microsoft Managed Start Screen-appen för Android Enterprise](/mem/intune/apps/app-configuration-managed-home-screen-app).

## <a name="secure-sign-out-of-frontline-workers-from-shared-devices"></a>Säker utloggning av Frontline-arbetskrafter från delade enheter

Många företag använder delade enheter så att Frontline arbetare kan utföra lager hanterings-och kassa transaktioner, utan IT-bördan för etablering och spårning av enskilda enheter. Med delad enhets utloggning är det enkelt för en Frontline arbetare att på ett säkert sätt logga ut från alla appar på en delad enhet innan de återgår till en hubb eller skickar den till en arbets grupp på nästa SKIFT. Microsoft Teams är en av de appar som för närvarande stöds på delade enheter och det gör att Frontline-arbetare kan visa uppgifter som har tilldelats dem. När en arbets tagare loggar ut från en delad enhet rensar Intune och Azure AD alla företags data så att enheten säkert kan skickas vidare till nästa koppling. Du kan välja att integrera den här funktionen i alla branschspecifika [iOS](../develop/msal-ios-shared-devices.md) -och [Android](../develop/msal-android-shared-devices.md) -appar med hjälp av [Microsoft Authentication Library](../develop/msal-overview.md).

![Utloggning av delad enhet](media/concept-fundamentals-frontline-worker/shared-device-signout.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om delegerad användar hantering finns i [dokumentationen för min personal användare](../user-help/my-staff-team-manager.md).
- För inkommande användar etablering från SAP SuccessFactors, se självstudien om hur [du konfigurerar SAP-SuccessFactors för att Active Directory användar etablering](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
- Information om ingående användar etablering från arbets dagar finns i självstudien om hur du [konfigurerar arbets dagar för automatisk användar etablering](../saas-apps/workday-inbound-tutorial.md).
