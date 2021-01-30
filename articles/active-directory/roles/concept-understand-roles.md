---
title: Förstå begrepp för Azure Active Directory roll
description: Lär dig att förstå Azure Active Directory inbyggda och anpassade roller med resurs omfång i Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3101cf3650fcfb7f9313b13c1c07a03dd734e9dd
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089232"
---
# <a name="understand-roles-in-azure-active-directory"></a>Förstå roller i Azure Active Directory

Det finns cirka 60 Azure Active Directory (Azure AD) inbyggda roller, som är roller med en fast uppsättning roll behörigheter. För att komplettera de inbyggda rollerna stöder Azure AD även anpassade roller. Använd anpassade roller för att välja de roll behörigheter som du vill använda. Du kan till exempel skapa en för att hantera specifika Azure AD-resurser, till exempel program eller tjänstens huvud namn.

I den här artikeln förklaras vad Azure AD-roller är och hur de kan användas.

## <a name="how-azure-ad-roles-are-different-from-other-microsoft-365-roles"></a>Hur Azure AD-roller skiljer sig från andra Microsoft 365 roller

Det finns många olika tjänster i Microsoft 365, till exempel Azure AD och Intune. Några av dessa tjänster har sina egna rollbaserade åtkomst kontroll system; utformat

- Azure AD
- Exchange
- Intune
- Security Center
- Compliance Center
- Microsoft Cloud App Security
- Detaljhandel

Andra tjänster, till exempel team, SharePoint och hanterade datorer har inte separata rollbaserade åtkomst kontroll system. De använder Azure AD-roller för sin administrativa åtkomst. Azure har sitt eget rollbaserade åtkomst kontroll system för Azure-resurser, till exempel virtuella datorer, och det här systemet är inte detsamma som för Azure AD-roller.

När vi säger separat rollbaserad åtkomst kontroll system. Det innebär att det finns ett annat data lager där roll definitioner och roll tilldelningar lagras. På samma sätt finns det en annan princip besluts punkt där åtkomst kontroller sker. Mer information finns i [roller för Microsoft 365 tjänster i Azure AD](m365-workload-docs.md) och [klassiska prenumerations administratörs roller, Azure-roller och Azure AD-roller](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="why-some-azure-ad-roles-are-for-other-services"></a>Varför vissa Azure AD-roller är för andra tjänster

Microsoft 365 har ett antal rollbaserade åtkomst kontroll system som utvecklas oberoende av tiden, var och en med sin egen service portal. För att göra det enklare för dig att hantera identitet i Microsoft 365 från Azure AD-portalen har vi lagt till vissa tjänstesärskilda inbyggda roller som ger administrativ åtkomst till en Microsoft 365 tjänst. Ett exempel på det här tillägget är rollen Exchange-administratör i Azure AD. Den här rollen motsvarar [roll gruppen organisations hantering](/exchange/organization-management-exchange-2013-help) i det rollbaserade åtkomst kontroll systemet i Exchange, och kan hantera alla aspekter av Exchange. På samma sätt har vi lagt till Intune-administratörskonsolen, team administratör, SharePoint-administratör och så vidare. Tjänstspecifika roller är en kategori av inbyggda Azure AD-roller i följande avsnitt.

## <a name="categories-of-azure-ad-roles"></a>Kategorier av Azure AD-roller

Inbyggda Azure AD-roller skiljer sig åt i var de kan användas, vilket ligger i följande tre breda kategorier.

- **Azure AD-särskilda roller**: de här rollerna beviljar behörigheter att endast hantera resurser i Azure AD. Till exempel, användar administratör, program administratör, grupper administratör ger alla behörighet att hantera resurser som är aktiva i Azure AD.
- **Tjänstspecifika roller**: för större Microsoft 365 tjänster (icke-Azure AD) har vi skapat tjänstspecifika roller som ger behörighet att hantera alla funktioner i tjänsten.  Till exempel kan rollen Exchange-administratör, Intune-administratör, SharePoint-administratör och team-administratörer hantera funktioner med deras respektive tjänster. Exchange-administratören kan hantera post lådor, Intune-administratören kan hantera enhets principer. SharePoint-administratören kan hantera webbplats samlingar och team administratören kan hantera samtals kvaliteter och så vidare.
- **Roller för flera tjänster**: det finns vissa roller som omfattar tjänster. Vi har två globala roller – global administratör och global läsare. Alla Microsoft 365-tjänster följer dessa två roller. Det finns också några säkerhetsrelaterade roller som säkerhets-och säkerhets läsare som beviljar åtkomst över flera säkerhets tjänster inom Microsoft 365. Du kan till exempel använda säkerhets administratörs roller i Azure AD för att hantera Microsoft 365 Security Center, Microsoft Defender Avancerat skydd och Microsoft Cloud App Security. På samma sätt kan du, i rollen kompatibilitets administratör, hantera inställningar för kompatibilitet i Microsoft 365 Compliance Center, Exchange och så vidare.

![De tre kategorierna av inbyggda Azure AD-roller](./media/concept-understand-roles/role-overlap-diagram.png)

Följande tabell erbjuds som ett stöd för att förstå dessa roll kategorier. Kategorierna namnges godtyckligt och är inte avsedda att innebära andra funktioner utöver de [dokumenterade roll behörigheterna](permissions-reference.md).

Kategori | Roll
---- | ----
Azure AD-särskilda roller | Programadministratör<br>Programutvecklare<br>Administratör för autentisering<br>B2C IEF Keys-administratör<br>B2C IEF-princip administratör<br>Molnprogramadministratör<br>Moln enhets administratör<br>Administratör för villkorsstyrd åtkomst<br>Enhetsadministratörer<br>Katalog läsare<br>Konton för katalog synkronisering<br>Katalog skrivare<br>Användar flödes administratör för externt ID<br>Administratör för externt ID-attribut för användar flöde<br>Administratör för extern identitetsprovider<br>Grupp administratör<br>Gäst deltagare<br>Support administratör<br>Hybrid identitets administratör<br>Licens administratör<br>Support för partner 1<br>Support för partner – nivå 2<br>Lösen ords administratör<br>Administratör för privilegie rad autentisering<br>Privilegie rad roll administratör<br>Rapportläsare<br>Administratör för användar konton
Kors tjänst roller | Global administratör<br>Efterlevnadsadministratör<br>Efterlevnadsdataadministratör<br>Global läsare<br>Säkerhetsadministratör<br>Säkerhetsoperatör<br>Säkerhetsläsare<br>Tjänst support administratör
Tjänstspecifika roller | Azure DevOps-administratör<br>Azure Information Protection administratör<br>Faktureringsadministratör<br>Administratör för CRM-tjänsten<br>Åtkomst god kännare för kund lås<br>Administratör för Skriv bords analys<br>Exchange service-administratör<br>Insights-administratör<br>Affärs ledare för insikter<br>Intune Service-administratör<br>Kaizala-administratör<br>Administratör för Lync-tjänsten<br>Meddelande Center sekretess läsare<br>Meddelande Center läsare<br>Modern Commerce-användare<br>Nätverks administratör<br>Office Apps-administratör<br>Power BI-tjänstadministratör<br>Power Platform-administratör<br>Skrivar administratör<br>Skrivar tekniker<br>Sök administratör<br>Sök redigerare<br>SharePoint-tjänsteadministratör<br>Team kommunikations administratör<br>Support tekniker för Teams kommunikation<br>Support specialist för Teams kommunikation<br>Team enhets administratör<br>Team tjänst administratör

## <a name="next-steps"></a>Nästa steg

- [Översikt över rollbaserad åtkomst kontroll i Azure AD](custom-overview.md)
- Skapa roll tilldelningar med hjälp av [Azure Portal, Azure AD PowerShell och Graph API](custom-create.md)
- [Visa tilldelningarna för en roll](custom-view-assignments.md)
