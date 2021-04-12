---
title: Säkerhet från slut punkt till slut punkt i Azure | Microsoft Docs
description: Artikeln innehåller en karta över Azure-tjänster som hjälper dig att skydda och skydda dina moln resurser och identifiera och undersöka hot.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/07/2021
ms.author: terrylan
ms.openlocfilehash: be270383b868abb7e2865920f4a3852e2f6210dc
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031424"
---
# <a name="end-to-end-security-in-azure"></a>Säkerhet från slut punkt till slut punkt i Azure
Ett av de bästa orsakerna till att använda Azure för dina program och tjänster är att dra nytta av dess breda uppsättning säkerhets verktyg och funktioner. Med dessa verktyg och funktioner kan du skapa säkra lösningar på den säkra Azure-plattformen. Microsoft Azure ger konfidentialitet, integritet och tillgänglighet för kund information, samtidigt som du aktiverar transparent ansvars rätt.

Följande diagram och dokumentation beskriver säkerhets tjänsterna i Azure. Med de här säkerhets tjänsterna kan du möta företagets säkerhets behov och skydda dina användare, enheter, resurser, data och program i molnet.

## <a name="microsoft-security-services-map"></a>Microsoft Security Services-karta

Säkerhets tjänst kartan organiserar tjänster av de resurser som de skyddar (kolumn). Diagrammet grupperar även tjänster till följande kategorier (rad):

- Skydda och skydda-tjänster som gör att du kan implementera en lager och en djupgående strategi för att identifiera identiteter, värdar, nätverk och data. Den här samlingen av säkerhets tjänster och funktioner är ett sätt att förstå och förbättra din säkerhets position i Azure-miljön.
- Identifiera hot – tjänster som identifierar misstänkta aktiviteter och gör det lättare att minska risken.
- Undersök och svara – tjänster som hämtar loggnings data så att du kan utvärdera en misstänkt aktivitet och svara.

Diagrammet innehåller Azures säkerhets-benchmark-program, en samling med högkvalitativa säkerhets rekommendationer som du kan använda för att skydda de tjänster som du använder i Azure.

:::image type="content" source="media/end-to-end/security-diagram.svg" alt-text="Diagram som visar säkerhets tjänster från slut punkt till slut punkt i Azure." border="false":::

## <a name="security-controls-and-baselines"></a>Säkerhets kontroller och bas linjer
[Azures säkerhets benchmark](../benchmarks/introduction.md) -program innehåller en samling med högkvalitativa säkerhets rekommendationer som du kan använda för att skydda de tjänster som du använder i Azure:

- Säkerhets kontroller – de här rekommendationerna är allmänt tillgängliga i din Azure-klient och Azure-tjänster. Varje rekommendation identifierar en lista över intressenter som vanligt vis ingår i planering, godkännande eller implementering av benchmark.
- Tjänste bas linjer – dessa gäller för enskilda Azure-tjänster för att tillhandahålla rekommendationer för den tjänstens säkerhets konfiguration.

## <a name="secure-and-protect"></a>Skydda och skydda

:::image type="content" source="media/end-to-end/secure-and-protect.svg" alt-text="Diagram över Azure-tjänster som hjälper dig att skydda dina moln resurser." border="false":::

| Tjänst | Beskrivning |
|------|--------|
| [Azure Security Center](../../security-center/security-center-introduction.md)| Ett enhetligt infrastruktur säkerhets hanterings system som stärker position av dina data Center och ger avancerat skydd för dina hybrid arbets belastningar i molnet, oavsett om de är i Azure eller inte och lokalt. |
| **Identitets &nbsp; & &nbsp; åtkomst &nbsp; hantering** | |
| [Azure Active Directory (AD)](../../active-directory/fundamentals/active-directory-whatis.md)| Microsofts molnbaserade identitets-och åtkomst hanterings tjänst.  |
|  | [Villkorlig åtkomst](../../active-directory/conditional-access/overview.md) är det verktyg som används av Azure AD för att ge identitets signaler tillsammans, för att fatta beslut och tillämpa organisations principer. |
|  | [Domain Services](../../active-directory-domain-services/overview.md) är det verktyg som används av Azure AD för att tillhandahålla hanterade domän tjänster, till exempel domän anslutning, grup princip, LDAP (Lightweight Directory Access Protocol) och KERBEROS/NTLM-autentisering. |
|  | [Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) är en tjänst i Azure AD som gör att du kan hantera, kontrol lera och övervaka åtkomst till viktiga resurser i din organisation. |
|  | [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) är det verktyg som används av Azure AD för att skydda åtkomsten till data och program genom att kräva en andra form av autentisering. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) | Ett verktyg som gör det möjligt för organisationer att automatisera identifiering och reparation av identitetsbaserade risker, undersöka risker med data i portalen och exportera risk identifierings data till tredje parts verktyg för ytterligare analys. |
| **Infrastruktur &nbsp; & &nbsp; nätverk** |  |
| [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) | En virtuell nätverksgateway som används för att skicka krypterad trafik mellan ett virtuellt Azure-nätverk och en lokal plats via det offentliga Internet och för att skicka krypterad trafik mellan virtuella Azure-nätverk över Microsoft-nätverket. |
| [Azure DDoS Protection Standard](../../ddos-protection/ddos-protection-overview.md) | Tillhandahåller förbättrade DDoS-funktioner för att skydda mot DDoS-attacker. Den justeras automatiskt för att hjälpa till att skydda dina Azure-resurser i ett virtuellt nätverk. |
| [Azure Front Door](../../frontdoor/front-door-overview.md) | En global, skalbar start punkt som använder Microsoft Global Edge-nätverket för att skapa snabba, säkra och mycket skalbara webb program. |
| [Azure Firewall](../../firewall/overview.md) | En hanterad, molnbaserad nätverks säkerhets tjänst som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillståndskänslig tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet. |
| [Azure Key Vault](../../key-vault/general/overview.md) | En hanterad, molnbaserad nätverks säkerhets tjänst som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillståndskänslig tjänst med inbyggd hög tillgänglighet och obegränsad molnskalbarhet. |
| [Key Vault hanterad HSDM (förhands granskning)](../../key-vault/managed-hsm/overview.md) | En fullständigt hanterad, en standard-kompatibel moln tjänst med hög tillgänglighet, som gör att du kan skydda kryptografiska nycklar för dina moln program med hjälp av FIPS 140-2 nivå 3-verifierade HSM: er. |
| [Azure Private Link](../../private-link/private-link-overview.md) | Ger dig åtkomst till Azure PaaS Services (till exempel Azure Storage och SQL Database) och Azure-värdbaserade kund tjänster/partner tjänster via en privat slut punkt i det virtuella nätverket. |
| [Azure Application Gateway](../../application-gateway/overview.md) | En belastningsutjämnare för avancerad webb trafik som gör att du kan hantera trafik till dina webb program. Application Gateway kan fatta beslut om routning baserat på ytterligare attribut för en HTTP-begäran, till exempel URI-sökväg eller värd rubriker. |
| [Azure Service Bus](../../service-bus-messaging/service-bus-messaging-overview.md) | En fullständigt hanterad meddelande tjänst för företags meddelanden med meddelande köer och publicerings prenumerations ämnen. Service Bus används för att frikoppla program och tjänster från varandra. |
| [Brandvägg för webbaserade program](../../web-application-firewall/overview.md) | Tillhandahåller centraliserat skydd för dina webb program mot vanliga sårbarheter och sårbarheter. WAF kan distribueras med Azure Application Gateway och Azure-front dörren. |
| **Data & program** |  |
| [Azure Backup](../../backup/backup-overview.md) | Tillhandahåller enkla, säkra och kostnads effektiva lösningar för att säkerhetskopiera dina data och återställa dem från Microsoft Azure molnet. |
| [Azure Storage tjänst kryptering](../../storage/common/storage-service-encryption.md) | Krypterar automatiskt data innan de lagras och dekrypterar dem automatiskt när du hämtar dem. |
| [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) | En molnbaserad lösning som gör det möjligt för organisationer att identifiera, klassificera och skydda dokument och e-postmeddelanden genom att använda etiketter på innehåll. |
| [API Management](../../api-management/api-management-key-concepts.md) | Ett sätt att skapa konsekventa och moderna API-gatewayer för befintliga Server dels tjänster. |
| [Konfidentiell databehandling i Azure](../../confidential-computing/overview.md) | Gör att du kan isolera känsliga data medan de bearbetas i molnet. |
| [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops) | Dina utvecklings projekt drar nytta av flera nivåer av säkerhet och styrnings tekniker, operativa metoder och efterlevnadsprinciper när de lagras i Azure DevOps. |
| **Kund åtkomst** |  |
| [Externa Azure AD-identiteter](../../active-directory/external-identities/compare-with-b2c.md) | Med externa identiteter i Azure AD kan du ge personer utanför organisationen åtkomst till dina appar och resurser, samtidigt som de kan logga in med vilken identitet de föredrar. |
|  | Du kan dela dina appar och resurser med externa användare via [Azure AD B2B](../../active-directory/external-identities/what-is-b2b.md) -samarbete. |
|  | Med [Azure AD B2C](../../active-directory-b2c/overview.md) kan du stödja miljon tals användare och miljarder autentiseringar per dag, övervaka och automatiskt hantera hot som denial-of-service, lösen ords spray eller brute force-attacker. |

## <a name="detect-threats"></a>Identifiera hot

:::image type="content" source="media/end-to-end/detect-threats.svg" alt-text="Diagram som visar Azure-tjänster som identifierar hot." border="false":::

| Tjänst | Beskrivning |
|------|--------|
| [Azure Defender](../../security-center/azure-defender.md) | Ger avancerad, intelligent, skydd av dina Azure-och hybrid resurser och arbets belastningar. Azure Defender-instrumentpanelen i Security Center ger insyn och kontroll över funktionerna för moln skydds funktioner i din miljö. |
| [Azure Sentinel](../../sentinel/overview.md) | En skalbar, molnbaserad, SIEM-lösning (Security information Event Management) och SOAR (Security Orchestration autoresponse). Sentinel ger intelligent säkerhets analys och hot information i hela företaget, vilket ger en enda lösning för aviserings identifiering, Hot synlighet, proaktiv jakt och hot svar. |
| **Identitets &nbsp; & &nbsp; åtkomst &nbsp; hantering** |  |
| [Microsoft 365 Defender](https://docs.microsoft.com/microsoft-365/security/defender/microsoft-365-defender) | En enhetlig för hands version av företags skydds paket som internt koordinerar identifiering, förebyggande, undersökning och svar över slut punkter, identiteter, e-post och program för att ge ett integrerat skydd mot avancerade attacker. |
|  | [Microsoft Defender för slut punkt](https://docs.microsoft.com/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint.md) är en säkerhets plattform för Enterprise-slutpunkt som är utformad för att hjälpa företags nätverk att förhindra, upptäcka, undersöka och svara på avancerade hot. |
|  | [Microsoft Defender för identitet](https://docs.microsoft.com/defender-for-identity/what-is) är en molnbaserad säkerhetslösning som utnyttjar dina lokala Active Directory-signaler för att identifiera, identifiera och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder som riktas mot din organisation. |
| [Azure AD Identity Protection](../../active-directory/identity-protection/howto-identity-protection-configure-notifications.md) | Skickar två typer av automatiserade e-postmeddelanden som hjälper dig att hantera användar risker och risk identifieringar: användare med risk identifierad e-post och e-postmeddelande med veckovis Sammanfattning. |
| **Infrastruktur & nätverk** |  |
| [Azure Defender för IoT](../../defender-for-iot/overview.md) | En enhetlig säkerhetslösning för att identifiera IoT/enhets enheter, sårbarheter och hot. Det gör att du kan skydda hela IoT/miljö-miljön, oavsett om du behöver skydda befintliga IoT/enhets enheter eller bygga säkerhet i nya IoT-innovationer. |
| [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) | Innehåller verktyg för att övervaka, diagnostisera, Visa mått och aktivera eller inaktivera loggar för resurser i ett virtuellt Azure-nätverk. Network Watcher har utformats för att övervaka och reparera nätverks hälsan för IaaS-produkter som innehåller virtuella datorer, virtuella nätverk, programgatewayer och belastningsutjämnare. |
| [Azure Policy gransknings loggning](../../governance/policy/overview.md) | Hjälper till att upprätthålla organisations standarder och utvärdera kompatibiliteten i stor skala. Azure Policy använder aktivitets loggar som aktive ras automatiskt för att inkludera händelse källa, datum, användare, tidsstämpel, käll adresser, mål adresser och andra användbara element. |
| **Data & program** |  |
| [Azure Defender för containerregister](../../security-center/defender-for-container-registries-introduction.md) | Innehåller en sårbarhets-skanner som söker igenom avbildningarna i Azure Resource Manager-baserade Azure Container Registry register och ger djupare insyn i dina avbildnings sårbarheter. |
| [Azure Defender för Kubernetes](../../security-center/defender-for-kubernetes-introduction.md) | Ger skydd på kluster nivå genom att övervaka dina AKS-hanterade tjänster via de loggar som hämtas av Azure Kubernetes service (AKS). |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) | En CASB (Cloud Access Security Broker) som fungerar på flera moln. Den ger omfattande synlighet, kontroll över datatrafik och avancerad analys för att identifiera och bekämpa cyberhot i alla dina molntjänster. |

## <a name="investigate-and-respond"></a>Undersöka och svara

:::image type="content" source="media/end-to-end/investigate-and-respond.svg" alt-text="Diagram över Azure-tjänster som hjälper dig att undersöka och reagera på hot." border="false":::

| Tjänst | Beskrivning |
|------|--------|
| [Azure Sentinel](../../sentinel/hunting.md) | Kraftfulla Sök-och specialverktyg som söker efter säkerhetshot i din organisations data källor. |
| [&nbsp; &nbsp; Loggar &nbsp; och &nbsp; mått för Azure Monitor](../../azure-monitor/overview.md) | Tillhandahåller en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer. Azure Monitor [samlar in och samlar in data](../../azure-monitor/data-platform.md#observability-data-in-azure-monitor) från en mängd olika källor till en gemensam data plattform där det kan användas för analys, visualisering och aviseringar. |
| **Identitets &nbsp; & &nbsp; åtkomst &nbsp; hantering** |  |
| [Azure &nbsp; AD- &nbsp; rapporter &nbsp; och- &nbsp; övervakning](https://docs.microsoft.com/azure/active-directory/reports-monitoring/) | [Azure AD-rapporter](../../active-directory/reports-monitoring/overview-reports.md) ger en omfattande vy över aktiviteter i din miljö. |
|  | Med [Azure AD-övervakning](../../active-directory/reports-monitoring/overview-monitoring.md) kan du dirigera dina Azure AD-aktivitets loggar till olika slut punkter.|
| [Gransknings historik för Azure AD PIM](../../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md) | Visar alla roll tilldelningar och aktiveringar under de senaste 30 dagarna för alla privilegierade roller. |
| **Data & program** |  |
| [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/investigate) | Innehåller verktyg för att få en djupare förståelse för vad som händer i din moln miljö. |

## <a name="next-steps"></a>Nästa steg

- Förstå ditt [delade ansvar i molnet](shared-responsibility.md).

- Förstå [isolerings alternativen i Azure-molnet](isolation-choices.md) mot både skadliga och icke-skadliga användare.