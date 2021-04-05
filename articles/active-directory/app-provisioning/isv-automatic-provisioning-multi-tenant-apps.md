---
title: Aktivera automatisk användar etablering för program med flera klienter – Azure AD
description: En guide för oberoende program varu leverantörer för att aktivera automatisk etablering
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/23/2019
ms.author: kenwith
ms.reviewer: zhchia
ms.openlocfilehash: 7bd0fc634109beb6cc674d89f56666e7035d33ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255703"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Aktivera automatisk användar etablering för ditt program för flera klienter

Automatisk användar etablering är en process där du automatiserar genereringen, underhållet och borttagningen av användar identiteter i mål systemen, till exempel program vara som en tjänst.

## <a name="why-enable-automatic-user-provisioning"></a>Varför ska jag aktivera automatisk användar etablering?

Program som kräver att en användar post finns i programmet innan en användares första inloggning kräver användar etablering. Det finns fördelar med dig som en tjänste leverantör och förmåner till dina kunder.

### <a name="benefits-to-you-as-the-service-provider"></a>Fördelar till dig som tjänst leverantör

* Öka säkerheten för ditt program med hjälp av Microsoft Identity Platform.

* Sänk faktisk och uppfattad kund ansträngning för att anta ditt program.

* Minska kostnaderna i integrering med flera identitets leverantörer (IDP: er) för automatisk användar etablering med hjälp av system för SCIM-baserad etablering (Cross-Domain Identity Management).

* Minska support kostnaderna genom att tillhandahålla omfattande loggar som hjälper kunderna att felsöka problem med användar etablering.

* Öka synligheten för ditt program i [Azure AD App-galleriet](https://azuremarketplace.microsoft.com/marketplace/apps).

* Få en prioriterad lista på sidan med program självstudier.

### <a name="benefits-to-your-customers"></a>Förmåner till dina kunder

* Öka säkerheten genom att automatiskt ta bort åtkomsten till programmet för användare som ändrar roller eller lämna organisationen till ditt program.

* Förenkla användar hanteringen för ditt program genom att undvika mänskligt fel och repetitivt arbete som är kopplat till manuell etablering.

* Sänk kostnaderna för att vara värd för och underhålla anpassade etablerings lösningar.

## <a name="choose-a-provisioning-method"></a>Välj en etableringsmetod

Azure AD tillhandahåller flera integrerings vägar för att aktivera automatisk användar etablering för programmet.

* [Azure AD Provisioning-tjänsten](../app-provisioning/user-provisioning.md) hanterar etableringen och avetableringen av användare från Azure AD till ditt program (utgående etablering) och från ditt program till Azure AD (inkommande etablering). Tjänsten ansluter till systemet med API-slutpunkter för användar hantering i SCIM (Cross-Domain Identity Management) som tillhandahålls av ditt program.

* När du använder [Microsoft Graph](/graph/)hanterar ditt program inkommande och utgående etablering av användare och grupper från Azure AD till ditt program genom att fråga Microsoft Graph-API: et.

* Användar etableringen för Security Assertion Markup Language just-in-Time (SAML JIT) kan aktive ras om programmet använder SAML för federation. Den använder anspråk information som skickas i SAML-token för att etablera användare.

För att avgöra vilket integrations alternativ som ska användas för programmet, se jämförelse tabellen på hög nivå och se mer detaljerad information om varje alternativ.

| Funktioner som är aktiverade eller utökade genom automatisk etablering| Azure AD Provisioning-tjänst (SCIM 2,0)| Microsoft Graph API (OData v 4.0)| SAML JIT |
|---|---|---|---|
| Hantering av användare och grupper i Azure AD| √| √| Endast användare |
| Hantera användare och grupper som synkroniseras från lokala Active Directory| √*| √*| Endast användare * |
| Få åtkomst till data utanför användare och grupper under etablerings åtkomst till Microsoft 365 data (team, SharePoint, e-post, kalender, dokument osv.)| X +| √| X |
| Skapa, läsa och uppdatera användare baserat på affärs regler| √| √| √ |
| Ta bort användare baserat på affärs regler| √| √| X |
| Hantera automatisk användar etablering för alla program från Azure Portal| √| X| √ |
| Stöd för flera identitets leverantörer| √| X| √ |
| Stöd gäst konton (B2B)| √| √| √ |
| Stöd för icke-Enterprise-konton (B2C)| X| √| √ |

<sup>*</sup> – Azure AD Connect installationen krävs för att synkronisera användare från AD till Azure AD.  
<sup>+</sup >– Om du använder SCIM för etablering hindras du från att integrera ditt program med Microsoft Graph för andra användnings sätt.

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD Provisioning-tjänst (SCIM)

Azure AD Provisioning Services använder [scim](https://aka.ms/SCIMOverview), en bransch standard för etablering som stöds av många identitets leverantörer (IDP: er) samt program (t. ex. slack, g Suite, Dropbox). Vi rekommenderar att du använder Azure AD Provisioning-tjänsten om du vill stödja IDP: er utöver Azure AD, eftersom alla SCIM-kompatibla IdP kan ansluta till din SCIM-slutpunkt. Genom att skapa en enkel/User-slutpunkt kan du aktivera etablering utan att behöva underhålla din egen synkronisering. 

Mer information om hur Azure AD Provisioning-tjänsten användare SCIM finns i: 

* [Läs mer om SCIM-standarden](https://aka.ms/SCIMOverview)

* [Använda systemet för SCIM (Cross-Domain Identity Management) för att automatiskt etablera användare och grupper från Azure Active Directory till program](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Förstå Azure AD SCIM-implementeringen](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph för etablering

När du använder Microsoft Graph för etablering har du till gång till alla omfattande användar data som är tillgängliga i Graph. Förutom information om användare och grupper kan du också hämta ytterligare information, t. ex. användarens roller, chef och direkt rapporter, ägda och registrerade enheter och hundratals andra data delar som är tillgängliga i [Microsoft Graph](/graph/api/overview). 

Fler än 15 000 000 organisationer och 90% av Fortune 500-företag använder Azure AD och prenumererar på Microsofts moln tjänster som Microsoft 365, Microsoft Azure eller Enterprise Mobility Suite. Du kan använda Microsoft Graph för att integrera din app med administrativa arbets flöden, till exempel medarbetarnas onboarding (och terminering), profil underhåll med mera. 

Läs mer om hur du använder Microsoft Graph för etablering:

* [Microsoft Graph start sida](https://developer.microsoft.com/graph)

* [Översikt över Microsoft Graph](/graph/overview)

* [Översikt över Microsoft Graph auth](/graph/auth/)

* [Komma igång med Microsoft Graph](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>Använda SAML JIT för etablering

Om du bara vill etablera användare vid första inloggningen till ditt program och inte behöver avetablera användare automatiskt, är SAML JIT ett alternativ. Ditt program måste ha stöd för SAML 2,0 som ett Federations protokoll för att använda SAML JIT.

SAML JIT använder anspråks informationen i SAML-token för att skapa och uppdatera användar information i programmet. Kunder kan konfigurera dessa nödvändiga anspråk i Azure AD-programmet efter behov. Ibland måste JIT-etableringen aktive ras från program sidan så att kunden kan använda den här funktionen. SAML JIT är användbart för att skapa och uppdatera användare, men det går inte att ta bort eller inaktivera användare i programmet.

## <a name="next-steps"></a>Nästa steg

* [Aktivera enkel inloggning för ditt program](../develop/v2-howto-app-gallery-listing.md)

* [Skicka in din program lista](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) och partner med Microsoft för att skapa dokumentation på Microsofts webbplats.

* [Gå med i Microsoft Partner Network (kostnads fri) och skapa en marknads plan](https://partner.microsoft.com/explore/commercial).
