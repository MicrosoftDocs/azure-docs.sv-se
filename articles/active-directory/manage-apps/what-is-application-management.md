---
title: Vad är programhantering i Azure Active Directory
description: En översikt över hur Azure Active Directory (AD) används som ett IAM-system (Identitets- och åtkomsthantering) för dina molnprogram och lokala program.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: iangithinji
ms.reviewer: ''
ms.openlocfilehash: 8bdb8eb9cf176f8e190769e38c81d02ad2985196
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376400"
---
# <a name="what-is-application-management"></a>Vad är programhantering?

Azure AD är ett IAM-system (identitets- och åtkomsthantering). Det ger en enskild plats för lagring av information om digitala identiteter. Du kan konfigurera dina program så att de använder Azure AD som den plats där användarinformation lagras. 

Azure AD måste konfigureras för att integreras med ett program. Med andra ord måste den veta vilka appar som använder den för identiteter. Att göra Azure AD medveten om dessa appar och hur de ska hanteras kallas programhantering.

Du hanterar program på **sidan Företagsprogram** som finns i avsnittet Hantera i Azure Active Directory portalen.

![Alternativet Företagsprogram under avsnittet Hantera i Azure AD-portalen.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Vad är ett IAM-system (Identitets- och åtkomsthantering)?
Ett program är en programvara som används för något ändamål. De flesta appar kräver att användarna loggar in.

Ett centraliserat identitetssystem ger en enda plats för att lagra användarinformation som sedan kan användas av alla program. Dessa system har blivit kända som IAM-system (Identitets- och åtkomsthantering). Azure Active Directory är IAM-systemet för Microsoft-molnet.

>[!TIP]
>Ett IAM-system tillhandahåller en enda plats där du kan hålla reda på användaridentiteter. Azure AD är IAM-systemet för Microsoft-molnet.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Varför hantera program med en molnlösning?

Organisationer har ofta hundratals program som användarna behöver för att utföra sitt arbete. Användare använder dessa program på många enheter och platser. Nya program läggs till, utvecklas och tas bort hela tiden. Med så många appar och åtkomstpunkter är det viktigt att använda en identitetslösning som fungerar med alla.

>[!TIP]
>Azure AD-appgalleriet innehåller många populära program som redan är förkonfigurerade för att fungera med Azure AD som identitetsprovider.

## <a name="how-does-azure-ad-work-with-apps"></a>Hur fungerar Azure AD med appar?

Azure AD finns i mitten och tillhandahåller identitetshantering för molnbaserade och lokala appar. 

![Diagram som visar federerade appar via Azure AD](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Minska administrativa kostnader genom [att automatisera användareablering](../app-provisioning/user-provisioning.md) så att användarna automatiskt läggs till i Azure AD när du lägger till dem i företagets HR-system. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Vilka typer av program kan jag integrera med Azure AD?

Du kan använda Azure AD som identitetssystem för i de flesta fall vilken app som helst. Många appar är redan förkonfigurerade och kan konfigureras med minimal ansträngning. Dessa förkonfigurerade appar publiceras i [Azure AD App Gallery](/azure/active-directory/saas-apps/). 

Du kan konfigurera de flesta appar för enkel inloggning manuellt om de inte redan finns i galleriet. Azure AD har flera alternativ för enkel inloggning. Några av de mest populära är SAML-baserad enkel inloggning och OIDC-baserad enkel inloggning. Mer information om hur du integrerar appar för att aktivera enkel inloggning [finns i alternativ för enkel inloggning.](sso-options.md) 

Använder din organisation lokala appar? Du kan integrera dem med appproxy. Mer information finns i [Ge fjärråtkomst till lokala program via Azure AD:s Programproxy](application-proxy.md).

>[!TIP]
>När du skapar egna affärsprogram kan du integrera dem med Azure AD för att stödja enkel inloggning. Mer information om hur du utvecklar appar för Azure AD finns i [Microsoft Identity Platform](..//develop/v2-overview.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Hantera risker med principer för villkorsstyrd åtkomst

Att koppla azure AD enkel inloggning (SSO) med [villkorsstyrd åtkomst ger](../conditional-access/concept-conditional-access-cloud-apps.md) hög säkerhet för åtkomst till program. Principer för villkorsstyrd åtkomst ger detaljerad kontroll över appar baserat på villkor som du anger. 

## <a name="improve-productivity-with-single-sign-on"></a>Förbättra produktiviteten med enkel inloggning

Enkel inloggning (SSO) ger en enhetlig användarupplevelse mellan Microsoft 365 och alla andra appar som du använder. Säg hej då till att hela tiden ange ditt användarnamn och lösenord!

Mer information om enkel inloggning finns i [vad är enkel inloggning.](what-is-single-sign-on.md)

## <a name="address-governance-and-compliance"></a>Ta itu med styrning och efterlevnad

Övervaka appar via rapporter som använder SIEM-verktyg (Säkerhetsincident- och händelseövervakning). Du kommer åt rapporterna i portalen eller med API:er. Granska programmatiskt vem som har åtkomst till dina program och ta bort åtkomst för inaktiva användare via åtkomstgranskningar.

## <a name="manage-costs"></a>Hantera kostnader

Genom att migrera till Azure AD kan du minska kostnaderna och slippa besväret med att hantera en lokal infrastruktur. Azure AD tillhandahåller även åtkomst till program via självbetjäning, vilket sparar tid för både administratörer och användare. Enkel inloggning eliminerar programspecifika lösenord. Den här möjligheten att logga in en enda gång sänker kostnaderna i samband med återställning av lösenord för program och förlorad produktivitet under hämtning av lösenord.

För program som fokuserar på personal eller andra program med många användare kan du använda appetablering för att göra livet enklare. Appetablering automatiserar processen med att lägga till och ta bort användare. Mer information finns i [Vad är programetablering?](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Nästa steg

- [Snabbstartsserie om programhantering](view-applications-portal.md)
- [Kom igång med programintegrering](plan-an-application-integration.md)
- [Lär dig hur du automatiserar etablering](../app-provisioning/user-provisioning.md)