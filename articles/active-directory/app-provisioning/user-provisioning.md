---
title: Vad är automatiserad SaaS app User-etablering i Azure AD
description: En introduktion till hur du kan använda Azure AD för att automatiskt etablera, avetablera och kontinuerligt uppdatera användar konton över flera SaaS-program från tredje part.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: overview
ms.workload: identity
ms.date: 01/11/2021
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: cb2c168786ee4cb14f93544d4cd9632b86ba5156
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255533"
---
# <a name="what-is-automated-saas-app-user-provisioning-in-azure-ad"></a>Vad är automatiserad SaaS app User-etablering i Azure AD?

I Azure Active Directory (Azure AD), avser termen **app-etablering** att automatiskt skapa användar identiteter och roller i molnet ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) som användarna behöver åtkomst till. Förutom att skapa användar identiteter omfattar automatisk etablering underhåll och borttagning av användar identiteter som status eller roller ändras. Vanliga scenarier innefattar etablering av en Azure AD-användare i program som [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md), [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md), [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)och mycket annat.

Om du vill veta mer om SCIM och delta i Tech community-konversationen går du till [etablering med scim Tech community](https://aka.ms/scimoverview).

![Översikts diagram för etablering](./media/user-provisioning/provisioning-overview.png)

Med den här funktionen kan du:

- **Automatisera etablering**: skapa automatiskt nya konton i rätt system för nya personer när de ansluter till ditt team eller din organisation.
- **Automatisera avetablering:** Inaktivera konton automatiskt i rätt system när personer lämnar teamet eller organisationen.
- **Synkronisera data mellan system:** Se till att identiteterna i dina appar och system hålls uppdaterade baserat på ändringar i katalogen eller personal systemet.
- **Etablerings grupper:** Etablera grupper för program som stöder dem.
- **Styr åtkomst:** Övervaka och granska som har etablerats till dina program.
- **Distribuera sömlöst i bruna fält scenarier:** Matcha befintliga identiteter mellan system och möjliggör enkel integrering, även när användare redan finns i mål systemet.
- **Använd omfattande anpassning:** Dra nytta av anpassningsbara mappningar av attribut som definierar vilka användar data som ska flöda från käll systemet till mål systemet.
- **Hämta aviseringar för kritiska händelser:** Etablerings tjänsten tillhandahåller varningar för kritiska händelser och möjliggör Log Analytics-integrering där du kan definiera anpassade aviseringar för dina affärs behov.

## <a name="benefits-of-automatic-provisioning"></a>Fördelar med automatisk etablering

Antalet program som används i moderna organisationer fortsätter att växa, men IT-administratörerna kan använda åtkomst hantering i stor skala. Standarder, till exempel SAML (Security Assertion Markup Language) eller Open ID Connect (OIDC) gör det möjligt för administratörer att snabbt konfigurera enkel inloggning (SSO), men åtkomst kräver också att användare tillhandahålls till appen. För många administratörer innebär etableringen att manuellt skapa varje användar konto eller att ladda upp CSV-filer varje vecka, men dessa processer är tids krävande, kostsamma och fel känsliga. Lösningar som till exempel SAML just-in-Time (JIT) har vidtagits för att automatisera etableringen, men företag behöver också en lösning för att avetablera användare när de lämnar organisationen eller inte längre behöver åtkomst till vissa appar baserat på roll ändringar.

Några vanliga orsaker till att använda automatisk etablering är:

- Maximera effektiviteten och noggrannheten vid etablering av processer.
- Spara pengar på kostnader som är kopplade till värd tjänster och underhåll av anpassade och utvecklade etablerings lösningar och skript.
- Att skydda din organisation genom att genast ta bort användarnas identiteter från viktiga SaaS-appar när de lämnar organisationen.
- Du kan enkelt importera ett stort antal användare till ett visst SaaS-program eller system.
- Med en enda uppsättning principer kan du bestämma vem som är etablerad och vem som kan logga in på en app.

Användar etablering i Azure AD kan hjälpa dig att lösa dessa utmaningar. Om du vill veta mer om hur kunder har använt Azure AD-etableringen kan du läsa [fallstudien för Asos](https://aka.ms/asoscasestudy). Videon nedan innehåller en översikt över användar etablering i Azure AD:

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Vilka program och system kan jag använda med automatisk användar etablering i Azure AD?

Azure AD innehåller Förintegrerad support för många populära SaaS-appar och personal system, och allmänt stöd för appar som implementerar vissa delar av [SCIM 2,0-standarden](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010).

* **Förintegrerade program (Galleri SaaS-appar)**. Du kan hitta alla program för vilka Azure AD har stöd för en förintegrerad etablerings koppling i [listan över program självstudier för användar etablering](../saas-apps/tutorial-list.md). De förintegrerade programmen som listas i galleriet använder vanligt vis SCIM 2,0-baserade användar hanterings-API: er för etablering. 

   ![Salesforce-logotyp](./media/user-provisioning/gallery-app-logos.png)

   Om du vill begära ett nytt program för etablering kan du [begära att ditt program är integrerat med vårt app-Galleri](../develop/v2-howto-app-gallery-listing.md). För en användar etablerings förfrågan kräver vi att programmet har en SCIM-kompatibel slut punkt. Be program leverantören att följa SCIM-standarden så att vi kan publicera appen på vår plattform snabbt.

* **Program som stöder SCIM 2,0**. Information om hur du kan ansluta program som implementerar SCIM 2,0-baserade API: er för användar hantering finns i [bygga en scim-slutpunkt och konfigurera användar etablering](use-scim-to-provision-users-and-groups.md).

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>Vad är system för SCIM (Cross-Domain Identity Management)?

För att automatisera etablering och avetablering kan appar exponera egna användar-och grupp-API: er. Men alla som försöker hantera användare i mer än en app meddelar dig att varje app försöker utföra samma enkla åtgärder, till exempel att skapa eller uppdatera användare, lägga till användare i grupper eller avetablera användare. Alla dessa enkla åtgärder implementeras bara lite annorlunda, med olika slut punkts sökvägar, olika metoder för att ange användar information och ett annat schema som representerar varje informations element.

För att lösa dessa utmaningar tillhandahåller SCIM-specifikationen ett gemensamt användar schema som hjälper användarna att förflytta sig till, ut och runt appar. SCIM håller på att bli den facto standard för etablering och, om det används tillsammans med Federations standarder som SAML eller OpenID Connect, ger administratörer en heltäckande lösning för åtkomst hantering från slut punkt till slut punkt.

Detaljerad information om hur du utvecklar en SCIM-slutpunkt för att automatisera etableringen och avetableringen av användare och grupper till ett program finns i [bygga en scim-slutpunkt och konfigurera användar etablering](use-scim-to-provision-users-and-groups.md). För förintegrerade program i galleriet (slack, Azure Databricks, snö, osv.) kan du hoppa över dokumentationen för utvecklare och använda de självstudier som finns [här](../saas-apps/tutorial-list.md).

## <a name="manual-vs-automatic-provisioning"></a>Manuell jämfört med automatisk etablering

Program i Azure AD-galleriet stöder ett av två etablerings lägen:

* **Manuell** etablering innebär att det inte finns någon automatisk Azure AD-etablerings anslutning för appen än. Användar konton måste skapas manuellt, till exempel genom att lägga till användare direkt i appens administrativa portal eller ladda upp ett kalkyl blad med information om användar kontot. Läs dokumentationen från appen eller kontakta appens utvecklare för att ta reda på vilka mekanismer som är tillgängliga.

* **Automatisk** innebär att en Azure AD-etablerings anslutning har utvecklats för det här programmet. Du bör följa installations självstudien som är specifik för att konfigurera etablering för programmet. Själv studie kurser för appar finns i [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md).

I Azure AD-galleriet anges program som stöder automatisk etablering av en **etablerings** ikon. Växla till den nya Galleri förhands granskningen för att se dessa ikoner (i banderollen högst upp på **sidan Lägg till ett program**) väljer du den länk som visas **Klicka här för att prova det nya och förbättrade app-galleriet**.

![Etablerings ikon i program galleriet](./media/user-provisioning/browse-gallery.png)

Det etablerings läge som stöds av ett program visas också på fliken **etablering** när du har lagt till programmet i dina **företags program**.

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>Hur gör jag för att konfigurera automatisk etablering till ett program?

För förintegrerade program som listas i galleriet finns steg-för-steg-anvisningar om hur du konfigurerar automatisk etablering. Se [listan med självstudier för integrerade Gallery-appar](../saas-apps/tutorial-list.md). Följande videoklipp visar hur du konfigurerar automatisk användar etablering för SalesForce.

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

För andra program som stöder SCIM 2,0 följer du stegen i artikeln [Bygg en scim-slutpunkt och konfigurerar användar etablering](use-scim-to-provision-users-and-groups.md).


## <a name="next-steps"></a>Nästa steg

- [Lista över självstudier om hur du integrerar SaaS-appar](../saas-apps/tutorial-list.md)
- [Anpassa mappningar av attribut för användar etablering](customize-application-attributes.md)
- [Omfångs filter för användar etablering](define-conditional-rules-for-provisioning-user-accounts.md)
