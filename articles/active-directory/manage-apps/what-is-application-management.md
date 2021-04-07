---
title: Vad är program hantering i Azure Active Directory
description: En översikt över hur du använder Azure Active Directory (AD) som identitets-och åtkomst hanterings system (IAM) för ditt moln och lokala program.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 01/22/2021
ms.author: kenwith
ms.reviewer: ''
ms.openlocfilehash: 247e824997fd95434246e49c78bf167f36e146c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258599"
---
# <a name="what-is-application-management"></a>Vad är programhantering?

Azure AD är ett IAM-system (identitets- och åtkomsthantering). Det ger en enskild plats för lagring av information om digitala identiteter. Du kan konfigurera dina program så att de använder Azure AD som den plats där användarinformation lagras. 

Azure AD måste konfigureras för integrering med ett program. Med andra ord måste de veta vilka appar som använder den för identiteter. Att göra Azure AD medveten om dessa appar och hur de ska hantera dem kallas för program hantering.

Du hanterar program på sidan **företags program** som finns i avsnittet hantera i Azure Active Directory-portalen.

![Alternativet företags program under avsnittet hantera i Azure AD-portalen.](media/what-is-application-management/enterprise-applications-in-nav.png)

## <a name="what-is-an-identity-and-access-management-iam-system"></a>Vad är ett identitets-och åtkomst hanterings system (IAM)?
Ett program är en del av program varan som används för något ändamål. De flesta appar kräver att användare loggar in.

Ett centraliserat identitets system tillhandahåller en enda plats för att lagra användar information som sedan kan användas av alla program. Dessa system har visat sig vara kända som identitets-och åtkomst hanterings system (IAM). Azure Active Directory är IAM-systemet för Microsoft-molnet.

>[!TIP]
>Ett IAM-system ger en enda plats för att hålla koll på användar identiteter. Azure AD är IAM-systemet för Microsoft-molnet.

## <a name="why-manage-applications-with-a-cloud-solution"></a>Varför hantera program med en molnlösning?

Organisationer har ofta hundratals program som användarna behöver för att utföra sitt arbete. Användare använder dessa program på många enheter och platser. Nya program läggs till, utvecklas och solnedgång hela tiden. Med så många appar och åtkomst punkter är det viktigt att använda en identitets lösning som fungerar med alla.

>[!TIP]
>Azure AD App-galleriet innehåller många populära program som redan är förkonfigurerade för att fungera med Azure AD som en identitets leverantör.

## <a name="how-does-azure-ad-work-with-apps"></a>Hur fungerar Azure AD med appar?

Azure AD är i mitten och ger identitets hantering för moln-och lokala appar. 

![Diagram som visar federerade appar via Azure AD](media/what-is-application-management/app-management-overview.png)

>[!TIP]
>Minska de administrativa kostnaderna genom att [Automatisera användar etableringen](../app-provisioning/user-provisioning.md) så att användarna automatiskt läggs till i Azure AD när du lägger till dem i ditt företags HR-system. 

## <a name="what-types-of-applications-can-i-integrate-with-azure-ad"></a>Vilka typer av program kan jag integrera med Azure AD?

Du kan använda Azure AD som ditt identitets system för precis om en app. Många appar är redan förkonfigurerade och kan konfigureras med minimal ansträngning. Dessa förkonfigurerade appar publiceras i [Azure AD App galleriet](/azure/active-directory/saas-apps/). 

Du kan konfigurera de flesta appar manuellt för enkel inloggning om de inte redan finns i galleriet. Azure AD tillhandahåller flera SSO-alternativ. Några av de mest populära är SAML-baserade SSO och OIDC SSO. Mer information om hur du integrerar appar för att aktivera SSO finns i [alternativ för enkel inloggning](sso-options.md). 

Använder din organisation lokala appar? Du kan integrera dem med hjälp av App proxy. Mer information finns i [ge fjärråtkomst till lokala program via Azure Ads Application Proxy](application-proxy.md).

>[!TIP]
>När du skapar egna affärs program kan du integrera dem med Azure AD för att stödja enkel inloggning. Mer information om hur du utvecklar appar för Azure AD finns i [Microsoft Identity Platform](..//develop/v2-overview.md).

## <a name="manage-risk-with-conditional-access-policies"></a>Hantera risker med principer för villkorlig åtkomst

Koppling av enkel inloggning med Azure AD (SSO) med [villkorlig åtkomst](../conditional-access/concept-conditional-access-cloud-apps.md) ger hög säkerhet för åtkomst till program. Principer för villkorlig åtkomst ger detaljerad kontroll över appar baserat på de villkor du anger. 

## <a name="improve-productivity-with-single-sign-on"></a>Förbättra produktiviteten med enkel inloggning

Enkel inloggning (SSO) ger en enhetlig användar upplevelse mellan Microsoft 365 och alla andra appar som du använder. Du kan inte ange ditt användar namn och lösen ord hela tiden.

Mer information om enkel inloggning finns i [Vad är enkel inloggning](what-is-single-sign-on.md).

## <a name="address-governance-and-compliance"></a>Ta itu med styrning och efterlevnad

Övervaka appar via rapporter som använder SIEM-verktyg (säkerhets incidenter och händelse övervakning). Du kommer åt rapporterna i portalen eller med API:er. Granska programmatiskt vem som har åtkomst till dina program och ta bort åtkomst för inaktiva användare via åtkomstgranskningar.

## <a name="manage-costs"></a>Hantera kostnader

Genom att migrera till Azure AD kan du minska kostnaderna och slippa besväret med att hantera en lokal infrastruktur. Azure AD tillhandahåller även åtkomst till program via självbetjäning, vilket sparar tid för både administratörer och användare. Enkel inloggning eliminerar programspecifika lösenord. Den här möjligheten att logga in en enda gång sänker kostnaderna i samband med återställning av lösenord för program och förlorad produktivitet under hämtning av lösenord.

För fokuserade program med mänsklig personal eller andra program med en stor uppsättning användare kan du använda app-etablering för att göra livet enklare. Med appens etablering automatiseras processen med att lägga till och ta bort användare. Mer information finns i [Vad är program etablering?](../app-provisioning/user-provisioning.md)

## <a name="next-steps"></a>Nästa steg

- [Snabb starts serie för program hantering](view-applications-portal.md)
- [Kom igång med program integrering](plan-an-application-integration.md)
- [Lär dig hur du automatiserar etablering](../app-provisioning/user-provisioning.md)