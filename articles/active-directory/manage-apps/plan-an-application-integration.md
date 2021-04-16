---
title: Kom igång med att integrera Azure Active Directory med appar
description: Den här artikeln är en komma igång-guide för Azure Active Directory (AD) med lokala program och molnprogram.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2021
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 37916e5e356e7c5ad6e685ac0838363fe2579496
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374989"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Kom igång Azure Active Directory integrering med program

Det här avsnittet sammanfattar processen för att integrera program med Azure Active Directory (AD). Vart och ett av avsnitten nedan innehåller en kort sammanfattning av ett mer detaljerat ämne så att du kan identifiera vilka delar av den här kom igång-guiden som är relevanta för dig.

Information om hur du laddar ned djupgående distributionsplaner finns i [Nästa steg.](#next-steps)

## <a name="take-inventory"></a>Gör en inventering
Innan du integrerar program med Azure AD är det viktigt att veta var du är och vart du vill gå.  Följande frågor är avsedda att hjälpa dig att tänka på ditt Azure AD-programintegreringsprojekt.

### <a name="application-inventory"></a>Applikationsinventering
* Var finns alla dina program? Vem äger dem?
* Vilken typ av autentisering kräver dina program?
* Vem behöver åtkomst till vilka program?
* Vill du distribuera ett nytt program?
  * Kommer du att skapa den internt och distribuera den på en Azure-beräkningsinstans?
  * Kommer du att använda en som är tillgänglig i Azure Application galleriet?

### <a name="user-and-group-inventory"></a>Användar- och gruppinventering
* Var finns dina användarkonton?
  * Lokalt Active Directory
  * Azure AD
  * I en separat programdatabas som du äger
  * I icke-sanktionerade program
  * Alla alternativ ovan
* Vilka behörigheter och rolltilldelningar har enskilda användare för närvarande? Behöver du granska deras åtkomst eller är du säker på att din användaråtkomst och rolltilldelningar är lämpliga nu?
* Har grupper redan upprättats i lokal Active Directory?
  * Hur organiseras dina grupper?
  * Vilka är gruppmedlemmarna?
  * Vilka behörigheter/rolltilldelningar har grupperna för närvarande?
* Behöver du rensa användar-/gruppdatabaser innan du integrerar?  (Det här är en viktig fråga. Skräp in, skräp ut.)

### <a name="access-management-inventory"></a>Åtkomsthanteringsinventering
* Hur hanterar du för närvarande användaråtkomst till program? Behöver det ändras?  Har du övervägt andra sätt att hantera åtkomst, till exempel [med Azure RBAC?](../../role-based-access-control/role-assignments-portal.md)
* Vem behöver åtkomst till vad?

Du kanske inte har svar på alla dessa frågor direkt, men det är okej.  Den här guiden kan hjälpa dig att besvara några av dessa frågor och fatta välgrundade beslut.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Hitta osanktionerade molnprogram med Cloud Discovery

Som nämnts ovan kan det finnas program som inte har hanterats av din organisation förrän nu.  Som en del av inventeringsprocessen är det möjligt att hitta osanktionerade molnprogram. Se [Konfigurera Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrera program med Azure AD
Följande artiklar beskriver de olika sätt som program integrerar med Azure AD och ger viss vägledning.

* [Avgöra vilken Active Directory som ska användas](../fundamentals/active-directory-whatis.md)
* [Använda program i Azure-programgalleriet](what-is-single-sign-on.md)
* [Integrera en lista med självstudier för SaaS-program](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Funktioner för appar som inte visas i Azure AD-galleriet

Du kan lägga till program som redan finns i din organisation eller program från tredje part från en leverantör som inte redan ingår i Azure AD-galleriet. Beroende på [ditt licensavtal](https://azure.microsoft.com/pricing/details/active-directory/)är följande funktioner tillgängliga:

- Självbetjäning av alla program som har [stöd för Security Assertion Markup Language (SAML) 2.0-identitetsproviders](https://wikipedia.org/wiki/SAML_2.0) (SP-initierade eller IdP-initierade)
- Självbetjäning av alla webbprogram som har en HTML-baserad inloggningssida med [lösenordsbaserad enkel inloggning](sso-options.md#password-based-sso)
- Självbetjäningsanslutning av program som använder [protokollet System for Cross-Domain Identity Management (SCIM) för användareablering](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Möjlighet att lägga till länkar till alla program i [Office 365-appstartaren](https://support.microsoft.com/office/meet-the-microsoft-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) [eller Mina appar](https://myapplications.microsoft.com/)

Om du behöver vägledning för utvecklare om hur du integrerar anpassade appar med Azure AD kan du gå till [Autentiseringsscenarier för Azure AD.](../develop/authentication-vs-authorization.md) När du utvecklar en app som använder ett modernt protokoll som [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) för att autentisera användare kan du registrera den med Microsoft Identity Platform med [hjälp av Appregistreringar-upplevelsen](../develop/quickstart-register-app.md) i Azure Portal.

### <a name="authentication-types"></a>Autentiseringstyper
Vart och ett av dina program kan ha olika autentiseringskrav. Med Azure AD kan signeringscertifikat användas med program som använder SAML 2.0, WS-Federation eller OpenID Connect-protokoll och enkel inloggning med lösenord. Mer information om typer av programautentisering finns i Hantera certifikat för [federerad](manage-certificates-for-federated-single-sign-on.md) enskild Sign-On i Azure Active Directory [och Lösenordsbaserad enkel inloggning på](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Aktivera enkel inloggning med Azure AD App Proxy
Med Microsoft Azure AD Programproxy kan du ge åtkomst till program som finns i ditt privata nätverk på ett säkert sätt, var som helst och på valfri enhet. När du har installerat en programproxyanslutning i din miljö kan den enkelt konfigureras med Azure AD.

### <a name="integrating-custom-applications"></a>Integrera anpassade program
Om du vill lägga till ditt anpassade program i Azure Application-galleriet kan du gå till Publicera din [app i Azure AD-appgalleriet](../develop/v2-howto-app-gallery-listing.md).

## <a name="managing-access-to-applications"></a>Hantera åtkomst till program
I följande artiklar beskrivs hur du kan hantera åtkomst till program när de har integrerats med Azure AD med hjälp av Azure AD-anslutningsprogram och Azure AD.

* [Hantera åtkomst till appar med Azure AD](what-is-access-management.md)
* [Automatisera med Azure AD-anslutningsappar](../app-provisioning/user-provisioning.md)
* [Tilldela användare till ett program](./assign-user-or-group-access-portal.md)
* [Tilldela grupper till ett program](./assign-user-or-group-access-portal.md)
* [Dela konton](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Nästa steg
Om du vill ha detaljerad information kan du ladda ned Azure Active Directory distributionsplaner från [GitHub.](../fundamentals/active-directory-deployment-plans.md) För galleriprogram kan du ladda ned distributionsplaner för enkel inloggning, villkorsstyrd åtkomst och användareablering via [Azure Portal](https://portal.azure.com).

Så här laddar du ned en distributionsplan från Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Företagsprogram**  |  **Välj en appdistributionsplan.**  |  
