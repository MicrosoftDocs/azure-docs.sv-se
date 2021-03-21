---
title: Azure Security benchmark v2 – nätverks säkerhet
description: Säkerhet för Azures säkerhets benchmark v2-nätverk
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b47f833a3b47dae145aa18eb6eda0c6bd95f6689
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042921"
---
# <a name="security-control-v2-network-security"></a>Säkerhets kontroll v2: nätverks säkerhet

Nätverks säkerhet täcker kontroller för att skydda och skydda Azure-nätverk. Detta innefattar att skydda virtuella nätverk, upprätta privata anslutningar, förebygga och begränsa externa attacker och säkra DNS.

Om du vill se vilka inbyggda Azure Policy som finns kan du läsa [mer i information om det inbyggda initiativet för Azure Security benchmark-efterlevnad: nätverks säkerhet](../../governance/policy/samples/azure-security-benchmark.md#network-security)

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementera säkerhet för intern trafik

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| NS-1 | 9,2, 9,4, 14,1, 14,2, 14,3 | AC-4, CA-3, SC-7 |

Se till att alla virtuella Azure-nätverk följer en företags segment princip som passar affärs riskerna. Alla system som kan ådra sig högre risk för organisationen bör isoleras i det egna virtuella nätverket och tillräckligt säkra med antingen en nätverks säkerhets grupp (NSG) och/eller Azure-brandvägg.

Beroende på dina program och din strategi för företags segmentering begränsar eller tillåter du trafik mellan interna resurser baserat på regler för nätverks säkerhets grupper. För vissa väldefinierade program (t. ex. en app på en 3-nivå) kan detta vara en mycket säker "neka som standard, Tillåt enligt undantag". Detta kanske inte kan skalas bra om du har många program och slut punkter interagerar med varandra. Du kan också använda Azure-brandvägg i situationer där central hantering krävs över ett stort antal företags segment eller ekrar (i en nav/eker-topologi).

Använd Azure Security Center anpassad nätverks härdning för att rekommendera konfigurationer för nätverks säkerhets grupper som begränsar portar och käll-IP-adresser baserat på referensen till externa nätverks trafik regler.

Använd Azure Sentinel för att identifiera användningen av äldre oskyddade protokoll som SSL/TLSv1, SMBv1, LM/NTLMv1, wDigest, osignerade LDAP-bindningar och svaga chiffer i Kerberos.

- [Så här skapar du en nätverks säkerhets grupp med säkerhets regler](../../virtual-network/tutorial-filter-network-traffic.md)

- [Distribuera och konfigurera Azure-brandvägg](../../firewall/tutorial-firewall-deploy-portal.md)

- [Anpassad nätverks härdning i Azure Security Center](../../security-center/security-center-adaptive-network-hardening.md)

- [Arbets bok för Azure Sentinel-oskyddade protokoll](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2: Anslut privata nätverk till varandra

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| NS-2 | Ej tillämpligt | CA-3, AC-17, MA-4 |

Använd Azure ExpressRoute eller Azure Virtual Private Network (VPN) för att skapa privata anslutningar mellan Azure-datacenter och lokal infrastruktur i en miljö med en valfri plats. ExpressRoute-anslutningar går inte via det offentliga Internet och de ger högre tillförlitlighet, snabbare hastigheter och lägre fördröjning än vanliga Internet anslutningar. För punkt-till-plats-VPN och plats-till-plats-VPN kan du ansluta lokala enheter eller nätverk till ett virtuellt nätverk med valfri kombination av de här VPN-alternativen och Azure-ExpressRoute.

Om du vill ansluta två eller flera virtuella nätverk i Azure tillsammans använder du peering för virtuella nätverk eller privat länk. Nätverks trafik mellan peer-kopplat virtuella nätverk är privat och lagras i Azures stamnät nätverk.

- [Vad är ExpressRoute-anslutnings modeller](../../expressroute/expressroute-connectivity-models.md)

- [Översikt över Azure VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Peering för virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md)

- [Azure Private Link](../../private-link/private-link-service-overview.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Upprätta privat nätverksåtkomst till Azure-tjänster

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| NS-3 | 14,1 | AC-4, CA-3, SC-7 |

Använd Azures privata länk för att aktivera privat åtkomst till Azure-tjänster från dina virtuella nätverk, utan att passera Internet. I situationer där Azure Private Link inte är tillgängligt ännu, använder du Azure Virtual Network-tjänstens slut punkter. Azure Virtual Network-tjänstens slut punkter ger säker åtkomst till tjänster via en optimerad väg över Azure stamnät nätverket.

Privat åtkomst är ett ytterligare skydd i djupgående mått utöver autentisering och trafik säkerhet som erbjuds av Azure-tjänster.

- [Förstå privat Azure-länk](../../private-link/private-link-overview.md)

- [Förstå Virtual Network tjänstens slut punkter](../../virtual-network/virtual-network-service-endpoints-overview.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: skydda program och tjänster från externa nätverks attacker

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| NS-4 | 9,5, 12,3, 12,9 | SC-5, SC-7 |

Skydda Azure-resurser mot attacker från externa nätverk, inklusive DDoS-attacker (distributed denial of Service), programspecifika attacker och oönskad och potentiellt skadlig Internet trafik. Azure innehåller inbyggda funktioner för detta:
-   Använd Azure-brandväggen för att skydda program och tjänster mot potentiellt skadlig trafik från Internet och andra externa platser. 

-   Använd WAF-funktioner (Web Application Firewall) i Azure Application Gateway, Azures front dörr och Azure Content Delivery Network (CDN) för att skydda dina program, tjänster och API: er mot program lager attacker.

-   Skydda dina till gångar mot DDoS-attacker genom att aktivera DDoS standard skydd på dina virtuella Azure-nätverk.
-   Använd Azure Security Center för att identifiera risker för felaktig konfiguration som är relaterade till ovanstående.

- [Dokumentation om Azure Firewall](../../firewall/index.yml)

- [Så här distribuerar du Azure-WAF](../../web-application-firewall/overview.md)

- [Hantera Azure DDoS Protection standard med hjälp av Azure Portal](../../ddos-protection/manage-ddos-protection.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Distribuera intrångs identifiering/intrångs skydd system (ID/IP-adresser)

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| NS-5 | 12,6, 12,7 | SI-4 |

Använd Azure Firewall Threat Intelligence-baserad filtrering för att varna vid och/eller blockera trafik till och från kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet. När nytto lasten är nödvändig kan du distribuera ett intrångs Avkännings system (ID/IP: er) från tredje part från Azure Marketplace med funktioner för nytto Last kontroll. Alternativt kan du använda värdbaserade ID: n/IP-adresser eller en värdbaserad lösning för slut punkts identifiering och-svar (EDR) tillsammans med eller i stället för nätverksbaserade ID: n/IP-adresser.

Obs! Om du har en regel eller annat krav för ID/IP-adresser kan du se till att det alltid är anpassat för att tillhandahålla aviseringar med hög kvalitet till din SIEM-lösning. 

- [Så här distribuerar du Azure-brandvägg](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace innehåller ID-funktioner från tredje part](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender för slut punkts funktion](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

Förenkla nätverks säkerhets reglerna genom att använda service märken och program säkerhets grupper (program säkerhets grupper).

Använd Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange namnet på service tag i fältet käll-eller mål för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Du kan också använda program säkerhets grupper för att förenkla komplex säkerhets konfiguration. I stället för att definiera en princip baserat på explicita IP-adresser i nätverks säkerhets grupper kan du med program säkerhets grupper konfigurera nätverks säkerhet som ett naturligt tillägg till ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverks säkerhets principer baserat på dessa grupper.

- [Förstå och använda service märken](../../virtual-network/service-tags-overview.md)

- [Förstå och använda program säkerhets grupper](../../virtual-network/network-security-groups-overview.md#application-security-groups)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: säker Domain Name Service (DNS)

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP 800-53 R4-ID: n |
|--|--|--|--|
| NS-7 | Ej tillämpligt | SC-20, SC – 21 |

Följ metod tipsen för DNS-säkerhet för att undvika vanliga attacker som Dangling DNS, DNS-utlösnings attacker, DNS-förgiftning och förfalskning osv.

När Azure DNS används som auktoritativ DNS-tjänst kontrollerar du att DNS-zoner och-poster skyddas från oavsiktlig eller skadlig ändring med hjälp av Azure RBAC-och resurs lås.

- [Översikt över Azure DNS](../../dns/dns-overview.md)

- [Distributions guide för säker Domain Name System (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [Förhindra Dangling DNS-poster och Undvik under domän övertag Ande](../fundamentals/subdomain-takeover.md)

**Ansvar**: Kund

**Kund säkerhets intressenter** ([Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Program säkerhet och DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)
