---
title: Kom igång med att integrera Azure AD med appar
description: Den här artikeln är en komma igång-guide för att integrera Azure Active Directory (AD) med lokala program och moln program.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 8b321acb00e6e9b4b6cca117afba8bf0c9432719
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258474"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Att integrera Azure Active Directory med program komma igång-guide

I det här avsnittet sammanfattas processen för att integrera program med Azure Active Directory (AD). Vart och ett av avsnitten nedan innehåller en kort sammanfattning av ett mer detaljerat avsnitt, så att du kan identifiera vilka delar av den här komma igång-guiden som är relevanta för dig.

Information om hur du hämtar djupgående distributions planer finns i [Nästa steg](#next-steps).

## <a name="take-inventory"></a>Ta inventering
Innan du integrerar program med Azure AD är det viktigt att veta var du är och var du vill gå.  Följande frågor är avsedda att hjälpa dig att tänka på ditt Azure AD Application Integration-projekt.

### <a name="application-inventory"></a>Applikationsinventering
* Var finns alla dina program? Vem äger dem?
* Vilken typ av autentisering kräver dina program?
* Vem behöver åtkomst till vilka program?
* Vill du distribuera ett nytt program?
  * Kommer du att bygga den internt och distribuera den på en Azure Compute-instans?
  * Kommer du att använda en som är tillgänglig i Azure Application galleriet?

### <a name="user-and-group-inventory"></a>Användar-och grupp inventering
* Var finns dina användar konton?
  * Lokalt Active Directory
  * Azure AD
  * I en separat program databas som du äger
  * I program som inte är sanktionerade
  * Alla alternativ ovan
* Vilka behörigheter och roll tilldelningar har enskilda användare för närvarande? Behöver du granska åtkomsten eller är du säker på att användarnas åtkomst-och roll tilldelningar är lämpliga nu?
* Är grupper redan etablerade i din lokala Active Directory?
  * Hur organiseras dina grupper?
  * Vilka är grupp medlemmarna?
  * Vilka behörigheter/roll tilldelningar har grupperna för närvarande?
* Behöver du rensa användare/grupp-databaser innan du integrerar?  (Det här är en ganska viktig fråga. Skräp in, skräp ut.)

### <a name="access-management-inventory"></a>Åtkomst hanterings inventering
* Hur hanterar du användar åtkomst för program? Vill du ändra?  Har du funderat på andra sätt att hantera åtkomst, t. ex. med [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) till exempel?
* Vem behöver åtkomst till vad?

Du kanske inte har svar på alla dessa frågor fram, men det är OK.  Den här guiden hjälper dig att besvara några av dessa frågor och fatta några välgrundade beslut.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Hitta ej sanktionerade moln program med Cloud Discovery

Som nämnts ovan kan det finnas program som inte har hanterats av din organisation förrän nu.  Som en del av inventerings processen är det möjligt att hitta ej sanktionerade moln program. Se [konfigurera Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Integrera program med Azure AD
I följande artiklar diskuteras de olika sätten att integrera med Azure AD och ge lite vägledning.

* [Bestämma vilken Active Directory som ska användas](../fundamentals/active-directory-whatis.md)
* [Använda program i Azures program Galleri](what-is-single-sign-on.md)
* [Integrerings lista för integrering av SaaS-program](../saas-apps/tutorial-list.md)

### <a name="authentication-types"></a>Autentiseringstyper
Varje program kan ha olika autentiseringskrav. Med Azure AD kan du använda signerings certifikat med program som använder SAML 2,0, WS-Federation eller OpenID Connect-protokoll samt lösen ords enkel inloggning. Mer information om programautentiserings typer som används med Azure AD finns i [Hantera certifikat för federerade enkla Sign-On i Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) och [lösen ords baserad enkel inloggning](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Aktivera SSO med Azure AD App proxy
Med Microsoft Azure AD Application Proxy kan du ge åtkomst till program som finns i ditt privata nätverk på ett säkert sätt, från valfri plats och på valfri enhet. När du har installerat en Application Proxy-koppling i din miljö kan den enkelt konfigureras med Azure AD.

### <a name="integrating-custom-applications"></a>Integrera anpassade program
Om du vill lägga till ett anpassat program i Azure Application galleriet, se [Publicera appen i Azure AD App-galleriet](../develop/v2-howto-app-gallery-listing.md).

## <a name="managing-access-to-applications"></a>Hantera åtkomst till program
I följande artiklar beskrivs hur du kan hantera åtkomst till program när de har integrerats med Azure AD med hjälp av Azure AD-anslutningar och Azure AD.

* [Hantera åtkomst till appar med hjälp av Azure AD](what-is-access-management.md)
* [Automatisera med Azure AD-anslutningar](../app-provisioning/user-provisioning.md)
* [Tilldela användare till ett program](./assign-user-or-group-access-portal.md)
* [Tilldela grupper till ett program](./assign-user-or-group-access-portal.md)
* [Dela konton](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Nästa steg
För detaljerad information kan du hämta Azure Active Directory distributions planer från [GitHub](../fundamentals/active-directory-deployment-plans.md). För Galleri program kan du hämta distributions planer för enkel inloggning, villkorlig åtkomst och användar etablering via [Azure Portal](https://portal.azure.com). 

Så här hämtar du en distributions plan från Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **företags program**  |  **Välj en app**  |  **Deployment-plan**.

Ge feedback om distributions planer genom att ta [undersökningen om distributions planen](https://aka.ms/DeploymentPlanFeedback).