---
title: Azure Active Directory och transactable SaaS-erbjudanden på den kommersiella marknaden
description: Lär dig hur Azure Active Directory fungerar med transactable SaaS-erbjudanden på Microsofts kommersiella marknads platser.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 674f267d3d99dd22c1ae06b6d32587761d5983ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93124925"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>Azure AD och transactable SaaS-erbjudanden på den kommersiella marknaden

Den molnbaserade identitets-och åtkomst hanterings tjänsten i Microsoft, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) hjälper användarna att logga in och komma åt interna och externa resurser. På Microsofts kommersiella marknads plats gör Azure AD att du kan använda transactable SaaS enklare och säkrare för alla, inklusive utgivare, köpare och användare. Med Azure AD kan utgivare automatisera etableringen av användare till deras SaaS-appar (program vara som en tjänst) och köparna själva kan hantera dessa etablerade användare. 

Dessutom ger [Azure AD enkel inloggning](../active-directory/manage-apps/what-is-single-sign-on.md) (SSO) säkerhet och bekvämlighet när användare loggar in på appar i Azure AD. Snabbare engagemang och optimerade upplevelser är också inspirera köpare och användarens förtroende från den allra första interaktionen med en utgivares SaaS-app. Detta ger ett positivt intryck som skapar synlighet och uppmuntrar upprepade affärer.

Genom att följa anvisningarna i den här artikeln får du hjälp med att certifiera ditt SaaS-erbjudande på den kommersiella Marketplace. Om du vill ha mer information om certifiering läser du de detaljerade [certifierings principerna för kommersiella marknads platser](/legal/marketplace/certification-policies#100-general), inklusive de [som är speciella för SaaS](/legal/marketplace/certification-policies#1000-software-as-a-service-saas).

## <a name="before-you-begin"></a>Innan du börjar

När du [skapar ditt SaaS-erbjudande](./create-new-saas-offer.md) i Partner Center väljer du från en uppsättning med olika List alternativ som visas i erbjudande listan. Ditt val avgör hur ditt erbjudande ska hanteras i den kommersiella marknads platsen. Erbjudanden som säljs via Microsoft kallas för transactable-erbjudanden. Vi fakturerar kunden för din räkning för alla transactable-erbjudanden. Om du väljer att sälja via Microsoft och har oss värd transaktioner för din räkning (alternativet **Ja** ) har du valt att skapa ett valfritt erbjudande och den här artikeln är för dig. Vi rekommenderar att du läser det helt.

Om du väljer att bara lista ditt erbjudande via de kommersiella marknads platser och bearbeta transaktioner oberoende av varandra (alternativet **Nej** ) har du tre alternativ för hur potentiella kunder får åtkomst till erbjudandet: Skaffa det nu (kostnads fritt), kostnads fri utvärderings version och kontakta mig. Om du väljer **Hämta nu (kostnads fritt)** eller en **kostnads fri utvärderings version** är den här artikeln inte för dig. Mer information finns i avsnittet [bygga landnings sidan för ditt kostnads fria eller utvärderings SaaS-erbjudande på den kommersiella Marketplace](./azure-ad-free-or-trial-landing-page.md) . Om du väljer **kontakta mig** finns det inga direkta utgivar ansvar. Fortsätt att skapa ditt erbjudande i Partner Center.

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Hur Azure AD fungerar med den kommersiella Marketplace för SaaS-erbjudanden

Azure AD möjliggör sömlös inköp, utförande och hantering av kommersiella Marketplace-lösningar. Bild 1 visar hur utgivare, köpare och användare interagerar med köp och aktiverar en prenumeration. Det visar också hur kunderna använder och hanterar SaaS-program som de får från den kommersiella marknads platsen. I den här bilden är köparen SaaS program användare som initierar ett köp från den kommersiella Marketplace.

Som du ser i bild 1, när en köpare väljer erbjudandet, så startar de en kedja av arbets flöden som omfattar köp-, prenumerations-och användar hantering. I den här kedjan är utgivaren ansvarig för vissa krav och Microsoft tillhandahåller support på viktiga punkter.

***Bild 1: använda Azure AD för SaaS-erbjudanden på den kommersiella marknaden***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="Illustrerar stegen för hantering av inköps hantering, prenumerations hantering och valfria användar hantering.":::

I följande avsnitt finns information om kraven för varje process steg.

## <a name="process-steps-for-purchase-management"></a>Process steg för inköps hantering

Den här bilden visar de fyra process stegen för inköps hantering.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="Illustrerar de fyra stegen i processen för inköps hantering.":::

Den här tabellen innehåller information om stegen för inköps hanterings processen.

| Process steg | Utgivar åtgärd | Rekommenderas eller krävs för utgivare |
| ------------ | ------------- | ------------- |
| 1. köparen loggar in på den kommersiella marknads platsen med sin Azure ID-identitet och väljer ett SaaS-erbjudande. | Ingen utgivar åtgärd krävs. | Inte tillämpligt |
| 2. efter köpet väljer köparen **Konfigurera konto** i Azure Marketplace eller **konfigurerar nu** i AppSource, som dirigerar köparen till utgivarens landnings sida för det här erbjudandet. Köparen måste kunna logga in på utgivarens SaaS-program med Azure AD SSO och får bara tillfrågas om minimalt medgivande som inte kräver Azure AD-administratörs godkännande. | Utforma en [landnings sida](azure-ad-transactable-saas-landing-page.md) för erbjudandet så att den får en användare med sin Azure AD-eller Microsoft-konto-identitet (MSA) och underlättar eventuell ytterligare etablering eller konfiguration som krävs. | Obligatorisk |
| 3. utgivaren begär köp information från API: et för SaaS-utförande. | Med hjälp [av en åtkomsttoken](./partner-center-portal/pc-saas-registration.md) som genererats från landnings sidans program-ID, [anropar du matchnings slut punkten](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) för att hämta information om köpet. | Obligatorisk |
| 4. via Azure AD och Microsoft Graph API samlar utgivaren in företags-och användar information som krävs för att tillhandahålla köparen i utgivarens SaaS-program.  | Dela upp Azure AD-användartoken för att hitta namn och e-post eller [anropa Microsoft Graph-API: et](/graph/use-the-api) och Använd delegerade behörigheter för att [Hämta information](/graph/api/user-get) om den användare som är inloggad. | Obligatorisk |
||||

## <a name="process-steps-for-subscription-management"></a>Process steg för prenumerations hantering

Den här bilden visar de två stegen i processen för prenumerations hantering.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="Visar de två stegen i processen för prenumerations hantering.":::

Den här tabellen beskriver detaljerna om stegen för prenumerations hanterings processen.

| Process steg | Utgivar åtgärd | Rekommenderas eller krävs för utgivare |
| ------------ | ------------- | ------------- |
| 5. utgivaren hanterar prenumerationen på SaaS-programmet via SaaS-uppfyllande API: et. | Hantera prenumerations ändringar och andra hanterings uppgifter via [API: er för SaaS-utförande](./partner-center-portal/pc-saas-fulfillment-api-v2.md).<br><br>Det här steget kräver en åtkomsttoken enligt beskrivningen i process steg 3. | Obligatorisk |
| 6. När du använder mätnings priser genererar utgivaren användnings händelser till API: et för mätnings tjänsten. | Om din SaaS-app har användnings fakturerings funktioner, kan du göra användnings meddelanden via [API: er för avläsning av tjänst](./partner-center-portal/marketplace-metering-service-apis.md).<br><br>Det här steget kräver en åtkomsttoken enligt beskrivningen i steg 3. | Krävs för mätning |
||||

## <a name="process-steps-for-user-management"></a>Process steg för användar hantering

Den här bilden visar de tre process stegen för användar hantering.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="Illustrerar de tre valfria process stegen för användar hantering.":::

Process steg 7 till 9 är valfria steg för att hantera användar hantering. De ger ytterligare förmåner för utgivare som har stöd för enkel inloggning med Azure AD. I den här tabellen beskrivs information om stegen för användar hanterings processen.

| Process steg | Utgivar åtgärd | Rekommenderas eller krävs för utgivare |
| ------------ | ------------- | ------------- |
| 7. Azure AD-administratörer på Köp företaget kan alternativt hantera åtkomst för användare och grupper via Azure AD. | Ingen utgivar åtgärd krävs för att aktivera detta om Azure AD SSO har kon figurer ATS för användare (steg 9). | Inte tillämpligt |
| 8. Azure AD Provisioning-tjänsten kommunicerar ändringar mellan Azure AD och utgivarens SaaS-program. | [Implementera en scim-slutpunkt](../active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md) för att ta emot uppdateringar från Azure AD när användare läggs till och tas bort. | Rekommenderas |
| 9. När appen har behörighet och är etablerad kan användare från köparens företag använda Azure AD SSO för att logga in i utgivarens SaaS-program. | [Använd Azure AD SSO](../active-directory/manage-apps/what-is-single-sign-on.md) för att göra det möjligt för användare att logga in en gång med ett konto till utgivarens SaaS-program. | Rekommenderas |
||||

## <a name="next-steps"></a>Nästa steg

- [Bygg in landnings sidan för ditt SaaS-erbjudande i kommersiellt marknads plats](azure-ad-transactable-saas-landing-page.md)
- [Bygg in landnings sidan för ditt kostnads fria eller utvärderings SaaS-erbjudande på den kommersiella Marketplace](azure-ad-free-or-trial-landing-page.md)
- [Så här skapar du ett SaaS-erbjudande på den kommersiella Marketplace](create-new-saas-offer.md)