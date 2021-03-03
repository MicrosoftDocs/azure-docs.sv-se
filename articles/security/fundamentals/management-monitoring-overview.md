---
title: Säkerhets funktioner för hantering och övervakning – Microsoft Azure | Microsoft Docs
description: Den här artikeln innehåller en översikt över de säkerhetsfunktioner och tjänster som Azure tillhandahåller för att hjälpa till med hantering och övervakning av Azure Cloud Services och Virtual Machines.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: terrylan
ms.openlocfilehash: f87ea1e1c9f43de4e9e0f94d1cd855615a0a880c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712053"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Översikt över Azure Security Management och Monitoring
Den här artikeln innehåller en översikt över de säkerhetsfunktioner och tjänster som Azure tillhandahåller för att hjälpa till med hantering och övervakning av Azure Cloud Services och Virtual Machines.

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Azure rollbaserad åtkomst kontroll (Azure RBAC) ger detaljerad åtkomst hantering för Azure-resurser. Genom att använda Azure RBAC kan du endast bevilja användare den mängd åtkomst som de behöver för att utföra sina jobb. Azure RBAC kan också hjälpa dig att se till att när personer lämnar organisationen förlorar de åtkomst till resurser i molnet.

Läs mer:

* [Active Directory Team blogg på Azure RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Azure RBAC (rollbaserad åtkomstkontroll)](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Programvara mot skadlig kod

Med Azure kan du använda program mot skadlig kod från stora säkerhets leverantörer som Microsoft, Symantec, Trend Micro, McAfee och Kasper Sky. Den här program varan hjälper till att skydda dina virtuella datorer från skadliga filer, annons program och andra hot.

Microsoft Antimalware för Azure Cloud Services och Virtual Machines ger dig möjlighet att installera en agent för program mot skadlig kod för både PaaS-roller och virtuella datorer. Den här funktionen är baserad på System Center-Endpoint Protection och ger den beprövade säkerhets tekniken till molnet.

Vi erbjuder också djupgående integrering för trenders [djup säkerhet](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) och [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) -produkter i Azure-plattformen. Djupgående säkerhet är en antivirus lösning och SecureCloud är en krypterings lösning. Djup säkerhet distribueras i virtuella datorer via en tilläggs modell. Genom att använda Azure Portal användar gränssnitt och PowerShell kan du välja att använda djup säkerhet i nya virtuella datorer som är stora eller befintliga virtuella datorer som redan har distribuerats.

Symantec Endpoint Protection (SEP) stöds också på Azure. Via portal integrering kan du ange att du tänker använda SEP på en virtuell dator. SEP kan installeras på en ny virtuell dator via Azure Portal, eller så kan den installeras på en befintlig virtuell dator via PowerShell.

Läs mer:

* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware för Azure Cloud Services och Virtual Machines](antimalware.md)
* [Så här installerar och konfigurerar du Trend Micro djupgående säkerhet som en tjänst på en virtuell Windows-dator](/previous-versions/azure/virtual-machines/extensions/trend)
* [Så här installerar och konfigurerar du Symantec Endpoint Protection på en virtuell Windows-dator](../../virtual-machines/extensions/symantec.md)
* [Nya alternativ för program mot skadlig kod för att skydda Azure Virtual Machines](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure AD Multi-Factor Authentication är en autentiseringsmetod som kräver användning av mer än en verifieringsmetod. Den lägger till ett kritiskt andra säkerhets lager för användar inloggningar och transaktioner.

Multi-Factor Authentication hjälper till att skydda åtkomsten till data och program samtidigt som du kan möta användarnas behov av en enkel inloggnings process. Den ger stark autentisering via en mängd verifierings alternativ (telefonsamtal, textmeddelande eller meddelande för mobilapp eller verifierings kod) och OATH-token från tredje part.

Läs mer:

* [Multifaktorautentisering](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Vad är Azure AD Multi-Factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md)
* [Så fungerar Azure AD-multifaktorautentisering](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Du kan använda Azure-ExpressRoute för att utöka dina lokala nätverk till Microsoft Cloud över en dedikerad privat anslutning som under lättas av en anslutnings leverantör. Med ExpressRoute kan du upprätta anslutningar till Microsofts moln tjänster som Azure, Microsoft 365 och CRM Online. Anslutningen kan vara från:

* Ett valfritt-till-valfritt-nätverk (IP-nätverk).
* Ett Ethernet-nätverk från punkt till punkt.
* En virtuell kors anslutning via en anslutnings leverantör i en samplacerings funktion.

ExpressRoute-anslutningar går inte via det offentliga Internet. De kan erbjuda mer tillförlitlighet, snabbare hastighet, lägre fördröjning och högre säkerhet än vanliga anslutningar via Internet.

Läs mer:

* [Teknisk översikt för ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuella nätverksgatewayer

VPN-gatewayer, som även kallas Azure virtuella nätverksgateway, används för att skicka nätverks trafik mellan virtuella nätverk och lokala platser. De används också för att skicka trafik mellan flera virtuella nätverk i Azure (nätverk till nätverk). VPN-gatewayer ger säker anslutning mellan olika platser mellan Azure och din infrastruktur.

Läs mer:

* [Om VPN-gatewayer](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Översikt över nätverkssäkerhet i Azure](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Ibland behöver användarna utföra privilegierade åtgärder i Azure-resurser eller andra SaaS-program. Detta innebär ofta att organisationer ger dem permanent privilegie rad åtkomst i Azure Active Directory (Azure AD).

Detta är en växande säkerhets risk för resurser som är värd för molnet eftersom organisationer inte kan övervaka vad dessa användare gör med sin privilegie rad åtkomst. Om ett användar konto med privilegie rad åtkomst komprometteras, kan det dessutom påverka en organisations övergripande moln säkerhet. Azure AD Privileged Identity Management hjälper till att lösa den här risken genom att minska exponerings tiden för privilegier och öka insynen i användningen.  

Privileged Identity Management introducerar konceptet för en tillfällig administratör för en roll eller "just-in-Time"-administratörs åtkomst. Den här typen av administratör är en användare som måste slutföra en aktiverings process för den tilldelade rollen. Aktiverings processen ändrar tilldelningen av användaren till en roll i Azure AD från inaktiv till aktiv under en angiven tids period.

Läs mer:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Komma igång med Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection ger en samlad vy över misstänkta inloggnings aktiviteter och potentiella sårbarheter som hjälper dig att skydda ditt företag. Identitets skyddet identifierar misstänkta aktiviteter för användare och privilegierade (admin) identiteter, baserat på signaler som:

* Brute-force-attacker.
* Läckta autentiseringsuppgifter.
* Inloggningar från okända platser och infekterade enheter.

Genom att tillhandahålla meddelanden och Rekommenderad reparation bidrar identitets skyddet till att minska riskerna i real tid. Den beräknar allvarlighets grad för användar risk. Du kan konfigurera riskfyllda principer för att automatiskt skydda program åtkomsten från framtida hot.

Läs mer:

* [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)
* [Kanal 9: Azure AD och identitet Visa: identitets skydd för hands version](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Azure Security Center hjälper dig att förebygga, identifiera och reagera på hot. Security Center ger ökad insyn i och kontroll över säkerheten för dina Azure-resurser samt de i din hybrid moln miljö. 

Security Center utför kontinuerliga säkerhets utvärderingar av dina anslutna resurser och jämför deras konfiguration och distribution mot [Azures säkerhets benchmark](../benchmarks/introduction.md) för att ge detaljerade säkerhets rekommendationer som är anpassade för din miljö.

Security Center hjälper dig att optimera och övervaka säkerheten för dina Azure-resurser genom att:

- Gör det möjligt att definiera principer för dina prenumerations resurser för Azure enligt:
    - Organisationens säkerhets behov.
    - Typ av program eller känslighet för data i varje prenumeration.
    - Alla bransch-eller reglerings standarder eller riktmärken som du använder för dina prenumerationer. 
- Övervaka statusen för dina virtuella Azure-datorer, nätverk och program.
- Tillhandahålla en lista över prioriterade säkerhets aviseringar, inklusive aviseringar från integrerade partner lösningar. Den innehåller också den information som du behöver för att snabbt undersöka ett angrepp och rekommendationer om hur du kan åtgärda det.

Läs mer:

* [Introduktion till Azure Security Center](../../security-center/security-center-introduction.md)
* [Förbättra dina säkra poäng i Azure Security Center](../../security-center/secure-score-security-controls.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph ger skydd mot hot i real tid i Microsofts produkter och tjänster. Den använder avancerad analys som länkar en enorm mängd hot information och säkerhets data för att ge insikter som kan stärka organisationens säkerhet. Microsoft använder avancerade analyser – bearbetning av mer än 450 000 000 000 autentiseringar per månad, genomsökning av 400 000 000 000-e-post för skadlig kod och nätfiske samt uppdatering av 1 000 000 000-enheter – för att leverera djupare insikter. Dessa insikter kan hjälpa din organisation att upptäcka och svara på attacker snabbt.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [delade ansvars modellen](shared-responsibility.md) och vilka säkerhets uppgifter som hanteras av Microsoft och vilka aktiviteter som hanteras av dig.

Mer information om säkerhets hantering finns [i säkerhets hantering i Azure](management.md).